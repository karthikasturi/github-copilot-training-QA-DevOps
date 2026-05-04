# Day 05 Lab Guide – Copilot for DevOps Scripting and CI/CD

This lab focuses on generating DevOps automation with Copilot for the Conduit project. The Day 5 capstone-aligned tasks are to build a GitHub Actions CI workflow with lint, unit tests, API tests, and Docker build; add a deployment health-check script; and optionally create an E2E workflow and Dependabot config.[file:92]

---

## Lab Objective

- Generate automation scripts with Copilot for DevOps tasks.[file:92]
- Build a GitHub Actions workflow for lint, unit tests, API tests, and Docker build.[file:92]
- Create a Python health-check script suitable for CI use.[file:92]
- Prepare the repo for the Day 10 capstone integration path.[file:92]

---

## Setup Checklist

- Days 1–4 complete and tests passing locally.[file:92]
- Docker installed.[file:92]
- GitHub repository exists with Actions enabled.[file:92]
- Work only from your personal feature branch, not main.[file:92]

---

## Lab Task 1 – Generate the CI Workflow

Target file: `.github/workflows/ci.yml`.[file:92]

### Goal

Create a GitHub Actions workflow that runs on push and pull request, performs lint checks, executes unit tests, and runs API tests with a PostgreSQL service container.[file:92]

### Prompt

Role:
You are a DevOps engineer creating a beginner-friendly GitHub Actions CI workflow for a FastAPI project.

Goal:
Generate a CI workflow for the Conduit project that validates code quality and automated tests on every push and pull request.

Explicit scope:
- Create `.github/workflows/ci.yml`
- Trigger on `push` and `pull_request` for any branch
- Add these jobs only:
  1. `lint` running `black --check`, `isort --check`, and `ruff` on app and tests
  2. `unit-tests` running `pytest testsunit` with coverage
  3. `api-tests` running `pytest testsapi` with a PostgreSQL service container
- Use Ubuntu runners and Python 3.11
- Use pip cache where appropriate

Constraints:
- Keep the workflow minimal and readable
- Use GitHub Actions YAML syntax correctly
- Do not add unrelated jobs
- Do not hardcode secrets
- Keep job and step names clear for trainees

Validation / verification style:
- Ensure YAML is valid and logically ordered
- Ensure each job checks out code, sets up Python, installs dependencies, and runs its commands
- Ensure the `api-tests` job includes the required PostgreSQL service configuration
- Ensure the workflow is suitable to run in GitHub Actions without manual edits beyond project-specific secrets or env vars

### Expected output

- `.github/workflows/ci.yml` created.[file:92]

---

## Lab Task 2 – Add Docker Build Job

Target file: `.github/workflows/ci.yml`.[file:92]

### Goal

Extend the CI workflow with a Docker build job that depends on successful API tests and tags the image with the Git SHA.[file:92]

### Prompt

Role:
You are a DevOps engineer extending an existing GitHub Actions workflow.

Goal:
Add a Docker build job to the existing CI pipeline for the Conduit project.

Explicit scope:
- Modify only `.github/workflows/ci.yml`
- Add one new job named `docker-build`
- Make it depend on `api-tests`
- Build the project Dockerfile
- Tag the image with the Git SHA
- Push the image to GitHub Container Registry using the GitHub token

Constraints:
- Do not use the `latest` tag
- Use built-in GitHub Actions patterns where appropriate
- Keep the job simple and readable
- Do not hardcode credentials
- Use GitHub secrets or `GITHUB_TOKEN` patterns only

Validation / verification style:
- Ensure the new job runs only after `api-tests` succeeds
- Ensure the image tag uses the current Git SHA
- Ensure authentication is handled securely
- Ensure the YAML remains valid after modification

### Expected output

- `docker-build` job added to `.github/workflows/ci.yml`.[file:92]

---

## Lab Task 3 – Create Health Check Script

Target file: `scripts/healthcheck.py`.[file:92]

### Goal

Create a Python health-check script that can be used as a CI validation step after deployment or service startup.[file:92]

### Prompt

Role:
You are a Python automation engineer writing a CI-friendly deployment health-check script.

Goal:
Generate a Python script that validates whether the Conduit application is healthy and responsive.

Explicit scope:
- Create `scripts/healthcheck.py`
- The script must:
  - check `/health` returns HTTP 200
  - check `/api/tags` returns HTTP 200 with valid JSON
  - verify each response completes within 2 seconds
  - retry up to 5 times with 10-second backoff
  - exit with code 1 on failure
- Read base URL from the first CLI argument or `APP_URL` environment variable

Constraints:
- Use Python, not shell
- Keep the script simple and production-practical
- Include clear exit behavior for CI usage
- Avoid unnecessary abstractions
- Keep log messages readable

