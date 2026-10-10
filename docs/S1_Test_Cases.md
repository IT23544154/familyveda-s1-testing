# S1 Test Case Document – Consent State Machine (xUnit)

**Feature:** `ConsentStateMachine.CanTransition` and `RequiresReaffirmation`
**Precondition:** .NET SDK 8.0.425 installed; project restored; run from the group project root.
**Technique:** State Transition testing (4 states, 16 possible moves: 6 allowed, 10 forbidden) and Boundary Value Analysis (the 18th birthday).
**Ownership:** rows marked **Mine** were written by me. Rows marked **Group** existed already; I ran them.

States: `NotSet`, `Granted`, `Revoked`, `PendingReaffirmation`.

## A. Original tests (Group) – 5 test cases

| ID | Input | Expected | Actual | Status | Owner |
|---|---|---|---|---|---|
| TC-S1-01 | Granted → PendingReaffirmation | `true` | `true` | Pass | Group |
| TC-S1-02 | PendingReaffirmation → Granted | `true` | `true` | Pass | Group |
| TC-S1-03 | Born 2008-08-04, today 2026-08-04, guardian granted | `RequiresReaffirmation` = `true` (turns 18 today) | `true` | Pass | Group |
| TC-S1-04 | Born 2008-08-05, guardian granted (17 years 364 days) | `false` | `false` | Pass | Group |
| TC-S1-05 | Born 2008-08-04, guardian did NOT grant | `false` | `false` | Pass | Group |

Evidence: `evidence/04-consent-baseline-5-passed.png`

## B. My new test cases – forbidden moves (10 rows in 2 theories → 6 + 4)

Test method: `CanTransition_WhenMoveIsNotAllowed_ReturnsFalse`

| ID | From → To | Expected | Actual | Status | Owner |
|---|---|---|---|---|---|
| TC-S1-06 | NotSet → Revoked | `false` | `false` | Pass | Mine |
| TC-S1-07 | NotSet → PendingReaffirmation | `false` | `false` | Pass | Mine |
| TC-S1-08 | Granted → Granted | `false` | `false` | Pass | Mine |
| TC-S1-09 | Granted → NotSet | `false` | `false` | Pass | Mine |
| TC-S1-10 | Revoked → PendingReaffirmation | `false` | `false` | Pass | Mine |
| TC-S1-11 | PendingReaffirmation → NotSet | `false` | `false` | Pass | Mine |

## C. My new test cases – allowed moves

Test method: `CanTransition_WhenMoveIsAllowed_ReturnsTrue`

| ID | From → To | Expected | Actual | Status | Owner |
|---|---|---|---|---|---|
| TC-S1-12 | NotSet → Granted | `true` | `true` | Pass | Mine |
| TC-S1-13 | Granted → Revoked | `true` | `true` | Pass | Mine |
| TC-S1-14 | Revoked → Granted | `true` | `true` | Pass | Mine |
| TC-S1-15 | PendingReaffirmation → Revoked | `true` | `true` | Pass | Mine |

Evidence: `evidence/13-consent-15-passed.png` (15 passed, 0 failed) and `evidence/15-retest-15-passed.png`.

## D. Web route-guard tests (Group, run only)

`web/src/routes/AppRouter.test.tsx` – 18 tests, all passed (`evidence/11-web-approuter-18-passed.png`).
Examples: unauthenticated visitor redirected to login; family user blocked from doctor routes; unverified doctor blocked from clinical routes; public pages open without sign-in.

## Summary

| | Count |
|---|---|
| Consent tests executed (final) | 15 |
| Passed | 15 |
| Failed | 0 |
| Of which written by me | 10 |
| Web route tests executed (run only) | 18 passed |

## Not covered (known gaps)

4 forbidden moves are still untested: NotSet → NotSet, Revoked → NotSet, Revoked → Revoked, PendingReaffirmation → PendingReaffirmation.
