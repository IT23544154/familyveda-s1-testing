# Viva study notes (plain English)

## 1. The big idea in 30 seconds

My part of the app is the **door** of Family Veda: who logs in, and who allows whom to see data (**consent**).
Consent has 4 states, like a door lock:

| State | Meaning |
|---|---|
| NotSet | Nobody has decided yet |
| Granted | "Yes, you may see my data" |
| Revoked | "No, I take my permission back" |
| PendingReaffirmation | A parent said yes for a child. The child turned 18 and must confirm it themselves |

Only some moves between states make sense. Example: you cannot **revoke** something that was **never set**.
The code that decides this is `ConsentStateMachine.CanTransition(from, to)`. It returns `true` (allowed) or `false` (forbidden).

**My test job:** prove the code says YES to the allowed moves and NO to the forbidden moves.

## 2. The numbers to remember

- 4 states → 4 × 4 = **16 possible moves**
- **6 allowed**, **10 forbidden**
- Original tests: 5. I added **10** cases → **15** total.
- My 10 cases: 4 allowed + 6 forbidden. Allowed: now **6 of 6** covered. Forbidden: **6 of 10** covered.

## 3. How to read my test (line by line)

```csharp
[Theory]                                                     // one test, run many times with different data
[InlineData(ConsentStatus.NotSet, ConsentStatus.Revoked)]    // data row 1: from NotSet to Revoked
[InlineData(ConsentStatus.Granted, ConsentStatus.Granted)]   // data row 2 ...
public void CanTransition_WhenMoveIsNotAllowed_ReturnsFalse(ConsentStatus from, ConsentStatus to)
{
    Assert.False(ConsentStateMachine.CanTransition(from, to)); // the check
}
```

- **`[Fact]`** = a test with no inputs, runs once.
- **`[Theory]` + `[InlineData]`** = the same exam question asked with different numbers. Each row is its own test case.
- **Assert** = the line that decides pass/fail. `Assert.False(x)` passes only if `x` is `false`.
- **Arrange / Act / Assert** = prepare, call the code once, check the result.
- **Technique:** State Transition testing. (Test allowed moves AND forbidden moves.)
- **Why `xUnit`?** It is the standard .NET test tool, and `[Theory]` lets me test many cases without copy-pasting.

## 4. What each screenshot shows (in order)

| File | What to say |
|---|---|
| 01 | I checked my tools: .NET 8.0.425 and Node v24.14.0 |
| 02 | My C: drive is full, so I moved the NuGet and npm download caches to E: |
| 03–04 | First run of the consent tests, before my changes: **5 passed** |
| 05 | `npm install` for the web app (the "6 vulnerabilities" message is about packages, not my tests) |
| 06 | Web test run. The yellow `act(...)` text is a **warning**, not a failure |
| 11 | Web router tests: **18 passed**. These are the group's tests; I ran them |
| 12 | The code I added at the bottom of the test file |
| 13 | First failed command was the wrong folder (`MSB1009 Project file does not exist`). I moved into the inner project folder and it ran: **15 passed** |
| 14 | One test **failed**: Granted → Revoked gave `false`, expected `true` |
| 15 | After I fixed it: **15 passed** again |

## 5. The failure in screenshot 14 (be ready for this one)

- Test failed: `CanTransition_WhenMoveIsAllowed_ReturnsTrue (from: Granted, to: Revoked)`
- Message: `Expected: True, Actual: False`, at test file **line 84**
- Meaning: the test said "this move must be allowed", but the code said "forbidden".
- In the group's real code the line is `(Granted, Revoked) => true`, so for it to return `false` that line must have been changed in my copy.
- **Say exactly what you did.** If you changed it on purpose to prove the test works, say: "I injected a fault on purpose, the test caught it, I restored it, and the retest passed." That is a legitimate technique. Do **not** say it was a bug in the group's system.
- Why it matters: if this were real, a user could never withdraw their consent. That is a privacy failure.

## 6. Likely questions and short answers

**What did you test?** The consent state machine: which moves between the 4 consent states are allowed and which are forbidden, plus the turning-18 rule.

**Which tests did you write yourself?** Two theories with 10 data rows: 6 forbidden moves and 4 allowed moves. The first 5 tests were already in the file.

**Why test forbidden moves?** Because bugs hide there. The original tests only checked two allowed moves, so a wrong "yes" would never be noticed.

**What does a Theory do?** Runs one test method once per `[InlineData]` row, so 10 rows = 10 test cases.

**How do you run it?** `dotnet test backend/tests/UnitTests/FamilyVeda.UnitTests.csproj --filter "FullyQualifiedName~ConsentStateMachineTests"` from the project root.

**What does `--filter` do?** Runs only tests whose name contains `ConsentStateMachineTests`, not all 430.

**What did the `act(...)` warning mean?** React says a screen update happened outside its test helper. The tests still passed, so it is a warning only.

**Did you write the web tests?** No. They are the group's `AppRouter.test.tsx`. I ran them to check the route guard (who may open which page) and can explain what they check.

**What are the limits of your testing?** 4 forbidden moves are still untested. Unit tests only prove the rules in code, not the full app with the database.

**Did you use AI?** Answer truthfully and say what you checked and ran yourself. The brief allows AI but requires that you can explain and reproduce your work.

## 7. Practise these three things on your laptop (10 minutes)

1. Run the consent tests and read the summary line (`Passed! - Failed: 0, Passed: 15`).
2. Make one fail on purpose: in `ConsentStateMachineTests.cs` change one `Assert.True` to `Assert.False`, run, read `Expected` vs `Actual`, then `git restore` the file.
3. Add one new `[InlineData]` row and run again. The total should go up by one.

## 8. Optional: close the remaining gap (4 more rows)

Add these 4 rows above the `public void CanTransition_WhenMoveIsNotAllowed_ReturnsFalse` line:

```csharp
[InlineData(ConsentStatus.NotSet, ConsentStatus.NotSet)]
[InlineData(ConsentStatus.Revoked, ConsentStatus.NotSet)]
[InlineData(ConsentStatus.Revoked, ConsentStatus.Revoked)]
[InlineData(ConsentStatus.PendingReaffirmation, ConsentStatus.PendingReaffirmation)]
```

Run the tests. If it says 19 passed, you have tested **all 16** moves. Then update the numbers in the README and the test case doc, and take a new screenshot.
