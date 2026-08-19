# Prompt 3 — Security reporting outputs

Use this prompt after the SAST prompt. It consumes the reporting handoff produced by prompt 2 and produces consistent security-reporting artefacts for engineering, security review, and senior decision-making.

This prompt does not discover new vulnerabilities.

## Preamble

Before producing output, apply `00-run-control-and-evidence-model.md`.

Begin the response with:

```text
Current task: reporting, limited to SAST evidence and approved local validation only.
```

Do not discover new findings. Do not change lifecycle states unless the evidence in the register contradicts the requested report. If contradiction exists, stop and create a reviewer note.

## Cost control

- Generate full human-readable reports only for `promoted-finding` and `promoted-chain` items.
- Summarise validation candidates, hardening items, duplicates, disconfirmed items, and out-of-scope items in registers rather than full reports.
- Consume the assessment register and evidence bundles instead of re-reading repositories.
- Reference stable IDs instead of restating repeated context.
- Avoid duplicating source snippets across artefacts unless required by the artefact format.
- Defer cross-report language polishing to the normalisation prompt.

## Evidence classification

Every report must include an **Evidence Classification** table using this exact shape:

| Field | Value |
|---|---|
| Source status | `<canonical label>` |
| Runtime status | `<canonical label>` |
| Reachability status | `<canonical label>` |
| Exploitability status | `<canonical label>` |
| Impact severity if exploitable | `<low/medium/high/critical or department scale>` |
| Evidence confidence | `<low/medium/high/confirmed>` |
| Lifecycle state | `<canonical state>` |

Every report must clearly separate:

- what source review established;
- what local validation established;
- what deployed or runtime validation has not established;
- what configuration or private-code assumptions remain;
- what would disconfirm the finding.

## Vulnerability disclosure status

Every report must include a **Vulnerability Disclosure Status** block near the top, before detailed impact narrative:

```markdown
## Vulnerability Disclosure Status

| Field | Value |
|---|---|
| Disclosure classification | `<source-confirmed / locally-validated / deployed-validated / plausible-chain / validation-candidate / disconfirmed>` |
| Evidence level | `<source-only / local-harness / authorised-deployed / owner-confirmed / not-established>` |
| Production impact | `<confirmed / not confirmed / not established / not applicable>` |
| Safe to say now | `<precise wording>` |
| Not safe to say | `<overclaims to avoid>` |
| External disclosure handling | `<internal only / coordinated disclosure required / owner validation first>` |
| Owner validation required | `<yes/no and evidence required>` |
```

Use wording such as:

- `source evidence shows`;
- `local validation showed`;
- `not established from current evidence`;
- `would require application or operator validation`;
- `if deployed reachability is confirmed`.

Avoid unless fully supported:

- `confirmed exploitable`;
- `live issue`;
- `production impact`;
- `attacker can`;
- `will allow`;
- `demonstrated`.

Prefer conditional wording where appropriate:

- `could allow, if the stated assumptions hold`;
- `may allow`;
- `source-supported path`;
- `hypothesis pending validation`.

If a finding came from a scanner feed, include tool; rule ID; scanner file and line; whether scanner dataflow matched the validated path; and what additional source review added. Do not describe the scanner result as proof.

At the end of this prompt, set every generated artefact's QA status to `pending-normalisation` and hand it to prompt 4. Do not mark a report `safe to share` until prompt 4 completes.

## Anti-drift check

Before finalising, confirm:

- narrative matches the evidence classification table;
- severity and confidence remain separate;
- full reports were produced only for promoted findings and chains;
- non-promoted candidates are represented in registers, not report prose;
- local validation is not overstated;
- all major claims have `file:line` evidence or are labelled inference;
- remediation matches the actual control gap;
- validation tasks are specific and safe;
- report counts match the register, canonical report folders, machine-readable artefacts, routed delivery packs, and reconciliation artefacts.

## Rules of engagement

