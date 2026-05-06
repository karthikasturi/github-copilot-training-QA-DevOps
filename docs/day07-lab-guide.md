# Day 07 Lab Guide

This lab guide is for participants implementing monitoring and logging assets for the Conduit application using GitHub Copilot. The Day 07 plan is to instrument the app, create alert rules, and build a log parser, with optional SLO queries, synthetic monitoring, or Grafana dashboard generation.[file:92]

## Objective

- Add basic observability to the Conduit application.[file:92]
- Generate alerting rules and validate them.[file:92]
- Create a resilient log parsing utility.[file:92]
- Build practical monitoring artifacts that support the capstone path.[file:92]

## Setup checklist

- `prometheus-fastapi-instrumentator` installed.[file:92]
- Prometheus running through Docker Compose or equivalent.[file:92]
- `monitoring` and `scripts` folders exist.[file:92]
- Sample log file available for testing.[file:92]
- Work in your feature branch.[file:92]

## Lab task 1: Add Prometheus instrumentation

**Target file:** `app/main.py`.[file:92]

### Goal

Expose Prometheus metrics and add a custom application counter.[file:92]

### Copilot prompt

```text
Role:
You are a Python observability engineer instrumenting a FastAPI application.

Goal:
Add Prometheus instrumentation to the Conduit app and expose useful metrics.

Explicit scope:
- Modify app/main.py
- Add prometheus-fastapi-instrumentator
- Expose /metrics
- Add a custom counter named conduitarticlescreatedtotal
- Increment the counter on successful POST /api/articles requests

Constraints:
- Keep the code small and readable
- Avoid unrelated refactoring
- Preserve existing application structure
- Add only the instrumentation needed for this task

Validation / verification style:
- Ensure /metrics is reachable
- Ensure the custom metric appears in the output
- Ensure the metric name is conduit-prefixed
- Ensure the counter is linked to successful article creation
```

### Validation

- `curl http://localhost:8000/metrics` [file:92]

## Lab task 2: Alerting rules

**Target file:** `monitoring/alert-rules.yml`.[file:92]

### Goal

Create Prometheus alert rules for important Conduit reliability signals.[file:92]

### Copilot prompt

```text
Role:
You are an SRE engineer writing Prometheus alert rules.

Goal:
Generate alert rules for Conduit reliability monitoring.

Explicit scope:
- Create monitoring/alert-rules.yml
- Add these alerts:
  1. ConduitHighErrorRate
  2. ConduitHighLatency
  3. ConduitDBConnectionsExhausted
  4. ConduitPodRestarting
- Add severity labels
- Add runbook_url annotations

Constraints:
- Keep the YAML valid and simple
- Keep expressions readable
- Do not add unrelated alerts
- Make the rules easy for trainees to understand

Validation / verification style:
- Ensure the file passes promtool check rules
- Ensure each rule includes alert name, expression, duration, labels, and annotations
- Ensure names match the requested alert names
```

### Validation

- `promtool check rules monitoring/alert-rules.yml` [file:92]

## Lab task 3: Log parser

**Target file:** `scripts/logparser.py`.[file:92]

### Goal

Create a Python utility that parses Conduit structured logs and produces useful summaries.[file:92]

### Copilot prompt

```text
Role:
You are a Python SRE automation engineer writing a log parsing script.

Goal:
Generate a script that analyzes Conduit structured JSON access logs.

Explicit scope:
- Create scripts/logparser.py
- Accept a log file path as CLI input
- Report:
  - top 10 slowest endpoints by average response time
  - top 5 most frequent error paths with 4xx and 5xx responses
  - requests per minute for the last hour
  - trace IDs appearing 20 times as possible loops
- Print results in a readable summary table

Constraints:
- Handle malformed or non-JSON lines without crashing
- Keep the script lightweight and readable
- Do not assume every log line has every field
- Keep the output understandable for trainees

Validation / verification style:
- Ensure the script accepts a file path argument
- Ensure malformed lines are skipped safely
- Ensure all four summaries are produced
- Ensure the script runs successfully against a sample log file
```

### Validation

- `python scripts/logparser.py samplelogs/conduit.log` [file:92]

## Optional stretch 1: SLO queries

**Target file:** `monitoring/slo-queries.md`.[file:92]

### Copilot prompt

```text
Role:
You are an SRE engineer preparing starter PromQL queries for service-level monitoring.

Goal:
Generate PromQL queries for basic SLO tracking of the Conduit app.

Explicit scope:
- Create monitoring/slo-queries.md
- Add queries for:
  - 99.5 percent availability over 30 days
  - p99 write endpoint latency under 500ms
  - error budget burn rate for 5 percent monthly budget in 1 hour

Constraints:
- Keep the file concise
- Keep the queries readable and briefly explained
- Do not add unrelated formulas

Validation / verification style:
- Ensure each query maps clearly to the requested monitoring objective
- Ensure the output is suitable as training reference material
```

## Optional stretch 2: Synthetic monitor

**Target file:** `scripts/syntheticmonitor.py`.[file:92]

### Copilot prompt

```text
Role:
You are an SRE automation engineer building a lightweight synthetic monitor.

Goal:
Generate a Python script that runs a synthetic transaction against the Conduit application.

Explicit scope:
- Create scripts/syntheticmonitor.py
- Every 60 seconds:
  - register a user
  - create an article
  - fetch the article
  - delete the article
- Record per-step latency to metrics.csv
- Exit cleanly on SIGINT

Constraints:
- Keep the script training-friendly
- Keep dependencies minimal
- Handle interruptions cleanly
- Keep the output and CSV writing simple

Validation / verification style:
- Ensure the script loops at the required interval
- Ensure metrics.csv is written
- Ensure SIGINT stops the script cleanly
- Ensure each synthetic step records latency
```

## Optional stretch 3: Grafana dashboard

**Target file:** `monitoring/grafana-dashboard.json`.[file:92]

### Copilot prompt

```text
Role:
You are an observability engineer creating a starter Grafana dashboard.

Goal:
Generate a Grafana dashboard JSON for the Conduit application.

Explicit scope:
- Create monitoring/grafana-dashboard.json
- Add three panels:
  - request rate
  - p99 latency
  - error rate
- Assume Prometheus data source

Constraints:
- Keep the dashboard simple
- Use clear panel titles
- Do not add excessive styling or unused panels

Validation / verification style:
- Ensure the JSON structure is valid enough for Grafana import
- Ensure each panel maps to one of the required signals
```

## Working order

1. Instrument the app and verify /metrics.
2. Generate and validate alert rules.
3. Generate and run the log parser.
4. Attempt stretch tasks if time permits.

## Acceptance criteria

- `GET /metrics` returns a Conduit-prefixed custom metric.[file:92]
- `promtool check rules monitoring/alert-rules.yml` passes with 0 errors.[file:92]
- `python scripts/logparser.py samplelogs/conduit.log` runs without error.[file:92]

## Submission

- Commit Day 07 files to your feature branch.[file:92]
- Save any fixes made after validation.
- Push when your monitoring files and scripts validate successfully.
