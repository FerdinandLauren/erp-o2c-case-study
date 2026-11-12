# Assumptions, Risks, Dependencies, and Decisions

**Document ID:** ARR-01
**Version:** 0.1 (Draft)
**Phase:** 1 — Business Discovery
**Source documents:** `PRD.md` §1.5, §12; `Plan.md` §6–§7; `Design.md` §1, §12
**Status:** Draft — registers open; reviewed at every phase gate

---

## 1. Purpose

Three things can quietly destroy an ERP design: an assumption nobody wrote down, a risk nobody owns, and a decision nobody remembers making. This document keeps all three visible.

Registers:

| Register | Prefix | Purpose |
|---|---|---|
| Assumptions | `AS-` | Things treated as true in order to proceed |
| Risks | `RSK-` | Things that may prevent the project delivering its design |
| Dependencies | `DEP-` | Things the project needs from others |
| Decisions | `DEC-` | Choices already made, with rationale |
| Issues | `ISS-` | Problems that are happening now (none yet) |

Review cadence: at each phase gate (`Plan.md §6`). Any register item that changes after a gate is versioned with a note in §8.

---

## 2. Assumption register

An assumption is not a fact. It is a placeholder that must be either validated or replaced by an answer to a `DQ-` item. Each assumption states what happens if it is wrong.

| ID | Assumption | Basis | If wrong | Validate via | Status |
|---|---|---|---|---|---|
| AS-01 | The company is fictional and all data in this repository is synthetic. | PRD §12 | The entire case study becomes a misrepresentation of a real company | n/a | Accepted |
| AS-02 | Wave 1 covers a single legal entity. | Simplification for a portfolio case | Multi-entity design changes master data, tax, and consolidation | DQ-01 | Open |
| AS-03 | A single functional currency is used for design purposes. | Simplification; no currency requirement stated | Multi-currency adds rate tables, revaluation, and reporting complexity | DQ-01 | Open |
| AS-04 | No integrated ERP exists at project start; records are spreadsheet and email based. | PRD §1.2 | An existing system changes migration, integration, and change effort substantially | DQ-08, DQ-53 | Open |
| AS-05 | The company buys and resells; manufacturing and import are outside the O2C boundary. | Most likely reading of PRD §1.2 | Production orders, landed cost, customs, and batch control enter scope | DQ-02 | Open |
| AS-06 | Customers are business buyers only; there is no direct-to-consumer channel. | PRD §1.2 | Consumer channel adds returns, payments, and fulfilment complexity | DQ-06 | Open |
| AS-07 | Products are non-serialised, and batch/expiry tracking is not assumed. | Nothing in the PRD states otherwise | Batch/expiry changes warehouse, delivery, recall, and costing design | DQ-13 | Open |
| AS-08 | Procurement (P2P) is outside wave 1. | Scope statement in PRD §1.5 | Shared stock and supplier model, plus sequencing risk | DQ-03 | Open |
| AS-09 | Tax and e-invoicing follow statutory rules validated by Finance; no tax advice is given here. | PRD §1.5 exclusion | A mandatory e-invoice requirement discovered late can block go-live | DQ-37 | Open |
| AS-10 | Standard ERP capabilities exist for the core O2C functions listed in `Design.md` §3. | Vendor-neutral design assumption | Requirements may become extension or process change, altering cost and risk | DQ-61, DQ-63 | Open |
| AS-11 | Phase durations in `Plan.md` are portfolio estimates, not a resourced project schedule. | Plan.md §1 | Stakeholders may treat the plan as a committed delivery date | n/a | Accepted |
| AS-12 | Documentation is written in English; the business context is Indonesian. | Portfolio presentation choice | A bilingual requirement would double document maintenance | DQ-49 | Open |
| AS-13 | Management acts as project sponsor and holds go/no-go authority. | Required for any implementation; not stated in PRD | No funding authority or escalation path exists | DQ-60 | Open |
| AS-14 | Stakeholder roles are inferred from the PRD; no organisation chart is available. | CON-01 | Register, RACI, and role design may not match the real organisation | DQ-49 | Open |
| AS-15 | Master Data is a responsibility that may sit inside Sales Ops rather than a separate department. | Design.md §6.1 vs PRD FR-01/FR-02 ambiguity | Conflicting data ownership; no single accountable data owner | DQ-08, DQ-14 | Open |
| AS-16 | No external stakeholder (customer, carrier, bank) participates in design or UAT. | Fictional case; no real counterparties | External requirements (EDI, portal, bank formats) surface late | DQ-56 | Open |
| AS-17 | Approval thresholds and credit policy are business decisions to be set by Finance and Sales, not designed by the consultant. | PRD §7 status "To Validate"; Design principle 7 | A consultant-invented policy could be treated as approved practice | DQ-45, DQ-46 | Open |
| AS-18 | No KPI baseline exists, and no benefit quantification is claimed. | No live system or historical extract exists | Unsupported improvement claims would undermine the case study's credibility | §8 in `as-is-process.md` | Accepted |

