# ERP Functional Implementation Case Study — Order-to-Cash (O2C)

**Company:** PT Sinar Distribusi Nusantara (fictional)
**Business domain:** Wholesale distribution — packaged consumer and industrial products
**Process boundary:** Order-to-Cash (customer order → delivery → invoice → cash collected and receivable closed)
**Approach:** Vendor-neutral ERP functional design
**Repository role:** ERP Functional Consultant / IT Business Analyst portfolio case study
**Last updated:** 12 November 2026

---

## 1. What this repository is

This repository documents an end-to-end ERP functional implementation case study for a fictional wholesale distributor. It demonstrates the deliverables a functional consultant produces during a real ERP programme: business discovery, process analysis, requirements definition, fit-gap analysis, solution design, testing readiness, and cutover preparation.

It is **not** a software product, a working ERP, or a configuration guide.

### Integrity statement

- PT Sinar Distribusi Nusantara is fictional. It is not a real company.
- No real customer, supplier, employee, or confidential business data is used. All sample data is synthetic.
- No ERP product is installed, configured, or deployed for this case study.
- No vendor-specific capability is claimed. Every fit-gap classification is a hypothesis until validated against a selected product, version, licensing, and official documentation.
- No legal, tax, or accounting advice is provided.
- Business problems, volumes, and metrics in this repository are described as hypotheses unless a document explicitly records a measured baseline. No baseline has been measured in this simulation.

---

## 2. Current status

| Phase | Scope | Planned | Status |
|---|---|---|---|
| 1 | Business discovery | Days 1–4 | **Complete as a draft baseline** — 5 deliverables, 63 open questions, Gate 1 evidence recorded |
| 2 | Requirements and process design | Days 5–9 | **Complete as a draft baseline** — To-Be process, 23 FR, 22 AC, 11 NFR, 15 BR, traceability matrix |
| 3 | Fit-gap analysis and solution blueprint | Days 10–15 | **Complete as a draft baseline** — 23 fit-gap findings, blueprint, 9 roles, 25 data entities, 7 interfaces |
| 4 | Testing and migration readiness | Days 16–20 | Planned |
| 5 | Deployment and portfolio preparation | Days 21–25 | Planned |

Status definitions: `Complete as a draft baseline` = written, internally consistent, and gated on evidence, but not validated by a business owner (none exists in a fictional scenario); `Planned` = not started.

---

## 3. Document map

### 3.1 Baseline documents

| Path | Purpose | Status |
|---|---|---|
| `PRD.md` | Business context, problem statement, requirements, business rules, KPIs, acceptance criteria | Complete (v1.0 baseline) |
| `Plan.md` | WBS, phases, quality gates, Definition of Done, target repository structure | Complete (v1.0 baseline) |
| `Design.md` | Target process, capability map, fit-gap method, role design, data and integration design, UAT and cutover design | Complete (v1.0 baseline) |

### 3.2 Phase 1 deliverables

| Path | Deliverable (per `Plan.md` §3) | Status |
|---|---|---|
| `docs/business-context.md` | Business Context | Draft |
| `docs/stakeholder-analysis.md` | Stakeholder Analysis | Draft |
| `docs/as-is-process.md` | As-Is Process | Draft |
| `docs/discovery-questions.md` | Discovery Questions | Draft v0.2 — 63 items, 17 blockers, provisional positions mapped |
| `docs/assumptions-risks.md` | Assumptions and Risks Register | Draft v0.2 — 18 assumptions, 16 risks, 8 dependencies, 21 decisions |

### 3.3 Phase 2 deliverables

| Path | Deliverable (per `Plan.md` §3) | Status |
|---|---|---|
| `docs/to-be-process.md` | To-Be Process — 10 forward stages, 5 reverse-flow stages, 15 exceptions, status models | Draft |
| `docs/requirements.md` | Functional and non-functional requirement catalogue, business rules | Draft — 23 FR, 22 AC, 11 NFR, 15 BR |
| `docs/traceability-matrix.md` | Requirement Traceability Matrix | Draft — forward and reverse trace complete |
| `diagrams/as-is-o2c.mmd` | As-Is process diagram source (extracted from Phase 1) | Draft |
| `diagrams/to-be-o2c.mmd` | To-Be forward process diagram source | Draft |
| `diagrams/o2c-reverse-flow.mmd` | Returns and credit-note flow diagram source | Draft |

