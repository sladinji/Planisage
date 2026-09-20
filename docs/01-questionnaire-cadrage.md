# Planisage — Questionnaire de cadrage

**À remplir par :** _(nom du paysagiste)_
**Date :** ____ / ____ / 2026
**Objectif :** cadrer le produit avant toute ligne de code, et figer le périmètre du POC.

> Mode d'emploi : réponds au fil de l'eau, même partiellement. Pour les tableaux de
> fonctionnalités, mets une croix dans **une seule** colonne :
>
> | Code | Signification |
> |---|---|
> | **POC** | Indispensable pour la première démo utilisable (dans ~2-3 mois) |
> | **V1** | Nécessaire pour vendre/utiliser en vrai, mais pas dans le POC |
> | **+TARD** | Utile un jour, pas prioritaire |
> | **NON** | On n'en veut pas |
>
> Si tu hésites entre deux cases, mets la moins ambitieuse : on pourra toujours remonter.

---

## 1. Contexte et objectif du projet

1.1 En une phrase, quel problème l'application doit-elle résoudre en priorité ?
_(ex. « je perds 5 h par semaine à recopier les heures des gars pour la paie »)_

> …

1.2 Aujourd'hui, avec quoi tu travailles ? (papier, Excel, WhatsApp, EBP, Batappli, Codial,
Organilog, Kizeo, Sage, autre…) Qu'est-ce qui marche bien et qu'on ne doit **pas** casser ?

> …

1.3 Quelles sont les 3 tâches qui te coûtent le plus de temps ou d'énervement chaque semaine ?

> 1. …
> 2. …
> 3. …

1.4 Si dans 6 mois l'application ne faisait **qu'une seule chose**, ce serait laquelle ?

> …

1.5 L'application est-elle d'abord **pour toi** (outil interne), ou l'objectif est-il de la
**vendre à d'autres paysagistes** ? Si les deux : lequel passe en premier ?

- [ ] D'abord mon entreprise, la revente viendra peut-être après
- [ ] D'abord un produit à vendre, mon entreprise sert de terrain d'essai
- [ ] Les deux en même temps

---

## 2. Taille et typologie des utilisateurs

2.1 Combien de personnes dans ton entreprise aujourd'hui ? Et dans 3 ans ?

| | Aujourd'hui | Dans 3 ans |
|---|---|---|
| Gérant(s) / conducteur(s) de travaux | | |
| Chefs d'équipe | | |
| Ouvriers / jardiniers | | |
| Apprentis / saisonniers / intérim | | |
| Administratif / compta | | |

2.2 Combien d'équipes partent en chantier le matin, en moyenne ? En pleine saison ?

> …

2.3 L'appli doit-elle aussi servir à **l'artisan seul** (1 personne, pas d'équipe) ?
Si oui, quelle est la version « minimale » qui a du sens pour lui ?

> …

2.4 Tu parles d'un profil **manager** et d'un profil **ouvrier**. Est-ce qu'il faut un
niveau intermédiaire **chef d'équipe** (voit son planning + celui de son équipe, valide
les heures de ses gars, mais pas la facturation) ?

- [ ] Oui, 3 rôles : Manager / Chef d'équipe / Ouvrier
- [ ] Non, 2 rôles suffisent
- [ ] Il faut aussi un rôle **Compta/Admin** (facturation, pas de planning)
- [ ] Il faut aussi un accès **Client** (voir ses devis, ses passages) → cf. §6.9

2.5 Concrètement : qu'est-ce qu'un ouvrier ne doit **jamais** voir ?
_(prix de vente, marge, salaires des collègues, coordonnées clients, autre ?)_

> …

---

## 3. Le terrain : mobile et hors connexion

3.1 Sur quoi travaillent les gars sur le chantier ?

