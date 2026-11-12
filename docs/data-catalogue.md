# Data Catalogue

**Document ID:** DCT-01
**Version:** 0.1 (Draft)
**Phase:** 3 — Fit-Gap Analysis and Solution Blueprint
**Inputs:** `PRD.md` §9; `Design.md` §6; `docs/functional-blueprint.md`; `docs/to-be-process.md`; `docs/role-authorization-matrix.md`
**Diagram:** `diagrams/o2c-data-model.mmd`
**Status:** Draft — entity and ownership design; no extract profiled (`DEP-04` open)

---

## 1. Purpose and limits

The catalogue defines the data objects the O2C design depends on: what each represents, who owns it, what makes a record valid, and what would have to be migrated. It is the reference for Phase 4 data migration and for the role design in `docs/role-authorization-matrix.md`.

**Limits, stated up front:**

- No legacy extract exists. Field lists are *design requirements*, not a description of a current database (`AS-04`, `DEP-04`).
- No volumes, cardinalities, or growth rates are given. `DQ-05` and `DQ-57` are open; a number here would be invented.
- No physical schema, data type, or index is specified. Those belong to product configuration.

---

## 2. Data principles

| Principle | Consequence |
|---|---|
| One owner per entity | Every entity names a single accountable role, not a department (`NFR-04`) |
| Transaction references master data | Orders store a customer ID and product ID; they do not copy and detach the master values |
| Documents reference their parent | No free-standing invoice, credit note, or payment allocation (`FR-13`) |
| Status is stored, history is derived | Current status on the record; every change in audit history (`FR-15`, `FR-20`) |
| Deactivate, never delete | Master data and posted documents are never physically removed (`BR-13`, `BR-15`) |
| Quality is defined, not assumed | Each entity has explicit validity rules and an owner who is accountable for them |
| Synthetic by construction | All sample data in Phase 4 is generated, never derived from a real company (`DEC-04`) |

---

## 3. Entity catalogue

`DE-` identifies a data entity. `Type` distinguishes master data (long-lived, owned) from transaction data (created by a process) and reference data (configuration).

### 3.1 Master data

| ID | Entity | Represents | Natural key | Owner (role) | Lifecycle |
|---|---|---|---|---|---|
| DE-01 | Customer | Legal or commercial buyer, and its ship-to locations | Customer number; tax ID for duplicate check | ROL-03 Master Data | Active → blocked → inactive |
| DE-02 | Customer site | Ship-to address, contact, delivery constraints | Customer number + site number | ROL-03 | Active → inactive |
| DE-03 | Product | Sellable item with sales UoM and tax classification | Product number; no reuse after deactivation | ROL-03 | Active → blocked → inactive |
| DE-04 | Product UoM conversion | Base-to-sales unit conversion per product | Product + UoM | ROL-03 | Versioned, change logged |
| DE-05 | Warehouse / location | Physical stocking point used for availability and shipment | Warehouse code | ROL-04 Inventory Controller | Active → inactive |
| DE-06 | Price condition | Agreed price per customer, product, or list, with validity dates | Customer + product + validity period | ROL-02 Sales Manager | Active → expired |
| DE-07 | Credit limit | Approved exposure ceiling per customer | Customer + validity period | ROL-08 Finance Manager | Active → suspended → withdrawn |

### 3.2 Transaction data

