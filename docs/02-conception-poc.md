# Planisage — Conception du POC

**Périmètre retenu (6 fonctionnalités + carto)**

| # | Fonctionnalité |
|---|---|
| F1 | Saisie des informations clients, y compris les informations sensibles (codes d'accès, clés, consignes) |
| F2 | Ajout de tâches dans le planning |
| F3 | Contraintes sur les tâches (ex. contrat de 12 tontes par an) |
| F4 | Génération d'un planning à partir des contraintes de chaque client |
| F5 | Suivi des tâches : heures réalisées chez un client, reste à faire |
| F6 | Envoi du bordereau de déduction fiscale au client |
| F7 | Cartographie : répartition des clients sur une carte |

---

## 1. Vue d'ensemble

```mermaid
flowchart LR
    subgraph Saisie["Saisie — Manager"]
        A["F1 · Clients, sites,<br/>accès sécurisés"]
        B["F3 · Contrats et<br/>contraintes"]
    end

    subgraph Moteur["Moteur"]
        C["F4 · Génération<br/>du planning"]
    end

    subgraph Terrain["Terrain — Ouvrier"]
        D["F2 · Planning<br/>et tâches"]
        E["Pointage<br/>et clôture"]
    end

    subgraph Restitution["Restitution"]
        F["F5 · Suivi des heures<br/>et du reste à faire"]
        G["F6 · Attestation<br/>fiscale annuelle"]
        H["F7 · Carte<br/>des clients"]
    end

    A --> B --> C --> D --> E
    E --> F
    F -. "réajuste" .-> C
    E --> G
    A --> H
    H -. "regroupement par secteur" .-> C
```

Le point important : **F4 alimente F2, et F5 réalimente F4.** Si un passage saute
(intempéries, absence), le moteur doit replanifier le reste de l'année pour tenir le
quota du contrat. C'est ce qui fait la valeur du produit, et c'est la partie la plus
délicate à construire.

---

## 2. Modèle de données

```mermaid
erDiagram
    ENTREPRISE ||--o{ UTILISATEUR : "emploie"
    ENTREPRISE ||--o{ CLIENT : "gere"
    ENTREPRISE ||--o{ PRESTATION : "catalogue"
    ENTREPRISE ||--o{ EQUIPE : "organise"

    CLIENT ||--o{ SITE : "possede"
    CLIENT ||--o{ CONTACT : "a"
    CLIENT ||--o{ CONTRAT : "souscrit"
    CLIENT ||--o{ FACTURE : "recoit"
    CLIENT ||--o{ ATTESTATION : "recoit"

    SITE ||--o| ACCES : "protege par"
    SITE ||--o{ TACHE : "accueille"

    CONTRAT ||--o{ LIGNE_CONTRAT : "contient"
    PRESTATION ||--o{ LIGNE_CONTRAT : "reference"
    LIGNE_CONTRAT ||--o{ CONTRAINTE : "impose"
    LIGNE_CONTRAT ||--o{ TACHE : "genere"

    EQUIPE ||--o{ AFFECTATION : "compose"
    UTILISATEUR ||--o{ AFFECTATION : "membre de"
    EQUIPE ||--o{ TACHE : "realise"

    TACHE ||--o{ POINTAGE : "consomme"
    UTILISATEUR ||--o{ POINTAGE : "saisit"
    TACHE ||--o{ PHOTO : "documente"

    FACTURE ||--o{ REGLEMENT : "encaisse"
    TACHE }o--o| FACTURE : "facturee dans"
    REGLEMENT }o--o{ ATTESTATION : "cumule dans"

    CLIENT {
        uuid id
        string type "particulier | pro | copropriete | collectivite"
        string nom
        string email
        bool eligible_sap "credit impot services a la personne"
    }
    SITE {
        uuid id
        string libelle
        string adresse
        float latitude "geocode a l enregistrement"
        float longitude
        int surface_m2
        string secteur "pour le regroupement en tournees"
    }
    ACCES {
        uuid id
        text contenu_chiffre "code portail, emplacement cle, alarme"
        text consignes "chien, horaires, voisin"
        timestamp derniere_consultation
    }
    PRESTATION {
        uuid id
        string libelle "tonte, taille de haie, desherbage"
        int duree_std_minutes
        decimal prix_unitaire
        bool eligible_sap
    }
    CONTRAT {
        uuid id
        date debut
        date fin
        string statut "brouillon | actif | suspendu | termine"
        decimal montant_annuel
    }
    LIGNE_CONTRAT {
        uuid id
        int quantite_annuelle "ex 12 tontes"
        int duree_prevue_minutes "par passage"
    }
    CONTRAINTE {
        uuid id
        string type "voir section 3"
        json parametres
        bool bloquante "stricte ou simple preference"
    }
    TACHE {
        uuid id
        date date_prevue
        date fenetre_debut "au plus tot"
        date fenetre_fin "au plus tard"
        int duree_prevue_minutes
        int duree_reelle_minutes "somme des pointages"
        string statut "voir section 5"
        string origine "generee | manuelle"
    }
    POINTAGE {
        uuid id
        timestamp debut
        timestamp fin
        bool valide_manager
    }
    ATTESTATION {
        uuid id
        int annee_fiscale
        decimal montant_regle "sommes effectivement encaissees"
        decimal montant_eligible "apres plafond"
        string statut "brouillon | emise | envoyee"
    }
```

### Points d'attention sur le modèle

**`ACCES` est une table à part, et c'est volontaire.** Un code de portail ou
l'emplacement d'une clé n'est pas un champ de commentaire comme un autre :

- contenu chiffré en base, jamais en clair dans les journaux ni dans les exports ;
- visible par l'ouvrier **uniquement** le jour où il a une tâche sur ce site, et
  masqué derrière un bouton « afficher » ;
- chaque consultation est horodatée — si un cambriolage a lieu, ton ami doit pouvoir
  dire qui a vu le code et quand ;
- purge ou rotation quand le client part.

**`TACHE` porte une fenêtre, pas seulement une date.** `fenetre_debut` /
`fenetre_fin` sont ce qui permet au moteur de décaler un passage sans casser le
contrat. Une tâche qui n'a qu'une date est impossible à replanifier proprement.

**`duree_reelle_minutes` est dérivée des pointages**, jamais saisie directement :
c'est elle qui fait tout le §6 (suivi) et qui révèle si un contrat est rentable.

---

## 3. F3 — Les contraintes

C'est le cœur du sujet et la partie que le questionnaire doit préciser. Un « contrat de
12 tontes par an » n'est pas une contrainte, c'est **quatre** contraintes qui se
combinent :

```mermaid
flowchart TD
    L["Ligne de contrat<br/>Tonte × 12 / an"] --> Q["QUOTA<br/>12 passages sur l'année"]
    L --> S["SAISON<br/>uniquement du 1er avril au 31 octobre"]
    L --> E["ESPACEMENT<br/>entre 15 et 25 jours entre deux passages"]
    L --> P["PREFERENCE<br/>plutôt le mardi, plutôt le matin"]

    Q --> G{"Moteur de<br/>génération"}
    S --> G
    E --> G
    P --> G

    C1["Capacité des équipes<br/>heures disponibles / jour"] --> G
    C2["Secteur géographique<br/>regrouper les clients proches"] --> G
    C3["Indisponibilités<br/>congés, jours fériés"] --> G
    C4["Exclusions client<br/>absent en août"] --> G

    G --> R["Planning proposé"]
```

### Catalogue des types de contrainte à implémenter

| Type | Paramètres | Exemple | POC |
|---|---|---|:--:|
| `QUOTA` | nombre, période | 12 tontes par an | ✅ |
| `SAISON` | date début, date fin | tonte d'avril à octobre | ✅ |
| `ESPACEMENT` | min jours, max jours | 15 à 25 jours entre deux tontes | ✅ |
| `JOUR_PREFERE` | jours de la semaine | mardi ou jeudi | ✅ |
| `EXCLUSION` | plages de dates | client absent du 1er au 20 août | ✅ |
| `DUREE` | minutes | 1 h 30 par passage sur ce site | ✅ |
| `CRENEAU` | heure début, heure fin | uniquement le matin | ⬜ V1 |
| `EQUIPE_IMPOSEE` | équipe | toujours l'équipe de Marc, le client la connaît | ⬜ V1 |
| `METEO` | seuil | pas de tonte si pluie la veille | ⬜ V1 |
| `PRE_REQUIS` | tâche | tailler avant de désherber | ⬜ +tard |

**Question à poser à ton ami :** est-ce que le quota est **ferme** (12 passages dus,
point) ou **indicatif** (on tond quand l'herbe pousse, environ 12 fois) ? Les deux
existent dans le métier et ils ne produisent pas le même moteur. Le premier se
planifie un an à l'avance ; le second demande un ajustement permanent et une notion de
« prochain passage souhaitable ».

---

## 4. F4 — Génération du planning

```mermaid
flowchart TD
    START(["Manager lance la génération<br/>pour une période"]) --> LOAD["Charger les contrats actifs<br/>et leurs contraintes"]
    LOAD --> DUE["Étape 1 — Calculer les passages dus<br/>par ligne de contrat"]
    DUE --> WIN["Étape 2 — Découper en fenêtres<br/>saison ÷ quota, borné par l'espacement"]
    WIN --> DONE["Étape 3 — Retirer ce qui est<br/>déjà réalisé ou déjà planifié"]
    DONE --> CAND["Étape 4 — Proposer une date candidate<br/>par fenêtre, selon les préférences"]
    CAND --> GEO["Étape 5 — Regrouper par secteur<br/>les clients proches le même jour"]
    GEO --> CAP{"Étape 6 — La capacité<br/>de l'équipe tient ?"}
    CAP -- "non" --> SHIFT["Décaler dans la fenêtre<br/>ou changer d'équipe"]
    SHIFT --> CAP
    CAP -- "oui, mais<br/>plus de marge" --> CONF["Marquer en conflit<br/>et remonter au manager"]
    CAP -- "oui" --> PROP["Tâche à l'état PROPOSEE"]
    CONF --> REVIEW
    PROP --> REVIEW["Étape 7 — Écran de prévisualisation<br/>proposé / conflits / non plaçable"]
    REVIEW --> VALID{"Manager<br/>valide ?"}
    VALID -- "ajuste" --> MANUAL["Glisser-déposer<br/>dans le planning"]
    MANUAL --> REVIEW
    VALID -- "oui" --> COMMIT["Tâches à l'état PLANIFIEE<br/>notification aux équipes"]
    VALID -- "non" --> ABORT(["Abandon, rien n'est écrit"])
    COMMIT --> END(["Planning publié"])
```

### Trois règles de conception à tenir

1. **Le moteur propose, l'humain valide.** Rien n'est écrit dans le planning réel
   avant la validation du manager. Un moteur qui écrit tout seul est rejeté par les
   utilisateurs dès le premier passage aberrant.
2. **Une génération est rejouable et ne détruit rien.** On régénère uniquement ce qui
   est encore à l'état `PROPOSEE` ; ce qui est validé, déplacé à la main ou déjà
   réalisé est intouchable. Sans cette règle, le manager perd son travail à chaque
   relance et n'utilise plus la fonction.
3. **L'échec est une réponse acceptable.** Si un passage ne rentre nulle part, on
   l'affiche comme « non plaçable » avec la raison. Un moteur qui force une solution
   incohérente est pire qu'un moteur qui dit non.

Pour le POC, un algorithme glouton (étaler les fenêtres, remplir au plus tôt dans la
capacité restante, trier par secteur) suffit largement. L'optimisation fine des
tournées est un chantier en soi, à garder pour plus tard.

---

## 5. Cycle de vie d'une tâche

```mermaid
stateDiagram-v2
    [*] --> PROPOSEE : générée par le moteur
    [*] --> PLANIFIEE : créée à la main par le manager

    PROPOSEE --> PLANIFIEE : validée par le manager
    PROPOSEE --> [*] : rejetée ou régénérée

    PLANIFIEE --> AFFECTEE : équipe et date confirmées
    AFFECTEE --> EN_COURS : l'ouvrier démarre son pointage
    EN_COURS --> REALISEE : clôture, photos, compte-rendu

    AFFECTEE --> REPORTEE : intempéries, absence, client
    PLANIFIEE --> REPORTEE : intempéries, absence, client
    REPORTEE --> PLANIFIEE : replanifiée dans la fenêtre
    REPORTEE --> ANNULEE : hors fenêtre, quota perdu

    REALISEE --> FACTUREE : intégrée à une facture
    FACTUREE --> [*]
    ANNULEE --> [*]

    note right of REPORTEE
        Un report rouvre la fenêtre
        de la ligne de contrat :
        le moteur doit replacer
        le passage ailleurs.
    end note
```

---

## 6. F5 — Suivi des tâches et du reste à faire

```mermaid
sequenceDiagram
    actor O as Ouvrier
    participant M as Mobile
    participant API as Serveur
    participant DB as Base
    actor G as Manager

    O->>M: Ouvre « Ma journée »
    M->>API: Tâches du jour
    API->>DB: Tâches AFFECTEE pour l'équipe
    DB-->>M: 4 interventions

    O->>M: Ouvre la fiche, demande le code d'accès
    M->>API: Consultation accès site
    API->>DB: Déchiffre + journalise qui et quand
    DB-->>M: Code portail 4512B

    O->>M: Démarrer
    M->>API: Ouverture du pointage
    API->>DB: POINTAGE.debut, tâche EN_COURS

    O->>M: Terminer + photos + note
    M->>API: Clôture
    API->>DB: POINTAGE.fin, tâche REALISEE
    API->>DB: Recalcule durée réelle et compteur du contrat

    Note over API,DB: Tonte 7 / 12 réalisée<br/>5 restantes avant le 31 octobre

    G->>API: Écran de suivi
    API->>DB: Agrégation par client et par contrat
    DB-->>G: Heures vendues 18 h · réalisées 21 h 30 · marge −3 h 30
    G->>API: Valide les heures de la semaine
    API->>DB: POINTAGE.valide_manager
```

**Les deux indicateurs qui portent la fonctionnalité :**

| Indicateur | Calcul | À quoi ça sert |
|---|---|---|
| **Reste à faire** | quota du contrat − tâches `REALISEE`, rapporté aux jours restants dans la saison | « Il me reste 5 tontes à caser avant fin octobre chez les Martin » |
| **Dérive horaire** | somme des pointages − durée prévue, par contrat | « Ce contrat me coûte 3 h 30 de plus que ce que je l'ai vendu » |

C'est le deuxième qui fera vendre le produit : aucun paysagiste ne sait aujourd'hui
quel contrat d'entretien lui fait perdre de l'argent.

---

## 7. F6 — Bordereau de déduction fiscale

Il s'agit de l'**attestation fiscale annuelle services à la personne** : le client
particulier bénéficie d'un crédit d'impôt de 50 % sur les petits travaux de jardinage,
et il lui faut un justificatif nominatif pour sa déclaration.

```mermaid
flowchart TD
    START(["Manager lance la campagne<br/>pour l'année N"]) --> ELIG["Sélectionner les clients<br/>particuliers éligibles SAP"]
    ELIG --> REG["Agréger les règlements<br/>ENCAISSÉS entre le 1er janvier<br/>et le 31 décembre de l'année N"]
    REG --> FILT["Ne garder que les prestations<br/>éligibles du catalogue"]
    FILT --> CAP["Appliquer le plafond annuel<br/>de dépenses jardinage par foyer"]
    CAP --> CTRL{"Contrôles"}
    CTRL -- "SIRET, n° de déclaration SAP,<br/>identité et adresse du client<br/>incomplets" --> ERR["Liste des anomalies<br/>à corriger"]
    ERR --> CTRL
    CTRL -- "OK" --> PDF["Générer le PDF nominatif<br/>montant réglé · montant éligible"]
    PDF --> REVIEW["Aperçu et contrôle<br/>par le manager"]
    REVIEW --> SEND["Envoi par mail<br/>+ dépôt dans l'espace client"]
    SEND --> LOG["Journaliser l'envoi<br/>et archiver le PDF"]
    LOG --> END(["Campagne clôturée"])
```

### Trois points qui vont mordre

1. **L'attestation se base sur les sommes encaissées, pas facturées.** Une facture de
   décembre payée en janvier bascule sur l'année fiscale suivante. Cela veut dire que
   le POC a besoin, au minimum, d'une trace des **règlements** — ce qui n'était pas
   dans ta liste de 6 fonctionnalités. Deux options : une saisie manuelle
   « facture payée le … » (quelques heures de développement) ou un import du relevé
   bancaire (bien plus lourd). Je recommande la saisie manuelle pour le POC.
2. **L'entreprise doit être déclarée SAP** auprès de l'État pour que l'attestation ait
   une valeur. Il faut demander à ton ami s'il l'est déjà — sinon la fonctionnalité ne
   sert à rien chez lui, et il faudra la tester chez quelqu'un d'autre.
3. **Seules certaines prestations sont éligibles** (petit jardinage chez un
   particulier, avec un plafond de dépenses annuel), et l'abattage ou la création d'un
   jardin ne le sont pas. D'où le drapeau `eligible_sap` au niveau de la prestation et
   non du client. Les montants de plafond et les règles exactes sont à vérifier auprès
   de son comptable avant l'implémentation — ils changent régulièrement et je ne veux
   pas les figer dans le code sur la base d'un chiffre approximatif.

---

## 8. F7 — Cartographie

```mermaid
flowchart LR
    SAVE["Enregistrement<br/>d'un site"] --> GEO["Géocodage de l'adresse<br/>API Base Adresse Nationale"]
    GEO --> STORE["Stockage latitude<br/>et longitude"]
    STORE --> MAP["Carte des clients"]
    STORE --> SECT["Calcul du secteur<br/>regroupement géographique"]
    SECT --> ENGINE["Moteur de génération<br/>du planning"]
    STORE --> TOUR["Vue tournée du jour<br/>ordre de passage"]

    MAP --> F1["Filtre : type de client"]
    MAP --> F2["Filtre : contrat actif ou non"]
    MAP --> F3["Couleur : retard sur le quota"]
    MAP --> F4["Filtre : équipe habituelle"]
```

La carte a deux usages, et c'est le second qui compte vraiment :

- **Visualiser** la répartition des clients — utile commercialement, agréable en démo ;
- **Alimenter le moteur** : le champ `secteur` déduit des coordonnées permet de
  regrouper les passages proches le même jour. C'est ce qui évite un planning
  théoriquement valide mais qui fait traverser le département trois fois dans la
  journée.

Le géocodage se fait une fois, à l'enregistrement du site. Base Adresse Nationale est
gratuite et sans clé pour les adresses françaises ; le fond de carte peut venir
d'OpenStreetMap ou de l'IGN, ce qui évite la facturation à l'usage d'une carte
propriétaire.

---

## 9. Écrans à créer

### Synthèse

| | POC | V1 complète |
|---|:---:|:---:|
| Web — manager | **12** | 21 |
| Mobile — ouvrier | **4** | 6 |
| Transverse | **1** | 2 |
| **Total** | **17** | **29** |

### Détail

Complexité : **S** = simple (formulaire, liste) · **M** = moyen · **L** = lourd,
plusieurs jours, comporte le risque technique du projet.

#### Transverse

| Réf | Écran | Cplx | POC |
|---|---|:--:|:--:|
| T01 | Connexion | S | ✅ |
| T02 | Profil et mot de passe | S | ⬜ |

#### F1 · Clients et sites

| Réf | Écran | Cplx | POC |
|---|---|:--:|:--:|
| C01 | Liste des clients — recherche, filtres, tri | S | ✅ |
| C02 | Fiche client — onglets sites, contrats, historique, heures | M | ✅ |
| C03 | Formulaire client — création et édition | S | ✅ |
| C04 | Fiche site — adresse, surface, **accès sécurisés** avec révélation contrôlée | M | ✅ |
| C05 | Journal des consultations d'un accès sensible | S | ⬜ |
| C06 | Import de clients depuis un fichier Excel | M | ⬜ |

#### F3 · Contrats et contraintes

| Réf | Écran | Cplx | POC |
|---|---|:--:|:--:|
| K01 | Liste des contrats — statut, échéance, avancement | S | ✅ |
| K02 | **Éditeur de contrat** — lignes de prestation et contraintes | **L** | ✅ |
| K03 | Fiche d'avancement d'un contrat — réalisé / dû / reste à faire | M | ✅ |
| K04 | Reconduction et révision de prix | M | ⬜ |

#### F2 et F4 · Planning

| Réf | Écran | Cplx | POC |
|---|---|:--:|:--:|
| P01 | **Planning semaine par équipe** — glisser-déposer | **L** | ✅ |
| P02 | **Assistant de génération** — paramètres, prévisualisation, conflits, non plaçables | **L** | ✅ |
| P03 | Fiche tâche — détail, affectation, report, clôture | M | ✅ |
| P04 | Création rapide d'une tâche hors contrat | S | ✅ |
| P05 | Vue tournée du jour sur carte — ordre de passage | M | ⬜ |
| P06 | Vue charge sur plusieurs mois | M | ⬜ |
| P07 | Report en masse pour intempéries | M | ⬜ |

#### F5 · Suivi

| Réf | Écran | Cplx | POC |
|---|---|:--:|:--:|
| S01 | Tableau de suivi — heures vendues / réalisées / dérive, par client et contrat | M | ✅ |
| S02 | Validation hebdomadaire des heures | M | ⬜ |
| S03 | Détail des pointages d'un client | S | ⬜ |

#### F6 · Attestations fiscales

| Réf | Écran | Cplx | POC |
|---|---|:--:|:--:|
| A01 | Campagne annuelle — sélection, anomalies, génération en lot | M | ✅ |
| A02 | Aperçu et envoi d'une attestation | S | ✅ |
| A03 | Saisie des règlements encaissés | S | ✅ |
| A04 | Suivi des envois et renvois | S | ⬜ |

#### F7 · Carte

| Réf | Écran | Cplx | POC |
|---|---|:--:|:--:|
| G01 | **Carte des clients** — filtres, couleurs par statut, accès à la fiche | M | ✅ |

#### Paramétrage

| Réf | Écran | Cplx | POC |
|---|---|:--:|:--:|
| R01 | Catalogue des prestations — durée standard, prix, éligibilité SAP | S | ✅ |
| R02 | Équipes et indisponibilités | M | ⬜ |
| R03 | Paramètres entreprise — SIRET, n° SAP, logo, modèles de document | S | ⬜ |
| R04 | Utilisateurs et rôles | S | ⬜ |

#### Mobile · Ouvrier

| Réf | Écran | Cplx | POC |
|---|---|:--:|:--:|
| M01 | Ma journée — liste des interventions | S | ✅ |
| M02 | Détail de l'intervention — consignes, **code d'accès**, itinéraire | M | ✅ |
| M03 | Pointage — démarrer, pause, terminer | M | ✅ |
| M04 | Clôture — photos, compte-rendu, reste à faire signalé | M | ✅ |
| M05 | Mes heures de la semaine | S | ⬜ |
| M06 | Mes prochains jours | S | ⬜ |

### Navigation du POC

```mermaid
flowchart TD
    T01["T01 Connexion"] --> ROLE{"Rôle"}

    ROLE -- "Manager" --> P01["P01 Planning semaine"]
    ROLE -- "Ouvrier" --> M01["M01 Ma journée"]

    P01 --> P02["P02 Assistant<br/>de génération"]
    P01 --> P03["P03 Fiche tâche"]
    P01 --> P04["P04 Tâche rapide"]
    P02 --> P01

    P01 --- NAV["Navigation principale"]
    NAV --> C01["C01 Clients"]
    NAV --> K01["K01 Contrats"]
    NAV --> S01["S01 Suivi"]
    NAV --> G01["G01 Carte"]
    NAV --> A01["A01 Attestations"]
    NAV --> R01["R01 Prestations"]

    C01 --> C02["C02 Fiche client"]
    C01 --> C03["C03 Formulaire client"]
    C02 --> C04["C04 Fiche site + accès"]
    C02 --> K02["K02 Éditeur de contrat"]
    G01 --> C02
    K01 --> K02
    K01 --> K03["K03 Avancement"]
    S01 --> K03
    A01 --> A02["A02 Aperçu attestation"]
    A01 --> A03["A03 Règlements"]

    M01 --> M02["M02 Détail intervention"]
    M02 --> M03["M03 Pointage"]
    M03 --> M04["M04 Clôture"]
    M04 --> M01
```

---

## 10. Où se concentre le risque

Trois écrans sur dix-sept concentrent l'essentiel de la difficulté. Ce sont eux qu'il
faut prototyper en premier, avant tout le reste.

| Écran | Pourquoi c'est risqué |
|---|---|
| **K02 · Éditeur de contrat** | Il faut rendre compréhensible, pour un paysagiste, un système de contraintes qui est conceptuellement complexe. Si cet écran est raté, personne ne saisit jamais de contrat et tout le reste s'écroule. |
| **P02 · Assistant de génération** | C'est le moteur. Le difficile n'est pas de produire un planning, c'est de produire un planning que le manager accepte sans tout reprendre à la main — et d'expliquer lisiblement pourquoi un passage n'est pas plaçable. |
| **P01 · Planning semaine** | Le glisser-déposer multi-équipes avec recalcul des contraintes en direct est le composant le plus coûteux de l'interface. |

**Proposition de séquence de construction :**

1. Modèle de données + C01 → C04 (clients et accès) : la base de tout.
2. R01 (prestations) + K02 (contrat et contraintes) : la matière première du moteur.
3. Le moteur F4 en ligne de commande, sans interface, validé sur les vrais contrats de
   ton ami. **C'est ici qu'on saura si le projet tient.**
4. P01 et P02 : l'interface du planning.
5. M01 → M04 : le mobile, qui ferme la boucle et alimente le suivi.
6. S01, puis A01 → A03 et G01.

Les étapes 1 à 3 sont le vrai POC technique. Si le moteur ne produit pas un planning
crédible sur les données réelles, mieux vaut le découvrir à la troisième étape qu'après
avoir construit dix-sept écrans.

---

## 11. Questions ouvertes à trancher avec ton ami

1. Le quota d'un contrat est-il **ferme** ou **indicatif** ? (§3)
2. Qui saisit les heures : l'ouvrier sur son téléphone, ou le chef d'équipe pour tout
   le monde ? La réponse ajoute ou retire quatre écrans mobiles.
3. L'entreprise est-elle **déclarée services à la personne** ? Sans cela, F6 n'est pas
   testable chez lui. (§7)
4. Accepte-t-on une **saisie manuelle des règlements** dans le POC, ou faut-il une vraie
   facturation ? (§7)
5. Quelle granularité pour le **secteur géographique** : commune, code postal, ou zones
   dessinées à la main sur la carte ? (§8)
6. Les **accès sensibles** : qui a le droit de les voir ? Tous les ouvriers, ou
   seulement ceux affectés au site ce jour-là ? (§2)
