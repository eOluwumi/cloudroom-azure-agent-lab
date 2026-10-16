# Cloudroom Azure Agent Lab

A small, isolated Azure environment for validating a self-hosted Cloudroom Core agent backed by Azure AI Foundry.

## Milestone status

**Mission 001: passed for its defined scope.**

- Cloudroom Core was running as a systemd service on an isolated Ubuntu VM.
- The Pi coding agent used Azure AI Foundry's Responses API with the **gpt-6-astra** deployment.
- VM managed identity was used for authentication; no model API key was hardcoded in the agent configuration.
- A bounded Python JSONL diagnostic task was generated and exercised on the VM.
- Python compilation succeeded and all **7 unit tests passed**.
- CLI checks passed for both normal and strict modes: normal mode returned exit code 0; strict mode returned exit code 1 when malformed input was present.
- The agent-to-model response path was verified. External tool execution by the agent and reboot/disaster recovery are **not** claimed as tested.

## Repository contents

- [Mission 001 report](docs/mission-001.md) — scope, architecture summary, checks, and limitations.
- [Operations guide](docs/operations.md) — safe VM stop/start and verification steps.

The Mission 001 Python files were created and tested on the VM. They are not included in this documentation-only commit; copy them into version control in a subsequent reviewed change if source preservation is required.

## Security notes

This repository is currently public. Keep it free of credentials, access tokens, .env files, private keys, managed-identity tokens, internal logs, and unreviewed machine configuration. The VM uses managed identity for Azure AI Foundry access. Do not commit service environment files or token-helper contents.

## Next steps

1. Preserve the Mission 001 source artifacts in a reviewed commit.
2. Test service and agent recovery after VM restart.
3. Define and test a backup/restore procedure for Cloudroom's local history.
4. Add automated checks for the diagnostic utility.
