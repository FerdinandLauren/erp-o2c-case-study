# Requirement Traceability Matrix

**Document ID:** TRM-01
**Version:** 0.1 (Draft)
**Phase:** 2 — Requirements and Process Design
**Sources:** `docs/requirements.md`, `docs/to-be-process.md`, `docs/as-is-process.md`, `docs/discovery-questions.md`
**Status:** Draft — complete for Phase 2; UAT references added in Phase 4

---

## 1. Purpose and rules

Traceability answers three questions a reviewer will ask:

1. **Why does this requirement exist?** Every FR points to a pain point, control gap, or open decision.
2. **Where is it enforced?** Every FR points to a To-Be stage.
3. **How will we know it works?** Every `Must` FR points to an acceptance criterion, which becomes a UAT scenario in Phase 4.

Rules:

- No requirement without a justification. A requirement that cannot name a business problem is deleted, not kept "because ERPs have it".
- No pain point without a requirement. If a documented pain point has no FR, either the analysis or the requirement set is incomplete.
- Coverage is counted, not asserted. The summary in §4 is derived from the rows below.
- In Phase 4, a `UAT-` column is added. This matrix is the link between design and test, and it is the artefact that proves the design is complete.

---

## 2. Forward traceability

| FR | Requirement (short) | Stage | BR | NFR | AC | Justified by | Status |
|---|---|---|---|---|---|---|---|
| FR-01 | Customer master with unique ID | S1 | BR-01, BR-13 | NFR-04 | AC-01 | PP-01, CG-01 | Baseline |
| FR-02 | Product master with unique ID and UoM | S1 | BR-01, BR-13 | NFR-04 | AC-02 | PP-01, CG-01 | Baseline |
| FR-03 | Create and update sales orders | S2 | BR-02 | NFR-01, NFR-02 | AC-03, AC-04 | PP-03 | Baseline |
| FR-04 | Validate order fields | S3 | BR-02 | NFR-07 | AC-05 | PP-03 | Baseline |
| FR-05 | Inventory availability information | S4 | BR-03 | — | AC-06 | PP-02 | Baseline |
| FR-06 | Configurable approval rules | S5 | BR-04, BR-09 | NFR-01 | — (Should) | CG-02, OBJ-02 | Baseline |
| FR-07 | Fulfilment requirement from confirmed orders | S6 | BR-03 | — | AC-07 | PP-03 | Baseline |
| FR-08 | Record picked, packed, shipped quantities | S6 | BR-05, BR-14 | — | AC-08 | PP-03, PP-04 | Baseline |
| FR-09 | Delivery status and evidence | S7 | BR-10 | — | — (Should) | PP-04, CG-04 | Baseline |
| FR-10 | Invoice from approved billing trigger | S8 | BR-06, BR-10 | — | AC-09, AC-10 | PP-05, CG-05 | Baseline |
| FR-11 | Invoice status tracking | S8, S10 | BR-06 | NFR-08 | AC-11 | PP-05, CG-05 | Baseline |
| FR-12 | Record and allocate payments | S9 | BR-07 | — | AC-12, AC-13 | CG-06 | Baseline |
| FR-13 | End-to-end traceability | S2–S10 | BR-07 | — | AC-14 | PP-04, CG-04 | Baseline |
| FR-14 | Operational reports | S10 | — | NFR-08 | — (Should) | PP-06, CG-07 | Baseline |
| FR-15 | Transaction history for audit | All | BR-13 | NFR-02 | AC-15 | CG-08, CG-02 | Baseline |
| FR-16 | Returns and credit notes | R1–R5 | BR-12, BR-15 | — | AC-16, AC-17 | DQ-22, EX-08 | Proposed |
| FR-17 | Credit exposure check and block | S5 | BR-09 | NFR-01 | AC-18 | DQ-45, CG-02 | Proposed |
| FR-18 | Backorder and outstanding quantity | S6–S7 | BR-05, BR-14 | — | — (Should) | DQ-26, PP-02 | Proposed |
| FR-19 | Price determination and override control | S2, S8 | BR-11 | — | AC-19 | DQ-17, PP-05 | Proposed |
| FR-20 | Lifecycle status and valid transitions | All | BR-08 | NFR-07 | AC-20 | PP-04, CG-04 | Proposed |
| FR-21 | Exception register with reason and owner | S3–S10 | — | NFR-08 | — (Should) | KPI-07, CG-07 | Proposed |
| FR-22 | Statutory invoice data | S8 | BR-06 | — | AC-21, AC-22 | DQ-37 | Proposed |
| FR-23 | Duplicate detection at master data entry | S1 | BR-01 | NFR-04 | — (Should) | PP-01, CG-01 | Proposed |

---

## 3. Reverse traceability — business problem to requirement

### 3.1 Pain points

| ID | Pain point (Phase 1) | Covered by | Strength of coverage |
|---|---|---|---|
| PP-01 | Duplicate customer and product information | FR-01, FR-02, FR-23 | Direct; prevention plus detection |
| PP-02 | Limited inventory visibility | FR-05, FR-18 | Direct; availability plus backorder handling |
| PP-03 | Manual departmental handoffs | FR-03, FR-04, FR-07, FR-08 | Direct; the handoff becomes a system document |
| PP-04 | Limited order traceability | FR-08, FR-09, FR-13, FR-20 | Direct; traceability plus status model |
| PP-05 | Potential delays in billing | FR-10, FR-11, FR-19 | Direct; trigger plus price accuracy |
| PP-06 | Inconsistent operational reporting | FR-14, FR-21 | Direct; standard reports plus exception data |

