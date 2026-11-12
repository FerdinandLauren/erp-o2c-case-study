# Fit-Gap Analysis

**Document ID:** FGA-01
**Version:** 0.1 (Draft)
**Phase:** 3 — Fit-Gap Analysis and Solution Blueprint
**Method source:** `PRD.md` §8; `Design.md` §4
**Inputs:** `docs/requirements.md` (FR-01…FR-23, BR-01…BR-15), `docs/to-be-process.md` (§3–§9), `docs/assumptions-risks.md`
**Status:** Draft — hypotheses only; no ERP product, version, edition, or licence has been selected

---

## 1. What this document can and cannot claim

`PRD.md` §8 requires every functional requirement to be classified as Fit, Configuration, Extension, Integration, Process Change, or Gap / Decision Required. This document does that.

**It does not claim that any specific ERP product supports any requirement.** No product was selected (`DQ-61`), no edition or licence was examined (`DQ-63`), and no official documentation was reviewed. Every classification below is therefore a *hypothesis about the class of mainstream ERP capability that would normally be expected to cover this need*.

| Term used here | Means exactly this | Does **not** mean |
|---|---|---|
| Fit | A standard concept exists in the O2C capability set; adopting it requires no software change | That the selected product has it |
| Configuration | The capability exists but requires setup (rules, statuses, number ranges, tolerances) | That setup effort is small or cheap |
| Extension | The described behaviour most likely exceeds standard configuration in some detail | That development is definitely required |
| Integration | The requirement depends on data from another system | That an interface exists or is technically simple |
| Process Change | The requirement changes what people do, not only what the system does | That training alone will deliver it |
| Gap / Decision Required | The requirement cannot be classified until a business decision is made | That the product cannot do it |

**Confidence** is graded separately, because a classification can be right in kind and wrong in detail:

- **High** — the concept is common across order-management and receivables systems.
- **Medium** — usually available, but commonly restricted by edition, module, or data quality.
- **Low** — highly product-specific or dependent on an unanswered decision. Treat as unknown.

**Status rule.** Every finding starts `Open`. It becomes `Validated` only when a named reviewer checks it against a selected product's official documentation and records the version. Nothing in this document is `Validated`.

---

## 2. Method

1. Take each `Must` and `Should` requirement from `docs/requirements.md` §3.
2. Identify the capability it needs from `Design.md` §3.
3. Assign the most conservative classification the wording allows.
4. Record the evidence that would change the classification.
5. Separate **software gaps** from **process and policy gaps**. A requirement that a system can enforce but the business has not decided is a `Gap / Decision Required`, not a Fit.
6. Re-check the result against the business rules (`BR-`) and exceptions (`EX-`) that depend on it.

The last step matters most. A requirement can be a textbook Configuration fit and still fail because the policy behind it does not exist. Those cases are marked `Process Change` in §6 even when the software side is simple.

---

## 3. Fit-gap register

One row per functional requirement. `Decision status` records whether the *design* is settled, not whether the product is validated.

