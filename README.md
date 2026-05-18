# LimeSurvey + MySQL + Excel + Looker Studio

## Présentation du projet

Ce projet met en place un système complet de suivi d’enquêtes téléphoniques en temps réel.

Le système utilise :

- LimeSurvey
- MySQL
- Python
- Excel
- Looker Studio

L’objectif est de :

- suivre les réponses des participants,
- suivre les performances des enquêteurs,
- analyser les heures de pointe,
- mesurer les taux de complétion,
- automatiser le suivi des questionnaires,
- visualiser les données dans un dashboard interactif.

---
🔴🔴🔴🔴🔴Note : **LimeSurvey est open source, ce qui signifie qu’il aurait été possible de suivre une autre approche consistant à modifier directement le code source de LimeSurvey afin qu’il écrive automatiquement dans les fichiers Excel, la base de données et le dashboard. Cette approche aurait pu être utilisée par ma part dans le cadre d’une étude nécessitant une version modifiée de LimeSurvey adaptée aux besoins spécifiques de l’enquête. 
Pour cette démonstration, je me suis permis d’utiliser uniquement la version standard de LimeSurvey, tout en interagissant directement avec sa base de données MySQL et en exploitant les données de celle-ci.**

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

## Installation

herbergement sur XAMPP pour simuller un serveur php en loclhost:

<img width="834" height="538" alt="image" src="https://github.com/user-attachments/assets/6890515b-c319-4dac-b766-cf4dce4dcef8" />

## Interface d'Installation:

<img width="1280" height="660" alt="maxresdefault" src="https://github.com/user-attachments/assets/85612013-5e47-4648-9bf1-70b6ab3746d7" />



### Explication

Cette étape permet :

- de configurer la langue,
- d’initialiser LimeSurvey,
- de préparer la connexion à MySQL,
- de créer l’administrateur.

---

# 2. Base de données MySQL

Toutes les réponses du questionnaire sont automatiquement stockées dans MySQL utilisé par limesurvey.

La table principale utilisée est :

```sql
lime_responses_851155
```
851155 etant l'id du survey

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


Lorsqu’un participant répond :

- sa ligne devient verte,
- le statut passe à :

```text
ANSWERED
```

Cela permet un suivi en temps réel.

avec apache airflow cette modification est planifiée tout les jours pour que le suivit soit quotidien
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

Looker Studio permet de visualiser les données sous forme de dashboard interactif connecté en temps reel avec la base de données.

## Dashboard global
<img width="652" height="487" alt="Capture" src="https://github.com/user-attachments/assets/b9e335f9-eabf-47f6-9c81-73b9a33f3932" />


(Celui qui a été réalisé ci-dessus a été fait en 15 minutes uniquement dans un objectif de démonstration.)
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

- envoi automatique de SMS (en combinant Python Panda avec un service api de sms),
- dashboard temps réel avancé ,
- prédiction des heures optimales,
- analyse NLP des réponses ouvertes,
- déploiement cloud.

---


# Auteur

Adam Sirri  
INSEA — Data & Software Engineering

