# Stakeholder Analysis

**Document ID:** SA-01
**Version:** 0.1 (Draft)
**Phase:** 1 — Business Discovery
**Source documents:** `PRD.md` §2, §1.5; `Design.md` §5, §8
**Status:** Draft — roles derived from the PRD; organisational structure unconfirmed (`DQ-49`)

---

## 1. Purpose

Identify everyone who can affect or is affected by the O2C implementation, what each person needs from the system, and who decides what. This drives:

- Process ownership in the To-Be design (`Design.md` §2.1).
- The role and authorisation matrix (Phase 3).
- UAT participant selection (Phase 4).
- Change management and training (Phase 5).

Weak stakeholder analysis is the most common reason a functionally correct ERP design is rejected in practice: the design is right, but the people who must live with it were never enrolled.

---

## 2. Stakeholder register

Influence and interest are assessed for **this project**, not for the company hierarchy (which is unknown).

| ID | Stakeholder | Function | Role in O2C | Primary responsibility | Main concern | Influence | Interest |
|---|---|---|---|---|---|---|---|
| STK-01 | Sales Representative | Sales | Order capture | Create and maintain customer orders | Accurate, fast order entry without extra steps | Low | High |
| STK-02 | Sales Manager | Sales | Commercial control | Review and approve exceptions | Commercial control, margin, no lost sales | High | High |
| STK-03 | Master Data / Sales Ops | Master Data | Master data owner | Maintain valid customer and product records | Data quality, no duplicate records | Medium | High |
| STK-04 | Inventory Controller | Inventory | Availability owner | Maintain inventory accuracy; commit stock | Stock accuracy and availability commitments | Medium | High |
| STK-05 | Warehouse Operator | Warehouse | Fulfilment | Pick and pack products | Clear task list, workable quantities, no rework | Low | High |
| STK-06 | Logistics Coordinator | Logistics | Delivery execution | Manage shipment and delivery | Delivery visibility, proof of delivery | Medium | High |
| STK-07 | Finance / AR Officer | Finance | Billing and cash | Create invoices; record and allocate payments | Financial accuracy, few unmatched payments | Medium | High |
| STK-08 | Finance Manager | Finance | Financial control | Monitor receivables; approve financial exceptions | Control, cash flow, audit readiness | High | High |
| STK-09 | Management (project sponsor) | Executive | Direction and funding | Approve scope, priorities, go/no-go | Business outcome, cost, disruption risk | High | Medium |
| STK-10 | IT / Integration Team | IT | Technical operations | Support access, integrations, environment | Security, reliability, supportability | High | Low–Medium |
| STK-11 | ERP Functional Consultant | Project | Design authority | Translate requirements into functional design | Defensible, implementable design | Medium | High |

### 2.1 Notes on the register

- `STK-09` is shown as sponsor because a project of this scope requires a funding and go/no-go authority (`Design.md §10.2`). The PRD lists "Management" as a consumer of reporting, not explicitly as sponsor — sponsor assignment is an assumption (`AS-13`).
- `STK-11` is the author role for this case study; in a real programme it is a delivery role, not a business stakeholder. It is retained so decision ownership stays explicit.
- No external stakeholders are identified in the baseline documents. Customers, carriers, and banks interact with the process but are not project participants; if a customer portal or EDI is in scope, external representation becomes necessary (`DQ-56`).
- An ERP Administrator appears in `Design.md §5` but not in `PRD.md §2`. It is treated as an IT sub-role (`STK-10`) until `DQ-49` confirms the organisation.

---

## 3. RACI — To-Be O2C stages

Stages follow `PRD.md` §4 (ten To-Be steps). `Design.md §2.1` groups steps 2–3 (order creation and validation) and steps 6–7 (fulfilment and shipment) into single stages; the finer PRD breakdown is used here to keep approval and delivery accountability visible.

Legend: **R** = Responsible (does the work), **A** = Accountable (owns the outcome, one per row), **C** = Consulted, **I** = Informed.