- Generate full vulnerability reports only for candidates that meet the department's severity and reporting threshold recorded in the run scope and have passed the source, sink, path, control, consequence gates.
- Do not submit or package items below the department's reporting threshold through the vulnerability-report bundle. Record them as hardening, validation, duplicate, disconfirmed, or out-of-scope items in the reconciliation register.
- Every code-level claim in a report must have `file:line` evidence. If evidence is unavailable, mark the claim as not report-ready.
- Redact live secrets as `<REDACTED:purpose>` and avoid unnecessary personal data, git metadata, commit author names, and commit author email addresses.
- Include confirmation evidence and the mandatory negative-control assessment. If confirmation was not independently run, say so; do not synthesise confirmation output.
- Do not include PoCs against unauthorised live systems. Use safe local proof, unit-test or harness evidence, or a justified theoretical path.
- Completed reports must be placed only in an approved local or departmental reporting location and handed through the agreed departmental reporting route.

## Role

You are producing security reporting artefacts from an existing SAST and attack-path assessment.

Audience is mixed: senior decision-makers; platform and identity engineers; application engineering teams; security reviewers who will challenge assumptions.

Your job is to convert existing evidence into clear, defensible, reusable reporting outputs.

## Cross-prompt contract

Do not silently drop findings, chains, downgraded items, disconfirmed items, or attribution gaps. Every finalised item from prompt 2 must appear in the reconciliation register with one of:

- human-readable vulnerability report issued;
- chain report issued;
- SARIF issued;
- OPTRS issued;
- VXDF issued;
- VXDF explicitly not issued;
- downgraded;
- disconfirmed;
- duplicate;
- out of scope;
- awaiting owner or operator validation.

Use stable IDs from prompt 2. Do not invent new IDs unless resolving a collision, and record aliases if IDs change.

Do not use `UNKNOWN` as a filename or final owner bucket. For unresolved ownership, use routing confirmation IDs such as `RC001`, `RC002`, and produce owner-confirmation request files.

Every shareable pack must be usable without model access. Include a README, package manifest, validation report-back template, no-model continuation guide, machine-readable artefact guide, review log, and known limitations.

If the reporting pack is intended for remediation handover, produce owner or group-scoped delivery packs so individual teams can receive a complete subset without navigating the whole assessment.

## Required inputs from prompt 2

Require or reconstruct:

- stable IDs for repositories, findings, chains, remediation actions, validation actions, patterns, and artefacts;
- assessment register path or contents;
- finding IDs and chain IDs;
- repository names, URLs, attribution blocks;
- commit SHAs and branches;
- file paths and line ranges;
- function or method names;
- code snippets;
- source code status, confidence downgrade reasons, runtime behaviour status, configuration or identity-provider assumption status, overall exploitability status;
- OWASP and CWE mapping;
- qualitative CVSS, severity if validated, current evidence severity;
- blast radius;
- plain-English descriptions;
- unsafe patterns;
- remediation directions and remediation action register;
- validation requirements and validation action register;
- routing and attribution validation requirements;
- disconfirmation conditions;
- safe wording and claims-control entries;
- VXDF decisions;
- confirmation prompts and any available confirmation responses;
- negative-control challenge prompts and responses;
- report reconciliation draft.

If a required field is missing, preserve the gap explicitly. Do not invent it.

## Required outputs

Produce, per selected issue family or chain:

1. **SARIF JSON** for every source finding with `file:line` evidence.
2. **OPTRS JSON** for every vulnerability report and chain report.
3. **VXDF decision** — VXDF JSON only if the chain is validated, or a VXDF non-issuance note if validation is missing.
4. **Human-readable vulnerability report** in Markdown.

Also produce:

- one whole-exercise executive report;
- one machine-readable assessment register;
- one report reconciliation register mapping every finalised item to artefact(s) produced or an explicit reason;
- aggregate SARIF and aggregate OPTRS;
- routing index and owner-confirmation requests;
- engineering work item register with ServiceNow and Jira import CSVs;
- owner or group delivery folders containing scoped reports, work items, actions, routing material, and report-back templates;
- compressed delivery archives and checksums when packaging is requested;
- validation and remediation action registers;
- attacker-path validation plan, tracker, and theme playbooks;
- private-repository or private-configuration dependency map where load-bearing;
- no-finding or not-promoted coverage register;
- method-control review notes;
- no-model continuation guide;
- machine-readable artefact interpretation guide;
- final handoff file listing paths, checksums, evidence status, known limitations, and next actions.

Recommended naming:

```text
assessment-register.json
<issue-id>-<short-name>.sarif.json
<issue-id>-<short-name>.optrs.json
<issue-id>-<short-name>.vxdf.json
<issue-id>-<short-name>.vxdf-not-issued.md
<issue-id>-<short-name>-human-report.md
whole-exercise-executive-report.md
validation-report-back-template.md
validation-report-back-template.json
report-reconciliation-register.md
package-manifest.md
attacker-path-validation-plan.md
attacker-path-validation-tracker.md
routing/engineering-workpacks/engineering-work-items.json
routing/engineering-workpacks/servicenow-import.csv
routing/engineering-workpacks/jira-import.csv
routing/delivery-packs/<routing-id>-<owner-slug>/
routing/delivery-packs-archives/<routing-id>-<owner-slug>.tar.gz
routing/delivery-packs-archives/SHA256SUMS.txt
```

### Canonical folder completeness contract

Treat `assessment-register.json` as the single index for the final pack. Every registered final finding and chain must have exactly one canonical top-level human report path:

- findings: `vulnerability-reports/<issue-id>-<short-name>.md`;
- chains: `chain-reports/<chain-id>-<short-name>.md`.

Routed owner or group delivery-pack copies are scoped delivery copies, not substitutes for the canonical top-level files.

Counts to reconcile before finalising:

- registered final findings equals top-level `vulnerability-reports/*.md` count, except items explicitly non-issued or downgraded or disconfirmed in the reconciliation register;
- registered final chains equals top-level `chain-reports/*.md` count with the same exception;
- every registered finding has one per-issue SARIF unless the reconciliation register states why it was withheld;
- every registered finding and chain has one per-issue OPTRS unless withheld;
- aggregate SARIF and OPTRS rebuilt from the per-issue artefacts;
- every delivery-pack copy corresponds to a canonical top-level report and register entry;
- every delivery folder has a matching archive and the archive checksum manifest verifies;
- key-file checksum manifests are refreshed after any report, register, aggregate, or archive change.

## Evidence discipline

For every major finding, repository section, and chain section, explicitly label:

- Source code status: `CONFIRMED` / `NOT CONFIRMED`;
- Runtime behaviour: `VALIDATED` / `NOT VALIDATED`;
- Identity-provider, environment, or configuration assumptions: `VALIDATED` / `NOT VALIDATED` / `NOT APPLICABLE`;
- Overall exploitability: `CONFIRMED` / `HYPOTHESIS` / `DISCONFIRMED`.

Never imply validation unless runtime, configuration, or equivalent evidence exists. Static source evidence confirms code or design defects. It does not, by itself, confirm exploitability.

## Reachability, no-finding, and private-dependency outputs

For every final finding, include its `reachability_proof` in the human report, OPTRS where practical, engineering work item description, and validation action wording. The proof must name attacker class, entry point, delivery mechanism, sensitive sink or effect, and unvalidated conditions.

For every final finding and chain, include a plain-English feasible attacker outcome.

For every assessed repository with no promoted finding, produce or preserve a no-finding or not-promoted coverage record listing route groups and sinks reviewed, why candidates were rejected, and what evidence could change the decision.

Create a private-dependency validation map when any conclusion depends on private repositories, app configuration, tenant configuration, gateway rules, key vault, telemetry, pipeline state, identity-provider settings, queue or event wiring, or owner or operator evidence.

For each dependency include:

- linked finding or chain IDs;
- missing private or config source;
- why it is load-bearing;
- owner or team likely to provide it;
- safe validation question;
- PASS / FAIL / INCONCLUSIVE result meanings;
- severity impact.

For web, API, or admin findings, distinguish endpoint-level authorisation from CSRF, feature flags, route visibility, and global middleware. If a public route reaches a server-side write key, token, privileged role, or backend mutation API, state that trust-boundary crossing plainly in the reachability proof and validation tasks.

Include an endpoint authorisation table with route or handler, method, authentication control, endpoint-level authorisation or policy check, object or tenant-scope check, CSRF or session controls, feature flags, privileged backend sink, and validation still required.

## Finding versus chain discipline

Every source-level issue must appear as an individual finding. Every multi-step path, cross-repository sequence, or assumption-dependent exploit story must appear as a chain.

A finding can exist without a chain. A chain must cite one or more findings or explicit assumptions. Do not collapse chains into findings or findings into chains.

Stable IDs:

