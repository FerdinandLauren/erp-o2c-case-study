# Business Context — PT Sinar Distribusi Nusantara

**Document ID:** BC-01
**Version:** 0.1 (Draft)
**Phase:** 1 — Business Discovery
**Source documents:** `PRD.md` §1–§3, §12; `Design.md` §1, §12
**Owner:** ERP Functional Consultant / IT Business Analyst
**Status:** Draft — pending business validation

---

## 1. Purpose of this document

This document establishes the business context for the Order-to-Cash (O2C) ERP implementation case study: who the company is, how it earns revenue, which functions participate in O2C, what business problems are believed to exist, and where the project boundary sits.

It is the foundation for every later deliverable. If a statement here is wrong or unconfirmed, the As-Is process, the To-Be design, and the fit-gap analysis inherit that error. For that reason, statements are separated into three kinds:

| Kind | Marking | Meaning |
|---|---|---|
| Fact of the case | Source citation, e.g. `(PRD §1.2)` | Given by the baseline documents. Not independently verified — the company is fictional. |
| Assumption | `(AS-xx)` | Needed to proceed. Registered in `assumptions-risks.md`. Must be validated or withdrawn. |
| Open question | `(DQ-xx)` | Unknown. Registered in `discovery-questions.md`. Must not be answered by guesswork. |

---

## 2. Company profile

| Attribute | Description | Source |
|---|---|---|
| Legal name | PT Sinar Distribusi Nusantara (fictional) | PRD §1.2, §12 |
| Industry | Wholesale distribution | PRD §1.2 |
| Product scope | Packaged consumer and industrial products | PRD §1.2 |
| Customer type | Business customers (B2B) | PRD §1.2 |
| Geographic coverage | Multiple regions | PRD §1.2 |
| Current systems | Spreadsheets, email, separate departmental records | PRD §1.2 |
| ERP status | No integrated ERP in place at the start of the project (`AS-04`) | PRD §1.2 |
| Legal entities in scope | One assumed for wave 1 (`AS-02`) | DQ-01 |
| Functional currency | Single currency assumed for design (`AS-03`) | DQ-01 |

### 2.1 What the company does

The company buys or sources packaged goods and distributes them to business customers. Its value proposition is availability, reliable delivery, and commercial terms — not manufacturing brand or retail experience (`AS-05`).

The exact sourcing model (manufacture, import, or pure buy-and-resell) is **not documented** and materially changes the process boundary: manufacturing and import introduce production orders, landed cost, customs, and batch/expiry concerns that would overlap O2C. This is raised as `DQ-02`.

### 2.2 Customer base

Business customers across several regions. Within wholesale distribution, three customer patterns are typical and are treated here as hypotheses only:

| Customer pattern | Typical ordering behaviour | Design implication | Status |
|---|---|---|---|
| Small reseller / retailer | Frequent, low-value, repeat orders | Low-touch order entry, standard terms | Hypothesis (`DQ-05`, `DQ-06`) |
| Regional distributor | Scheduled, high-volume orders | Delivery scheduling, partial delivery tolerance | Hypothesis |
| Institutional / project buyer | Tender or contract-based, irregular | Contract pricing, credit control | Hypothesis |

Whether these segments exist, and their relative volume, is confirmed only by `DQ-05` and `DQ-06`. They are not asserted as company facts.

---

## 3. Business model and revenue mechanics

```text
 Source goods ──► Hold stock at warehouse(s) ──► Receive customer order
                                                        │
                                                        ▼
                                        Pick, pack, ship to customer
                                                        │
                                                        ▼
                                        Invoice customer on agreed trigger
                                                        │
                                                        ▼
                                        Collect cash, allocate to invoice
                                                        │
                                                        ▼
                                            Close receivable, report
```

Three revenue mechanics drive the functional design and are currently undocumented:

