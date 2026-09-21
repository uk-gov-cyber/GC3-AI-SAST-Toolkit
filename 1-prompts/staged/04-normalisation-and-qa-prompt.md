# Prompt 4 — Normalisation and QA

Use this prompt after the reporting prompt has generated draft artefacts. This is a consistency review, not a discovery pass.

## Scope re-anchor

Begin the response with:

```text
Current task: normalisation, limited to SAST evidence and approved local validation only.
```

## Rules of engagement

Apply the rules of engagement recorded in the run scope. A report is not safe to share if it violates them.

QA checks against the run scope:

- The assessed repository was approved for this exercise, or approval gaps are explicitly recorded.
- Source scanning was limited to an approved sanitised local copy, or lack of sanitisation is recorded as a blocker.
- Reports and evidence do not include live secrets, unnecessary personal data, git commit author names, commit author email addresses, or commit history.
- No claim relies on `.git/`, commit history, local environment files, unrelated local files, or files outside the scan copy unless explicit approval is documented.
- No PoC, validation task, or remediation instruction requires interaction with unauthorised live systems.
- Reports submitted through the departmental reporting route observe the severity threshold recorded in the run scope; items below the threshold are recorded in the reconciliation register with the reason they were not submitted, not silently dropped.
- Confirmation and negative-control checks are present for every report-ready finding or chain.
- No completed report, candidate finding, scan output, exploit evidence, secret, personal data, repository source code, or departmental vulnerability data is written into the toolkit repository.

## Role

You are the consistency reviewer. Normalise reports as a set and identify drift.

Do not discover new findings. Do not improve impact language beyond what the evidence supports.

## Cost control

- Review the report set using registers and evidence classifications first.
- Inspect individual report bodies only when the register indicates a mismatch, missing label, duplicate, or shareability concern.
- Normalise repeated wording once at pattern level, then apply consistently.
- Do not perform new repository reads unless a specific inconsistency cannot be resolved from supplied artefacts.
- Produce concise change lists rather than rewriting reports wholesale unless required.

## Inputs

Use:

- all drafted reports;
- assessment register;
- report reconciliation register;
- promoted findings register;
- promoted chains register;
- validation-candidate register;
- local validation log;
- assumptions register;
- claims-control entries;
- canonical labels from prompt 0.

If an input is missing, state the gap and continue conservatively.

## QA tasks

Review every report for:

- source, sink, path, control, and consequence gate completion;
- reachability proof presence for every final finding — attacker class, entry point, delivery mechanism, sensitive sink or effect, unvalidated conditions;
- feasible attacker outcome presence in every final report;
- severity-confidence separation;
- static evidence versus runtime validation wording;
- local validation versus deployed validation wording;
- local validation summary presence where local checks, harnesses, builds, request tests, scanner credential checks, or runtime validations were run;
- consistent evidence labels;
- consistent vulnerability-class naming;
- consistent CWE and OWASP mapping;
- duplicate findings;
- chains incorrectly merged into findings;
- findings incorrectly inflated into chains;
- scanner leads incorrectly described as proof;
- assumptions hidden in narrative;
- missing or weak Vulnerability Disclosure Status block;
- disclosure wording that does not separate source-confirmed, locally validated, deployed validated, and plausible-chain states;
- impact statements that imply production impact when only static or source evidence exists;
- missing endpoint authorisation map for web, API, or admin findings;
- reports that read as security narratives but lack engineering-ready remediation ownership, acceptance criteria, validation evidence, or coordinated work-package structure;
- missing coordinated remediation work package for multi-service, shared-substrate, repeated-pattern, or identity findings;
- remediation that does not match the control gap;
- validation tasks that are vague, unsafe, or not actionable;
- no-finding or not-promoted coverage records for every assessed repository without promoted findings;
- private-repository or private-configuration dependency map when private, config, or runtime evidence is load-bearing;
- method-control review notes when method improvements or follow-on SAST or DAST work were requested;
- SARIF, OPTRS, VXDF, Markdown, and register disagreement;
- register entries whose report paths are blank, point only to routed or supplement copies, or do not resolve to existing canonical top-level reports;
- canonical top-level `vulnerability-reports/` and `chain-reports/` counts that do not match the assessment register after excluding explicitly non-issued, downgraded, or disconfirmed items;
- missing per-issue SARIF for registered findings unless SARIF is explicitly withheld in the reconciliation register;
- missing per-issue OPTRS for registered findings or chains unless OPTRS is explicitly withheld in the reconciliation register;
- aggregate SARIF or OPTRS indexes not rebuilt from the final per-issue artefacts;
- routed delivery-pack copies that lack corresponding canonical top-level reports and register entries;
- delivery-pack archive or checksum manifests that do not verify after any routed file change;
- evidence that report drift required repeated manual correction and should be captured as a reusable rule.

