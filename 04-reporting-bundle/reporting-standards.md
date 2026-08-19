# Reporting standards

This document defines the reporting model used by the bundle. It keeps human-readable reports, machine-readable artefacts, work items, validation actions, and handoff packs consistent.

## Core principles

- Keep individual source findings and attack chains separate, linked by stable IDs.
- Make each human report self-contained: repository, commit, file, line, code evidence, abuse case, feasible attacker outcome, evidence state, validation gaps, remediation direction, and safe wording.
- Separate source-confirmed behaviour from runtime-validated behaviour and deployed-environment validation.
- Do not describe a finding as proven production compromise unless the report contains that evidence.
- Preserve lower-severity, duplicate, disconfirmed, downgraded, not-promoted, and out-of-scope decisions in registers rather than silently dropping them.
- Require reachability reasoning before promotion: entrypoint, source, sink, authentication or authorisation boundary, guard conditions, abuse case, and validation dependency.
- Treat scanner output as candidate discovery and file ranking until human source-to-sink review promotes a finding.
- Keep remediation tasks separate from validation tasks.

## Required output layers

| Layer | Required artefact | Purpose |
|---|---|---|
| Human findings | `vulnerability-reports/` | Self-contained reports for individual source findings. |
| Human chains | `chain-reports/` | Self-contained reports for multi-step or cross-repository chains. |
| Executive or programme view | `whole-exercise-report.md` | Summary of scope, top risks, evidence state, validation gaps, systemic themes, and next actions. |
| Reconciliation | `report-reconciliation-register.md` | Trace every finding and chain to an artefact or explicit reporting decision. |
| Machine-readable register | `assessment-register.json` | Canonical machine-readable state for repositories, findings, chains, actions, attribution, claims, and artefacts. |
| SARIF | `sarif/` | Developer-ingestible source-level findings. |
| OPTRS | `optrs/` | Structured engagement or report containers for findings and chains. |
| VXDF decisions | `vxdf/` | Issue VXDF only for validated exploitable flows; otherwise produce non-issuance notes. |
| Validation | `validation/` | Actions and evidence required to close uncertainty. |
| Remediation | `remediation/` | Engineering actions required to fix or reduce risk. |
| Routing | `routing/` | Owner routing, engineering work items, delivery packs, and import files. |
| Review or supporting evidence | `review/` | Scanner triage, validation evidence, method-control notes, limitations, and review supplements. |

## Evidence states

Use these labels consistently across Markdown, JSON, CSV, SARIF, OPTRS, VXDF decisions, and delivery packs. Detailed vocabulary is defined in `../03-prompt-guidance/staged/00-run-control-and-evidence-model.md`.

| Evidence state | Meaning |
|---|---|
| Source-confirmed | Source shows the behaviour or missing control. |
| Locally validated | A local unit, request, compiled, harness, shell, or framework test exercised the stated behaviour. |
| Runtime or deployed validated | Authorised testing or owner evidence confirmed behaviour in a deployed or representative environment. |
| Scanner-supported | Scanner output identified a candidate; source-to-sink review is still needed before reporting as a vulnerability. |
| Conditional | Impact depends on a named private, runtime, identity-provider, secret, pipeline, cloud, telemetry, or configuration dependency. |
| Disconfirmed | Evidence showed the suspected issue or chain does not hold as originally stated. |
| Not promoted | Reviewed candidate did not meet the reporting threshold. |

Local validation must explain exactly what was run and what it proves. It must not be used as shorthand for production exploitability unless deployed evidence is also present.

## Human report requirements

Every vulnerability and chain report should include:

- decision snapshot;
- plain-English human summary;
- feasible attacker outcome;
- affected repository, commit, file, and line evidence;
- source-to-sink or route or reachability explanation;
- preconditions and assumptions;
- evidence state and validation dependency;
- false-positive reducers and negative controls;
- remediation guidance;
- validation actions;
- claims-control wording;
- routing and attribution;
- references to related artefacts.

The human summary should answer: what is wrong, how an attacker could use it, what they could realistically achieve, what evidence supports that conclusion, and what remains unvalidated.

## SARIF