| ID | Requirement | Capability area | Hypothesis | Confidence | Condition that would change it | Decision status |
|---|---|---|---|---|---|---|
| FG-01 | FR-01 — customer master with unique ID | Master Data | Configuration | High | Uniqueness cannot be enforced across company codes | Provisional (`DEC-20`, `DEC-13`) |
| FG-02 | FR-02 — product master with UoM | Master Data | Configuration | High | Product has no sales UoM or conversion model | Provisional (`DEC-20`) |
| FG-03 | FR-03 — create and update sales orders | Sales | Fit | High | Order types require custom screens | Settled |
| FG-04 | FR-04 — validate order fields | Sales | Configuration | High | Validation needs cross-field rules the product cannot express | Settled |
| FG-05 | FR-05 — inventory availability information | Inventory | Configuration | Medium | No ATP concept; availability is a report only | Provisional (`DEC-13`, `DQ-25`) |
| FG-06 | FR-06 — configurable approval rules | Approval | Configuration | Medium | Multi-level, value-band workflow needs an extension | Provisional (`DEC-14`, `DQ-46`) |
| FG-07 | FR-07 — fulfilment requirement from confirmed orders | Warehouse | Fit | High | Order-to-delivery is manual and not document-linked | Settled |
| FG-08 | FR-08 — picked, packed, shipped quantities | Warehouse | Configuration | Medium | Over-delivery tolerance is not configurable per customer | Provisional (`DEC-15`, `DQ-26`) |
| FG-09 | FR-09 — delivery status and evidence | Logistics | Integration | Medium | POD is recorded manually in the ERP, not received from a carrier | Open (`DQ-53`) |
| FG-10 | FR-10 — invoice from approved billing trigger | Billing | Configuration | High | Billing is not linked to delivery; invoices are free-standing | Provisional (`DEC-11`, `DQ-33`) |
| FG-11 | FR-11 — invoice status tracking | Receivables | Fit | High | Status model conflicts with the product's clearing logic | Settled |
| FG-12 | FR-12 — record and allocate payments | Receivables | Fit | High | Partial allocation and unapplied cash are not supported | Settled |
| FG-13 | FR-13 — end-to-end traceability | Cross-process | Fit | High | Documents are not linked by parent reference | Settled |
| FG-14 | FR-14 — operational reports | Reporting | Configuration | Medium | KPI definitions require a reporting layer, not standard reports | Provisional (`DQ-51`) |
| FG-15 | FR-15 — transaction history for audit | Audit | Fit | High | Audit is procedural only, not system-enforced | Settled |
| FG-16 | FR-16 — returns and credit notes | Reverse logistics / AR | Configuration | Medium | Authorisation must be a separate role; product ties it to the creator | Provisional (`DEC-16`) |
| FG-17 | FR-17 — credit exposure check and block | Credit | Configuration | Medium | Credit check needs an external data feed or manual exposure input | Provisional (`DEC-14`, `DQ-45`) |
| FG-18 | FR-18 — backorder and outstanding quantity | Inventory / Sales | Configuration | Medium | Backorder release needs a scheduling rule the product lacks | Provisional (`DEC-13`) |
| FG-19 | FR-19 — price determination and override control | Pricing | Configuration | Medium | Override approval above tolerance needs an extension | Settled (`DQ-17` still open as policy) |
| FG-20 | FR-20 — lifecycle status and valid transitions | Cross-process | Configuration | Medium | Product statuses cannot be restricted to the designed transitions | Provisional (`DEC-19`) |
| FG-21 | FR-21 — exception register with reason and owner | Reporting / Audit | Extension | Low | Standard reason codes plus a report may satisfy it | Open (`KPI-07`) |
| FG-22 | FR-22 — statutory invoice data | Billing / Tax | Gap / Decision Required | Low | Tax authority requires a certified e-invoice channel | Open — `DQ-37` blocker |
| FG-23 | FR-23 — duplicate detection at entry | Master Data | Configuration | Medium | No fuzzy matching; only exact-match duplicate check is available | Settled |

### 3.1 Classification summary

| Classification | Count | IDs |
|---|---|---|
| Fit | 6 | FG-03, FG-07, FG-11, FG-12, FG-13, FG-15 |
| Configuration | 14 | FG-01, FG-02, FG-04, FG-05, FG-06, FG-08, FG-10, FG-14, FG-16, FG-17, FG-18, FG-19, FG-20, FG-23 |
| Extension | 1 | FG-21 |
| Integration | 1 | FG-09 |
| Process Change | 0 in this table | See §6 — process findings are tracked separately and cross-reference FG IDs |
| Gap / Decision Required | 1 | FG-22 |

**Reading the summary:** a design where 6 of 23 requirements are expected standard and 14 need configuration is normal for a first ERP implementation. It is not evidence of a cheap project. Configuration effort is dominated by decisions, data quality, and testing, none of which this classification measures. No effort estimate is given because no product or team exists to estimate for.

---

## 4. Gap detail

Only findings that are not plain `Fit` need an option analysis. Options are written as choices a business can actually make, with the cost of each stated in non-financial terms.

### 4.1 FG-06 — approval workflow (Configuration, Medium)

| | Option A — value-band approval in standard workflow | Option B — extension for full matrix | Option C — manual approval outside the system |
|---|---|---|---|
| What it means | Band A/B/C by order value, credit check first | Approval matrix by customer, margin, and value | Manager approves by email; system records only the outcome |
| Advantage | Auditable, one rule, low build risk | Matches a complex real policy | Fast to start |
| Cost / risk | Cannot express customer-specific exceptions | Build and upgrade cost; policy must be documented first | Recreates CG-02; approval evidence stays outside the system |
| Recommendation | **Adopt A for wave 1**; treat B as a change request if policy demands it | Only if `DQ-46` produces a matrix that A cannot express | Reject — it defeats FR-06 |

