# GC3 AI SAST Toolkit

Guidance for government departments running AI-assisted static application security testing (SAST) against their own source code.

The toolkit provides a repeatable workflow for using a coding agent (Claude Code, Codex, or an equivalent) to identify, validate, and report vulnerabilities in departmental code. It does not provide the model itself. Departments are responsible for their own commercial agreement with an AI provider and for holding the associated API credentials.

## Contents

```text
00-introduction/          What AI SAST is, aims, and core principles.
01-pre-requisites/        Model access, sandbox hardening, network, monitoring,
                          repository selection, and repository preparation.
02-methodology/           Mapping, scanning, validation, and reporting principles.
03-prompt-guidance/       Prompting principles, staged prompts, and one-shot fallback.
04-reporting-bundle/      Reporting standards, review checklist, and templates.
CLAUDE.md / AGENTS.md     Agent instructions loaded automatically by coding agents.
```

## How agents use this toolkit

Departments should run the coding agent from the root of this toolkit. `CLAUDE.md` (for Claude Code) and `AGENTS.md` (for Codex and other agents that honour it) are loaded automatically and instruct the agent to:

- work only on the approved, sanitised local repository copy;
- follow the staged prompts in `03-prompt-guidance/`;
- cite every code claim as `path/to/file.ext:NN-MM`;
- separate source-confirmed behaviour from runtime-validated behaviour;
- run a mandatory negative-control check before promoting any finding;
- write reports into an approved local location using the templates in `04-reporting-bundle/templates/`;
- never upload findings, prompts, secrets, personal data, or source excerpts to this repository or to any other unapproved location.

There is a single set of agent instructions at the toolkit root. The reporting bundle deliberately does not have its own `CLAUDE.md` / `AGENTS.md` — the root files cover the entire workflow, including report filing.

## Where the code being scanned lives

The target code is a **sanitised local clone inside the hardened sandbox** — not in a prompt, not on GitHub for the purposes of scanning, and not inside this toolkit repository.

Typical layout:

```text
~/sandbox-workspace/
├── gc3-ai-sast-toolkit/        # this toolkit
└── scan-copy/
    └── <target-repo>/          # sanitised clone; .git/ removed, secrets and PII redacted
```

Start the coding agent from `gc3-ai-sast-toolkit/` so `CLAUDE.md` / `AGENTS.md` load automatically, then direct it at the scan copy. The agent reads files from the local sandbox filesystem using its own tools; source code is never pasted into prompts. GitHub, if used at all, is only a source for the initial clone — scan outputs, findings, and reports must never be pushed back to GitHub or committed to this toolkit repository.

## Participant journey

1. Read `00-introduction/overview.md`.
2. Complete every item in `01-pre-requisites/` before running any prompt.
3. Read `02-methodology/README.md`.
4. Use the staged prompts in `03-prompt-guidance/staged/` in order. If you cannot run the full staged workflow, use the one-shot fallback prompt.
5. Validate every candidate finding using the confirmation and negative-control steps in `02-methodology/validation-and-triage.md`.
6. Report validated findings using `04-reporting-bundle/`.
7. Preserve a no-finding / not-promoted coverage record so future reviewers know what was reviewed and why candidates were not promoted.

## Documentation only

This toolkit is documentation. Do not commit vulnerability reports, candidate findings, scan outputs, prompt transcripts, exploit evidence, source code, secrets, personal data, or departmental vulnerability data to this repository. Findings must be handled through the agreed departmental reporting route.

## Complementary controls

AI SAST complements — it does not replace — DAST, software composition analysis, secret scanning, manual code review, and threat modelling. Departments should continue to run the controls they already use and treat AI SAST as an additional evidence source.

## Cost indication

At the time of writing (mid 2026), the table below is an indication of cost. 

| Repo size | Small | Med | Large |
|---|---| ---| ---|
| approx. lines of code | 17,800 | 300,000 | 1,500,000 |
| Cost of AI scan (£) | 2 | 25 | 123 |

## Style

British English. ASCII only in report bodies.
