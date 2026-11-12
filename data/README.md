# Sample Data — Synthetic Only

**Purpose:** illustrate the structure and internal consistency expected of the master and order data described in `docs/data-catalogue.md` and loaded by `docs/data-migration-plan.md`.

> **All records are synthetic.** PT Sinar Distribusi Nusantara and every customer, product, tax identifier, person, and transaction in this folder are fictional. Names are generic and do not refer to real companies. No record is derived from a real customer, supplier, employee, or system. No financial figure here is a real business result.

## Files

| File | Rows | Contents |
|---|---|---|
| `sample-customer-master.csv` | 12 customers | Terms, credit limits, status, sales owner |
| `sample-product-master.csv` | 20 products | Category, UoM conversion, list price, tax class, status |
| `sample-sales-orders.csv` | 30 order lines | 15 orders in ten different lifecycle states |

## Column dictionary

### Customer master

| Column | Meaning | Rule |
|---|---|---|
| `customer_id` | Synthetic customer key | Unique, immutable |
| `legal_name` | Fictional legal name | Not blank |
| `tax_id` | Synthetic tax identifier | Unique; format is illustrative, not a real numbering scheme |
| `city`, `province` | Location | Used for ship-to and tax grouping |
| `segment` | Retail, Wholesale, Horeca, Industrial | Drives reporting only |
| `payment_terms` | `COD`, `NET14`, `NET30`, `NET45` | Must exist in reference data |
| `credit_limit_idr` | Approved limit in rupiah; `0` means no limit defined | Not an assertion that a limit was approved |
| `status` | `Active`, `Blocked`, `Inactive` | Blocked blocks new orders; inactive blocks all new activity |
| `sales_owner` | Synthetic user label | Not a real person |

### Product master

| Column | Meaning | Rule |
|---|---|---|
| `product_id` | Synthetic product key | Unique, never reused |
| `description` | Fictional product description | Not blank |
| `category` | Reporting group | — |
| `base_uom`, `sales_uom` | Base and selling unit | Conversion must be exact |
| `conversion` | Base units per sales unit | Positive integer |
| `list_price_idr` | Price per **sales unit** in rupiah | Non-negative |
| `tax_class` | `STANDARD` or `EXEMPT` | Classification only; no tax rate is stated |
| `status` | `Active` or `Blocked` | One blocked product is intentional (`SKU-1019`) |

### Sales orders

| Column | Meaning | Rule |
|---|---|---|
| `order_id` | Synthetic order key | One header per ID |
| `order_type` | `STANDARD`, `RUSH`, `RETURN` | Return requires `reference_order` |
| `order_date` | Order creation date | ISO format |
| `customer_id` | Must exist in customer master | Referential integrity |
| `warehouse_id` | `WH-JKT01` or `WH-SBY01` | Illustrative; warehouse master is not included |
| `product_id` | Must exist in product master | Referential integrity |
| `quantity` | Positive quantity in the product's sales UoM | > 0 |
| `unit_price_idr` | Price per sales unit | `LIST` lines equal the list price |
| `price_source` | `LIST`, `CONTRACT`, `OVERRIDE-APPROVED`, `RETURN-CREDIT` | `CONTRACT` and override are below list |
| `order_status` | Lifecycle state | See distribution below |
| `requested_date` | Customer-requested date | Not before order date in this sample |
| `reference_order` | Original order for a return | Blank for all non-return rows |

## Deliberate edge cases

| Case | Record | Used by |
|---|---|---|
| Blocked customer with an order | `CUST-0005` / `ORD-2026-009` | UAT-17 credit block |
| Inactive customer with a cancelled order | `CUST-0010` / `ORD-2026-010` | UAT-33 deactivation |
| Blocked product | `SKU-1019` | Must be refused on a new order line |
| Approved price override | `ORD-2026-014`, `SKU-1001` at 46,000 vs list 48,000 | UAT-18 |
| Contract price | `ORD-2026-002`, `ORD-2026-004`, `ORD-2026-011` | FG-19 price conditions |
| Return referencing an invoice | `ORD-2026-015` → `ORD-2026-003` | UAT-16 |
| Zero credit limit (not "unlimited") | `CUST-0003`, `CUST-0007` | UAT-17 note: absence of a limit is a risk flag |

## Derived totals

| Measure | Value |
|---|---|
| Customers | 12 (10 active, 1 blocked, 1 inactive) |
| Products | 20 (19 active, 1 blocked) |
| Orders / lines | 15 / 30 |
| Open order lines | 16 (Draft, Submitted, Approved, Released, Delivered, CreditBlocked) |
| Open order value | IDR 26,546,000 |
| Order status distribution | Closed 3, Invoiced 2, Delivered 2, Released 2, Approved 1, Submitted 1, Draft 1, CreditBlocked 1, Cancelled 1, Credited 1 |

These totals are computed from the files, not asserted. If a file changes, the totals must be recomputed; a stale total in this table is a documentation defect.

## What this data is not

- **Not a volume test.** Fifteen orders prove structure, not performance.
- **Not a migration extract.** It is generated for illustration; `docs/data-migration-plan.md` explicitly has no source extract.
- **Not a complete test data set.** Deliveries, invoices, payments, and allocations are created during UAT execution.
- **Not tax advice.** `tax_class` is a label; no rate, statutory field, or e-invoice rule is defined here.

## Integrity rules applied

1. Every `customer_id` and `product_id` in the order file exists in its master file.
2. Every `LIST` line price equals the product's list price.
3. Every `CONTRACT` and `OVERRIDE-APPROVED` line is below list price.
4. Every `RETURN` line references an existing order.
5. Quantities are positive; prices are non-negative integers.
6. One order header value per order; all lines of an order share status, date, customer, and warehouse.
