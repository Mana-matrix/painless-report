# painless report

Lets painless prove the automated checks of a delivery with your
own CI. Your tests stay as they are: painless reads their results and you link each check to the tests
that prove it (by hand or with "Match tests (AI)" on the delivery protocol).

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

Copy [`CLAUDE.md`](CLAUDE.md) into your repo (or append it to yours): Claude then sets this up and writes
tests that prove delivery checks.
