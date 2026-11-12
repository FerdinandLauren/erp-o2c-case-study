# UAT Plan and Scenario Catalogue

**Document ID:** UAT-01
**Version:** 0.1 (Draft)
**Phase:** 4 — Testing and Migration Readiness
**Inputs:** `docs/requirements.md` §4, `docs/traceability-matrix.md`, `docs/to-be-process.md`, `docs/role-authorization-matrix.md`, `docs/data-catalogue.md`
**Status:** Plan only — **no test has been executed**; there is no system to execute against

---

## 1. Purpose and honesty statement

This document defines how acceptance would be proven if an ERP were configured. It converts every acceptance criterion in `docs/requirements.md` §4 into a testable scenario with preconditions, steps, expected results, and a named role.

**What this is not:**

- It is not a test execution report. Every scenario status is `Not run`.
- It is not evidence that the design works. A test plan proves intent to verify, not verification.
- It is not a substitute for business sign-off. `Plan.md` §6, Gate 4 requires test readiness, not test success.

The value of the document is that it makes the design falsifiable: a reviewer can see exactly what would have to be true for each requirement to be accepted.

---

## 2. Scope

| In scope | Out of scope |
|---|---|
| O2C forward flow S1–S10 | Procurement (P2P), general ledger, manufacturing |
| Reverse flow R1–R5 | Payroll, HR, fixed assets |
| Role and authorisation negative tests | Penetration testing and security certification |
| Data migration reconciliation | Legacy archive design |
| Interface idempotency and error handling | Load, stress, and volume testing (no volumes known, `DQ-05`) |
| Report reconciliation for defined KPIs | Statutory audit |

**Interfaces tested:** `INT-01` bank, `INT-02` tax/e-invoice, `INT-07` document output. `INT-03`–`INT-06` are optional and would be tested only if procured.

---

## 3. Test approach

| Level | Purpose | Owner | Evidence |
|---|---|---|---|
| Unit / configuration test | One rule behaves as configured | Consultant | Configuration test log |
| Integration test | Documents flow across stages; references hold | Consultant + key user | Document chain screenshot and status |
| UAT | Business confirms the process works in their role | Process owner | Signed scenario record |
| Migration rehearsal | Load and reconcile a full data set | Data lead + Finance | Reconciliation checklist |
| Regression | Change after defect fix does not break a passed scenario | Consultant | Re-run record |

**Scenario design rules:**

1. Every `Must` acceptance criterion has at least one scenario. No exceptions.
2. Every negative rule (must be refused) has a scenario that tries to break it.
3. A scenario is written from the **actor's role**, not from the system's function, because adoption fails at the role boundary.
4. Expected result is stated before the test, never adjusted afterwards to match observed behaviour. A changed expectation is a design change and returns to `docs/requirements.md` §8.
5. Test data is synthetic (`DEC-04`). No real customer, product, or person is used.

---

## 4. Entry and exit criteria

### 4.1 Entry criteria

| Criterion | Evidence | Status |
|---|---|---|
| System configured for all Must requirements | Configuration checklist signed | Not met (no system) |
| Test environment available | Environment access confirmed (`DEP-07`) | Not met |
| Roles and users created | Role assignment list | Not met |
| Test data loaded and reconciled | Migration rehearsal checklist | Not met |
| Scenario catalogue approved | This document reviewed by process owners | Not met |
| Defect process agreed | Severity definitions (§9) accepted | Not met |

### 4.2 Exit criteria

| Criterion | Threshold |
|---|---|
| `Must` scenarios executed | 100% |
| `Must` scenarios passed | 100% |
| Severity 1 defects open | 0 |
| Severity 2 defects open | 0 (or written workaround accepted by sponsor) |
| Financial reconciliation | Zero difference; rounding differences documented and approved |
| Role negative tests | 100% denied as expected |
| Business sign-off | Named process owners per area |

**Gate rule:** a scenario that was never run cannot be counted as passed, and a requirement covered only by an unrun test is `Not verified`, not `Met`.

---

## 5. Roles in testing

