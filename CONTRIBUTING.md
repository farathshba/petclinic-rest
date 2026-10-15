# Contributing to PetClinic REST

Thanks for improving this project — whether you are a **student** working
through `exercises/` or a **contributor** fixing and extending the app.

## Ground rules

- Be excellent to each other — see [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md).
- This repo is a fork of
  [spring-petclinic/spring-petclinic-rest](https://github.com/spring-petclinic/spring-petclinic-rest).
  Fixes that are generally useful belong **upstream**; keep this fork for
  event-specific material and exercises.

## Development setup

- **JDK 17+** and Git. No separate Maven install needed.

```bash
./mvnw spring-boot:run     # run the app  → http://localhost:9966/petclinic/
./mvnw test               # run the Java test suite
```

The default database is in-memory H2, re-seeded at every startup — a
restart is a full reset.

## Workflow

1. Fork, then branch from `master` with a prefix:
   `feat/`, `fix/`, `docs/`, `test/`, `chore/`.
2. Use [Conventional Commits](https://www.conventionalcommits.org/):
   `feat: ...`, `fix: ...`, `docs: ...`.
3. Open a PR against `master` and fill in the PR template.
4. CI must be green before review.

## Test-related contributions

- **Never commit generated reports** (Newman HTML, `.jtl`, Allure output).
  They are build artifacts, gitignored — attach them to the PR or link them.
- Every run starts from a **freshly seeded database** (restart the app).
- New requests in a Postman collection must include assertions — a request
  without tests is a manual probe, not an automated test.

## Filing bugs

Use the **Bug report** issue template. A good report includes: the exact
request (URL + payload), expected vs. actual behaviour, and how you ran the
app (`mvnw` vs Docker, JDK version).
