# LimeSurvey + MySQL + Excel + Looker Studio

## Présentation du projet

Ce projet met en place un système complet de suivi d’enquêtes téléphoniques en temps réel.

Le système utilise :

- LimeSurvey
- MySQL
- Python
- Excel
- Google Sheets
- Looker Studio

L’objectif est de :

- suivre les réponses des participants,
- suivre les performances des enquêteurs,
- analyser les heures de pointe,
- mesurer les taux de complétion,
- automatiser le suivi des questionnaires,
- visualiser les données dans un dashboard interactif.

---

# Architecture générale du système

```text
Participants
      ↓
Questionnaire LimeSurvey
      ↓
Base de données MySQL
      ↓
Script Python automatique
      ↓
Fichiers Excel
      ↓
Google Sheets / Looker Studio
```

---

# 1. Installation de LimeSurvey

LimeSurvey est utilisé pour créer et administrer les questionnaires.

Fonctionnalités utilisées :

- création du questionnaire,
- génération des tokens,
- gestion des participants,
- stockage des réponses,
- attribution des enquêteurs.

## Interface d’installation

L’image suivante montre l’installation de LimeSurvey.

<img width="1137" height="522" alt="LimeSurvey-Instalar-Encuesta-01" src="https://github.com/user-attachments/assets/aafc5ebd-740a-44a2-a7bb-12cb972b9383" />



### Explication

Cette étape permet :

- de configurer la langue,
- d’initialiser LimeSurvey,
- de préparer la connexion à MySQL,
- de créer l’administrateur.

---

# 2. Base de données MySQL

Toutes les réponses du questionnaire sont automatiquement stockées dans MySQL.

La table principale utilisée est :

```sql
lime_responses_851155
```

## Table des réponses

<img width="1858" height="151" alt="limeresponse" src="https://github.com/user-attachments/assets/154df8e7-5d97-4721-a21f-7f713ef53945" />


### Explication

Cette table contient toutes les réponses envoyées par les participants.

Chaque ligne correspond à une réponse.

Colonnes importantes :

| Colonne | Description |
|---|---|
| token | identifiant unique du participant |
| submitdate | date de soumission |
| Q4 | code enquêteur |
| Q5 | variable supplémentaire |

---

# 3. Exemple de réponses enregistrées

L’image suivante montre les réponses stockées dans MySQL.

<img width="1506" height="354" alt="Capture7" src="https://github.com/user-attachments/assets/e9af334b-e100-4457-9bf7-6091305bd91c" />


### Explication

Exemple :

| token | Q4 |
|---|---|
| byRZ1Y6My0HOdXn | AO01 |
| HyIQQxG4wriZRAa | AO02 |

Le champ :

```text
Q4
```

correspond à l’enquêteur.

Par exemple :

| Code | Enquêteur |
|---|---|
| AO01 | Adam |
| AO02 | Nour |

Cette information est utilisée ensuite dans Excel et dans le dashboard.

---

# 4. Automatisation avec Python

Python est utilisé pour connecter :

- MySQL,
- Excel,
- les fichiers enquêteurs.

Bibliothèques utilisées :

```python
pymysql
openpyxl
collections
```

Le script Python réalise automatiquement :

- lecture des réponses MySQL,
- détection des tokens ayant répondu,
- coloration des lignes Excel,
- mise à jour des statuts,
- comptage des formulaires par enquêteur.

---

# 5. Fichier Excel principal

Le fichier Excel contient tous les participants.

## Exemple du fichier Excel

<img width="1828" height="748" alt="Capture2" src="https://github.com/user-attachments/assets/9edb7af6-5240-4a81-8529-6f0b22dc83a8" />


### Explication

Colonnes importantes :

| Colonne | Description |
|---|---|
| firstname | prénom |
| lastname | nom |
| email | email |
| token | token LimeSurvey |
| invited | invitation envoyée |
| completed | questionnaire complété |

Lorsqu’un participant répond :

- sa ligne devient verte,
- le statut passe à :

```text
ANSWERED
```

Cela permet un suivi en temps réel.

---

# 6. Feuilles enquêteurs

Chaque enquêteur possède une feuille dédiée.

Exemple :

- adam
- nour

---

## Feuille Adam

<img width="1167" height="813" alt="Capture3" src="https://github.com/user-attachments/assets/f1e84fe4-4914-46fc-8e8e-dc0c9aedd0cf" />


### Explication

Cette feuille affiche automatiquement :

```text
Nombre de formulaire répondu
```

Le compteur est mis à jour automatiquement grâce au script Python.

---

## Feuille Nour

<img width="1245" height="783" alt="Capture4" src="https://github.com/user-attachments/assets/aafd823c-df3a-437b-bd69-bff7b9d52450" />

### Explication

Même principe que pour Adam.

Chaque enquêteur possède :

- son compteur,
- ses statistiques,
- son suivi de réponses.

---

# 7. Dashboard Looker Studio

Looker Studio permet de visualiser les données sous forme de dashboard interactif en temps reel.

## Dashboard global
<img width="652" height="487" alt="Capture" src="https://github.com/user-attachments/assets/b9e335f9-eabf-47f6-9c81-73b9a33f3932" />



---

# 8. Analyse des KPI

Le dashboard contient plusieurs indicateurs.

## Nombre de réponses

Le dashboard affiche :

- nombre total de réponses,
- nombre total d’appels,
- progression globale.

---
### Objectif

Ce graphique permet de mesurer :

- la qualité des appels,
- le taux de conversion,
- le nombre de refus,
- les mauvais numéros.

---

# 10. Analyse des heures de pointe

Le dashboard affiche également les réponses par heure.

Les heures sont regroupées :

| Heure réelle | Heure regroupée |
|---|---|
| 15:30 | 15 |
| 15:45 | 15 |
| 16:02 | 16 |

Champ utilisé dans Looker Studio :

```sql
REGEXP_EXTRACT(`Horaire du dernier appel`, "^\\d+")
```

### Objectif

Cette analyse permet :

- d’identifier les heures optimales d’appel,
- d’optimiser les performances,
- d’augmenter le taux de réponse.

---

# 11. Jauge de progression

Le dashboard contient également une jauge de progression.

Exemple :

```text
354 appels réalisés sur 2000
```

### Utilité

Cette jauge permet de suivre :

- l’avancement du projet,
- les objectifs,
- la progression globale.

---

# 12. Fonctionnement temps réel

```text
Participant répond au questionnaire
            ↓
LimeSurvey enregistre la réponse dans MySQL
            ↓
Le script Python détecte le token
            ↓
La ligne Excel devient verte
            ↓
Les compteurs enquêteurs sont mis à jour
            ↓
Google Sheets se synchronise
            ↓
Looker Studio actualise le dashboard
```

---

# 13. Technologies utilisées

| Technologie | Utilisation |
|---|---|
| LimeSurvey | Création des questionnaires |
| MySQL | Stockage des réponses |
| Python | Automatisation |
| Excel | Suivi des participants |
| Google Sheets | Synchronisation cloud |
| Looker Studio | Dashboard et visualisation |

---

# 14. Améliorations futures

Possibilités d’évolution :

- envoi automatique de SMS,
- synchronisation API,
- dashboard temps réel avancé,
- classement des enquêteurs,
- prédiction des heures optimales,
- analyse NLP des réponses ouvertes,
- déploiement cloud.

---

# 15. Cas d’utilisation

- enquêtes CATI,
- laboratoires de recherche,
- études marketing,
- ONG,
- sondages politiques,
- terrain académique.

---

# Auteur

Adam Sirri  
INSEA — Data & Software Engineering

