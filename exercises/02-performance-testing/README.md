# Exercise 02 — Performance Testing (JMeter)

**Role:** the clinic expects a class of 30 testers hitting the API at once.
Your job: establish a performance baseline and find where it bends.

## Setup

1. App running fresh: [`README §1`](../../README.md#1-run-the-application)
2. [Apache JMeter](https://jmeter.apache.org/download_jmeter.cgi) 5.6+

## Build the plan

1. **Thread Group** — 10 threads, 60 seconds, ramp-up 10s.
2. **HTTP Request Defaults** — server `localhost`, port `9966`, path
   `/petclinic`.
3. **HTTP Request** — `GET /api/owners`.
4. **Assertions** — Response Assertion: code contains `200`; Duration
   Assertion: 2000 ms.

## Run it

```bash
# fresh app first! (restart before every measurement)
jmeter -n -t my-baseline.jmx -l results.jtl -e -o dashboard/
```

`results.jtl` and `dashboard/` are gitignored — artifacts, not source.

## Questions to answer

1. Throughput (req/s), average, and p95 response time at 10 threads?
2. Repeat at 50 threads — which metric degrades first?
3. Error rate: still 0.00%? If not, what fails and why?
4. Compare a read (`GET /api/owners`) against a write (`POST /api/owners`)
   at the same load.

## Definition of done

- [ ] `.jmx` plan committed with meaningful element names
- [ ] Dashboard generated locally at 10 and 50 threads
- [ ] Answers to the 4 questions (PR description or a `docs/` note)
- [ ] App restarted before each measurement
