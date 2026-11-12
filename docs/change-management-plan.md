# Change Management and Training Plan

**Document ID:** CMP-01
**Version:** 0.1 (Draft)
**Phase:** 5 — Deployment and Portfolio Preparation
**Inputs:** `docs/fit-gap-analysis.md` §6 (`FG-P-01`–`FG-P-08`), `docs/role-authorization-matrix.md`, `docs/to-be-process.md`, `docs/UAT-plan.md`, `docs/hypercare-plan.md`
**Status:** Draft — plan only; no training has been delivered and no readiness assessment has been run

---

## 1. Purpose

An ERP design fails in the gap between "the system allows it" and "people do it". This plan addresses that gap: who is affected, what changes for them, what they must learn, how readiness is judged, and what happens when resistance is rational rather than obstructive.

It is built on one finding from Phase 3: **eight of the design's process findings are behaviour changes, not configuration** (`docs/fit-gap-analysis.md` §6). If training only teaches screens, those eight findings remain unimplemented.

**What this plan does not claim:** no training has been delivered, no adoption has been measured, and no productivity or benefit figure is asserted (`DEC-08`).

---

## 2. What actually changes

| Change | From | To | Most affected roles | Related finding |
|---|---|---|---|---|
| Order capture | Spreadsheet or email order | System order with validation and status | ROL-01 | `FG-P-04` |
| Approval | Informal manager consent | Value-band approval with recorded evidence | ROL-02, ROL-08 | `FG-P-01` |
| Credit control | Ad hoc, relationship-based | Limit check before confirmation; Finance releases blocks | ROL-08, ROL-01 | `FG-P-03` |
| Billing trigger | Inconsistent, sometimes before delivery | Confirmed POD only | ROL-07, ROL-06 | `FG-P-02` |
| Proof of delivery | Optional in practice | Mandatory reference before invoice | ROL-06, ROL-05 | `FG-P-05` |
| Pricing | Negotiated per order, weakly recorded | Maintained conditions; override needs approval and reason | ROL-01, ROL-02 | `FG-P-06` |
| Returns | Informal fix | Request → authorise → receive → credit note | ROL-01, ROL-08, ROL-07 | `FG-P-07` |
| Cash application | One person's knowledge | Documented matching rule; unapplied cash visible | ROL-07, ROL-08 | `FG-P-08` |
| Master data | Departmental lists | One owner per entity; duplicate prevention | ROL-03 | `FG-P-04` |
| Reporting | Rebuilt per request | One definition and cut-off per KPI | ROL-02, ROL-08 | `FG-P-04` |
| Audit | Reconstructed after the fact | System-recorded user, time, and reason | All roles | `FG-P-01`, `FG-P-05` |

**Design consequence:** the biggest adoption risk is not the invoice screen. It is that approval, credit, and POD become visible and non-negotiable.

---

## 3. Stakeholder impact assessment

Influence and interest are taken from `docs/stakeholder-analysis.md` §2.

| Stakeholder | Impact of change | Influence | Interest | Engagement approach | Main resistance risk |
|---|---|---|---|---|---|
| STK-01 Sales Representative | Daily order entry changes; validation blocks incomplete orders | Low | High | Hands-on training, first-line key user, quick-reference card | "Extra steps slow me down" |
| STK-02 Sales Manager | Approvals become recorded and value-banded; cannot approve own orders | High | High | Decision-rights workshop; approval report preview | Loss of informal discretion |
| STK-03 Master Data / Sales Ops | Becomes a named owner; duplicate prevention blocks familiar shortcuts | Medium | High | Data ownership workshop; duplicate resolution clinic | "I know the customer, the system does not" |
| STK-04 Inventory Controller | Owns availability and stock accuracy; confirms reservations | Medium | High | Stock count and ATP training | Blame for old stock errors |
| STK-05 Warehouse Operator | Picks, packs, and confirms goods issue in the system | Low | High | Task-based training at the terminal | "The paper list was faster" |
| STK-06 Logistics Coordinator | POD becomes mandatory and consequential | Medium | High | POD capture drill; exception handling | Drivers cannot always obtain a signature |
| STK-07 Finance / AR Officer | Posts invoices, records payments, allocates cash; cannot self-authorise credits | High | High | Scenario-based training with UAT data | Workload spike at cutover |
| STK-08 Finance Manager | Owns credit, AR control, return authorisation, differences | High | High | Control design review; sign-off role in UAT | Accountability without authority over Sales |
| STK-09 Management / Sponsor | Sees one version of the numbers; approves go/no-go and control exceptions | High | Medium | Decision briefings; exception reporting | Impatience with reconciliation discipline |
| STK-10 ERP Administrator | Configures, supports, cannot transact | Medium | Medium | Configuration and audit training | Becoming an invisible approver |

