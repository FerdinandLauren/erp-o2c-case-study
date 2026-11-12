# Executive Summary

**Document ID:** EXS-01
**Version:** 0.1 (Draft)
**Phase:** 5 — Deployment and Portfolio Preparation
**Audience:** Hiring manager, practice lead, or sponsor who wants the whole case study in ten minutes

---

## 1. What this is

A vendor-neutral, end-to-end **Order-to-Cash (O2C) functional implementation case study** for a fictional Indonesian wholesale distributor, PT Sinar Distribusi Nusantara.

It is the document set a functional consultant produces across a five-phase ERP programme: discovery, requirements and process design, fit-gap and solution blueprint, testing and migration readiness, and cutover preparation. It is not a working system, a configuration guide, or a claim that an ERP was deployed.

---

## 2. The business problem

The fictional distributor sells packaged consumer and industrial products through two warehouses to retail, wholesale, Horeca, and industrial customers. Commercial activity runs on spreadsheets, email, and individual knowledge.

Six pain points drive the case:

1. Duplicate customer and product records.
2. Limited inventory visibility, so promises are made without a reliable availability view.
3. Manual departmental handoffs between sales, warehouse, logistics, and finance.
4. Limited order traceability from order to cash.
5. Potential billing delays because the invoice trigger is not defined.
6. Inconsistent operational reporting with no single KPI definition.

Eight control gaps accompany them, including no enforced unique identifier, no recorded approval evidence, no segregation of duties, and no audit trail with identity and time.

---

## 3. Approach

| Phase | Question answered | Main artefacts |
|---|---|---|
| 1 — Discovery | What is happening today, and what do we not know? | Business context, stakeholder analysis, As-Is process, 63 discovery questions, assumption/risk/decision registers |
| 2 — Requirements and design | What should happen, and how will we know it works? | To-Be process (10 forward stages, 5 reverse-flow stages, 15 exceptions), 23 FR, 22 acceptance criteria, 11 NFR, 15 business rules, traceability matrix |
| 3 — Fit-gap and blueprint | How would an ERP realise this, and where are the gaps? | 23 fit-gap findings, 8 process findings, 23 configuration objects, 9 roles with 10 segregation-of-duties conflicts, 25 data entities, 7 interface candidates, architecture and data-model diagrams |
| 4 — Testing and migration | How would acceptance and data load be proven? | 33 UAT scenarios, 12 migration objects, 29 reconciliation checks, three synthetic CSV samples |
| 5 — Deployment | How would the switch be controlled? | Cutover runbook and go/no-go criteria, hypercare plan, change management and training plan, this summary |

The method is deliberately conservative: **record the unknown instead of inventing it**. Where a decision is unavoidable, it is logged as a provisional design position with a named question and a named exit, not presented as company policy.

---

## 4. Key design decisions

| Decision | Rationale |
|---|---|
| One document chain, no free-standing transactions | Invoice requires delivery; credit note requires delivery and invoice; allocation requires invoice. This is the backbone of traceability (`FR-13`). |
| Billing only from a confirmed proof of delivery | Makes the invoice defensible and removes the most common cause of billing disputes (`DEC-11`). |
| Stock reserved at order confirmation, released on cancellation | Commits stock only for validated orders (`DEC-12`). |
| Available-to-promise = on-hand minus reservations, excluding in-transit | Avoids promising goods the company cannot see (`DEC-13`). |
| Credit exposure checked before value-band approval | Separates commercial risk from spend authority, so one approval cannot silently override the other (`DEC-14`). |
| Human authority for credit release, return authorisation, and write-off | Automation supports the decision but never becomes the decision-maker (`DEC-05`). |
| Core O2C runs with zero interfaces | Every interface is an enhancement; interfaces are the most common cause of go-live delay (`RSK-07`). |
| Deactivate, never delete | Master data and posted documents are never physically removed (`BR-13`, `BR-15`). |

---

## 5. What makes the design defensible

1. **Traceability is complete in both directions.** Every requirement points to a pain point or control gap, a process stage, and a test scenario. Every pain point points to a requirement.
2. **Unknowns are visible.** 17 `Blocker` questions remain open. Where the design had to proceed, a provisional decision (`DEC-11`–`DEC-21`) names the question it stands in for and what would replace it. No business fact was invented.
3. **Fit-gap is honest about evidence.** No ERP product was selected, so every "Fit" is labelled a hypothesis with a confidence grade and a condition that would change it. No vendor claim is made.
4. **Controls are designed, not assumed.** The role matrix states not only who can do what, but which combinations are forbidden. Ten segregation-of-duties conflicts are named, including two that a small distributor cannot fully separate and must compensate for.
5. **Testing is falsifiable.** Each acceptance criterion has a scenario with preconditions and an expected result written before execution. Negative tests exist specifically to try to break the controls.
6. **Migration proves numbers, not just loads.** Counts reconcile before values, and the business signs, not IT.

---

## 6. What is explicitly not proven

This is the part a reviewer should test first.

| Not proven | Why |
|---|---|
| Any ERP product supports the design | No product, edition, or licence was selected (`DQ-61`, `DQ-63`). Every fit classification is a market inference. |
| Any test passed | There is no system. All 33 UAT scenarios are `Not run`. |
| Any data migrated | No source extract exists (`DEP-04`). The CSVs are synthetic illustrations. |
| Any policy is correct | Approval bands, credit limits, tolerances, and the billing trigger are provisional or unanswered (`DQ-17`, `DQ-26`, `DQ-33`, `DQ-34`, `DQ-45`, `DQ-46`). |
| Any benefit was achieved | No baseline was measured. No time, cost, or error-rate improvement is claimed (`DEC-08`). |
| The organisation agreed | The company is fictional and no stakeholder participated (`DQ-56`). |
| The cutover happened | The plan has 12 entry criteria and every one is `Not met`. |

The value of the case study is not that it proves an outcome. It is that it shows **how an outcome would be proven**, and refuses to pretend the proof already exists.

---

## 7. What a reviewer can inspect

| If you want to see… | Read |
|---|---|
| Business understanding | `docs/business-context.md`, `docs/as-is-process.md` |
| Requirements discipline | `docs/requirements.md`, `docs/traceability-matrix.md` |
| Process design | `docs/to-be-process.md`, `diagrams/to-be-o2c.mmd` |
| Fit-gap judgement | `docs/fit-gap-analysis.md` |
| Solution design | `docs/functional-blueprint.md`, `diagrams/solution-architecture.mmd` |
| Access and controls | `docs/role-authorization-matrix.md` |
| Data thinking | `docs/data-catalogue.md`, `diagrams/o2c-data-model.mmd` |
| Integration thinking | `docs/integration-inventory.md` |
| Test design | `docs/UAT-plan.md` |
| Migration design | `docs/data-migration-plan.md`, `data/` |
| Go-live realism | `docs/cutover-plan.md`, `docs/hypercare-plan.md` |
| Adoption realism | `docs/change-management-plan.md` |
| Honesty about limits | `README.md` §9, `docs/assumptions-risks.md`, this document §6 |

---

## 8. Conclusion

The case study demonstrates the full functional consulting lifecycle on a single, traceable process boundary. Its strongest quality is not the volume of documents; it is that every claim is either sourced, labelled as an assumption, or raised as an open question — and that the places where the design is unproven are stated as plainly as the places where it is complete.

A reviewer who wants to test the work should start with the open questions, not the diagrams.

---

## 9. Change log

| Version | Date | Change |
|---|---|---|
| 0.1 | 12 Nov 2026 | Initial executive summary of the five-phase case study |
