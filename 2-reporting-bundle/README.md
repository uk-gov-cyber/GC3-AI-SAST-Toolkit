# Reporting bundle

The reporting bundle turns validated candidate findings into artefacts that engineering owners, security teams, and reviewers can act on.

## Contents

```text
README.md                  This file.
reporting-standards.md     Reporting model: layers, evidence states, claims control, VXDF/SARIF/OPTRS use.
review-checklist.md        Reviewer checklist for reporting method, prompts, templates, and generated outputs.
templates/                 Report and supporting templates.
```

Reviewer-facing procedure (when and how a human uses these artefacts, submitter checklist, and the departmental reporting route) is in the AI SAST Toolkit written guidance, not in this repository.

## Use with a coding agent

The root `CLAUDE.md` and `AGENTS.md` instruct any coding agent to follow this bundle when asked to report, escalate, or file a vulnerability. The agent must:

- follow `templates/agent-filling-guide.md`;
- apply the severity threshold recorded in the run scope (the agent does not invent one);
- cite every code claim as `file:line`;
- run confirmation prompts and the mandatory negative-control prompt;
- record discovery and confirmation tool, model, model version, and prompt-pack version honestly;
- avoid live secrets, unnecessary personal data, and git metadata;
- produce the report in an approved local or departmental location;
- not submit or commit the report to this toolkit repository.