- [ ] Leur téléphone perso (Android / iPhone — préciser la répartition)
- [ ] Un téléphone fourni par l'entreprise
- [ ] Une tablette partagée par équipe (1 par camion)
- [ ] Rien, ils ne saisissent rien (c'est le chef qui saisit)

3.2 **Question clé — le hors connexion.** À quelle fréquence es-tu réellement sans réseau
sur un chantier ?

- [ ] Jamais ou presque (zones urbaines / péri-urbaines)
- [ ] Parfois (quelques chantiers isolés, caves, sous-bois, parkings souterrains)
- [ ] Souvent (campagne, forêt, montagne) — c'est bloquant

3.3 Si le réseau tombe en plein chantier, qu'est-ce qui doit **absolument** continuer à
marcher ? (coche ce qui est vital)

- [ ] Consulter le planning du jour et l'adresse du chantier
- [ ] Consulter la fiche chantier / les consignes / le plan
- [ ] Démarrer et arrêter le pointage des heures
- [ ] Prendre des photos et les rattacher au chantier
- [ ] Remplir un compte-rendu / une check-list
- [ ] Faire signer le client sur l'écran
- [ ] Créer un devis sur place
- [ ] Rien, ils peuvent attendre d'avoir du réseau

3.4 Accepterais-tu que le hors-connexion soit **en lecture seule** au départ (on voit son
planning téléchargé le matin, mais la saisie attend le réseau) pour aller plus vite ?

- [ ] Oui, acceptable pour le POC
- [ ] Non, la saisie hors ligne est indispensable dès le début

> **Pourquoi on insiste :** le hors-connexion avec synchronisation (deux personnes qui
> modifient la même donnée chacune de leur côté) est la décision qui coûte le plus cher
> du projet. Elle peut facilement doubler le temps de développement de la partie mobile.
> Il faut donc savoir si c'est un vrai besoin ou un confort.

3.5 Faut-il une vraie application à installer (App Store / Play Store), ou un site web qui
s'ouvre dans le navigateur du téléphone (et qu'on peut épingler sur l'écran d'accueil) ?

- [ ] Vraie appli installable, c'est plus crédible / les gars s'y retrouveront mieux
- [ ] Peu importe, tant que ça marche bien sur téléphone
- [ ] Un site web suffit

---

## 4. Modèle produit : multi-entreprises ou marque blanche

> **Rappel des options**
>
> | | Multi-tenant sous notre marque | Marque blanche |
> |---|---|---|
> | Principe | Une seule application, chaque entreprise a son espace cloisonné | On revend le produit à un intégrateur/réseau qui le met à ses couleurs et à son nom |
> | Notre marge | Abonnement récurrent direct | Licence / revenus partagés |
> | Complexité technique | Standard, prévue dès le départ | + logos, couleurs, domaines, parfois options par revendeur |
> | Qui parle au client final | Nous | Le revendeur |
> | Risque | Il faut aller chercher les clients un par un | On dépend d'un partenaire |
>
> **Notre recommandation : multi-tenant sous notre propre marque.** C'est le modèle qui
> permet de garder la relation client, d'itérer vite sur un seul code, et d'encaisser un
> abonnement récurrent. La marque blanche reste possible plus tard : techniquement, un
> produit multi-tenant bien fait peut être « habillé » aux couleurs d'un revendeur, alors
> que l'inverse est beaucoup plus douloureux. Décision à valider ensemble, mais elle n'est
> pas bloquante pour le POC.

4.1 Ton avis sur le modèle :

- [ ] D'accord : multi-tenant, notre marque, abonnement
- [ ] Je préfère la marque blanche — pourquoi ? → …
- [ ] Je veux d'abord un outil **rien que pour moi**, on verra après
- [ ] Sans avis, je vous fais confiance

4.2 Connais-tu déjà un réseau, une franchise, une coopérative, un syndicat (UNEP…) ou un
gros donneur d'ordre qui pourrait être intéressé ? _(ça change la stratégie)_

> …

4.3 Combien penses-tu qu'un paysagiste accepterait de payer par mois ?

- [ ] Par entreprise : _____ € / mois
- [ ] Ou par utilisateur actif : _____ € / mois / personne
- [ ] Je ne sais pas

4.4 Y a-t-il des entreprises concurrentes que tu as testées ou qu'on t'a démarchées ?
Qu'en as-tu pensé ? _(Organilog, Kizeo Forms, Twimm, Batappli, Obat, Vertuoza, Praxedo,
Synchroteam, Costructor, Tolteck…)_

> …

---

## 5. Contraintes légales et sensibles (France)

5.1 **Géolocalisation des salariés.** Veux-tu savoir où sont les équipes ?

- [ ] Oui, en temps réel sur une carte
- [ ] Uniquement un « pointage géolocalisé » : on enregistre la position au moment où
      l'ouvrier démarre et arrête son chantier
- [ ] Non, pas de géolocalisation du tout

> **Attention :** la CNIL encadre strictement ce point. La géolocalisation ne peut pas
> servir à contrôler le temps de travail s'il existe un autre moyen, elle doit être
> désactivable hors temps de travail, les salariés doivent être informés et le CSE
> consulté. Selon la réponse, on prévoit les réglages nécessaires (et le fait que chaque
> entreprise cliente puisse couper la fonction).

