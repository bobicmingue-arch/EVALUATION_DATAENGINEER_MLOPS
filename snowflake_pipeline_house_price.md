
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

## Data Engineering (Transformation des données)
Les données étant fournies au format JSON, elles sont transformées en une table
relationnelle exploitable grâce à la fonction FLATTEN.

```sql

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
```

## Exploration des données
Quelques requêtes simples sont utilisées afin de mieux comprendre les données.
```sql
SELECT COUNT(*) FROM house_price;
SELECT * FROM house_price LIMIT 10;
```

## Préparation des données pour le Machine Learning

Les modèles de Machine Learning nécessitent des données numériques.
Les variables catégorielles sont donc transformées en valeurs numériques.

```sql
CREATE OR REPLACE VIEW house_price_ml AS
SELECT
    price,
    area,
    bedrooms,
    bathrooms,
    stories,
    CASE WHEN mainroad = 'yes' THEN 1 ELSE 0 END AS mainroad,
    CASE WHEN guestroom = 'yes' THEN 1 ELSE 0 END AS guestroom,
    CASE WHEN basement = 'yes' THEN 1 ELSE 0 END AS basement,
    CASE WHEN hotwaterheating = 'yes' THEN 1 ELSE 0 END AS hotwaterheating,
    CASE WHEN airconditioning = 'yes' THEN 1 ELSE 0 END AS airconditioning,
    parking,
    CASE WHEN prefarea = 'yes' THEN 1 ELSE 0 END AS prefarea,
    CASE
        WHEN furnishingstatus = 'furnished' THEN 2
        WHEN furnishingstatus = 'semi-furnished' THEN 1
        ELSE 0
    END AS furnishingstatus
FROM house_price;
```
## Entraînement des modèles de Machine Learning

Les données préparées sont chargées en Python à l'aide de Snowpark,
puis utilisées pour entraîner plusieurs modèles de Machine Learning.

```sql
df = session.table("HOUSE_PRICE_DB.RAW.HOUSE_PRICE_ML")
pdf = df.to_pandas()

X = pdf.drop(columns=["PRICE"])
y = pdf["PRICE"]
```

## Évaluation des performances des modèles

Les modèles sont évalués à l'aide de la métrique MAE (Mean Absolute Error)

```sql
mae_lr = mean_absolute_error(y_test, y_pred_lr)
mae_rf = mean_absolute_error(y_test, y_pred_rf)
```

## Application du modèle sur le dataset

Le modèle Random Forest, présentant les meilleures performances,
est appliqué sur l'ensemble du jeu de données afin de générer des prédictions.

```sql
predictions = rf_model.predict(X)
SELECT * FROM HOUSE_PRICE_PREDICTIONS;
```