| # | To-Be stage | Sales Rep | Sales Mgr | Master Data | Inventory Ctrl | Warehouse Op | Logistics Coord | Finance/AR | Finance Mgr | Management | IT |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | Customer and product master data | C | I | **R/A** | C | I | I | C | I | I | I |
| 2 | Sales order creation | **R** | **A** | C | I | I | I | I | I | I | I |
| 3 | Order validation | **R** | **A** | C | I | I | I | I | I | I | I |
| 4 | Inventory availability check | C | I | I | **R/A** | I | I | I | I | I | I |
| 5 | Order approval (where required) | C | **R/A** | I | C | I | I | C | C | I | I |
| 6 | Warehouse fulfilment | I | I | I | **A** | **R** | I | I | I | I | I |
| 7 | Shipment and delivery confirmation | I | I | I | C | C | **R/A** | I | I | I | I |
| 8 | Customer invoicing | C | C | I | I | I | C | **R** | **A** | I | I |
| 9 | Incoming payment recording | I | I | I | I | I | I | **R** | **A** | I | C |
| 10 | Receivables monitoring and reporting | I | C | I | I | I | I | **R** | **A** | **R** | I |

### 3.1 RACI observations and tensions

1. **Stage 2 — Sales Rep is R, Sales Manager is A.** The representative enters the order, but accountability for commercial correctness sits with the manager. This is defensible only if the manager actually reviews orders; if not, accountability is fictional and should move to the Sales Rep (`DQ-46`).
2. **Stage 5 — Sales Manager is both R and A.** Acceptable for an approval step, but a second approver is required above a defined value threshold (`DQ-46`, `DQ-47`).
3. **Stage 6 — Warehouse Operator is R, Inventory Controller is A.** This deliberately separates physical execution from stock accountability. Collapsing both into one role would remove an inventory control.
4. **Stages 8–9 — Finance/AR is R, Finance Manager is A.** The officer creates invoices and applies cash; the manager owns the numbers. This creates a segregation-of-duties question: can the same officer both create an invoice and record the payment against it? (`DQ-50`).
5. **Management appears as R on stage 10** because reporting is a management activity, but the data is produced by Finance. In practice Finance owns report production and Management owns interpretation.
6. **Master Data has authority at stage 1 but is consulted elsewhere.** If master data is maintained inside Sales Ops, stage 1's A and stage 2's A are the same function — a control weakness (`DQ-08`, `DQ-14`).
7. **IT is Informed almost everywhere.** Correct for business process; IT must become Consulted for authorisation design and any integration point (`DQ-52`, `DQ-53`).

This RACI is **proposed**. It requires a segregation-of-duties review (`RSK-16`) and organisational confirmation (`DQ-49`).

---

## 4. Power / interest assessment

| Quadrant | Stakeholders | Engagement strategy |
|---|---|---|
| High influence, high interest — *manage closely* | STK-02 Sales Manager, STK-08 Finance Manager | Joint design decisions; sign-off on approval rules and billing trigger; early involvement in To-Be |
| High influence, lower interest — *keep satisfied* | STK-09 Management, STK-10 IT | Concise decision papers; clear scope and risk reporting; no surprises at go/no-go |
| Lower influence, high interest — *keep informed and involved* | STK-01 Sales Rep, STK-03 Master Data, STK-04 Inventory, STK-05 Warehouse, STK-06 Logistics, STK-07 AR Officer | Workshops, process walkthroughs, UAT participation, feedback loops on usability |
| Lower influence, lower interest — *monitor* | None identified | Reassess after `DQ-49` confirms the organisation |

**Design consequence:** the people with the least formal influence (order entry, picking, cash application) determine whether the system is actually used. Their concerns — extra keystrokes, unclear screens, conflicting priorities at peak — must be treated as design requirements, not training problems.

---

## 5. Engagement and decision plan

| Activity | Participants | Purpose | Timing | Output |
|---|---|---|---|---|
| Scope and sponsor alignment | STK-09, STK-11 | Confirm boundary, entities, wave 1 | Phase 1 | Approved scope, `DQ-01`–`DQ-03` closed |
| Process discovery workshop — Sales | STK-01, STK-02, STK-03 | As-Is orders, pricing, approval policy | Phase 1 | Validated As-Is, `DQ-16`–`DQ-22` |
| Process discovery workshop — Supply | STK-04, STK-05, STK-06 | Stock commitment, fulfilment, delivery evidence | Phase 1 | Validated As-Is, `DQ-23`–`DQ-32` |
| Process discovery workshop — Finance | STK-07, STK-08 | Billing trigger, cash application, receivables | Phase 1 | Validated As-Is, `DQ-33`–`DQ-44` |
| To-Be design review | STK-02, STK-04, STK-06, STK-08 | Agree target flow and exception handling | Phase 2 | Signed To-Be process |
| Fit-gap decision session | STK-02, STK-08, STK-09 | Decide extension vs process change vs deferral | Phase 3 | Disposition per gap |
| Authorisation review | STK-08, STK-10, STK-11 | Confirm roles and segregation of duties | Phase 3 | Approved role matrix |
| UAT execution | STK-01, STK-05, STK-07, STK-06 | Validate real business scenarios | Phase 4 | UAT results and defects |
| Go/no-go review | STK-09, STK-02, STK-08, STK-10 | Approve production opening | Phase 5 | Go/no-go decision record |

