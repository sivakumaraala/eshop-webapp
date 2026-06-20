# build

This folder contains helper scripts used by the release and image workflows.

## Purpose

The scripts here support repeatable operational tasks such as building images or
assembling manifests. They are useful when a manual or scheduled release step needs
the same command sequence every time.

## Contents

- `acr-build/queue-all.ps1`
- `multiarch-manifests/create-manifests.ps1`

## How To Think About These Scripts

- They support release automation
- They should stay generic and non-sensitive
- They should not hard-code environment secrets
- They should be easy to invoke from a workflow or operator shell

## Notes

- Use these scripts as helpers, not as hidden deployment logic
- Keep them aligned with the workflows that call them
