# Data Migration Plan and Reconciliation Checklist

**Document ID:** DMP-01
**Version:** 0.1 (Draft)
**Phase:** 4 — Testing and Migration Readiness
**Inputs:** `docs/data-catalogue.md`, `Design.md` §6.3, `docs/to-be-process.md`, `docs/role-authorization-matrix.md`
**Status:** Draft — plan only; **no source extract exists**, no volume is known, no load has been performed

---

## 1. Purpose and limits

This plan describes how opening data would move from the current spreadsheet-and-email landscape into an ERP without losing a customer, an open order, or a receivable. It defines the objects, the load sequence, the cleansing rules, and the reconciliation that proves the load is correct.

**Limits, stated once and applied throughout:**

- No legacy extract has been received (`DEP-04`), so source field names, formats, and quality are unknown.
- No volume is known (`DQ-57`), so no duration, batch size, or effort estimate is given.
- No cleansing has been performed, so no duplicate rate or rejection rate is claimed.
- Migration is *not* a history archive. Only open obligations move; closed documents remain in the legacy source under its retention policy.

A plan with unknown inputs is still useful: it fixes the **sequence and the proof**, which are the parts that do not change when the numbers arrive.

---

## 2. Migration principles

| Principle | Consequence |
|---|---|
| Open obligations only | Open orders, open deliveries, open invoices, unapplied cash, and open returns migrate; closed history does not |
| Count, then value | Record counts reconcile before financial totals are compared; a matching total with missing rows is not success |
| One owner per object | Every migration object has a named business owner who signs its reconciliation |
| No undocumented transformation | Every changed value has a mapping rule; "we fixed it in Excel" is not a rule |
| Reject, do not guess | A record that fails validation is rejected with a reason and listed; it is never silently corrected |
| Freeze at a stated point | Transactions stop in the source before the final extract; later changes go through a controlled delta |
| The business signs, not IT | IT proves the load ran; Finance and Sales prove the numbers are right |
| Rehearse twice | At least one full rehearsal before the cut-over rehearsal; the first attempt is never the go-live attempt |
| No parallel run of everything | Parallel running multiplies work; only AR and stock need a controlled overlap, and only for the agreed window |

---

## 3. Migration object inventory

`DM-` identifies a migration object. Source is deliberately `TBD` where no system is confirmed (`DQ-08`).

| ID | Object | Target entities | Source | Scope | Volume | Business owner | Load order | Reconciliation control |
|---|---|---|---|---|---|---|---|---|
| DM-01 | Customer master | DE-01, DE-02 | TBD — spreadsheets / accounting system | All active and blocked customers with open business | Unknown (`DQ-57`) | ROL-03 / ROL-08 | 1 | Count; unique tax ID; no duplicate active name |
| DM-02 | Product master | DE-03, DE-04 | TBD | All sellable products, including blocked ones with open orders | Unknown | ROL-03 | 1 | Count; unique product code; valid UoM conversion |
| DM-03 | Warehouse and locations | DE-05 | TBD | All stocking points used by open orders or stock | Unknown | ROL-04 | 1 | Count; every stock record has a valid location |
| DM-04 | Price conditions | DE-06 | TBD — price lists / contracts | Current valid prices only | Unknown | ROL-02 | 2 | Sample price check against source; no expired condition loaded |
| DM-05 | Credit limits | DE-07 | TBD | Current approved limits, if maintained | Unknown | ROL-08 | 2 | Count; limit per customer matches approved list |
| DM-06 | Open sales orders | DE-08, DE-09, DE-10 | TBD | Orders not yet delivered or invoiced in full | Unknown | ROL-02 | 3 | Line count; open quantity; order value |
| DM-07 | Opening inventory | DE-11 | TBD + physical count | Stock on hand at cut-over, by product and location | Unknown | ROL-04 | 3 | Quantity by product/location; value; no negative stock |
| DM-08 | Open deliveries and POD | DE-12, DE-13, DE-14 | TBD | Deliveries shipped but not invoiced, plus POD references | Unknown | ROL-06 | 4 | Delivery count; quantity; POD presence for billable deliveries |
| DM-09 | Open invoices | DE-15, DE-16 | TBD | Posted invoices with an open balance | Unknown | ROL-08 | 4 | Count; AR balance by customer; tax total |
| DM-10 | Open payments and allocations | DE-17, DE-18 | TBD | Unapplied cash and allocations against open invoices | Unknown | ROL-08 | 5 | Unapplied total; allocation total ≤ payment total |
| DM-11 | Open returns and credit notes | DE-19, DE-20 | TBD | Returns in progress and credit notes not yet applied | Unknown | ROL-08 | 5 | Count; credit total; reference to original invoice |
| DM-12 | Reference and configuration data | DE-22, DE-23, DE-24 | Design decisions | Approval bands, number ranges, status profiles, reason codes | n/a | ROL-09 + business | 0 | Configuration review; no business value invented |

