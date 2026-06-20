# CI/CD Flow

This document explains the GitHub Actions workflows in this repository from a DevOps perspective.

## Workflow Map

### Pull Request Validation

- `pr-validation.yml` validates the web solution on Linux
- `pr-validation-maui.yml` validates the MAUI/client-side project on Windows
- `playwright.yml` runs end-to-end browser tests

### Build and Scan

- `build-container-images.yml` builds service images
- Trivy scanning is part of the container build workflow

### Deployment

- `deploy-dev.yml` promotes the web app to Dev through GitOps
- `deploy-prod.yml` promotes the web app to Prod through GitOps

### Maintenance

- `markdownlint.yml` checks markdown quality

## Pull Request Validation

The PR workflows are the first gate for change quality.
They verify that the repository still builds and that the relevant tests pass
before a merge is approved.

Typical behavior:

- The solution is built
- Application tests are run
- Platform-specific tests are run on the appropriate runner
- Browser tests are run when the Playwright workflow is included

## Image Build Flow

The image build workflow is used to create container images for application services.
From a DevOps view, this is important because the image tag is what GitOps later deploys.

The workflow:

- Builds multiple service images
- Runs Trivy on the selected services
- Saves artifact metadata for downstream use

## Dev Deployment Flow

The Dev deployment workflow:

- Uses the Dev branch or Dev-triggered path
- Builds the web app image
- Pushes the image to the registry
- Clones the GitOps repo
- Updates the Dev manifest
- Opens a GitOps pull request

This keeps the cluster state in Git and avoids manual kubectl edits.

## Prod Deployment Flow

The Prod deployment workflow follows the same pattern as Dev but should be treated
as a controlled release:

- It targets Prod
- It should be protected by approvals
- It updates the Prod GitOps manifests instead of Dev
- It should keep release intent clear in the commit history

## CI/CD Principles Used Here

- Build first, deploy second
- Keep runtime desired state in Git
- Scan container images before release
- Separate Dev from Prod
- Use workflow secrets instead of hard-coded credentials

## Review Checklist

When you review a workflow change, check:

- Which branch triggers it
- Which environment it targets
- Whether it builds, tests, scans, or deploys
- Whether it writes to GitOps
- Whether it exposes any sensitive value in logs or shell scripts
