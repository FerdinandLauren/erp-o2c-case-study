# Functional Solution Blueprint

**Document ID:** FBP-01
**Version:** 0.1 (Draft)
**Phase:** 3 — Fit-Gap Analysis and Solution Blueprint
**Inputs:** `docs/to-be-process.md`, `docs/requirements.md`, `docs/fit-gap-analysis.md`, `docs/role-authorization-matrix.md`, `docs/data-catalogue.md`, `docs/integration-inventory.md`
**Diagram:** `diagrams/solution-architecture.mmd`
**Status:** Draft — vendor-neutral conceptual design; no product configured

---

## 1. Purpose and boundary

This blueprint explains **how the To-Be process would be realised in an ERP**, at the level of design objects a consultant configures: document types, statuses, rules, tolerances, roles, and outputs. It is the bridge between the process narrative (`docs/to-be-process.md`) and a future configuration workbook.

It deliberately does **not** contain:

- transaction codes, module names, or customising paths from any product;
- screen layouts or field-level UI specification;
- database schema, tables, or APIs;
- a build plan, effort estimate, or licence recommendation.

Those belong to a product-specific design phase. This document is the last artefact that can be written correctly without choosing a vendor.

---

## 2. Architecture principles

| Principle | Practical consequence in this blueprint |
|---|---|
| One document chain, no free-standing transactions | Invoice requires a delivery reference; credit note requires delivery and invoice; payment allocation requires invoice (`FR-13`, `BR-15`) |
| Status is a promise, not a label | Each object has one lifecycle; transitions are restricted by role, not by convention (`FR-20`, `NFR-01`) |
| Rules are configured, not remembered | Approval bands, tolerances, credit limits, and price overrides are system-enforced (`BR-04`, `BR-05`, `BR-09`, `BR-11`) |
| Human authority for commercial decisions | Credit release, approval, write-off, and return authorisation are role-gated, never automatic (`DEC-05`) |
| Master data is owned | One named owner per entity; deactivation over deletion (`BR-13`, `NFR-04`) |
| Integrations are optional at go-live | Core O2C runs without CRM, WMS, carrier, or bank interfaces; each interface is an enhancement (`§7`) |
| Audit is a by-product of design | Every state change writes user, timestamp, old value, new value, and reason where required (`FR-15`, `NFR-02`) |

---

## 3. Solution landscape

The conceptual landscape has four parts. The diagram source is `diagrams/solution-architecture.mmd`.

| Layer | Contents | Design responsibility |
|---|---|---|
| User channels | Sales desk, warehouse terminal, finance workstation, management reporting | Role-based access, not separate systems |
| ERP core | Master data, order, approval, inventory, warehouse, delivery, billing, receivables, returns, audit, reporting | All process rules and document links |
| External systems | CRM/portal, WMS, carrier, bank, tax/e-invoice, reporting platform | Optional or statutory; each has an interface record (`INT-`) |
| Data stores | ERP database and reporting extract | One source of truth for open documents; reporting is read-only |

**Deployment questions are not answered here.** Hosting model, number of environments, and disaster recovery are IT decisions (`DQ-51`, `DQ-59`). The blueprint only requires that a test environment exists before Phase 4 (`DEP-07`).

---

## 4. Core document flow and data ownership

