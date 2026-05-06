# Day 07 Demo Guide

This guide is for the live Day 07 session on Monitoring, Logging, and SRE workflows using GitHub Copilot in the Conduit FastAPI RealWorld project. The Day 07 training plan centers on Prometheus instrumentation, alerting rules, a log parser, and optional synthetic monitoring or Grafana assets.[file:92]

## Objective

- Show how Copilot can generate practical SRE assets from monitoring requirements.[file:92]
- Demonstrate basic Prometheus instrumentation in the application.[file:92]
- Generate alert rules and validate them with `promtool`.[file:92]
- Generate a Python log parser that works on structured Conduit logs.[file:92]
- Reinforce that monitoring code and configs must be validated, not blindly accepted.

## Demo scope

Use these live demo items:

1. Add Prometheus instrumentation and expose `/metrics`.[file:92]
2. Add a custom metric `conduitarticlescreatedtotal` for successful article creation.[file:92]
3. Generate `monitoring/alert-rules.yml` with four alerts and validate using `promtool check rules`.[file:92]
4. Generate `scripts/logparser.py` to analyze structured JSON access logs.[file:92]
5. Optional: generate SLO PromQL queries or a synthetic monitor script.[file:92]

## Setup

- `prometheus-fastapi-instrumentator` installed.[file:92]
- Prometheus available through Docker Compose or local setup.[file:92]
- `monitoring` and `scripts` folders exist.[file:92]
- Sample log file available, for example `samplelogs/conduit.log`.[file:92]
- Work in your feature branch only.[file:92]

## Recommended live sequence

1. Instrument the app and expose metrics.
2. Verify `/metrics` output in browser or curl.
3. Generate alert rules.
4. Validate rules with `promtool`.
5. Generate log parser and run it against sample log data.
6. Optional: show SLO query file or synthetic monitor.

## Demo 1: Prometheus instrumentation

**Target file:** `app/main.py`.[file:92]

### What to explain

The Day 07 core task is to add `prometheus-fastapi-instrumentator`, expose `/metrics`, and add a custom counter named `conduitarticlescreatedtotal` that increments on each successful `POST /api/articles`.[file:92]

### Copilot prompt

```text
Role:
You are a Python observability engineer instrumenting a FastAPI application for Prometheus monitoring.

Goal:
Add Prometheus metrics exposure and a custom application metric to the Conduit FastAPI app.

Explicit scope:
- Modify app/main.py
- Add prometheus-fastapi-instrumentator integration
- Expose metrics at /metrics
- Add a custom counter named conduitarticlescreatedtotal
- Increment the custom counter on each successful POST /api/articles request

Constraints:
- Keep the code minimal and readable
- Do not redesign unrelated application code
- Preserve existing FastAPI setup
- Add only the instrumentation needed for the training task

Validation / verification style:
- Ensure /metrics is exposed successfully
- Ensure the custom metric name is conduit-prefixed
- Ensure the custom counter increments only for successful article creation
- Keep the code suitable for manual verification with curl or browser access
```

### What to review live

- where instrumentation is initialized
- how `/metrics` is exposed
- how the custom counter is incremented
- how to verify the metric appears in output

### Validation

- `curl http://localhost:8000/metrics` [file:92]

## Demo 2: Alerting rules

**Target file:** `monitoring/alert-rules.yml`.[file:92]

### What to explain

The Day 07 core task requires four alerts: high error rate, high latency, DB connections exhausted, and pod restarting. Each alert must include severity label and runbook URL annotation, and the file must pass `promtool check rules`.[file:92]

### Copilot prompt

