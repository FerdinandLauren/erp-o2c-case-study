# Discovery Questions and Open Decisions

**Document ID:** DQ-REG
**Version:** 0.1 (Draft)
**Phase:** 1 — Business Discovery
**Source documents:** `PRD.md` §7–§9, §12; `Design.md` §12; `docs/business-context.md`; `docs/as-is-process.md`
**Status:** 63 open items — none answered

---

## 1. Purpose and rules

Every gap identified during Phase 1 is recorded here instead of being filled with a plausible-sounding guess. This register is the quality control for the case study: a reviewer can see exactly what is known, what is assumed, and what still needs a business decision.

Rules:

1. **One question per item.** A question that requires two different people to answer is two questions.
2. **No leading questions.** "Do you use credit limits?" invites a yes/no; "How are credit limits set, applied, and released?" produces a process.
3. **No invented answers.** Answered items keep the question, the answer, the answerer, and the date. An empty answer column is information.
4. **IDs are permanent.** Deferred or withdrawn items stay in the register.
5. **Blockers gate the phase.** A `Blocker` must be resolved before the phase that depends on it can pass its quality gate (`Plan.md §6`).

### 1.1 Priority definitions

| Priority | Meaning | Consequence if unresolved |
|---|---|---|
| Blocker | Design cannot proceed correctly | Phase gate cannot pass; downstream work is provisional |
| High | Materially changes process, data, or control design | Design must be revisited; rework likely |
| Medium | Affects usability, reporting, or effort estimate | Acceptable to proceed with a documented assumption |
| Low | Detail that can be settled during configuration or UAT | No design impact |

### 1.2 Status definitions

| Status | Meaning |
|---|---|
| Open | Not yet discussed |
| In progress | Discussed; answer incomplete or conflicting |
| Answered | Answer recorded with owner and date |
| Deferred | Consciously postponed, with a named future decision point |
| Out of scope | Excluded from wave 1, recorded for later |

---

## 2. Question register

### 2.1 Business, scope, and organisation

| ID | Question | Why it matters | Owner | Priority | Trace | Status |
|---|---|---|---|---|---|---|
| DQ-01 | Which legal entities, branches, and regions must the first implementation wave cover, and in which currency do they transact? | Multi-entity and multi-currency change master data, tax, and consolidation design | STK-09 | Blocker | `AS-02`, `AS-03` | Open |
| DQ-02 | Does the company manufacture, import, or purely buy-and-resell? | Manufacturing/import introduces production, landed cost, and batch concerns that overlap O2C | STK-09 | Blocker | `AS-05` | Open |
| DQ-03 | Is wave 1 limited to O2C, or does it also cover procurement (P2P) and general finance? | Shared stock and supplier model; sequencing and scope risk | STK-09 | Blocker | `AS-08`, `RSK-10` | Open |
| DQ-04 | How many warehouses and storage locations exist, and are any operated by a third party? | Determines stock model, delivery logic, and integration surface | STK-04 | High | `PP-02` | Open |
| DQ-05 | What volumes must the design support: orders per day, order lines per order, active customers, active SKUs, delivery points? | Needed for NFR sizing, warehouse design, and migration effort | STK-09 | High | `NFR-06` | Open |
| DQ-06 | Which sales channels exist: field sales, telesales, branch counter, customer portal, EDI? | Each channel needs a different order capture and validation path | STK-02 | High | `DQ-56` | Open |
| DQ-07 | Which three business outcomes does management expect within twelve months of go-live? | Prioritises requirements and defines what "success" means | STK-09 | High | `OBJ-01`–`OBJ-08` | Open |

### 2.2 Master data

