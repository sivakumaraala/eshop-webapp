# Disaster Recovery Runbook

This document covers the recovery strategy for the eShop DevOps project.
It focuses on the production-style stack represented in the public repos:

- Application code and build artifacts in `eshop-webapp`
- Kubernetes manifests and GitOps promotion in `eshop-gitops`
- Terraform-based infrastructure in `eshop-infra`

## Goals

- Keep application and infrastructure recovery repeatable.
- Preserve enough data to restore service after a failure.
- Make rollback simple by relying on Git history and GitOps reconciliation.
- Avoid ad hoc manual changes in the cluster whenever possible.

## Recovery Targets

- RPO: 24 hours for application databases unless the environment has stricter backups.
- RTO: restore the application stack as soon as GitOps and infrastructure are available again.
- Priority: recover the data layer first, then application workloads, then observability.

## What Must Be Backed Up

### Databases

Back up all persistent databases used by the stack:

- `catalogdb`
- `identitydb`
- `orderingdb`
- Any additional persistent data stores added later

Recommended backup method:

- Use scheduled database dumps for logical recovery
- Keep periodic full backups
- Keep a retention window of daily backups plus at least one longer-term snapshot

### Cluster and Git State

Back up the sources of truth that describe the platform:

- `eshop-gitops`
- `eshop-infra`
- GitHub workflow definitions
- Terraform variable files and backend settings

Why this matters:

- Git history is the main rollback mechanism
- Cluster state should be recreated from manifests, not repaired by hand

### Persistent Storage

If any workload later adds persistent volumes, back up:

- Volume snapshots where the platform supports them
- Exported archives of any uploaded files or generated business data

### Secrets and Configuration

Do not commit secret values to Git.
Instead:

- Store secrets in GitHub Actions secrets, Vault, Key Vault, or an equivalent secret manager
- Keep a documented inventory of which secret belongs to which service
- Ensure the restore process includes rehydrating those secrets before workloads are started

## Backup Strategy

### Daily

- Run a database backup at least once per day.
- Store the backup in a separate durable location from the running cluster.
- Verify that the backup job completed successfully.

### Weekly

- Keep a longer-term snapshot or archive.
- Validate that at least one restore test can be performed from the latest backup set.

### Before Major Changes

Take a fresh backup before:

- Terraform changes
- Kubernetes manifest changes that affect data stores
- Version upgrades for database or messaging components
- Large app releases that touch schema or data contracts

## Recovery Scenarios

### 1. Single Pod Failure

If a pod crashes or is evicted:

1. Let Kubernetes reschedule the pod.
2. Check readiness and liveness probes.
3. Confirm the service is healthy again.

No data restore should be needed unless the pod was stateful and its volume is damaged.

### 2. Node Failure

If a node fails:

1. Allow Kubernetes to recreate workloads on a healthy node.
2. Confirm that services with replicas come back automatically.
3. Check stateful workloads and ensure their volumes reattach or are restored.

### 3. Namespace Failure

If the namespace is deleted or corrupted:

1. Reapply the GitOps manifests from `eshop-gitops`.
2. Recreate config, services, deployments, and stateful workloads.
3. Restore databases from backup if any persistent data was lost.

### 4. Cluster Rebuild

If the cluster itself is lost:

1. Recreate the cloud or local infrastructure from `eshop-infra`.
2. Reinstall GitOps tooling and Argo CD if needed.
3. Reapply the `eshop-gitops` manifests.
4. Restore any required databases and secrets.
5. Verify monitoring and ingress-style entry points, if present, after the workloads are stable.

### 5. Database Restore

If a database is corrupted or data is lost:

1. Stop or scale down the affected workload.
2. Restore the most recent valid backup.
3. Reapply schema migrations only if required by the restore procedure.
4. Restart the application and verify that it can connect.
5. Check application logs for data mismatches or failed migrations.

## Rollback Procedure

Use Git as the rollback source of truth.

### Rollback an Application Release

1. Identify the last known good commit in `eshop-webapp`.
2. Revert the release-related change.
3. Allow the deployment workflow to open a new GitOps PR or manually update the manifest history.
4. Let Argo CD resync the previous manifest version.
5. Verify the app health checks and smoke tests.

### Rollback a GitOps Change

1. Revert the bad commit in `eshop-gitops`.
2. Rebuild or revalidate the manifests if needed.
3. Allow GitOps to sync the reverted state.
4. Confirm the affected namespace returns to the previous working version.

### Rollback an Infrastructure Change

1. Revert the Terraform change in `eshop-infra`.
2. Run plan first to confirm the delta.
3. Apply the rollback through the normal workflow.
4. Reconcile any Kubernetes objects or secrets affected by the infrastructure change.

## Restore Order

When recovering a full environment, restore in this order:

1. Infrastructure
2. GitOps controllers and platform services
3. Secrets and config
4. Databases and persistent data
5. Application workloads
6. Observability stack
7. Smoke tests and functional verification

## Validation Checklist

After any restore or rollback, verify:

- Pods are running and ready
- Services respond on their health endpoints
- The web app loads successfully
- Data can be read from the restored databases
- Monitoring dashboards show healthy targets
- No unexpected image tags or stale manifests remain in prod

## Operational Notes

- Dev is treated as the working reference environment.
- Prod should be restored by reapplying the GitOps and Terraform definitions, not by manual cluster edits.
- Backups must be tested. A backup that has never been restored is not a proven backup.
