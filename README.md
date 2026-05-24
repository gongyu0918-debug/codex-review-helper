# Codex Review Helper

Single-packet review helper for Codex.

This skill lets Codex ask a configured CodeWhale agent CLI for one read-only second opinion on a small review packet. Codex remains the host: it chooses whether to call the helper, verifies every finding locally, and accepts or rejects the result inside the Codex thread.

The required companion tool is the third-party [Hmbown/CodeWhale](https://github.com/Hmbown/CodeWhale) project, formerly named DeepSeek-TUI. CodeWhale is an independent terminal coding agent, closer in category to Codex or Claude Code than to a model provider. Do not describe this helper as using DeepSeek itself; CodeWhale can use different providers, and the old project name should not be treated as the model provider name.

## Compatibility

This helper is tested with the Hmbown agent CLI family on Windows and expects the current `codewhale` command surface: `exec` for one-shot prompts and `mcp-server` for optional MCP probing.

Provider, model, and API key setup live in CodeWhale. The helper only passes `--provider` and `--model` through to that local agent CLI; it does not configure provider accounts and it is not tied to one remote model.

Before using this skill, verify all of these are true:

- CodeWhale is installed and exposes the `codewhale` command.
- CodeWhale has a configured provider, model, and valid API key.
- `codewhale doctor` succeeds.
- A tiny non-secret `codewhale exec` request succeeds before Codex sends review packets.
- API keys stay in CodeWhale configuration; do not place keys in tasks, JSON packets, context files, or review output.

## Boundaries

- One explicit packet per call.
- Review only: code review, code recheck, bug-risk findings, small logs/configs, or short prose.
- No implementation, patch application, full-repo work, migrations, security-owned decisions, or final acceptance.
- No secrets, cookies, credentials, environment dumps, hidden prompts, full conversation history, or repo-wide context.
- No batch jobs, queues, map/reduce, A/B ablation, calibration, scoring, labeling, benchmarks, or corpus processing.
- No loop where helper output is sent back to the helper for grading, correction, or prompt tuning.

## Install

Install CodeWhale first:

```powershell
npm install -g codewhale
where.exe codewhale
codewhale doctor
```

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

`--cwd` is used by the helper to resolve explicit `context_files`. Context files must resolve under that directory, or under the current working directory when `--cwd` is omitted. The CodeWhale process is launched from an isolated temporary directory so it does not start inside the repository.

When CodeWhale supports the flags, the helper passes `--telemetry false`, `--approval-policy never`, and `--sandbox-mode read-only`. On Windows, CLI sandboxing may be best-effort; do not rely on it for secrets or private repositories. The helper prefers the installed `.exe` or PowerShell shim and refuses bare `.cmd`/`.bat` shims that would require command-string evaluation.

## License

MIT-0.
