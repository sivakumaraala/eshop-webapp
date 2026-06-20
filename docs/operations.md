# Operational Notes

This document captures the non-sensitive operational details that matter to DevOps.

## Monitoring

Monitoring is handled outside this repo, but this repo should still be aware of it.
When releases are promoted, operators need to be able to confirm that:

- The app is healthy
- The image tag matches the intended release
- The browser-facing flow still works
- No unexpected errors appear after deployment

## Security

Security-relevant practices in this repo include:

- Using GitHub secrets instead of hard-coded credentials
- Scanning images with Trivy
- Keeping dependency alerts visible through Dependabot
- Avoiding sensitive data in documentation and manifests

## Release Hygiene

- Keep image tags traceable
- Keep Dev and Prod changes separate
- Prefer GitOps updates over manual cluster edits
- Document rollout intent when workflows change

## Rollback Thinking

Rollback should be done through Git history and GitOps reconciliation, not through
ad hoc fixes in the cluster.

If a release fails:

1. Revert the GitOps change
2. Re-run the promotion path if needed
3. Verify the image and manifest state
4. Confirm the cluster has reconciled

## Operational Boundaries

This repo should not store:

- Secret values
- Private keys
- Personal access tokens
- Environment credentials

If a workflow or script needs one of those values, the value should come from the
appropriate secret store at runtime.