| Document | Created from | Created by | Owns | Cannot exist without |
|---|---|---|---|---|
| Customer master | Change request | Master Data | Customer ID, terms, credit limit, status | Valid tax and address data |
| Product master | Change request | Master Data | Product ID, UoM, sales status, price group | Valid UoM and classification |
| Sales order | Customer requirement | Sales | Customer, requested date, lines, price | Active customer and product |
| Approval decision | Sales order | Sales Manager / Finance | Decision, reason, timestamp | Order above threshold or credit exposure |
| Delivery | Released order | Warehouse / Logistics | Ship-from, ship-to, quantity, shipment date | Released order with stock |
| Proof of delivery | Delivery | Logistics | Receiver, date, evidence reference | Completed delivery |
| Invoice | Confirmed delivery | Finance / AR | Amount, tax, due date, invoice number | POD confirmed (`BR-10`) |
| Payment | Bank receipt | Finance / AR | Amount, date, reference, customer | Identified payer |
| Payment allocation | Payment + invoice | Finance / AR | Allocated amount per invoice | Open invoice and unapplied payment |
| Return request | Customer claim | Sales / Customer Service | Reason, quantity, original delivery | Original delivery and invoice |
| Credit note | Authorised return | Finance | Credit amount, tax reversal, reference | Return authorisation (`BR-12`) |

**Rule:** a document may reference an earlier document or a master record, never copy its values. Copying price, address, or terms into a transaction and then editing it silently is how master data stops being a source of truth.

---

## 5. Functional design by stage

Each stage lists the design objects, the rules enforced, and the requirement it satisfies. Configuration identifiers are defined in §8.

### 5.1 S1 — Master data

| Design object | Behaviour | Requirement |
|---|---|---|
| Customer account groups | Separate sold-to, ship-to, payer; one customer number series | FR-01 |
| Duplicate check | Exact tax ID and name match blocks; near-match warns and requires a reason | FR-23 |
| Product sales view | Sales UoM, base UoM, conversion, sales status, tax classification | FR-02 |
| Deactivation | Status change only; no deletion once a transaction exists | BR-13 |
| Change log | Field-level history with user and timestamp | FR-15 |

**Data quality gate:** no customer or product is activated without tax/registration data, address, payment terms, and UoM. This is a *process* control (`FG-P-04`), not a system guarantee.

### 5.2 S2–S3 — Order entry and validation

| Design object | Behaviour | Requirement |
|---|---|---|
| Order types | Standard order, rush order (same controls, different lead time), return order (reverse flow) | FR-03 |
| Number range | One series per order type; gaps are allowed but logged | FR-03 |
| Incompletion check | Order cannot be confirmed while mandatory fields are blank | FR-04 |
| Price determination | Price from maintained condition; manual override records old price, new price, user, reason | FR-19 |
| Duplicate order warning | Same customer + same day + same value range triggers a warning | FR-21 (partial) |
| Status | `Draft` → `Submitted` → `Approved` / `Rejected` → `Released` | FR-20 |

**Design choice:** validation is split into *hard blocks* (missing data, inactive product, invalid UoM) and *soft warnings* (price below list, unusual quantity, duplicate suspicion). Hard blocks protect the process; soft warnings preserve commercial judgement. Conflating them either stops valid business or trains users to ignore messages.

### 5.3 S4–S5 — Availability, approval, credit

| Design object | Behaviour | Requirement |
|---|---|---|
| Availability check | ATP = on-hand minus reservations, excluding in-transit (`DEC-13`); checked per line and per warehouse | FR-05 |
| Reservation | Created at order confirmation, released on cancellation or rejection (`DEC-12`) | FR-05 |
| Approval matrix | Band A auto-release within limit; Band B Sales Manager; Band C Sales Manager + Finance (`DEC-14`; thresholds `DQ-46`) | FR-06 |
| Credit check | Exposure = open receivables + open order value + overdue balance; compare with approved limit; block or route to Finance | FR-17 |
| Credit release | Finance-only action with reason, timestamp, and optional expiry | BR-09 |
| Backorder | Unconfirmed quantity stays open with a next-available date; release is a controlled action | FR-18 |

**Ordering rule:** credit is evaluated before value-band approval. Otherwise a manager can approve an order that Finance must immediately block, and the audit trail shows an approval that never took effect.

### 5.4 S6–S7 — Fulfilment and delivery