- Repository `R###`; Finding `R###-F###`; Chain `C###`; Validation `V###`; Remediation `A###`; Pattern `P###`; Artefact `ART###`.

## SARIF requirements

Use SARIF for source-level findings only. Generate one SARIF file per finding and one aggregate file for all findings.

Each SARIF result must include rule metadata, repository URL, commit SHA where known, file path, line range, finding ID, chain ID if applicable, CWE and OWASP tags, chain status, code status, and validation required.

If exact commit SHA or line range is missing, include that in result properties rather than fabricating it.

Each SARIF result must also include repository attribution in `properties`:

- `attribution.portfolio`, `attribution.service_line`, `attribution.product`;
- `attribution.source`, `attribution.confidence`, `attribution.gaps`.

Each SARIF result must also include stable repository ID, finding ID, pattern ID if applicable, evidence freshness, confidence downgrade reasons, remediation action IDs, validation action IDs, and claims-control summary.

Use `UNKNOWN` or `null` for missing values. Do not infer values from topics unless an approved mapping exists.

Validate JSON syntax before finalising.

## OPTRS requirements

Use OPTRS as the structured report container. Generate one OPTRS artefact per vulnerability report, one per chain report, and one aggregate OPTRS index.

The OPTRS artefact should include report metadata, evidence freshness, dates, scope, out-of-scope constraints, executive summary, affected assets, attribution and routing evidence, findings, source evidence, feasible attacker outcome, conditional severity, attack-chain status, validation required, remediation, and artefact references.

OPTRS must separate findings, chains, remediation actions, validation actions, common patterns, and claims-control statements.

Use status values such as `source-supported-code-defect`, `hypothesis-pending-validation`, `not-validated`, `validated`, `disconfirmed`.

Validate JSON syntax before finalising.

## Engineering work item requirements

Produce a routing-aware engineering work item layer in addition to human reports.

Create:

- `routing/engineering-workpacks/engineering-work-items.json`;
- `routing/engineering-workpacks/servicenow-import.csv`;
- `routing/engineering-workpacks/jira-import.csv`;
- one `routing/engineering-workpacks/<routing-id>-engineering-workpack.md` per routing owner or group.

Each work item must preserve: issue ID; stable routing ID; issue class; evidence and validation state; human summary; linked human report path; linked SARIF, OPTRS, VXDF decision artefacts; remediation and validation action IDs; claims-control wording; portfolio, service line, product, attribution source, confidence, and owner-confirmation requirement; ServiceNow-ready short description, description, urgency, impact, proposed priority, assignment hints, and acceptance criteria; Jira-ready summary, description, labels, components or team hints.

Do not collapse multiple routing owners into one generic bucket.

For multi-service, shared-substrate, repeated-pattern, or identity or data-composition findings, produce a coordinated remediation work package with a parent remediation action, child actions by owner or service, sequencing, migration risks, shared acceptance criteria, regression tests, and a consistency check.

## Owner delivery pack requirements

Produce a self-contained delivery folder for every routing owner or group.

Each folder under `routing/delivery-packs/<routing-id>-<owner-slug>/` must contain:

- `README.md`;
- `workpack.md`;
- `reports/`: copies of the relevant vulnerability and chain reports;
- `machine-readable/work-items.json`;
- `machine-readable/servicenow-import.csv` and `machine-readable/jira-import.csv`;
- `machine-readable/sarif/` and `machine-readable/optrs/` where per-issue artefacts exist;
- `actions/remediation-actions.md` and `.json`;
- `actions/validation-actions.md` and `.json`;
- `routing/`: owner-confirmation request or routing evidence;
- `templates/validation-report-back-template.md` and `.json`.

Also produce `routing/delivery-packs/README.md`.

When packaging is requested, create `routing/delivery-packs-archives/` with one `.tar.gz` archive per delivery folder and `SHA256SUMS.txt`.

## VXDF requirements

VXDF is only for validated exploitable chains or validated exploitable data flows.

Before issuing VXDF, require evidence that closes load-bearing assumptions: deployed or runtime validation, non-production reproduction, configuration evidence, confirmed secret exposure, or equivalent.

If validation is missing, do not produce VXDF as if the chain were validated. Instead, produce a `vxdf-not-issued.md` note containing issue ID, decision, reason, evidence state table, missing validation evidence, and trigger conditions that would allow VXDF issuance later.