| ID | Entity | Represents | Parent reference | Owner (role) | Terminal state |
|---|---|---|---|---|---|
| DE-08 | Sales order header | Commercial commitment for a customer and date | Customer | ROL-01 Sales Rep | `Closed` / `Cancelled` |
| DE-09 | Sales order line | Product, quantity, price, requested date | Sales order | ROL-01 | `Closed` / `Cancelled` |
| DE-10 | Approval decision | Who approved or rejected, when, why | Sales order or credit case | ROL-02 / ROL-08 | Recorded, immutable |
| DE-11 | Inventory balance | On-hand, reserved, available per product and warehouse | Product + warehouse | ROL-04 | Continuous |
| DE-12 | Delivery header | Shipment of a released order | Sales order | ROL-06 Logistics | `POD Confirmed` / `Failed` |
| DE-13 | Delivery line | Shipped quantity per order line | Delivery + order line | ROL-05 Warehouse | Closed with delivery |
| DE-14 | Proof of delivery | Receiver, date, evidence reference | Delivery | ROL-06 | Recorded, immutable |
| DE-15 | Invoice header | Amount due, tax, due date, statutory number | Delivery | ROL-07 Finance/AR | `Cleared` / `Written off` |
| DE-16 | Invoice line | Quantity, price, tax per invoice | Invoice + delivery line | ROL-07 | Closed with invoice |
| DE-17 | Payment | Cash received from a customer | Customer + bank reference | ROL-07 | `Fully allocated` / `Unapplied` |
| DE-18 | Payment allocation | Amount of a payment applied to an invoice | Payment + invoice | ROL-07 | Recorded; reversible only with reason |
| DE-19 | Return request | Customer claim against a delivery | Delivery + invoice | ROL-01 / ROL-06 | `Credited` / `Rejected` |
| DE-20 | Credit note | Reduction of receivable for an authorised return or adjustment | Return + invoice | ROL-07 (post), ROL-08 (authorise) | Posted, immutable |
| DE-21 | Exception record | Reason, owner, timestamp, resolution for a process exception | Any document (`EX-01`…`EX-15`) | Stage owner | `Resolved` / `Open` |

### 3.3 Reference and control data

| ID | Entity | Represents | Owner | Change control |
|---|---|---|---|---|
| DE-22 | Approval rule | Value bands, approver roles, escalation | ROL-08 + ROL-02 (joint policy) | Change logged; effective-dated |
| DE-23 | Document number range | Number series per document type | ROL-09 ERP Administrator | Change logged; statutory series protected |
| DE-24 | Status profile | Allowed statuses and transitions per object | ROL-09 (configuration), business approves | Change logged and tested |
| DE-25 | Audit entry | User, timestamp, object, action, old/new value, reason | System-generated | Append-only |

**Count:** 25 entities — 7 master, 14 transaction, 4 reference/control.

---

## 4. Key entity specifications

Only entities with non-obvious rules are detailed. A field list is not a design; the rules are.

### 4.1 DE-01 Customer

| Attribute | Required | Rule |
|---|---|---|
| Customer number | Yes | System-assigned or approved external range; unique, never reused |
| Legal name | Yes | Duplicate check against existing names above a similarity threshold |
| Tax / registration ID | Yes | Unique where the tax authority requires it; duplicate blocks creation (`AC-01`) |
| Payment terms | Yes | Must exist in the terms table; no free text |
| Credit limit reference | No | Absence means "no limit defined" — a risk flag, not an unlimited approval |
| Status | Yes | `Active`, `Blocked`, `Inactive`; blocked still allows read and collection |
| Owner | Yes | Named ROL-03 user, not a department |

### 4.2 DE-03 Product

| Attribute | Required | Rule |
|---|---|---|
| Product number | Yes | Unique; structure is a policy, not a design constraint |
| Description | Yes | Sales description and short description |
| Base UoM | Yes | Must exist in the UoM table |
| Sales UoM | Yes | Conversion to base UoM must be exact; no rounding loss |
| Sales status | Yes | `Active`, `Blocked`, `Inactive`; blocked prevents new order lines |
| Tax classification | Yes | Drives tax determination (`FR-22`); cannot be blank |
| Price group | No | Default for price determination; not the price itself |

### 4.3 DE-08 / DE-09 Sales order and line

| Rule | Detail |
|---|---|
| Header total | Derived from lines; never entered independently |
| Line quantity | Positive, in sales UoM; over-delivery tolerance per order type (`DEC-15`) |
| Line price | From DE-06; manual override writes old value, new value, user, and reason (`BR-11`) |
| Requested date | Must be a working day; promised date may differ and must record why |
| Status transitions | Only those in `docs/to-be-process.md` §8; no transition by direct field edit |
| Deletion | Draft may be cancelled; released order may only be cancelled with reason and stock release |