```text
Role:
You are an SRE engineer creating beginner-friendly Prometheus alert rules for an application.

Goal:
Generate Prometheus alerting rules for the Conduit application.

Explicit scope:
- Create monitoring/alert-rules.yml
- Add these alerts only:
  1. ConduitHighErrorRate for 5xx rate over 5 minutes
  2. ConduitHighLatency for p99 latency above 500ms over 10 minutes
  3. ConduitDBConnectionsExhausted for DB pool above 80 percent
  4. ConduitPodRestarting for more than 3 restarts in 15 minutes
- Add a severity label to each alert
- Add a runbook_url annotation to each alert

Constraints:
- Keep the YAML simple and valid for Prometheus rule syntax
- Use clear alert names and readable expressions
- Do not include unrelated alerts
- Keep it training-friendly rather than production-complete

Validation / verification style:
- Ensure the file structure is valid Prometheus alert rule YAML
- Ensure each alert has expr, for, labels, and annotations
- Ensure promtool check rules can validate the file
- Ensure alert names and labels are consistent and readable
```

### What to review live

- groups and rules structure
- `alert`, `expr`, `for`, `labels`, `annotations`
- why simple alerts are better for training
- how runbook URLs help operational response

### Validation

- `promtool check rules monitoring/alert-rules.yml` [file:92]

## Demo 3: Log parser script

**Target file:** `scripts/logparser.py`.[file:92]

### What to explain

The core task asks for a Python script that parses structured JSON access logs and reports top slow endpoints, most frequent error paths, requests per minute for the last hour, and repeated trace IDs. It must accept a log file path as input and survive malformed lines without crashing.[file:92]

### Copilot prompt

```text
Role:
You are a Python SRE automation engineer building a log analysis utility.

Goal:
Generate a Python script that parses Conduit structured JSON access logs and prints a useful summary.

Explicit scope:
- Create scripts/logparser.py
- Accept log file path as a CLI argument
- Report:
  - top 10 slowest endpoints by average response time
  - top 5 most frequent error paths with 4xx and 5xx responses
  - requests per minute for the last hour
  - trace IDs appearing 20 times as possible loop indicators
- Output a readable summary table to stdout

Constraints:
- Keep the script simple and readable
- Handle malformed or non-JSON lines without crashing
- Do not depend on heavy external libraries unless necessary
- Keep the output practical for trainees to understand

Validation / verification style:
- Ensure the script accepts a file path argument
- Ensure malformed lines are skipped safely
- Ensure the required summaries are printed
- Ensure the script runs successfully against a sample log file
```

### What to review live

- input handling
- safe JSON parsing
- grouping and aggregation logic
- handling malformed lines
- why resilient parsing matters in SRE workflows

### Validation

- `python scripts/logparser.py samplelogs/conduit.log` [file:92]

## Optional demo 4: SLO PromQL queries

**Target file:** `monitoring/slo-queries.md`.[file:92]

### What to explain

The optional task asks for PromQL queries for 99.5 percent availability over 30 days, p99 write endpoint latency under 500ms, and an error budget burn rate alert for 5 percent monthly budget in 1 hour.[file:92]

### Copilot prompt

```text
Role:
You are an SRE engineer creating PromQL queries for service-level monitoring.

Goal:
Generate starter PromQL queries for SLO-style monitoring of the Conduit application.

Explicit scope:
- Create monitoring/slo-queries.md
- Add queries for:
  - 99.5 percent availability over 30 days
  - p99 write endpoint latency under 500ms
  - error budget burn rate for 5 percent monthly budget in 1 hour

Constraints:
- Keep the queries readable and explained briefly
- Do not include unrelated SLO concepts
- Keep the file suitable as training notes

Validation / verification style:
- Ensure the queries are syntactically PromQL-like and training-appropriate
- Ensure each query maps clearly to the requested reliability objective
```

## Expected outputs

- `app/main.py` updated with Prometheus instrumentation.[file:92]
- `monitoring/alert-rules.yml` created.[file:92]
- `scripts/logparser.py` created.[file:92]
- Optional: `monitoring/slo-queries.md`.[file:92]

## Demo success criteria

- `/metrics` returns Conduit-prefixed metric output.[file:92]
- `promtool check rules monitoring/alert-rules.yml` passes.[file:92]
- `python scripts/logparser.py samplelogs/conduit.log` runs without error.[file:92]
