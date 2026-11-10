# Planisage — Réponses au questionnaire de cadrage

**Rempli par :** Eliot · **Date :** 20/09/2026 · Questionnaire : [01-questionnaire-cadrage.md](01-questionnaire-cadrage.md)

## Ce qu'il faut retenir

- **Le périmètre est validé tel quel** (7 fonctionnalités).
- **Un seul utilisateur au départ : Eliot lui-même, à partir de janvier 2027.** Le POC doit être utilisable sur le terrain à cette date.
- **Critères de réussite** : la génération des plannings, les heures par semaine sur chantier et le **compte-rendu client**.
- **Aujourd'hui, tout est sur papier** (voir [03-fiches-papier-eliot.md](03-fiches-papier-eliot.md)).

## 1. Périmètre

| Question | Réponse |
|---|---|
| 1.1 Le périmètre est-il bon ? | Oui, on part là-dessus |

## 2. 🔴 Les six questions bloquantes

| Question | Réponse | Conséquence pour la conception |
|---|---|---|
| 2.1 Le nombre de passages est-il ferme ou indicatif ? | **Ça dépend du client.** Si l'herbe ne pousse pas, on peut faire d'autres tâches à la place (désherbage, petite taille). | Le contrat porte une option ferme / indicatif. Un passage prévu peut être **substitué** par d'autres prestations : on suit plutôt un budget de passages ou d'heures qu'une liste figée de tâches. |
| 2.2 Qui saisit les heures ? | Le **chef d'équipe**, pour toute son équipe | Le mobile sert au chef d'équipe et M03 saisit le temps de l'équipe, pas celui d'un individu. C'est cohérent avec les fiches papier, où les heures sont cumulées. |
| 2.3 Qui voit les codes d'accès ? | **Seulement les chefs d'équipe** | Deux rôles suffisent : manager et chef d'équipe. |
| 2.3b Tracer qui consulte quel code ? | Non, « c'est de la paranoïa » | L'écran C05 (journal des consultations) est abandonné. |
| 2.4 Regroupement géographique | **Par temps de trajet**, sans logique de zone. Secteurs habituels : Meursault, Fontaine-lès-Dijon | Le moteur regroupe d'après les temps de trajet entre sites, pas d'après des zones. Pas de secteurs à dessiner sur la carte. |
| 2.5 Déclaré services à la personne ? | **Oui** | F6 (attestation fiscale) est testable en conditions réelles. |
| 2.6 Comment savoir qu'il a été payé ? | Il coche « payée le… » sur ses factures dans l'application | Il faut une entité Facture minimale avec une date de paiement. Pas de rapprochement bancaire. |

## 3. 🔴 Données à recevoir

| Question | Réponse |
|---|---|
| 3.1 Données envoyées | Une photo du planning (en pratique, nous avons reçu les fiches client et les comptes rendus papier) |
| 3.2 Outil actuel | Papier |

Il manque encore : le catalogue de prestations, d'autres contrats, la liste des clients, une attestation fiscale et une facture type.

## 4. 🟠 Le terrain

| Question | Réponse |
|---|---|
| 4.2 Fréquence sans réseau | Jamais ou presque |
| 4.3 Ce qui doit marcher hors ligne | Le planning du jour et l'adresse ; les consignes et le code d'accès |
| 4.4 Hors ligne en lecture seule au début ? | Oui, acceptable pour commencer |

Conséquence : un cache en lecture seule de la journée suffit. Il contiendra des codes d'accès, donc **ces données sensibles seront stockées sur le téléphone**. Il faudra les purger en fin de journée.

## 5. 🟠 L'entreprise

| Question | Réponse |
|---|---|
| 5.3 La saison | Tonte de mars à novembre ; taille de haie 2 fois par an (printemps et automne) ; taille des arbustes selon la floraison |

Ce sont des contraintes directes pour F3 : une fenêtre saisonnière pour la tonte, deux passages espacés pour les haies, une période qui dépend de l'espèce pour les arbustes.

## 6. 🟠 Légal et sensible

| Question | Réponse |
|---|---|
| 6.1 Géolocalisation des équipes | Non |
| 6.2 Les heures servent-elles à la paie ? | Non : c'est un indicateur de gestion, la paie se fait ailleurs |

## 8. 🟢 Produit et modèle

| Question | Réponse |
|---|---|
| 8.2 Prix mensuel acceptable | Ne sait pas, dépend de la taille de l'entreprise |
| 8.3 Concurrents testés ou démarchage | Non |

## 9. Organisation

| Question | Réponse |
|---|---|
| 9.1 Test en conditions réelles | Une seule personne, lui-même, à partir de janvier |
| 9.2 Critères de réussite | Génération des plannings, heures par semaine sur chantier, compte-rendu client |

## Sans réponse

4.1, 4.5, 5.1, 5.2, 6.3, 7.1, 7.2, 8.1, 8.4, 9.3, 9.4, 10.1.
