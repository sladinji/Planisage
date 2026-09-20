# Planisage — Questionnaire de cadrage

**À remplir par :** _(nom du paysagiste)_ · **Date :** ____ / ____ / 2026

Le périmètre du POC est arrêté (§1). Ce questionnaire ne sert donc plus à choisir
**quoi** faire, mais à débloquer la construction : chaque réponse manquante nous force
à deviner, et une mauvaise devinette se paie en semaines de développement.

| Priorité | Signification |
|---|---|
| 🔴 | Bloquant — on ne peut pas commencer sans la réponse |
| 🟠 | Nécessaire avant la fin du POC |
| 🟢 | Pour la suite, réponds si tu as le temps |

> _Une version longue et exhaustive de ce questionnaire (90 fonctionnalités à arbitrer)
> existe dans l'historique Git du dépôt. Elle resservira au moment de cadrer la V1._

---

## 1. Ce qui est déjà décidé — à valider

Le POC couvrira ces sept fonctionnalités, et rien d'autre :

1. Saisie des clients et des sites, avec les informations sensibles (codes d'accès, clés, consignes)
2. Ajout de tâches dans le planning
3. Contraintes sur les tâches (ex. contrat de 12 tontes par an)
4. Génération automatique du planning à partir de ces contraintes
5. Suivi : heures réalisées chez un client, reste à faire
6. Bordereau de déduction fiscale envoyé au client
7. Carte de répartition des clients

**Est-ce bien ça ?**

- [ ] Oui, on part là-dessus
- [ ] Non, il manque quelque chose d'indispensable → …
- [ ] Il y a une de ces sept choses dont je n'ai pas vraiment besoin → …

---

## 2. 🔴 Les six questions qui bloquent la conception

### 2.1 Le quota d'un contrat est-il ferme ou indicatif ?

- [ ] **Ferme** — 12 tontes vendues, 12 tontes dues, je dois pouvoir prouver que je les ai faites
- [ ] **Indicatif** — on tond quand l'herbe pousse, ça fait à peu près 12 fois dans l'année
- [ ] Ça dépend du client → dans quels cas ? …

> Ces deux réponses ne produisent pas le même logiciel. La première se planifie un an
> à l'avance et se contrôle ; la seconde demande un « prochain passage souhaitable »
> recalculé en permanence. C'est la question la plus structurante de tout le projet.

### 2.2 Qui saisit les heures ?

- [ ] Chaque ouvrier, sur son téléphone
- [ ] Le chef d'équipe, pour toute son équipe
- [ ] Le manager, le soir ou le lendemain
- [ ] Personne pour l'instant, on verra plus tard

> Impact direct : la première réponse ajoute quatre écrans mobiles au POC.

### 2.3 Qui a le droit de voir un code d'accès client ?

- [ ] Tous les salariés
- [ ] Seulement ceux qui ont une intervention prévue sur ce site, le jour même
- [ ] Seulement les chefs d'équipe
- [ ] Moi seul, je les donne de vive voix

Et : veux-tu savoir **qui a consulté quel code et quand** ?

- [ ] Oui, c'est important — en cas de problème chez un client je dois pouvoir répondre
- [ ] Non, c'est de la paranoïa

### 2.4 Comment regroupes-tu tes clients géographiquement ?

Le moteur doit éviter de faire traverser le département trois fois dans la journée.
Sur quelle base regroupes-tu, dans ta tête, les chantiers d'une même journée ?

- [ ] Par commune
- [ ] Par code postal
- [ ] Par secteurs que j'ai dans la tête, que je pourrais dessiner sur une carte
- [ ] Par temps de trajet, sans logique de zone

Cite deux ou trois de tes secteurs habituels :

> …

### 2.5 Es-tu déclaré « services à la personne » ?

- [ ] Oui, numéro de déclaration : …
- [ ] Non
- [ ] Je ne sais pas / c'est mon comptable qui gère

> Sans cette déclaration, l'attestation fiscale n'a pas de valeur légale et la
> fonctionnalité n'est pas testable chez toi.

Quelles prestations de ton catalogue sont éligibles au crédit d'impôt ?

> …

### 2.6 Le suivi des paiements

L'attestation fiscale se calcule sur les sommes **réellement encaissées** dans l'année,
pas sur les factures émises : une facture de décembre payée en janvier bascule sur
l'année suivante. L'application a donc besoin de savoir quand tu es payé.

- [ ] Je peux cocher « payée le … » sur mes factures dans l'application _(le plus simple)_
- [ ] Il faut que ça vienne automatiquement de ma banque ou de ma compta
- [ ] Je préfère saisir les montants annuels à la main une fois par an

---

## 3. 🔴 Ce qu'on a besoin de recevoir

Le moteur de génération sera testé sur tes vraies données, pas sur des exemples
inventés. C'est la seule façon de savoir s'il tient la route.

| À fournir | Format | Fourni |
|---|---|:--:|
| Ton **catalogue de prestations** : libellé, durée standard, unité, prix, éligible au crédit d'impôt | Excel ou liste écrite | ☐ |
| **3 à 5 vrais contrats d'entretien**, y compris un compliqué | PDF, noms masqués si tu préfères | ☐ |
| Un **export de tes clients** actuels | Excel, ou ce que ton outil sait sortir | ☐ |
| Une **photo ou capture de ton planning** d'une semaine de pleine saison | photo du tableau, capture d'écran | ☐ |
| Une **attestation fiscale** que tu as déjà envoyée, si tu en fais | PDF | ☐ |
| Une **facture** type | PDF | ☐ |

---

## 4. 🟠 Le terrain

### 4.1 Sur quoi travaillent les gars sur le chantier ?

- [ ] Leur téléphone perso — plutôt Android / plutôt iPhone (entoure)
- [ ] Un téléphone fourni par l'entreprise
- [ ] Une tablette par camion
- [ ] Rien, ils ne saisissent rien

### 4.2 Le hors connexion

À quelle fréquence es-tu réellement sans réseau sur un chantier ?

- [ ] Jamais ou presque
- [ ] Parfois — quelques chantiers isolés
- [ ] Souvent — c'est bloquant

Si ça arrive, qu'est-ce qui doit **absolument** continuer à marcher ?

- [ ] Voir le planning du jour et l'adresse
- [ ] Voir les consignes et le code d'accès
- [ ] Pointer ses heures
- [ ] Prendre des photos
- [ ] Rien, ça peut attendre le retour du réseau

Accepterais-tu, pour le POC, un hors-connexion **en lecture seule** — on voit son
planning téléchargé le matin, mais la saisie attend le réseau ?

- [ ] Oui, acceptable
- [ ] Non, la saisie hors ligne est indispensable dès le départ

> La synchronisation hors ligne en écriture est le poste qui peut doubler le coût de la
> partie mobile. D'où l'insistance.

### 4.3 Qu'est-ce qui risque de faire capoter l'adoption chez tes gars ?

_(âge, rapport au téléphone, peur du flicage, gants et mains sales, écran au soleil…)_

> …

---

## 5. 🟠 Ton entreprise en chiffres

| | Aujourd'hui | Dans 3 ans |
|---|---|---|
| Gérant / conducteur de travaux | | |
| Chefs d'équipe | | |
| Ouvriers | | |
| Saisonniers, apprentis, intérim | | |
| **Équipes qui partent le matin** | | |

| | Nombre |
|---|---|
| Clients actifs | |
| Dont particuliers éligibles au crédit d'impôt | |
| Contrats d'entretien en cours | |
| Interventions par semaine en pleine saison | |
| Années d'historique à conserver | |

Ta saison : de quel mois à quel mois pour la tonte ? Et la taille ?

> …

---

## 6. 🟠 Légal et sensible

### 6.1 Géolocalisation des équipes

- [ ] Oui, je veux les voir en temps réel sur une carte
- [ ] Seulement la position au moment où l'ouvrier démarre et termine un chantier
- [ ] Non, pas de géolocalisation

> La CNIL encadre strictement ce point : la géolocalisation ne peut pas servir à
> contrôler le temps de travail s'il existe un autre moyen, elle doit être désactivable
> hors temps de travail, les salariés doivent être informés et le CSE consulté selon
> l'effectif. Selon ta réponse, on prévoit les réglages nécessaires.

### 6.2 Les heures pointées servent-elles de base à la paie ?

- [ ] Oui — donc historique inaltérable, validation du salarié, valeur probante
- [ ] Non — c'est un indicateur de gestion, la paie reste faite ailleurs

### 6.3 Y a-t-il des règles de calcul particulières ?

_(heures supplémentaires, modulation ou annualisation, paniers repas, zones et petits
déplacements, trajet domicile-chantier, intempéries)_

> …

---

## 7. 🟢 Après le POC

Sans refaire la liste complète : **cite les cinq choses** que tu voudrais voir arriver
juste après le POC, dans l'ordre.

> 1. …
> 2. …
> 3. …
> 4. …
> 5. …

_Pour t'aider à piocher : devis et signature électronique · facturation · compte-rendu
automatique au client après chaque passage · gestion du matériel et des engins ·
congés et absences · rentabilité par chantier · espace client en ligne · export vers la
compta ou la paie · report en masse pour intempéries · optimisation des tournées._

Avec quels outils l'application devra-t-elle communiquer, et lesquels ?

> Compta : … · Paie : … · Banque : … · Agenda : … · Site web : …

---

## 8. 🟢 Le produit et son modèle

Notre recommandation : une application **multi-entreprises sous notre propre marque**,
vendue par abonnement. Chaque entreprise a son espace cloisonné, on garde la relation
client, et on n'entretient qu'un seul logiciel. La marque blanche reste possible plus
tard : un produit bien conçu peut se rhabiller aux couleurs d'un revendeur, alors que
l'inverse est douloureux.

- [ ] D'accord
- [ ] Je préfère la marque blanche, parce que … 
- [ ] Je veux d'abord un outil rien que pour moi

Combien penses-tu qu'un paysagiste paierait par mois ?

> _____ € par entreprise · ou _____ € par utilisateur · ou : je ne sais pas

As-tu testé ou été démarché par des concurrents ? Qu'en as-tu pensé ?
_(Organilog, Kizeo, Twimm, Batappli, Obat, Vertuoza, Praxedo, Synchroteam, Tolteck…)_

> …

Connais-tu un réseau, une franchise, une coopérative ou un syndicat professionnel qui
pourrait être intéressé ?

> …

---

## 9. Organisation

Es-tu prêt à faire tourner le POC en vrai sur tes chantiers ? Avec combien de personnes,
et à partir de quand ?

> …

Comment saura-t-on que le POC est réussi ? Donne un critère que l'on peut mesurer.
_(ex. « pendant 3 semaines, mon planning d'entretien a été généré par l'appli et je n'ai
eu à déplacer que deux passages à la main »)_

> …

Combien de temps peux-tu consacrer au projet par semaine (tests, retours, relectures) ?

> …

Une échéance à ne pas rater ? _(reprise de saison, salon, rendez-vous banque…)_

> …

---

## 10. Questions libres

Tout ce que tu veux ajouter, et les questions que tu te poses et auxquelles on n'a pas
répondu ici :

> …
