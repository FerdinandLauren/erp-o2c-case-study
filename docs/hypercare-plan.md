# Hypercare Plan

**Document ID:** HCP-01
**Version:** 0.1 (Draft)
**Phase:** 5 — Deployment and Portfolio Preparation
**Inputs:** `docs/cutover-plan.md`, `docs/UAT-plan.md`, `docs/role-authorization-matrix.md`, `docs/integration-inventory.md`, `docs/change-management-plan.md`
**Status:** Draft — plan only; no hypercare period has run

---

## 1. Purpose

Hypercare is the period immediately after go-live when the business is operating the new process for the first time and the project team is still present. It is not a second testing phase and it is not indefinite support. It has a defined purpose, a defined team, and a defined exit.

The purpose is narrow: **protect the first close cycle and the first month of trading** while the organisation moves from "the project team fixes it" to "the business runs it".

**What this plan is not:**

- a claim that hypercare happened;
- a service-level agreement (no product, vendor, or IT service organisation exists);
- a licence to keep changing the design after go-live. Changes follow change control, not enthusiasm.

---

## 2. Objectives

| Objective | Measure of success | Note |
|---|---|---|
| Business keeps trading | Orders, deliveries, invoices, and cash application continue daily | No fabricated volume target |
| First invoice and first cash application succeed | Both completed and reconciled | Checked in cutover runbook T+1, T+2 |
| Defects are triaged, not absorbed | Every issue has an ID, owner, and resolution | Severity model in §4 |
| Users become independent | Key users resolve routine questions without the project team | Assessed at exit, §8 |
| Controls stay switched on | Credit blocks, POD-before-invoice, allocation limits still enforced | A control bypassed in week 1 rarely returns |
| First management reporting cycle is correct | Reports reconcile to the ERP for the period | `NFR-08` |

---

## 3. Hypercare structure

| Window | Focus | Team presence |
|---|---|---|
| Days 1–5 (stabilisation) | First orders, deliveries, invoices, cash application; defect triage | Full core team on site or on call during business hours |
| Week 2 (stabilise reporting) | First weekly reporting; ageing and credit reports; exception backlog | Core team reduced; daily stand-up |
| Weeks 3–4 (transition) | Key users own first-line support; project team handles exceptions only | Project team on call; twice-weekly review |
| Exit | Criteria in §8 met | Team withdraws; support moves to business-as-usual |

**Duration:** four weeks is the proposed baseline (`AS-11`). It is a plan, not a commitment; the exit criteria decide when hypercare ends, not the calendar.

---

## 4. Severity model

| Severity | Definition | Example | Target response | Target resolution |
|---|---|---|---|---|
| S1 — Critical | Business cannot invoice, ship, or apply cash; financial or statutory impact | Invoice posting fails for all deliveries; duplicate invoice created | Immediate, all available | Workaround or fix same day; escalation to sponsor |
| S2 — Major | A required control or process step fails for a group of users or documents | Credit block not enforced for one order type; POD field not mandatory | Within business hours | Before the next reporting cycle |
| S3 — Minor | Process works with a documented workaround | Report column order wrong; message unclear | Next business day | Within the hypercare window |
| S4 — Cosmetic | No functional impact | Label alignment | Logged | Backlog |

**Rules:**

1. A workaround is recorded, not improvised. An undocumented workaround becomes the real process.
2. No S1 or S2 is closed by changing the expected result. If the requirement was wrong, it goes through change control.
3. A control disabled "temporarily" to keep trading needs sponsor approval, a named owner, and an end date. Temporary controls have a way of becoming permanent.

---

## 5. Team and responsibilities

| Role | Hypercare responsibility | Named person |
|---|---|---|
| Hypercare lead | Runs the daily stand-up, owns the issue log, escalates S1/S2 | ERP Functional Consultant (author) |
| Business process owners | Decide business questions; approve workarounds; sign off area stability | ROL-02, ROL-08, ROL-06 |
| Key users | First-line support in their own role; reproduce and document issues | Sales, AR, Warehouse key users |
| IT / environment | Access, performance, interface monitoring, data refresh | IT / Integration Team |
| Data steward | Master data corrections and duplicate resolution | ROL-03 |
| Sponsor | Decides escalation, control exceptions, and hypercare exit | `STK-09` |

**Ownership rule:** the project team owns the issue until it is either fixed or formally handed to the business with an agreed workaround. "It works on my machine" is not a handover.

