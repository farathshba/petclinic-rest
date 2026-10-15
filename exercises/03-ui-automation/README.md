# Exercise 03 — UI Automation (optional)

**Role:** the clinic wants a smoke suite so regressions are caught before
anyone notices.

> **Honest scope note:** this repo is the REST API only — it has no browser
> UI out of the box. Choose Path A (wire the real frontend) or Path B
> (automate the built-in pages).

## Path A — wire the Angular frontend *(full experience)*

The API's companion frontend is
[spring-petclinic-angular](https://github.com/spring-petclinic/spring-petclinic-angular):

```bash
git clone https://github.com/spring-petclinic/spring-petclinic-angular.git
cd spring-petclinic-angular
npm install
# point its API base URL at http://localhost:9966/petclinic (see that repo's
# README for the exact config file), then start its dev server
```

## Path B — automate the built-in pages *(no extra repo)*

`http://localhost:9966/petclinic/swagger-ui.html` and the H2 console are
real web pages: form fills, navigation, DOM assertions all apply.

## Suggested stack

Python + Selenium + pytest + Allure:

```bash
python3 -m venv .venv && source .venv/bin/activate
pip install pytest selenium allure-pytest
pytest --alluredir=allure-results        # suites live next to this README
allure generate allure-results -o allure-report --clean
allure open allure-report                # must be served over HTTP
```

Allure output is gitignored — artifacts, not source.

## Definition of done

- [ ] At least 5 browser tests passing (navigation, fill, assert, verify)
- [ ] Tests are deterministic: fresh app restart before the run
- [ ] Allure report generated locally
- [ ] No sleeps as synchronization — use explicit waits
