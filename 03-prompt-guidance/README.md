# Prompt guidance

This section contains the model prompt pack. It is derived from a departmental pilot approach that has been shown to produce defensible, evidence-led output when applied consistently.

## Files

Use in order:

1. `staged/00-run-control-and-evidence-model.md` — run control, scope statement, evidence-label vocabulary. Prime the agent with this before any substantive prompt.
2. `staged/01-repo-triage-prompt.md` — codebase mapping and repository triage.
3. `staged/02-full-sast-attack-path-prompt.md` — source-to-sink SAST and attack-path analysis.
4. `staged/03-security-reporting-output-prompt.md` — reporting output: human reports, machine-readable artefacts, routing packs.
5. `staged/04-normalisation-and-qa-prompt.md` — normalisation and QA across the report set.

For departments that cannot run the staged workflow, use:

- `one-shot-minimum-sast-prompt.md` — a minimum single-prompt fallback that combines scope control, triage, source-to-sink review, compact reporting, and QA.

The one-shot prompt is a fallback, not a replacement for the staged workflow.

## Prompting principles

- Give the agent a clear role, task, and scope.
- Ask for `file:line` citations on every code claim.
- Ask the agent to distinguish evidence from assumption.
- Ask for source, sink, path, guards, reachability, and negative-control assessment.
- Clear agent context between unrelated tasks.
- Do not ask the agent to interact with live systems or unauthorised infrastructure.
- Do not ask the agent to inspect `.git/`, commit history, commit author metadata, or files outside the sanitised scan copy.
- Do not include live secrets, personal data, or commit author information in prompts.
- Prefer prompts that test both sides: "assess whether this candidate finding is real, then argue the strongest case that it is a false positive".

## Cost control

Use deterministic tooling to narrow the corpus before model review. Feed scanner output as structured lead IDs and file/rule/path metadata, not as large raw dumps. Define repository, file, lead, and time or token budgets before review starts. Stop candidate analysis when a required evidence gate fails.

## Version pinning

Record for every run:

- model name and version;
- prompt-pack version;
- prompt IDs used;
- agent client and version.

Behaviour changes across model and prompt versions. Version pinning is what makes a past finding reproducible.

## Adapting the prompts

The prompts may be extended, but any change must preserve the evidence gates, coverage records, and negative-control requirements. Document any intentional change in the run scope so the deviation is auditable.
