# Project Screenshots and Evidence

## Purpose

This directory will contain selected screenshots demonstrating the implementation and validation of the Enterprise Commerce Data Platform (ECDP).

Screenshots will provide visual evidence of completed work and help reviewers understand the architecture and processing results.

## Planned Evidence

| Screenshot | Evidence to capture |
|---|---|
| `01-adf-ingestion.png` | Successful Azure Data Factory ingestion pipeline run |
| `02-adls-bronze.png` | Source files organized in the Bronze storage area |
| `03-silver-validation.png` | PySpark Silver transformation and validation results |
| `04-gold-sales.png` | Gold daily sales dataset and reconciliation |
| `05-gold-data-quality.png` | Gold grain and data-quality audit results |
| `06-snowflake-validation.png` | Dataset loaded and validated in Snowflake |
| `07-dbt-tests.png` | dbt model and test results |

## Screenshot Guidelines

- Capture actual results from the relevant platform.
- Keep important table names, status messages, and row counts visible.
- Avoid exposing account IDs, access tokens, credentials, or other sensitive information.
- Use descriptive filenames.
- Include only evidence that accurately reflects the current implementation status.

## Current Status

The screenshots listed above are planned evidence files. They should be added only after the corresponding results have been captured and verified.

Snowflake and dbt screenshots will be added after those phases are implemented.
