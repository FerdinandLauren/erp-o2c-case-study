# Role and Authorization Matrix

**Document ID:** RAM-01
**Version:** 0.1 (Draft)
**Phase:** 3 — Fit-Gap Analysis and Solution Blueprint
**Inputs:** `Design.md` §5; `docs/stakeholder-analysis.md` §3–§4; `docs/to-be-process.md` §3–§7; `docs/requirements.md` (FR-15, FR-16, FR-17, FR-20; NFR-01, NFR-02; BR-08, BR-12, BR-15)
**Status:** Draft — proposed access design; organisation structure unconfirmed (`DQ-49`, `DQ-50`)

---

## 1. Purpose

`NFR-01` requires that access follows role-based authorization, and `BR-08` requires that users only reach transactions permitted by their role. This document defines the roles, the permissions, and — more importantly — the **conflicts** that must not be granted to one person.

It is a design proposal, not a security configuration. No product roles, authorisation objects, or profile names are used.

---

## 2. Design rules

| Rule | Consequence |
|---|---|
| Least privilege | A role gets the minimum actions needed for its stage, not the module |
| One owner per decision | Approval, credit release, return authorisation, and write-off each have exactly one accountable role |
| No self-approval | A user cannot approve a document they created, at any value |
| Status gates permission | A permission applies to a status, not to an object in general: "change order" means "change a draft order" |
| Technical access is not business authority | An administrator can configure a rule but cannot approve, post, or release a business document |
| Deactivation over deletion | No role can delete a posted document, customer, or product (`BR-13`, `BR-15`) |

---

## 3. Role catalogue

| ID | Role | Business function | Primary purpose | Stakeholder |
|---|---|---|---|---|
| ROL-01 | Sales Representative | Sales | Capture and maintain draft orders | STK-01 |
| ROL-02 | Sales Manager | Sales | Approve commercial exceptions; own sales reporting | STK-02 |
| ROL-03 | Master Data Specialist | Master Data / Sales Ops | Create and maintain customer and product records | STK-03 |
| ROL-04 | Inventory Controller | Inventory | Own stock accuracy and availability commitments | STK-04 |
| ROL-05 | Warehouse Operator | Warehouse | Pick, pack, and confirm goods issue | STK-05 |
| ROL-06 | Logistics Coordinator | Logistics | Manage shipment, delivery status, and POD | STK-06 |
| ROL-07 | Finance / AR Officer | Finance | Post invoices, record payments, allocate cash | STK-07 |
| ROL-08 | Finance Manager | Finance | Own credit, receivables control, return authorisation, financial exceptions | STK-08 |
| ROL-09 | ERP Administrator | IT | Configure and support; no business authority | STK-10 |

**Not a role:** "Management" (`STK-09`) receives reports but does not transact. Report access is granted to ROL-08 and to a read-only management view, not to an operational role.

**Why ROL-09 exists despite `PRD.md` not naming it:** `Design.md` §5 includes an ERP Administrator, and any real system has one. The important design point is that the administrator has **no business approval by default** (`Design.md` §5), which is the control that prevents a technical user from becoming an invisible approver.

---

## 4. Permission matrix

Legend: **C** create · **R** read · **U** change (within permitted status) · **A** approve / post / release · **—** no access. `own` = only documents the user created or is assigned to.

| Role | Customer master | Product master | Sales order | Pricing | Credit limit | Delivery / POD | Invoice | Payment / allocation | Returns / credit note | Reports | Audit log |
|---|---|---|---|---|---|---|---|---|---|---|---|
| ROL-01 Sales Rep | R | R | C, R own, U own draft | R | R own customers | R own orders | R own customers | — | C request | Own portfolio | — |
| ROL-02 Sales Manager | R | R | R team, A band B | R, A override | R | R team | R team | — | R | Sales | R team |
| ROL-03 Master Data | C, R, U | C, R, U | — | R | R | — | — | — | — | Data quality | R master data |
| ROL-04 Inventory Controller | R | R | R | — | — | R, U availability | — | — | R goods receipt | Inventory | R stock |
| ROL-05 Warehouse Operator | R ship-to | R | R fulfilment view | — | — | C, U pick / pack / issue | — | — | U receive | Fulfilment | — |
| ROL-06 Logistics Coordinator | R | R | R | — | — | C, U, A POD | R | — | C collection | Delivery | — |
| ROL-07 Finance / AR Officer | R | R | R | R | R | R | C, A post | C, U allocate | A post credit note | AR | R financial docs |
| ROL-08 Finance Manager | R | R | R | R | C, U, A | R | R, A release | R, A write-off / difference | A authorise | All finance | R |
| ROL-09 ERP Administrator | R (support) | R (support) | R (support) | R config | R config | R support | R support | R support | R support | Technical | R technical |

