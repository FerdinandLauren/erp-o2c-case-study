# Requirements Catalogue — Order-to-Cash

**Document ID:** REQ-01
**Version:** 0.1 (Draft)
**Phase:** 2 — Requirements and Process Design
**Sources:** `PRD.md` §5–§7; `docs/as-is-process.md`; `docs/to-be-process.md`; `docs/assumptions-risks.md` §5
**Status:** Draft — baseline requirements retained, new requirements proposed

---

## 1. Purpose

This catalogue turns the business problem into testable requirement statements. It covers three layers:

| Layer | Question it answers | Count |
|---|---|---|
| Functional requirements (FR) | What must the system do? | 23 |
| Acceptance criteria (AC) | How do we know it does it? | 17 (one or more per `Must` requirement) |
| Non-functional requirements (NFR) | How well must it do it? | 11 |
| Business rules (BR) | What must be true before an action is allowed? | 15 |

**Baseline discipline (DEC-20).** `FR-01`–`FR-15`, `NFR-01`–`NFR-08`, and `BR-01`–`BR-08` are carried over from `PRD.md` unchanged in wording, so traceability to the approved baseline survives. New items created from Phase 1 findings start at `FR-16`, `NFR-09`, and `BR-09`. IDs are never renumbered.

**Honesty note.** These requirements are written for a fictional scenario. They are *specific and testable*, which is what Gate 2 requires, but they are not *validated*: the policies behind them (thresholds, billing trigger, returns) remain business decisions tracked as `DQ-` items. Status columns say so.

---

## 2. Conventions

| Term | Meaning here |
|---|---|
| `shall` | Mandatory system behaviour |
| `Must` | Essential for go-live; without it the process cannot run |
| `Should` | Important; a documented workaround is acceptable temporarily |
| `Could` | Optional; not used in this catalogue |
| Stage | To-Be stage from `docs/to-be-process.md` §3 (S1–S10, R1–R5) |
| Status `Baseline` | Approved wording from `PRD.md` |
| Status `Proposed` | New requirement derived from Phase 1 findings; not yet approved |
| Status `To Validate` | Baseline rule whose policy content is still undecided |

Each requirement must answer four questions: **who** needs it, **what** changes, **why** it exists (traceable to a pain point or control gap), and **how** it will be verified.

---

## 3. Functional requirements

| ID | Requirement | Priority | Owner | Stage | Justification | Status |
|---|---|---|---|---|---|---|
| FR-01 | The system shall maintain customer master records with unique identifiers. | Must | Sales Ops | S1 | PP-01, CG-01 | Baseline |
| FR-02 | The system shall maintain product master records with unique identifiers, descriptions, and units of measure. | Must | Master Data | S1 | PP-01, CG-01 | Baseline |
| FR-03 | Authorized Sales users shall be able to create and update sales orders. | Must | Sales | S2 | PP-03 | Baseline |
| FR-04 | The system shall validate customer, product, quantity, and required order fields. | Must | Sales | S3 | PP-03, BR-02 | Baseline |
| FR-05 | The system shall provide inventory availability information during order processing. | Must | Inventory | S4 | PP-02 | Baseline |
| FR-06 | The system shall support configurable order approval rules. | Should | Sales Manager | S5 | CG-02, OBJ-02 | Baseline |
| FR-07 | The system shall generate fulfillment requirements from eligible confirmed orders. | Must | Warehouse | S6 | PP-03 | Baseline |
| FR-08 | The system shall record picked, packed, and shipped quantities. | Must | Warehouse | S6 | PP-03, PP-04 | Baseline |
| FR-09 | The system shall record delivery status and supporting evidence where applicable. | Should | Logistics | S7 | PP-04, CG-04 | Baseline |
| FR-10 | The system shall support customer invoice creation based on an approved billing trigger. | Must | Finance | S8 | PP-05, CG-05 | Baseline |
| FR-11 | The system shall track invoice statuses, including open, partially paid, and cleared. | Must | Finance | S8, S10 | PP-05, CG-06 | Baseline |
| FR-12 | The system shall support recording and allocation of incoming payments. | Must | Finance | S9 | CG-06 | Baseline |
| FR-13 | The system shall maintain traceability between sales orders, fulfillment, deliveries, invoices, and payments. | Must | ERP | S2–S10 | PP-04, CG-04 | Baseline |
| FR-14 | The system shall provide operational reports for orders, fulfillment, billing, and receivables. | Should | Management | S10 | PP-06, CG-07 | Baseline |
| FR-15 | The system shall maintain transaction history for audit purposes. | Must | ERP / Audit | All | CG-08, CG-02 | Baseline |
| FR-16 | The system shall support customer returns and credit notes linked to the original delivery and invoice, with an authorisation step separate from the request. | Must | Finance | R1–R5 | DQ-22, EX-08 | Proposed |
| FR-17 | The system shall evaluate customer credit exposure at order confirmation and block or route orders that exceed the approved credit limit. | Must | Finance Manager | S5 | DQ-45, BR-09 | Proposed |
| FR-18 | The system shall record outstanding order quantities and support backorder release when stock becomes available. | Should | Warehouse | S6–S7 | DQ-26, PP-02 | Proposed |
| FR-19 | The system shall determine order and invoice prices from maintained price conditions and record any manual override with approval and reason. | Must | Sales | S2, S8 | DQ-17, PP-05 | Proposed |
| FR-20 | The system shall expose a defined lifecycle status for orders, deliveries, invoices, payments, and returns, and permit only valid transitions. | Must | Sales Ops | All | PP-04, CG-04 | Proposed |
| FR-21 | The system shall record order and delivery exceptions with reason code, owner, timestamp, and resolution. | Should | Sales Ops | S3–S10 | KPI-07, CG-07 | Proposed |
| FR-22 | The system shall store and output statutory invoice data determined by Finance-configured rules. | Must | Finance | S8 | DQ-37 | Proposed |
| FR-23 | The system shall detect potential duplicate customer and product records at entry using defined matching attributes. | Should | Master Data | S1 | PP-01, CG-01 | Proposed |

