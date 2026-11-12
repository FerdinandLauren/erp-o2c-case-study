# Cutover Plan and Go/No-Go Criteria

**Document ID:** CUT-01
**Version:** 0.1 (Draft)
**Phase:** 5 — Deployment and Portfolio Preparation
**Inputs:** `docs/data-migration-plan.md`, `docs/UAT-plan.md`, `docs/integration-inventory.md`, `docs/role-authorization-matrix.md`, `docs/functional-blueprint.md`
**Status:** Draft — plan only; no cutover has been performed and no system exists

---

## 1. Purpose

Cutover is the short window where a working business stops using one way of recording orders and starts using another. Everything before it builds the capability; cutover is where the business either keeps trading or does not.

This plan defines:

- what must be true before the switch is attempted (entry criteria);
- the sequence of the switch itself (runbook);
- who decides to proceed, pause, or reverse (go/no-go and rollback);
- how the first days are controlled.

It does **not** claim a date, a duration in hours, or a team size. Those depend on record volumes, rehearsal results, and people that do not exist in this simulation (`DQ-05`, `DQ-57`). Durations below are relative phases (`T-n`), not clock times.

---

## 2. Cutover strategy

| Option | Description | Fit for this design | Verdict |
|---|---|---|---|
| Big bang | All O2C scope switches in one window; legacy becomes read-only | Small single-entity distributor; one currency; no manufacturing | **Chosen**, with a controlled rollback |
| Phased by process | Orders first, then billing, then AR over several weeks | Creates two sources of truth for the same order; reconciliation cost is high | Rejected for wave 1 |
| Parallel run (full) | Both systems record every transaction until confidence is high | Doubles data entry and creates conflicting invoices | Rejected; only AR and stock get a controlled overlap |
| Pilot by branch or customer group | One warehouse or customer segment first | Attractive, but master data and credit limits are shared, so the boundary leaks | Deferred to a later wave if `DQ-01` expands scope |

**Why big bang is defensible here:** the process boundary is one legal entity, one currency, and one order-to-cash chain. Splitting the chain across two systems is the failure mode this design is built to avoid (`docs/functional-blueprint.md` §2). The risk is managed by rehearsal, a freeze, and a rollback trigger — not by a longer parallel run.

---

## 3. Scope of the switch

| In scope at cutover | Explicitly out of scope |
|---|---|
| Customer, product, warehouse master (`DM-01`–`DM-03`) | Procurement, general ledger, manufacturing (`AS-08`) |
| Price conditions and credit limits (`DM-04`, `DM-05`) | Closed historical transactions (stay in legacy archive) |
| Open sales orders (`DM-06`) | Payroll, HR, fixed assets |
| Opening inventory (`DM-07`) | CRM / customer portal (`INT-05`) |
| Open deliveries and POD (`DM-08`) | WMS (`INT-04`) unless already operating |
| Open invoices and AR balance (`DM-09`) | Carrier tracking (`INT-03`) |
| Unapplied payments and allocations (`DM-10`) | Bank API integration (`INT-01`) — manual upload is the baseline |
| Open returns and credit notes (`DM-11`) | Reporting platform (`INT-06`) — standard reports cover the gap |
| Configuration: approval bands, number ranges, status profiles (`DM-12`) | Custom development of any kind |

**Interfaces required at go-live:** `INT-02` tax/e-invoice **if** `DQ-37` establishes a statutory mandate, and `INT-07` document output. Every other interface has a designed manual fallback (`docs/integration-inventory.md` §7).

---

## 4. Entry criteria

Cutover cannot start until every item is evidenced. "Mostly ready" is a no.

