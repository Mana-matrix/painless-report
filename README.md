# painless report

Lets painless prove the automated checks of a delivery with your
own CI. Your tests stay as they are: painless reads their results and you link each check to the tests
that prove it (by hand or with "Match tests (AI)" on the delivery protocol).

## Get started

### With Claude Code (no CI knowledge needed)

Open your repo in Claude Code and paste:

> Set up painless-report in this repo: follow https://github.com/Mana-matrix/painless-report.
> Make every test runner write JUnit XML, add the painless-report step to our test workflow
> (create one if there is none) running on pull requests and on pushes to `delivery/**`,
> save its CLAUDE.md as `.claude/painless.md` and add the line `@.claude/painless.md` to our CLAUDE.md.

Check the pull request it makes, merge it, then do step 3 below in painless.

### By hand

1. Make your test runners write JUnit XML (table below).
2. Add the step to your test workflow (below).
3. Give Claude Code the rules: `mkdir -p .claude && curl -fsSL https://raw.githubusercontent.com/Mana-matrix/painless-report/main/CLAUDE.md -o .claude/painless.md`,
   then add the line `@.claude/painless.md` to your `CLAUDE.md` (create it if there is none).
   Run the same `curl` again to update.
4. In painless: link the branch and the tests (step 3 below).

No submodule, nothing to install: GitHub fetches the action on every run.

## 1. Write a JUnit report

Every common runner can write one. Put the files anywhere; the default glob is `**/junit*.xml`.

| Runner | How |
|---|---|
| Vitest | `vitest run --reporter=default --reporter=junit --outputFile.junit=reports/junit-unit.xml` |
| Jest | `npm i -D jest-junit`, then `jest --reporters=default --reporters=jest-junit` (`JEST_JUNIT_OUTPUT_DIR=reports`, `JEST_JUNIT_OUTPUT_NAME=junit-jest.xml`) |
| Playwright | `playwright test --reporter=list,junit` with `PLAYWRIGHT_JUNIT_OUTPUT_NAME=reports/junit-e2e.xml` |
| pytest | `pytest --junitxml=reports/junit-py.xml` |
| Go | `go install gotest.tools/gotestsum@latest`, then `gotestsum --junitfile reports/junit-go.xml` |
| PHPUnit | `phpunit --log-junit reports/junit-php.xml` |

## 2. Upload it

Add the step after your tests, and run the workflow on pushes to your delivery branches (a merge there gets
a run of its own, which is the one painless reads):

```yaml
on:
  pull_request:
  push:
    branches: ['delivery/**']

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v5
      # … install, run your tests (writing JUnit, see above) …
      - uses: Mana-matrix/painless-report@v1
        if: always()            # failed tests are results too
        with:
          path: 'reports/junit*.xml'
```

## 3. Link the branch in painless

Delivery protocol → Proof by tests → Link a branch (a repo linked to the project, the branch, e.g.
`delivery/p2`). painless reads the newest finished run on it. Then link each automated check to its tests
(the check's **Tests** button) or let **Match tests (AI)** propose the links.

A test may also name a check's id in its title, `it('[c-7k2m9q] wrong password shows an error', …)`;
both ways count. A linked test that disappears from the report (renamed, deleted) shows as gone: the check
isn't proven until you link it again.

The painless GitHub App needs **Actions: read** on the repo to download the report.

## Claude Code

[`CLAUDE.md`](CLAUDE.md) is meant for your repo (as `.claude/painless.md`, imported from your own
`CLAUDE.md`): Claude Code then keeps the report working, writes tests that prove delivery checks and
commits as `claude-<your GitHub handle>`, so painless tells its work apart from yours.