| Design object | Behaviour | Requirement |
|---|---|---|
| Delivery type | One delivery per shipment; consolidated shipment allowed only if the invoice remains per delivery (`DEC-17`) | FR-07 |
| Picking confirmation | Records picked quantity per line; short pick creates a backorder | FR-08, FR-18 |
| Packing | Records packed quantity and package count | FR-08 |
| Over-delivery tolerance | 0% default; higher only with approved tolerance on the order type (`DEC-15`) | BR-14 |
| Goods issue | Reduces stock at shipment, not at order | FR-07 |
| POD capture | Receiver name, date, evidence reference; mandatory before billing (`BR-10`) | FR-09 |
| Delivery status | `Created` → `Picked` → `Packed` → `Shipped` → `POD Confirmed` / `Failed` | FR-20 |

**Control:** the POD reference is the only thing standing between "we think it arrived" and "we can bill it". If POD capture is manual (`FG-09` Option A), the reference field must be mandatory, not free text marked optional.

### 5.5 S8 — Billing

| Design object | Behaviour | Requirement |
|---|---|---|
| Billing type | Invoice per confirmed delivery; no invoice without POD (`DEC-11`, `BR-10`) | FR-10 |
| Invoice number range | Statutory series; gap-free where required by tax rules (`DQ-37`) | FR-22 |
| Invoice copy control | One invoice per delivery; duplicate submission rejected and logged | FR-10 |
| Price and quantity check | Invoice value must reconcile to delivery quantity × agreed price; mismatch blocks posting | FR-19 |
| Tax determination | Configured per product tax classification and customer status; no tax advice given | FR-22 |
| Output | Invoice document with statutory fields; channel depends on `DQ-37` | FR-22 |
| Status | `Posted` → `Sent` → `Partially Paid` → `Cleared` / `Overdue` / `Disputed` | FR-11 |

**Rule:** a posted invoice is never edited. Correction is a credit note or debit note referencing it (`BR-15`). This is the single most important AR control in the blueprint.

### 5.6 S9 — Cash application

| Design object | Behaviour | Requirement |
|---|---|---|
| Payment record | Customer, amount, value date, bank reference, method | FR-12 |
| Matching rule | Exact amount + invoice reference auto-matches; partial or ambiguous goes to a worklist (`DEC-18`) | FR-12 |
| Allocation | One payment to many invoices; total allocation cannot exceed payment amount | FR-12 |
| Unapplied cash | Residual remains visible on the customer account; never silently written off | BR-07 |
| Difference handling | Short payment, deduction, or overpayment needs a reason code and, above a threshold, approval | FR-12, `DQ-41` |

**Design choice:** the system proposes a match; a human confirms exceptions. Full automation of cash application without a bank feed and clean references would create false confidence.

### 5.7 S10 — Reporting and collections

| Design object | Behaviour | Requirement |
|---|---|---|
| Report catalogue | Order status, fulfilment rate, billing due, AR ageing, credit exposure, exception count | FR-14 |
| KPI definitions | One written definition per KPI, with cut-off and source document (`docs/traceability-matrix.md` §3.3) | NFR-08 |
| Collection worklist | Invoices past due, sorted by exposure and days overdue | FR-14 |
| Exception extract | Count and age exceptions by reason and owner | FR-21 |

**Limitation:** KPI-07 (exception rate) is only measurable if exception reasons are captured consistently. `FG-21` is classified `Extension` precisely because the register may not exist in the standard product.

### 5.8 R1–R5 — Returns and credit notes

| Design object | Behaviour | Requirement |
|---|---|---|
| Return request | References original delivery and invoice; reason code mandatory | FR-16 |
| Authorisation | Separate role from requester; cannot self-approve | BR-12 |
| Goods receipt | Returned quantity inspected; scrap or return to stock | FR-16 |
| Credit note | References delivery and invoice; reverses tax proportionally | FR-16, BR-15 |
| Status | `Requested` → `Authorised` → `Received` → `Credited` / `Rejected` | FR-20 |