---

## 6. Daily and weekly cadence

| Ceremony | Frequency | Participants | Output |
|---|---|---|---|
| Hypercare stand-up | Daily, 15 minutes | Hypercare lead, key users, IT | Issue log updated; blockers named |
| Triage | Daily, after stand-up | Lead + process owner for affected area | Severity, owner, target date |
| Sponsor update | Daily for week 1; weekly thereafter | Sponsor, hypercare lead | One-page status: S1/S2, controls, reconciliation |
| Reporting reconciliation | Weekly | ROL-08, ROL-02 | Report totals versus ERP totals |
| Retrospective | End of each week | Core team | What broke, what changed, what to stop doing |
| Exit review | End of window | Sponsor and process owners | Exit decision against §8 |

**Meeting discipline:** a stand-up that reviews the same unresolved issue for five days is a signal that the issue is owned by the wrong person.

---

## 7. Monitoring and controls

Hypercare is where controls are most likely to be quietly bypassed. These are checked, not assumed.

| Control | What is checked | Frequency | Owner |
|---|---|---|---|
| POD before invoice | Sample of invoices has a POD reference and confirmed delivery | Daily, week 1; weekly after | ROL-08 |
| Credit block | Blocked orders have a Finance release with reason and timestamp | Daily, week 1 | ROL-08 |
| Price override | Overrides have an approver and a reason | Weekly | ROL-02 |
| Allocation limit | No payment allocated above its received amount | Daily, week 1 | ROL-07 |
| Duplicate documents | No duplicate invoice per delivery; no duplicate order | Daily, week 1 | ROL-07 |
| Status integrity | No document edited outside its allowed transitions | Weekly | ROL-09 |
| Master data quality | New master records have tax ID, terms, and owner | Weekly | ROL-03 |
| Reconciliation | AR ageing agrees with the ledger extract | Weekly | ROL-08 |
| Access | No shared accounts; leavers removed | Weekly | ROL-09 |

**Reporting rule:** if a control check cannot be evidenced, it is treated as failed. "We believe it is working" is not a control result.

---

## 8. Exit criteria

Hypercare ends when all of the following are true and evidenced:

| # | Criterion | Evidence |
|---|---|---|
| X1 | No open S1 or S2 issue | Issue log |
| X2 | First month-end or first reporting cycle completed and reconciled | Signed reconciliation |
| X3 | Key users resolve routine questions without the project team for two consecutive weeks | Support log trend |
| X4 | Every control in §7 checked and passing for the final two weeks | Control checklist |
| X5 | Support model agreed: who takes first-line, who is second-line, how changes are requested | Signed support handover |
| X6 | Open S3/S4 issues have an owner and a date, or are accepted into the backlog | Backlog |
| X7 | Process owners sign off their area | Signed exit record |

**Exit rule:** withdrawal is a decision, not a date. If X1–X7 are not met, hypercare extends with a named reason; it does not end because the calendar says so.

---

## 9. Common failure modes

| Failure mode | Why it happens | Prevention |
|---|---|---|
| Project team becomes permanent support | No handover plan | X3 and X5 make handover an exit condition |
| Controls bypassed to clear a backlog | Pressure to invoice or ship | §7 checks; sponsor approval required for any exception |
| Issues closed without evidence | Triage fatigue | Closure requires a reproduction and a result |
| Same defect reopened repeatedly | Fix applied to symptom | Root cause recorded; change control if design is wrong |
| Users keep a shadow spreadsheet | Distrust or missing report | Named report owner; shadow files explicitly retired (`docs/change-management-plan.md` §7) |
| Hypercare ends too early | Optimism | Exit criteria, not calendar |
| Hypercare never ends | No exit owner | Sponsor owns X7 |

---

## 10. What this plan does not claim

1. **No execution.** No hypercare period has occurred.
2. **No service levels.** Response targets are planning intentions, not contractual SLAs.
3. **No volume or performance data.** `DQ-05` remains open; no throughput claim is made.
4. **No staffing commitment.** Roles are named; people and hours are not.
5. **No benefit claim.** The plan protects the process; it does not assert a financial outcome (`DEC-08`).

---

## 11. Change log

| Version | Date | Change |
|---|---|---|
| 0.1 | 12 Nov 2026 | Initial hypercare plan: objectives, four-week structure, severity model, team, cadence, control checks, exit criteria, failure modes |
