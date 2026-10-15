# Testing Guide

How to test this application properly. Start here before
[`exercises/`](../exercises/README.md).

## The golden rule: fresh data

The H2 database is in-memory and seeded at every startup. Suites mutate
records, so **results are only comparable from a fresh seed**:

```bash
# Ctrl+C the app, then
./mvnw spring-boot:run
```

Restart before every run. No exceptions — this is how your runs stay
reproducible.

## Know the contract first

Before writing tests, read the spec the server is built from:

- Interactive: [Swagger UI](http://localhost:9966/petclinic/swagger-ui.html)
- Machine-readable: `src/main/resources/openapi.yml` (OpenAPI 3.1)
- Full endpoint table: [`readme.md`](../readme.md)

Anything where behavior and `openapi.yml` disagree is a finding.

## Black-box test design (manual & automated)

Map techniques onto real fields — do not test randomly:

| Technique | Where it applies here |
| --- | --- |
| **Equivalence partitioning** | `owner.telephone` (digits / letters / empty), `pet.name` (empty string), `pet.birthDate` |
| **Boundary values** | dates at yesterday / today; pet name length limits; ID boundaries (`/api/owners/0`, large IDs) |
| **Negative testing** | `GET /api/owners/999999` → 404; invalid JSON body → 400; `PUT` on a deleted owner |
| **CRUD matrix** | each resource (owners, pets, vets, pettypes, specialties, visits) × each verb (GET/POST/PUT/DELETE) — a full row is a test suite on its own |
| **ID chaining** | create owner → add pet → add visit: assert state at each step, in order |
| **Auth tier** | with `petclinic.security.enable=true`: no credentials → 401; wrong role → 403; `admin/admin` → 200 |

## API automation with Postman/Newman

The repo ships a non-regression suite in `src/test/postman/`:

```bash
npm install -g newman newman-reporter-htmlextra
./mvnw spring-boot:run                                   # terminal 1
cd src/test/postman                                       # terminal 2
newman run petclinic-nonregressiontests.postman_collection.json \
  -e petclinic-env.postman_environment.json \
  -r cli,htmlextra --reporter-htmlextra-export reports/report.html
```

**Green = 7 requests, 12 assertions, 0 failures.** Study the collection's
test scripts — then extend it or build your own from
[`exercises/01-api-testing/starter`](../exercises/01-api-testing/README.md).

## Performance testing mini-primer

JMeter works out of the box against this API. Full brief:
[`exercises/02-performance-testing`](../exercises/02-performance-testing/README.md).

```bash
jmeter -n -t your-plan.jmx -l results.jtl -e -o dashboard/
```

`results.jtl` and `dashboard/` are artifacts — never commit them.

## Reporting hygiene

- Generated reports (Newman HTML, `.jtl`, Allure output) are **gitignored
  artifacts**. Attach them to a PR or keep them locally — do not force-add.
- A test without an assertion is not a test.
- File product defects you find via the **Bug report** issue template —
  endpoint, payload, expected vs. actual. Real trackers beat screenshots.
