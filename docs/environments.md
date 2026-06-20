# Environment Model

This document describes the Dev and Prod model used by the repository.

## Dev

Dev is the working environment.
It is designed to move quickly and validate changes before promotion.

Typical Dev characteristics:

- Faster feedback
- More frequent image promotion
- GitOps updates created automatically by the workflow
- Lower risk than Prod

## Prod

Prod is the controlled release environment.
It should reflect the same application shape as Dev, but with stricter review.

Typical Prod characteristics:

- Approval-driven release flow
- More conservative image promotion
- GitOps updates that require review
- Higher attention to rollback and auditability

## Relationship To GitOps

The app repo does not directly "deploy" in the old imperative sense.
Instead, it proposes a change to the GitOps repo, and the GitOps repo becomes the
source of truth that Argo CD uses to reconcile the cluster.

## What Changes Between Environments

- Image tags
- Sync behavior
- Approval requirements
- Target namespace
- Operational expectations

## What Should Stay Aligned

- Service names
- Manifest structure
- Release semantics
- Monitoring expectations
- Disaster recovery assumptions

## Documentation Rule

When a Dev and Prod behavior differ, document the reason clearly.
If a value is sensitive, do not add it to the documentation.