**Count:** 12 migration objects. Load order 0–5 defines the dependency: configuration before master data, master data before transactions, transactions before balances.

**Explicitly not migrated:** `DE-21` historical exception records, `DE-25` legacy audit history, closed orders, cleared invoices, settled payments, and any document older than the retention boundary. They remain accessible in the legacy source or archive.

---

## 4. Scope decisions

| Decision | Position | Consequence |
|---|---|---|
| Closed documents | Do not migrate | Smaller load, faster reconciliation; history stays in the legacy system |
| Open orders partially delivered | Migrate open quantity only | Avoids double fulfilment |
| Invoices partially paid | Migrate open balance, not full history | AR ageing starts from the correct open amount |
| Unapplied cash | Migrate as unapplied payment | Cash is never dropped to make the balance look clean |
| Blocked customers and products | Migrate, with status | Historical documents may still reference them |
| Duplicate customers | Merge before load, not during | A merge after go-live is a data incident |
| Price history | Not migrated | Only current conditions are needed to trade |
| Tax master data | Configured, not migrated | Values are policy and statutory, not legacy data |
| Attachments and POD images | Migrate only for open deliveries | Evidence for closed deliveries stays in the archive |

---

## 5. Field mapping approach

A mapping is written per object, reviewed by the business owner, and versioned. Source fields are unknown, so the table fixes the *target* rule and the *validation*, not the source column.

### 5.1 Example — customer (DM-01)

| Target attribute | Required | Transformation rule | Validation | Rejection reason |
|---|---|---|---|---|
| Customer number | Yes | Use legacy ID where unique; otherwise assign from the approved number range | Unique across active and blocked | Duplicate or missing ID |
| Legal name | Yes | Trim, standardise legal form, preserve meaning | Not blank; duplicate similarity check | Blank or suspected duplicate without resolution |
| Tax ID | Yes | Normalise separators to the statutory format | Unique; format valid | Invalid format or duplicate |
| Address | Yes | Structured street, city, postal code, country | Country in allowed list | Incomplete address |
| Payment terms | Yes | Map legacy free text to an approved term code | Term exists in reference data | Unmapped term |
| Credit limit | No | Load current approved limit; blank means not defined | Non-negative; owner approval recorded | Negative or unapproved value |
| Status | Yes | Map legacy status to `Active` / `Blocked` / `Inactive` | Mapping table complete | Unknown status |
| Sales owner | No | Map legacy salesperson to a role holder | Role exists | Unknown user |

### 5.2 Example — open order (DM-06)

| Target attribute | Rule | Validation |
|---|---|---|
| Order number | Keep legacy reference for traceability; new internal number allowed if cross-referenced | Unique; cross-reference stored |
| Customer | Must match a loaded customer | Exists and is not `Inactive` |
| Order date | Keep original date | Not in the future |
| Requested date | Keep original; if past, flag for re-promise | Not blank |
| Line product and quantity | Load open quantity only | Product exists; quantity > 0; UoM valid |
| Line price | Keep agreed price; record as a manual condition with reason "migration" | Non-negative; reason recorded |
| Order status | Map to `Approved` / `Released` only; no `Draft` orders migrate | Mapping table complete |
| Approval evidence | Load approver and date if available; otherwise flag `Approval evidence missing` | Flag visible to Sales Manager |