### 3.2 Control gaps

| ID | Control gap (Phase 1) | Covered by | Note |
|---|---|---|---|
| CG-01 | No enforced unique identifier | FR-01, FR-02, FR-23 | Preventive control at entry |
| CG-02 | No recorded approval evidence | FR-06, FR-15, FR-17 | Approval, audit, and credit release |
| CG-03 | No segregation of duties in shared spreadsheets | NFR-01, BR-08, FR-15 | Enforced through access design in Phase 3 |
| CG-04 | No enforced document traceability | FR-09, FR-13, FR-20 | Parent reference on every child document |
| CG-05 | No defined billing trigger | FR-10, FR-11, BR-10 | Trigger is a decision (`DQ-33`), not an assumption |
| CG-06 | No cash application control | FR-11, FR-12, BR-07 | Allocation total check plus unapplied cash visibility |
| CG-07 | No single KPI definition or cut-off | FR-14, FR-21, NFR-08 | Reporting rules plus exception data |
| CG-08 | No audit trail with identity and time | FR-15, NFR-02 | System-enforced, not procedural |

### 3.3 KPI measurability

A KPI that cannot be derived from transaction data is a wish. This table proves each KPI in `PRD.md` §10 has a data source in the design.

| KPI | Data source requirement | Derived from |
|---|---|---|
| KPI-01 Order Cycle Time | Order confirmation timestamp and fulfilment milestone | FR-13, FR-20 |
| KPI-02 On-Time Delivery Rate | Promised date and POD date | FR-09, FR-13 |
| KPI-03 Order Fulfillment Rate | Ordered quantity versus fulfilled quantity | FR-05, FR-08, FR-18 |
| KPI-04 Billing Delay | POD timestamp versus invoice posting date | FR-10, FR-22 |
| KPI-05 Open Receivables | Invoice value minus allocations at cut-off | FR-11, FR-12 |
| KPI-06 Overdue Receivables | Due date versus open balance at cut-off | FR-11, FR-14 |
| KPI-07 Order Exception Rate | Exception records with reason code | FR-21 |

**Limitation:** KPI-07 has no baseline because no exception log exists today (`docs/as-is-process.md` §8). The requirement creates the data; it does not prove improvement.

---

## 4. Coverage summary

| Measure | Count | Coverage |
|---|---|---|
| Functional requirements | 23 | 100% mapped to a stage |
| `Must` FRs with owner | 17 / 17 | 100% |
| `Must` FRs with acceptance criteria | 17 / 17 | 100% |
| `Should` FRs with acceptance criteria | 0 / 6 | Deferred to Phase 4 best effort |
| Acceptance criteria | 22 | All traced back to FR |
| Business rules | 15 | All have an enforcement point |
| NFRs | 11 | 4 `Must`, 7 `Should`; targets pending input |
| Pain points covered | 6 / 6 | 100% |
| Control gaps covered | 8 / 8 | 100% (CG-03 via NFR-01 in Phase 3) |
| KPIs with a data source | 7 / 7 | 100% |

### 4.1 Orphan check

| Check | Result |
|---|---|
| FR with no justification | None |
| FR with no stage | None |
| FR with no owner | None |
| BR with no enforcement point | None |
| AC with no FR | None |
| PP/CG with no FR | None |

---

## 5. Known gaps and deferred coverage

| Gap | Consequence | When resolved |
|---|---|---|
| `Should` FRs have no acceptance criteria | Lower confidence in approval workflow, delivery evidence, reporting, backorder, exception register, duplicate detection | Phase 4 |
| NFR targets are unquantified | Cannot test performance or retention objectively | After `DQ-05`, `DQ-51`, `DQ-59` |
| Business rules are policy placeholders | UAT would test the design, not the approved policy | After `DQ-33`, `DQ-45`, `DQ-46`, `DQ-17` |
| Returns flow is `Proposed` | If excluded from wave 1, FR-16 and R1–R5 are deferred | `DQ-22` |
| Tax and e-invoice scope unconfirmed | FR-22 may become an integration requirement | `DQ-37` |
| No UI or screen design | Requirements are behavioural, not visual | Phase 3 (role design), not a gap in Phase 2 |

---

## 6. Maintenance

| Trigger | Action |
|---|---|
| New requirement added | Add row in §2 and §3; update counts in §4 |
| Requirement withdrawn | Keep row, mark `Withdrawn`, add reason and date |
| Phase 4 test created | Add `UAT-` reference to the matching FR row |
| Phase 3 fit-gap changes scope | Re-check §5; a deferred requirement must be visible, not silently dropped |
| Blocker question answered | Re-validate the FRs that depend on it; record the change in `docs/requirements.md` §8 |

This matrix is the audit trail of the design. If a future reviewer cannot connect a test back to a business problem through this table, the design has a hole.