| # | Criterion | Evidence | Owner | Status |
|---|---|---|---|---|
| E1 | All `Must` UAT scenarios executed and passed | Signed UAT summary (`docs/UAT-plan.md` §4.2) | Test lead | Not met — no system |
| E2 | No open Severity 1 or 2 defect | Defect log | Test lead | Not met |
| E3 | Migration rehearsal R2 passed; checklist signed | `docs/data-migration-plan.md` §8 | Data lead + Finance | Not met |
| E4 | Opening AR balance reconciles to the source control total | F1 signed | ROL-08 | Not met |
| E5 | Opening stock counted, reconciled, and frozen | F5, S7 signed | ROL-04 | Not met |
| E6 | Roles assigned to named users; access tested | Access list; negative tests `UAT-28` | ROL-09 + function heads | Not met |
| E7 | Approval, credit, and tolerance parameters approved in writing | Signed policy values (`DQ-45`, `DQ-46`, `DQ-26`) | Sponsor + ROL-08 | Not met |
| E8 | Tax/e-invoice position confirmed and, if mandated, live | `INT-02` status | ROL-08 | Not met — `DQ-37` open |
| E9 | Invoice and credit-note output tested end to end | `INT-07` test evidence | ROL-07 | Not met |
| E10 | Training completed for every role; attendance recorded | Training register (`docs/change-management-plan.md` §6) | Change lead | Not met |
| E11 | Freeze and communication plan issued to all staff and key customers | Issued notice | Sponsor | Not met |
| E12 | Rollback decision owner named and available for the whole window | This document, §7 | Sponsor | Met by designation |

**Hard gate:** E1–E5 and E8 cannot be waived. A sponsor may accept a written risk on E6, E9, or E10; no one may accept a risk on financial reconciliation or statutory invoicing.

---

## 5. Cutover runbook

Relative phases. Each phase has an exit condition; the next phase does not start until it is met.

| Phase | Activity | Lead | Exit condition |
|---|---|---|---|
| T-10 | Final data freeze announced for master data and prices | ROL-03 | No new customer, product, or price except approved exceptions |
| T-7 | Order entry freeze announced; open order list cut | ROL-02 | No new orders in legacy; open order report captured |
| T-5 | Final source extract and validation | Data lead | Extract complete; control totals recorded |
| T-4 | Load master data (`DM-01`–`DM-05`) | ROL-09 | Counts and duplicates reconcile |
| T-3 | Load open orders and opening stock (`DM-06`, `DM-07`) | ROL-09 + ROL-04 | Open quantity and stock quantity reconcile |
| T-2 | Load deliveries, invoices, payments, returns (`DM-08`–`DM-11`) | ROL-09 + ROL-08 | AR, unapplied cash, and credit totals reconcile |
| T-1 | Full reconciliation and rejection resolution | Business owners | Checklist R1–R11, F1–F8, S1–S10 signed |
| T-1 | Go/No-Go meeting | Sponsor | Written decision with named attendees |
| T-0 | Switch: legacy becomes read-only; new system opens for order entry | Sponsor | First live order accepted |
| T+1 | First live delivery and first live invoice | ROL-05 / ROL-07 | Document chain order → delivery → invoice intact |
| T+2 | First live cash application | ROL-07 | Payment matched to a live invoice; unapplied cash visible |
| T+3 | First live return and credit note | ROL-08 / ROL-07 | Credit note references delivery and invoice |
| T+5 | Legacy write access removed; read access per retention | ROL-09 | Access review signed |

**Sequence rules:**

1. Master data before transactions. A transaction loaded against missing master data becomes an orphan that reconciliation cannot fix.
2. Counts before values. A matching financial total with missing rows is a coincidence, not a success.
3. No manual correction in the target. A rejected record goes back to its owner, is corrected at source, and is reloaded.
4. One decision owner. During the window, the sponsor decides; committees do not.

---

## 6. Go/No-Go decision

### 6.1 Criteria

| Dimension | Go | No-Go |
|---|---|---|
| Financial reconciliation | AR, unapplied cash, stock, and open order value reconcile within stated tolerance | Any unexplained difference above zero |
| UAT | 100% `Must` scenarios passed; no S1/S2 open | Any `Must` scenario failed or unrun |
| Data quality | Rejected records all resolved or accepted in writing with owner and date | Rejection register incomplete |
| Statutory invoicing | Tax/e-invoice position confirmed and, if mandated, tested | `DQ-37` unresolved or channel untested |
| Access | Every role assigned and negative tests passed | Shared accounts or untested roles |
| Training | All roles trained; key users confident | Key users not trained |
| Support | Hypercare team named and reachable | No named first-line support |
| Rollback | Rollback tested or explicitly accepted as untested with a reason | No rollback plan |

### 6.2 Decision record

