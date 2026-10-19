# dbt — Analytics Engineering

## Overview

dbt (data build tool) will be used to implement reusable SQL transformations, data-quality tests, and generated documentation for the Enterprise Commerce Data Platform (ECDP).

The planned dbt models will run against data loaded into Snowflake.

## Planned Workflow

```text id="7w8p6k"
Validated Data
      |
      v
   Snowflake
      |
      v
  dbt Staging Models
      |
      v
  dbt Intermediate Models
      |
      v
  dbt Marts
      |
      v
  Data Quality Tests
      |
      v
  Generated Documentation
```

## Planned Model Layers

### Staging

Staging models will standardize source columns, types, naming conventions, and basic transformations.

Potential models include:

- `stg_customers`
- `stg_products`
- `stg_orders`
- `stg_order_items`
- `stg_inventory`
- `stg_shipments`
- `stg_payments`
- `stg_returns`
- `stg_customer_events`

### Intermediate

Intermediate models will organize reusable business logic, joins, and calculations.

Examples include customer-order relationships, shipment calculations, and payment or return enrichment.

### Marts

Mart models will provide analytics-ready outputs for sales, inventory, shipment performance, returns, customer engagement, and product performance.

The models will be designed around documented business grains and validated against the existing Gold datasets where appropriate.

## Planned Data Quality Tests

- `not_null` tests for required columns.
- `unique` tests for business keys.
- `relationships` tests for valid references.
- `accepted_values` tests for controlled status fields.
- Custom SQL tests for business rules and reconciliation.

## Documentation

dbt documentation will describe models, columns, relationships, assumptions, and test results.

## Current Status

Snowflake integration and dbt project implementation have not yet been completed. This README documents the planned structure; it does not claim that dbt models or tests already exist.

## Planned Next Steps

1. Prepare a cost-conscious Snowflake connection.
2. Load and validate selected datasets.
3. Initialize a dbt project and configure the Snowflake connection.
4. Build staging models.
5. Add intermediate and mart models.
6. Implement tests and reconciliation checks.
7. Generate and review dbt documentation.

**Design principle:** Keep transformations modular, testable, documented, and consistent with the business definitions used by the Gold datasets.
