
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
CREATE OR REPLACE TABLE house_price_json (
    data VARIANT
);
COPY INTO house_price_json
FROM @house_price_stage;

```

Data Engineering (Transformation des données)
Les données étant fournies au format JSON, elles sont transformées en une table
relationnelle exploitable grâce à la fonction FLATTEN.

CREATE OR REPLACE TABLE house_price AS
SELECT
    value:price::NUMBER AS price,
    value:area::NUMBER AS area,
    value:bedrooms::NUMBER AS bedrooms,
    value:bathrooms::NUMBER AS bathrooms,
    value:stories::NUMBER AS stories,
    value:mainroad::STRING AS mainroad,
    value:guestroom::STRING AS guestroom,
    value:basement::STRING AS basement,
    value:hotwaterheating::STRING AS hotwaterheating,
    value:airconditioning::STRING AS airconditioning,
    value:parking::NUMBER AS parking,
    value:prefarea::STRING AS prefarea,
    value:furnishingstatus::STRING AS furnishingstatus
FROM house_price_json,
LATERAL FLATTEN(input => data);
