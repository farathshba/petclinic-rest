# Architecture

PetClinic REST is a deliberately small Spring Boot application: a veterinary
clinic API for **owners, pets, visits, vets, specialties, and pet types**.
This page is the 10-minute orientation for contributors and curious students.

## The stack at a glance

| Layer | Technology | Notes |
| --- | --- | --- |
| Runtime | Spring Boot (embedded Tomcat) | port 9966, context path `/petclinic` |
| API spec | OpenAPI 3.1 (`src/main/resources/openapi.yml`) | **API-first**: DTOs and API interfaces are generated from the spec at build time |
| Mapping | MapStruct | entity ↔ DTO conversion |
| Persistence | Spring Data JPA | H2 (default), HSQLDB, MySQL, or PostgreSQL via profiles |
| Docs | springdoc Swagger UI | `/petclinic/swagger-ui.html` |
| Ops | Spring Boot Actuator | `/petclinic/actuator/health` |

## The API-first pipeline

```
openapi.yml  ──(OpenAPI Generator, build time)──▶  DTOs + API interfaces
                                                       │
                                            controllers implement them
                                                       │
              MapStruct ◀─────────────────────  services ↔ JPA repositories
```

Consequence for testers: the contract (`openapi.yml`) is the source of
truth. If behavior and spec disagree, that is a **finding**, not a
misunderstanding.

## Domain model

- `Owner` (a `Person`) has many `Pet`s
- `Pet` (with a `PetType`) has many `Visit`s
- `Vet` has many `Specialty`s
- `User` with roles for the optional auth tier

An ER diagram ships in the repo root: `petclinic-ermodel.png`.

## Configuration & profiles

`src/main/resources/application.properties` selects:

- **Database** — `h2` (default, in-memory, seeded at startup from
  `db/h2/`), `hsqldb`, `mysql`, `postgres`.
- **Security** — `petclinic.security.enable=true` switches on basic auth +
  role-based authorization (seeded `admin`/`admin`).

In-memory means **restart = full reset** — the reproducibility rule for every
test run lives [in the README](../README.md#2-reset-the-data-before-every-test-run).

## Ports & paths

| Service | URL |
| --- | --- |
| API base | http://localhost:9966/petclinic/ |
| Swagger UI | http://localhost:9966/petclinic/swagger-ui.html |
| OpenAPI JSON | http://localhost:9966/petclinic/v3/api-docs |
| Actuator health | http://localhost:9966/petclinic/actuator/health |
| H2 console | http://localhost:9966/petclinic/h2-console (sa / blank) |

## Provenance

Forked from [spring-petclinic/spring-petclinic-rest](https://github.com/spring-petclinic/spring-petclinic-rest)
(Apache 2.0). Upstream's full endpoint reference is kept in `readme.md`.