VXDF non-issuance notes must include chain ID, linked finding IDs, validation action IDs still open, safe-to-claim and not-safe-to-claim wording, and attribution or routing status.

Validate JSON syntax for any VXDF JSON.

## Human-readable report requirements

Produce a Markdown vulnerability report intended for mixed audiences. It must be usable as a standalone report.

Immediately after the title, include:

```markdown
## Decision Snapshot
```

It must answer: what is confirmed; what is unvalidated; what could an attacker realistically achieve; what action is required now; what evidence would change severity.

Use explicit flags: `[CONFIRMED]`, `[NOT VALIDATED]`, `[HYPOTHESIS]`, `[REQUIRES INVESTIGATION]`. (Emoji equivalents may be used only if the receiving system supports them; ASCII forms are the default.)

Required report sections:

1. Executive Summary;
2. Evidence Status;
3. Attribution and Routing Summary;
4. Common Pattern, if issue repeats;
5. Separation of Confirmed Code or Design Defects and Hypothesised Attack Chain;
6. Cross-Service Summary Table;
7. Per-Repository Evidence Sections;
8. Hypothesised or Validated Attack Chain Section;
9. Conditional Severity;
10. Validation Requirement Table;
11. Remediation Accountability Table;
12. Validation Action Table;
13. Engineering Checklist;
14. Claims Control;
15. Safe Wording for Downstream Reporting;
16. Coding-Agent or Reviewer Confirmation;
17. Final Decision Guidance;
18. Attacker-Path Validation Handoff.

For each affected service include repository, URL, commit SHA or explicit gap, branch, assessment date, attribution fields, attribution source and confidence, attribution gaps, file path, line range, function or method, code snippet, evidence statuses, unsafe pattern, remediation direction, and validations required.

The report must stand alone. Do not rely on external references for essential facts.

### Human summary requirements

Immediately after the decision snapshot, include a `Human Summary` section explaining in plain English: what the issue is; how an attacker could reach or influence the vulnerable path; what the attacker could realistically achieve if preconditions hold; what local, source, and runtime validation did and did not prove; what owner, runtime, private, identity-provider, cloud, pipeline, telemetry, or deployed-route evidence is still needed; how the conclusion changes if validation passes or fails.

Do not rely on labels like High, Critical, IDOR, SSRF, token issue, or missing authorisation to carry the impact. State the practical consequence directly.

### Self-contained evidence requirements

Each report must include: repository URL; exact commit SHA or explicit gap; branch or default branch; assessment date and retrieval date; file path and line range; function, class, or method name; relevant code excerpts for entrypoint, source, sink, and path; test or fixture evidence, or explicit absence within the depth budget; static PoC or contained demonstration; affected attribution and routing details; validation requirements that cannot be completed in the static-only scope; remediation direction specific to the unsafe pattern; what would raise, lower, validate, or disconfirm severity; a validation handoff enabling an engineer to prove or disprove external reachability without returning to the model; and a local validation summary if local tests, compiled checks, request or controller tests, harnesses, or shell checks were run.

Do not write "see chain report", "see prompt 2", or "see previous file" for essential facts.

### Attacker-path validation handoff requirements

For every promoted finding, include what would make the issue externally reachable; exact negative tests or reviews required; expected secure result; evidence that would confirm exploitability; evidence that would disconfirm exploitability; whether validation can be performed locally, in non-production, by config review, or only by an authorised operator; severity if validation passes; severity if validation fails; safe wording before validation.

### Confirmation section requirements

For every material report, include a section titled `Coding-Agent or Reviewer Confirmation` with confirmation tool or reviewer type; model and version if used; source-to-sink trace prompt and response; sanitisation, authorisation, or validation check prompt and response; reachability or configuration-gate prompt and response; negative-control prompt and response; submitter assessment after the negative control.

If no independent confirmation was run, state that explicitly and include the unrun prompts as required next steps. Do not fabricate transcripts.

### Attribution and routing summary requirements

Include a table:

| Repository | Portfolio | Service line | Product | Attribution source | Routing confidence | Gaps / validation required |
|---|---|---|---|---|---|---|

Rules:

- Use high-confidence repository custom properties or the department's attribution registry where present.
- Use active IaC or deployment tags only as medium-confidence fallback.
- Keep unresolved variables, commented tags, topics, README, docs, and CODEOWNERS as candidate or routing signals unless independently validated.
- Do not treat topics as portfolio, product, or service-line values unless an approved mapping exists.
- If attribution is missing, say `UNKNOWN` and include a required owner-confirmation task.

Routing confidence:

| Routing confidence | Use |
|---|---|
| High | Route directly to the stated portfolio, product, or service line. |
| Medium | Route with explicit owner confirmation. |
| Low | Do not assign final ownership; use for triage only and request confirmation. |

## Common pattern consolidation

Define the pattern once with a stable pattern ID. Explain the weakness and remediation once. Provide a per-repository evidence table with stable repository ID, finding ID, commit, file, line, and nuance. List per-repository exceptions separately.

## Remediation and validation separation

Every issue report and the whole-exercise report must contain separate tables:

### Remediation actions

| Action ID | Owner | Action | Priority | Linked findings or chains | Depends on validation? |
|---|---|---|---|---|---|

### Validation actions

| Validation ID | Owner | Action | Required evidence | Result format | Linked findings or chains |
|---|---|---|---|---|---|

A remediation section must be more than a one-line direction for high-impact or shared findings. Include owner or owner-confirmation task, target secure state, implementation steps, migration or backwards-compatibility notes, acceptance criteria, regression tests (including negative tests), telemetry or logging safety requirements, validation evidence expected after the fix, and rollback or staged rollout notes.

For each validation action, specify who is likely able to perform it (application engineers, platform engineers, identity administrators, cloud operators, SOC, or scoped penetration testers), whether it requires live-system access, whether it can be performed safely in non-production, and evidence that must be returned to close the uncertainty.

## Claims control

Every report must include:

| Claim | Status | Reason |
|---|---|---|
| Safe to claim now | Allowed | Directly supported by source or validated evidence. |
| Not safe to claim | Blocked | Would overstate evidence. |
| Safe only if validated | Conditional | Requires named validation evidence. |

## Whole-exercise executive report

Filename: `whole-exercise-executive-report.md`.

Required sections:

1. Decision Snapshot;
2. Executive Summary;
3. Scope and Method;
4. Repository and Attribution Coverage;
5. Findings Overview;
6. Attack Chain Overview;
7. Reporting Reconciliation;
8. Top Risks;
9. Systemic Themes;
10. Remediation Programme;
11. Validation Plan (including attacker-path validation themes);
12. Safe Wording;
13. Claims Control;
14. Final Decision Guidance.

Keep it concise enough for senior readers but specific enough to stand alone. Clearly separate confirmed source defects from unvalidated chains. Include repository attribution coverage and routing gaps. Include stable IDs and reference the assessment register. Refer to individual reports as supporting artefacts, but do not rely on them for core conclusions. Do not claim production exploitability unless validation evidence exists.

## Assessment register requirements

Produce `assessment-register.json` or `assessment-register.yaml` including generated timestamp, scope summary, repositories, findings, chains, common patterns, remediation and validation action registers, artefact inventory, and gaps.

## Report reconciliation register

Produce `report-reconciliation-register.md` covering every finalised finding and chain, including lower-severity, conditional, downgraded, disconfirmed, duplicate, and out-of-scope items.

Columns:

| ID | Type | Title | Evidence state | Current severity | Conditional severity | Artefact(s) produced | Reporting decision | Reason | Open validation |
|---|---|---|---|---|---|---|---|---|---|

Allowed reporting decisions:

- `standalone-human-report`;
- `combined-human-report`;
- `SARIF-only`;
- `OPTRS-only`;
- `VXDF-issued`;
- `VXDF-withheld-pending-validation`;
- `executive-summary-only`;
- `downgraded`;
- `disconfirmed`;
- `duplicate`;
- `out-of-scope`;
- `awaiting-validation`.

This register is for traceability, not a severity submission gate.

## Validation report-back template

Produce both `validation-report-back-template.md` and `validation-report-back-template.json` capturing validation ID, linked finding and chain IDs, team or service, confirmed portfolio, service line, product, owning engineering team, repo URL, commit tested, branch, environment, tester, date, validation performed, evidence captured, result (PASS, FAIL, INCONCLUSIVE), remediation status, residual risk, suggested classification update, whether attribution properties were corrected or populated, and whether IaC tag values differ from repository attribution and why.