| Field | Value |
|---|---|
| Decision | Go / No-Go / Conditional Go |
| Decision owner | Sponsor (`STK-09`) |
| Attendees | Sponsor, ROL-08, ROL-02, ROL-04, ROL-06, ROL-09, change lead, test lead |
| Evidence reviewed | Reconciliation checklist, UAT summary, defect log, training register |
| Conditions (if conditional) | Named, time-bound, and owned — not "monitor" |
| Date and time | To be recorded at the meeting |
| Signature | Sponsor |

**Rule:** a "conditional go" is only valid if every condition has an owner and a deadline before the first live invoice. Otherwise it is a no-go with optimistic wording.

---

## 7. Rollback

Rollback is not "undo the project". It is a bounded decision to return to the legacy process while the cause is fixed.

| Question | Position |
|---|---|
| Trigger | A Severity 1 defect with no workaround that affects invoicing, cash application, or stock accuracy; or a reconciliation difference that cannot be explained within the cutover window |
| Decision owner | Sponsor, on the advice of ROL-08 and the test lead |
| Time limit | Defined by how long the business can operate without issuing invoices — not by pride. It is agreed before cutover, not during it |
| What happens to data | Transactions created in the new system are exported and re-keyed into the legacy process under a controlled procedure; the new system is frozen, not deleted |
| What is preserved | The reconciliation evidence, the defect record, and the decision log |
| Re-entry | A new go/no-go meeting after the cause is fixed and the failed phase is re-rehearsed |

**Honest limitation:** a true rollback after live invoicing is expensive and may be impossible if statutory numbers have been issued. That is precisely why E1–E5 and E8 are hard gates. The plan states a rollback option; it does not pretend rollback is cheap.

---

## 8. Communication

| Audience | Message | When | Owner |
|---|---|---|---|
| All staff | Freeze dates, what stops, what starts, who to ask | T-10 and T-2 | Change lead |
| Sales | No new orders after the freeze; how to handle urgent requests | T-7 | ROL-02 |
| Warehouse | Stock freeze and count instructions | T-7 | ROL-04 |
| Finance | Reconciliation schedule and sign-off expectations | T-5 | ROL-08 |
| Key customers | Invoice and delivery contact changes, if any | T-5 | ROL-02 |
| Management | Go/No-Go outcome, first-day status | T-1 and daily during hypercare | Sponsor |

**Rule:** no customer is told "the system is live" until the first live invoice has been issued successfully. Announcing a switch before it works converts an internal delay into an external credibility problem.

---

## 9. Cutover risks

| Risk | Trigger | Impact | Mitigation |
|---|---|---|---|
| Reconciliation difference discovered late | Poor source data | Go-live delay | Rehearsal R2; rejection register owned by business |
| Freeze leakage | Orders keyed during extract | Missing or duplicate orders | Written freeze, delta procedure, named gatekeeper |
| Key person unavailable | Single-trained AR officer | Cash application stalls | Cross-training; two people per critical role (`RSK-15`) |
| Statutory invoice channel not ready | `DQ-37` unresolved | Go-live blocked | Decide early; treat as critical path |
| Over-optimistic conditional go | Schedule pressure | Defects carried into live operation | Conditions must be owned and time-bound (§6.2) |
| Stock count disputed | No physical count discipline | Orders against phantom stock | Count and freeze before load (`DM-07`) |
| Users revert to spreadsheets | Training or trust gap | Two sources of truth | Hypercare presence; no parallel spreadsheet tolerated (`docs/change-management-plan.md` §7) |

---

## 10. What this plan does not establish

1. **No date.** A real cutover date follows rehearsal evidence; inventing one here would be theatre.
2. **No duration.** Load time depends on volumes and quality (`DQ-57`).
3. **No team size.** The RACI names roles, not headcount.
4. **No executed checklist.** Every entry criterion is `Not met`; this is the shape of readiness, not readiness itself.
5. **No statutory advice.** `DQ-37` remains unanswered; the plan flags it as a hard gate rather than guessing.

---

## 11. Change log

| Version | Date | Change |
|---|---|---|
| 0.1 | 12 Nov 2026 | Initial cutover plan: strategy options, scope, 12 entry criteria, relative runbook, go/no-go criteria and decision record, rollback, communication, risks |
