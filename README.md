# Bloc3-rendu-NYC

# 🚕 Yellow Taxi MeteoRide  
## ELT & Modélisation prédictive de l’activité taxi-météo à NYC

---

## 📝 Partie 1 : Cadrage du projet

### 🎯 Objectif général  
Analyser l’impact des conditions météorologiques sur les trajets de taxi à New York pour :  
- Identifier les facteurs météo influents sur la demande,  
- Construire un modèle prédictif du nombre de passagers,  
- Automatiser l’ensemble du pipeline de collecte, transformation, modélisation et visualisation.

### 🔍 Problématique  
> Comment prédire les variations d’activité des taxis à NYC en fonction de la météo pour améliorer leur allocation et réduire les trajets à vide ?

### ✅ Contraintes et opportunités  
- **Données volumineuses** (plusieurs Go/mois)  
- **Sources hétérogènes** (fichiers, API météo)  
- **Données publiques** et projet à visée RSE  
- **Besoin d’automatisation complète** pour un usage opérationnel

---

## 🧱 Partie 2 : Architecture technique

### ⚙️ Pipeline ELT


## Architecture du système
Le pipeline suit une architecture ELT robuste sur Google Cloud Platform (GCP) :

```
NYC.gov (trips CSV)  ──┐
                       │
Weather API (AOS)   ──>│
                       │
                       v
                Airflow (Orchestration)
                       │
                       v
                GCS (Stockage brut)
                       │
                       v
               BigQuery (tables raw)
                       │
                       v
                    dbt (Transformation)
                       │
                       v
                BigQuery (tables marts)
                       │
                       v
                     Power BI
```


### 📦 Technologies utilisées

| Outil         | Rôle                                 |
|---------------|--------------------------------------|
| Airflow       | Orchestration des tâches ETL         |
| GCS           | Stockage des fichiers bruts          |
| BigQuery      | Entrepôt de données scalable         |
| dbt           | Nettoyage, transformation, tests     |
| Power BI      | Visualisation analytique             |
| Scikit-learn  | Modélisation prédictive (ML)         |
| GitHub/Trello | Suivi projet, CI/CD, documentation   |

---

## 🧠 Partie 3 : Pipeline et composantes

### 🔹 Étape 1 – Extraction  
- Fichiers taxi mensuels récupérés depuis NYC.gov  
- Données météo collectées via l’API ASOS (observations horaires)  
- Scripts : `extract.py`, `extract_weather.py`

### 🔹 Étape 2 – Chargement  
- Stockage des fichiers dans GCS (bronze)  
- Ingestion dans BigQuery : deux tables `raw_taxi_trips` et `raw_weather_data`  
- Détection de doublons, gestion des erreurs

### 🔹 Étape 3 – Transformation avec dbt  
- Nettoyage des nulls et formats incohérents  
- Uniformisation des dates, formats géographiques  
- Mapping des zones taxis ↔ stations météo  
- Création de tables marts prêtes pour analyse et modélisation  
- Tests qualité : `not_null`, `accepted_values`, `unique`

---

## 🔮 Partie 4 : Modélisation prédictive distribuée (Dataproc + PySpark)

### 🎯 Objectif

Construire un **modèle prédictif du nombre de passagers (`passenger_count`)** en fonction :
- des caractéristiques temporelles (`hour`, `day_of_week`)
- des conditions météorologiques (`tmpf`, `precipitation`, `wind`, `wxcodes`)
- de la localisation de prise en charge (`PULocationID`)

---

### ⚙️ Architecture de modélisation

```
BigQuery (tables marts)  
        │
        ▼
Dataproc (cluster Spark)  
        │
        ▼
PySpark (prétraitement + entraînement MLlib)
        │
        ▼
GCS (sauvegarde du modèle entraîné)
```

---

### 🔁 Pipeline de Machine Learning