| Role | Responsibility | Named person |
|---|---|---|
| Test lead | Plan, schedule, defect triage, reporting | ERP Functional Consultant (author) |
| Business process owner | Approve expected results; sign off area | Sales Manager, Finance Manager, Logistics Coordinator |
| Key user | Execute scenarios in their own role | Sales Rep, AR Officer, Warehouse Operator |
| IT / environment | Access, data refresh, interface stubs | IT / Integration Team |
| Data lead | Load, reconcile, obtain migration sign-off | Master Data + Finance |

**No self-certification:** the person who configured a rule cannot be the only person who signs it off. In this fictional case the author plays both roles, which is itself a limitation and is disclosed in §12.

---

## 6. Scenario catalogue

Types: **Normal** (expected path), **Edge** (boundary), **Negative** (must be refused).
Status: all `Not run`. Priority reflects business impact, not execution order.

| ID | Scenario | Type | Primary role | Expected result | Traces to | Priority | Status |
|---|---|---|---|---|---|---|---|
| UAT-01 | Standard order from entry to cleared invoice | Normal | Sales Rep → Finance/AR | Order, delivery, invoice, payment, and allocation are linked; invoice clears | AC-09, AC-11, AC-14; FR-03, FR-07, FR-10, FR-12, FR-13 | 1 | Not run |
| UAT-02 | Create customer with an existing tax ID | Negative | Master Data | Creation blocked; possible duplicate shown; no second record | AC-01; FR-01, FR-23 | 1 | Not run |
| UAT-03 | Create product with an existing code | Negative | Master Data | Creation blocked; conflicting product named | AC-02; FR-02 | 1 | Not run |
| UAT-04 | Authorised user creates and changes a draft order | Normal | Sales Rep | Only permitted fields change; each change writes an audit entry | AC-03; FR-03 | 2 | Not run |
| UAT-05 | Order creation by a warehouse user | Negative | Warehouse Operator | Action denied; security event recorded | AC-04; NFR-01 | 1 | Not run |
| UAT-06 | Confirm an order with a missing mandatory field | Negative | Sales Rep | Confirmation blocked; field named; order stays `Draft` | AC-05; FR-04 | 2 | Not run |
| UAT-07 | Order 80 when available-to-promise is 70 | Edge | Sales Rep | ATP shows 70; order cannot commit 80 without an exception | AC-06; FR-05 | 2 | Not run |
| UAT-08 | Release the same order to the warehouse twice | Negative | Sales Rep / Warehouse | No second fulfilment requirement; attempt logged | AC-07; FR-07 | 3 | Not run |
| UAT-09 | Record 12 picked against 10 ordered with 0% tolerance | Edge | Warehouse Operator | Excess rejected unless approved tolerance or reason exists | AC-08; FR-08, BR-14 | 2 | Not run |
| UAT-10 | Run billing twice for one delivery | Negative | Finance/AR | No second invoice; duplicate attempt logged | AC-10; FR-10 | 1 | Not run |
| UAT-11 | Apply two partial payments to one invoice | Normal | Finance/AR | Status `Partially Paid`, then `Cleared`; balance correct | AC-11; FR-11 | 1 | Not run |
| UAT-12 | Allocate 900 of a 1,000 payment across two invoices | Edge | Finance/AR | Payment `Partially Allocated`; 100 visible as unapplied cash | AC-12; FR-12 | 2 | Not run |
| UAT-13 | Allocate more than the payment amount | Negative | Finance/AR | Allocation rejected; payment unchanged | AC-13; FR-12 | 1 | Not run |
| UAT-14 | Inspect and attempt to edit transaction history | Normal | Auditor / Finance Manager | History shows user, time, action, reason; edit refused | AC-15; FR-15 | 2 | Not run |
| UAT-15 | Requester authorises their own return | Negative | Sales Rep | Authorisation refused; a different role must decide | AC-16; FR-16, BR-12 | 1 | Not run |
| UAT-16 | Accept a return and post a credit note | Edge | Warehouse → Finance | Credit note references delivery and invoice; receivable reduced | AC-17; FR-16, BR-15 | 2 | Not run |
| UAT-17 | Confirm an order above the credit limit | Negative | Sales Rep → Finance Manager | Order blocked or routed; release requires Finance reason and timestamp | AC-18; FR-17, BR-09 | 1 | Not run |
| UAT-18 | Override a maintained price without approval | Edge | Sales Rep | Override refused until an authorised approver records a reason | AC-19; FR-19, BR-11 | 2 | Not run |
| UAT-19 | Move a closed order back to released | Negative | Sales Rep | Transition refused; attempt logged | AC-20; FR-20 | 3 | Not run |
| UAT-20 | Print an invoice with complete statutory configuration | Normal | Finance/AR | Tax ID, tax amount, invoice number, and date present and reconciling | AC-21; FR-22 | 1 | Not run |
| UAT-21 | Post an invoice with missing statutory configuration | Negative | Finance/AR | Posting blocked; missing element named | AC-22; FR-22 | 1 | Not run |
| UAT-22 | Route band B and band C orders through approval | Normal | Sales Manager / Finance | B goes to Sales Manager; C requires both; A releases automatically | FR-06 (Should); BR-04 | 2 | Not run |
| UAT-23 | Invoice a delivery with no POD | Negative | Finance/AR | Invoice creation blocked until POD is confirmed | FR-09 (Should), BR-10 | 1 | Not run |
| UAT-24 | Release a backorder when stock arrives | Normal | Inventory Controller | Outstanding quantity confirmed; delivery created; customer date updated | FR-18 (Should) | 3 | Not run |
| UAT-25 | Reconcile AR ageing against the general ledger extract | Normal | Finance Manager | Totals agree for the period; differences explained | FR-14 (Should), NFR-08 | 2 | Not run |
| UAT-26 | Record an exception with reason, owner, and resolution | Edge | Sales Rep | Exception visible with all four fields; included in the exception report | FR-21 (Should) | 3 | Not run |
| UAT-27 | Create a customer whose name is a near match | Edge | Master Data | Warning shown; reason required; record created and flagged | FR-23 (Should) | 2 | Not run |
| UAT-28 | Sweep the role matrix with every role | Negative | All roles | Every action outside the role matrix is denied | NFR-01; SOD-01…SOD-10 | 1 | Not run |
| UAT-29 | Replay a bank statement file | Edge | Finance/AR | One payment results; duplicate line ignored and logged | INT-01; FR-12 | 2 | Not run |
| UAT-30 | Receive a tax-channel rejection | Negative | Finance/AR | Invoice not treated as valid; exception assigned with reason | INT-02; FR-22 | 1 | Not run |
| UAT-31 | Reconcile a full migration rehearsal | Normal | Data lead + Finance | Counts, AR balance, open order value, and stock agree | DM-01…DM-12 | 1 | Not run |
| UAT-32 | Export audit data for a period | Normal | Finance Manager | Export reconciles to source totals and opens without error | NFR-11 | 3 | Not run |
| UAT-33 | Deactivate a customer with open transactions | Edge | Master Data | Deactivation allowed; history retained; no deletion | BR-13 | 3 | Not run |

