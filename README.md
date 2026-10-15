# PetClinic REST — Software Testing Practice Target 🐾

This repository is the **application under test**: a deliberately simple
veterinary-clinic REST API (Spring Boot) that runs locally in one command,
plus a bundled API test suite (Postman/Newman) you can run, study, and
extend.

> Forked from [spring-petclinic/spring-petclinic-rest](https://github.com/spring-petclinic/spring-petclinic-rest) (Apache 2.0).
> The original `readme.md` (kept unchanged) documents every endpoint in detail.

## Repository Map

| Path | What it is |
| --- | --- |
| `src/main/java/...` | The Spring Boot REST API — owners, pets, vets, visits, pet types, specialties, users (full CRUD) |
| `src/main/resources/openapi.yml` | **The API contract** — OpenAPI 3.1 spec the server code is generated from |
| `src/test/postman/` | Bundled Postman/Newman non-regression suite + environment files |
| `exercises/` | Self-contained practice briefs — API, performance, UI automation |
| `docs/` | [Architecture overview](docs/architecture.md) and the [testing guide](docs/testing-guide.md) |
| `CONTRIBUTING.md` | How to submit tests and fixes (branch naming, Conventional Commits, PR rules) |
| `CHANGELOG.md` | What changed, per version — and how this fork diverges from upstream |
| `postman-tests.sh` | One-shot runner for the Newman suite (macOS/Linux) |
| `readme.md` | Original upstream README — full endpoint reference table |
| `pom.xml`, `mvnw` | Build definition + Maven wrapper (no separate Maven install needed) |

## 1. Run the Application

**Prerequisites (pick one):**

- **JDK 17+** — recommended; the build enforces the version via maven-enforcer, **or**
- **Docker**

```bash
git clone https://github.com/farathshba/petclinic-rest.git
cd petclinic-rest
./mvnw spring-boot:run
```

(On Windows, use `mvnw.cmd` instead of `./mvnw`. The first run downloads
dependencies — allow a few minutes.) Wait for:

```
Tomcat started on port 9966 ... Started PetClinicApplication
```

Docker alternative:

```bash
docker run -p 9966:9966 springcommunity/spring-petclinic-rest
```

### Where everything is

| Service | URL | Notes |
| --- | --- | --- |
| API base | http://localhost:9966/petclinic/ | context path `/petclinic` |
| Swagger UI | http://localhost:9966/petclinic/swagger-ui.html | explore & try every endpoint |
| OpenAPI 3.1 docs (JSON) | http://localhost:9966/petclinic/v3/api-docs | the machine-readable contract |
| Actuator health check | http://localhost:9966/petclinic/actuator/health | smoke-test endpoint |
| H2 database console | http://localhost:9966/petclinic/h2-console | inspect the data behind the API |

Verify the stack:

```bash
curl http://localhost:9966/petclinic/actuator/health     # {"status":"UP",...}
curl http://localhost:9966/petclinic/api/owners          # seeded sample owners
```

H2 console login: JDBC URL `jdbc:h2:mem:petclinic`, username `sa`,
blank password.

## 2. Reset the Data Before Every Test Run

The default database is an **in-memory H2 database, seeded with sample data
at every startup**. Test suites create/edit/delete records — results are
only reproducible from a fresh seed. **Always start a test run with a
freshly started app:**

```bash
# stop the app (Ctrl+C), then
./mvnw spring-boot:run
```

A restart is a full reset: the in-memory DB is wiped and re-seeded
automatically. (Docker: stop the container and `docker run` a fresh one.)

## 3. Run the Bundled API Test Suite (Postman/Newman)

The repo ships a non-regression suite covering two customer scenarios:

1. **01 - First Visit** — register owner → register pet → schedule first visit
2. **02 - Returning Customer** — update owner → confirm update → update pet → new visit

```bash
npm install -g newman newman-reporter-htmlextra    # once (needs Node.js)

./mvnw spring-boot:run                             # terminal 1 — fresh app

cd src/test/postman                                # terminal 2
newman run petclinic-nonregressiontests.postman_collection.json \
  -e petclinic-env.postman_environment.json \
  -r cli,htmlextra --reporter-htmlextra-export reports/report.html
```

**Green = 7 requests, 12 assertions, 0 failures.**
The HTML report lands in `src/test/postman/reports/report.html`.

macOS/Linux shortcut: run `./postman-tests.sh` from the repo root (needs
`node` and `jq` installed).

The collection is also your starting point — import
`petclinic-nonregressiontests.postman_collection.json` into the Postman
desktop app, study its scripts, and add your own requests and assertions.

## 4. Optional: Turn On Authentication

Authentication is **OFF by default** so the API is easy to explore. For a
second, harder testing tier, set in `src/main/resources/application.properties`:

```properties
petclinic.security.enable=true
```

Restart the app — every endpoint now requires basic auth. Seeded user:
`admin` / `admin`. Roles control access:

| Role | Can access |
| --- | --- |
| `OWNER_ADMIN` | owners, pets, visits |
| `VET_ADMIN` | vets, specialties, pet types |
| `ADMIN` | users (`POST /api/users` creates new users) |

## 5. Exercises

Structured practice briefs live in [exercises/](exercises/README.md):

| # | Topic | Tooling |
| --- | --- | --- |
| [01](exercises/01-api-testing/README.md) | API testing | Postman + Newman |
| [02](exercises/02-performance-testing/README.md) | Performance baseline | JMeter |
| [03](exercises/03-ui-automation/README.md) | UI automation *(optional)* | Selenium + pytest + Allure |

Reports you generate (Newman HTML, `.jtl`, Allure) are **artifacts —
gitignored, never committed**; attach them to your PR instead. Found a
product bug? File it via the **Bug report** issue template — real tracker,
real practice.

## Things to Test (Ideas)

- CRUD happy paths for each resource; 404 on unknown IDs; 400 on invalid bodies
- Field validation rules from `src/main/resources/openapi.yml` (dates, enums, required fields)
- ID chaining (owner → pets → visits) and cross-resource integrity
- Error responses: what happens with duplicate names, future birth dates, empty strings?
- Performance: JMeter against `GET /api/owners` / login-style flows
- Contract testing: compare live `/v3/api-docs` against `openapi.yml`
- UI automation: pair with the [Angular frontend](https://github.com/spring-petclinic/spring-petclinic-angular) if your module requires a browser UI

## Troubleshooting

- **Port 9966 already in use** — stop the other instance (`lsof -i :9966` on macOS/Linux).
- **"This build requires at least Java ..."** — your JDK is too old; install JDK 17+ and re-run.
- **First `./mvnw` run is slow / fails offline** — the wrapper downloads Maven and dependencies on first use; it needs internet access.
- **My data disappeared after restart** — expected: the H2 database is in-memory; every startup re-seeds the same sample data.
- **Newman not found** — `npm install -g newman newman-reporter-htmlextra`.
- **H2 console rejected login** — use JDBC URL `jdbc:h2:mem:petclinic`, user `sa`, empty password.

## Credits

Application: the Spring team's [Spring PetClinic REST](https://github.com/spring-petclinic/spring-petclinic-rest) sample, Apache License 2.0.
This README is the student-facing quickstart; see `readme.md` for the complete upstream documentation and endpoint list, and [CHANGELOG.md](CHANGELOG.md) for how this fork diverges.