### 4.4 DE-15 Invoice

| Rule | Detail |
|---|---|
| Creation precondition | Delivery status `POD Confirmed` (`BR-10`) |
| Reference | Delivery number mandatory; order number derived through the delivery |
| Duplicate control | One invoice per delivery unless a consolidated billing rule is enabled (not in wave 1, `DEC-17`) |
| Amount | Quantity × price must reconcile to the delivery; mismatch blocks posting |
| Number | Statutory series; gap-free if required (`DQ-37`) |
| After posting | Immutable; correction by DE-20 credit note referencing the invoice (`BR-15`) |
| Status | Derived from allocations and due date, not manually set |

### 4.5 DE-17 / DE-18 Payment and allocation

| Rule | Detail |
|---|---|
| Payment identity | Customer + amount + value date + bank reference; reference may be blank only if manual matching is used |
| Allocation total | Sum of allocations ≤ payment amount (`AC-13`) |
| Multi-invoice | One payment may settle several invoices (`AC-12`) |
| Unapplied cash | Remaining amount stays on the customer account; not written off without approval |
| Reversal | Reallocation requires reason and is audited; the original allocation is retained as history |
| Difference | Short pay, deduction, or overpay needs a reason code; approval above threshold (`DQ-41`) |

### 4.6 DE-21 Exception record

Only exists if `FG-21` is delivered as an extension or a structured reason-code report. The design requirement is that an exception has **reason, owner, timestamp, and resolution** (`FR-21`). Without those four fields, KPI-07 cannot be measured.

---

## 5. Data quality rules

| # | Rule | Entity | Enforcement | Owner |
|---|---|---|---|---|
| 1 | Customer tax ID is unique | DE-01 | Hard block at entry | ROL-03 |
| 2 | Customer name near-duplicate requires reason | DE-01 | Warning plus reason | ROL-03 |
| 3 | Product number is unique and never reused | DE-03 | Hard block | ROL-03 |
| 4 | UoM conversion is exact and positive | DE-04 | Hard block | ROL-03 |
| 5 | No order line for a blocked or inactive product | DE-09 | Hard block | ROL-01 |
| 6 | No order for a blocked customer | DE-08 | Hard block | ROL-01 |
| 7 | Order total equals sum of line values | DE-08 | Derived, not entered | System |
| 8 | Price must come from a valid condition or an approved override | DE-09 | Hard block | ROL-02 |
| 9 | Invoice requires a POD-confirmed delivery | DE-15 | Hard block | ROL-07 |
| 10 | One invoice per delivery unless consolidated billing is enabled | DE-15 | Hard block | ROL-07 |
| 11 | Invoice amount reconciles to delivery quantity × price | DE-15 | Hard block | ROL-07 |
| 12 | Allocation total never exceeds payment amount | DE-18 | Hard block | ROL-07 |
| 13 | Credit note references an authorised return and the original invoice | DE-20 | Hard block | ROL-08 |
| 14 | Posted documents are immutable | DE-15, DE-20 | Hard block | System |
| 15 | Every status change writes an audit entry with user and timestamp | DE-25 | System-enforced | System |
| 16 | Master data deactivation requires a reason and a replacement record where applicable | DE-01, DE-03 | Hard block | ROL-03 |

**What quality rules cannot fix:** a correct system fed with a wrong opening stock balance produces a correct-looking wrong answer. That is why Phase 4 requires reconciliation sign-off, not only data load success.

---

## 6. Ownership and stewardship

