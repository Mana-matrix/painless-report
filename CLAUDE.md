# Delivery checks (painless)

This project's deliveries are accepted in painless: each delivered item has checks, and automated checks
are proven by this repo's tests. painless reads the JUnit report our CI uploads as the artifact
`painless-tests` (action `Mana-matrix/painless-report`) from the newest finished run on the delivery
branch (`delivery/**`).

## Keep the report working
- The test workflow runs on pull requests and on `push` to `delivery/**`, writes JUnit XML for every test
  runner and ends with `uses: Mana-matrix/painless-report@v1` (`if: always()`, `path` = the XML files).
  When you add a test runner or a test job, make it write JUnit into the same glob.
- Never skip, disable or quarantine a test to get green: a check proven by a skipped test is not proven.

## Writing tests for a check
A ticket made from a delivery item lists its acceptance checks (in painless and in its GitHub issue,
section "Tests to write"): each automated one with the test's name and "How to reproduce" steps.
- Name the test exactly as the ticket says: `[c-xxxxxx] <check>`. The id in brackets is how painless
  finds the test in the CI report; keep it when you rename or move the test.
- The test does exactly what the steps say and asserts what they say should be seen. Unit or API
  level when the check is about logic or data, browser (e2e) when it is about what a user does and sees.
- One test per automated check (a test may carry several ids when it really proves several checks).
- A check marked "already proven by …" needs no new test: keep the named test working.
- Manual checks get no test; don't fake one.

## Commits by Claude
- Commit as `claude-<handle>`, where `<handle>` is the GitHub handle of the person you work for in this
  session (e.g. `claude-octocat`), email `noreply@anthropic.com`. Set it before your first commit:
  `git config user.name "claude-<handle>" && git config user.email noreply@anthropic.com`.
  That keeps your work apart from the person's own commits in painless (git activity, time tracking)
  while showing whose session made it.
- If you don't know the handle, ask once; don't guess it from a name or an e-mail.
