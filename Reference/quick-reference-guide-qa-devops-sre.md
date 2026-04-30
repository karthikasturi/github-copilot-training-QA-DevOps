# Quick Reference Guide for QA, DevOps, and SRE

A compact guide for participants in the GitHub Copilot for QA, DevOps, and SRE training. It focuses on the minimum terminology and relationships needed to understand the labs, complete the hands-on exercises, and finish the capstone project successfully.[web:20][web:36]

## How the pieces connect

This training is easier when participants see the flow rather than isolated terms. In practice, business requirements lead to scenarios, scenarios lead to test cases, test cases lead to automated scripts, scripts run in pipelines, pipelines produce artifacts and reports, and monitoring plus alerts help teams detect issues after deployment or during validation.[web:43][web:44][web:54][web:55]

### End-to-end flow

```text
Business requirement / user story
  -> Test scenario
  -> Test case
  -> Test data + expected result
  -> Automation script
  -> CI/CD pipeline job
  -> Test report / build artifact
  -> Deployment / environment validation
  -> Monitoring metrics / logs
  -> Alert
  -> Incident / response
  -> Fix / change
  -> Commit / pull request
  -> Pipeline rerun
```

This is the core relationship model behind the full training and the capstone project.[web:43][web:44][web:54][web:55]

## QA relationships

QA terms make more sense when understood as a chain of refinement.

### Requirement -> Scenario -> Test Case -> Script

- A **requirement** or user story defines what the application should do.
- A **test scenario** is a high-level flow that identifies what should be tested from that requirement.[web:43][web:45]
- A **test case** is derived from the scenario and adds steps, input data, and expected results.[web:43][web:44]
- An **automation script** implements the test case in code so it can run repeatedly.[web:20]

### Example

```text
Requirement: User must be able to log in.
Scenario: Verify successful login with valid credentials.
Test Case: Open login page, enter valid username and password, click Sign in, verify dashboard appears.
Automation Script: Playwright test that performs those steps and asserts the dashboard is visible.
```

### QA glossary

| Term | Meaning | Relationship |
|---|---|---|
| Requirement / User Story | Business need or feature expectation | Starting point for testing |
| Test Scenario | High-level workflow to validate | Derived from requirement [web:43][web:45] |
| Test Case | Detailed steps, data, expected result | Derived from scenario [web:43][web:44] |
| Test Data | Input values used in test execution | Attached to test case |
| Expected Result | What should happen if behavior is correct | Compared during validation |
| Assertion | Code-level check for expected behavior | Implements expected result in automation [web:20] |
| Smoke Test | Small set of critical checks | Often runs first in CI |
| Regression Test | Re-checks existing features after change | Usually part of ongoing automation |
| Flaky Test | Test that fails inconsistently | Reduced by stable locators and auto-waiting [web:20] |

## Playwright relationships

Playwright is the bridge between QA intent and executable automation. A test script is built from page actions plus assertions, and locators are the mechanism used to find elements before actions are performed.[web:20][web:13]

### Locator -> Action -> Assertion

- A **locator** identifies the UI element to work with.[web:13]
- An **action** performs something on that element, such as click or fill.[web:10]
- An **assertion** verifies that the action produced the expected result.[web:20]

### Example

```text
getByLabel('User Name') -> fill('john')
getByRole('button', { name: 'Sign in' }) -> click()
expect(getByText('Welcome, John!')) -> toBeVisible()
```

### Playwright glossary

| Term / Function | Meaning | Relationship |
|---|---|---|
| `page` | Active browser tab/page | Base object for test execution [web:20] |
| Locator | Element finder | Used before action [web:13] |
| `getByRole()` | Role-based locator | Preferred for buttons, links, menus [web:13] |
| `getByLabel()` | Label-based locator | Preferred for form fields [web:13] |
| `click()` | Action on element | Trigger behavior [web:10] |
| `fill()` | Enter text | Used with inputs [web:10] |
| `expect()` | Assertion entry point | Verifies result [web:20] |
| `toBeVisible()` | Visibility assertion | Confirms expected UI state [web:20] |
| Auto-waiting | Built-in readiness checks | Supports reliable actions and assertions [web:20] |

## DevOps relationships

DevOps terms become clearer when viewed as an automation chain from source code to validated delivery.

### Commit -> Pipeline -> Stage -> Job -> Artifact -> Deployment

- A **commit** stores a change in version control.
- A **pipeline** starts automatically or manually to validate and deliver the change.
- A pipeline contains **stages**, and each stage contains one or more **jobs**.
- Jobs can produce **artifacts**, such as reports, packages, or build output, that are passed forward or archived.[web:54][web:57]
- Successful validated output can then be used for **deployment** or further environment checks.[web:51][web:57]

### Example

```text
Code commit
  -> CI pipeline starts
  -> test stage runs Playwright tests
  -> job generates HTML report
  -> artifact is stored
  -> deploy stage uses approved build output
```

### DevOps glossary