1. **Pricing basis** — price list, customer contract, or negotiated per order (`DQ-17`). Without a defined basis, invoice accuracy cannot be controlled and billing disputes are unavoidable.
2. **Billing trigger** — invoice on order confirmation, on shipment, or on proof of delivery (`DQ-33`, `BR-06`). This single decision changes revenue timing, credit exposure, and the whole To-Be flow.
3. **Delivery obligation** — whether the company delivers, the customer collects, or a carrier is used (`DQ-29`). This determines who owns proof of delivery and delivery discrepancies.

---

## 4. Organisational functions involved in O2C

Only functions that touch the order-to-cash chain are listed. Reporting lines, headcount, and organisational structure are unknown (`DQ-49`).

| Function | Role in O2C | Primary output |
|---|---|---|
| Sales | Customer relationship, order capture, commercial negotiation | Confirmed sales order |
| Sales Management | Exception review, commercial control | Approval or rejection decision |
| Master Data / Sales Ops | Customer and product master maintenance | Valid master records |
| Inventory Control | Stock accuracy and availability | Availability commitment |
| Warehouse | Physical fulfilment | Picked, packed, shipped quantities |
| Logistics | Transport and delivery execution | Delivery confirmation and evidence |
| Finance / AR | Invoicing, collections, cash application | Invoice, payment allocation |
| Finance Management | Receivables control, financial exceptions | Financial approval |
| Management | Performance monitoring | Consolidated reporting |
| IT / Integration | Access, integration, technical support | Working, controlled system access |
| ERP Functional Consultant | Translate business needs into ERP design | This repository |

**Structural observation:** the design distinguishes "Master Data / Sales Ops" as an owner (`Design.md` §6.1), while `PRD.md` assigns customer master ownership to "Sales Ops" (FR-01) and product master to "Master Data" (FR-02). Whether a dedicated master-data function exists, or the responsibility sits inside Sales Ops, is unresolved (`DQ-08`, `DQ-14`). This matters because unclear data ownership is the most common cause of duplicate master records.

---

## 5. Current system landscape (As-Is)

The company operates without an integrated ERP. Information is held in department-specific spreadsheets, exchanged by email, and supplemented by separate departmental records (`PRD §1.2`).

| Capability area | Current mechanism | Integration | Confidence |
|---|---|---|---|
| Customer records | Spreadsheet(s) maintained by Sales | None | High (PRD §1.2) |
| Product records | Spreadsheet(s), possibly duplicated per department | None | High (PRD §1.2) |
| Order capture | Manual entry from email / customer spreadsheet | None | High (PRD §3) |
| Stock visibility | Separate stock records | None | High (PRD §3) |
| Fulfilment instruction | Manual communication to Warehouse | None | High (PRD §3) |
| Delivery tracking | Separate logistics records | None | High (PRD §3) |
| Invoicing | Manual preparation from order and delivery information | None | High (PRD §3) |
| Cash application | Manual reconciliation | None | High (PRD §3) |
| Reporting | Manual consolidation from multiple sources | None | High (PRD §3) |

Not yet known, and required before Phase 3:

- How many spreadsheet files exist, who owns each, and which is authoritative (`DQ-08`).
- Whether any point solution exists (accounting package, warehouse tool, CRM) that must integrate or be retired (`DQ-53`).
- Whether customers submit orders through a portal or EDI today (`DQ-06`, `DQ-56`).

### 5.1 Why the current landscape fails at scale

The As-Is model is not "wrong" — it is locally efficient and globally inconsistent. Each department optimises its own record. The cost appears at the seams:

- The same customer or product is created more than once, with divergent attributes.
- Stock commitments are made from data that is already stale.
- A handoff is complete only when an email is read, so process state lives in people's inboxes.
- Reconstructing "what happened to order X" requires assembling evidence from four functions.
- Reports disagree because they are built from different snapshots.

These are the mechanics behind the pain points catalogued in `docs/as-is-process.md` §5.

---

## 6. Business drivers