**Priority totals:** 17 `Must`, 6 `Should`.

---

## 4. Acceptance criteria

Written as Given/When/Then so Phase 4 can convert them directly into UAT scenarios. `Normal` = expected path, `Edge` = boundary condition, `Negative` = must be refused.

| ID | FR | Type | Given | When | Then |
|---|---|---|---|---|---|
| AC-01 | FR-01 | Negative | A customer record already exists with a tax ID | A user creates a customer with the same tax ID | Creation is blocked, the possible duplicate is shown, and no second record is saved |
| AC-02 | FR-02 | Negative | A product exists with code `P-100` and unit `CTN` | A user creates another product with code `P-100` | Creation is blocked and the conflicting product is named |
| AC-03 | FR-03 | Normal | An authorised Sales user is signed in | They create and update an order | Only permitted fields change, and each change writes an audit entry |
| AC-04 | FR-03 | Negative | A user without the Sales order role is signed in | They attempt to create an order | The action is denied and a security event is recorded |
| AC-05 | FR-04 | Negative | An order is in `Draft` | The user confirms it with a missing mandatory field or inactive product | Confirmation is blocked, the field is named, and the order stays `Draft` |
| AC-06 | FR-05 | Normal | Stock on hand is 100 and 30 are reserved | A user enters an order line for 80 | Available-to-promise shows 70 and the order cannot commit 80 without an exception |
| AC-07 | FR-07 | Negative | An order has been released to the warehouse | A user attempts to release it a second time | No second fulfilment requirement is created and the attempt is logged |
| AC-08 | FR-08 | Edge | An order line is for 10 units with 0% over-delivery tolerance | The operator records 12 picked | The system rejects the excess unless an approved tolerance or reason is recorded |
| AC-09 | FR-10 | Normal | A delivery is `POD Confirmed` | The billing run processes that delivery | Exactly one invoice is created, referencing the delivery and order |
| AC-10 | FR-10 | Negative | An invoice already exists for a delivery | The billing run processes the same delivery again | No second invoice is created and the duplicate attempt is logged |
| AC-11 | FR-11 | Normal | An invoice for 1,000 is `Open` | A payment of 400 is allocated to it | Status becomes `Partially Paid`; after 600 more, status becomes `Cleared` |
| AC-12 | FR-12 | Edge | A payment of 1,000 is recorded | The user allocates 600 to one invoice and 300 to another | The payment stays `Partially Allocated` and 100 remains visible as unapplied cash |
| AC-13 | FR-12 | Negative | A payment of 1,000 is recorded | A user attempts to allocate 1,100 | Allocation is rejected because it exceeds the payment amount |
| AC-14 | FR-13 | Normal | An order has a delivery, invoice, and payment | A user traces the order | All child documents are reachable in both directions and no orphan document exists |
| AC-15 | FR-15 | Normal | Any transaction has been created or changed | An auditor opens its history | User, timestamp, action, and reason (where required) are visible and cannot be edited |
| AC-16 | FR-16 | Negative | A return is requested | The same user attempts to authorise it | Authorisation is refused; a different authorised role must decide |
| AC-17 | FR-16 | Edge | Goods are returned against a valid delivery and invoice | The goods are received and accepted | A credit note is created, referencing both, and the receivable balance reduces |
| AC-18 | FR-17 | Negative | A customer's exposure exceeds the approved credit limit | A Sales user confirms an order | The order is blocked or routed to Finance; release requires a Finance decision with reason and timestamp |
| AC-19 | FR-19 | Edge | A product has a maintained price of 100 | A user overrides the price to 90 without approval | The override is refused until an authorised approver records a reason |
| AC-20 | FR-20 | Negative | An order is `Closed` | A user attempts to move it back to `Released` | The transition is refused and the attempt is logged |
| AC-21 | FR-22 | Normal | Statutory invoice configuration is complete | An invoice is posted and printed | Tax identifier, tax amount, invoice number, and invoice date are present and reconcile |
| AC-22 | FR-22 | Negative | Statutory invoice configuration is missing a required element | A user attempts to post an invoice | Posting is blocked and the missing configuration is named |

