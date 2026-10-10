# S1 Defect / Fault Report

| Field | Value |
|---|---|
| Defect ID | S1-FI-01 |
| Component | Consent state machine – `ConsentStateMachine.CanTransition` |
| Found by | xUnit test `CanTransition_WhenMoveIsAllowed_ReturnsTrue(from: Granted, to: Revoked)` |
| Description | The allowed move **Granted → Revoked** returned `false`. Expected `true`. |
| Impact | A member who had granted consent could not withdraw it. For a health app this is a serious privacy problem. |
| Severity / Priority | High / P1 (if it had been real) |
| How it was found | Test run: `Failed! - Failed: 1, Passed: 14, Total: 15` |
| Evidence (failing) | `evidence/14-failing-test-granted-to-revoked.png` (error: `Assert.True() Failure, Expected: True, Actual: False`, test file line 84) |
| Root cause | `CanTransition` returned `false` for (Granted, Revoked). The group code has `(Granted, Revoked) => true`, so the rule had been changed. |
| Origin of the fault | [CONFIRM: write what you really did, e.g. "I temporarily changed `(Granted, Revoked) => true` to `false` on purpose to prove the new test catches it (fault injection)", or the real cause] |
| Fix | Restored `(ConsentStatus.Granted, ConsentStatus.Revoked) => true` in `ConsentStateMachine.cs` |
| Status | Closed |
| Retest | Same command re-run: `Passed! - Failed: 0, Passed: 15, Total: 15` (`evidence/15-retest-15-passed.png`) |

## Notes

- The group repo's `develop` branch has the correct rule, so this was **not** a bug in the delivered system. Do not present it as one.
- Value of the exercise: it shows the new tests really detect a broken rule (a test that can never fail proves nothing).
- The last run used `-m:1 -nodeReuse:false`. These only make MSBuild build one project at a time and not keep background build processes, to avoid file-lock problems. They do not change test results.
