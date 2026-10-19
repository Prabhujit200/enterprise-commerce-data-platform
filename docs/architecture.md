# ECDP — Architecture Documentation

## 1. Overview

The Enterprise Commerce Data Platform (ECDP) is a portfolio data engineering project that simulates data processing for a fictional e-commerce business, ShopSphere.

The platform uses a medallion architecture to organize data into Bronze, Silver, and Gold layers.

## 2. Architecture Diagram

```text
+-------------------------------+
|       Source Data Files       |
|       CSV and JSON            |
+---------------+---------------+
                |
                v
+-------------------------------+
|      Azure Data Factory       |
|  Metadata-driven CSV Ingestion|
+---------------+---------------+
                |
                v
+-------------------------------+
|   Azure Data Lake Storage     |
|           Gen2                |
+---------------+---------------+
                |
                v
+-------------------------------+
|         BRONZE LAYER          |
|      Raw ingested data        |
+---------------+---------------+
                |
                v
+-------------------------------+
|       PySpark Processing      |
|     Cleaning and Validation   |
+---------------+---------------+
                |
                v
+-------------------------------+
|         SILVER LAYER          |
|    Cleaned Delta datasets     |
+---------------+---------------+
                |
                v
+-------------------------------+
|          GOLD LAYER           |
| Business-ready analytical data|
+---------------+---------------+
                |
                v
+-------------------------------+
|       Snowflake (Planned)     |
+---------------+---------------+
                |
                v
+-------------------------------+
|         dbt (Planned)         |
| Models, Tests, Documentation  |
+---------------+---------------+
                |
                v
+-------------------------------+
|    Power BI (Planned)         |
+-------------------------------+
```

**Implementation note:** The Azure Data Factory pipeline ingests configured CSV files into Azure Data Lake Storage. PySpark transformations and Delta datasets have been developed separately in Databricks Free Edition. The current environment does not automatically connect the Free Edition volume to Azure Data Lake Storage. Snowflake, dbt, and Power BI integration remains future work.

## 3. Medallion Layers

### Bronze Layer

Purpose: Preserve ingested source data for downstream processing.

Responsibilities:
- Ingest configured source CSV files through Azure Data Factory.
- Store files in the Bronze area of Azure Data Lake Storage.
- Use metadata-driven orchestration for configured ingestion workloads.

### Silver Layer

Purpose: Produce cleaned and validated datasets.

Responsibilities:
- Remove identified exact duplicate records.
- Standardize selected values.
- Parse timestamps and validate required fields.
- Check unique keys and referential relationships.
- Store transformed datasets in Delta format.

Current result: Nine Silver datasets containing 7,535,000 records.

### Gold Layer

Purpose: Provide business-oriented datasets for analytical queries.

The six Gold datasets are:

1. `gold_daily_sales` — daily order and gross order-value analysis.
2. `gold_inventory_health` — latest inventory record per product and warehouse.
3. `gold_shipment_performance` — shipment status, delivery outcome, and transit analysis.
4. `gold_returns_analytics` — return quantities, statuses, and refund-amount analysis.
5. `gold_customer_engagement` — customer events by date, event type, channel, and device.
6. `gold_product_performance` — product units, line values, discounts, and taxes by currency.

The six Gold datasets contain 1,254,533 rows in total.

## 4. Data Quality and Reconciliation

Validation performed during development includes:
- Duplicate-key and business-grain checks.
- Required-field and timestamp validation.
- Referential-integrity checks.
- Reconciliation of source and Gold aggregates.
- Shipment date-sequence and transit-duration checks.
- Verification of Gold row counts and table readability.

The current `gold_audit_summary` records the readability and row and column counts of the six Gold tables. It is not yet a complete automated data-quality framework.

## 5. Documented Assumptions

### Customer identifiers

Order customer IDs contain a `C` prefix and leading zeros, while customer source IDs are numeric-looking strings. A separate normalized key removes the prefix and leading zeros for matching. Original IDs are preserved.

### Inventory records

Silver retains all 40,000 source inventory records. Gold retains the latest record for each product-and-warehouse combination, resulting in 32,957 records. Treating repeated records as historical snapshots is a working assumption that requires confirmation against source-system semantics.

### Synthetic datasets

The project uses generated data for learning and portfolio demonstration. Referential checks help validate consistency but do not establish that every record represents a real business transaction.

## 6. Planned Architecture Improvements

- Connect or transfer validated datasets into Snowflake using a cost-conscious approach.
- Build reusable dbt models and automated tests.
- Generate dbt documentation.
- Improve automated quality monitoring and audit reporting.
- Develop Power BI dashboards.
- Expand incremental ingestion and operational monitoring.

## 7. Design Principle

Preserve source data, make transformations traceable, validate outputs against their inputs, document assumptions, and avoid claiming integrations or automation that have not yet been implemented.
