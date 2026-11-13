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
A ticket made from a delivery item carries its checks: a short statement, Automated or Manual, and
"How to reproduce" steps (what is done, what should be seen).
- Write one test per automated check that does exactly what its steps say and asserts what they say
  should be seen. Unit or API level when the check is about logic or data, browser (e2e) when it is
  about what a user does and sees.
- Give the test a plain, stable title that states the check in words ("wrong password shows an error").
  painless links checks to tests by "file › title", so renaming or moving a proving test breaks the
  link: keep titles stable, and say in the PR when you rename or move one.
- If the ticket gives the check's id (`c-xxxxxx`), you may put it in the title, `[c-xxxxxx] …`: that
  proves the check without linking.
- Manual checks get no test; don't fake one.
