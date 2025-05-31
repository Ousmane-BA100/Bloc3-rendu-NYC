# 🚖 Yellow Taxi MeteoRide – De l’Open Data à la Prédiction

**Projet fictif** – Livrable Bloc 3  
**Durée** : 8 semaines  
**Participants** : Ousmane BA et collaborateur pair  
**Contexte** : Ville de New York – Mobilité intelligente

---

## 📖 Sommaire

1. [Introduction & Contexte](#1-🎯-introduction--contexte)  
2. [Cadrage du projet (A3.1)](#2-🧭-cadrage-du-projet-a31)  
3. [Dimensionnement & faisabilité](#3-📐-dimensionnement--faisabilité)  
4. [Architecture technique & pipeline](#4-🧱-architecture-technique--pipeline)  
5. [Description des étapes du projet](#5-🔄-étapes-détaillées-du-projet)  
6. [Modélisation prédictive distribuée](#6-🤖-modélisation-prédictive-distribuée)  
7. [Pilotage et suivi du projet (A3.2)](#7-📊-pilotage-et-suivi-du-projet-a32)  
8. [Équipe projet & routines (A3.3)](#8-👥-équipe-projet--routines-a33)  
9. [Veille, sécurité & éthique (A3.4)](#9-🌱-veille-sécurité--éthique-a34)  
10. [Annexes](#10-📦-annexes)

---

## 1. 🎯 Introduction & Contexte

La ville de New York souhaite comprendre et anticiper l’impact des conditions météo sur l’activité des taxis afin d’optimiser leur répartition.

Le projet **Yellow Taxi MeteoRide** vise à :

- croiser données de trajets et météo,  
- construire un entrepôt analytique automatisé (GCP),  
- modéliser la demande en nombre de passagers,  
- fournir une interface visuelle et une API de prédiction.

---

## 2. 🧭 Cadrage du projet (A3.1)

### Problématique  
> Comment prédire les variations d’activité des taxis à NYC selon les conditions météorologiques pour améliorer la répartition des véhicules et réduire les trajets à vide ?

### Objectifs

- Construire un pipeline ELT automatisé sur GCP  
- Nettoyer et croiser données taxi & météo  
- Modéliser le nombre de passagers (ML)  
- Déployer une API de prédiction  
- Visualiser les résultats via Power BI  

### Contraintes
- Volumétrie importante (fichiers mensuels > 500 Mo)  
- Sources hétérogènes (API, CSV)  
- API météo limitée en granularité  
- Données non personnelles mais sensibles au bruit

### Enjeux RSE
- Réduction de l’empreinte CO₂ en optimisant les trajets  
- Usage de données publiques, respect RGPD  
- Approche responsable et éthique

---

## 3. 📐 Dimensionnement & faisabilité

### Ressources humaines
- 2 Data Engineers juniors  
- Collaboration en binôme : pipeline / modélisation / visualisation

### Ressources techniques
- GCP : GCS, BigQuery, Dataproc, Cloud Run  
- Airflow, dbt, Power BI, Streamlit, Flask  
- GitHub + Trello

### Charge de travail estimée
- 8 semaines, 4 grandes phases

### Budget fictif
- ~100 €/mois sur GCP (stockage, compute)  
- Aucun coût RH (auto-formation)

### Faisabilité
- Données ouvertes et testées  
- Scripts opérationnels en local  
- Modèle validé sur échantillon

---

## 4. 🧱 Architecture technique & pipeline

### Pipeline global

```
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
```


---

## 5. 🔄 Étapes détaillées du projet

### Étape 1 – Extraction (S1)
- Ingestion automatique des fichiers taxi via Airflow  
- Requête API ASOS (météo) → stockage CSV dans GCS

### Étape 2 – Chargement (S2)
- Données brutes stockées dans GCS  
- Ingestion dans BigQuery via Airflow → 2 tables `raw_taxi`, `raw_weather`

### Étape 3 – Transformation (S3)
- dbt : typage, suppression nulls, enrichissement  
- Jointure taxi ↔ météo (via `pickup_hour` et `station_id`)  
- Création des tables `marts` prêtes pour BI et ML

### Étape 4 – Visualisation (S4–S5)
- Connexion BigQuery → Power BI  
- Mise en place des filtres (zone, météo, jour)  
- Intégration de KPIs clés : volume courses, moyenne passagers, météo dominante

### Étape 5 – Modélisation (S6)
- Lecture des données via Spark-BigQuery connector  
- Préparation des variables d’entrée  
- Entraînement sur Dataproc (PySpark + RandomForest)  
- Évaluation RMSE/MAE et export du modèle dans GCS

### Étape 6 – Déploiement modèle (S7–S8)
- API REST construite avec Flask (serveur local)  
- Interface Streamlit pour test utilisateur  
- Dockerisation complète (backend + frontend)  
- Déploiement via Cloud Run (GCP) avec GitHub Actions

---

## 6. 🤖 Modélisation prédictive distribuée

### Objectif

Prédire le nombre de passagers (`passenger_count`) à partir de :

- **Hour** : influence des heures de pointe  
- **Is_business_day** : trafic différent week-end/semaine  
- **Weather** : pluie, vent, conditions extrêmes  
- **Temp_avg** : corrélé à la fréquentation  
- **PULocationID** : effet zone géographique  

### Résultats

- RMSE ≈ 0.85  
- MAE ≈ 0.62  
- Top prédicteurs : `hour`, `tmpf`, `PULocationID`, `wxcodes`

### Technologies utilisées

**Backend** :  
- Flask 🐍 : API REST  
- PySpark ⚡ : Modèle de prédiction  
- Docker 🐳 : Conteneurisation  

**Frontend** :  
- Streamlit 🎨 : Interface utilisateur  
- Docker 🐳 : Conteneurisation  

**Autres outils** :  
- Git 🔗 : Versionnement  
- Docker Compose ⚙️ : Multi-services

---

## 7. 📊 Pilotage et suivi du projet (A3.2)

### Méthodologie

- **Agile / Scrum**  
- Outil de suivi : **Trello**  
- Sprints hebdomadaires avec planning  
- Réunions 1 à 2 fois par semaine (revue + planification)

### Planning prévisionnel (8 semaines)

| Semaine   | Activité principale                                     |
|-----------|----------------------------------------------------------|
| Semaine 1 | Extraction des données taxi et météo                     |
| Semaine 2 | Chargement + création des tables `raw`                   |
| Semaine 3 | Nettoyage et transformation avec dbt                     |
| Semaine 4 | Début du dashboard Power BI (structure, connexion)       |
| Semaine 5 | Finalisation du dashboard et publication                 |
| Semaine 6 | Modélisation prédictive sur Dataproc                     |
| Semaine 7 | Développement de l’API Flask + interface Streamlit       |
| Semaine 8 | Dockerisation + déploiement via Cloud Run + CI/CD        |

### Indicateurs de pilotage

- Taux de succès des DAGs  
- RMSE/MAE du modèle  
- % d'avancement des tâches sur Trello  
- Couverture fonctionnelle du dashboard

---

## 8. 👥 Équipe projet & routines (A3.3)

### Composition

- **2 Data Engineers juniors**  
  - Membre 1 : Extraction, BigQuery, dbt, Power BI  
  - Membre 2 : Modélisation, API Flask, Streamlit, CI/CD

### Routines

- **Daily async** sur Trello  
- **Sprint planning** en début de semaine  
- **Sprint review** chaque fin de semaine  
- Démo technique hebdomadaire (Airflow UI, modèle, dashboard)

---

## 9. 🌱 Veille, sécurité & éthique (A3.4)

### Veille technologique

- Suivi de l’évolution : dbt, GCP, Spark, Power BI  
- Sources : Medium, newsletters Data, GCP changelog

### Réglementation

- Respect du RGPD : aucune donnée personnelle  
- Stockage sur GCP (ISO/IEC certifié)  
- APIs sécurisées (clé privée + accès restreint)

### RSE / Éthique

- Objectif CO₂ : moins de trajets à vide  
- Projet 100% Open Data  
- Gouvernance responsable : accès contrôlés, déploiement sécurisé

---

## 10. 📦 Annexes

- Schéma pipeline ELT (png/pdf)  
- Captures : Airflow DAG, BigQuery tables, dbt model  
- Planning Trello exporté  
- Extraits : dbt config, Dockerfile, API test  
- Lien GitHub ou dépôt zip (structure projet)

---

## ✅ Conclusion

Le projet Yellow Taxi MeteoRide démontre la capacité à :

- piloter un projet Data de bout en bout,  
- mettre en œuvre un pipeline Cloud moderne,  
- livrer un modèle prédictif et une API exploitable,  
- intégrer éthique, documentation et méthodologie projet.

---