### 4.1 Reading the matrix correctly

- **Read does not mean export.** `NFR-11` requires structured export for audit; export is granted to ROL-08 and ROL-09 only, and logged.
- **`A` is always scoped.** ROL-02's `A band B` does not approve band C; ROL-07's `A post` does not authorise a credit note; ROL-08's `A` does not include posting.
- **ROL-07 creating a credit note is deliberately blocked.** The officer posts the credit note *after* ROL-08 authorises it. If the same person could authorise and post, `BR-12` would be decorative.
- **ROL-04 and ROL-05 are separated** although both touch stock: the controller owns the quantity, the operator executes the movement. Collapsing them removes an inventory control (`docs/stakeholder-analysis.md` §3.1).

---

## 5. Critical transaction matrix

The permission matrix says who can read a module. This says who can perform the actions that move money or change control state.

| Transaction | ROL-01 | ROL-02 | ROL-03 | ROL-04 | ROL-05 | ROL-06 | ROL-07 | ROL-08 | ROL-09 |
|---|---|---|---|---|---|---|---|---|---|
| Create customer or product | — | — | **Yes** | — | — | — | — | — | — |
| Change credit limit | — | — | — | — | — | — | — | **Yes** | — |
| Create sales order | **Yes** | Yes | — | — | — | — | — | — | — |
| Confirm / submit order | **Yes** | Yes | — | — | — | — | — | — | — |
| Approve order — band A | auto | — | — | — | — | — | — | — | — |
| Approve order — band B | — | **Yes** | — | — | — | — | — | — | — |
| Approve order — band C | — | Yes (commercial) | — | — | — | — | — | **Yes** (financial) | — |
| Release credit-blocked order | — | — | — | — | — | — | — | **Yes** | — |
| Reserve / release stock | — | — | — | **Yes** | — | — | — | — | — |
| Pick and pack | — | — | — | — | **Yes** | — | — | — | — |
| Confirm goods issue | — | — | — | — | **Yes** | Yes | — | — | — |
| Confirm POD | — | — | — | — | — | **Yes** | — | — | — |
| Post invoice | — | — | — | — | — | — | **Yes** | — | — |
| Record payment | — | — | — | — | — | — | **Yes** | — | — |
| Allocate payment | — | — | — | — | — | — | **Yes** | — | — |
| Approve write-off / difference | — | — | — | — | — | — | — | **Yes** | — |
| Request return | **Yes** | Yes | — | — | — | Yes | — | — | — |
| Authorise return | — | — | — | — | — | — | — | **Yes** | — |
| Post credit note | — | — | — | — | — | — | **Yes** | — | — |
| Change approval matrix | — | — | — | — | — | — | — | — | **Yes** (config only) |
| Change a posted invoice | — | — | — | — | — | — | — | — | — |

**Two deliberate blanks:** no role can change a posted invoice (`BR-15`), and no role can approve its own return request (`BR-12`). Those blanks are the control.

---

## 6. Segregation-of-duties conflicts

A conflict exists when one person holding two permissions could complete a transaction without independent review. `RSK-16` notes that a small company cannot always separate every pair; the honest response is to name the conflict and the compensating control, not to pretend it is resolved.

| ID | Conflicting permissions | Risk if combined | Separable in wave 1? | Compensating control |
|---|---|---|---|---|
| SOD-01 | Create order + release credit block | Uncontrolled credit exposure | Yes (ROL-01 vs ROL-08) | None needed |
| SOD-02 | Maintain customer master + change credit limit | Inflated limit on a friendly customer | Yes (ROL-03 vs ROL-08) | Master data change report reviewed monthly |
| SOD-03 | Request return + authorise return | Fictitious returns and credits | Yes (ROL-01/06 vs ROL-08) | Authorisation log with reason |
| SOD-04 | Authorise return + post credit note | Unreviewed credit issued | Yes (ROL-08 vs ROL-07) | Segregated by design |
| SOD-05 | Maintain price list + approve price override | Margin erosion with no challenge | **Partly** | Price-override report reviewed by ROL-08 |
| SOD-06 | Create invoice + record/allocate payment | Invoice cleared without cash | **No** in a small AR team | Weekly allocation review by ROL-08; bank reconciliation independent |
| SOD-07 | Record payment + reconcile bank | Payment recorded but not received | **Partly** | Finance Manager reviews bank reconciliation, not the officer |
| SOD-08 | Change credit limit + release blocked order | Limit raised simply to unblock | **Partly** | Monthly credit-release report to Management (`STK-09`) |
| SOD-09 | Configure approval matrix + approve an order | Self-approval through configuration | Yes (ROL-09 vs ROL-02/08) | Administrator cannot hold a business role |
| SOD-10 | Change master data + create transactions | Order keyed against an unapproved record | **Partly** | Master data activation report; no transaction in the same session |

