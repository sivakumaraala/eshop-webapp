# .github

This folder contains repository-level automation and release metadata.

## Purpose

It keeps the GitHub Actions workflows and related settings together so release
behavior is easy to find and review.

## Main Areas

- `workflows/` - build, test, deploy, and validation pipelines
- `dependabot.yml` - dependency update automation

## Notes

- Treat workflow changes as operational changes
- Review triggers, branches, and permissions carefully
- Keep any example values or documentation non-sensitive
