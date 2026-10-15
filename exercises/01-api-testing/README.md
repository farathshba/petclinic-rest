# Exercise 01 — API Testing (Postman + Newman)

**Role:** you are the QA engineer for a small veterinary clinic's API.
Your job: prove the owner-management flows work — and find what does not.

## Setup

1. App running fresh: [`README §1`](../../README.md#1-run-the-application)
2. Node.js installed, then:

```bash
npm install -g newman newman-reporter-htmlextra
```

## Part 1 — Get the baseline green

Run the bundled non-regression suite
([`docs/testing-guide.md`](../../docs/testing-guide.md) has the full
commands). **Green = 7 requests, 12 assertions, 0 failures.** Read the
collection's scripts while it runs — that is your style reference.

## Part 2 — Build your own suite

Open [`starter/petclinic-starter.postman_collection.json`](starter/petclinic-starter.postman_collection.json)
in Postman. Request 1 is solved for you. Complete the TODOs:

| # | Request | Must assert |
| --- | --- | --- |
| 2 | `GET /api/owners/{ownerId}` | 200; body contains the owner's fields |
| 3 | `GET /api/owners/999999` | 404 |
| 4 | `POST /api/owners` | 201; capture the new `id` into a variable |
| 5 | `PUT /api/owners/{ownerId}` | expected status; then `GET` proves the change persisted |
| 6 | `DELETE /api/owners/{ownerId}` | expected status; then `GET` proves it is gone (404) |

Run it headless:

```bash
newman run starter/petclinic-starter.postman_collection.json \
  -r cli,htmlextra --reporter-htmlextra-export my-report.html
```

## Part 3 — Stretch goals

- Negative/validation cases: empty `firstName`, letters in `telephone`,
  future `birthDate` — what does the API actually return?
- The CRUD matrix: one full row for `pets` or `visits`.
- Auth tier: restart with `petclinic.security.enable=true` and prove
  401/403/200 per role (`docs/architecture.md`).

## Definition of done

- [ ] Part 1 suite green (7 requests / 12 assertions / 0 failures)
- [ ] All 6 starter requests implemented **with assertions**
- [ ] At least 3 negative cases
- [ ] Newman HTML report generated (attached, not committed)
- [ ] App restarted before each run