5.2 Y a-t-il une **convention collective** ou des règles internes qui comptent pour le
calcul des heures ? (paysage : heures supplémentaires, modulation/annualisation, petits
déplacements et zones, paniers repas, trajet domicile-chantier, heures d'intempéries…)

> …

5.3 Le pointage doit-il servir de **base officielle à la paie** (donc valeur probante,
historique inaltérable, validation du salarié) ou seulement d'indicateur de gestion ?

- [ ] Base officielle pour la paie
- [ ] Indicateur interne uniquement, la paie reste faite ailleurs

5.4 **Facturation électronique.** La réforme française rend la réception de factures
électroniques obligatoire pour toutes les entreprises en septembre 2026, et l'émission
suit selon la taille. Est-ce que l'application doit émettre les factures, ou est-ce que ça
reste dans ton logiciel de compta ?

- [ ] L'appli doit facturer (donc on devra traiter le sujet Factur-X / plateforme agréée)
- [ ] L'appli prépare, la compta facture
- [ ] Hors sujet pour l'instant

5.5 Faut-il gérer des documents réglementaires ? (attestation de vigilance URSSAF,
assurance décennale, PPSPS, plan de prévention, fiches de données de sécurité produits
phyto, Certiphyto, registre des passages phyto…)

> …

---

## 6. Liste complète des fonctionnalités

> Une croix par ligne : **POC**, **V1**, **+TARD** ou **NON**.
> Les lignes vides en bas de chaque tableau sont là pour que tu ajoutes ce qu'on a oublié.

### 6.1 Clients et prospects

| Fonctionnalité | POC | V1 | +TARD | NON |
|---|:--:|:--:|:--:|:--:|
| Fiche client (particulier / entreprise / syndic / collectivité) | | | | |
| Plusieurs adresses ou sites par client (copro avec 4 résidences) | | | | |
| Contacts multiples par client (gardien, président du conseil syndical…) | | | | |
| Historique complet : devis, chantiers, factures, photos, échanges | | | | |
| Notes et consignes d'accès (code portail, chien, clé, horaires) | | | | |
| Suivi commercial des prospects (relances, statut, raison de perte) | | | | |
| Import de ta base clients existante (Excel / autre logiciel) | | | | |
| … | | | | |

### 6.2 Devis et chiffrage

| Fonctionnalité | POC | V1 | +TARD | NON |
|---|:--:|:--:|:--:|:--:|
| Bibliothèque de prestations avec prix (tonte, taille, abattage, plantation…) | | | | |
| Chiffrage au temps passé (heures × taux horaire) | | | | |
| Chiffrage au métré (m², ml, unité) avec quantités | | | | |
| Déboursé sec : main d'œuvre + matériaux + engins, et marge calculée | | | | |
| Catalogue végétaux avec prix fournisseur | | | | |
| Devis en PDF à ton logo, envoyé par mail | | | | |
| Signature électronique du devis par le client | | | | |
| Relance automatique des devis sans réponse | | | | |
| Variantes / options dans un même devis | | | | |
| Photos et croquis joints au devis | | | | |
| Conversion du devis accepté en chantier planifié, en un clic | | | | |
| … | | | | |

### 6.3 Contrats d'entretien

| Fonctionnalité | POC | V1 | +TARD | NON |
|---|:--:|:--:|:--:|:--:|
| Contrat annuel avec nombre de passages prévus (ex. 12 tontes + 2 tailles) | | | | |
| Génération automatique des passages dans le planning sur toute l'année | | | | |
| Calendrier saisonnier (tonte d'avril à octobre, taille en hiver…) | | | | |
| Suivi « passages réalisés / passages dus » et alerte si on est en retard | | | | |
| Facturation automatique : mensuelle, trimestrielle, à l'avance, au passage | | | | |
| Reconduction tacite et alerte avant échéance | | | | |
| Révision annuelle des prix (indice, %) | | | | |
| Rentabilité du contrat : heures réellement passées vs forfait vendu | | | | |
| Avenants / prestations hors contrat facturées en plus | | | | |
| Compte-rendu de passage envoyé automatiquement au client | | | | |
| … | | | | |

### 6.4 Chantiers et travaux

| Fonctionnalité | POC | V1 | +TARD | NON |
|---|:--:|:--:|:--:|:--:|
| Fiche chantier : adresse, client, prestations, consignes, contacts | | | | |
| Étapes / phases avec avancement (%) | | | | |
| Budget prévu vs réalisé (heures, matériaux, engins) en temps réel | | | | |
| Photos avant / pendant / après, classées automatiquement | | | | |
| Check-list de travaux à cocher sur place | | | | |
| Bon d'intervention signé par le client sur le téléphone | | | | |
| Gestion des réserves et de la reprise | | | | |
| Sous-traitance (qui, combien, suivi) | | | | |
| Évacuation des déchets verts / bordereau de déchets | | | | |
| … | | | | |

### 6.5 Planning et affectation des équipes

| Fonctionnalité | POC | V1 | +TARD | NON |
|---|:--:|:--:|:--:|:--:|
| Planning semaine par équipe, en glisser-déposer | | | | |
| Planning à la journée pour l'ouvrier (« ce que je fais aujourd'hui ») | | | | |
| Affectation nominative des personnes aux chantiers | | | | |
| Gestion des indisponibilités (congés, arrêts, formation) | | | | |
| Report automatique en cas d'intempéries, avec météo intégrée | | | | |
| Ordre de passage / tournée optimisée dans la journée | | | | |
| Temps de trajet entre deux chantiers | | | | |
| Notification à l'ouvrier quand son planning change | | | | |
| Vue « charge » : est-ce que je peux accepter ce chantier en mai ? | | | | |
| … | | | | |

### 6.6 Heures et pointage

| Fonctionnalité | POC | V1 | +TARD | NON |
|---|:--:|:--:|:--:|:--:|
| Pointage début / fin de journée par l'ouvrier | | | | |
| Pointage par chantier (départ et arrivée sur chaque chantier) | | | | |
| Saisie manuelle des heures a posteriori (oubli de pointage) | | | | |
| Pointage groupé : le chef pointe pour toute son équipe | | | | |
| Pauses, temps de trajet, temps d'atelier / dépôt | | | | |
| Validation des heures par le manager avant export | | | | |
| Calcul heures supplémentaires / modulation | | | | |
| Paniers repas, zones et petits déplacements | | | | |
| Congés et absences : demande par l'ouvrier, validation par le manager | | | | |
| Export vers la paie (format à préciser) ou vers le cabinet comptable | | | | |
| Récap hebdo des heures par personne et par chantier | | | | |
| … | | | | |

### 6.7 Équipe, RH et compétences

| Fonctionnalité | POC | V1 | +TARD | NON |
|---|:--:|:--:|:--:|:--:|
| Fiche salarié (contrat, coût horaire, contact d'urgence) | | | | |
| Compétences et habilitations (CACES, Certiphyto, permis EB, élagage, SST) | | | | |
| Alerte avant expiration d'une habilitation ou d'une visite médicale | | | | |
| Saisonniers et intérimaires | | | | |
| Coût horaire chargé par personne, pour calculer la rentabilité | | | | |
| … | | | | |

### 6.8 Matériel, véhicules et stock

| Fonctionnalité | POC | V1 | +TARD | NON |
|---|:--:|:--:|:--:|:--:|
| Parc matériel (tondeuses, débroussailleuses, mini-pelle, remorques…) | | | | |
| Affectation du matériel à une équipe / un chantier | | | | |
| Entretien et révisions à échéance (heures moteur, dates) | | | | |
| Pannes signalées depuis le terrain avec photo | | | | |
| Véhicules : contrôle technique, assurance, carburant | | | | |
| Stock de consommables (huile, fil, engrais, terreau, végétaux) | | | | |
| Commandes fournisseurs et réception | | | | |
| Coût du matériel imputé au chantier | | | | |
| … | | | | |

### 6.9 Facturation et finances

| Fonctionnalité | POC | V1 | +TARD | NON |
|---|:--:|:--:|:--:|:--:|
| Facture depuis un devis ou un contrat, en PDF | | | | |
| Acomptes et situations (chantiers longs) | | | | |
| Taux de TVA multiples selon la prestation | | | | |
| Crédit d'impôt services à la personne (petit jardinage chez particuliers) | | | | |
| Suivi des paiements et relances impayés | | | | |
| Export comptable / connexion au cabinet | | | | |
| Facture électronique conforme à la réforme 2026 | | | | |
| Tableau de bord : CA, encours, marge | | | | |
| … | | | | |

### 6.10 Relation client

| Fonctionnalité | POC | V1 | +TARD | NON |
|---|:--:|:--:|:--:|:--:|
| Envoi automatique d'un compte-rendu après chaque passage (photos incluses) | | | | |
| SMS / mail « on passe demain chez vous » | | | | |
| Espace client en ligne (devis, factures, historique, demandes) | | | | |
| Formulaire de demande d'intervention depuis ton site web | | | | |
| Demande d'avis Google après chantier | | | | |
| … | | | | |

### 6.11 Pilotage et rentabilité

| Fonctionnalité | POC | V1 | +TARD | NON |
|---|:--:|:--:|:--:|:--:|
| Rentabilité par chantier : heures vendues vs heures réalisées | | | | |
| Rentabilité par contrat d'entretien | | | | |
| Rentabilité par client et par équipe | | | | |
| Taux d'occupation des équipes | | | | |
| Prévisionnel de charge sur les prochaines semaines | | | | |
| Export Excel de tout | | | | |
| … | | | | |

### 6.12 Ce qu'on a oublié

Ajoute ici tout ce qui manque, même si ça te paraît évident :

> …

---

## 7. Intégrations avec l'existant

7.1 Avec quels outils l'application devrait-elle communiquer ? (coche et précise le nom)

- [ ] Logiciel de comptabilité / expert-comptable → …
- [ ] Logiciel de paie → …
- [ ] Banque (rapprochement des paiements) → …
- [ ] Agenda Google / Outlook → …
- [ ] Boîte mail (envoi des devis/factures depuis ton adresse) → …
- [ ] Ton site internet → …
- [ ] Google Maps / navigation → …
- [ ] Boîtier télématique des véhicules → …
- [ ] Aucun pour l'instant

---

## 8. Données, sécurité, engagement

8.1 Quel volume approximatif ? _(nombre de clients actifs, de chantiers par an, de devis
par mois — même une estimation au doigt mouillé aide)_

> Clients : ____  Chantiers/an : ____  Devis/mois : ____  Factures/mois : ____

8.2 Combien d'années d'historique faut-il conserver et pouvoir consulter ?

> …

8.3 Que se passe-t-il si l'application est indisponible une demi-journée un mardi matin ?

- [ ] C'est gênant mais on fait sans
- [ ] C'est très grave, les équipes sont bloquées

8.4 Qui, chez toi, sera responsable de la configuration (créer les comptes, les tarifs,
les modèles de devis) ?

> …

---

## 9. Le POC : ce qu'on construit en premier

> Un POC réussi, ce n'est pas une petite version de tout : c'est **une** chaîne complète qui
> marche vraiment, en conditions réelles, sur un seul scénario. Le reste vient après.

9.1 Parmi ces scénarios, lequel doit marcher de bout en bout dans le POC ?
_(classe-les 1, 2, 3 — le 1 sera développé en premier)_

| # | Scénario | Rang |
|---|---|:---:|
| A | **La journée de l'ouvrier** : il ouvre son téléphone, voit ses chantiers du jour, pointe ses heures, prend des photos, clôture. Le manager voit les heures le soir et les valide. | |
| B | **Le contrat d'entretien** : je crée un contrat 12 passages, l'appli remplit mon planning de l'année, chaque passage réalisé génère un compte-rendu, et la facturation suit. | |
| C | **Du devis au chantier** : je fais un devis chez le client, il le signe, ça devient un chantier planifié avec une équipe affectée. | |
| D | **Le planning du lundi matin** : je répartis mes 3 équipes sur la semaine en glisser-déposer, chacun reçoit son planning. | |
| E | Autre → … | |

9.2 Quels sont les **5 écrans** que tu veux absolument voir dans la démo du POC ?

> 1. …
> 2. …
> 3. …
> 4. …
> 5. …

9.3 Comment saura-t-on que le POC est un succès ? Donne un critère mesurable.
_(ex. « pendant 2 semaines, mes 2 équipes ont pointé toutes leurs heures dans l'appli et
je n'ai rien eu à recopier dans Excel »)_

> …

9.4 Es-tu prêt à faire tester le POC en vrai sur tes chantiers, avec de vrais salariés ?
Combien de personnes, et à partir de quand ?

> …

9.5 Qu'est-ce qui, chez tes gars, risque de faire capoter l'adoption ?
_(âge, rapport au téléphone, peur du flicage, mains sales et gants, écran au soleil…)_

> …

9.6 Quelle est ta contrainte de calendrier ? Une échéance à ne pas rater ?
_(la reprise de saison en mars, un salon, un rendez-vous banque…)_

> …

---

## 10. Budget et organisation

10.1 Quelle enveloppe et quel modèle envisages-tu ? (investissement, association,
prestation payée, parts dans la société…)

> …

10.2 Combien de temps peux-tu consacrer au projet par semaine ? (tests, retours,
relectures — c'est ce qui fait la différence entre un bon et un mauvais produit)

> …

10.3 Qui décide en cas d'arbitrage sur le périmètre ?

> …

---

## 11. Questions libres

Tout ce que tu veux ajouter, ou les questions que tu te poses et auxquelles on n'a pas
répondu ici :

> …