**High-impact, low-influence groups (STK-01, STK-05)** are where adoption usually fails quietly. They get the most practice time, not the most slides.

---

## 4. Guiding principles

| Principle | Practical meaning |
|---|---|
| Explain the why before the how | A user who understands the control tolerates the extra field; a user who does not, works around it |
| Train the role, not the module | Scenarios start from "you are the AR officer with a short payment", not from a screen tour |
| One source of truth in training | Training uses the same scenarios, data, and terminology as `docs/UAT-plan.md`; a second vocabulary creates a second process |
| Managers own adoption | The Sales Manager, not the project team, is accountable for Sales using the system |
| No shadow process | Spreadsheets and email approvals are retired explicitly, with a date, not gradually |
| Resistance is data | A refusal often identifies a real design problem; it is logged and assessed, not dismissed |
| Readiness is evidenced | Attendance is not competence; a role is ready when its user completes the scenario unaided |

---

## 5. Training approach

| Audience | Format | Content | Duration | Evidence of competence |
|---|---|---|---|---|
| All users | 60-minute orientation | Why the project exists, what changes, what does not, support model | 1 hour | Attendance and questions log |
| ROL-01 Sales Rep | Hands-on, scenario-based | Order creation, validation, price override request, order status, credit block message | Half day | Create an order and handle a blocked one unaided |
| ROL-02 Sales Manager | Workshop + hands-on | Approval bands, evidence, rejection, team reporting | Half day | Approve, reject, and explain an approval decision |
| ROL-03 Master Data | Workshop + clinic | Duplicate rules, required fields, deactivation, change log | Full day | Create and reject a duplicate record correctly |
| ROL-04 Inventory | Hands-on | Availability, reservation, stock count, backorder release | Half day | Confirm availability and release a backorder |
| ROL-05 Warehouse | Task-based at terminal | Pick, pack, goods issue, short pick, return receipt | Half day | Complete a pick/pack/issue without help |
| ROL-06 Logistics | Drill | Delivery status, POD capture, failed delivery, reschedule | Half day | Record a POD with evidence reference |
| ROL-07 Finance / AR | Scenario-based | Invoice from delivery, payment entry, allocation, unapplied cash, credit-note posting | Full day | Clear an invoice and handle a short payment |
| ROL-08 Finance Manager | Workshop | Credit limits, release, return authorisation, write-off, reconciliation | Full day | Release a blocked order with reason; authorise a return |
| ROL-09 ERP Administrator | Technical + control | Configuration, number ranges, status profiles, audit extract, no business approval | Full day | Change a parameter and show the audit entry |
| Sponsor / Management | Briefing | Go/no-go evidence, control exceptions, reporting definitions | 2 hours | Signed go/no-go understanding |

**Training rules:**

1. Every role trains on its **own** transactions, using the critical transaction matrix (`docs/role-authorization-matrix.md` §5).
2. Negative behaviour is trained, not just the happy path: what the system refuses and why.
3. Key users are trained before go-live and act as first-line support during hypercare.
4. Training material is versioned with the design. A stale screenshot is a defect.
5. Attendance is recorded; competence is assessed by doing, not by sitting.

---

## 6. Training schedule and register

| When | Activity | Owner | Output |
|---|---|---|---|
| T-20 | Training needs confirmed against the role catalogue | Change lead | Training matrix |
| T-15 | Materials built from To-Be stages and UAT scenarios | Functional consultant | Material v1.0 |
| T-12 | Key-user training (train the trainer) | Change lead | Key users certified |
| T-8 | Role training, wave 1 | Key users + consultant | Attendance and competence record |
| T-4 | Refresher and Q&A clinic | Change lead | Open questions closed |
| T-2 | Readiness check per role | Managers | Readiness sign-off |
| T+5 | Floor-walking and quick clinics | Key users | Issue log from training gaps |

**Register fields:** role, named user, session, date, attendance, competence result, refresher needed, sign-off by manager.

**Limit:** this schedule is relative (`T-n`) because no cutover date exists. It states sequence and dependency, not calendar dates.

---

