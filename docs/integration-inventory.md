# Integration Inventory

**Document ID:** INT-01
**Version:** 0.1 (Draft)
**Phase:** 3 — Fit-Gap Analysis and Solution Blueprint
**Inputs:** `PRD.md` §9; `Design.md` §7; `docs/functional-blueprint.md` §7; `docs/data-catalogue.md`
**Status:** Draft — candidate interfaces; none agreed, none implemented (`DQ-53`)

---

## 1. Purpose

`RSK-07` rates late integration discovery as a high risk because interfaces are where ERP timelines actually slip. This inventory forces the question early: which systems must exchange O2C data, who owns each data object, and what happens if an interface is not ready at go-live.

It is an inventory, not a specification. No endpoint, protocol, or vendor connector is defined, because no product or system has been selected.

---

## 2. Integration principles

| Principle | Consequence |
|---|---|
| Core O2C runs without interfaces | Every interface below is `Optional` or `Statutory`, never a precondition for order-to-cash to function (`docs/functional-blueprint.md` §7) |
| One system of record per object | An interface copies data; it does not create a second truth (`docs/data-catalogue.md` §2) |
| Idempotency is mandatory | A re-sent message must not create a duplicate payment, invoice, or stock movement |
| Failure is visible | Every interface has an owner, an error queue, and a reconciliation check |
| Manual fallback is designed | If an interface fails, the process continues with a named manual step and a control |
| No silent correction | An interface error is fixed at the source or through a logged adjustment document, never by editing posted data |
| Statutory beats convenience | A tax-mandated channel outranks an internal preference for simplicity (`DQ-37`) |

---

## 3. Interface register

`Criticality` describes the effect of the interface being unavailable at go-live.

| ID | Interface | Direction | Data exchanged | Pattern | Frequency | Owner | Criticality | Status |
|---|---|---|---|---|---|---|---|---|
| INT-01 | Bank statement | Inbound | Statement lines, amounts, references, value dates; payment status outbound if supported | File or API | Daily | ROL-08 Finance Manager | Optional | Candidate |
| INT-02 | Tax / e-invoice channel | Outbound, inbound status | Invoice data, tax identifiers, clearance status, rejection reasons | API or provider file | Per invoice | ROL-08 Finance Manager | **Statutory** if mandated | Unknown (`DQ-37`) |
| INT-03 | Carrier tracking | Inbound, optional outbound | Shipment reference, delivery events, POD evidence reference | API or portal export | Near real time / daily | ROL-06 Logistics Coordinator | Optional | Candidate |
| INT-04 | Warehouse management system | Bidirectional | Stock balances, reservations, pick/pack confirmation, goods issue | API or message | Near real time | ROL-04 Inventory Controller | Optional | Candidate (`DQ-27`) |
| INT-05 | CRM / customer portal | Inbound, optional outbound | Customer master, order request, order status | API | On event | ROL-03 Master Data | Optional | Candidate (`DQ-56`) |
| INT-06 | Reporting platform | Outbound | Orders, deliveries, invoices, payments, exceptions | Extract (file or database view) | Daily | ROL-08 Finance Manager | Optional | Candidate |
| INT-07 | Invoice delivery channel | Outbound | Invoice PDF, credit note PDF, delivery note | Email / print service | Per document | ROL-07 Finance/AR Officer | Needed | Candidate |

**Count:** 7 candidate interfaces. One is potentially statutory, one is operationally needed (document output), five are optional enhancements.

**What is deliberately absent:** no supplier/procurement interface (P2P out of scope, `AS-08`), no general ledger interface (finance scope not defined, `DQ-03`), no e-commerce channel (B2B only, `AS-06`), and no HR or identity interface (IT scope, not O2C data).

---

## 4. Interface detail

### 4.1 INT-01 — Bank statement

| Aspect | Design position |
|---|---|
| Purpose | Feed cash application with actual receipts instead of user-typed amounts |
| System of record | Bank is the source of the money movement; ERP is the source of the customer and invoice |
| Minimum data | Value date, amount, currency, remitter name, remittance reference, bank transaction ID |
| Identity rule | Bank transaction ID is the idempotency key; a repeated line is ignored and logged |
| Matching | Amount + reference auto-match; everything else goes to the AR worklist (`DEC-18`) |
| Error handling | Unparseable file rejects as a whole, not line by line; the previous balance stays intact |
| Reconciliation | Sum of statement lines equals sum of posted payments plus unapplied cash for the period |
| Fallback | Manual payment entry with the bank reference as a mandatory field |
| Risk | A format change at the bank breaks parsing silently — monitor file-level totals |

