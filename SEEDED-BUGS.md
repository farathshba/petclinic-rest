# 🔑 Seeded Defects — Trainer Answer Key

> **Trainer only.** This file lives on the `solutions` branch. Never merge it
> into `master` or `challenge`. It documents the six defects seeded on the
> `challenge` branch so trainers can grade submissions, verify fixes, and
> answer "is this a real bug?" questions during the exercise.

The seeded diff is a single commit on top of `master`:
`git log master..challenge` — but discourage students from reading it; the
exercise is black-box.

## Severity model

**Critical** — security boundary broken · **Major** — documented API behaviour
wrong · **Minor** — validation/persistence gap on an edge case.

## The six defects

| ID | Where | Seeded change | Symptom | Expected vs actual | Severity | How students should find it | Fix (revert to master) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| BUG-01 | `OwnerRestControllerV1.addOwner` | `HttpStatus.CREATED` → `HttpStatus.OK` | `POST /api/owners` | 201 Created + Location header vs 200 OK | Major | Status-code assertion on any POST — **the bundled suite catches this one** ("Register a Owner" expects 201) | Restore `HttpStatus.CREATED` |
| BUG-02 | `OwnerRestControllerV1.getOwner` | null-check guard removed | `GET /api/owners/99999` | 404 Not Found vs 200 with empty body | Major | Boundary test with an unknown owner ID | Re-add the `if (owner == null) return NOT_FOUND` guard |
| BUG-03 | `OwnerRestControllerV1.deleteOwner` | `clinicService.deleteOwner(owner)` call removed | `DELETE /api/owners/{id}`, then re-GET the same ID | Owner gone (404 on re-GET) vs still present | Major | Verify-after-delete read-back | Restore the `deleteOwner` service call |
| BUG-04 | `OwnerRestControllerV1.updateOwner` | `setTelephone(...)` line removed | `PUT /api/owners/{id}` with a new telephone, then read back | Telephone persisted vs silently unchanged | Minor | Full-field update then read-back comparison (the bundled suite only asserts firstName/lastName, so this slips past it) | Restore the `setTelephone` line |
| BUG-05 | `validation/PetAgeValidator` | future-birth-date validation block removed | `POST /api/owners/{id}/pets` with a future `birthDate` | 400 "Birth date cannot be in the future" vs 201 accepted | Minor | Validation/boundary tests derived from the openapi contract | Re-add the `isAfter(today)` block |
| BUG-06 | `UserRestControllerV1.addUser` | `@PreAuthorize("hasRole(@roles.ADMIN)")` → `hasRole(@roles.OWNER_ADMIN)` | With security enabled, an owner-scoped user calls `POST /api/users` | 403 Forbidden vs 201 Created — privilege escalation | **Critical** | Enable auth (README §4) and probe role boundaries | Restore `hasRole(@roles.ADMIN)` |

## Coverage notes

- **Only BUG-01 is caught by the bundled Newman suite** (the "Register a Owner"
  request asserts status 201 and will fail; the request body still parses, so
  the rest of the suite keeps running). Students who re-run the suite see one
  red assertion — a good first clue, but it must not be their last.
- **BUG-02, BUG-03, BUG-04** all live on the owners endpoints the suite already
  touches — but on paths it never exercises (unknown IDs, DELETE, full-field
  read-back). Great teaching point: happy-path coverage ≠ defect coverage.
- **BUG-05** needs a deliberately invalid input (future birthDate).
- **BUG-06** needs the optional auth tier enabled; only students who read the
  README thoroughly and test negative authorization will find the critical one.

## Grading tips

- A finding without a reproducing test scores half; a test without an issue
  scores half — the scoring table in `CHALLENGE.md` (on the `challenge`
  branch) is designed to reward the full evidence chain.
- Accept equivalent fixes, not just reverts: e.g. BUG-02 may be fixed in the
  service layer or with a proper 404 response body, as long as the contract is
  honoured.