Validation / verification style:
- Validate HTTP status codes explicitly
- Validate JSON parsing for `/api/tags`
- Validate response time threshold
- Ensure retry behavior is implemented clearly
- Ensure the script exits 0 on success and 1 on failure

### Expected output

- `scripts/healthcheck.py` created.[file:92]

---

## Lab Task 4 – Add Report Job

Target file: `.github/workflows/ci.yml`.[file:92]

### Goal

Add a summary/report job that runs after the other jobs and fails if any upstream job has failed.[file:92]

### Prompt

Role:
You are a DevOps engineer improving CI visibility in a GitHub Actions workflow.

Goal:
Add a final report job to the existing CI workflow and generate a workflow status badge snippet for the README.

Explicit scope:
- Modify `.github/workflows/ci.yml`
- Add a final job that runs after lint, unit-tests, api-tests, and docker-build
- The job should fail if any upstream job failed
- Also generate the markdown for a workflow status badge suitable for README use

Constraints:
- Keep the report job simple
- Do not add external notification tools
- Do not modify unrelated jobs
- Keep the badge output separate from the YAML if needed

Validation / verification style:
- Ensure the report job depends on all previous jobs
- Ensure the job correctly reflects upstream failure
- Ensure the badge markdown matches the workflow name and file

### Expected output

- Report job added to the workflow.[file:92]
- README badge markdown generated.[file:92]

---

## Optional Stretch – E2E Workflow

Target file: `.github/workflows/e2e.yml`.[file:92]

### Goal

Create a separate GitHub Actions workflow for Playwright-based E2E tests on push to main.[file:92]

### Prompt

Role:
You are a DevOps engineer creating a dedicated E2E workflow for browser automation.

Goal:
Generate a GitHub Actions workflow that runs Playwright tests for the Conduit project on push to main.

Explicit scope:
- Create `.github/workflows/e2e.yml`
- Trigger only on push to `main`
- Start the full stack with docker-compose
- Wait for health check to pass
- Run `pytest testse2e`
- Upload the HTML report as an artifact on failure

Constraints:
- Keep the workflow separate from the main CI workflow
- Do not duplicate unnecessary setup beyond what E2E needs
- Keep artifact upload only for failure cases
- Keep the YAML readable for trainees

Validation / verification style:
- Ensure trigger scope is correct
- Ensure stack startup happens before tests
- Ensure health validation happens before E2E execution
- Ensure HTML report artifact upload is configured for failure conditions

### Expected output

- `.github/workflows/e2e.yml` created.[file:92]

---

## Optional Stretch – Dependabot Config

Target file: `.github/dependabot.yml`.[file:92]

### Goal

Generate a Dependabot configuration for regular pip and GitHub Actions updates.[file:92]

### Prompt

Role:
You are a DevOps engineer maintaining dependency hygiene in a GitHub repository.

Goal:
Generate a Dependabot configuration for the Conduit project.

Explicit scope:
- Create `.github/dependabot.yml`
- Configure weekly updates for pip dependencies
- Configure monthly updates for GitHub Actions versions

Constraints:
- Keep the configuration minimal
- Do not add package ecosystems not used in this project
- Use valid Dependabot YAML syntax

Validation / verification style:
- Ensure schedule intervals are correct
- Ensure the correct directories and ecosystems are defined
- Ensure YAML is valid and ready to commit

### Expected output

- `.github/dependabot.yml` created.[file:92]

---

## Lab Rules

- Keep the workflow minimal first; refine only after it works.
- Do not hardcode secrets anywhere.[file:92]
- Use `github.sha` for image tags, not `latest`.[file:92]
- Review Copilot-generated YAML carefully before committing.
- Validate one job at a time when debugging.

---

## Validation Commands

Run these after generation and refinement:

- `pytest testsunit -v`
- `pytest testsapi -v`
- `python scripts/healthcheck.py http://localhost:8000`
- Push branch and verify GitHub Actions run status.[file:92]

---

## Acceptance Criteria

- CI workflow includes lint, unit, API, and docker-build jobs.[file:92]
- No hardcoded secrets; use secrets-based patterns only.[file:92]
- Docker image is tagged with `github.sha`, never `latest`.[file:92]
- Pip cache is used in jobs where relevant.[file:92]
- Health-check script exits 0 on live app and 1 on bad URL.[file:92]

---

## Suggested Working Order

1. Generate the base CI workflow.
2. Run and fix lint, unit, and API jobs locally where possible.
3. Add the docker-build job.
4. Generate and validate the health-check script.
5. Add the final report job.
6. Attempt stretch tasks if time permits.

---

## Submission

- Commit all generated Day 5 files to your feature branch.[file:92]
- Push and confirm the Actions workflow completes successfully.[file:92]
- Save any Copilot corrections or observations in your notes folder if your trainer requests them.[file:92]