| Term | Meaning | Relationship |
|---|---|---|
| Commit | Saved code change in Git | Can trigger a pipeline |
| Branch | Isolated line of development | Used before merge |
| Pull Request / Merge Request | Review before integration | Happens before or around merge |
| CI | Continuous Integration | Automates build and validation |
| CD | Continuous Delivery / Deployment | Moves validated changes forward |
| Pipeline | End-to-end automation workflow | Contains stages and jobs |
| Stage | Logical grouping in pipeline | Contains jobs |
| Job | Executable unit in a stage | Runs scripts/tests |
| Runner / Agent | System that executes jobs | Required for pipeline execution |
| Artifact | Output from a job | Passed to later steps or archived [web:54][web:57] |
| YAML | Configuration format for pipelines/IaC | Defines automation logic |
| Shell Script | Command-based automation | Often used inside jobs |

## SRE relationships

SRE terminology is easiest to understand as a reliability loop.

### Metrics -> SLI -> SLO -> Alert -> Incident -> Response

- **Metrics** are raw measurements such as latency, request count, or error count.[web:34]
- An **SLI** is a chosen reliability indicator derived from those measurements, such as successful request rate.[web:38][web:55]
- An **SLO** is the target value for that indicator, such as 99.9% success over a period.[web:38][web:55]
- If the service behavior threatens the objective, an **alert** may fire.[web:52]
- If the alert represents meaningful impact, it becomes an **incident** that requires response.[web:52][web:55]

### Example

```text
Metric: API request latency
SLI: Percentage of requests under 500 ms
SLO: 99% of requests should stay under 500 ms this month
Alert: Trigger if bad performance risks the SLO
Incident: Team investigates service slowdown
```

### SRE glossary

| Term | Meaning | Relationship |
|---|---|---|
| Monitoring | Collecting and watching system signals | Source of operational awareness |
| Metrics | Numeric signals about system behavior | Input to SLIs [web:34] |
| Logs | Event records with timestamps | Used for debugging and investigation |
| Traces | Request path across services | Helps diagnose distributed issues |
| SLI | Measured reliability indicator | Derived from metrics [web:38][web:55] |
| SLO | Target for the SLI | Drives reliability expectations [web:38][web:55] |
| Alert | Notification of meaningful issue | Often tied to SLO risk [web:52] |
| Incident | Service problem needing response | Triggered by alerts or user impact |
| Availability | How often service is usable | Common reliability goal [web:34] |
| Latency | Response time | Common metric [web:34] |
| Error Rate | Failed request proportion | Common metric [web:34] |
| Postmortem | Review after an incident | Leads to improvement actions |

## Cross-functional mapping

The capstone project works best when participants understand how their responsibilities connect rather than seeing QA, DevOps, and SRE as separate silos.[web:34][web:36]

| Role view | Creates / uses | Hands off to |
|---|---|---|
| QA | Scenarios, test cases, automation scripts, assertions | DevOps pipeline and report consumers |
| DevOps | Pipeline, job orchestration, artifacts, environment setup | QA execution flow and SRE visibility |
| SRE | Monitoring checks, alerts, reliability validation | Incident response and feedback to engineering |

### One capstone flow

```text
Scenario defined
  -> Test case written
  -> Copilot generates Playwright script
  -> Script runs in CI pipeline
  -> Pipeline stores report artifact
  -> Deployment or validation environment updated
  -> Monitoring/log check runs
  -> Alert or success status reported
```

## Minimum understanding needed by day

### Days 1-2

- Prompt, code suggestion, context, function, script, test case.

### Day 3

- Scenario, test case, locator, action, assertion, auto-waiting.[web:20][web:13]

### Day 4

- Endpoint, request, response, status code, payload, validation.

### Day 5

- Pipeline, stage, job, artifact, shell script, YAML.[web:54]

### Day 6

- IaC, Terraform, resource, variable, module, YAML.

### Day 7

- Monitoring, logs, metrics, alert, latency, error rate, threshold.[web:34]

### Days 8-10

- Debugging, refactoring, secure coding, workflow standardization, capstone integration.

## Prompting guide by relationship

Use prompts that preserve the relationship between concepts instead of asking for isolated code.

### Better prompt examples

- "Create test cases from this login scenario with positive and negative paths."
- "Convert this test case into a beginner-friendly Playwright test using role-based locators."
- "Generate a GitHub Actions workflow that runs the Playwright tests and stores the report as an artifact."
- "Create a simple shell script to scan logs for errors and print a warning if failures exceed a threshold."
- "Generate a basic monitoring check for API latency and explain what metric and threshold it uses."

These prompts work better because they tell Copilot what the input is, what the output should become, and what constraints should be followed.[web:20][web:36]

## Capstone checklist

Participants should be able to connect these steps by the end of the training:

1. Start from a requirement or business workflow.
2. Define a test scenario.
3. Expand it into one or more test cases.[web:43][web:44]
4. Turn a test case into automation with Copilot and Playwright.[web:20]
5. Run the automation from a CI/CD pipeline.[web:54]
6. Save a report or output as an artifact.[web:54][web:57]
7. Add a basic monitoring or logging validation step.[web:34]
8. Interpret failures as either test defects, pipeline issues, or operational signals.

## Reference links

- [Playwright Writing Tests](https://playwright.dev/docs/writing-tests) [web:20]
- [Playwright Locators](https://playwright.dev/docs/locators) [web:13]
- [GitLab Job Artifacts](https://docs.gitlab.com/ci/jobs/job_artifacts/) [web:54]
- [Google SRE Workbook: Alerting on SLOs](https://sre.google/workbook/alerting-on-slos/) [web:52]

