# e2e

This folder contains the end-to-end browser tests used for release confidence.

## Purpose

The tests here validate user-facing behavior in a browser, which makes them
valuable from a DevOps perspective because they verify the experience after build
and deployment.

## What This Folder Represents

- Real user journeys
- Release confidence checks
- A signal that the deployed app still behaves correctly

## Notes

- Keep tests focused on user flows that matter to release quality
- Avoid putting secrets or environment credentials in test code
- Match test setup with the workflow that runs them
