# PySpark and Databricks Notebooks

## Overview

PySpark was used to clean, validate, transform, and aggregate ShopSphere's synthetic e-commerce datasets.

The transformations were developed in Databricks Free Edition and the resulting datasets were stored in Delta format.

## Silver Layer Processing

The Silver layer contains nine datasets:

| Dataset | Records |
|---|---:|
| Customers | 100,000 |
| Products | 20,000 |
| Orders | 1,000,000 |
| Order Items | 2,500,000 |
| Inventory | 40,000 |
| Shipments | 850,000 |
| Payments | 950,000 |
| Returns | 75,000 |
| Customer Events | 2,000,000 |
| **Total** | **7,535,000** |

Processing included duplicate removal where identified, value standardization, timestamp validation, key checks, referential checks, and Delta storage.

## Gold Layer Processing

Six analytical datasets were produced:

- `gold_daily_sales`
- `gold_inventory_health`
- `gold_shipment_performance`
- `gold_returns_analytics`
- `gold_customer_engagement`
- `gold_product_performance`

These datasets support sales, inventory, shipment, returns, customer engagement, and product-performance analysis.

## Validation

The development work included:

- Source-to-target row-count reconciliation.
- Duplicate and business-grain checks.
- Referential-integrity checks.
- Timestamp and date-sequence validation.
- Aggregate reconciliation by currency and status.
- Validation of Gold table readability and row counts.

The current `gold_audit_summary` is a basic audit report. A fully automated, reusable data-quality framework remains planned work.

## Environment Notes

- Transformations were developed in Databricks Free Edition.
- Delta datasets are stored in the managed volume `/Volumes/workspace/default/shopsphere_landing/`.
- The Free Edition volume is not automatically connected to Azure Data Lake Storage Gen2.
- Snowflake and dbt integration has not yet been completed.

## Next Improvements

- Add exported or source-controlled notebook files.
- Organize reusable PySpark transformations into modules.
- Parameterize processing for repeatable runs.
- Automate data-quality checks and reconciliation.
- Add incremental processing and operational logging.
- Integrate validated datasets with Snowflake.

**Status:** Silver and Gold transformations and validation were completed in the development environment. Source-controlled notebooks and end-to-end orchestration are future improvements.