| ID | Question | Why it matters | Owner | Priority | Trace | Status |
|---|---|---|---|---|---|---|
| DQ-08 | Which spreadsheets or databases currently hold customer, product, and warehouse records, who owns each, and which one is authoritative? | Without a known source of truth, migration scope and ownership cannot be defined | STK-03 | Blocker | `CG-01`, `PP-01` | Open |
| DQ-09 | Is there an existing coding standard for customer and product identifiers, and must legacy codes be preserved? | Determines ID design, migration mapping, and user familiarity | STK-03 | High | `BR-01`, `DQ-57` | Open |
| DQ-10 | Which fields are mandatory to create a customer: legal name, tax ID, credit limit, payment terms, billing and shipping addresses, contacts? | Defines validation rules and `FR-01` acceptance criteria | STK-03, STK-08 | High | `FR-01`, `BR-02` | Open |
| DQ-11 | Do customers have multiple ship-to and bill-to addresses, parent-child groups, or central billing arrangements? | Address and hierarchy modelling affects order entry and invoicing | STK-03 | High | `FR-01` | Open |
| DQ-12 | Are products sold in multiple units of measure with conversion factors (e.g. carton vs piece)? | Wrong UoM design causes quantity and pricing errors throughout O2C | STK-03 | High | `FR-02` | Open |
| DQ-13 | Do products require batch, expiry-date, or serial-number tracking? | Changes warehouse, delivery, and recall design; cannot be retrofitted cheaply | STK-04 | High | `FR-02`, `FR-08` | Open |
| DQ-14 | Who may create, change, and deactivate customer and product master records, and what approval is required? | Uncontrolled master data is the root cause of duplicate and invalid records | STK-09 | High | `NFR-04`, `AS-15` | Open |
| DQ-15 | How are duplicates detected and resolved today, and how many duplicate records are known to exist? | Sizes cleansing effort and justifies duplicate-prevention controls | STK-03 | Medium | `PP-01`, `RSK-02`, `RSK-03` | Open |

### 2.3 Sales order and pricing

| ID | Question | Why it matters | Owner | Priority | Trace | Status |
|---|---|---|---|---|---|---|
| DQ-16 | Which order types exist (standard, rush, sample, consignment, return) and which need different handling? | Order type drives validation, approval, and billing behaviour | STK-02 | High | `FR-03`, `DQ-22` | Open |
| DQ-17 | Where do prices come from: price list, customer contract, or per-order negotiation? Who maintains them and how often? | Without a pricing source, invoice accuracy cannot be controlled | STK-02, STK-08 | Blocker | `FR-10`, `BR-06` | Open |
| DQ-18 | Are discounts and rebates approved per order line, per agreement, or retroactively? | Affects margin control, approval rules, and credit-note volume | STK-02 | High | `FR-06`, `DQ-46` | Open |
| DQ-19 | Which data is captured at order entry, and is any handoff currently timestamped or acknowledged? | Defines mandatory fields and the baseline for `KPI-01` | STK-01 | High | `FR-04`, `PP-03` | Open |
| DQ-20 | May an order be amended after confirmation, and which changes require re-approval? | Determine traceability and control requirements for changes | STK-02 | High | `FR-03`, `FR-15` | Open |
| DQ-21 | Are there minimum order quantities or values, and what happens when they are not met? | Affects order validation and exception handling | STK-02 | Medium | `FR-04` | Open |
| DQ-22 | How are cancellations, returns, and credit notes handled today, and must they be in wave 1? | A missing reverse flow makes the O2C design incomplete | STK-02, STK-08 | Blocker | `Design.md §12` Q5 | Open |

### 2.4 Inventory and warehouse

| ID | Question | Why it matters | Owner | Priority | Trace | Status |
|---|---|---|---|---|---|---|
| DQ-23 | How is stock recorded per location today, how often is it physically counted, and what is the known accuracy level? | Determines whether availability data can be trusted at go-live | STK-04 | High | `PP-02`, `KPI-03` | Open |
| DQ-24 | What is the stock commitment policy: reserve at order entry, at approval, or at picking? Who owns the availability check? | Directly determines over-commitment risk and `BR-03` implementation | STK-04 | Blocker | `BR-03`, `FR-05` | Open |
| DQ-25 | Which availability figure should Sales see: on-hand, available (on-hand minus reservations), or available-to-promise including inbound? | Different figures produce different promises and disputes | STK-04, STK-02 | High | `FR-05` | Open |
| DQ-26 | Are partial deliveries, backorders, and over-deliveries allowed? Who approves them and what tolerance applies? | Affects fulfilment, billing, and customer expectation | STK-04, STK-06 | Blocker | `BR-05`, `FR-08` | Open |
| DQ-27 | Is picking guided by bin/location, and is barcode or handheld scanning used? | Determines warehouse usability, accuracy controls, and possible WMS need | STK-05 | Medium | `FR-08`, `DQ-53` | Open |
| DQ-28 | Which warehouse documents are used (pick list, packing list, delivery note) and who signs them? | Defines required outputs and evidence for delivery | STK-05, STK-06 | High | `FR-08`, `FR-09` | Open |