### 2.1 Assumptions that would materially change the design if wrong

Not all assumptions carry equal weight. These five are structural: if one is wrong, sections of the design must be rewritten, not adjusted.

| Assumption | What would have to be rewritten |
|---|---|
| AS-05 — buy-and-resell only | Process boundary, stock model, cost flow, batch handling, To-Be flow |
| AS-08 — P2P out of scope | Programme sequencing, shared master data, integration architecture |
| AS-02 / AS-03 — single entity, single currency | Data model, tax determination, reporting, authorisation |
| AS-10 — standard capability exists | Fit-gap classifications, effort estimate, extension backlog |
| AS-17 — policy set by business | Approval design and `BR-04` implementation |

---

## 3. Risk register

Scoring: Likelihood (L) and Impact (I) each 1–5. Score = L × I. Rating: **High** ≥ 15, **Medium** 8–14, **Low** ≤ 7. Response types: Avoid, Mitigate, Transfer, Accept.

| ID | Risk | Cause / trigger | L | I | Score | Rating | Response | Mitigation | Owner | Early warning |
|---|---|---|---|---|---|---|---|---|---|---|
| RSK-01 | Incomplete or conflicting business requirements | Discovery limited to documents; no process owners available (`AS-14`) | 4 | 4 | 16 | High | Mitigate | Maintain the DQ register; gate each phase on blocker answers; label all unvalidated design | Functional Consultant | Blocker questions still open at phase gate |
| RSK-02 | Poor customer master data quality | Duplicate and inconsistent records across spreadsheets | 4 | 4 | 16 | High | Mitigate | Profile and cleanse before migration; define a single data owner; enforce unique IDs | Master Data Owner | Duplicate rate above agreed threshold at profiling |
| RSK-03 | Poor product master data quality | Multiple product records, inconsistent units of measure | 3 | 4 | 12 | Medium | Mitigate | Product cleansing sprint; UoM standardisation; freeze before cutover | Master Data Owner | Conflicting UoM found during mapping |
| RSK-04 | Inconsistent or undocumented approval policy | Policy exists only in practice (`AS-17`) | 4 | 3 | 12 | Medium | Mitigate | Obtain written policy and delegation of authority before workflow design | Finance Manager | Approval rules change during design |
| RSK-05 | Unclear billing trigger | Multiple practices for invoice timing | 3 | 4 | 12 | Medium | Mitigate | Force a documented decision (`DQ-33`); design for one trigger, not several | Finance Manager | Invoice timing differs by customer or officer |
| RSK-06 | ERP capability assumption proves wrong | Design is vendor-neutral; no product validated (`AS-10`) | 4 | 3 | 12 | Medium | Mitigate | Re-validate every `Fit` against product version and documentation (`DQ-63`) | Functional Consultant | A "fit" needs configuration or extension |
| RSK-07 | Integration dependencies discovered late | Interface inventory not agreed with IT (`DQ-53`) | 4 | 4 | 16 | High | Mitigate | Complete integration inventory in Phase 3; assign interface owners early | IT / Integration Lead | New system appears during design |
| RSK-08 | Case study misread as a real implementation | Portfolio is presented publicly; documents use implementation language | 3 | 5 | 15 | High | Avoid | Prominent disclaimer in README and every document; no fabricated metrics or go-live claims | Author | Reader asks "which client was this?" |
| RSK-09 | Low user adoption and shadow processes | Extra discipline without visible benefit to users | 4 | 4 | 16 | High | Mitigate | Involve users in design; exception-based approvals; usability testing; training before go-live | Change Lead | Users keep parallel spreadsheets |
| RSK-10 | Scope creep beyond O2C | Adjacent process gaps surface during discovery | 3 | 3 | 9 | Medium | Mitigate | Written scope boundary; change control; park out-of-scope items in a backlog | Sponsor | New process areas added to workshops |
| RSK-11 | Single-author knowledge concentration | No team; one person holds the whole design | 3 | 3 | 9 | Medium | Mitigate | Written rationale for every design decision (`DEC-`); documentation over verbal reasoning | Author | Decisions cannot be explained from documents |
| RSK-12 | No baseline means no measurable benefit | No historical extract or live system (`AS-18`) | 5 | 3 | 15 | High | Accept | State the limitation explicitly; define baseline measurement as a project task, not a claim | Sponsor | Documents imply improvement without data |
| RSK-13 | Statutory e-invoicing or tax requirement discovered late | Not addressed in baseline documents | 3 | 5 | 15 | High | Mitigate | Raise `DQ-37` as a blocker; require Finance confirmation before Phase 3 | Finance Manager | Tax authority format mentioned in passing |
| RSK-14 | Parallel data maintenance during transition | Users do not trust the new system early | 4 | 3 | 12 | Medium | Mitigate | Reconciliation reports during hypercare; visible matching evidence; retire legacy access formally | Finance Manager | Two versions of the same order exist |
| RSK-15 | Key-person dependency in reconciliation | One clerk knows how payments are matched | 3 | 4 | 12 | Medium | Mitigate | Document matching rules; cross-train; embed rules in the system | Finance Manager | Only one person can explain an allocation |
| RSK-16 | Segregation-of-duties conflict in role design | Small teams; one person performs several duties | 3 | 4 | 12 | Medium | Mitigate | Explicit conflict matrix; compensating controls where separation is impossible | Finance Manager | Same user creates and clears an invoice |

