# Methodology

The methodology in this section is the departmental model methodology used in the pilot phase of AI-assisted SAST across government. It has been shown to produce defensible, evidence-led output when applied consistently.

## Workflow

```text
approved sanitised scan copy
  -> codebase mapping (trust boundaries, entrypoints, high-risk areas)
    -> vulnerability scanning (source-to-sink candidate identification)
      -> validation and triage (confirmation, negative control, evidence state)
        -> reporting through the departmental route
```

## Documents in this section

- `codebase-mapping.md` — enumerate entrypoints, trust boundaries, and high-risk areas before scanning.
- `vulnerability-scanning.md` — targeted, evidence-led scanning against the mapped surface.
- `validation-and-triage.md` — validation, confirmation, and mandatory negative-control checks.
- `reporting-principles.md` — evidence states, claims-control wording, and coverage records.

## Where the prompts fit

The prompts in `03-prompt-guidance/staged/` implement this methodology step-by-step. Prompt `00` establishes run control and the evidence-label vocabulary. Prompt `01` performs codebase mapping. Prompt `02` performs source-to-sink scanning. Prompt `03` produces the reporting output. Prompt `04` performs normalisation and QA across the report set.

A department that cannot run the staged workflow may use `03-prompt-guidance/one-shot-minimum-sast-prompt.md` as a fallback. The one-shot prompt produces a compact minimum output; it is not a replacement for the staged workflow where time and tooling allow.

## Non-negotiable evidence gates

Regardless of which prompt path is used, do not promote a finding to a report until:

- source, sink, path, control gap, and consequence are documented with `file:line` citations;
- reachability reasoning is recorded (entrypoint, trust boundary, guard conditions);
- source-confirmed behaviour is separated from runtime-validated behaviour;
- claims-control wording is applied — no production-exploitability claim without deployed evidence;
- a negative-control argument that tries to refute the finding has been produced;
- the evidence state is labelled using the vocabulary in `03-prompt-guidance/staged/00-run-control-and-evidence-model.md`;
- reviewed candidates that were not promoted are recorded in the no-finding / not-promoted coverage register.