### 2.5 Logistics and delivery

| ID | Question | Why it matters | Owner | Priority | Trace | Status |
|---|---|---|---|---|---|---|
| DQ-29 | Is delivery performed by own fleet, third-party carriers, or both? Who arranges transport? | Determines delivery status ownership and integration needs | STK-06 | High | `Design.md §7` | Open |
| DQ-30 | What constitutes proof of delivery, and is it captured digitally (signature, photo, timestamp, receiver name)? | Proof of delivery is the usual billing trigger and the main dispute evidence | STK-06 | High | `FR-09`, `DQ-33` | Open |
| DQ-31 | Which delivery statuses must be visible to Sales and to customers, and who answers "where is my order"? | Defines status model and `KPI-02` measurement | STK-06 | Medium | `PP-04`, `FR-14` | Open |
| DQ-32 | Who bears responsibility and cost for short delivery, damage, or wrong-item delivery? | Determines exception workflow and return handling requirements | STK-06, STK-08 | Medium | `Design.md §8` | Open |

### 2.6 Billing and invoicing

| ID | Question | Why it matters | Owner | Priority | Trace | Status |
|---|---|---|---|---|---|---|
| DQ-33 | What event authorises invoice creation: order confirmation, shipment, delivery confirmation, or proof of delivery? | Sets revenue timing, credit exposure, and the whole To-Be flow | STK-08 | Blocker | `BR-06`, `FR-10`, `KPI-04` | Open |
| DQ-34 | Is invoicing per delivery or consolidated per period/customer? Are proforma or down-payment invoices used? | Changes invoice structure, matching, and AR ageing | STK-08 | Blocker | `FR-10`, `FR-11` | Open |
| DQ-35 | How are price, discount, and tax validated before an invoice is released? | Prevents invoice errors and disputes; defines control point | STK-08 | High | `FR-10`, `BR-06` | Open |
| DQ-36 | Who may create, correct, and cancel an invoice, and what approval is required? | Core segregation-of-duties and audit question | STK-08 | High | `FR-15`, `DQ-50` | Open |
| DQ-37 | Are e-invoices or tax invoices mandated in the operating country, and through which channel or provider? | A statutory requirement discovered late can delay go-live | STK-08 | Blocker | `AS-09`, `RSK-13` | Open |
| DQ-38 | How are invoice corrections tracked today (credit note, debit note, re-issue), and who approves them? | Affects AR reconciliation and audit trail | STK-08 | High | `FR-11`, `DQ-22` | Open |

### 2.7 Accounts receivable and payments

| ID | Question | Why it matters | Owner | Priority | Trace | Status |
|---|---|---|---|---|---|---|
| DQ-39 | Which payment methods and channels are used: bank transfer, virtual account, cheque, cash, offset? | Determines cash application design and reconciliation rules | STK-07 | High | `FR-12` | Open |
| DQ-40 | Which bank statement formats are available, and can they be imported or must they be keyed? | Determines the level of automation possible in cash application | STK-07, STK-10 | High | `FR-12`, `RSK-07` | Open |
| DQ-41 | How is a payment matched to invoices today, and what tolerance applies to short payment, overpayment, and deductions? | Matching rules define the difference between automated and manual AR | STK-07 | Blocker | `BR-07`, `FR-12`, `CG-06` | Open |
| DQ-42 | Who approves write-offs, adjustments, and the resolution of unapplied cash? | Financial control and audit requirement | STK-08 | High | `CG-06` | Open |
| DQ-43 | What are the standard payment terms, and how is collection escalation (reminder, hold, legal) handled? | Drives due-date logic, credit blocks, and `KPI-06` | STK-08 | High | `FR-11` | Open |
| DQ-44 | Which ageing buckets and reporting cut-off rules are used for receivables? | Two ageing reports with different rules destroy trust in reporting | STK-08 | Medium | `NFR-08`, `KPI-05` | Open |

