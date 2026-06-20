# Testing Strategy

This document explains the repository's quality gates from a DevOps view.

## Test Layers

### Unit Tests

Unit tests validate small pieces of logic quickly.
They are the cheapest and fastest signal that the code still behaves correctly.

### Functional Tests

Functional tests validate services together at a higher level than unit tests.
They help catch integration mistakes before deployment.

### End-to-End Tests

End-to-end tests use Playwright to validate the user flow in a browser.
They are the best signal that the app works as an experience, not just as code.

## Workflow Coverage

- `pr-validation.yml` covers the primary solution validation path
- `pr-validation-maui.yml` covers the client-side/MAUI validation path
- `playwright.yml` covers browser-based user flows

## Why This Matters For DevOps

Tests are not just a developer concern.
They are also a deployment gate because the release process should only promote
artifacts that have passed the right checks.

## Good Practices

- Run the smallest useful test set first
- Keep test names clear and behavior-focused
- Make failed tests easy to map back to a release or workflow
- Treat flaky tests as a release risk

## What Not To Document Here

- Real credentials
- Internal endpoints that are meant to stay private
- Environment-specific secrets

## Review Questions

When test-related files change, ask:

- Does this affect the release gate?
- Does it change which tests run in PRs?
- Does it change what gets promoted?
- Does it make CI slower or less reliable?
