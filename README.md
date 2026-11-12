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
| 1 | Business discovery | Days 1–4 | **In progress — deliverables drafted, awaiting validation** |
| 2 | Requirements and process design | Days 5–9 | Planned |
| 3 | Fit-gap analysis and solution blueprint | Days 10–15 | Planned |
| 4 | Testing and migration readiness | Days 16–20 | Planned |
| 5 | Deployment and portfolio preparation | Days 21–25 | Planned |

Status definitions: `Complete` = reviewed and consistent; `Draft` = written, not yet validated; `Planned` = not started.

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
| `docs/discovery-questions.md` | Discovery Questions | Draft |
| `docs/assumptions-risks.md` | Assumptions and Risks Register | Draft |

### 3.3 Phase 2–5 deliverables (planned)

| Path | Deliverable | Phase |
|---|---|---|
| `docs/to-be-process.md` | To-Be Process | 2 |
| `docs/requirements.md` | Functional and non-functional requirement catalogue, business rules | 2 |
| `docs/traceability-matrix.md` | Requirement Traceability Matrix | 2–4 |
| `docs/fit-gap-analysis.md` | Fit-Gap Analysis | 3 |
| `docs/functional-blueprint.md` | Functional Solution Blueprint | 3 |
| `docs/role-authorization-matrix.md` | Role and Authorization Matrix | 3 |
| `docs/data-catalogue.md` | Data Catalogue | 3 |
| `docs/integration-inventory.md` | Integration Inventory | 3 |
| `docs/UAT-plan.md` | UAT Plan and Scenario Catalogue | 4 |
| `docs/data-migration-plan.md` | Data Migration Plan and Reconciliation Checklist | 4 |
| `docs/cutover-plan.md` | Cutover Plan and Go/No-Go Criteria | 5 |
| `docs/hypercare-plan.md` | Hypercare Plan | 5 |
| `docs/change-management-plan.md` | Change Management and Training Plan | 5 |
| `diagrams/as-is-o2c.mmd` | As-Is process diagram source | 1–2 |
| `diagrams/to-be-o2c.mmd` | To-Be process diagram source | 2 |
| `diagrams/solution-architecture.mmd` | Conceptual architecture diagram source | 3 |
| `data/sample-customer-master.csv` | Synthetic customer master sample | 4 |
| `data/sample-product-master.csv` | Synthetic product master sample | 4 |
| `data/sample-sales-orders.csv` | Synthetic sales order sample | 4 |
| `portfolio/case-study.md` | Portfolio case study narrative | 5 |

Notes:

- `Plan.md` §4 lists the intended final structure. Phase 1 adds two files not named there — `discovery-questions.md` and `assumptions-risks.md` — because Phase 1 explicitly requires those deliverables.
- Diagrams currently live inline in `Design.md` and `docs/as-is-process.md`. They will be extracted to `diagrams/*.mmd` when Phase 2 freezes the process models, so there is one source of truth.

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
| `FR-` | Functional requirement | `PRD.md` §5 |
| `NFR-` | Non-functional requirement | `PRD.md` §6 |
| `BR-` | Business rule | `PRD.md` §7 |
| `STK-` | Stakeholder | `docs/stakeholder-analysis.md` |
| `KPI-` | Operational KPI | `PRD.md` §10 |
| `PP-` | As-Is pain point | `docs/as-is-process.md` |
| `CG-` | As-Is control gap (inferred) | `docs/as-is-process.md` |
| `DQ-` | Discovery question / open decision | `docs/discovery-questions.md` |
| `AS-` | Assumption | `docs/assumptions-risks.md` |
| `RSK-` | Risk | `docs/assumptions-risks.md` |
| `DEP-` | Dependency | `docs/assumptions-risks.md` |
| `DEC-` | Logged design decision | `docs/assumptions-risks.md` |
| `FG-` | Fit-gap finding | Phase 3 |
| `TST-` / `UAT-` | Test case | Phase 4 |
| `DM-` | Data migration object | Phase 4 |
| `INT-` | Integration interface | Phase 3 |

Rules:

1. Identifiers are permanent. A withdrawn item keeps its ID and is marked `Withdrawn` with a reason; IDs are never reused or renumbered.
2. Every requirement, rule, and test must trace upward. Traceability is recorded in Phase 2 and completed in Phase 4.
3. Nothing in this repository may state a fact that is not sourced from `PRD.md`/`Design.md`, labelled `AS-`, or raised as `DQ-`. This rule is the main defence against an invented case study.

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

## 7. Working rules for this repository

- Documents and analysis only. No application is built, and no ERP environment is installed (`Plan.md` §8).
- Vendor-neutral language. Capability statements are hypotheses until validated (`PRD.md` §8).
- Synthetic data only; no real personal or commercial data.
- Changes to baseline documents (`PRD.md`, `Plan.md`, `Design.md`) are versioned and explained. Working documents carry their own version number.
- Financial approvals, credit decisions, and write-offs remain with authorised human roles. Automation supports, it does not decide.

---

## 8. Version history

| Version | Date | Change |
|---|---|---|
| 0.1 | 12 Nov 2026 | Initial repository README; Phase 1 deliverables added |