**Owner:** Sales Manager + Finance Manager. **Blocked by:** `DQ-46` (thresholds), `DQ-45` (credit policy).

### 4.2 FG-09 — delivery status and proof of delivery (Integration, Medium)

| | Option A — manual POD entry in ERP | Option B — carrier interface | Option C — customer portal confirmation |
|---|---|---|---|
| What it means | Driver returns signed document; clerk records date and receiver | Carrier sends delivery events; POD image stored | Customer confirms receipt online |
| Advantage | No interface; works on day one | Timely status, less manual entry | Strong evidence, fewer disputes |
| Cost / risk | Delay between delivery and invoice; manual error | Interface build, carrier coverage varies | Adoption risk; not all customers will use it |
| Recommendation | **A for wave 1**, with the POD document reference mandatory; B as Phase 2 interface | Only for carriers already providing events | Defer until customer base is segmented |

**Owner:** Logistics Coordinator. **Blocked by:** `DQ-53` (which systems exist).

### 4.3 FG-16 — returns and credit notes (Configuration, Medium)

| | Option A — standard returns with credit note | Option B — credit note only, no goods return | Option C — manual credit outside the system |
|---|---|---|---|
| What it means | Return delivery → goods receipt → credit note → AR reduction | Commercial credit without physical return | Finance adjusts the ledger |
| Advantage | Full traceability to delivery and invoice | Handles price disputes | No process change |
| Cost / risk | Requires a returns authorisation role and warehouse handling | Stock and invoice diverge unless reason codes are strict | Breaks FR-13 and FR-16; no audit trail |
| Recommendation | **Adopt A**, with B as a reason-coded variant of the same document flow | Use B only for commercial adjustments, never for goods | Reject |

**Owner:** Finance Manager. **Blocked by:** `DQ-22` (in wave 1?). Design position: included (`DEC-16`).

### 4.4 FG-21 — exception register (Extension, Low)

| | Option A — reason codes on standard documents | Option B — dedicated exception object | Option C — report extracted to a spreadsheet |
|---|---|---|---|
| What it means | Each exception is a code on the order, delivery, or invoice | A separate record with owner, timestamp, resolution | Operational report is the register |
| Advantage | No build; uses existing audit trail | Meets FR-21 exactly; supports KPI-07 | Fast |
| Cost / risk | Hard to count exceptions across document types | One more object to design, build, and maintain | Not auditable; KPI-07 becomes a manual count |
| Recommendation | **Adopt A for wave 1**, then decide whether B is justified by exception volume | Only if KPI-07 is a management priority | Reject as a permanent control |

**Owner:** Sales Ops. **Note:** the honest position is that KPI-07 may not be measurable in wave 1. That is recorded in `docs/traceability-matrix.md` §3.3.

### 4.5 FG-22 — statutory invoice data (Gap / Decision Required, Low)

This is the only finding that can invalidate the invoice design after build.

| | Option A — statutory fields in standard invoice | Option B — certified e-invoice provider | Option C — defer |
|---|---|---|---|
| What it means | Tax ID, tax amount, invoice number and date printed from ERP | Invoice sent to a tax-authority channel through a provider | Invoice design proceeds without the requirement |
| Advantage | No external dependency | Meets a legal mandate | No effort now |
| Cost / risk | Fails if a certified channel is mandatory | Integration, provider selection, testing | Go-live blocked late (`RSK-13`) |
| Recommendation | **Do not decide here.** `DQ-37` must be answered by Finance before blueprint sign-off | Likely necessary if e-invoicing is mandated | Only with a written risk acceptance from the sponsor |

**Owner:** Finance Manager. **Status:** Open — blocker.

---

## 5. Requirements that are product-neutral by design

Six requirements are `Fit` because the concept is standard. Being standard does not make them free:

| Requirement | Why it still needs work |
|---|---|
| FR-03 order creation | Screen and field design, order types, and numbering must match the To-Be process |
| FR-07 fulfilment requirement | Requires stock accuracy; a standard document on wrong stock is still wrong |
| FR-11 invoice status tracking | Statuses must reconcile with the AR ageing report |
| FR-12 payment allocation | Bank statement format and matching rules are unresolved (`DQ-41`) |
| FR-13 traceability | Depends on users never creating free-standing documents |
| FR-15 audit history | Audit is only as good as the roles that are allowed to change data |

---

## 6. Process and policy findings

These are not software gaps. They are the changes without which the configured system will be bypassed. They are the real scope of Phase 5.