22 acceptance criteria for 17 `Must` requirements; four requirements carry two criteria because they have a normal and a refusal path.

---

## 5. Non-functional requirements

| ID | Requirement | Priority | Verification | Target |
|---|---|---|---|---|
| NFR-01 | Access to business information shall follow role-based authorization. | Must | Role matrix review + negative access tests | No role can exceed its defined scope |
| NFR-02 | Important transaction changes shall record user identity and timestamps. | Must | Audit history inspection | 100% of state changes traced |
| NFR-03 | Data in transit shall use organization-approved secure communication. | Must | Architecture review | Approved protocol only |
| NFR-04 | Master data ownership shall be documented. | Must | Data catalogue review | One named owner per entity |
| NFR-05 | Backup and recovery requirements shall be defined. | Should | Recovery plan review | RPO/RTO agreed with IT (`DQ-51`, `DQ-59`) |
| NFR-06 | Availability and response-time targets shall be agreed during technical discovery. | Should | NFR agreement record | Targets set with vendor and IT |
| NFR-07 | User-facing statuses and validation messages shall use consistent business terminology. | Should | Terminology review + UAT observation | One term per concept across screens and reports |
| NFR-08 | Reports shall use consistent KPI definitions and reporting cut-off rules. | Should | Report reconciliation test | Two reports for the same period agree |
| NFR-09 | Transaction and audit data shall be retained and retrievable for the approved retention period. | Should | Retention policy review + archive retrieval test | Period defined by policy (`DQ-51`, `DQ-58`) |
| NFR-10 | The solution shall support the confirmed transaction and data volumes without redesign. | Should | Volume model review with vendor | Volumes from `DQ-05`; promote to `Must` once known |
| NFR-11 | Audit and transaction data shall be exportable in a structured format for internal and external audit. | Should | Export a sample period and reconcile totals | Export reconciles to source |

**Priority totals:** 4 `Must`, 7 `Should`.

---

## 6. Business rules

Business rules state what must be true before an action is allowed. They are enforced at a named point, not left to user discipline.