| Entity group | Accountable owner | Responsible steward | Escalation |
|---|---|---|---|
| Customer and product | ROL-08 Finance Manager (commercial data) / ROL-04 (inventory data) | ROL-03 Master Data | Sponsor, if ownership is disputed (`AS-15`, `DQ-08`) |
| Pricing | ROL-02 Sales Manager | Sales Ops | ROL-08 for margin disputes |
| Credit | ROL-08 Finance Manager | AR team | Sponsor for limit above policy |
| Orders | ROL-02 Sales Manager | ROL-01 Sales Rep | ROL-08 for credit block |
| Inventory | ROL-04 Inventory Controller | ROL-05 Warehouse | ROL-08 for stock write-off |
| Delivery and POD | ROL-06 Logistics Coordinator | ROL-05 | ROL-08 for billing disputes |
| Invoice and payment | ROL-08 Finance Manager | ROL-07 Finance/AR | Sponsor for write-off |
| Configuration | ROL-09 ERP Administrator | IT | Change board (`NFR-04`) |

**One-owner rule:** where two roles plausibly own the same entity, this table names one accountable owner and lists the other as steward or escalation. Shared ownership is how master data becomes nobody's job.

---

## 7. Volume, retention, and migration

| Question | Status | Consequence for design |
|---|---|---|
| How many customers, products, warehouses? | Unknown (`DQ-05`) | Cannot size the solution (`NFR-10` stays `Should`) |
| How many open orders, deliveries, invoices, payments at cut-over? | Unknown (`DQ-57`) | Migration scope and reconciliation effort unknown |
| How many years of history must remain accessible? | Unknown (`DQ-51`) | Retention design is a placeholder (`NFR-09`) |
| Which legacy sources hold which entity? | Unknown (`DQ-08`) | Source-to-target mapping cannot be written |
| What duplicate rate exists in the current data? | Unknown; no extract | Cleansing effort cannot be estimated (`RSK-02`) |

### 7.1 Migration implications (hand-off to Phase 4)

| Entity | Migrate at cut-over? | Why |
|---|---|---|
| DE-01, DE-02 Customer | Yes | Open orders and receivables need a customer |
| DE-03, DE-04 Product | Yes | Open orders, stock, and invoices need a product |
| DE-05 Warehouse | Yes | Stock and deliveries are location-based |
| DE-06 Price condition | Yes, current valid only | Historical prices are not needed to operate |
| DE-07 Credit limit | Yes, if maintained | Without it, credit control starts blind (`BR-09`) |
| DE-08–DE-10 Open orders and approvals | Yes, open only | Closed history stays in the legacy archive |
| DE-11 Inventory balance | Yes, with physical count | Opening stock must be counted, not trusted from a spreadsheet |
| DE-12–DE-14 Open deliveries and POD | Yes, open only | In-transit goods need a record |
| DE-15, DE-16 Open invoices | Yes, open only | The receivable balance is the opening AR |
| DE-17, DE-18 Unapplied payments | Yes | Cash received but not applied must not be lost |
| DE-19, DE-20 Open returns and credit notes | Yes, open only | Avoids double credit after cut-over |
| DE-21 Exception records | No | Historical exceptions are not operational data |
| DE-22–DE-24 Configuration | Configured, not migrated | Values are business decisions |
| DE-25 Audit | No, beyond retention policy | New system starts its own audit trail |

**Rule:** migration moves *open obligations*, not history. A case study that promises to migrate five years of transactions is describing a different, larger project.

---

## 8. Open items and design risks

| Item | Risk if unresolved | Blocks |
|---|---|---|
| No source data profile | Cleansing scope unknown; cut-over date unplannable | `DEP-04`, Phase 4 |
| No named master data owner | Duplicate rules have no enforcer | `DQ-08`, `AS-15` |
| No volume figures | Cannot validate performance or retention | `NFR-10`, `DQ-05` |
| No retention policy | Archive design is provisional | `NFR-09`, `DQ-51` |
| Statutory invoice fields unknown | Invoice entity may be incomplete | `DQ-37`, `FR-22` |
| Tax classification per product unknown | Tax determination cannot be configured | `DQ-37` |
| Price source unknown | DE-06 may not exist as a maintained entity | `DQ-17` |

---

## 9. Change log

| Version | Date | Change |
|---|---|---|
| 0.1 | 12 Nov 2026 | Initial catalogue: 25 entities across master, transaction, and reference data; key entity rules; 16 quality rules; ownership; migration implications |