### 3.4 Phase 3 deliverables

| Path | Deliverable (per `Plan.md` §3) | Status |
|---|---|---|
| `docs/fit-gap-analysis.md` | Fit-Gap Analysis — 23 requirement findings, 8 process findings, validation plan | Draft — hypotheses, no product selected |
| `docs/functional-blueprint.md` | Functional Solution Blueprint — architecture, document chain, 23 configuration objects | Draft |
| `docs/role-authorization-matrix.md` | Role and Authorization Matrix — 9 roles, 10 SOD conflicts, status-based permissions | Draft |
| `docs/data-catalogue.md` | Data Catalogue — 25 entities, 16 quality rules, migration implications | Draft |
| `docs/integration-inventory.md` | Integration Inventory — 7 candidate interfaces, ownership, cut-over stance | Draft |
| `diagrams/solution-architecture.mmd` | Conceptual architecture diagram source | Draft |
| `diagrams/o2c-data-model.mmd` | Entity-relationship diagram source | Draft |

### 3.5 Phase 4–5 deliverables (planned)

| Path | Deliverable | Phase |
|---|---|---|
| `docs/UAT-plan.md` | UAT Plan and Scenario Catalogue | 4 |
| `docs/data-migration-plan.md` | Data Migration Plan and Reconciliation Checklist | 4 |
| `docs/cutover-plan.md` | Cutover Plan and Go/No-Go Criteria | 5 |
| `docs/hypercare-plan.md` | Hypercare Plan | 5 |
| `docs/change-management-plan.md` | Change Management and Training Plan | 5 |
| `data/sample-customer-master.csv` | Synthetic customer master sample | 4 |
| `data/sample-product-master.csv` | Synthetic product master sample | 4 |
| `data/sample-sales-orders.csv` | Synthetic sales order sample | 4 |
| `portfolio/case-study.md` | Portfolio case study narrative | 5 |

Notes:

- `Plan.md` §4 lists the intended final structure. Phase 1 adds two files not named there — `discovery-questions.md` and `assumptions-risks.md` — because Phase 1 explicitly requires those deliverables.
- `Plan.md` names one To-Be diagram; Phase 2 adds `o2c-reverse-flow.mmd` because the returns flow is a separate decision path and cannot be read clearly on the forward diagram. Phase 3 adds `o2c-data-model.mmd` because the entity relationships in `docs/data-catalogue.md` are easier to audit as a diagram than as five tables.
- Process diagrams are `.mmd` sources, not pasted images. They render on GitHub and remain reviewable as text; the prose is the authoritative description if a diagram and the text ever disagree.
- A **provisional** decision (`DEC-11`–`DEC-21`) is a design position, not a business fact. It is listed in `docs/assumptions-risks.md` §5 with the `DQ-` it stands in for, and it does not close that question.
- A fit-gap **hypothesis** is not a vendor claim. No product was selected (`DQ-61`), so every `Fit` in `docs/fit-gap-analysis.md` means "expected standard capability", not "confirmed in the product".

---

## 4. How to read this repository

1. **`PRD.md`** — why the project exists, what is in scope, and what "done" means.
2. **`Design.md`** — the conceptual target solution: process, capabilities, roles, data, integrations, controls.
3. **`Plan.md`** — how the work is sequenced, and the quality gates that must pass.
4. **`README.md`** (this file) — where everything is and what state it is in.
5. **`docs/`** — the working deliverables, produced phase by phase.
6. **`docs/discovery-questions.md`** — the single place where unknown facts are recorded instead of invented.

Reading order for a reviewer who wants the business story only: `docs/business-context.md` → `docs/as-is-process.md` → `Design.md` §2.

---

## 5. Identifier conventions