### 2.8 Credit and approval

| ID | Question | Why it matters | Owner | Priority | Trace | Status |
|---|---|---|---|---|---|---|
| DQ-45 | Are credit limits and credit blocks used today? Who sets, changes, and releases them? | Credit control is the main commercial risk control in wholesale | STK-08 | Blocker | `BR-04`, `FR-06` | Open |
| DQ-46 | What order approval thresholds exist: value, margin, customer risk, payment status, override? | Defines the approval engine and who is accountable | STK-02, STK-08 | Blocker | `FR-06`, `BR-04` | Open |
| DQ-47 | Is approval routed by role, amount band, or a delegation-of-authority matrix, and what happens during absence? | Determines workflow design and prevents process stalls | STK-02 | High | `FR-06` | Open |
| DQ-48 | What must be recorded as approval evidence for audit: approver, timestamp, reason, attachment? | `FR-15` acceptance depends on this | STK-08 | High | `CG-02`, `FR-15` | Open |

### 2.9 Roles, authorisation, and audit

| ID | Question | Why it matters | Owner | Priority | Trace | Status |
|---|---|---|---|---|---|---|
| DQ-49 | Which job roles exist, how many users per role, and where are they located? | Confirms the stakeholder register and drives role design | STK-09, STK-10 | High | `STK-01`–`STK-11` | Open |
| DQ-50 | Which segregation-of-duties conflicts must be prevented (e.g. create invoice vs record payment, create customer vs release credit block)? | Prevents a design that is functionally correct but financially unsafe | STK-08 | High | `RSK-16`, `NFR-01` | Open |
| DQ-51 | What are the audit-trail retention period and access-review requirements? | Affects data model, archiving, and `NFR-02` compliance | STK-08, STK-10 | High | `FR-15`, `NFR-02` | Open |
| DQ-52 | Is single sign-on or corporate identity management required, and are there remote-access rules? | Determines integration and access design | STK-10 | Medium | `NFR-01`, `NFR-03` | Open |

### 2.10 Integration and reporting

| ID | Question | Why it matters | Owner | Priority | Trace | Status |
|---|---|---|---|---|---|---|
| DQ-53 | Which external systems must exchange O2C data: CRM, WMS, carrier, bank, tax/e-invoice, reporting? | Integration scope discovered late is the most common cause of go-live delay | STK-10 | Blocker | `RSK-07`, `Design.md §7` | Open |
| DQ-54 | Which reports and dashboards are mandatory at go-live, and who consumes each? | Prevents building reports nobody uses and missing the ones that block sign-off | STK-09 | High | `FR-14` | Open |
| DQ-55 | Is reporting delivered by ERP standard reports, a data warehouse, or a BI tool? | Determines data model, extract design, and KPI consistency | STK-09, STK-10 | High | `NFR-08`, `CG-07` | Open |
| DQ-56 | Do key customers require EDI, a customer portal, or specific document formats? | External dependency with long lead times | STK-02 | Medium | `AS-16` | Open |

### 2.11 Data migration and cutover

| ID | Question | Why it matters | Owner | Priority | Trace | Status |
|---|---|---|---|---|---|---|
| DQ-57 | Which data objects and volumes must be migrated: customers, products, warehouses, open orders, open deliveries, open invoices, open payments, balances? | Defines migration scope, effort, and reconciliation effort | STK-08, STK-03 | Blocker | `PRD §9.3` | Open |
| DQ-58 | How much historical data must remain accessible after go-live, and for how long? | Affects archiving strategy and system sizing | STK-08 | High | `DQ-51` | Open |
| DQ-59 | What is the acceptable business downtime and preferred go-live window (avoid month-end, quarter-end, peak season)? | Cutover feasibility and business risk | STK-09, STK-10 | High | `Design.md §10` | Open |
| DQ-60 | Who signs off data reconciliation and overall business readiness before go-live? | Without a named signatory, go/no-go has no authority | STK-09 | High | `RSK-08` | Open |

