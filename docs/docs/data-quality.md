# ECDP — Data Quality and Validation

## 1. Purpose

This document describes the data-quality checks performed on the Bronze, Silver, and Gold layers of the Enterprise Commerce Data Platform (ECDP).

The goal is to improve data reliability, preserve traceability, and verify that analytical datasets reconcile with their source data.

## 2. Dataset Overview

The project uses nine synthetic source datasets.

| Dataset | Source rows | Silver rows |
|---|---:|---:|
| Customers | 100,001 | 100,000 |
| Products | 20,000 | 20,000 |
| Orders | 1,000,000 | 1,000,000 |
| Order Items | 2,500,000 | 2,500,000 |
| Inventory | 40,000 | 40,000 |
| Shipments | 850,000 | 850,000 |
| Payments | 950,000 | 950,000 |
| Returns | 75,000 | 75,000 |
| Customer Events | 2,000,000 | 2,000,000 |
| **Total** | **7,535,001** | **7,535,000** |

One exact duplicate customer row was removed. The other listed row counts were preserved during Silver processing.

## 3. Silver Layer Validation

### Customers

- Removed one exact duplicate row.
- Retained 100,000 customer records.
- Standardized selected customer attributes.
- Normalized customer IDs into a separate matching key for joining to orders.
- Preserved the original source identifiers.

### Products

- Retained 20,000 records.
- Checked the product dataset for invalid values during development.

### Orders

- Retained 1,000,000 records.
- Standardized selected order-status, payment-status, and currency values.
- Validated the order date range and checked for zero-value orders.
- Found no zero-value orders in the reported validation.

The source date range extends through December 2, 2026. Since this is later than the current project validation date, these future-dated records should be investigated rather than silently removed.

### Order Items

- Retained 2,500,000 records.
- Found zero duplicate order-item IDs.
- Found zero missing order references and zero missing product references in the reported checks.

### Inventory

- Retained 40,000 source records.
- Found 32,957 distinct product-and-warehouse combinations.
- Identified repeated records for some combinations.
- Preserved the Silver history rather than removing records simply because combinations repeat.

### Shipments

- Retained 850,000 records.
- Verified that shipment order references matched existing orders.
- Found zero impossible date sequences in the reported checks.
- Identified 106,197 late deliveries using actual delivery date greater than estimated delivery date.

### Payments

- Retained 950,000 records.
- Checked payment-ID uniqueness, required fields, positive amounts, timestamps, currencies, and order references.
- The reported checks found unique payment IDs, no missing required fields, positive amounts, parseable timestamps, and matching order references.

### Returns

- Retained 75,000 records.
- Checked required fields, positive return quantities, nonnegative refund amounts, timestamps, and customer, product, and order references.
- The reported reference checks found no missing customer, product, or order references.

### Customer Events

- Retained 2,000,000 records.
- Checked event-ID uniqueness, customer and product references, and timestamp parsing.
- The reported checks found unique event IDs, no missing customer or product references, and parseable timestamps.

## 4. Customer ID Matching Rule

Customer source IDs are numeric-looking strings, such as `123`. Order records may contain IDs such as `C000123`.

A separate normalized key removes the `C` prefix and leading zeros to make these values comparable.

The validation confirmed that all one million orders matched a customer using this rule, with 99,998 distinct order customer IDs matching the customer keys and no duplicate normalized customer keys.

Original IDs are preserved. The normalized key is used for matching, not as a replacement for source identifiers.

## 5. Gold Layer Validation

Six Gold datasets were created and checked for their intended business grain.

| Gold dataset | Row count | Validation focus |
|---|---:|---|
| `gold_daily_sales` | 155,328 | Order counts and gross order values reconcile by currency |
| `gold_inventory_health` | 32,957 | Latest-record selection and inventory calculations |
| `gold_shipment_performance` | 850,000 | Shipment IDs and delivery outcomes reconcile |
| `gold_returns_analytics` | 75,000 | Return counts and amounts reconcile by currency and status |
| `gold_customer_engagement` | 61,248 | All 2,000,000 source events are represented |
| `gold_product_performance` | 60,000 | Units, line values, discounts, and taxes reconcile by currency |
| **Total across Gold tables** | **1,254,533** | Sum of rows across six different analytical datasets |

The Gold tables were checked for null grain fields and duplicate grain groups. The reported audit found zero null grain rows and zero duplicate grain groups in all six tables.

### Sales reconciliation

Gold daily sales aggregates reconciled to the source order counts and gross order values by currency:

| Currency | Orders | Gross order value |
|---|---:|---:|
| CAD | 200,174 | 250,789,276.47 CAD |
| EUR | 199,953 | 250,799,768.36 EUR |
| USD | 599,873 | 752,485,691.07 USD |

Currencies are kept separate because their values are not directly comparable without currency conversion. Gross order value is not necessarily recognized revenue, particularly when cancelled or otherwise non-revenue orders are included.

### Inventory snapshot

The Gold inventory dataset selects the latest record per product-and-warehouse combination, producing 32,957 records from 40,000 Silver records.

The reported latest-snapshot totals were:

- Quantity on hand: 24,695,138
- Reserved quantity: 4,423,866
- Available quantity: 20,271,272
- Records flagged for reorder: 5,880

This is a latest-record view, not a sum of all inventory history. The assumption that repeated product-and-warehouse records represent historical snapshots should be documented and verified against source-system semantics.

### Shipment reconciliation

- All 850,000 shipment IDs were represented in Gold.
- No negative transit durations were found.
- The reported late-delivery count was 106,197.
- Delivery outcomes are kept distinct from workflow statuses such as `IN_TRANSIT` and `LABEL_CREATED`.

### Returns reconciliation

- All 75,000 return records were retained.
- Return counts and amounts reconciled across the 15 currency-and-status groups.
- 3,777 returns had no matching payment record; these returns were preserved.
- Return amounts should not automatically be interpreted as confirmed cash refunds.

### Customer engagement reconciliation

- All 2,000,000 customer events were represented in Gold.
- Event counts and dates reconciled with the Silver source.

### Product performance reconciliation

All 2,500,000 order-item records were retained during enrichment with product and order currency information. Units, line values, discounts, and taxes reconciled to Silver totals by currency.

## 6. Audit Summary

The Delta dataset `gold_audit_summary` contains six rows, one for each Gold table. It records:

- Gold table name
- Row count
- Column count
- Audit timestamp
- Record status

The