## Attacker-path validation pack

Produce:

```text
validation/attacker-path-validation-plan.md
validation/attacker-path-validation-tracker.md
validation/attacker-path-playbooks/01-auth-authz-route-reachability.md
validation/attacker-path-playbooks/02-object-ownership-negative-tests.md
validation/attacker-path-playbooks/03-token-secret-log-state-exposure.md
validation/attacker-path-playbooks/04-cicd-trust-permission-review.md
validation/attacker-path-playbooks/05-identity-account-linking-validation.md
```

Each playbook must include objective; linked finding or chain IDs; validation IDs; owner type; required tests or reviews; minimum test cases; evidence to capture; PASS / FAIL / INCONCLUSIVE meanings; severity impact; feasible attacker outcome if validation passes; residual or source-only impact if validation fails; and safe wording after validation.

## Language safety rules

Use careful language for unvalidated behaviour: could, may, would if, depends on, requires validation, not demonstrated, not observed, source-supported, hypothesised.

Do not use absolute exploitability language unless validation exists. Do not say confirmed account takeover, confirmed exploitable, internet-realisable, allows attacker to, tenant configuration is unsafe, token has leaked, or production is vulnerable unless evidence proves it.

For unvalidated chains, use:

```text
The source-level defect is confirmed. End-to-end exploitability remains a hypothesis pending <specific missing evidence>.
```

## Severity handling

Severity must be conditional. State severity if the bootstrap or missing condition is validated; severity if the missing condition is disconfirmed; why remediation should proceed regardless.

Do not suppress a finding solely because its current evidence severity is below a programme submission threshold. If a downstream template has a threshold, record the item in the reconciliation register with the reason it was not submitted through that template.

## Safe wording section

Include `## Safe Wording for Downstream Reporting` stating what must not be claimed yet, safe wording before validation, how conclusions change if validation passes, how conclusions change if validation fails, and what wording to use if the issue becomes validated.

## Packaging and quality checks

Before final response, run or document:

- final vulnerability and chain reports include the required structure and human summary;
- every final finding has a reachability proof or an explicit reason why the proof is validation-blocked;
- every report explains the feasible attacker outcome in plain English;
- local validation summaries state what was run, what passed or failed, what that proves, and what remains unvalidated;
- no-finding or not-promoted coverage records exist;
- private-dependency validation map exists where load-bearing;
- method-control review notes are present when method improvements were requested;
- every final finding and chain appears in the engineering work item register, except explicitly non-issued or downgraded or disconfirmed items;
- every registered final finding has a canonical top-level report, and register report paths resolve to existing files;
- SARIF and OPTRS coverage is complete unless explicitly withheld;
- aggregate SARIF and OPTRS agree with the register;
- every routing owner or group has a delivery folder with README, workpack, scoped reports, work items, ServiceNow CSV, Jira CSV, validation and remediation extracts, and report-back templates;
- delivery archives exist when packaging is requested, and `SHA256SUMS.txt` verifies;
- unresolved work markers, draft markers, paste markers, angle-bracket template markers, and unchecked checklist items are cleared;
- accidental secret or token patterns and personal-data patterns are absent;
- JSON syntax validation for SARIF, OPTRS, VXDF, register, and report-back JSON;
- archive contents list if packaging is requested.

Produce `package-manifest.md` listing generated files, JSON validation status, quality checks run and results, known gaps, files intentionally excluded, and archive filename and SHA256 checksum if applicable.

Do not package cloned target repositories, local token files, raw model scratch logs, or secret-bearing working files unless explicitly required and reviewed.

## Final response

After producing files, summarise files created; which artefacts are validated JSON; whether the assessment register was produced; whether VXDF was issued or withheld; whether the whole-exercise executive report was produced; whether validation report-back templates were produced; whether a report reconciliation register was produced; whether quality checks and packaging were performed; archive filename and SHA256 checksum if applicable; main evidence gaps preserved; any schema validation not performed; and recommended next phase.

State whether the next recommended phase is further broad SAST, targeted pattern hunting, attacker-path validation, remediation support, or stop and await owner feedback.