#### 1. Chargement des données
- Connexion directe à **BigQuery** depuis **PySpark** à l’aide du **Spark BigQuery Connector**
- Lecture d’un échantillon nettoyé depuis les tables `marts`

#### 2. Préparation des données
- Sélection des colonnes : `hour`, `day_of_week`, `PULocationID`, `tmpf`, `precipitation`, `wxcodes`
- Transformation via `VectorAssembler`
- Encodage des variables catégorielles si nécessaire

#### 3. Entraînement du modèle
- Utilisation de `RandomForestRegressor` depuis **Spark MLlib**
- Séparation des données en **80% train / 20% test**
- Réglage de quelques hyperparamètres de base (ex. nombre d’arbres)

#### 4. Évaluation
- Calcul des métriques : `RMSE`, `MAE`, `R²`
- Résultats loggués depuis le cluster Dataproc

#### 5. Sauvegarde
- Le modèle est sauvegardé dans un bucket **GCS** au format MLlib (`.sav`)
- Exporté pour être utilisé localement ou déployé via API sur GCP

---

### 📊 Résultats obtenus

| Métrique        | Valeur approximative |
|-----------------|----------------------|
| RMSE            | ~0.85 passager       |
| MAE             | ~0.62 passager       |
| Variables clés  | `hour`, `wxcodes`, `tmpf`, `PULocationID` |

---

### 🚀 Étapes suivantes

- Intégrer le script d’entraînement dans **Airflow** via `DataprocSubmitJobOperator`
- Export du modèle au format `.pkl` ou `.onnx` pour déploiement
- Création d’un **endpoint API (Flask + Cloud Run)** pour rendre le modèle accessible
- Déploiement automatisé avec **GitHub Actions (CI/CD)**

---


## 🚦 Partie 5 : Suivi de projet et pilotage

### 🧰 Méthodologie  
- Méthode **Kanban** (adaptative, visuelle)  
- Suivi via **GitHub Projects** ou **Trello**  
- Reporting hebdomadaire (progression, blocages, tests)

### 📊 Indicateurs de pilotage  
- % de fichiers traités vs attendus  
- Taux de réussite des tâches Airflow  
- Nombre de lignes transformées (bronze → marts)  
- Performance du modèle ML : RMSE, MAE

### 🔄 Rituels projet  
- Daily async (10 minutes)  
- Démo fin de semaine (DAGs, modèles, visualisations)

---

## 👥 Partie 6 : Équipe et gouvernance

### 💼 Équipe fictive
- **Data Engineer** : Ines, Ousmane  
- Supervision pédagogique : enseignants, pairs reviewers

### 🎓 Montée en compétence  
- Documentation officielle Airflow / GCP / dbt  
- Formation continue via la réalisation du projet

---

## 🌱 Partie 7 : Éthique, sécurité et RSE

### ✅ Données  
- **Open Data** uniquement  
- **Aucune donnée personnelle** collectée

### 🔐 Sécurité  
- Accès restreints via `credentials.json`  
- Variables d’environnement sécurisées (GCP secrets manager si besoin)

### 🌍 Enjeux RSE  
- Optimisation de la demande taxi = réduction de trajets à vide  
- Projet reproductible, respectueux et transparent

---

## 📦 Livrables pour le jury

- 🧾 Document PDF structuré (ce livrable)  
- 📸 Captures d’écran du DAG Airflow, tables BigQuery, dbt models  
- 📁 Fichiers : `extract.py`, `dbt_project.yml`, `modélisation_rf.ipynb`, etc.  
- 📊 Dashboard Power BI (ou maquette)  
- 🗂 Diagramme de Gantt ou board Trello exporté

---

## ✅ Synthèse finale

> Ce projet démontre la capacité à concevoir un pipeline ELT de bout en bout sur GCP, enrichi d'une brique de modélisation prédictive, dans un contexte réaliste et réplicable. Il combine des outils Cloud modernes (Airflow, BigQuery, dbt, Scikit-learn), une gouvernance projet agile, et une démarche responsable orientée impact.