| ID | Finding | Related | Change required | Owner | Resistance expected |
|---|---|---|---|---|---|
| FG-P-01 | Approval exists in practice but not in writing | FR-06, BR-04, `DQ-46` | Publish a delegation-of-authority table before workflow configuration | Finance Manager | Managers lose informal discretion |
| FG-P-02 | Invoice timing is inconsistent | FR-10, BR-10, `DQ-33` | Adopt one billing trigger (POD) for wave 1 | Finance Manager | Sales wants earlier invoicing |
| FG-P-03 | Credit limits are not maintained | FR-17, BR-09, `DQ-45` | Maintain limits per customer; define who releases a blocked order | Finance Manager | Sales sees credit blocks as lost revenue |
| FG-P-04 | Master data has no single owner | FR-01, FR-02, NFR-04, `AS-15` | Name one owner per entity; freeze data before cutover | Sponsor | Departments lose local control of "their" list |
| FG-P-05 | Proof of delivery is optional in practice | FR-09, BR-10 | Make POD reference mandatory before invoicing | Logistics Coordinator | Drivers and clerks add a step |
| FG-P-06 | Prices are negotiated per order without records | FR-19, BR-11, `DQ-17` | Maintain price conditions; require approval and reason for override | Sales Manager | Sales loses pricing flexibility |
| FG-P-07 | Returns are handled informally | FR-16, BR-12, `DQ-22` | Formal request → authorise → receive → credit note | Finance Manager | "We just fix it" culture |
| FG-P-08 | Payment matching depends on one person | FR-12, `RSK-15` | Document matching rules; cross-train; use unapplied cash | Finance Manager | Key-person comfort |

**Test for this section:** if a finding cannot name a role that must behave differently, it is not a process change — it is a configuration detail that belongs in §3.

---

## 7. Validation plan

A fit-gap that is never validated is a guess with a table around it. This is how each classification would be checked in a real programme.

| Classification | Evidence required | Who validates | Recorded as |
|---|---|---|---|
| Fit | Product documentation for the exact version + a scripted demo of the To-Be step | Functional Consultant + vendor | FG status `Validated`, with version and date |
| Configuration | Configuration guide + a configured test case in the sandbox | Consultant + process owner | Configuration item reference |
| Extension | Written gap statement + technical feasibility review + cost/risk decision | Architect + sponsor | Change request or backlog item |
| Integration | Interface specification + test message exchange | IT / Integration Lead | `INT-` record updated with status |
| Process Change | Signed policy or procedure + named role accountable | Process owner | Change management plan action |
| Gap / Decision Required | Written business decision with owner and date | Sponsor or delegated owner | `DQ-` answered; finding reclassified |

**Gate rule.** Phase 3 may close with hypotheses, but Phase 4 test design may not test a `Low`-confidence finding as if it were configured. Either the finding becomes `Validated`, or the test is written as an assumption test with an explicit expected failure mode.

---

## 8. What would change this analysis

| Event | Consequence |
|---|---|
| ERP product selected (`DQ-61`) | Every row re-reviewed; confidence re-graded; some Configuration becomes Extension or Fit |
| Edition or licence restricted (`DQ-63`) | Approval workflow, credit management, and reporting are the usual casualties |
| `DQ-37` answered as mandatory e-invoicing | FG-22 becomes an Integration finding and a critical-path item |
| `DQ-53` reveals a WMS | FG-09 and FG-18 change; inventory ownership moves |
| `DQ-22` excludes returns | FG-16 and R1–R5 are deferred; a control gap for damaged goods must still be described |
| `DQ-45`/`DQ-46` answered | FG-06 and FG-17 become configuration against a real policy, not a provisional one |

---

## 9. Weaknesses of this document

1. **No product, no evidence.** Every confidence grade is an inference about a market, not a test of software. This is the main limitation and it cannot be removed inside a fictional case study.
2. **Process findings are judgments.** §6 is the author's reading of Phase 1 evidence, not a validated change-readiness assessment.
3. **No effort estimate.** Counting configured requirements would create a false precision. Effort depends on product, team, data quality, and decisions — none of which exist here.
4. **Extension risk is understated by design.** `Low` confidence on FG-21 and FG-22 is deliberate: both could expand after product validation.

---

## 10. Change log

| Version | Date | Change |
|---|---|---|
| 0.1 | 12 Nov 2026 | Initial fit-gap register: 23 findings across FR-01…FR-23, 8 process findings, validation plan |
