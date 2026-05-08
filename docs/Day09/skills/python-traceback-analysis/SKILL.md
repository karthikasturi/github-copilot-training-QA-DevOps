---
name: python-traceback-analysis
description: "Analyze Python tracebacks to debug errors, explain their causes, and suggest likely fixes for Python scripts, services, or tests."
user-invocable: true
context: fork
---

# Python Traceback Analysis

This skill helps you debug Python tracebacks by explaining the root cause of errors and suggesting practical fixes. Use it to quickly understand and resolve Python exceptions in scripts, services, or tests.

## When to Use This Skill
- You have a Python traceback from a test, script, or running service.
- You want a clear explanation of what went wrong and where in your code.
- You need suggestions for how to fix or prevent the error.

## Workflow
1. Read the full Python traceback and identify the deepest (most recent) call where the error occurred.
2. Extract the exception type and error message.
3. Inspect the code snippet around the failing line (if provided) and explain the likely cause in plain language.
4. Suggest one or more possible fixes, including code or configuration changes.
5. If information is missing (e.g., no code shown), ask the user to provide the relevant file, function, or code snippet.
6. Provide a short list of follow-up checks (e.g., input types, external dependencies, environment variables) that may help prevent similar issues.

## Expected Outputs
- A short summary of the error in plain language.
- A breakdown of the stack trace, highlighting where the error actually originates.
- One or more suggested fixes, with code snippets or configuration changes where appropriate.
- Optional: recommendations to add tests or logging to help prevent recurrence.

## Notes and Limitations
- The analysis is based only on the traceback and any code provided.
- For production incidents, always confirm fixes in a safe environment before deploying.
- If the traceback involves external services or dependencies, more context may be needed for a complete diagnosis.
- Use concise, clear language suitable for context-limited environments.
- Companion scripts or example files are not included yet, but may be added in the future.
