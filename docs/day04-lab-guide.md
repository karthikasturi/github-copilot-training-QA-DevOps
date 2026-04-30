# Day 04 Lab Guide - Copilot for API Testing

This guide contains participant-facing lab prompts rewritten in a structured format using **role**, **goal**, **explicit scope**, **constraints**, and **validation / verification style**.

---

## Lab Task 1 - Auth Endpoint Tests

**Target file:** `tests/api/test_auth.py`

### Prompt

```text
Role:
You are a Python QA engineer writing async API tests for a FastAPI application.

Goal:
Create a complete login endpoint test set for the Conduit project.

Explicit scope:
- Write tests only for POST /api/users/login
- Save the code in tests/api/test_auth.py
- Use the existing asyncclient fixture
- Cover these scenarios:
  1. valid credentials return 200 with token
  2. wrong password returns 422
  3. non-existent user returns 422
  4. missing email field returns 422

Constraints:
- Use async pytest test functions
- Keep one scenario per test
- Keep the code simple and readable
- Do not create extra helpers unless repetition becomes large
- Match the project's existing test style where possible

Validation / verification style:
- Assert status code first in every test
- For success, verify important JSON fields such as token or user structure
- For failures, verify useful error details in the response body
- Use clear assertions that make the scenario easy to understand
```

---

## Lab Task 2 - Articles CRUD Tests

**Target file:** `tests/api/test_articles.py`

### Prompt

```text
Role:
You are a Python API automation engineer building async CRUD tests for a real project.

Goal:
Create article API tests for authenticated and authorization-sensitive operations in the Conduit project.

Explicit scope:
- Write tests in tests/api/test_articles.py
- Use the existing asyncclient and authheaders fixtures
- Generate tests for exactly these scenarios:
  1. create article authenticated returns 201 with slug
  2. create article unauthenticated returns 401
  3. get article by slug returns 200 with correct title
  4. update article title returns 200 and slug changes
  5. delete article as author returns 204
  6. delete article as non-author returns 403

Constraints:
- Use async pytest style
- Reuse fixtures instead of hardcoding auth tokens
- Use Authorization: Token <jwt> format only
- Never use Bearer
- Keep the test data small and realistic
- Avoid over-engineering helpers unless needed for readability

Validation / verification style:
- Assert status code first in every test
- For create and update operations, verify important article fields in the response
- For get by slug, verify the returned title matches the created article
- For delete scenarios, verify the expected response code and follow-up behavior if appropriate
- Keep assertions focused on business outcome, not only transport success
```

---

## Lab Task 3 - Pagination and Filtering Tests

**Target file:** `tests/api/test_articles_list.py`

### Prompt

```text
Role:
You are a QA automation engineer validating API listing behavior for pagination and filtering.

Goal:
Create tests for article listing behavior in the Conduit API.

Explicit scope:
- Write tests in tests/api/test_articles_list.py
- Use the existing asyncclient fixture and any available seeded or created article data
- Cover exactly these scenarios:
  1. default limit is 20
  2. limit and offset query parameters work
  3. filtering by tag returns only tagged articles
  4. filtering by author returns only that author's articles

Constraints:
- Use async pytest tests
- Keep tests independent where practical
- Do not assume data exists unless you create it or the project fixture provides it
- Keep the code readable for participants who are new to API testing

Validation / verification style:
- Assert status code first
- Verify returned list size, returned article attributes, or both depending on scenario
- For filtered results, verify the response does not include mismatched records
- Prefer direct checks that clearly show why the test passes or fails
```

---

## Lab Task 4 - Boundary Tests

**Target file:** `tests/api/test_articles.py`

### Prompt

```text
Role:
You are a QA engineer focused on edge cases and boundary validation for API inputs.

Goal:
Add boundary tests for article creation payload validation in the Conduit API.

Explicit scope:
- Add tests to tests/api/test_articles.py
- Cover exactly these cases:
  1. article body at 0 characters
  2. article title at 256 characters
- Reuse asyncclient and authheaders if authentication is required

Constraints:
- Use async pytest style
- Keep the tests small and explicit
- Do not guess expected behavior blindly; write assertions based on observed API behavior
- Avoid combining both boundary checks into one test function

Validation / verification style:
- Assert status code first
- If the API accepts the payload, verify returned article data is correct
- If the API rejects the payload, verify the error structure and field-level validation message if present
- Make the expected boundary behavior obvious from the test name and assertions
```

---

## Optional Stretch - Postman Collection

**Target file:** `tests/api/conduit.postman_collection.json`

### Prompt

```text
Role:
You are an API test engineer preparing a Postman collection from an existing automated test suite.

Goal:
Generate a simple Postman collection for the Conduit API based on the API tests created in this project.

Explicit scope:
- Generate a collection JSON file named tests/api/conduit.postman_collection.json
- Include login and article endpoints
- Add a pre-request script that reads the auth token from a Postman environment variable
- Apply the token to authenticated requests

Constraints:
- Keep the collection simple and training-friendly
- Do not include unrelated endpoints
- Keep request names clear and aligned with the pytest scenarios
- Do not hardcode secrets or tokens

Validation / verification style:
- Ensure the generated collection JSON is structurally valid
- Ensure authenticated requests refer to the environment token variable
- Ensure request names and descriptions are easy to map back to the API tests
```

---

## Acceptance Checklist

- Minimum 20 API test functions across API test files
- Status code is the first assertion in every test
- Authorization uses `Token` prefix throughout
- `pytest testsapi -v` exits with zero failures