**Rule:** a migrated order with missing approval evidence is not silently treated as approved. It is loaded with a flag and reviewed before release.

---

## 6. Cleansing rules

| # | Rule | Applies to | Action on failure |
|---|---|---|---|
| 1 | Tax ID must be present and unique | Customer | Reject; return to owner |
| 2 | Customer legal name must not be blank | Customer | Reject |
| 3 | Duplicate customer names above the similarity threshold must be merged or justified | Customer | Hold for master data decision |
| 4 | Product code must be unique and not reused | Product | Reject |
| 5 | Base and sales UoM must convert exactly | Product | Reject; do not round |
| 6 | Payment terms must map to an approved code | Customer | Reject; map before load |
| 7 | Price must be non-negative and have a valid from-date | Price condition | Reject |
| 8 | Open order quantity must be > 0 and ≤ ordered quantity | Order line | Reject line; order held |
| 9 | Open invoice balance must be ≥ 0 and ≤ invoice amount | Invoice | Reject |
| 10 | Payment allocation must not exceed the payment amount | Payment | Reject |
| 11 | Stock quantity must not be negative at load | Inventory | Hold until physical count resolves |
| 12 | Every open delivery must have a valid order reference | Delivery | Reject |
| 13 | Every credit note must reference an original invoice | Credit note | Reject |
| 14 | Dates must be valid and not in the future where the object is historical | All | Reject |
| 15 | Currency must be the single design currency | All | Reject if not (`AS-03`) |

**Rejection handling:** rejected records go to a rejection register with object, legacy key, reason, owner, and resolution date. The register is part of the reconciliation evidence. A rejection rate is measured during rehearsal, not assumed now.

---

## 7. Load sequence

| Step | Object(s) | Depends on | Entry condition | Exit condition |
|---|---|---|---|---|
| 0 | DM-12 configuration | Design sign-off | Approval, number range, and status decisions recorded | Configuration review passed |
| 1 | DM-01, DM-02, DM-03 master data | Step 0 | Cleansed and duplicate-resolved | Counts reconcile; no invalid master record |
| 2 | DM-04 price, DM-05 credit | Step 1 | Owner-approved lists | Sample and count checks pass |
| 3 | DM-06 open orders, DM-07 stock | Steps 1–2 | Cut-off extract taken | Open quantity and stock quantity reconcile |
| 4 | DM-08 deliveries, DM-09 invoices | Step 3 | Deliveries matched to orders | Delivery count and AR balance reconcile |
| 5 | DM-10 payments, DM-11 returns | Steps 3–4 | Allocations matched to invoices | Unapplied cash and credit totals reconcile |
| 6 | Final reconciliation and sign-off | All | All control totals available | Checklist signed by business owners |

**Failure rule:** a failed step stops the load. It is not patched in the next step. Fixing master data after transactions are loaded creates orphan records that reconciliation cannot repair.

---

## 8. Reconciliation checklist

Every line must be completed and signed. `Tolerance` is stated so that "close enough" cannot be negotiated during cut-over.

### 8.1 Record-level reconciliation

