# Évaluation Data Engineering & MLOps – Prédiction du prix des maisons

Projet réalisé dans le cadre du **MBA ESG – Architecture Big Data**.

Ce projet a pour objectif de construire un pipeline complet de **Data Engineering** et de **Machine Learning** directement dans Snowflake afin de prédire le prix de vente d’une maison à partir de ses caractéristiques.

---

## Membres du groupe

Projet réalisé dans le cadre du MBA ESG – DATA_ENGINEER_MLOPS

- **MINGUE KEPSI Bobic**
- **ADONI Allika Mireille**
- **TAGNE KAMGHEM Leonel**

---

## Objectif du projet

L’objectif du projet est de :
- Charger et transformer des données immobilières stockées sur Amazon S3
- Préparer les données pour le Machine Learning
- Entraîner et comparer plusieurs modèles de Machine Learning
- Sélectionner le meilleur modèle
- Appliquer le modèle sur le dataset afin de générer des prédictions
- Mettre en place une application Streamlit pour interagir avec les résultats

L’ensemble du pipeline est réalisé **directement dans Snowflake**, sans export des données vers un environnement externe.

---

## Technologies utilisées

- Snowflake
- Snowpark (Python)
- SQL
- scikit-learn
- Streamlit

---

## Pipeline Machine Learning

Le pipeline complet est documenté dans le notebook Snowflake fourni dans ce dépôt.  
Il comprend les étapes suivantes :

1. Ingestion des données depuis un bucket Amazon S3
2. Transformation des données JSON en table relationnelle
3. Exploration et compréhension des données
4. Préparation des données pour le Machine Learning (feature engineering)
5. Entraînement des modèles de Machine Learning
6. Évaluation des performances des modèles
7. Sélection du meilleur modèle
8. Application du modèle sur l’ensemble du dataset (inférence)
9. Visualisation des résultats via une application Streamlit

---

## Modèles entraînés

Deux modèles de Machine Learning ont été entraînés et comparés :

- **Régression Linéaire**
- **Random Forest Regressor**

Les modèles ont été entraînés sur le dataset préparé dans Snowflake afin de prédire le prix des maisons.

---

## Analyse des performances du modèle

L’évaluation des performances a été réalisée à l’aide de la métrique **MAE (Mean Absolute Error)**.

Résultats obtenus :

- Régression Linéaire : **MAE ≈ 40 253**
- Random Forest Regressor : **MAE ≈ 19 500**

Le modèle **Random Forest Regressor** présente une erreur moyenne plus faible et de meilleures performances globales.  
Il a donc été sélectionné comme **modèle final**.

---

## Application du modèle

Le modèle Random Forest a été appliqué sur l’ensemble du dataset afin de générer des prédictions.  
Les résultats ont été stockés dans une table Snowflake dédiée.

Une application **Streamlit** a été développée dans Snowflake afin de permettre aux utilisateurs métier d’interagir avec le pipeline MLOps et de visualiser les prédictions.

En raison des contraintes de sécurité de Snowflake Streamlit (absence d’accès Internet et impossibilité d’installer certaines dépendances externes), l’inférence est réalisée directement dans Snowflake.  
L’application Streamlit sert d’interface de démonstration et de visualisation des résultats.

---

## Conclusion

Ce projet démontre la mise en place d’un pipeline complet de **Data Engineering** et de **Machine Learning** dans Snowflake.  
Le modèle Random Forest a été sélectionné pour ses meilleures performances et utilisé pour générer des prédictions exploitables par les utilisateurs métier.

Ce travail illustre une approche MLOps réaliste et conforme aux pratiques industrielles.