### 2.12 ERP selection and validation

| ID | Question | Why it matters | Owner | Priority | Trace | Status |
|---|---|---|---|---|---|---|
| DQ-61 | Which ERP product or products are being evaluated, and has a vendor been selected? | Fit-gap classifications cannot be finalised without a product | STK-09, STK-10 | Blocker | `PRD §8`, `RSK-06` | Open |
| DQ-62 | Which modules and licence tiers are budgeted, and are any capabilities excluded by licensing? | A "fit" that is not licensed is a gap | STK-09 | High | `RSK-06` | Open |
| DQ-63 | Which product version and official documentation will be used to validate each fit-gap classification? | Vendor-specific claims are unverifiable without a cited source | STK-10 | High | `PRD §8` | Open |

---

## 3. Mapping from `Design.md` §12 open questions

The baseline design already listed eight open questions. They are carried forward here rather than left in two places.

| Design.md open question | Discovery ID | Priority |
|---|---|---|
| Q1 ERP product and version | DQ-61, DQ-63 | Blocker |
| Q2 Separate Warehouse Management System | DQ-27, DQ-53 | Medium / Blocker |
| Q3 Approved order and credit approval rules | DQ-45, DQ-46, DQ-47 | Blocker |
| Q4 Event that triggers invoice creation | DQ-33 | Blocker |
| Q5 Returns, credit notes, cancellations | DQ-22 | Blocker |
| Q6 Payment channels and bank statement formats | DQ-39, DQ-40 | High |
| Q7 Reporting platform | DQ-55 | High |
| Q8 Data retention and audit policies | DQ-51, DQ-58 | High |

---

## 4. Blocker shortlist

Seventeen items block progress. If this were a real engagement, the first workshop would address only these, because every other design conversation can drift without them.

| ID | Question in one line | Blocks |
|---|---|---|
| DQ-01 | Entities, regions, currency | Master data, tax, reporting design |
| DQ-02 | Manufacture, import, or trade | Scope boundary |
| DQ-03 | O2C only or wider programme | Scope and sequencing |
| DQ-08 | Where master data lives today | Migration and data ownership |
| DQ-17 | Pricing basis | Billing and margin control |
| DQ-22 | Returns and credit notes | Process completeness |
| DQ-24 | Stock commitment policy | Availability and over-commitment control |
| DQ-26 | Partial delivery and tolerance | Fulfilment and billing |
| DQ-33 | Billing trigger | Revenue timing and whole To-Be flow |
| DQ-34 | Invoice per delivery or consolidated | Invoice and AR structure |
| DQ-37 | E-invoice mandate | Statutory compliance and integration |
| DQ-41 | Payment matching and tolerance | Cash application design |
| DQ-45 | Credit limits and blocks | Commercial risk control |
| DQ-46 | Approval thresholds | Workflow design |
| DQ-53 | External systems to integrate | Architecture and effort |
| DQ-57 | Migration scope and volumes | Migration plan |
| DQ-61 | ERP product selection | Fit-gap validity |

---

## 5. Answer log

To be completed as questions are answered. Format is fixed so answers are auditable.

| ID | Answer | Answered by | Date | Evidence / reference | Impact on documents |
|---|---|---|---|---|---|
| — | *No answers recorded. This is expected at the start of Phase 1.* | — | — | — | — |

---

## 6. Summary

| Dimension | Count |
|---|---|
| Total open items | 63 |
| Blocker | 17 |
| High | 38 |
| Medium | 8 |
| Low | 0 |
| Answered | 0 |

**Interpretation:** a design produced without these answers is a set of hypotheses. That is acceptable for a portfolio case study only if it is labelled as such — which is why every downstream document carries a status and each assumption is registered.