### 3.1 Risk summary

| Rating | Count | IDs |
|---|---|---|
| High | 7 | RSK-01, RSK-02, RSK-07, RSK-08, RSK-09, RSK-12, RSK-13 |
| Medium | 9 | RSK-03, RSK-04, RSK-05, RSK-06, RSK-10, RSK-11, RSK-14, RSK-15, RSK-16 |
| Low | 0 | — |

### 3.2 The three risks that matter most

1. **RSK-12 — no baseline.** It cannot be mitigated, only accepted and disclosed. It is also the most common way portfolio ERP case studies become dishonest: a plausible "30% faster order processing" appears with no source. This repository will not contain such a number.
2. **RSK-09 — adoption.** A functionally perfect design that users circumvent is a failed implementation. It is the reason the As-Is document explains *why* the spreadsheet process persists.
3. **RSK-07 — late integration.** Interfaces are where ERP timelines actually slip. The mitigation is administrative, not technical: force the inventory in Phase 3, before design is frozen.

---

## 4. Dependency register

| ID | Dependency | Needed for | Impact if unavailable | Owner | Needed by |
|---|---|---|---|---|---|
| DEP-01 | Access to process owners for validation workshops | Gate 1 and Gate 2 sign-off | All Phase 1–2 outputs remain `Draft` | Sponsor | End of Phase 1 |
| DEP-02 | ERP product and version selection | Valid fit-gap classification | Phase 3 remains hypothesis-level | Sponsor | Start of Phase 3 |
| DEP-03 | Vendor documentation and licence confirmation | Capability validation | `Fit` claims cannot be substantiated | IT Lead | Phase 3 |
| DEP-04 | Legacy data extract (customers, products, open transactions) | Data profiling and migration mapping | Migration effort and cleansing scope unknown | Master Data Owner | Start of Phase 4 |
| DEP-05 | Written approval and credit policy | Workflow and credit-block design | Approval design is invented, not implemented | Finance Manager | Phase 2 |
| DEP-06 | Bank statement samples and format specification | Cash application and reconciliation design | Payment matching remains manual by default | Finance Manager | Phase 3 |
| DEP-07 | Test environment and user accounts | UAT execution | Phase 4 cannot run | IT Lead | Start of Phase 4 |
| DEP-08 | Confirmed scope and wave-1 boundary | All design work | Continuous rework as scope shifts | Sponsor | End of Phase 1 |