| Prefix | Meaning | Defined in |
|---|---|---|
| `FR-` | Functional requirement | `docs/requirements.md` §3 (baseline from `PRD.md` §5) |
| `AC-` | Acceptance criterion, Given/When/Then | `docs/requirements.md` §4 |
| `NFR-` | Non-functional requirement | `docs/requirements.md` §5 (baseline from `PRD.md` §6) |
| `BR-` | Business rule | `docs/requirements.md` §6 (baseline from `PRD.md` §7) |
| `S1`–`S10` | To-Be forward stage | `docs/to-be-process.md` §3 |
| `R1`–`R5` | To-Be reverse-flow stage | `docs/to-be-process.md` §4 |
| `EX-` | Process exception | `docs/to-be-process.md` §7 |
| `STK-` | Stakeholder | `docs/stakeholder-analysis.md` |
| `KPI-` | Operational KPI | `PRD.md` §10 |
| `PP-` | As-Is pain point | `docs/as-is-process.md` |
| `CG-` | As-Is control gap (inferred) | `docs/as-is-process.md` |
| `DQ-` | Discovery question / open decision | `docs/discovery-questions.md` |
| `AS-` | Assumption | `docs/assumptions-risks.md` |
| `RSK-` | Risk | `docs/assumptions-risks.md` |
| `DEP-` | Dependency | `docs/assumptions-risks.md` |
| `DEC-` | Logged design decision (`Agreed` or `Provisional`) | `docs/assumptions-risks.md` |
| `FG-` | Fit-gap finding | `docs/fit-gap-analysis.md` |
| `FG-P` | Process or policy finding (not a software gap) | `docs/fit-gap-analysis.md` §6 |
| `CFG-` | Configuration design object | `docs/functional-blueprint.md` §8 |
| `ROL-` | Role in the access design | `docs/role-authorization-matrix.md` §3 |
| `SOD-` | Segregation-of-duties conflict | `docs/role-authorization-matrix.md` §6 |
| `DE-` | Data entity | `docs/data-catalogue.md` §3 |
| `INT-` | Integration interface | `docs/integration-inventory.md` §3 |
| `TST-` / `UAT-` | Test case | Phase 4 |
| `DM-` | Data migration object | Phase 4 |

Rules:

1. Identifiers are permanent. A withdrawn item keeps its ID and is marked `Withdrawn` with a reason; IDs are never reused or renumbered.
2. Every requirement, rule, and test must trace upward. Traceability is recorded in Phase 2 and completed in Phase 4.
3. Nothing in this repository may state a fact that is not sourced from `PRD.md`/`Design.md`, labelled `AS-`, or raised as `DQ-`. This rule is the main defence against an invented case study.
4. A `Must` requirement without an acceptance criterion cannot pass Gate 2. A `Should` requirement may defer its criterion to Phase 4, but the deferral must be named in `docs/traceability-matrix.md` §5.

---

## 6. Phase 1 gate evidence (`Plan.md` §6, Gate 1)

| Gate 1 criterion | Evidence | Status |
|---|---|---|
| Business problem is documented | `docs/business-context.md` §7, `docs/as-is-process.md` §5 | Met (hypotheses, labelled) |
| Stakeholders are identified | `docs/stakeholder-analysis.md` §2–§4 | Met (roles confirmed pending org chart, `DQ-49`) |
| Process boundaries are defined | `docs/business-context.md` §9, `docs/as-is-process.md` §2 | Met |
| Assumptions and open questions are recorded | `docs/assumptions-risks.md`, `docs/discovery-questions.md` | Met (63 open questions, 18 assumptions, 16 risks) |
| No missing information presented as fact | Assumption/question labelling applied throughout | Met by construction; reviewer spot-check pending |

Gate 1 is **not signed off** until a business owner confirms scope and stakeholder roles in a discovery workshop. In this simulation that sign-off is recorded as `AS-14` and remains open.

---

## 7. Phase 2 gate evidence (`Plan.md` §6, Gate 2)

| Gate 2 criterion | Evidence | Status |
|---|---|---|
| To-Be process is documented end to end | `docs/to-be-process.md` §3 (S1–S10), §4 (R1–R5), diagrams `to-be-o2c.mmd` and `o2c-reverse-flow.mmd` | Met |
| Each requirement has unique ID, owner, priority, and source | `docs/requirements.md` §3 — 23 FR, all four attributes populated | Met |
| Requirements are specific and testable | `docs/requirements.md` §4 — 22 Given/When/Then criteria covering 17/17 `Must` FRs | Met |
| Business rules have an enforcement point or are marked proposed | `docs/requirements.md` §6 — 15 BR, 7 marked `To Validate` | Met |
| Every business problem maps to a requirement | `docs/traceability-matrix.md` §3 — PP 6/6, CG 8/8, KPI 7/7 covered | Met |
| Unknowns are recorded, not invented | `docs/discovery-questions.md` §5.1 — 17 blockers remain `Open`; 8 with a provisional position, 9 with a named assumption or later deliverable | Met by declaration |
| Requirements conflict with the baseline are visible | `DEC-20` — baseline `FR-01`–`FR-15` unchanged; additions start at `FR-16` | Met |