| Driver | Description | Implication |
|---|---|---|
| Growth beyond spreadsheet capacity | Order and SKU volume outgrow manual coordination (`PRD §1.2`) | Need a single transactional system of record |
| Management visibility | Consolidated performance reporting is manual (`PRD §3`) | Standard data model and KPI definitions |
| Service reliability | Customers depend on delivery promises | Availability check before commitment (`BR-03`) |
| Cash flow discipline | Receivables tracked separately from billing (`PRD §3`) | Invoice-to-cash traceability (`FR-13`) |
| Operational standardisation | Departments work to local habits | To-Be process with named owners (`Design.md §2`) |
| Auditability | Transaction history is reconstructed manually | System-recorded user and timestamp (`NFR-02`, `FR-15`) |

These drivers are the reason the project exists. They are business hypotheses, not measured findings (`PRD §1.3`).

---

## 7. Business problems (problem statement)

The current process fragments information across Sales, Warehouse, Logistics, and Finance (`PRD §1.3`). The resulting problems are stated as **hypotheses to be validated**, because no baseline measurement has been performed.

| ID | Problem hypothesis | Business consequence if true | Validate via |
|---|---|---|---|
| PB-01 | Sales orders are accepted without reliable inventory visibility | Over-commitment, missed delivery promises, cancelled orders | DQ-23, DQ-24, KPI-03 |
| PB-02 | Departments re-enter the same information | Labour waste, transcription errors, version conflicts | DQ-08, DQ-19 |
| PB-03 | Manual communication delays handoffs | Longer order cycle time, idle stock, idle trucks | DQ-19, DQ-28, KPI-01 |
| PB-04 | Delivery and order status are hard to track | Poor customer service, reactive escalation | DQ-30, DQ-31 |
| PB-05 | Invoice preparation depends on multiple sources | Billing delay, revenue leakage, disputes | DQ-33, DQ-34, KPI-04 |
| PB-06 | Payment reconciliation is handled separately | Unapplied cash, ageing inaccuracy, collection delay | DQ-39, DQ-41, KPI-05 |
| PB-07 | Management reporting requires manual consolidation | Slow, inconsistent decisions | DQ-54, DQ-55, all KPIs |

**Caution for the portfolio narrative:** these must never be written up as measured company performance. They are the hypotheses the discovery phase exists to test (`PRD §1.3`, `§12`).

---

## 8. Business objectives and success measures

Derived from `PRD.md` §1.4. Objectives are business-facing; success measures are the KPIs in `PRD.md` §10, whose formulas still require stakeholder confirmation.

| ID | Objective | Success measure (proposed) | Baseline available? |
|---|---|---|---|
| OBJ-01 | Standardise the end-to-end O2C process | One documented To-Be process with named owners for every step | n/a (design artefact) |
| OBJ-02 | Establish clear departmental responsibilities | Role and authorisation matrix approved | n/a (design artefact) |
| OBJ-03 | Define customer, product, and transaction data requirements | Data catalogue with named data owners | n/a (design artefact) |
| OBJ-04 | Identify required ERP capabilities | Capability map validated against a selected product | No — pending `DQ-61` |
| OBJ-05 | Complete fit-gap analysis for all Must requirements | 100% of `Must` FRs classified (`Plan.md` Gate 3) | No — Phase 3 |
| OBJ-06 | Design a standardised target process | To-Be approved by process owners | No — Phase 2 |
| OBJ-07 | Prepare UAT and implementation documentation | Every Must requirement has ≥1 test scenario | No — Phase 4 |
| OBJ-08 | Define post-implementation KPIs | KPI definitions with formula, source, cut-off, owner | No — baseline measurement not performed |

**Honest limitation:** the case study can prove design completeness, not business results. Without a live environment, KPI targets and benefit claims would be fabricated. They are therefore explicitly excluded (see `assumptions-risks.md`, `RSK-08`).

---

## 9. Project scope boundary

### 9.1 In scope

Business process analysis; stakeholder identification; As-Is and To-Be design; functional and non-functional requirements; business rules; fit-gap analysis; functional blueprint; role and authorisation design; master and transaction data design; integration considerations; UAT planning; data migration planning; cutover and hypercare planning (`PRD §1.5`).

### 9.2 Out of scope

