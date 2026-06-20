# DevOps Overview

This document explains the repository from an operations and delivery perspective.
It intentionally avoids sensitive values, environment credentials, and secret material.

## Repository Role

This repo is the application-side control plane for the eShop project.
It is where developers and operators validate code, build images, run tests,
and prepare releases for GitOps promotion.

## Major Areas

### Application Code

The `src/` directory contains the application services and UI projects.
From a DevOps perspective, these are the workloads that are built into images
and later deployed through GitOps.

### Testing

The `tests/` and `e2e/` directories represent the quality gates.
They prove that the application can be built, exercised, and checked before release.

### Release Automation

The `.github/workflows/` directory contains the automated pipelines that:

- Validate pull requests
- Run application and UI tests
- Build container images
- Promote Dev and Prod through GitOps changes

### Supporting Scripts

The `build/` directory contains helper scripts for container and manifest workflows.
These are used when a release process needs repeatable command-line steps.

### Documentation

The `docs/` directory contains operational guidance for:

- Delivery flow
- Environment behavior
- Testing strategy
- Monitoring and security notes
- Disaster recovery

## Delivery Model

The normal delivery path is:

1. A code or configuration change lands in this repo.
2. The appropriate validation workflow runs.
3. Container images are built and checked.
4. Dev changes are promoted into GitOps.
5. Prod changes follow a more controlled promotion path.

## What Not To Put Here

- API keys
- Personal access tokens
- Connection strings
- Environment credentials
- Internal-only backend URLs that are meant to stay private

## How To Read Changes

When reviewing a change, ask:

- Does it affect build, test, or release behavior?
- Does it change which image gets deployed?
- Does it change a workflow trigger or approval path?
- Does it alter how Dev or Prod are promoted?

If the answer is yes, treat it as an operational change, not just an app change.
