# Exercises

Self-contained practice briefs. Do them in order — each one's README is
complete: goal, setup, tasks, and a definition of done.

| # | Folder | Topic | Tooling |
| --- | --- | --- | --- |
| 01 | [api-testing](01-api-testing/README.md) | REST API functional testing | Postman + Newman |
| 02 | [performance-testing](02-performance-testing/README.md) | Load / performance baseline | JMeter |
| 03 | [ui-automation](03-ui-automation/README.md) | Browser automation *(optional)* | Selenium + pytest + Allure |

## Rules of engagement

1. **Fresh seed before every run** — restart the app ([README §2](../README.md)).
2. **Never commit generated reports** — Newman HTML, `.jtl`, and Allure
   output are gitignored artifacts; attach them to your PR instead.
3. **Assertions or it didn't happen** — a request without tests is a probe,
   not a test.
4. Found a product bug? File it with the **Bug report** issue template —
   that's part of the exercise too.

## Submitting your work

Follow [CONTRIBUTING.md](../CONTRIBUTING.md): branch, Conventional Commits,
PR with the template filled in. Trainers keep verified solutions on the
`solutions` branch — compare only after you have submitted.
