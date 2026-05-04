# Day 04 Demo Guide - Copilot for API Testing

This guide contains instructor-ready demo prompts rewritten in a structured format using **role**, **goal**, **explicit scope**, **constraints**, and **validation / verification style**.

---

## Demo 1 - Auth Endpoint Tests

**Target file:** `tests/api/test_auth.py`

### Prompt

```text
Role:
You are a senior Python QA automation engineer working in an async FastAPI codebase.

Goal:
Generate API tests for the Conduit login endpoint so learners can see how Copilot converts an API scenario into async pytest tests.

Explicit scope:
- Create tests only for POST /api/users/login
- Write the code in tests/api/test_auth.py
- Use the existing asyncclient fixture
- Cover exactly these scenarios:
  1. valid credentials return 200 with a token
  2. wrong password returns 422
  3. non-existent user returns 422
  4. missing email field returns 422

Constraints:
- Use pytest async style with async def test functions
- Use await asyncclient.post(...) for requests
- Keep each scenario in a separate test function
- Do not add helper abstractions unless truly necessary
- Do not assume field names without checking common FastAPI JSON response patterns
- Keep the code readable for beginners

Validation / verification style:
- In every test, assert the status code first
- For the success case, verify the response contains the expected user/token structure
- For failure cases, verify the response body contains a meaningful validation or error structure
- Prefer clear, direct assertions over clever compact code
```

---

## Demo 2 - Authenticated Article Creation

**Target file:** `tests/api/test_articles.py`

### Prompt

```text
Role:
You are a senior API test engineer creating maintainable async pytest tests for a FastAPI application.

Goal:
Generate article creation tests that demonstrate authenticated and unauthenticated API behavior in the Conduit project.

Explicit scope:
- Write code in tests/api/test_articles.py
- Use the existing asyncclient and authheaders fixtures
- Generate only these two tests:
  1. authenticated user can create an article and gets 201 with a slug
  2. unauthenticated create article request returns 401

Constraints:
- Use async def pytest tests
- Reuse the authheaders fixture instead of hardcoding a token
- Do not use Bearer token format anywhere
- Follow the Conduit API convention that authenticated calls use Authorization: Token <jwt>
- Keep payload data simple and inline unless reuse is necessary
- Keep the code beginner-friendly and aligned to typical pytest style

Validation / verification style:
- Assert status code first in each test
- For the success case, verify slug, title, and body fields from the response
- For the unauthenticated case, verify the expected error response shape if available
- Avoid weak tests that validate only the HTTP code
```

---

## Demo 3 - Refine a Generated Test

**Target file:** existing generated test in `tests/api/test_articles.py`

### Prompt

```text
Role:
You are a code reviewer specializing in API test quality for Python pytest projects.

Goal:
Review and improve this generated API test without changing the original scenario being tested.

Explicit scope:
- Focus only on the test currently selected in the editor
- Do not rewrite unrelated tests
- Improve correctness, readability, and assertion quality

Constraints:
- Do not change the intended scenario
- Do not introduce advanced abstractions or fixtures unless necessary
- Preserve async pytest style
- Keep naming simple and descriptive
- Ensure the auth header format matches the Conduit API rules

Validation / verification style:
- Check that status code is the first assertion
- Check that Authorization uses Token and not Bearer
- Strengthen weak response body assertions
- Remove unnecessary duplication if it hurts readability
- Produce a version that is easier for trainees to understand and run
```
