# Azure Data Factory Pipelines

## Overview

Azure Data Factory (ADF) is used to orchestrate file ingestion for the Enterprise Commerce Data Platform (ECDP).

The current ingestion workflow processes configured CSV datasets from the landing area into the Bronze area in Azure Data Lake Storage Gen2.

## Current Implementation

### Metadata-driven ingestion

The pipeline `PL_METADATA_DRIVEN_INGESTION` uses metadata to control which configured CSV files are processed.

The workflow uses:

1. **Lookup** — reads ingestion configuration metadata.
2. **ForEach** — iterates through the configured dataset entries.
3. **Copy activity** — copies the configured source files into the Bronze layer.

A dedicated customer ingestion pipeline, `PL_INGEST_CUSTOMERS_BRONZE`, was also tested successfully during development.

## Storage Organization

The ADLS Gen2 container uses the following directory structure:

```text
shopsphere/
├── landing/
│   └── metadata/
│       └── ingestion_metadata.csv
├── bronze/
├── silver/
└── gold/
```

The configured ingestion metadata identifies the source files and the information needed to process them.

## Validation Performed

- Validated the metadata-driven pipeline.
- Ran the configured CSV ingestion workflow successfully.
- Confirmed that ingested files reached the Bronze layer.
- Investigated and resolved a storage access issue encountered during setup.

## Current Limitations

- The documented metadata-driven workflow covers configured CSV ingestion.
- JSON ingestion has not yet been integrated into this ADF workflow.
- PySpark Silver and Gold transformations were developed separately in Databricks Free Edition.
- The Free Edition storage volume is not automatically connected to the Azure Data Lake.
- End-to-end orchestration from source ingestion through Snowflake and dbt is not yet implemented.

## Planned Improvements

- Export and version-control the ADF pipeline definitions.
- Add explicit failure handling and ingestion logging.
- Improve schema and source-file validation.
- Extend metadata-driven ingestion to supported JSON formats.
- Add incremental ingestion and operational monitoring.
- Integrate the validated datasets with Snowflake using a cost-conscious approach.

**Status:** CSV ingestion into Bronze has been tested successfully. Further automation and downstream integration remain planned work.