**Gate 2 pass condition in this simulation:** the design is complete and internally traceable; it is **not** validated. Three things are explicitly not proven:

1. The 7 `To Validate` business rules depend on unanswered policy questions (`DQ-17`, `DQ-26`, `DQ-33`, `DQ-34`, `DQ-45`, `DQ-46`).
2. Six `Should` requirements have no acceptance criteria yet (`docs/traceability-matrix.md` §5).
3. NFR targets for availability, response time, retention, and volume are unquantified (`DQ-05`, `DQ-51`, `DQ-59`).

A reviewer should read Gate 2 as "the design holds together", not "the design is right for a real company".

---

## 8. Phase 3 gate evidence (`Plan.md` §6, Gate 3)

| Gate 3 criterion | Evidence | Status |
|---|---|---|
| Every `Must` requirement has a fit-gap classification | `docs/fit-gap-analysis.md` §3 — one FG row per FR-01…FR-23, including all 17 `Must` | Met (hypothesis) |
| Functional solution is documented | `docs/functional-blueprint.md` — landscape, document chain, stage design, 23 configuration objects | Met |
| Roles and responsibilities are defined | `docs/role-authorization-matrix.md` — 9 roles, permission matrix, 10 SOD conflicts | Met (proposed) |
| Data requirements are identified | `docs/data-catalogue.md` — 25 entities, 16 quality rules, ownership, migration scope | Met |
| Integration requirements are identified | `docs/integration-inventory.md` — 7 interfaces, ownership, cut-over stance | Met (candidate list) |
| Architecture is coherent with the process design | `diagrams/solution-architecture.mmd`; every To-Be stage maps to a core component | Met |
| Known gaps and decisions are visible | `docs/fit-gap-analysis.md` §9, `docs/functional-blueprint.md` §10, `docs/integration-inventory.md` §8 | Met |

**Gate 3 pass condition in this simulation:** the design is complete enough to test, not proven against any product. What is explicitly not established:

1. **No product validation.** Every `Fit` is an inference about mainstream capability, not a vendor confirmation (`DQ-61`, `DQ-63`).
2. **No policy values.** Approval bands, tolerances, credit limits, and price tolerances are blank parameters (`DQ-17`, `DQ-26`, `DQ-45`, `DQ-46`).
3. **No data profile.** Entity design is unvalidated against real source data, because none exists (`DEP-04`).
4. **No interface agreement.** The inventory is a candidate list; no system owner has confirmed anything (`DQ-53`).
5. **No effort estimate.** Counting configuration objects would create false precision, so none is given.

A reviewer should read Gate 3 as "the design is specific enough to test", not "the solution is designed".

---

## 9. Working rules for this repository

- Documents and analysis only. No application is built, and no ERP environment is installed (`Plan.md` §8).
- Vendor-neutral language. Capability statements are hypotheses until validated (`PRD.md` §8).
- Synthetic data only; no real personal or commercial data.
- Changes to baseline documents (`PRD.md`, `Plan.md`, `Design.md`) are versioned and explained. Working documents carry their own version number.
- Financial approvals, credit decisions, and write-offs remain with authorised human roles. Automation supports, it does not decide.

---

## 10. Version history

| Version | Date | Change |
|---|---|---|
| 0.1 | 12 Nov 2026 | Initial repository README; Phase 1 deliverables added |
| 0.2 | 12 Nov 2026 | Phase 2 complete as a draft baseline: To-Be process, requirements catalogue, traceability matrix, three Mermaid diagram sources; Gate 2 evidence added; provisional decisions `DEC-11`–`DEC-21` recorded and linked |
| 0.3 | 12 Nov 2026 | Phase 3 complete as a draft baseline: fit-gap analysis, functional blueprint, role and authorization matrix, data catalogue, integration inventory, architecture and data-model diagrams; Gate 3 evidence added |