**Count:** 33 scenarios — 12 Normal, 8 Edge, 13 Negative. 22 acceptance criteria covered, 6 `Should` requirements covered.

### 6.1 Coverage check

| Requirement set | Covered | Note |
|---|---|---|
| AC-01…AC-22 | 22 / 22 | Each appears in at least one scenario |
| `Must` FRs (17) | 17 / 17 | Through their acceptance criteria |
| `Should` FRs (6) | 6 / 6 | UAT-22, UAT-23, UAT-24, UAT-25, UAT-26, UAT-27 |
| NFR-01, NFR-08, NFR-11 | 3 / 11 | Remaining NFRs need architecture or IT evidence, not UAT |
| `EX-01`…`EX-15` | 15 / 15 | Each exception has a handling path in at least one scenario or the exception table in `docs/to-be-process.md` §7 |
| `SOD-01`…`SOD-10` | 10 / 10 | UAT-28 plus targeted scenarios UAT-15, UAT-17 |

**Not covered by UAT and why:** NFR-02 to NFR-07, NFR-09, NFR-10 require configuration review, architecture review, retention testing, or volume data. They are not user-acceptance scenarios and should not be forced into one.

---

## 7. Detailed scripts for critical scenarios

Five scenarios carry the controls most likely to fail in practice. They are written in full so that a reviewer can see the sequence, not only the expected result.

