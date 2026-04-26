
# Pipeline Machine Learning pour la prédiction du prix des maisons (Snowflake)

## Objectif du projet
L’objectif de ce projet est de construire un pipeline complet de Data Engineering et
de Machine Learning dans Snowflake afin de prédire le prix de vente d’une maison
à partir de ses caractéristiques (surface, nombre de chambres, salles de bain, etc.).

Technologies utilisées :
- Snowflake
- Snowpark (Python)
- SQL
- scikit-learn

---

## Ingestion des données
Les données sont stockées dans un bucket Amazon S3 et chargées dans Snowflake
à l’aide d’un stage externe.

```sql
CREATE OR REPLACE STAGE house_price_stage
URL='s3://logbrain-datalake/datasets/house_price/';