| ID | Rule | Type | Source | Enforced at | Status |
|---|---|---|---|---|---|
| BR-01 | Every customer and product must have a unique master-data identifier. | Constraint | PRD §7 | S1 | Proposed |
| BR-02 | Invalid mandatory customer or product information shall prevent order confirmation. | Constraint | PRD §7 | S3 | Proposed |
| BR-03 | Inventory availability must be checked before confirming a delivery commitment. | Constraint | PRD §7 | S4 | Proposed |
| BR-04 | Order approval thresholds must follow an authorized business policy. | Policy | PRD §7 | S5 | To Validate |
| BR-05 | Fulfillment quantities must follow approved partial or over-delivery rules. | Policy | PRD §7 | S6 | To Validate |
| BR-06 | Invoice creation must follow an approved billing trigger. | Policy | PRD §7 | S8 | To Validate |
| BR-07 | Payment allocation must remain traceable to the relevant customer and invoice. | Constraint | PRD §7 | S9 | Proposed |
| BR-08 | Users may only access transactions permitted by their assigned roles. | Constraint | PRD §7 | All | Proposed |
| BR-09 | An order that exceeds the approved credit limit is blocked from release until Finance releases it with a reason and timestamp. | Policy | DEC-14, DQ-45 | S5 | To Validate |
| BR-10 | An invoice cannot be created before delivery is confirmed by proof of delivery; no proof, no invoice. | Policy | DEC-11, DQ-33 | S8 | To Validate |
| BR-11 | A manual price override above the agreed tolerance requires Sales Manager approval and a recorded reason. | Policy | DQ-17 | S2, S8 | To Validate |
| BR-12 | A return must reference the original delivery and invoice and carry a reason code; the requester cannot authorise it. | Constraint | DEC-16, DQ-22 | R1–R4 | Proposed |
| BR-13 | Master data is deactivated, never deleted; change history is retained for audit. | Constraint | NFR-02, FR-15 | S1 | Proposed |
| BR-14 | Over-delivery is not permitted without approval; partial delivery is permitted and leaves the remainder open. | Policy | DEC-15, DQ-26 | S6 | To Validate |
| BR-15 | A posted invoice is never edited or deleted; corrections use credit or debit notes that reference the original. | Constraint | DEC-11, DQ-38 | S8, R4 | Proposed |

**Status totals:** 8 `Proposed`, 7 `To Validate`. None is presented as an approved company policy.

### 6.1 Rules that will change if a blocker is answered differently

| Rule | If the answer changes | Consequence |
|---|---|---|
| BR-10 | Billing trigger is shipment or order confirmation (DQ-33) | Invoice creation step moves earlier; POD becomes a control, not a precondition |
| BR-09 | No credit limits are used (DQ-45) | Approval band C loses its trigger; risk control drops to payment terms only |
| BR-14 | Business allows over-delivery within a tolerance (DQ-26) | Quantity validation becomes a tolerance check; invoice quantity rules change |
| BR-12 | Returns are out of wave 1 (DQ-22) | R1–R5 and FR-16 are deferred; the design must still explain what happens to damaged goods |

---

## 7. Requirement quality review (Gate 2 evidence)

| Gate 2 criterion (`Plan.md` §6) | Evidence | Status |
|---|---|---|
| Requirement IDs are unique | FR-01…FR-23, NFR-01…NFR-11, BR-01…BR-15; no duplicates; automated range check passed | Met |
| Requirements are specific and testable | Each `Must` FR has Given/When/Then acceptance criteria (§4) | Met for 17/17 `Must` |
| Business rules have a documented source or are marked proposed | Every BR carries Source and Status; 7 marked `To Validate` | Met |
| Requirements have clear ownership | Owner column populated for all 23 FR | Met |
| Every `Must` requirement has an acceptance approach | AC-01…AC-22 cover all `Must` FRs | Met |
| Requirements traceable to a business problem | Justification column references PP-, CG-, or DQ-; full matrix in `docs/traceability-matrix.md` | Met |

**Known weaknesses, stated rather than hidden:**

1. `Should` requirements (FR-06, FR-09, FR-14, FR-18, FR-21, FR-23) have no acceptance criteria yet. They get UAT coverage in Phase 4 on a best-effort basis.
2. Measurable NFR targets are absent for availability, response time, and retention, because the input values are unknown (`DQ-05`, `DQ-51`, `DQ-59`). Writing a number would be invention.
3. BR-04, BR-05, BR-06, BR-09, BR-10, BR-11, BR-14 are policy placeholders. The design is executable, but the *business* content is not decided.

---

## 8. Requirement change control

| Situation | Rule |
|---|---|
| Baseline wording must change | Do not edit `FR-01`–`FR-15`; raise a new requirement and mark the old one `Superseded by FR-xx` |
| New need discovered | Add the next free ID in the correct layer; do not reuse withdrawn IDs |
| Requirement withdrawn | Keep the row, set status `Withdrawn`, record reason and date |
| Priority changes | Record in the change log below; do not silently reorder |
| Ambiguity found in UAT | Return to this document first, then to design; do not fix it only in a test script |

### 8.1 Change log

| Version | Date | Change |
|---|---|---|
| 0.1 | 12 Nov 2026 | Baseline FR/NFR/BR carried forward; FR-16…FR-23, NFR-09…NFR-11, BR-09…BR-15 added; 22 acceptance criteria written |