The reverse flow reuses the same document discipline as the forward flow. It is not a manual ledger adjustment with a note attached.

---

## 6. Cross-cutting design

| Area | Design position | Requirement |
|---|---|---|
| Numbering | One series per document type; statutory invoice series may not have gaps | FR-03, FR-22 |
| Status models | Fixed lifecycle per object; role-restricted transitions; no reopening of closed documents | FR-20 |
| Audit | User, timestamp, action, old/new value, reason; exportable | FR-15, NFR-11 |
| Authorisation | Role-based, least privilege, SoD conflict matrix | NFR-01, `docs/role-authorization-matrix.md` |
| Master data | Single owner, deactivation, duplicate prevention | NFR-04, BR-13 |
| Reporting | One KPI definition and cut-off; two reports for one period must agree | NFR-08 |
| Output management | Invoice and credit note templates; print/email channel; no manual PDF editing | FR-22 |
| Data retention | Archive per policy; retrieval tested before go-live | NFR-09 |

---

## 7. Integration design stance

**Core O2C must work with zero interfaces.** Every interface is an enhancement, not a precondition. This is a deliberate risk decision (`RSK-07`): interfaces are the most common cause of go-live delay.

| Interface | Wave 1 position | If absent |
|---|---|---|
| Bank statement | Manual upload or file import; no direct bank connection | Cash application is manual but controlled |
| Tax / e-invoice | Depends on `DQ-37`; provider integration if mandated | Invoicing may be non-compliant — a go-live blocker |
| Carrier tracking | POD recorded manually (`FG-09` Option A) | Delivery status lags, invoice waits for POD |
| WMS | Stock maintained in ERP | Warehouse confirms in ERP; no separate system |
| CRM / portal | Not in wave 1 | Orders keyed by Sales |
| Reporting platform | Standard reports and extract | Management reporting is slower, not absent |

Each interface is detailed in `docs/integration-inventory.md`. The blueprint fixes only the ownership rule: **the system of record for a data object is never shared.** Customer master belongs to ERP; stock quantity belongs to ERP unless a WMS is confirmed as the owner.

---

## 8. Configuration register

These are design objects, not product transactions. A configuration workbook would later map each to a specific product artefact.

| ID | Design object | Key settings | Requirement | Fit-gap |
|---|---|---|---|---|
| CFG-01 | Customer account groups and number range | Sold-to / ship-to / payer, external numbering, required fields | FR-01 | FG-01 |
| CFG-02 | Product sales data and UoM | Base UoM, sales UoM, conversion, sales status, tax class | FR-02 | FG-02 |
| CFG-03 | Sales order types | Standard, rush, return; number ranges; reference mandatory | FR-03 | FG-03 |
| CFG-04 | Order incompletion rules | Mandatory fields per order type; hard block versus warning | FR-04 | FG-04 |
| CFG-05 | Availability check and reservation | ATP formula, checking group, reservation timing (`DEC-12`, `DEC-13`) | FR-05 | FG-05 |
| CFG-06 | Approval matrix | Value bands A/B/C, approver roles, escalation, reason mandatory | FR-06 | FG-06 |
| CFG-07 | Credit limit and exposure | Limit per customer, exposure formula, block and release roles | FR-17 | FG-17 |
| CFG-08 | Delivery types and picking | Delivery per shipment, picking and packing confirmation, short-pick rule | FR-07, FR-08 | FG-07 |
| CFG-09 | Quantity tolerances | Over-delivery 0% default, under-delivery allowed, approval for exceptions | FR-08 | FG-08 |
| CFG-10 | POD capture | Receiver, date, evidence reference; mandatory before billing | FR-09 | FG-09 |
| CFG-11 | Billing types and copy control | Invoice per delivery, duplicate block, billing due list | FR-10 | FG-10 |
| CFG-12 | Invoice status and clearing | Posted / sent / partially paid / cleared / disputed; ageing link | FR-11 | FG-11 |
| CFG-13 | Payment terms and allocation | Terms per customer, auto-match rule, unapplied cash, difference codes | FR-12 | FG-12 |
| CFG-14 | Document flow and references | Parent reference on delivery, invoice, credit note, allocation | FR-13 | FG-13 |
| CFG-15 | Report catalogue | Standard reports, selection variants, KPI definitions, cut-off | FR-14 | FG-14 |
| CFG-16 | Audit and change log | Field-level history, reason codes, export, retention | FR-15 | FG-15 |
| CFG-17 | Return and credit-note types | Return reasons, authorisation role, goods receipt, credit note reference | FR-16 | FG-16 |
| CFG-18 | Backorder and outstanding quantity | Open quantity, next-available date, release action, customer notice | FR-18 | FG-18 |
| CFG-19 | Price conditions and override | Price list, customer condition, override tolerance, approval | FR-19 | FG-19 |
| CFG-20 | Status profiles and transitions | Allowed transitions per role; no reopening closed documents | FR-20 | FG-20 |
| CFG-21 | Exception reason codes | Codes per document type; owner and resolution fields | FR-21 | FG-21 |
| CFG-22 | Tax determination and invoice output | Tax classification, statutory fields, output template, channel | FR-22 | FG-22 |
| CFG-23 | Duplicate-check rules | Exact match on tax ID and name; near-match threshold and reason | FR-23 | FG-23 |

