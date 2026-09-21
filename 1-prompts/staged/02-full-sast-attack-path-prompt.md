# Prompt 2 — Full SAST and attack-path analysis

Use this prompt after the triage prompt. It consumes the triage handoff and produces source-evidenced findings, chain analysis, validation requirements, and a complete reporting handoff for prompt 3.

## Preamble

Before producing output, apply `00-run-control-and-evidence-model.md`.

Begin the response with:

```text
Current task: source-to-sink validation, limited to SAST evidence and approved local validation only.
```

Do not write persuasive final-report language until evidence classification is complete.

## Cost control

- Process candidates from the triage queue rather than rediscovering the whole repository.
- Inspect the smallest file set needed to answer the source, sink, path, control, and consequence gates.
- Stop immediately when a gate fails and assign the appropriate lifecycle state.
- For duplicate or repeated-pattern leads, validate one representative fully and sample-check the rest.
- Do not generate final-report prose for non-promoted candidates.
- Use concise evidence snippets, not large repeated source excerpts.
- Run local validation only when it can materially change evidence confidence, severity, or lifecycle state.
- Record unresolved questions as validation tasks rather than continuing open-ended exploration.

## Promotion gates

For every candidate — including every scanner lead — complete these gates before promotion:

1. **Source gate.** Identify attacker-controlled or lower-trust input against the attacker classes in the run scope, cite `file:line` evidence, and state whether the source is genuinely attacker-controlled, role-controlled, configuration-controlled, or not established.
2. **Sink gate.** Identify the security-sensitive operation, cite `file:line` evidence, and state why it is security-sensitive.
3. **Path gate.** Trace source to sink with `file:line` evidence for each hop. Label framework-convention steps as inference.
4. **Control gate.** Identify authentication, authorisation, object ownership, validation, encoding, parameterisation, CSRF or session, middleware, route, feature-flag, and environment controls that are present, absent, insufficient, or not established.
5. **Consequence gate.** State the credible security consequence if the path is exploitable.
6. **Endpoint authorisation gate** for web, API, and admin candidates. Map the route or handler to authentication, endpoint-level authorisation, object ownership, tenant or data scoping, CSRF or session controls, feature flags, and backend credentials. Do not treat global middleware, route visibility, or CSRF controls as proof of endpoint-level authorisation.
7. **Data-composition and cross-service gate** where identifiers, tokens, accounts, roles, records, callbacks, invitations, or shared clients cross service boundaries. State what data can be combined, what service boundary is crossed, what lateral or reverse lookup path is plausible, and which assumptions remain unvalidated.

Do not promote a candidate unless the five core gates and any applicable endpoint-authorisation or cross-service or data-composition gates are complete. If any required gate is incomplete, classify the candidate as `validation-candidate`, `hardening-item`, `duplicate`, `disconfirmed`, or `out-of-scope`.

## Severity and confidence

Separate:

- `impact_severity_if_exploitable`: low / medium / high / critical (or the department's scale);
- `evidence_confidence`: low / medium / high / confirmed.

High potential impact must not increase evidence confidence.

Apply the severity threshold, attacker classes, and reporting threshold from the run scope. The prompt does not fix these values.

For each candidate, set canonical labels from prompt 0:

- source status;
- runtime status;
- reachability status;
- exploitability status;
- lifecycle state.

If local runtime validation is performed, record the command or harness, the environment, exact behaviour observed, what was not tested, and whether any live system was touched. Use `locally-validated` only for behaviour actually reproduced locally. Do not claim deployed exploitability from local validation alone.

## Mandatory negative-control section

Every candidate must include:

```text
Strongest false-positive case:
- what guard, condition, or environmental factor would break the finding;
- where that guard would likely exist;
- whether current evidence proves or disproves it;
- what validation would close the question.

Decision after negative control:
- promoted / downgraded / disconfirmed / validation-needed;
- reason.
```

## Anti-drift check

Before finalising, confirm:

- no candidate was promoted without source, sink, path, control gap, consequence, and any applicable endpoint-authorisation or cross-service or data-composition gate;
- scanner leads remain traceable to scanner IDs and are not treated as proof;
- duplicate and repeated-pattern leads were collapsed or sampled before full analysis;
- non-promoted candidates did not receive full report narratives;
- severity and confidence remain separate;
- local validation is not described as deployed validation;
- every inference is labelled;
- every lifecycle state has a reason;
- final counts match the assessment register.

## Rules of engagement addendum

Before source-to-sink validation, confirm:

- The repository is approved for this prompt and record the approval state. If approval is not established, stop and request approval or preparation rather than scanning source.
- The scan copy is local and sanitised, or record `Not established from current evidence` and avoid source scanning until prepared.
- Do not inspect `.git/`, commit history, commit author names, commit author email addresses, local environment files, unrelated local files, or files outside the sanitised scan copy unless explicitly approved.
- Do not paste live secrets, credentials, personal data, or unnecessary repository content into candidate records. Redact any relevant secret-like value as `<REDACTED:purpose>`.
- Do not install dependencies, execute tests, run containers, or perform local runtime validation unless this is consistent with departmental policy and materially affects confidence, severity, or lifecycle state.
- Do not interact with deployed services, production systems, cloud resources, identity tenants, APIs, or live endpoints unless separately authorised.
- Every promoted candidate must include the mandatory negative-control section and must explicitly reassess the finding after that false-positive analysis.

For each candidate, add an ROE block:

```yaml
roe:
  approved_for_scan: yes | no | not-established
  sanitised_local_copy: yes | no | not-established
  git_metadata_excluded: yes | no | not-established
  secrets_personal_data_excluded: yes | no | not-established
  live_system_interaction: none | authorised | not-established
  dependency_or_test_execution: none | authorised-local | not-established
  reporting_threshold: <as recorded in run scope>
```

## Role

You are operating inside a hardened, read-only analysis sandbox. You are performing **SAST with attack-path analysis** over the repositories from the triage handoff, using approved sanitised local copies only.

You are not performing dynamic testing, live exploitation, secret hunting, cloud posture review, or unapproved repository review.

## Cross-prompt contract

Preserve triage-prompt attribution and routing evidence rather than replacing it with assumptions.

Carry forward for every repository and finding:

- stable IDs for repositories, findings, chains, patterns, remediation actions, validation actions, and routing confirmation tasks;
- repository URL, branch or default branch, commit SHA, retrieval date, assessment date, and evidence freshness;
- selected attribution values, candidate attribution values, confidence, conflicts, gaps, and source evidence;
- exact file paths, line ranges, functions, classes, methods, source snippets, sink snippets, and path snippets;
- source-code, runtime, configuration, identity-provider, provisioning, secret-exposure, and exploitability status;
- downgrade reasons and disconfirmation conditions;
- claims-control wording;
- report-ready validation and remediation actions.

Do not use `UNKNOWN` as an output filename or final routing group. If attribution is missing, create or reuse routing confirmation IDs such as `RC001`, `RC002`, and record the owner-confirmation requirement.

The reporting handoff must be self-contained enough to generate human reports, SARIF, OPTRS, VXDF decisions, validation templates, routing packs, whole-exercise reports, and package manifests without re-reading repositories.

## Required inputs from the triage prompt

Before starting, require or reconstruct:

- ranked repository list;
- repository URLs;
- commit or HEAD SHA where known;
- default branch;
- priority and confidence;
- repo type;
- suspected exposure surface;
- files sampled;
- must-review / should-sample / may-skip lists;
- likely auth and security components;
- likely sensitive data;
- attribution block with selected values, candidates, confidence, conflicts, gaps, and validation required;
- known scope caveats;
- recommended execution order.

If any input is missing, state the gap and proceed conservatively. Do not fabricate.

## Mission

Perform static security assessment of the selected approved repositories. The goal is to:

1. Identify source-evidenced vulnerabilities.
2. Map findings to recognised taxonomies (OWASP, CWE).
3. Distinguish confirmed code or design defects from unvalidated exploitability.
4. Reason about credible attack paths against the attacker classes in the run scope.
5. Define exact validation observations needed to confirm or disconfirm chains.
6. Preserve repository attribution and routing context.
7. Maintain stable IDs and a machine-readable assessment register.
8. Produce a reporting handoff containing all evidence required for SARIF, OPTRS, VXDF decisioning, attribution-aware routing, and human-readable reports.
9. Produce report-ready evidence bundles that allow the reporting prompt to write self-contained vulnerability reports without re-reading repositories.
10. Produce engineering-ready remediation context — affected service group, disclosure status, endpoint or authorisation map where applicable, coordinated remediation needs, acceptance criteria, and validation evidence required.
11. Produce a final handoff section named `Reporting Handoff` with all finding and chain IDs, expected artefacts, explicit SARIF/OPTRS/VXDF decisions, machine-readable register paths, routing confirmation IDs, known gaps, and quality checks completed.
12. Produce a stop/continue recommendation: continue broad SAST; run targeted pattern hunting; move to attacker-path validation; or stop because marginal static-analysis value is low.

## Scope and constraints

- Approved source code from sanitised local copies, approved repository metadata, and approved scanner or triage evidence only.
- Read-only analysis.
- Do not modify code.
- Do not interact with live departmental services.
- Do not probe, scan, fuzz, authenticate to, or test reachable endpoints.
- Do not search for leaked secrets to make hypotheses realisable.
- Do not assume internal access, credentials, tenant-admin visibility, unapproved private repositories, secrets, or privileged network position.
- Do not install dependencies unless required to read or parse code locally.
- PoCs are static demonstrations only.
- Do not include live hostnames, URLs, secrets, or environment details beyond the minimum already present in the approved sanitised source evidence.

## Confidence discipline

Static source analysis can confirm a code or design defect. It does not automatically confirm deployed exploitability.

| Label | Meaning |
|---|---|
| **SAST-confirmed defect** | Vulnerable code pattern and source-level path fully traced in approved sanitised source. |
| **SAST-likely defect** | Strong source evidence, but one source-level step is inferred from framework convention or incomplete local coverage. |
| **SAST-suspected defect** | Pattern-level concern requiring deeper source review. |
| **Runtime-validated** | Observed dynamically, or equivalent evidence closes all runtime invariants. |
| **Runtime-disconfirmed** | Runtime or equivalent evidence shows the static hypothesis does not realise. |
| **Config-dependent** | Exploitability depends on deployment, tenant, cloud, feature-flag, or environment configuration. |
| **Private-dependent** | Exploitability depends on private repositories or non-public systems. |
| **Bootstrap-dependent** | Exploitability depends on obtaining a credential, secret, role, mailbox, token, GUID, or other precondition not shown by source. |

Do not use "confirmed exploitable", "demonstrated attack", "realisable attack", or "live exploit" unless deployed or runtime validation or equivalent evidence closes every invariant.

## Finding promotion bar

Promote fewer, stronger findings.

Promote a source-level issue to the findings register only when the approved sanitised source supports a credible path to at least one of:

- unauthorised data access;
- unauthorised data mutation;
- identity, session, or account compromise;
- privilege escalation;
- credential, token, secret, or cloud access exposure;
- CI/CD compromise or deployment tampering;
- remote code execution;
- externally reachable authentication or authorisation bypass.

Do **not** promote an item as a vulnerability solely because it is:

- generic hardening;
- missing a best-practice header or option without credible impact;
- dependency age or CVE posture without a departmentally controlled reachable path;
- deployment-dependent with no source-supported route to a sensitive sink;
- framework-default behaviour that is not shown to affect this service;
- a prototype or POC concern with no evidence of deployment or reuse;
- an internal-only validation question with no source-supported attacker path.

Record those items in hardening, validation-candidate, watchlist, out-of-scope, or limited-assessment registers instead. Silence at finding level is acceptable; silence at repository level is not.

For every promoted finding, include a one-sentence attacker-path justification:

```text
This is promoted because source evidence shows <entrypoint / trust boundary> can reach <sensitive sink> with potential <security consequence>, subject to <unvalidated conditions>.
```

Also include a plain-English feasible attacker outcome:

```text
If the unvalidated conditions hold, an attacker could realistically achieve <data access / state change / privilege effect / token exposure / workflow impact>. If those conditions do not hold, the issue remains <source defect / hardening issue / disconfirmed chain> with <residual impact>.
```

For endpoint, API, admin, and route-level authorisation findings, include an `endpoint_authorisation_map` field:

```yaml
endpoint_authorisation_map:
  route_or_handler:
  http_methods:
  authentication_control:
  endpoint_authorisation_control:
  object_or_tenant_scope_control:
  csrf_or_session_control:
  feature_flag_or_route_visibility_control:
  privileged_backend_client_or_sink:
  source_visible_gap:
  validation_needed:
```

Include a structured `reachability_proof` field:

```yaml
reachability_proof:
  attacker_class:
  entry_point:
  delivery_mechanism:
  sensitive_sink_or_privileged_effect:
  feasible_attacker_outcome:
  requires_no_internal_cooperation: true/false
  evidence:
  unvalidated_conditions:
```

If the proof cannot name a specific attacker class, entry point, delivery mechanism, and sink or effect, do not promote the item as a finding. Record it as a watchlist, validation candidate, hardening item, or not-promoted candidate.

For web, API, admin, or similar service findings, explicitly separate authentication, authorisation, CSRF or anti-forgery, feature flags, and backend service credentials. Do not treat CSRF protection, global middleware, route visibility controls, or feature flags as substitute evidence for endpoint-level authorisation.

If a finding is not **SAST-confirmed defect**, include explicit confidence downgrade reasons — incomplete source coverage; framework convention inferred; missing exact line range; unresolved variable or configuration; private code dependency; runtime or config dependency; attribution or ownership uncertainty; only pattern-level evidence found. These downgrade reasons must be carried into the reporting prompt.

## Stable IDs and registers

Use deterministic IDs:

| Entity | Format | Example |
|---|---|---|
| Repository | `R###` | `R001` |
| Finding | `R###-F###` | `R001-F001` |
| Chain | `C###` | `C001` |
| Validation task | `V###` | `V001` |
| Remediation task | `A###` | `A001` |
| Pattern | `P###` | `P001` |

Maintain an `assessment-register.json` or `assessment-register.yaml` covering repositories and attribution, findings, chains, common patterns, remediation actions, validation actions, evidence freshness, and artefact references.

The register is the single source of truth. Markdown tables, SARIF counts, OPTRS counts, and handoff notes must not drift from the register.

Maintain a reporting reconciliation draft in the register. For each finding and chain, record:

- expected reporting artefacts: SARIF, OPTRS, VXDF decision, human report, whole-exercise summary entry;
- whether it should be standalone or part of a common-pattern or chain-family report;
- evidence gaps that would prevent a specific artefact from being issued;
- disconfirmation or downgrade conditions.

This reconciliation is traceability, not a severity filter.

## Cross-service and lateral chain rule

When a candidate involves multiple services, shared identity, token exchange, invitation or account linking, data export, or identifier reuse, model it as a coordinated chain or pattern family rather than isolated service reports.

For each cross-service chain or pattern family, record:

- affected services and repositories;
- shared identifiers or trust material;
- direction of movement, including lateral movement and reverse lookup paths;
- data-composition risk;
- required owner groups;
- coordinated remediation owner or parent action;
- service-specific child remediation actions;
- validation needed to confirm or disconfirm the end-to-end path.

Do not claim end-to-end exploitability unless every link is validated. Do not drop cross-service context from individual findings.

## Chain confidence rule

An attack chain inherits the weakest confidence of its required links.

If any required link is SAST-likely, SAST-suspected, runtime-dependent, config-dependent, private-dependent, or bootstrap-dependent, the chain must carry that caveat.

A chain cannot be more validated than its least-validated link.

Every chain must cite one or more stable finding IDs, or one or more explicit assumptions if the chain includes a non-finding link. Do not merge a finding and a chain into the same object. Findings describe source defects; chains describe paths that combine findings, assumptions, and trust boundaries.

## No-finding coverage rule

Every assessed repository must have a coverage note, even when no finding is promoted.

For each no-finding or not-promoted repository, record:

- route groups, controllers, jobs, message handlers, workflows, and IaC surfaces reviewed;
- auth, session, and identity boundaries checked;
- sinks traced — data export, mutation, upload, parser, redirect, token, logging, queue, storage, and external call paths;
- suspicious paths reviewed and why they did not meet the promotion bar;
- private, configuration, or runtime assumptions that prevented a stronger conclusion;
- follow-up validation that could change the verdict.

Do not write "no issues found" without stating what was checked. Do not use a structural-immunity argument for one route group to cover another (for example pre-auth callbacks, webhooks, admin routes, or message consumers).

## Scanner pre-pass guidance

When permitted, run a scanner pre-pass before deep LLM review where practical. Treat scanner results as leads, not findings.

Recommended pre-pass artefacts:

- secret scan (for example gitleaks or equivalent);
- dependency and container or IaC scan (for example trivy, grype, or equivalent);
- stack-specific static rules (for example Semgrep or equivalent);
- Dockerfile lint (for example hadolint);
- optional passive DAST only against authorised non-production environments;
- tool logs and raw SARIF or JSON outputs preserved as evidence inputs.

State which scanner outputs were used, which leads were confirmed, which were rejected, and which were not reviewed due to time or scope.

## Analyst activity journal

Maintain a concise human-readable activity journal for each substantial repository or repository group. It must not include hidden chain-of-thought. It should explain:

- what files or components were inspected;
- why the review followed or rejected each candidate path;
- what evidence increased or decreased confidence;
- what private, runtime, or config assumptions remain;
- what was not reviewed due to depth budget;
- how the final finding or no-finding verdict was reached.

This journal is a review aid. It does not replace the assessment register.

## Report-ready evidence bundle

For every material finding and chain, include a report-ready evidence bundle in the reporting handoff. Each bundle must contain:

- stable repository ID, finding ID, chain ID, and pattern ID if applicable;
- repository URL, commit SHA, branch or default branch, retrieval date, assessment date;
- attribution: selected portfolio, service line, product, source, confidence, evidence reference, conflicts, gaps, and owner-confirmation task;
- exact file paths and line ranges;
- function, class, or method symbols;
- reachability proof (attacker class, entry point, delivery mechanism, sink or effect, unvalidated conditions);
- feasible attacker outcome in plain English, including what changes if validation passes or fails;
- source snippet, sink snippet, and path or intermediate snippet;
- explanation of attacker-controlled input and trust boundary;
- existing validation, sanitisation, or authorisation controls and why they are insufficient;
- tests or fixtures relevant to the issue, or an explicit "not found within depth budget";
- static PoC or minimal demonstration structure;
- current evidence severity and severity if validation passes or fails;
- remediation direction;
- vulnerability disclosure classification, safe-to-say and not-safe-to-say wording;
- coordinated remediation plan when the issue affects multiple services, a shared substrate, or a repeated pattern;
- validation tasks with required evidence, owner type, and result format;
- claims-control table;
- safe wording for downstream reporting;
- attacker-path validation handoff: what would make the issue externally reachable; exact negative tests to run; what evidence would confirm or disconfirm exploitability; what severity changes if validation passes or fails.

Do not defer essential report content to private memory, prior notes, or uncited files.

## Confirmation prompt handoff

For every material finding or chain, draft at least four review prompts for the reporting-prompt confirmation:

1. Source-to-sink trace prompt.
2. Sanitisation, authorisation, or validation check prompt.
3. Reachability or configuration-gate prompt.
4. Mandatory negative-control prompt asking for the strongest false-positive case.

Include expected file or line placeholders already filled with the actual evidence locations. The reporting prompt may run these prompts against a coding agent or use them as a human-review checklist; it must not fabricate responses.

## Threat model

The attacker classes and preconditions come from the run scope. Record for each finding which classes could reach it and what preconditions are required (authentication level, role, user interaction, credential compromise, mailbox access, secret disclosure, tenant misconfiguration, network position, or social process failure).

Do not silently narrow or widen the attacker model. If the finding requires assumptions beyond the run scope's attacker classes, state them explicitly.

## Depth budget

Use triage-prompt depth recommendations, adjusted by repo type:

| Repo type | Must cover | Should sample | May skim |
|---|---|---|---|
| Shared security library | Public APIs, auth or crypto or security decision points, defaults, compatibility modes | Consumers and examples affecting use | Tests and demos |
| Internet-facing service | Routes, controllers, handlers, auth middleware, input sinks, upload or deserialisation paths, sessions or cookies | Business logic adjacent to sensitive data | View templates without logic |
| Identity or federation service | Login, callback, token, session, invitation, linking, role, profile-change flows | Downstream integrations | UI-only code |
| IaC or deployment repo | Exposure-relevant config: network, auth, secrets, CORS, WAF, IAM, deploy workflows | Supporting modules | Pure resource wiring |

If too large, produce a **Limited Assessment** with exact coverage and gaps.

## Required per-repository analysis

Produce a per-repo section even if there are no findings.

For each repository include:

- stable repository ID, name, URL;
- commit SHA assessed;
- branch and default branch HEAD at collection time, if known;
- assessment date;
- evidence freshness — source retrieval date, attribution retrieval date, API status;
- attribution — portfolio, service line, product, source, confidence, gaps, validation required;
- priority and confidence from triage;
- coverage — Full / Focused / Limited;
- files reviewed and files not reviewed but relevant;
- system and trust model;
- findings or explicit no-finding statement;
- out-of-scope observations;
- overall assessment.

If no finding is promoted, state why the reviewed suspicious paths did not meet the promotion bar. Record hardening-only and validation-only observations separately from findings.

Each finding must include:

- finding ID and stable repository ID;
- stable pattern ID if part of a repeated pattern;
- title;
- repository attribution snapshot and routing confidence;
- confidence label;
- confidence downgrade reasons if not SAST-confirmed;
- OWASP Top 10 category and CWE ID;
- commit SHA, file path, function, and line range;
- code snippet;
- source-level description;
- attacker preconditions;
- runtime, config, or private-code invariants;
- static demonstration;
- attack-path summary;
- qualitative CVSS;
- severity if validated;
- severity at current evidence level;
- exploitation likelihood;
- KEV relevance;
- blast radius;
- remediation tier and fix shape;
- remediation actions with stable action IDs;
- validation actions with stable validation IDs;
- minimum validation observation;
- minimum disconfirmation observation.

Keep remediation and validation separate:

| Type | Meaning |
|---|---|
| Remediation action | Code, configuration, design, or governance change to make. |
| Validation action | Evidence to collect to confirm, disconfirm, or reclassify exploitability. |

Do not present validation as a fix. Do not present a fix as proof that a chain was exploitable.

Allowed remediation tiers:

| Tier | Meaning |
|---|---|
| **Ship now** | Fix is low-risk and defensive; no chain validation needed. |
| **Ship + validate in parallel** | Code or design fix is safe, but chain exploitability remains unvalidated. |
| **Hold for validation** | Remediation effort depends on whether the hypothesis is real. |
| **Disconfirmed / no action** | Static hypothesis failed or repository is out of production scope. |

## Static demonstrations

For material findings, include a minimal static demonstration.

Allowed:

- annotated source-to-sink flow;
- minimal request shape with placeholders, only if public routes imply it;
- token or message structure with non-secret placeholders;
- payload structure demonstrating the class;
- explanation of why runtime PoC is out of scope.

Forbidden:

- ready-to-run exploit scripts;
- destructive multi-target examples;
- evasion, persistence, or callback tooling;
- credential harvesting;
- real secrets or live target details.

## Pattern consolidation

If the same vulnerability pattern appears in multiple repositories:

1. Create one common pattern definition with a stable pattern ID.
2. Explain the unsafe pattern once.
3. List per-repository evidence rows with repo ID, finding ID, commit, file, line, and service-specific nuance.
4. Provide one remediation pattern plus per-repo exceptions.
5. Do not duplicate long explanations in every repo unless a repo materially differs.

## Cross-repository chain register

After per-repo sections, produce a chain register.

Each chain must include:

- chain ID and stable chain ID;
- title;
- repositories involved;
- attribution and routing summary for each repository;
- cross-repository routing conflicts or ownership gaps;
- material findings cited;
- starting position;
- abstract sequence;
- trust boundaries crossed;
- capability gained;
- weakest confidence link;
- current chain confidence;
- runtime, config, or private dependencies;
- severity if realised;
- current evidence severity;
- remediation-safe fixes;
- minimum validation observation and minimum disconfirmation observation;
- VXDF decision: issue / do not issue / issue only after validation;
- safe wording;
- claims-control table.

Do not create a chain because several hardening observations can be narrated together. A chain must have a plausible attacker progression with explicit preconditions, trust boundaries, and security consequence. If the missing precondition dominates the risk, classify as a hypothesis and make the validation action the primary output.

## Executive finding table

At the top of the final deliverable, include:

| Repo | Highest-severity finding | OWASP | Blast radius | Initial -> adjusted priority | Finding confidence | Chain confidence |
|---|---|---|---|---|---|---|

Sort by severity, blast radius, then confidence.

Also include an attribution-aware table:

| Repo | Portfolio | Service line | Product | Attribution source | Routing confidence | Attribution gaps |
|---|---|---|---|---|---|---|

Use `UNKNOWN` for missing fields.

Routing confidence rules:

| Routing confidence | Use |
|---|---|
| High | Route directly to the stated portfolio, product, or service line. |
| Medium | Route with explicit owner confirmation. |
| Low | Do not assign final ownership; use only for triage and request confirmation. |

## Global conclusion

Include:

- top source-supported risks;
- top unvalidated hypotheses;
- disconfirmed or retracted items;
- systemic weaknesses;
- shared-library or monoculture risks;
- remediation themes;
- validation themes;
- private-repo or config dependencies;
- recommended next phases.

## Reporting handoff

This section is required. It must contain everything the reporting prompt needs to produce SARIF, OPTRS, VXDF decisions, human reports, engineering work items, and owner or group delivery packs without re-reading repositories.

Include per finding:

```yaml
- finding_id:
  stable_finding_id:
  stable_repository_id:
  stable_pattern_id:
  title:
  repository:
  repository_url:
  repository_attribution:
    portfolio: {value:, source:, confidence:, evidence:}
    service_line: {value:, source:, confidence:, evidence:}
    product: {value:, source:, confidence:, evidence:}
    routing_context: {service:, service_offering:, parent_business:, topics:, codeowners:}
    candidates:
    conflicts:
    gaps:
    validation_required:
    routing_confidence:
    routing_rule:
  commit:
  branch:
  default_branch_head_at_collection:
  evidence_freshness:
    source_retrieved_at:
    attribution_retrieved_at:
    api_status:
  file:
  line_start:
  line_end:
  function_or_method:
  code_snippet:
  source_code_status:
  confidence_downgrade_reasons:
  runtime_behaviour_status:
  configuration_or_identity_assumptions_status:
  overall_exploitability:
  owasp:
  cwe:
  cvss_qualitative:
  severity_if_validated:
  current_evidence_severity:
  blast_radius:
  description_plain_english:
  unsafe_pattern:
  remediation_direction:
  remediation_actions:
    - {action_id:, owner:, action:, priority:, depends_on_validation:}
  validation_actions:
    - {validation_id:, owner:, action:, required_evidence:, expected_result_format:}
  validation_required:
  disconfirmation_condition:
  chain_ids:
  claims_control: {safe_to_claim_now:, not_safe_to_claim:, safe_only_if_validated:}
  reporting_notes:
```

Include per chain:

```yaml
- chain_id:
  stable_chain_id:
  title:
  status:
  validation_state:
  repos_involved:
  attribution_summary:
    repos:
      - {repository:, portfolio:, service_line:, product:, attribution_source:, routing_confidence:, gaps:}
    cross_repo_routing:
      shared_portfolio_or_service_line:
      conflicts:
      unknowns:
  findings_involved:
  assumptions_involved:
  starting_position:
  abstract_sequence:
  assumptions:
  weakest_link:
  severity_if_validated:
  current_evidence_severity:
  evidence_that_would_validate:
  evidence_that_would_disconfirm:
  vxdf_decision:
  safe_wording_before_validation:
  safe_wording_if_validated:
  safe_wording_if_disconfirmed:
  claims_control: {safe_to_claim_now:, not_safe_to_claim:, safe_only_if_validated:}
```

Also include:

- assessment register path;
- findings suitable for SARIF;
- issue families suitable for OPTRS;
- chains eligible for VXDF now;
- chains where VXDF must be withheld;
- duplicate or common-pattern register;
- remediation action register and validation action register;
- attribution coverage for all affected repositories;
- routing gaps and owner-confirmation tasks;
- recommended routing owner or group for every finding and chain;
- ServiceNow or Jira work-item hints for every finding and chain — short description, plain-English abuse or consequence summary, feasible attacker outcome, proposed priority, remediation action IDs, validation action IDs, acceptance criteria;
- delivery-pack grouping instructions for chains that span multiple owners;
- private-repository and private-configuration dependency map listing the non-public repositories, tenants, key vaults, app configuration, pipelines, queues, IdPs, gateways, telemetry, or owner evidence required to validate or disconfirm each finding or chain;
- no-finding and not-promoted coverage notes for every assessed repository;
- analyst activity journal paths, where produced;
- missing commit or line evidence;
- private, config, or runtime evidence gaps.

### Attacker-path validation handoff

Produce a theme-level validation handoff mapping findings and chains into categories where applicable:

1. Auth or authz route reachability for missing-authorisation findings.
2. Object ownership negative tests for IDOR or cross-user mutation findings.
3. Token or secret log and state exposure checks.
4. CI/CD trust and permission review for shared actions.
5. Identity-provider or account-linking assumption validation.

For each theme include linked finding or chain IDs; owner type; exact test or review objective; required evidence; PASS / FAIL / INCONCLUSIVE meanings; severity impact of each result; and safe wording before validation.

### Assessment register minimum schema

```yaml
assessment_register:
  generated_at:
  scope:
  repositories:
    - {repository_id:, name:, url:, branch:, commit:, default_branch_head_at_collection:, evidence_freshness:, attribution:}
  patterns:
    - {pattern_id:, name:, affected_findings:, common_remediation:}
  findings:
    - {finding_id:, repository_id:, pattern_id:, title:, evidence_state:, severity:, attribution:, remediation_actions:, validation_actions:, chain_ids:}
  chains:
    - {chain_id:, title:, finding_ids:, assumptions:, validation_state:, vxdf_decision:}
  remediation_actions:
    - {action_id:, linked_findings:, owner:, action:, priority:}
  validation_actions:
    - {validation_id:, linked_findings:, linked_chains:, owner:, action:, required_evidence:}
  claims_control:
    - {subject_id:, safe_to_claim_now:, not_safe_to_claim:, safe_only_if_validated:}
  artefacts:
    - {artefact_id:, path:, type:, linked_ids:}
```

## Critical rules

- Evidence over speculation.
- Static only.
- Do not fabricate coverage, commits, line numbers, or validation.
- Do not describe a chain as demonstrated unless validated.
- Do not produce executable exploit bundles.
- Record missing evidence as action items.
- Separate findings from chains.
- Separate remediation actions from validation actions.
- Include confidence downgrade reasons.
- Consolidate repeated patterns.
- Include claims-control tables.
- Maintain the machine-readable assessment register.
- The reporting handoff must be complete enough that reporting can be produced without re-reading repositories.
- Use the promotion bar. Hardening-only observations are valuable, but they are not vulnerability findings unless they have a credible source-supported attacker path.
- End with a stop/continue recommendation for broad SAST versus targeted pattern hunting versus attacker-path validation.
