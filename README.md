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

## 🔮 Partie 4 : Modélisation prédictive

### 🎯 Objectif  
Prédire le **nombre de passagers** par course (`passenger_count`) selon :  
- Variables temporelles : `hour`, `day_of_week`,  
- Variables spatiales : `PULocationID`,  
- Variables météo : `tmpf`, `precipitation`, `wind_speed`, `wxcodes`.

### ⚙️ Pipeline ML  
- Export d’un échantillon propre depuis BigQuery  
- Préparation des features avec `pandas`  
- Modèle : `RandomForestRegressor` (non-linéaire, robuste)  
- Évaluation : RMSE, MAE sur données de test

### 🧪 Résultats (prévision 1 course)  
- **RMSE ≈ 0.85 passager**  
- **MAE ≈ 0.62 passager**  
- **Top variables prédictives** : `hour`, `wxcodes`, `tmpf`, `PULocationID`

### 💡 Perspectives  
- Intégration du modèle dans le pipeline Airflow  
- API de prédiction à déployer avec GCP (Cloud Run ou Vertex AI)  
- Affichage dans le dashboard Power BI

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