## Normalisation rules

Use canonical labels exactly.

Replace near-synonyms:

| Replace | With |
|---|---|
| `confirmed in code` | `SAST-confirmed` |
| `probably vulnerable` | `SAST-likely` or `SAST-suspected` |
| `not tested` | `not-validated` |
| `needs config check` | `config-dependent` |
| `needs private repo` | `private-dependent` |
| `confirmed locally` | `locally-validated` |

Do not rewrite a source-only hypothesis into a confirmed exploitability claim.

## Report set checks

Produce a table with:

- report ID;
- lifecycle state;
- original severity;
- normalised severity;
- original confidence;
- normalised confidence;
- evidence-label changes;
- wording changes;
- downgrade required (yes/no);
- human review required (yes/no);
- reason.

Also produce a folder or artefact reconciliation table with:

- registered finding count;
- top-level vulnerability report count;
- registered chain count;
- top-level chain report count;
- per-issue SARIF count and withheld-SARIF count;
- per-issue OPTRS count and withheld-OPTRS count;
- delivery-pack folder count;
- delivery archive count;
- checksum verification result;
- unresolved mismatches.

If any unresolved mismatch exists, do not mark the pack safe to share. Fix the mismatch or list it in `unresolved-inconsistencies.md` with the exact file paths and reason.

## Required outputs

Produce:

1. `normalisation-summary.md`;
2. `qa-findings.md`;
3. updated assessment register with `qa_status`;
4. updated report reconciliation register;
5. list of reports safe to share;
6. list of reports requiring human review;
7. list of reports downgraded, merged, disconfirmed, or withheld;
8. unresolved inconsistencies;
9. missing-contract-artefacts section covering absent reachability proofs, feasible attacker outcomes, local validation summaries, no-finding coverage records, private-dependency maps, method-control notes, or required machine-readable artefacts.

## Shareability gate

A report is safe to share only if:

- lifecycle state is `promoted-finding` or `promoted-chain`;
- source, sink, path, control, and consequence gates are complete;
- reachability proof is present or explicitly validation-blocked;
- feasible attacker outcome is stated in plain English;
- evidence labels are present and canonical;
- vulnerability disclosure classification is present and does not overclaim;
- endpoint authorisation and object-scope controls are mapped for route, API, or admin findings;
- coordinated remediation plan exists for multi-service or shared-pattern findings;
- any local validation is summarised without implying deployed validation;
- negative-control result is present;
- assumptions are explicit;
- required private or config dependency and no-finding coverage artefacts exist at report-set level;
- validation tasks are safe and specific;
- machine-readable artefacts agree with the human-readable report;
- canonical top-level reports, register paths, per-issue machine-readable artefacts, routed delivery copies, archives, and checksum manifests reconcile end to end;
- no wording implies more certainty than the evidence supports.

Otherwise mark it `requires-human-review`.

## Final anti-drift statement

End with:

```text
Normalisation result: <count> reports safe to share, <count> requiring human review, <count> downgraded/merged/disconfirmed/withheld.
```