## 7. Retiring the shadow process

A new system plus an old spreadsheet is worse than either alone. The following are explicitly retired at go-live, with a named owner.

| Shadow practice | Retired by | Owner | Evidence |
|---|---|---|---|
| Order spreadsheet / email order | Order entry only in the system | ROL-02 | Spreadsheet read-only; email address for orders redirected |
| Email approval | Approval recorded in the system | ROL-02 | Approval log |
| Personal price list | Maintained price condition | ROL-02 | Price condition list reviewed |
| Manual invoice register | Invoice list from the system | ROL-07 | Register archived |
| Personal customer list | Customer master | ROL-03 | Duplicate list retired |
| Manual stock sheet | System stock plus count | ROL-04 | Sheet archived after reconciliation |
| Ad hoc credit release by phone | Finance release with reason and timestamp | ROL-08 | Release log |
| Cash matching notebook | Allocation in the system | ROL-07 | Notebook archived after first reconciliation |

**Rule:** retiring a shadow file is a communication event, not a deletion. Users need to know where the information now lives.

---

## 8. Readiness assessment

Readiness is assessed per role, not per project. A project can be 90% ready and still fail because one role cannot perform its first transaction.

| Dimension | Question | Evidence | Status |
|---|---|---|---|
| Aware | Does the role know what changes and when? | Communication log | Not assessed |
| Capable | Can the user complete the role scenario unaided? | Competence record | Not assessed |
| Equipped | Does the user have access, device, and data? | Access test | Not assessed |
| Supported | Does the user know who to ask? | Support model issued | Not assessed |
| Willing | Does the user see the change as workable? | Feedback log; resistance register | Not assessed |
| Managerial | Has the line manager signed off? | Readiness sign-off | Not assessed |

**Readiness threshold for go-live:** every go-live role has at least one competent user and a named backup; no role depends on a single untrained person (`RSK-15`).

---

## 9. Resistance and adoption risks

| Risk | Signal | Response |
|---|---|---|
| Sales bypasses validation | Orders keyed as drafts and edited repeatedly; urgent requests by email | Sales Manager owns the behaviour; show the control's purpose; escalate repeat bypass |
| Approval treated as a formality | Same approver approves everything instantly | Review approval sample; challenge with evidence |
| Credit blocks fought | Repeated release requests without new information | Finance owns the rule; sponsor arbitrates policy, not individual orders |
| Warehouse reverts to paper | Pick list printed and system updated later | Terminal-level training; show the cost of late confirmation |
| AR officer overloaded | Backlog at first close | Cross-training; temporary reassignment; not control bypass |
| Key-user dependency | One person answers every question | Train two per critical role; document answers |
| "We will fix it after go-live" | Known defects deferred without owner | Change control; no open S1/S2 at go-live |
| Management asks for a parallel spreadsheet report | Distrust of the system report | Reconcile once, in public, and retire the copy |

**Resistance register fields:** role, behaviour observed, date, related design finding, response, owner, outcome. It is reviewed weekly during hypercare.

---

## 10. Communication plan

| Message | Audience | When | Channel | Owner |
|---|---|---|---|---|
| Why the project exists and what it will change | All staff | Before training | Town hall | Sponsor |
| What changes for your role | Each role | At training | Session | Change lead |
| Cutover dates and freezes | All staff | T-10 and T-7 | Notice | Change lead |
| Go-live confirmed (after first invoice) | All staff | T+1 | Notice | Sponsor |
| First-week issues and fixes | All staff | Daily during hypercare | Stand-up summary | Hypercare lead |
| What "done" looks like | All staff | At hypercare exit | Notice | Sponsor |

**Rule:** no message promises a benefit that has not been measured. "Faster" and "fewer errors" are hypotheses until the baseline exists (`DEC-08`).

---

## 11. What this plan does not establish

1. **No delivery.** No session has been run and no competence has been assessed.
2. **No adoption metric.** No usage, error-rate, or productivity baseline exists.
3. **No organisational authority.** Role holders are fictional; the plan names roles, not people.
4. **No benefit claim.** Adoption is a precondition for value, not evidence of it.
5. **No one-time change.** The plan covers go-live; continuous improvement needs a separate mechanism.

---

## 12. Change log

| Version | Date | Change |
|---|---|---|
| 0.1 | 12 Nov 2026 | Initial change management and training plan: change impact by role, 11 training tracks, shadow-process retirement, readiness assessment, resistance and communication plans |
