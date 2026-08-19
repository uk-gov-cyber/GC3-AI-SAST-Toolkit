# Reporting bundle

The reporting bundle turns validated candidate findings into artefacts that engineering owners, security teams, and reviewers can act on. It is used by humans filling reports by hand and by coding agents filling them automatically.

## Contents

```text
README.md                  This file.
reporting-standards.md     Reporting model: layers, evidence states, claims control, VXDF/SARIF/OPTRS use.
review-checklist.md        Reviewer checklist for reporting method, prompts, templates, and generated outputs.
templates/                 Report and supporting templates.
```

## When to use the bundle

Use the vulnerability report template for findings that meet the severity threshold recorded in the department's run scope. Items below the threshold are recorded in the no-finding / not-promoted coverage register, not silently dropped.

The bundle is not the place for speculative issues, unreachable code, style concerns, or unvalidated model output.

## Use as a human

1. Read `templates/agent-filling-guide.md` end-to-end.
2. Review `templates/example-report.md` for the expected level of detail.
3. Copy `templates/vulnerability-report.md` to an approved local or departmental reporting location. Use the report-ID scheme the department has set.
4. Fill every section using `file:line` evidence.
5. Run the required confirmation prompts, including the mandatory negative-control prompt.
6. Redact secrets and avoid unnecessary personal data or git metadata.
7. Tick every box on the submitter checklist.
8. Change `Status` to `Submitted`.
9. Hand the file to the departmental reporting route.
10. Do not commit the completed report to this toolkit repository.

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

## Submission flow

1. Reporter writes a draft report in an approved local or departmental location using the department's report-ID scheme.
2. Reporter completes the submitter checklist.
3. Reporter changes `Status` from `Draft` to `Submitted`.
4. Reporter sends the file to the departmental reporting route.
5. The receiving team assigns a tracking ID, records the verdict, deduplicates where required, and routes the finding.

## Supporting artefacts

The bundle also contains templates for:

- **Scanner pre-pass record** — record deterministic scanner runs before AI SAST.
- **No-finding / not-promoted coverage** — record reviewed candidates that did not become reports.
- **Analyst activity journal** — reproducibility record of what was inspected, run, and decided.
- **Validation report-back** — engineering, platform, or operator reply to a validation task.
- **Private dependency validation map** — record dependencies on non-public evidence blocking validation.

These artefacts, together with the vulnerability report, form the departmental record of the AI SAST run.

## Customisation

You may safely change:

- report-ID scheme;
- vulnerability-class list in the classification section, as new patterns emerge;
- team-specific fields in the metadata header;
- validation report-back fields where local process differs.

You should not change without agreement:

- the `file:line` citation format;
- the mandatory negative-control prompt;
- the evidence-state vocabulary defined in `../03-prompt-guidance/staged/00-run-control-and-evidence-model.md`;
- the requirement to preserve reviewed-but-not-promoted candidates in a coverage register.