Use SARIF for source-level findings that benefit from file, line, rule, severity, CWE or OWASP, and remediation metadata. SARIF result properties should include stable IDs, repository URL, commit SHA, evidence state, validation dependency, routing fields, claims-control summary, and links back to the human report. Do not use SARIF as the only home for chain reasoning.

## OPTRS

Use OPTRS as a structured report container for findings and chains where a machine-readable engagement or report format is useful. OPTRS entries should preserve the same evidence state, affected assets, impact, validation gaps, remediation actions, and artefact references as the human reports. Markdown human reports remain the safest artefact for nuanced wording.

## VXDF

Issue VXDF only when an exploitable data flow or chain is validated with adequate proof evidence. If a chain is source-supported but not validated, produce a VXDF non-issuance note or mark VXDF as withheld pending validation. Do not issue VXDF for purely hypothetical, scanner-only, or private-dependency-blocked chains.

## Assessment register

The assessment register reconciles repositories and attribution; findings; chains; common patterns; validation actions; remediation actions; evidence states; claims-control entries; artefact paths; and routing and owner-confirmation fields. It is the machine-readable source for consistency checks and downstream generation. It must not contain raw secrets.

## Reconciliation register

The reconciliation register answers where every issue went. Accepted reporting decisions:

- standalone human report;
- combined issue-family report;
- SARIF or OPTRS generated;
- VXDF issued;
- VXDF withheld pending validation;
- downgraded;
- disconfirmed;
- duplicate;
- out of scope;
- not promoted;
- awaiting owner validation.

This register is a traceability control, not a severity gate.

## Validation and remediation

Validation actions must state linked finding or chain ID; owner or validator; exact evidence required; safe test boundary; expected `PASS`, `FAIL`, or `INCONCLUSIVE` result format; effect on evidence state, severity, or reporting decision.

Remediation actions must state linked finding or chain ID; engineering owner; concrete fix direction; priority; dependencies; verification method.

Validation is not remediation. Remediation is not proof that a chain was exploitable.

## Claims control

Every material report should include safe-claim wording.

| Claim type | Use |
|---|---|
| Safe to claim now | Directly supported by source or validated evidence. |
| Not safe to claim | Would overstate available evidence. |
| Safe only if validated | Requires named owner, runtime, identity, cloud, secret, pipeline, private-source, telemetry, or deployed-route evidence. |

This is mandatory where a finding could be misquoted as confirmed production exploitability.

## Attribution and routing

Use the department's attribution hierarchy:

1. Repository custom properties or the department's approved attribution registry: high confidence.
2. Active IaC or deployment tags: medium confidence fallback.
3. Resolved IaC variables or tfvars values: medium confidence with environment context.
4. Unresolved IaC expressions: low-confidence candidate only.
5. Commented IaC tags: low-confidence historical or template signal only.
6. Repository topics, README, CODEOWNERS, catalog files, and documentation: routing signals only unless an approved mapping validates them.

Do not infer portfolio, product, or service-line values from topics unless an approved mapping exists. Preserve attribution source, confidence, conflicts, gaps, and owner-confirmation requirements.

## Engineering work items and delivery packs

Create ServiceNow or Jira-friendly work items without losing assessment nuance. Work items should include issue ID; title; severity; evidence state; validation dependency; private dependency; safe claim; human summary; feasible attacker outcome; remediation action; validation action; report path; routing fields; owner-confirmation fields.

Create one delivery pack per routing owner or group where owner handoff is required. Each pack should include scoped reports, scoped work items, import CSVs, validation and remediation extracts, routing notes, and report-back templates.

## Packaging and quality checks

Before sharing a reporting bundle, run or record:

- placeholder and draft-marker scan;
- token or secret-pattern scan;
- temporary and editor-backup file scan;
- JSON validation for registers and machine-readable artefacts;
- human-report structure check;
- work-item and delivery-pack completeness check;
- checksum generation for selected key artefacts or archives;
- exclusions list for cloned repositories, local tokens, raw scanner archives, raw secrets, and scratch material;
- known limitations and validation dependencies.

The package manifest should describe what is included, what is excluded, what checks were completed, and what limitations remain.

## Post-remediation

After remediation, re-run the relevant staged prompt against the fixed code and update the evidence state and reporting decision. Do not close a finding on remediation alone — record the verification evidence.
