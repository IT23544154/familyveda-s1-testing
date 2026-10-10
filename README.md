# Family Veda – S1 Testing Contribution (SE3090 Assignment 2 / SE3110)

**Student:** Samaranayaka S.G.V.S (IT23544154) · **Group member:** S1
**Component tested:** Family, Identity & Consent (consent state machine) + web route guard run
**Group project:** https://github.com/sahansbandara/Family-Veda-SEF-Project (branch `develop`)

This repo holds **only my part** of the testing work: the test code I wrote, the commands I ran,
the screenshots (evidence) and my notes. The tests run inside the group project, so the test file here
mirrors the group repo's folder path.

## What is in this repo

| Path | What it is |
|---|---|
| `backend/tests/UnitTests/Component1_Family/ConsentStateMachineTests.cs` | Test file: the original 5 tests + **my 10 new test cases** |
| `code-under-test/ConsentStateMachine.cs` | Unmodified copy of the code being tested (reference only) |
| `evidence/` | Numbered screenshots, in the order I did the work |
| `docs/S1_Test_Cases.md` | Test case table (ID, input, expected, actual, Pass/Fail) |
| `docs/S1_Defect_Report.md` | Defect / fault-injection record with retest |
| `docs/VIVA_STUDY_NOTES.md` | My plain-English notes for the viva |
| `docs/original-test-log.docx` | My original Word log of the work |

## Tools

| Tool | Version | Used for |
|---|---|---|
| .NET SDK | 8.0.425 | Build + run backend tests |
| xUnit (`[Fact]`, `[Theory]`, `[InlineData]`) | via test project | Unit tests for the consent state machine |
| Node.js | v24.14.0 | Run the web tests |
| Vitest + React Testing Library | via `web/package.json` | Run the group's route-guard tests (`AppRouter.test.tsx`) |

## How to re-run (inside the group project)

1. Open the group project folder in VS Code (the folder that contains `backend` and `web`).
2. Copy `backend/tests/UnitTests/Component1_Family/ConsentStateMachineTests.cs` from this repo over the same path in the group project.
3. In the terminal, from the project root:

```powershell
dotnet test backend/tests/UnitTests/FamilyVeda.UnitTests.csproj --filter "FullyQualifiedName~ConsentStateMachineTests"
```

Web tests:

```powershell
cd web
npm install
npx vitest run src/routes/AppRouter.test.tsx
```

## Results

| Run | Tool | Result | Evidence |
|---|---|---|---|
| Consent tests, before my edit | xUnit | 5 passed, 0 failed | `evidence/04` |
| Consent tests, after adding my 10 cases | xUnit | **15 passed**, 0 failed | `evidence/13` |
| Failing run (code temporarily changed, see defect report) | xUnit | 14 passed, **1 failed** | `evidence/14` |
| Retest after restoring the code | xUnit | **15 passed**, 0 failed | `evidence/15` |
| `AppRouter.test.tsx` (group's existing tests, I ran them) | Vitest | 18 passed | `evidence/11` |

The `act(...)` warnings in `evidence/06-*` are React warnings, not failures. All 18 tests still passed.

## What I wrote vs. what I only ran (honest split)

- **Written by me:** the two `[Theory]` tests with 10 `[InlineData]` rows (6 forbidden moves, 4 allowed moves).
- **Not written by me:** the original 5 consent tests and the 18 `AppRouter` tests. I ran them and can explain them.

## Gap these tests close

The group's viva guide (section I2) says the original consent tests covered no forbidden transition.
My cases cover all 6 allowed moves and 6 of the 10 forbidden moves of the 4-state consent machine.

## AI declaration

[CONFIRM and edit before submitting: say which AI tools you used, for what (e.g. understanding the project,
preparing study notes), and that you ran and checked every test yourself.]
