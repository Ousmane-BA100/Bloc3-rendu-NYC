# 🚕 Yellow Taxi MeteoRide – Projet Data

> Projet fictif – Bloc 3 Ynov : Élaborer et piloter un projet Data  
> Auteurs : Ousmane BA & collaborateur (2 Data Engineers juniors)  
> Durée estimée : 8 semaines

---

## 🧠 Objectif général

Anticiper la demande de taxis à New York en fonction des conditions météo via :
- Un pipeline ELT sur GCP
- Un entrepôt de données structuré
- Un modèle prédictif (nombre de passagers)
- Un tableau de bord décisionnel
- Une API déployée sur Cloud Run

---

## 📁 Table des matières

1. [🎯 Cadrage du projet](#🎯-cadrage-du-projet)  
2. [📐 Dimensionnement](#📐-dimensionnement)  
3. [🧱 Architecture technique](#🧱-architecture-technique)  
4. [🔄 Pipeline détaillé](#🔄-pipeline-détaillé)  
5. [🤖 Modélisation prédictive](#🤖-modélisation-prédictive)  
6. [📊 Pilotage du projet](#📊-pilotage-du-projet)  
7. [👥 Équipe & routines](#👥-équipe--routines)  
8. [🌱 Éthique & veille](#🌱-éthique--veille)  
9. [📦 Annexes](#📦-annexes)

---

## 🎯 Cadrage du projet

### Problématique
> Comment prédire l’activité des taxis à NYC selon les conditions météo pour optimiser la répartition et réduire les trajets à vide ?

### Objectifs
- Croiser trajets de taxis & météo
- Nettoyer, structurer et enrichir les données
- Construire un modèle prédictif (nombre de passagers)
- Mettre à disposition une API et un dashboard

### Contraintes
- Fichiers mensuels > 500 Mo  
- API météo horaire (quota, fiabilité)  
- Données massives à synchroniser

### Enjeux RSE
- Moins de trajets à vide → réduction CO₂  
- Reproductibilité (Open Data)  
- Pas de données personnelles → conforme RGPD

---

## 📐 Dimensionnement

- **Ressources humaines** : 2 Data Engineers juniors  
- **Durée estimée** : 8 semaines  
- **Budget fictif** : ~100€/mois sur GCP  
- **Technos** : Airflow, BigQuery, GCS, dbt, Dataproc, Flask, Streamlit, Power BI  
- **Faisabilité** : Données accessibles, pipeline validé, modèle entraîné

---

## 🧱 Architecture technique

```text
NYC.gov (trips CSV) ─┐
                     ├─> Airflow (orchestration)
Weather API (ASOS) ──┘
                     ↓
        GCS (stockage brut)
                     ↓
        BigQuery (tables raw)
                     ↓
               dbt (transformation)
                     ↓
        BigQuery (tables marts)
                     ↓
   ↙︎                           ↘︎
Dataproc (modélisation)       Power BI
              ↓
      API Flask + Streamlit
              ↓
     Docker + Cloud Run (CI/CD)

## 5. 🔄 Étapes détaillées du projet

### Semaine 1 : Extraction
- Automatisation de l’ingestion des données taxi (fichiers mensuels CSV) via Airflow.
- Récupération des données météo via l’API ASOS (observations horaires).
- Stockage brut des fichiers dans Google Cloud Storage (GCS).

### Semaine 2 : Chargement
- Chargement des fichiers brut dans BigQuery (2 tables : `raw_taxi_trips` et `raw_weather_data`).
- Contrôles de qualité : format, complétude, détection de doublons.

### Semaine 3 : Transformation
- Utilisation de dbt pour :
  - Nettoyer les nulls, uniformiser les formats (dates, température, ID zones).
  - Créer des dimensions temporelles.
  - Mapper les stations météo aux zones taxi.
- Création de tables marts prêtes pour modélisation et visualisation.

### Semaine 4–5 : Visualisation (Power BI)
- Connexion Power BI à BigQuery pour visualisation en temps réel.
- Création d’un tableau de bord interactif :
  - Nombre moyen de passagers par heure/jour.
  - Influence de la météo.
  - Carte des zones de forte activité.

### Semaine 6 : Modélisation prédictive
- Export des données depuis BigQuery via Spark-BigQuery Connector.
- Prétraitement et entraînement sur Dataproc avec PySpark.
- Modèle : `RandomForestRegressor`.
- Évaluation du modèle (RMSE ≈ 0.85, MAE ≈ 0.62).
- Sauvegarde du modèle dans GCS.

### Semaine 7 : Développement API et interface
- Création d’une API Flask pour exposer le modèle.
- Création d’une interface Streamlit pour tester les prédictions.
- Conteneurisation avec Docker.

### Semaine 8 : Déploiement
- Déploiement de l’API et de l’interface sur **GCP Cloud Run**.
- Mise en place du CI/CD avec **GitHub Actions**.
- Tests de bout en bout sur environnement cloud.

---

## 6. 🤖 Modélisation prédictive

### Objectif
Prédire le `passenger_count` (nombre de passagers) à partir de caractéristiques contextuelles, temporelles et météorologiques.

### Exemples de variables utilisées (features) :
- **hour** : heure de la course (0–23)
- **is_business_day** : jour ouvré ou non (1 = oui, 0 = non)
- **weather** : indice météo (0 = clair, 1 = pluie)
- **temp_avg** : température moyenne en °C
- **PULocationID** : zone géographique de départ
- **wxcodes** : code météo détaillé (brume, orage, etc.)

### Résultats :
- RMSE ≈ 0.85  
- MAE ≈ 0.62  
- Top variables : `hour`, `temp_avg`, `wxcodes`, `PULocationID`

### Technologies utilisées

#### 🖥️ Backend :
- Flask 🐍 : API REST de prédiction
- PySpark ⚡ : entraînement du modèle sur Dataproc
- Docker 🐳 : conteneurisation du service

#### 🎨 Frontend :
- Streamlit 🎨 : interface utilisateur
- Docker 🐳 : conteneurisation

#### 🔧 Autres :
- Git 🔗 : versionnement
- Docker Compose ⚙️ : orchestration multi-services

---

## 7. 📊 Pilotage du projet

### Méthodologie
- **Méthode Agile – Scrum simplifié**
- Outil de gestion : **Trello**
- Organisation par sprints hebdomadaires
- Suivi des tâches : À faire → En cours → Terminé
- Réunions hebdomadaires (bilan + ajustements)

### Planning prévisionnel (8 semaines)

| Semaine   | Activité principale                                     |
|-----------|----------------------------------------------------------|
| Semaine 1 | Extraction (Airflow + API météo)                         |
| Semaine 2 | Chargement GCS → BigQuery (tables raw)                   |
| Semaine 3 | Transformation via dbt (tables marts)                    |
| Semaine 4 | Construction du dashboard Power BI                       |
| Semaine 5 | Finalisation des visualisations et mise en forme         |
| Semaine 6 | Modélisation avec PySpark sur Dataproc                   |
| Semaine 7 | Création de l’API Flask et de l’interface Streamlit      |
| Semaine 8 | Déploiement sur Cloud Run + CI/CD GitHub Actions         |

### Indicateurs de performance
- Taux de succès des DAGs Airflow
- Qualité des données transformées
- Performance du modèle (RMSE, MAE)
- Complétude des dashboards Power BI
- Nombre de tâches terminées vs prévues sur Trello

---

## 8. 👥 Équipe & routines

### Composition de l’équipe
- **2 Data Engineers Juniors**
  - Profil 1 : pipeline, BigQuery, visualisation
  - Profil 2 : modélisation, API, déploiement

### Montée en compétence
- Auto-formation sur :
  - GCP (BigQuery, Dataproc, Cloud Run)
  - dbt, Airflow, Flask, Streamlit
- Utilisation de la documentation officielle et de tutoriels spécialisés

### Routines projet
- **Daily async** sur Trello
- **Sprint review hebdomadaire**
- **Partage de démos et captures d’écran**
- **Feedback croisé** entre les membres de l’équipe

---

## 9. 🌱 Éthique & veille

### Veille technologique
- BigQuery ML, dbt v1.6, Spark 3.5
- GCP Data products : Vertex AI, Looker Studio
- Sources : Medium, TowardsDataScience, blogs GCP

### Veille réglementaire
- Respect RGPD : aucune donnée personnelle
- Stockage sécurisé sur GCP
- Gouvernance des accès : credentials sécurisés

### Enjeux RSE
- Optimisation de la mobilité = réduction CO₂
- Exploitation d’Open Data pour l’intérêt général
- Système reproductible, transparent et responsable

---

## 10. 📦 Annexes

- 📸 Captures d’écran :
  - DAG Airflow
  - Tables BigQuery
  - Dashboard Power BI
  - Interface Streamlit

- 📁 Fichiers :
  - `dbt_project.yml`
  - `docker-compose.yml`
  - Script d’entraînement PySpark
  - Script API Flask

- 🔗 Liens :
  - [GitHub du projet](#)
  - [Trello export](#)
  - [Vidéo démo (optionnel)](#)

---

## ✅ Conclusion

Ce projet fictif a permis de démontrer la capacité à :

- Structurer un projet Data de bout en bout (ELT → Modèle → API)
- Utiliser efficacement les services GCP
- Mettre en place une modélisation prédictive distribuée
- Créer une application déployable, maintenable et visualisable

**Yellow Taxi MeteoRide** est un projet à visée pédagogique mais réaliste, aligné sur les attentes d’un Data Engineer moderne.

---

