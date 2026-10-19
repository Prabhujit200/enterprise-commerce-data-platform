# Enterprise Commerce Data Platform (ECDP)

An end-to-end data engineering portfolio project that simulates an enterprise e-commerce and supply chain platform using large-scale datasets, cloud data ingestion, distributed processing, data quality validation, and analytics-ready data models.

## Project Overview

The Enterprise Commerce Data Platform (ECDP) is designed around a fictional e-commerce business called ShopSphere. It processes customer, product, order, inventory, shipment, payment, return, and customer engagement data.

The goal is to build a practical data engineering solution that demonstrates how raw source data can be ingested, cleaned, validated, transformed, and prepared for business analytics.

**Project status:** Bronze, Silver, and Gold datasets have been created and validated in the current development environment. Snowflake integration and dbt implementation are planned next.

## Architecture

```text
Source Files (CSV and JSON)
            |
            v
    Azure Data Factory
            |
            v
    Azure Data Lake Storage
            |
            v
       BRONZE LAYER
     Raw ingested data
            |
            v
   PySpark / Delta Lake
            |
            v
       SILVER LAYER
   Cleaned and validated data
            |
            v
        GOLD LAYER
 Business-ready analytical datasets
            |
            v
         Snowflake
            |
            v
            dbt
            |
            v
     Analytics / Power BI
```

The Snowflake, dbt, and Power BI portions represent planned project stages; they are not yet completed end-to-end integrations.

## Technology Stack

| Technology | Purpose |
|---|---|
| Azure Data Factory | Orchestrating metadata-driven file ingestion |
| Azure Data Lake Storage Gen2 | Cloud data lake storage |
| PySpark | Distributed data cleaning and transformation |
| Delta Lake | Storing structured data in Delta format |
| SQL | Data validation and analytical querying |
| Snowflake | Planned cloud data warehouse |
| dbt | Planned reusable SQL models, tests, and documentation |
| Power BI | Planned business intelligence and visualization |
| GitHub | Version control and project documentation |

## Dataset Scale

The project uses nine synthetic source datasets.

| Dataset | Approximate records |
|---|---:|
| Customers | 100,001 source rows |
| Products | 20,000 |
| Orders | 1,000,000 |
| Order Items | 2,500,000 |
| Inventory | 40,000 |
| Shipments | 850,000 |
| Payments | 950,000 |
| Returns | 75,000 |
| Customer Events | 2,000,000 |
| **Total source rows** | **7,535,001** |

The customer source contains one exact duplicate row. After removing it, the nine Silver datasets contain **7,535,000 records**.

## Medallion Architecture

### Bronze — Raw Data

The Bronze layer stores ingested source data while preserving the raw-data foundation for downstream processing.

The Azure Data Factory ingestion workflow uses metadata to process the configured CSV datasets from the landing area into Bronze storage.

### Silver — Cleaned Data

The Silver layer contains cleaned and standardized datasets stored in Delta format.

Key work includes:

- Removing an exact duplicate customer record.
- Standardizing selected status and currency values.
- Parsing and validating timestamps.
- Checking key uniqueness and required fields.
- Validating relationships between orders, customers, products, shipments, payments, and returns.
- Preserving source identifiers and using a separate normalized customer key for matching.

The current Silver datasets contain 7,535,000 records across nine datasets.

### Gold — Business-Ready Data

Six Gold analytical datasets have been created.

| Gold dataset | Grain | Purpose |
|---|---|---|
| `gold_daily_sales` | Date, currency, status, customer segment, country | Daily order and gross order-value analysis |
| `gold_inventory_health` | Latest inventory record per product and warehouse | Inventory availability and reorder analysis |
| `gold_shipment_performance` | Shipment | Shipment status, delivery outcome, and transit analysis |
| `gold_returns_analytics` | Return | Return status, quantities, and refund-amount analysis |
| `gold_customer_engagement` | Event date, event type, channel, device | Customer engagement trends |
| `gold_product_performance` | Product and currency | Units, line values, discounts, and taxes |

The six Gold datasets contain 1,254,533 rows in total. This is the sum of rows across different analytical tables, not a count of unique business entities.

## Data Quality and Validation

Validation performed during development includes:

- Duplicate and key checks.
- Required-field and timestamp checks.
- Referential-integrity checks.
- Reconciliation of source and Gold aggregates.
- Verification of Gold table grain and duplicate groups.
- Checks for invalid delivery-date sequences and negative transit durations.
- Reconciliation of sales, product, engagement, shipment, inventory, and return metrics.

A Delta dataset named `gold_audit_summary` records the readability and row and column counts of the six Gold tables. It is a basic audit summary, not yet a complete automated data-quality framework.

## Important Data Assumptions and Limitations

### Customer ID normalization

Customer IDs in the customer source are numeric-looking strings, while order records use a `C` prefix and leading zeros. A normalized matching key removes the prefix and leading zeros while preserving the original IDs. Testing confirmed that the order customer IDs matched the customer keys without duplicate normalized keys.

### Inventory history

Silver retains all 40,000 source inventory records. Gold selects the latest record for each product and warehouse combination, resulting in 32,957 records. This represents a latest-record view; the interpretation of repeated source records as historical snapshots is an assumption that should be confirmed against source-system semantics.

### Synthetic data

The source datasets are synthetic and intended for learning and portfolio demonstration. Passing referential checks does not prove that every cross-entity relationship represents a real business transaction.

### Environment and integration

The PySpark transformations and Delta datasets were developed in Databricks Free Edition. The current Free Edition storage volume is not automatically connected to the Azure Data Lake. The complete Azure-to-Databricks-to-Snowflake flow has not yet been automated.

## Planned Next Steps

- [ ] Finalize project documentation and add architecture and validation screenshots.
- [ ] Prepare a cost-conscious Snowflake integration.
- [ ] Load and validate the selected datasets in Snowflake.
- [ ] Build reusable dbt staging and analytical models.
- [ ] Add dbt data-quality tests and generated documentation.
- [ ] Improve automated data-quality checks and audit reporting.
- [ ] Develop Power BI dashboards from validated Gold or warehouse models.
- [ ] Document incremental processing, orchestration, and operational monitoring.

## Learning Objectives

This project demonstrates practical experience with:

- Cloud data ingestion and orchestration.
- Lakehouse architecture and Bronze/Silver/Gold modeling.
- Distributed data processing using PySpark.
- Delta Lake storage and transformations.
- Data quality, referential integrity, and reconciliation.
- Warehouse integration and analytics engineering.
- Version control and technical documentation.

---

**Note:** This is a learning and portfolio project using synthetic data. Technology integrations are documented according to their current implementation status.