**Why it is optional:** without it, AR records payments manually. That is slower and more error-prone, but the process still works. An interface is not allowed to become a go-live dependency for a control that can be manual.

### 4.2 INT-02 — Tax / e-invoice channel

| Aspect | Design position |
|---|---|
| Purpose | Satisfy a statutory invoice or clearance requirement, if one exists |
| Trigger | Answer to `DQ-37`; no design decision is valid before it |
| Data | Invoice number, date, tax identifier of both parties, line amounts, tax amounts, totals, status |
| Direction | Outbound invoice; inbound clearance, rejection, or cancellation status |
| Idempotency | Invoice number is the key; a re-send after a timeout must not create a second tax document |
| Error handling | Rejection is a business exception, not a technical retry; it blocks the invoice from being treated as valid |
| Reconciliation | Every posted invoice has a channel status; unmatched invoices are listed daily |
| Fallback | **None if mandated.** This is the one interface that can block go-live |
| Risk | Late discovery (`RSK-13`); format changes by the tax authority |

**Open:** whether the country mandates e-invoicing, whether a certified provider is required, and whether credit notes need separate clearance. None of these can be assumed.

### 4.3 INT-03 — Carrier tracking

| Aspect | Design position |
|---|---|
| Purpose | Reduce the gap between delivery and POD, which delays invoicing (`KPI-04`) |
| Data | Shipment ID, delivery status, attempt count, POD date, receiver, evidence reference |
| Identity rule | Carrier shipment ID maps to one delivery; unknown IDs are quarantined, not guessed |
| Error handling | Out-of-order events are sorted by event timestamp, not arrival time |
| Reconciliation | Deliveries marked `Shipped` without a POD after the agreed window appear on an exception report |
| Fallback | Manual POD entry (`FG-09` Option A) |
| Risk | Carrier coverage is partial; a per-carrier enablement decision is needed |

### 4.4 INT-04 — Warehouse management system

| Aspect | Design position |
|---|---|
| Purpose | Only relevant if a WMS exists or is planned (`DQ-27`) |
| Ownership decision | If a WMS owns stock quantity, the ERP owns the commercial documents. Two systems cannot both own stock. |
| Data | Stock balances, reservations, pick/pack confirmation, goods issue |
| Idempotency | Movement document number, not timestamp |
| Error handling | A failed goods issue blocks the delivery from reaching `Shipped`; the fallback is an ERP goods issue with a reconciliation flag |
| Reconciliation | Daily stock comparison; differences above a tolerance stop automated posting |
| Fallback | Operate warehouse confirmation inside ERP |
| Risk | Real-time stock sync is the most failure-prone interface in distribution; a batch cut-over is safer than a big-bang one |

### 4.5 INT-05 — CRM / customer portal

| Aspect | Design position |
|---|---|
| Purpose | Capture customer requests and master data changes closer to the source |
| Ownership | ERP owns customer master. A portal may *request* a change; it may not create a customer directly |
| Data | Customer request, order request, status query |
| Idempotency | External request ID |
| Error handling | Rejected requests return a reason code to the requester; no partial customer record |
| Fallback | Sales keys the order in ERP |
| Risk | `DQ-56` — no external stakeholder participates in design; portal requirements would be invented |

### 4.6 INT-06 — Reporting platform

| Aspect | Design position |
|---|---|
| Purpose | Provide cross-period analysis without loading the transactional system |
| Direction | Outbound only; the reporting platform is read-only |
| Data | Transaction extracts plus KPI definitions (`NFR-08`) |
| Freshness | Agreed cut-off; two reports for one period must agree |
| Error handling | A failed load leaves the previous extract in place and raises an alert |
| Reconciliation | Row counts and financial totals reconcile to the ERP for the period |
| Fallback | Standard ERP reports |
| Risk | Silent partial loads produce confident wrong dashboards; totals must be checked, not just job status |

### 4.7 INT-07 — Invoice delivery channel

| Aspect | Design position |
|---|---|
| Purpose | Send invoices and credit notes to customers reliably and retain proof of sending |
| Data | Document PDF, recipient, send timestamp, delivery status |
| Identity rule | Document number + recipient + send attempt |
| Error handling | Bounce is an exception on the invoice, not a silent failure |
| Fallback | Print and courier, recorded with the same document reference |
| Risk | Manual PDF editing before sending would break `BR-15`; output must be system-generated |