**What is missing from this register:** any setting whose value is a business policy. Thresholds, tolerances, credit limits, and price tolerances are *parameters of the design*, not design decisions. They stay blank until `DQ-17`, `DQ-26`, `DQ-45`, and `DQ-46` are answered, and a blank parameter is a visible project risk rather than a hidden guess.

---

## 9. Requirement coverage by design

| Stage | Requirements designed | Design sections |
|---|---|---|
| S1 | FR-01, FR-02, FR-23 | §5.1 |
| S2–S3 | FR-03, FR-04, FR-19, FR-21 (partial) | §5.2 |
| S4–S5 | FR-05, FR-06, FR-17, FR-18 | §5.3 |
| S6–S7 | FR-07, FR-08, FR-09 | §5.4 |
| S8 | FR-10, FR-11, FR-22 | §5.5 |
| S9 | FR-12 | §5.6 |
| S10 | FR-14, FR-21 | §5.7 |
| R1–R5 | FR-16 | §5.8 |
| Cross-cutting | FR-13, FR-15, all NFR | §6 |
| Not designed here | FR-20 is a cross-cutting status model, detailed per object in §5 | §5.2–§5.8 |

Every functional requirement maps to a design section. That is a completeness check, not proof of correctness.

---

## 10. Open design questions and limits

| Item | Why it stays open | Affected design |
|---|---|---|
| Approval thresholds | Policy decision (`DQ-46`) | CFG-06 |
| Credit limits and release authority | Policy decision (`DQ-45`) | CFG-07 |
| Billing trigger | Provisional POD (`DEC-11`, `DQ-33`) | CFG-11 |
| Returns in wave 1 | Provisional inclusion (`DEC-16`, `DQ-22`) | CFG-17 |
| Consolidation of invoices | Deferred (`DEC-17`, `DQ-34`) | CFG-11 |
| E-invoice mandate | Unknown (`DQ-37`) | CFG-22, `INT-` tax interface |
| Bank format and matching tolerance | Unknown (`DQ-41`) | CFG-13 |
| Product and edition | Unknown (`DQ-61`, `DQ-63`) | All CFG items |

**Hard limit:** this blueprint cannot be converted into a build estimate without a product. Any number produced now would measure the author's imagination, not the work.

---

## 11. Change log

| Version | Date | Change |
|---|---|---|
| 0.1 | 12 Nov 2026 | Initial blueprint: architecture, document chain, stage-by-stage design, cross-cutting rules, 23 configuration objects, requirement coverage |