### 7.1 UAT-01 — Standard order to cleared invoice

**Preconditions:** active customer with sufficient credit; active product with stock; price condition valid; user holds ROL-01; tax configuration complete.

| # | Actor | Action | Expected |
|---|---|---|---|
| 1 | Sales Rep | Create order: customer, 10 units, requested date | Order `Draft`; price from condition; total derived |
| 2 | Sales Rep | Submit order | Validation passes; status `Submitted` |
| 3 | System / Sales Manager | Approval per band | Status `Approved`; approver recorded |
| 4 | Inventory Controller | Confirm availability and reserve | Reservation created; ATP reduced |
| 5 | Warehouse Operator | Pick 10, pack, confirm goods issue | Delivery `Shipped`; stock reduced |
| 6 | Logistics Coordinator | Record POD: receiver, date, evidence reference | Delivery `POD Confirmed` |
| 7 | Finance/AR | Run billing for the delivery | Exactly one invoice, referencing the delivery |
| 8 | Finance/AR | Record payment for the full amount with invoice reference | Payment recorded; invoice `Cleared` |
| 9 | Auditor | Trace order → delivery → invoice → payment | All links reachable in both directions |

**Pass condition:** steps 1–9 complete with no manual database correction, and the document chain has no orphan.

### 7.2 UAT-13 — Allocation above payment value

**Preconditions:** payment of 1,000 recorded and unapplied; open invoice of 1,200.

| # | Actor | Action | Expected |
|---|---|---|---|
| 1 | Finance/AR | Allocate 1,100 to the invoice | Rejected with a clear message naming the payment amount |
| 2 | Finance/AR | Allocate 1,000 | Accepted; payment `Fully Allocated`; invoice `Partially Paid` |
| 3 | Finance/AR | Attempt a second allocation of 100 to the same payment | Rejected; no negative unapplied balance |

**Pass condition:** the payment can never allocate more than it received, including after a reversal.

### 7.3 UAT-17 — Credit block and release

**Preconditions:** customer limit set; exposure already at or above the limit; user holds ROL-01; Finance Manager available.

| # | Actor | Action | Expected |
|---|---|---|---|
| 1 | Sales Rep | Confirm an order that increases exposure above the limit | Order blocked or routed to Finance; reason visible |
| 2 | Sales Manager | Attempt to release the order | Refused; commercial approval cannot override credit |
| 3 | Finance Manager | Review exposure, release with reason and timestamp | Order proceeds; decision recorded |
| 4 | Auditor | Open the credit decision history | Shows who released, when, and why |

**Pass condition:** no path exists for Sales to bypass the block, including by re-keying the order.

### 7.4 UAT-28 — Role matrix sweep

**Preconditions:** one test user per role; the critical transaction matrix from `docs/role-authorization-matrix.md` §5 loaded as the expected result set.

| # | Actor | Action | Expected |
|---|---|---|---|
| 1 | Each role | Attempt every transaction in §5 | Allowed only where the matrix says `Yes` |
| 2 | Warehouse Operator | Attempt invoice posting | Denied and logged |
| 3 | Finance/AR | Attempt credit-limit change | Denied |
| 4 | Sales Manager | Attempt credit release | Denied |
| 5 | ERP Administrator | Attempt invoice approval | Denied |
| 6 | All roles | Attempt to edit a posted invoice | Denied |

**Pass condition:** every deviation is either a defect or an approved change to the matrix — never "works differently in practice".

### 7.5 UAT-31 — Migration rehearsal reconciliation

**Preconditions:** rehearsal data set loaded into a test client; source control totals available; Finance and Master Data present.