**Critical path:** DEP-01 and DEP-08 gate Phase 1; DEP-02 and DEP-05 gate Phase 2–3; DEP-04 and DEP-07 gate Phase 4. In a real programme, any of these arriving late shifts the go-live date, not just the document date.

---

## 5. Decision log

Decisions already made, with rationale, so they are not silently revisited.

| ID | Decision | Rationale | Source | Status |
|---|---|---|---|---|
| DEC-01 | Use Order-to-Cash as the process boundary | End-to-end, traceable, and demonstrates the full commercial cycle | Design.md §12 | Agreed |
| DEC-02 | Keep the solution vendor-neutral | No licensed product; vendor claims would be unverifiable | PRD §12, Design.md §1 | Agreed |
| DEC-03 | Prefer standard capability over custom extension | Cost, upgrade risk, and supportability | Design.md §1 principle 2 | Agreed |
| DEC-04 | Use synthetic data only | Privacy and integrity of the case study | PRD §12 | Agreed |
| DEC-05 | Keep financial and commercial approvals with authorised employees | Accountability cannot be delegated to automation | Design.md §1 principle 7 | Agreed |
| DEC-06 | Record unknowns as questions instead of assumptions of fact | Protects the case study from invented detail | This document, §2 | Agreed |
| DEC-07 | Do not build an application or configure an ERP | Scope is functional analysis, not software delivery | Plan.md §8 | Agreed |
| DEC-08 | Do not quantify benefits or ROI | No baseline exists; any figure would be fabricated | RSK-12 | Agreed |
| DEC-09 | Stage 1–10 numbering follows `PRD §4`, not the grouped stages in `Design §2.1` | Keeps approval and delivery accountability visible in RACI and traceability | stakeholder-analysis.md §3 | Agreed |
| DEC-10 | Documentation language is English, business context Indonesian | Portfolio readability for an international reviewer | AS-12 | Agreed |

---

## 6. Issue log

| ID | Issue | Raised | Impact | Owner | Status | Resolution |
|---|---|---|---|---|---|---|
| — | *No active issues. Discovery is document-based; no system or process failure has occurred.* | — | — | — | — | — |

Issues differ from risks: a risk may happen, an issue has happened. Examples that would be logged here in a real engagement: a process owner refusing to participate, a data extract arriving with corrupt records, a UAT environment unavailable on the agreed date.

---

## 7. Register governance

| Activity | Frequency | Owner | Output |
|---|---|---|---|
| Review assumptions against answered DQs | Each phase gate | Functional Consultant | Assumptions closed or re-justified |
| Re-score risks | Each phase gate | Functional Consultant + Sponsor | Updated rating and response |
| Confirm dependencies | Weekly in a real programme | Project Manager | Dependency status |
| Add decisions | Whenever a design choice is made | Design authority | New `DEC-` entry |
| Escalate blockers | Immediately | Sponsor | Decision or deferral |

**Rule:** a document may not pass a phase gate while it depends on an `Open` assumption that is also a `Blocker` question without saying so explicitly. Silent dependency is how projects fail politely.

---

## 8. Change history

| Version | Date | Change |
|---|---|---|
| 0.1 | 12 Nov 2026 | Initial registers created in Phase 1: 18 assumptions, 16 risks, 8 dependencies, 10 decisions |