| # | Check | Source | Target | Tolerance | Evidence |
|---|---|---|---|---|---|
| R1 | Customer count (all statuses) | Source extract | Loaded + rejected | Zero unexplained | Count report |
| R2 | Active customer count | Source | Loaded active | Zero | Count report |
| R3 | Duplicate tax IDs | n/a | 0 | Zero | Duplicate report |
| R4 | Product count | Source | Loaded + rejected | Zero unexplained | Count report |
| R5 | Product code duplicates | n/a | 0 | Zero | Duplicate report |
| R6 | Open order count and line count | Source | Loaded | Zero unexplained | Order list |
| R7 | Open delivery count | Source | Loaded | Zero unexplained | Delivery list |
| R8 | Open invoice count | Source | Loaded | Zero unexplained | Invoice list |
| R9 | Payment and allocation count | Source | Loaded | Zero unexplained | Payment list |
| R10 | Open return and credit note count | Source | Loaded | Zero unexplained | Return list |
| R11 | Rejected records | Rejection register | n/a | Every rejection has an owner and resolution | Signed register |

### 8.2 Financial reconciliation

| # | Check | Formula | Tolerance | Owner |
|---|---|---|---|---|
| F1 | Open AR balance | Sum of open invoice balances = source AR control total | Zero; rounding items listed | ROL-08 |
| F2 | Unapplied cash | Sum of unallocated payment amounts = source unapplied total | Zero | ROL-08 |
| F3 | Open order value | Sum of open order line value = source open order report | Zero at line level | ROL-02 |
| F4 | Credit exposure | Open AR + open order value per customer ≤ approved limit, or flagged | Zero unexplained flags | ROL-08 |
| F5 | Stock quantity | Quantity by product and location = physical count | Zero; count adjustments approved | ROL-04 |
| F6 | Stock value | Quantity × standard cost = source stock value | Zero; rounding per document only | ROL-08 |
| F7 | Tax totals on open invoices | Sum of invoice tax = source tax report | Zero | ROL-08 |
| F8 | Credit note total | Sum of open credit notes = source credit report | Zero | ROL-08 |

### 8.3 Structural reconciliation

| # | Check | Rule | Tolerance |
|---|---|---|---|
| S1 | Invoice without delivery | Must not exist in the new system | Zero |
| S2 | Delivery without order | Must not exist | Zero |
| S3 | Order line without product | Must not exist | Zero |
| S4 | Payment without customer | Must not exist | Zero |
| S5 | Allocation greater than payment | Must not exist | Zero |
| S6 | Credit note without original invoice | Must not exist | Zero |
| S7 | Negative stock | Must not exist at go-live | Zero |
| S8 | Status mapping completeness | Every legacy status has a target status | 100% |
| S9 | Date logic | Due date ≥ invoice date; shipment date ≤ POD date | 100% |
| S10 | Currency | Single design currency only | 100% |

**Rounding rule:** document-level rounding differences of 0.01 are permitted only if the invoice header equals the sum of its lines plus tax, and the difference is listed. Control totals never carry a rounding tolerance.

---

## 9. Rehearsal strategy

| Rehearsal | Purpose | Data | Success measure | When |
|---|---|---|---|---|
| R0 — dry run | Prove the mapping and scripts work | Subset, one customer group | Load completes; mapping errors listed | Early Phase 4 |
| R1 — full rehearsal | Measure quality, duration, and rejection rate | Full extract | All reconciliation checks pass or have approved exceptions | Mid Phase 4 |
| R2 — cut-over rehearsal | Prove the sequence and the freeze | Final extract simulation | Checklist signed; no sequence surprise | Before cut-over |
| Go-live load | Actual | Final extract | Signed checklist | Cut-over window |

**What is measured, not estimated:** actual load duration, rejection count and reason mix, reconciliation effort, and the number of manual interventions. These become inputs to the cut-over plan; they are not stated in advance because no extract exists.

**Rule:** if R2 does not pass, go-live moves. A failed rehearsal is information, not a schedule problem to be talked around.

---

## 10. Cut-over sequence