| # | Actor | Action | Expected |
|---|---|---|---|
| 1 | Data lead | Compare record counts per object | Source = target; rejected records listed with reason |
| 2 | Finance Manager | Compare opening AR balance | Zero difference; rounding items listed and approved |
| 3 | Sales Manager | Compare open order value | Zero difference at line level |
| 4 | Inventory Controller | Compare stock quantity and value | Zero difference after physical count adjustment |
| 5 | Data lead | Check orphans: invoice without delivery, payment without customer, order line without product | Zero orphans |
| 6 | All | Sign the reconciliation checklist | Signed with date and any accepted differences |

**Pass condition:** financial totals reconcile exactly; any accepted difference has a named approver and a reason. "Close enough" is a fail.

---

## 8. Test data

| Source | Contents | Use |
|---|---|---|
| `data/sample-customer-master.csv` | 12 synthetic customers with terms, credit limits, status | Master data and duplicate scenarios |
| `data/sample-product-master.csv` | 20 synthetic products with UoM and prices | Order, pricing, tax scenarios |
| `data/sample-sales-orders.csv` | 30 order lines across 15 orders and several statuses | Order, delivery, billing, reporting scenarios |
| Generated in test environment | Deliveries, invoices, payments, returns | Built during scenario execution |

**Data rules:**

- Names and tax identifiers are synthetic. No real company or person is represented (`DEC-04`).
- Values are internally consistent: every order line references an existing customer and product, and line totals equal quantity × price.
- Edge cases are seeded deliberately: one inactive customer, one blocked product, one order above credit limit, one over-delivery case.
- Test data is not a volume test. `DQ-05` and `DQ-57` remain open; no performance claim is made.

---

## 9. Defect management

| Severity | Definition | Example | Action |
|---|---|---|---|
| S1 — Critical | Process cannot continue; financial or statutory impact; no workaround | Invoice posted without POD; duplicate payment created | Fix before any further UAT |
| S2 — Major | Required behaviour missing; workaround is manual and unacceptable long-term | Credit block not enforced for one order type | Fix before exit |
| S3 — Minor | Behaviour works with a documented workaround | Wrong message text; report column order | Fix or accept with workaround |
| S4 — Cosmetic | No functional impact | Label alignment | Backlog |

**Defect states:** New → Triaged → In progress → Fixed → Retest → Closed / Deferred. A deferral needs a named approver and a date; it cannot be closed as "won't fix" without one.

**Rule:** a defect is never resolved by changing the expected result. If the expected result was wrong, the requirement is amended first (`docs/requirements.md` §8) and the scenario is re-baselined.

---

## 10. Schedule (proposed)

| Day | Activity | Output |
|---|---|---|
| 16 | Entry check, environment, roles, test data load | Entry checklist |
| 16–17 | Integration and role-negative scenarios | Execution log |
| 17–18 | Business UAT by process area | Signed scenario records |
| 19 | Defect retest, regression | Retest log |
| 19–20 | Migration rehearsal and reconciliation | Signed checklist |
| 20 | Exit review and Gate 4 decision | UAT summary report |

The schedule is a portfolio estimate (`AS-11`), not a resourced plan. It assumes a configured system exists, which it does not.

---

## 11. Requirements traceability

`docs/traceability-matrix.md` §2 now carries a `UAT` column linking each FR to its scenario. The matrix is the control: if a requirement has no scenario and no explicit deferral, Gate 4 fails.

---

## 12. Limitations

1. **Nothing is executed.** Every status is `Not run`; there is no system, no environment, and no test data beyond the samples.
2. **One author, many roles.** In a real UAT, business users test their own work. Here the author writes both the expectation and the verdict, which weakens independence.
3. **No volume or performance test.** Without volume data, a load test would measure nothing real.
4. **Threshold-dependent scenarios use placeholders.** UAT-17 and UAT-22 cannot pass against an unapproved credit or approval policy; they test the *mechanism*, not the policy.
5. **Interface tests depend on systems that may not exist.** INT-03 to INT-06 are not in the UAT scope for that reason.
6. **Tax scenarios assume a configuration.** `DQ-37` may change the invoice validation entirely.

---

## 13. Change log

| Version | Date | Change |
|---|---|---|
| 0.1 | 12 Nov 2026 | Initial UAT plan: 33 scenarios, 22/22 AC coverage, 6/6 Should FR coverage, 5 detailed scripts, defect model, entry/exit criteria |
