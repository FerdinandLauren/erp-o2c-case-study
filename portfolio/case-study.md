# Case Study: Designing Order-to-Cash for a Wholesale Distributor

**Role:** ERP Functional Consultant / IT Business Analyst
**Domain:** Wholesale distribution — packaged consumer and industrial products
**Process boundary:** Order-to-Cash
**Approach:** Vendor-neutral functional design
**Repository:** [erp-o2c-case-study](https://github.com/FerdinandLauren/erp-o2c-case-study)

---

## 1. The brief I set myself

Most ERP portfolio projects show screenshots of a configured system. That proves someone can follow a setup guide. It does not prove they can walk into a business, find out how it actually works, decide what should change, and defend the decision when someone disagrees.

So I set a harder brief: design the Order-to-Cash process for a fictional wholesale distributor **without naming a product**, and produce the same artefacts a functional consultant would produce on a real programme — discovery notes, requirements, fit-gap, blueprint, role design, data model, test plan, migration plan, cutover plan.

The constraint that shaped everything: **no invented facts.** The company is fictional, so I could have made up any detail I wanted. Instead, every unknown became a discovery question, and every unavoidable decision became a labelled provisional position. That is the discipline the case study is really demonstrating.

---

## 2. The business

PT Sinar Distribusi Nusantara is a fictional Indonesian distributor selling packaged consumer and industrial products from two warehouses. Customers are retail chains, wholesalers, Horeca, and light industrial buyers. The commercial model is straightforward: take orders, pick and deliver, invoice, collect cash.

The problems are equally ordinary, which is what makes them realistic:

- customer and product records duplicated across departments;
- no reliable view of stock when a customer asks for a promise date;
- handoffs by email and spreadsheet between sales, warehouse, logistics, and finance;
- no way to trace an order through to the invoice that was paid;
- invoices raised at inconsistent points, sometimes before delivery is confirmed;
- reports that disagree because nobody defined the KPI.

Underneath those symptoms sit eight control gaps: no enforced unique identifier, no recorded approval evidence, no segregation of duties, no enforced document traceability, no defined billing trigger, no cash application control, no single KPI definition, and no audit trail with identity and time.

---

## 3. How I worked

### Phase 1 — Discovery without a stakeholder

I wrote the business context, the stakeholder analysis, and the As-Is process, then produced a **63-item discovery question register**. Seventeen are blockers: without an answer, a design decision cannot be validated.

This was the most useful phase precisely because nothing could be asked. Writing "how are credit limits set, applied, and released?" instead of "we will use credit limits" forced the design to stay honest.

### Phase 2 — Process and requirements

The To-Be process has ten forward stages (order, validation, availability, approval, fulfilment, delivery, billing, cash application, reporting) and five reverse-flow stages for returns and credit notes.

The important decision here was **not to renumber the baseline**. Existing requirements stayed `FR-01`–`FR-15`; new ones started at `FR-16`. That kept traceability to the original brief intact and made the extension visible.

Every `Must` requirement received a Given/When/Then acceptance criterion. Six `Should` requirements deliberately did not, and the gap was recorded rather than hidden.

### Phase 3 — Fit-gap and blueprint

This is where a consultant earns their fee, and where it is easiest to bluff. With no product selected, any claim that "the system supports this" would be unverifiable.

So every classification is a **hypothesis with a confidence grade and a stated condition that would change it**. Twenty-three requirement findings: six expected standard, fourteen configuration, one extension, one integration, one decision required. Separately, eight process findings — because a requirement can be a textbook configuration fit and still fail if the policy behind it does not exist.

The blueprint translates the process into 23 configuration objects, a role and authorisation matrix with ten segregation-of-duties conflicts, a 25-entity data catalogue, and a seven-interface inventory.

### Phase 4 — Testing and migration

Thirty-three UAT scenarios cover all 22 acceptance criteria and all six `Should` requirements. Twelve are negative tests designed to break the controls: allocate more than the payment, authorise your own return, invoice without proof of delivery, move a closed order backwards.

The migration plan defines 12 objects, 15 cleansing rules, and 29 reconciliation checks. Counts reconcile before values; the business signs, not IT.

Three synthetic CSVs demonstrate structure and referential integrity — including deliberate edge cases: a blocked customer, a blocked product, an approved price override, a return referencing an earlier invoice.

### Phase 5 — Deployment

Cutover, hypercare, and change management. The cutover plan has twelve entry criteria and every one is marked `Not met`, because no system exists. Go/no-go criteria are written so that a sponsor cannot waive financial reconciliation or statutory invoicing.

---

## 4. Decisions I would defend in a design review

**Bill only from confirmed proof of delivery.** It is the most controlled trigger and makes the invoice defensible. Sales will argue for earlier invoicing; the answer is that an invoice you cannot evidence is a dispute waiting to happen.

**Check credit before value-band approval.** Otherwise a manager approves an order that Finance immediately blocks, and the audit trail records an approval that never took effect. Order matters.

**One invoice per delivery; consolidation deferred.** Per-delivery invoicing keeps invoice-to-delivery traceability exact. Consolidation is a convenience that dilutes the control the design is built on.

**Core O2C runs with zero interfaces.** Bank, carrier, WMS, CRM, and reporting are enhancements with manual fallbacks. Interfaces are the most common cause of go-live delay; making one a precondition is a schedule risk disguised as architecture.

**Deactivate, never delete.** Posted documents and master records are never physically removed. Correction is a new document that references the old one.

**Name the conflicts you cannot separate.** Two segregation-of-duties conflicts (invoice creation plus cash allocation; payment recording plus bank reconciliation) cannot be fully separated in a small AR team. Pretending otherwise would be worse than naming them with a compensating control.

---

## 5. What the work demonstrates

| Skill | Evidence in the repository |
|---|---|
| Structured discovery | 63 questions, 17 blockers, provisional positions mapped to decisions |
| Process design | 10 forward stages, 5 reverse stages, 15 exceptions, status models |
| Requirements discipline | 23 FR, 22 acceptance criteria, 11 NFR, 15 business rules, full traceability |
| Commercial judgement | Fit-gap classifications with confidence and change conditions |
| Solution design | 23 configuration objects, document chain, status profiles |
| Controls thinking | 9 roles, critical transaction matrix, 10 SOD conflicts |
| Data thinking | 25 entities, 16 quality rules, ownership, migration scope |
| Integration thinking | 7 interfaces with ownership, idempotency, fallback, cut-over stance |
| Test design | 33 scenarios, 5 full scripts, entry/exit criteria, defect model |
| Migration design | 12 objects, load sequence, 29 reconciliation checks |
| Go-live realism | 12 entry criteria, go/no-go, rollback, hypercare exit criteria |
| Writing | Every document states its own limits |

---

## 6. What is not proven

I want this section to be as easy to find as the achievements.

- **No product was validated.** Every fit classification is an inference about mainstream ERP capability, not a vendor confirmation.
- **No test was run.** All 33 scenarios are `Not run`; there is no configured system.
- **No data was migrated.** No legacy extract exists; the CSVs are generated illustrations.
- **No policy is approved.** Approval thresholds, credit limits, and tolerances are placeholders tied to open questions.
- **No benefit is claimed.** No baseline was measured, so no time, cost, or error-rate improvement is asserted.
- **No stakeholder agreed.** The company is fictional and nobody participated in a workshop.

A reviewer should treat the design as a set of well-reasoned hypotheses, not as evidence of a delivered system. That is exactly what it is.

---

## 7. What I would do next in a real engagement

1. **Resolve the blockers with named owners.** Start with the billing trigger, credit policy, approval thresholds, and the tax/e-invoice mandate. These four change the design more than any software choice.
2. **Profile the legacy data before promising a cutover date.** Duplicate rate and open-item volume determine effort more than the number of requirements.
3. **Select the product and re-grade every fit classification** against the exact version, edition, and licence.
4. **Run one full migration rehearsal before finalising the test plan.** Rehearsal results change the reconciliation effort, not the scope.
5. **Train key users before UAT, not after.** A key user who cannot execute the scenario cannot judge whether it is right.
6. **Keep a decision log during hypercare.** The first month produces more design changes than the preceding six, and undocumented changes are how a clean design decays.

---

## 8. Why this belongs in a portfolio

It shows how I think when the answer is not in a manual: what I ask, what I decide, what I refuse to invent, and how I make a design falsifiable.

The repository is intentionally reviewable as text. A reviewer can open any document, follow a requirement back to a business problem, forward to a test, and see the exact place where the design stops being proven.

That boundary — between what is designed and what is merely asserted — is the thing I most wanted to get right.

---

## 9. Repository map

| Path | What it contains |
|---|---|
| `README.md` | Navigation, status, gate evidence, identifier conventions |
| `PRD.md`, `Plan.md`, `Design.md` | Original brief, plan, and conceptual design baseline |
| `docs/business-context.md` | Company, model, problem, objectives, scope |
| `docs/as-is-process.md` | Current process, pain points, control gaps |
| `docs/discovery-questions.md` | 63 open questions, provisional-position mapping |
| `docs/assumptions-risks.md` | Assumptions, risks, dependencies, 21 decisions |
| `docs/to-be-process.md` | Target process, exceptions, status models, controls |
| `docs/requirements.md` | FR, AC, NFR, business rules |
| `docs/traceability-matrix.md` | Forward and reverse trace, UAT links, coverage |
| `docs/fit-gap-analysis.md` | 23 requirement findings, 8 process findings |
| `docs/functional-blueprint.md` | Landscape, document chain, 23 configuration objects |
| `docs/role-authorization-matrix.md` | 9 roles, permissions, 10 SOD conflicts |
| `docs/data-catalogue.md` | 25 entities, quality rules, migration scope |
| `docs/integration-inventory.md` | 7 interfaces, ownership, cut-over stance |
| `docs/UAT-plan.md` | 33 scenarios, scripts, entry/exit criteria |
| `docs/data-migration-plan.md` | 12 objects, cleansing, 29 reconciliation checks |
| `docs/cutover-plan.md` | Strategy, runbook, go/no-go, rollback |
| `docs/hypercare-plan.md` | Structure, severity, controls, exit criteria |
| `docs/change-management-plan.md` | Impact, training, readiness, resistance |
| `docs/executive-summary.md` | Ten-minute version of the whole case |
| `diagrams/` | Mermaid sources for As-Is, To-Be, reverse flow, architecture, data model |
| `data/` | Synthetic customer, product, and sales order samples |
