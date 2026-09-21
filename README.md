# GC3 AI SAST Toolkit

The toolkit provides a repeatable workflow for using a coding agent (Claude Code, Codex, or an equivalent) to identify, validate, and report vulnerabilities in source code.

## Contents

```text
1-prompts/             Prompting principles, staged prompts, and one-shot fallback.
2-reporting-bundle/    Reporting standards, review checklist, and templates.
CLAUDE.md / AGENTS.md  Agent instructions loaded automatically by coding agents.
```

## How agents use this toolkit

Run the coding agent from the root of this toolkit. `CLAUDE.md` (for Claude Code) and `AGENTS.md` (for Codex and other agents that honour it) are loaded automatically and instruct the agent to:

- work only on the approved, sanitised local repository copy;
- follow the staged prompts in `1-prompts/`;
- cite every code claim as `path/to/file.ext:NN-MM`;
- separate source-confirmed behaviour from runtime-validated behaviour;
- run a mandatory negative-control check before promoting any finding;
- write reports into an approved local location using the templates in `2-reporting-bundle/templates/`;
- never upload findings, prompts, secrets, personal data, or source excerpts to this repository or to any other unapproved location.

There is a single set of agent instructions at the toolkit root.

## Repo setup

Typical layout:

```text
~/sandbox-workspace/
├── gc3-ai-sast-toolkit/        # this toolkit
└── scan-copy/
    └── <target-repo>/          # sanitised clone; .git/ removed, secrets and PII redacted
```

Start the AI SAST agent from `gc3-ai-sast-toolkit/` so `CLAUDE.md` / `AGENTS.md` load automatically, then direct it at the scan copy.

## Agent journey

1. Load agent instructions from `CLAUDE.md` / `AGENTS.md` at the toolkit root.
2. Use the staged prompts in `1-prompts/staged/` in order. If the reviewer directs a one-shot run, use `1-prompts/one-shot-minimum-sast-prompt.md`.
3. Validate every candidate finding using the confirmation and negative-control steps required by the prompt pack.
4. Report validated findings using `2-reporting-bundle/`.
5. Preserve a no-finding / not-promoted coverage record so future reviewers know what was reviewed and why candidates were not promoted.

## Style

British English. ASCII only in report bodies.