**Simulation note:** in this portfolio case, workshops are not actually held. The activities define what a real engagement would require; the answers they would produce are tracked as open questions in `docs/discovery-questions.md` and are **not** invented (`AS-01`, `AS-14`).

### 5.1 Decision rights

| Decision | Decides | Consulted | Informed |
|---|---|---|---|
| Project scope and wave 1 boundary | STK-09 | STK-02, STK-08, STK-11 | All |
| Order and credit approval policy | STK-08 + STK-02 (joint) | STK-11 | STK-09 |
| Billing trigger | STK-08 | STK-02, STK-06 | STK-09 |
| Master data ownership model | STK-09 | STK-03, STK-02, STK-08 | All |
| Role and authorisation design | STK-08 | STK-10, STK-11, process owners | All |
| Fit-gap disposition (extension vs standard) | STK-09 | STK-08, STK-02, STK-10 | All |
| UAT exit and go/no-go | STK-09 | STK-02, STK-08, STK-10 | All |

No single role may both design and approve a financial control (`Design.md §1`, principle 6).

---

## 6. Stakeholder risks

| Risk | Description | Affected stakeholder | Mitigation | Reference |
|---|---|---|---|---|
| Sponsorship drift | Management approves scope but disengages before go/no-go | STK-09 | Fixed decision calendar; short decision papers | `RSK-10` |
| Commercial resistance | Sales perceives approval steps and validation as lost selling time | STK-01, STK-02 | Design order entry for speed; exception-based approval only | `RSK-09` |
| Data ownership vacuum | Nobody accepts accountability for master data quality | STK-03, STK-02 | Sponsor assigns a single data owner | `RSK-02`, `DQ-14` |
| Warehouse workaround | Operators bypass the system during peak volume | STK-05 | Usability testing with operators; peak-season cutover avoidance | `RSK-09` |
| Finance distrust | AR rejects automatic allocation and reconciles in spreadsheets anyway | STK-07, STK-08 | Tolerance rules agreed in advance; visible matching evidence | `RSK-14`, `DQ-41` |
| IT capacity | Integration and access work is under-resourced | STK-10 | Early integration inventory, sized in Phase 3 | `RSK-07` |
| Unrealistic benefit expectation | Sponsor expects measurable ROI from a design-only case | STK-09 | Explicit statement that no baseline exists | `RSK-12` |

---

## 7. Open questions affecting stakeholder analysis

| Question | Impact |
|---|---|
| `DQ-01` — entities and regions in scope | Determines whether one or several process owners exist |
| `DQ-03` — P2P in the same programme | Adds Procurement as a stakeholder |
| `DQ-08` — current master data ownership | Resolves STK-03 authority at stage 1 |
| `DQ-14` — master data approval authority | Defines who signs off records |
| `DQ-45`–`DQ-48` — credit and approval policy | Defines Sales Manager and Finance Manager decision rights |
| `DQ-49` — job roles and user counts | Confirms the register and role mapping |
| `DQ-50` — segregation-of-duties conflicts | May force role redesign |
| `DQ-52` — identity and access management | Defines IT's involvement level |
| `DQ-60` — business readiness sign-off | Confirms who owns go/no-go |

---

## 8. Assumptions used in this document

| ID | Assumption |
|---|---|
| AS-13 | Management acts as project sponsor and go/no-go authority |
| AS-14 | Stakeholder roles are inferred from the PRD; no organisational chart is available |
| AS-15 | Master Data is a distinct responsibility, not necessarily a distinct department |
| AS-16 | No external stakeholder (customer, carrier, bank) participates in design workshops |