| Phase | Activity | Owner | Exit condition |
|---|---|---|---|
| T-5 | Freeze master data changes; announce cut-off | ROL-03 | No new customer or product after this point except approved exceptions |
| T-4 | Freeze order entry in the source | ROL-02 | No new orders; open order list extracted |
| T-3 | Final extract and validation | Data lead | Extract complete; control totals captured |
| T-2 | Load master data and open transactions | ROL-09 | Steps 1–5 complete |
| T-1 | Reconcile and resolve rejections | Business owners | Checklist R1–R11, F1–F8, S1–S10 signed |
| T-0 | Go/No-Go decision; open new system | Sponsor | Go decision recorded; legacy read-only |
| T+1 | First live order and first live invoice | Sales / Finance | End-to-end document chain works |
| T+2 | First live cash application | Finance/AR | Payment matched to a live invoice |
| T+5 | Legacy access review | IT | Write access removed; read access per retention |

**T-minus notation is relative.** Durations are not given because the number of records, the number of rejects, and the team size are unknown.

---

## 11. Roles and responsibilities

| Activity | ROL-03 Master Data | ROL-04 Inventory | ROL-02 Sales | ROL-08 Finance | ROL-09 IT | Sponsor |
|---|---|---|---|---|---|---|
| Define mapping | R | C | C | C | A | I |
| Cleanse master data | R | C | I | C | I | I |
| Approve customer and product load | A | C | I | C | R | I |
| Physical stock count | C | **R/A** | I | C | I | I |
| Approve AR opening balance | I | I | I | **R/A** | R | I |
| Run load scripts | I | I | I | I | **R/A** | I |
| Reconcile and sign | C | R | R | **R/A** | C | I |
| Go/No-Go | I | I | I | C | C | **A** |

---

## 12. Migration risks

| Risk | Trigger | Impact | Mitigation |
|---|---|---|---|
| Duplicate customers merged wrongly | No unique tax ID in source | Wrong statement, credit exposure split | Duplicate report reviewed by business, not only IT |
| Opening stock does not match reality | Spreadsheet stock, no physical count | Orders confirmed against phantom stock | Physical count at cut-over; freeze movements |
| Unapplied cash dropped | Pressure to make AR balance "clean" | Customer paid but credit lost | F2 reconciliation; unapplied cash visible |
| Partially delivered orders double-counted | Open quantity not reduced | Over-delivery and double invoice | Load open quantity, never ordered quantity |
| Cut-off leakage | Orders taken during extract | Missing or duplicated orders | Freeze and delta process |
| Rejection backlog at go-live | Poor source quality | Load incomplete | Rehearsal measures rejection rate; cleansing before cut-over |
| Tax data configured wrongly | `DQ-37` unresolved | Non-compliant invoices | Finance sign-off on tax configuration |
| History expected in the new system | Stakeholder assumption | Scope shock late | Written scope: open obligations only |

---

## 13. Open questions

| Question | Effect | Owner |
|---|---|---|
| Which sources hold each object? (`DQ-08`) | No source-to-target mapping is possible | Master Data / IT |
| What volumes and open-item counts exist? (`DQ-57`) | Cannot size load or rehearsal | Finance / IT |
| What is the retention requirement? (`DQ-51`) | Legacy archive lifetime unknown | Sponsor |
| Are credit limits maintained today? (`DQ-45`) | DM-05 may be empty; credit control starts from zero | Finance Manager |
| Are prices maintained anywhere? (`DQ-17`) | DM-04 may require manual price list creation | Sales Manager |
| Is there a physical stock count process? | DM-07 reliability depends on it | Inventory Controller |

---

## 14. Limitations

1. **No source, no mapping.** Field-level mapping in a real project is written against actual extract columns. Here only the target rules exist.
2. **No measured quality.** Rejection rates, duplicate counts, and cleansing effort are unknown.
3. **No rehearsal result.** The plan is not evidence that the sequence works.
4. **No archive design.** Where closed history lives afterwards is outside this document.
5. **Sample CSVs are illustrative.** They demonstrate structure and consistency, not volume or data quality.

---

## 15. Change log

| Version | Date | Change |
|---|---|---|
| 0.1 | 12 Nov 2026 | Initial migration plan: 12 objects, scope decisions, mapping approach, 15 cleansing rules, 29 reconciliation checks, rehearsal and cut-over sequence |
