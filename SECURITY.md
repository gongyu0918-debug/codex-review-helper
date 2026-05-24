# Security

## Data Boundary

Codex Review Helper is a disclosure reducer, not a privacy guarantee. It blocks obvious secrets and restricts the helper to one explicit review packet, but any packet sent to a remote model provider may still be processed under that provider's terms.

Do not use the helper for private credentials, customer data, unpublished business material, full repositories, generated corpora, model evaluation datasets, or batch workflows.

## External CLI Review

This repository is a Codex skill and helper script. It is not an upstream model-provider official agent and is not affiliated with any model provider.

The current transport calls the third-party `Hmbown/CodeWhale` agent CLI, formerly named DeepSeek-TUI. CodeWhale is an independent terminal coding agent, not DeepSeek itself.

The npm wrapper launches downloaded release binaries, so review the installed package and binary provenance before use:

```powershell
where.exe codewhale
codewhale --version
codewhale doctor
```

CodeWhale must already have a configured provider, model, and valid API key before this helper can work. Keep API keys in CodeWhale configuration only; never put keys in review tasks, JSON packets, context files, issue reports, or output files.

The helper does not hide or bypass CodeWhale, the selected provider, or remote provider policy. If the configured provider cannot be trusted for a packet, do not send that packet.

The helper invokes CodeWhale with telemetry disabled for that process, read-only sandbox mode, no approvals, and an isolated working directory. These controls reduce accidental disclosure, but they do not change the remote provider's data handling.

Explicit context files are resolved under the configured working directory boundary. On Windows, the helper uses the installed release binary or PowerShell shim and refuses `.cmd`/`.bat` shims unless a same-name PowerShell shim can be used instead.

Optional global hardening for CodeWhale:

```powershell
codewhale config set allow_shell false
codewhale config set approval_policy never
codewhale config set sandbox_mode read-only
```

## Reporting

Open an issue with a minimal, non-secret reproduction. Do not include API keys, cookies, config files, private logs, or proprietary source.