---

## 5. Ownership and responsibility

| Data object | System of record | Interface owner | Business owner |
|---|---|---|---|
| Customer master | ERP | ROL-09 (technical) | ROL-03 Master Data |
| Product master | ERP | ROL-09 | ROL-03 |
| Stock quantity | ERP, unless a WMS is confirmed | ROL-04 | ROL-04 |
| Sales order | ERP | ROL-01 | ROL-02 |
| Delivery and POD | ERP (POD evidence may originate with carrier) | ROL-06 | ROL-06 |
| Invoice | ERP (tax channel receives a copy) | ROL-07 | ROL-08 |
| Payment | Bank (movement), ERP (customer allocation) | ROL-07 | ROL-08 |
| Reporting extract | ERP as source, platform as copy | ROL-08 | ROL-08 |

**Rule:** an interface never changes who is accountable for a data object. Moving data is a technical act; owning its correctness is a business one.

---

## 6. Non-functional requirements for every interface

These apply to all seven interfaces and belong in the interface specification, not in a later technical document.

| Requirement | Test |
|---|---|
| Authentication | No anonymous interface; credentials are per system, not per person |
| Authorisation | The interface can only read/write the objects it needs |
| Idempotency | Replay the same message twice; exactly one business document results |
| Ordering | Out-of-order messages are either rejected or sorted correctly |
| Error visibility | Failures appear on an owned queue with an alert; no silent drop |
| Retry | Bounded retries, then manual intervention; no infinite loop |
| Monitoring | Last successful run and backlog are visible |
| Audit | Every inbound and outbound message is traceable to a business document |
| Data protection | Only necessary fields; no personal data beyond what the process needs |
| Documentation | Interface contract versioned and owned (`NFR-04`) |

---

## 7. Cut-over stance

| Interface | Go-live stance | Reason |
|---|---|---|
| INT-01 Bank | Manual fallback ready; interface can start after go-live | Cash application works manually |
| INT-02 Tax | **Must be decided and, if mandated, live before go-live** | Statutory failure is not a defect to fix later |
| INT-03 Carrier | Optional; manual POD is the baseline | Invoice waits for POD, not for the interface |
| INT-04 WMS | Only if a WMS already operates; otherwise stock stays in ERP | Two stock owners at cut-over is a known failure pattern |
| INT-05 CRM / portal | Not in wave 1 | No external stakeholder design input (`DQ-56`) |
| INT-06 Reporting | Daily extract can start after go-live; standard reports cover the gap | Reporting lag is tolerable; wrong numbers are not |
| INT-07 Document output | **Must be live** | An invoice that cannot be sent is not an invoice |

**Cut-over rule:** an interface is either live and reconciled, or explicitly manual with a named owner. "Partially configured" is not a go-live state.

---

## 8. Open questions

| Question | Effect | Owner |
|---|---|---|
| Which systems exist today? (`DQ-53`) | Entire register is hypothetical | IT / Integration Lead |
| Is a WMS in place or planned? (`DQ-27`) | INT-04 becomes critical and changes stock ownership | IT + Inventory |
| Is e-invoicing mandated? (`DQ-37`) | INT-02 becomes a go-live blocker | Finance Manager |
| What bank formats are available? (`DQ-41`) | INT-01 feasibility and matching quality | Finance Manager |
| Is a reporting platform used? | INT-06 replaces standard reports | Management |
| Are carriers integrated at all? | INT-03 scope and coverage | Logistics |

---

## 9. Phase 4 test approach

| Test | Interface | Expected evidence |
|---|---|---|
| Duplicate replay | INT-01, INT-02 | One payment / one tax document, second attempt logged |
| Malformed file | INT-01 | Whole file rejected; no partial posting |
| Out-of-order events | INT-03 | Status reflects latest event by timestamp |
| Stock mismatch | INT-04 | Delivery blocked; reconciliation report names the difference |
| Tax rejection | INT-02 | Invoice not treated as valid; exception assigned to Finance |
| Extract reconciliation | INT-06 | Row counts and totals match the ERP for the same period |
| Bounce handling | INT-07 | Invoice shows delivery failure and is retried or printed |

**Honesty note:** none of these can be executed in this repository. They are the acceptance approach a real programme would use, not results.

---

## 10. Change log

| Version | Date | Change |
|---|---|---|
| 0.1 | 12 Nov 2026 | Initial inventory: 7 candidate interfaces, ownership table, interface NFRs, cut-over stance, Phase 4 test approach |