**Interpretation:** four of ten conflicts are fully separable in this design. Four are only partly separable, and two are accepted with a compensating control. The accepted ones are the ones a real small distributor would also accept — which is exactly why they must be written down (`RSK-16`).

---

## 7. Authorisation behaviour by status

`FR-20` requires valid transitions only. Permission is therefore a function of (role, object, status).

| Object | Status | Who may change it | What is blocked |
|---|---|---|---|
| Sales order | `Draft` | ROL-01 (own), ROL-02 | Confirmation while incomplete |
| Sales order | `Submitted` | ROL-02, ROL-08 (credit) | Editing lines after submission |
| Sales order | `Approved` | ROL-01 (cancel with reason) | Re-approval; price change |
| Sales order | `Released` | ROL-04 / ROL-05 (fulfilment only) | Any commercial change |
| Sales order | `Closed` | Nobody | Reopening; reversal requires a new document |
| Delivery | `POD Confirmed` | Nobody | Editing quantity or date |
| Invoice | `Posted` | Nobody | Editing; deletion; renumbering |
| Payment | `Allocated` | ROL-07 (reallocation with reason) | Allocation above payment value |
| Return | `Authorised` | ROL-05 (receive only) | Changing reason or quantity |
| Return | `Credited` | Nobody | Further credit against the same return |

---

## 8. Access governance

| Control | Design | Verification |
|---|---|---|
| Role assignment | Named individual, approved by function head; no shared accounts | Access list reviewed at UAT (`NFR-01`) |
| Periodic review | Quarterly; leavers removed within one working day | Review record |
| Emergency access | Time-bound, logged, requires ROL-08 approval | Emergency log reviewed monthly |
| Administrator activity | Configuration changes logged; no business document creation | Audit extract (`FR-15`, `NFR-11`) |
| Segregation test | Negative tests attempt each SOD pair | Phase 4 UAT scenarios |
| Session and authentication | Organisation standard (`NFR-03`); MFA where available | IT architecture review |

---

## 9. Phase 4 verification hooks

| Acceptance criterion | How it is tested against roles |
|---|---|
| AC-04 — user without Sales order role cannot create an order | Log in as ROL-05; attempt order creation; expect denial and security event |
| AC-16 — requester cannot authorise own return | Create return as ROL-01; attempt authorisation; expect refusal |
| AC-18 — credit-blocked order requires Finance decision | Confirm order above limit as ROL-01/02; attempt release; expect block until ROL-08 acts |
| AC-15 — audit history is visible and not editable | Open change history as ROL-08 and ROL-09; attempt edit; expect refusal |
| NFR-01 — no role exceeds scope | Execute the critical transaction matrix in §5 as every role; record actual versus expected |

---

## 10. Open items

| Item | Why open | Effect if unresolved |
|---|---|---|
| Organisation structure and role holders | `DQ-49` | Role catalogue may not match the company |
| Whether Finance/AR is one person or several | `DQ-50` | SOD-06 and SOD-07 cannot be separated |
| Whether master data sits in Sales Ops | `DQ-08`, `AS-15` | ROL-03 may collapse into ROL-01, creating SOD-10 |
| Approval thresholds | `DQ-46` | Band A/B/C boundaries are placeholders |
| Credit authority | `DQ-45` | ROL-08's release authority is proposed, not confirmed |
| Product authorisation model | `DQ-61`, `DQ-63` | Role design may need remapping to product objects |

**Limit:** this matrix defines intent. A product authorisation design can implement it, weaken it, or make part of it impossible. It must be re-validated after product selection, and negative tests are the only proof it works.

---

## 11. Change log

| Version | Date | Change |
|---|---|---|
| 0.1 | 12 Nov 2026 | Initial role catalogue (9 roles), permission matrix, critical transaction matrix, 10 SOD conflicts, status-based authorisation, Phase 4 test hooks |
