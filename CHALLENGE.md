# 🐛 Bug Hunt Challenge

This branch contains the same PetClinic REST application as `master` — it
compiles, boots, and the Swagger UI works — but **six defects have been
deliberately seeded** into the source code.

Your mission: **find all six, prove each one with a failing test, and fix it.**

## Rules of engagement

1. **Black-box only.** Do not diff this branch against `master` and do not
   look for an answer key. Work from the documented contract —
   `src/main/resources/openapi.yml` and the live Swagger UI — exactly like
   a professional tester would.
2. **The app boots normally.** Nothing crashes at startup, and the sample data
   is re-seeded on every restart (see the README).
3. **Run the bundled suite first.** It will catch at least one defect — but not
   all six. The rest only surface in tests you write yourself.
4. **Evidence or it didn't happen.** File every defect using the **Bug report**
   issue template: expected vs actual, the exact request and response, and a
   severity rating with business impact.
5. **One fix per PR.** Each fix must be accompanied by the automated test that
   catches it. Follow the branch naming and Conventional Commits rules in
   [CONTRIBUTING.md](CONTRIBUTING.md).

## Where to hunt

- **Happy paths** — exercise every CRUD operation on every resource
- **Boundaries** — unknown IDs, empty bodies, invalid and future dates
- **The contract** — does every response match `openapi.yml` (status codes, headers, schema)?
- **Persistence** — after any write, read it back. Is everything you sent actually there?
- **Access control** — enable authentication (README §4) and probe role boundaries

## Scoring

| Deliverable | Points |
| --- | --- |
| Defect identified and filed as a bug-report issue | 6 × 10 |
| Automated test per defect (committed with the fix) | 6 × 5 |
| Correct severity + business-impact justification | 6 × 3 |
| Clear repro steps and request/response evidence | 6 × 2 |

Total: 120 points. Partial credit applies per defect.

## Definition of done

- [ ] Six bug-report issues filed with evidence
- [ ] Each defect reproduced by a committed automated test
- [ ] Each defect fixed; your tests and the bundled suite are green
- [ ] PRs follow [CONTRIBUTING.md](CONTRIBUTING.md) (one fix per PR)

Stuck? Re-run the bundled suite and ask *why* each assertion failed. Then
write one exploratory test per endpoint you haven't covered yet.

Good hunting! 🐾