| Excluded | Reason |
|---|---|
| Building a complete ERP platform | The project is functional analysis, not software development (`PRD §1.5`, `Plan.md §2`) |
| Installing or configuring a live SAP / Oracle / Microsoft Dynamics environment | No licensed environment; vendor claims could not be substantiated |
| Real banking or payment system connectivity | Requires real credentials and contracts |
| Processing real customer transactions | No real company or data exists |
| Production-grade integrations | Interface design only, no build |
| Legal, tax, or accounting advice | Outside consultant competence and professional liability |
| Procurement-to-Pay, manufacturing, HR, fixed assets | Not part of the O2C boundary (`DQ-03`) |
| Benefit quantification / ROI claims | No baseline, no live measurement (`RSK-08`) |

### 9.3 Boundary decisions that still need confirmation

| Boundary question | Why it matters | Reference |
|---|---|---|
| Is procurement (P2P) in the same programme? | Shared supplier and stock model; sequencing risk | DQ-03 |
| Does the company manufacture or import? | Production/import steps touch stock and cost before O2C begins | DQ-02 |
| Are returns and credit notes in wave 1? | Return handling adds reverse logistics and credit control | DQ-22 |
| Are multiple legal entities or currencies involved? | Multi-entity design changes master data, tax, and reporting | DQ-01 |
| Is a separate WMS or TMS retained? | Determines integration surface and ownership of stock truth | DQ-27, DQ-53 |

---

## 10. Constraints

| ID | Constraint | Consequence |
|---|---|---|
| CON-01 | Fictional company; no access to real stakeholders, documents, or systems (`AS-01`) | Discovery is simulated; every "answer" is an assumption until validated |
| CON-02 | Vendor-neutral design (`PRD §12`) | Fit-gap stays at hypothesis level; no product screenshots or configuration claims |
| CON-03 | Five-week effort, single author (`Plan.md` §1) | Depth traded against breadth; some deliverables remain outline-level |
| CON-04 | No production environment | No performance, load, or integration testing evidence |
| CON-05 | No legal/tax authority | Tax and statutory e-invoicing requirements are raised as questions, never asserted (`DQ-37`) |
| CON-06 | Documents must remain internally consistent | IDs and terminology are controlled (`README.md` §5) |

---

## 11. Glossary

| Term | Definition in this case study |
|---|---|
| As-Is | The current, pre-implementation process |
| To-Be | The proposed target process after ERP implementation |
| O2C | Order-to-Cash: customer order through to cash collection and receivable closure |
| Sales order (SO) | Customer commitment to purchase specified products, quantities, prices, and terms |
| Fulfilment | Physical preparation of goods: picking, packing, staging |
| Delivery | Transfer of goods to the customer, with confirmation and evidence |
| Billing trigger | The business event that authorises invoice creation |
| AR | Accounts Receivable: money owed by customers for delivered goods |
| Payment allocation | Applying a received payment against one or more invoices |
| Unapplied cash | Received money not yet matched to an invoice |
| Master data | Relatively stable reference data: customer, product, warehouse, terms |
| Transaction data | Business events: orders, deliveries, invoices, payments |
| Fit-gap | Comparison of business requirement against standard ERP capability |
| Configuration | Setup of existing ERP capability without custom code |
| Extension | Custom development to close a validated capability gap |
| Segregation of duties | Separation of incompatible responsibilities to prevent error and fraud |
| UAT | User Acceptance Testing: business validation that the solution meets requirements |
| Cutover | Controlled transition from legacy process to the new system |
| Hypercare | Intensive post-go-live support period |
| KPI | Key Performance Indicator with an agreed formula and owner |

---

## 12. What this document does not establish

To keep the case study defensible, the following are explicitly **not** established here:

- Company size, revenue, headcount, order volume, SKU count, or number of warehouses.
- Organisational chart, reporting lines, or delegation-of-authority limits.
- Any measured baseline for the KPIs in `PRD.md` §10.
- Any claim that an ERP product supports a given requirement.
- Any statutory, tax, or e-invoicing obligation.
- Any statement about competitors, market share, or customer satisfaction.

Each gap is tracked in `docs/discovery-questions.md`. Phase 1 is complete only when those questions are either answered or formally deferred (`README.md` §6).
