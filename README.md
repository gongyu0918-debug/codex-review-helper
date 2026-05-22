# Codex Review Helper

Single-packet review helper for Codex.

This skill lets Codex ask a configured external review CLI for one read-only second opinion on a small review packet. Codex remains the host: it chooses whether to call the helper, verifies every finding locally, and accepts or rejects the result inside the Codex thread.

The current transport is compatible with the command named `deepseek` installed by the third-party [Hmbown/DeepSeek-TUI](https://github.com/Hmbown/DeepSeek-TUI) project. The provider and model are configurable; the packaged default is only a default. That dependency is an unofficial client, not an upstream model-provider official agent. This repository is not affiliated with any model provider, does not provide a model, and does not create a training or evaluation pipeline.

## Compatibility

This helper is tested with Hmbown/DeepSeek-TUI `0.8.39` on Windows. It should also work with compatible client releases that keep the same non-interactive command surface: `exec` for one-shot prompts and `mcp-server` for optional MCP probing.

Provider and model selection are passed through to the configured client. The helper is not tied to one remote model; change `--provider` and `--model` when using another supported backend.

## Boundaries

- One explicit packet per call.
- Review only: code review, code recheck, bug-risk findings, small logs/configs, or short prose.
- No implementation, patch application, full-repo work, migrations, security-owned decisions, or final acceptance.
- No secrets, cookies, credentials, environment dumps, hidden prompts, full conversation history, or repo-wide context.
- No batch jobs, queues, map/reduce, A/B ablation, calibration, scoring, labeling, benchmarks, or corpus processing.
- No loop where helper output is sent back to the helper for grading, correction, or prompt tuning.

## Install

Copy the skill folder into Codex:

```powershell
Copy-Item -Recurse .\_skill_package\codex-review-helper "$env:USERPROFILE\.codex\skills\codex-review-helper"
```

Recommended call:

```powershell
python "$env:USERPROFILE\.codex\skills\codex-review-helper\scripts\review_helper.py" `
  --input-json .\packet.review-helper.json `
  --structured-result `
  --json-result
```

## What The External CLI Gets

Only the explicit packet:

- `task`
- optional `context_text`
- contents of explicitly listed `context_files`
- helper framing for structured findings

It does not get Codex hidden prompts, memory, environment variables, browser/session data, credential files, or files that were not attached.

## Safety Notes

This helper cannot control a remote provider's retention policy. For sensitive code or documents, do not use a remote external model. Use Codex locally, a native Codex subagent, or a local model you control.

`--cwd` is used by the helper to resolve explicit `context_files`. Context files must resolve under that directory, or under the current working directory when `--cwd` is omitted. The downstream CLI process is launched from an isolated temporary directory so it does not start inside the repository.

When the configured CLI supports the flags, the helper passes `--telemetry false`, `--approval-policy never`, and `--sandbox-mode read-only`. On Windows, CLI sandboxing may be best-effort; do not rely on it for secrets or private repositories. The helper prefers the installed `.exe` or PowerShell shim and refuses bare `.cmd`/`.bat` shims that would require command-string evaluation.

## License

MIT-0.
