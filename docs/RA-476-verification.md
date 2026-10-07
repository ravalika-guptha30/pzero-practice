# RA-476 — RCI Rating Not Updated After Regenerating the Quality Report: Verification Record

**Purpose:** Record the RA-476 defect, why it cannot be fixed in this repository, and what the Lumos code owner should check.
**Status:** Not fixed. Not reproduced. This document does not change product behavior.

**Key takeaways**
- After "disregard testability" and a regenerated quality report, the pass/fail totals change but the RCI rating stays the same.
- The code that computes the RCI rating is in the Helio Lumos Requirement Analyzer repository. That repository is not connected here.
- The causes listed below are **unverified hypotheses**, not findings.

---

## 1. Ticket
| Field | Value |
|---|---|
| Ticket | RA-476 (Bug, High) |
| Product | Helio Lumos Requirement Analyzer |
| Environment | Staging |
| Related story | RA-303 |

## 2. Steps to reproduce
1. Choose to disregard testability.
2. Regenerate the quality report.

## 3. Actual result
- The pass/fail totals change from **49/51** to **72/29**.
- The RCI rating stays the same as before.

## 4. Expected result
- After the report is regenerated, the RCI rating is recalculated from the new check results.

## 5. Repository boundary
- `pzero-practice` contains only documentation. It has no RCI, quality-report or testability code.
- A search of all branches for `RCI`, `testability`, `quality report` and `pass/fail` found no matches.
- The defect was not reproduced or fixed in this repository.

## 6. Unverified hypotheses
These are places to look first. None was checked.
1. The RCI value is cached or stored with the first report and is not recalculated when the report is regenerated.
2. The regenerate path updates the check totals but does not call the RCI calculation.
3. The RCI calculation reads the original check set and ignores the "disregard testability" filter.
4. The UI shows a stale RCI value even though the backend recalculated it.

## 7. Verification needed in the Lumos repository
- Regenerate the quality report with testability disregarded.
- Confirm that the displayed RCI rating matches a fresh RCI calculation from the new results (72 pass / 29 fail in the reported case).
- Confirm that the stored RCI value and the displayed RCI value are the same.
