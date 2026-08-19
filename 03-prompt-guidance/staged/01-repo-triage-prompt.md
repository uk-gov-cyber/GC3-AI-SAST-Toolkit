# Prompt 1 — Repository triage and handoff

Use this prompt as the first substantive prompt in the staged workflow. It performs repository enumeration, prioritisation, attribution collection, and produces the structured handoff that prompt 2 will consume.

Triage does not perform full SAST and does not produce vulnerabilities. It produces a prioritised repository set and a handoff that later prompts can consume without repeating discovery work.

## Preamble

Before producing output, apply `00-run-control-and-evidence-model.md`.

Begin the response with:

```text
Current task: triage and scanner-feed intake, limited to SAST evidence and approved local validation only.
```

Model output in this phase is triage and candidate analysis only. Do not present vulnerabilities as confirmed. Scanner outputs — Semgrep, CodeQL, dependency tools, custom grep, or model-generated issue lists — are leads only.

## Required outputs

In addition to the triage report, produce:

- a depth-budget statement or section covering repository count, files sampled per repository, scanner leads inspected, stopping conditions, and known coverage gaps;
- `scanner-lead-register.json` or `.yaml`;
- `review-queue.json` or `.yaml` — repositories queued for the next prompt;
- `excluded-or-deferred-register.json` or `.yaml`;
- `endpoint-authz-inventory.json` or `.yaml` for web, API, and admin services, listing discovered route groups, handlers or controllers, HTTP methods where visible, authentication decorators or middleware, authorisation or policy checks, object-ownership checks, CSRF or session controls, feature flags, and unresolved route-to-control gaps;
- `cross-service-and-data-composition-map.json` or `.yaml` where services share identifiers, tokens, accounts, roles, callbacks, invitations, personal data, or privileged backend clients.

## Cost control

- Run deterministic filtering before model-heavy review where tooling is available.
- Summarise scanner feeds into lead records rather than carrying full raw scanner output forward.
- Deduplicate leads by repository, rule ID, source file, sink file, and apparent source/sink pair.
- Group repeated framework or pattern findings and select representative examples for the next prompt.
- Classify each repository as `deep-review`, `sample-only`, `defer`, `duplicate-pattern`, or `out-of-scope`.
- Classify each scanner lead as `inspect-now`, `sample-confirm`, `defer`, `duplicate`, or `exclude`.
- Avoid full vulnerability narrative in this prompt.

## Endpoint or route-group leads

For every endpoint or route-group lead, record:

- route, controller, or handler file and line where available;
- route pattern and HTTP method where visible;
- authentication mechanism;
- endpoint-level authorisation or policy check;
- object ownership or tenant or data-scope check;
- privileged backend client, write key, token, or data sink reached;
- whether the route may combine data with another service or identifier set;
- initial classification: `authz-review`, `object-scope-review`, `data-composition-review`, `chain-review`, `sample-confirm`, or `exclude`.

## Scanner leads

For every scanner lead, record:

- tool name;
- rule ID;
- file and line;
- candidate vulnerability class;
- apparent source;
- apparent sink;
- whether the scanner supplied dataflow;
- initial inspection confidence;
- reason to inspect, defer, or exclude.

Do not mark scanner leads as vulnerabilities. A scanner lead may only become a finding in a later prompt if it passes the source, sink, path, control gap, and consequence gate.

## Anti-drift check

Before finalising the triage output, confirm:

- no scanner lead is described as confirmed;
- every high-priority repository has a concrete security surface;
- every exclusion has a reason;
- duplicate and repeated-pattern leads have been grouped;
- missing evidence is explicitly labelled `Not established from current evidence`;
- the next prompt has enough file pointers to avoid repeating broad discovery;
- machine-readable registers agree with the Markdown summary.

## Role

You are operating inside a hardened analysis sandbox on an approved, sanitised local scan copy of the departmental repository set. You are performing **repository security triage and prioritisation**, not a full vulnerability assessment.

Your output must answer:

1. Which repositories should receive deeper SAST?
2. Why are they prioritised?
3. What files and security surfaces should the next prompt inspect first?
4. What repositories were excluded and why?
5. What attribution and routing evidence exists for each repository?
6. What evidence supports each triage decision?

## Cross-prompt contract

Every output from this prompt must support the next prompt without relying on chat memory, local scratch notes, or uncited assumptions.

Carry forward:

- stable repository IDs (for example `R001`, `R002`);
- repository URL, default branch, commit or HEAD SHA, collection date, and API status where available;
- attribution evidence with source, confidence, conflicts, gaps, and owner-confirmation requirements;
- candidate file paths and line numbers for security-relevant surfaces;
- explicit exclusions and out-of-scope decisions;
- depth limits and evidence gaps;
- no fabricated commits, line ranges, attribution, or coverage.

If a value is missing, write the gap explicitly. Do not use `UNKNOWN` as a filename or ownership bucket. Use routing confirmation IDs such as `RC001`, `RC002`, and carry the missing attribution as a validation requirement.

## Mission

Analyse the approved, in-scope repositories the reviewer has provided. Use the approved local scan copies and any approved manifests or metadata inventories the department has authorised.

Your tasks are to:

1. Enumerate the repositories the reviewer has approved for triage.
2. Collect repository attribution and routing evidence.
3. Exclude repositories that are out of scope.
4. Identify repositories that merit deeper SAST.
5. Rank the top candidates for the next prompt (typical shortlist size: 25).
6. For each selected repository, provide enough evidence and file pointers to start the next prompt efficiently.
7. Produce a structured handoff.
8. Produce a stop/continue recommendation for the next phase.

The handoff must include both human-readable Markdown and a machine-readable `assessment-register.json` or `assessment-register.yaml` seed. The register is the contract between prompts. Later prompts should not create separate tables or notes that disagree with the register.

## Scope and constraints

- Approved, sanitised local repository copies and approved manifest or metadata inventories only.
- Read-only analysis.
- Do not modify code.
- Do not interact with live departmental services.
- Do not probe, scan, fuzz, authenticate to, or test reachable endpoints.
- Do not search for leaked secrets to make hypotheses realisable.
- Do not assume internal access, credentials, tenant-admin visibility, secrets, unapproved private repositories, or privileged network position.
- Do not produce exploit steps, payloads, working requests, scanners, or PoCs.
- Do not present vulnerabilities as confirmed.
- Apply the attacker classes, severity threshold, and reporting threshold recorded in the run scope.

## Ownership and third-party boundaries

Assess departmental-owned or departmental-curated code only.

Definitions:

- **Owned:** code authored within the department.
- **Curated:** third-party code the department has forked, vendored, re-packaged, or distributes as authoritative.
- **Vendored or in-tree third-party code:** third-party code committed into a departmental repository. Treat as departmentally controlled for triage because the department controls the patch decision.

Out of scope unless departmental code exposes or configures the issue:

- external upstream dependency vulnerabilities;
- transitive dependency CVEs;
- framework CVEs without a departmentally exposed path;
- base-image CVEs.

Dependency posture may be used as a maintenance signal, not as a vulnerability finding.

## Repository inclusion rules

Include a repository in the shortlist only if it appears likely to contain security-relevant departmentally controlled code.

Include candidates showing evidence of:

- internet-facing web apps, APIs, admin tools, or gateways;
- authentication, authorisation, federation, OIDC, OAuth, SAML, JWT, sessions, or cookies;
- identity linking, invitation, account recovery, profile change, or role assignment;
- file upload, parsing, templating, deserialisation, dynamic execution, or user-supplied data processing;
- service-to-service trust, API keys, signing keys, bearer tokens, secrets, or mTLS;
- sensitive personal, operational, casework, or organisational data;
- deployment or infrastructure controls affecting exposure, auth posture, secrets, WAF, CORS, IAM, or CI/CD.

Exclude by default:

- archived repositories, unless evidence suggests current deployment;
- forks or mirrors where upstream is authoritative;
- empty repositories;
- documentation-only repositories;
- static content sites with no server-side logic;
- templates with no concrete deployment;
- prototypes not intended for production;
- repositories where the only concern is external third-party code.

If an excluded repository appears high-risk or possibly still deployed, list it in **Watchlist / Excluded But Noted**.

## Reachability-proof rule

For every repository selected for deeper SAST, include at least one concrete reachability proof for the security surface that justifies selection.

Each proof must name:

- the attacker class (drawn from the classes in the run scope — for example unauthenticated internet user, self-registered user, authenticated ordinary user, authenticated privileged user, insider, compromised dependency);
- the exact entry point — route, controller action, callback, webhook, public form, message consumer, workflow trigger, or IaC-provisioned public resource;
- the delivery mechanism that gets attacker-controlled input to that entry point;
- the privileged effect or sensitive sink worth reviewing;
- the evidence source, such as file path, route declaration, README deployment statement, workflow trigger, IaC resource, or explicit gap.

Do not rank a repository because it is generally important. Rank it because the source or metadata shows a concrete surface worth reviewing, or because an explicit private or configuration dependency blocks final reachability and must be validated.

If reachability is inferred from deployment shape rather than proven from approved reviewed source, label it `deployment-inferred`. If it depends on tenant configuration, app configuration, secrets, or operator evidence, label it `private/config-dependent`.

For web, API, admin, MVC or equivalent service candidates, the reachability proof must include a lightweight state-changing route or auth sweep when practical:

- route, action, or endpoint;
- HTTP method or trigger;
- whether it mutates state or reaches a privileged or server-side write sink;
- authentication and authorisation control observed in source;
- CSRF or anti-forgery control, separately from authentication;
- feature flag or configuration guard, if any;
- server-side service key, token, role, or backend write API used downstream;
- whether reachability against the attacker classes in scope is source-visible, deployment-inferred, private- or config-dependent, or not established.

Do not treat global middleware, feature-flag visibility, or anti-forgery tokens as proof of route authorisation unless the endpoint or action itself is covered by an explicit policy, attribute, convention, or fallback policy.

## Access and evidence rules

- Prefer targeted file reads over broad clones.
- Respect any rate or API limits imposed by the source inventory and record any gaps.
- For candidate repositories, sample enough files to justify priority and guide the next prompt.
- Record exact files read.
- Record commit SHA or default-branch HEAD SHA where available. If the SHA is unavailable, say so explicitly.

Recommended files to inspect:

- `README*`;
- `CODEOWNERS`, `.github/CODEOWNERS`;
- catalog or service-registry files (for example `catalog-info.yaml`);
- package manifests and lockfiles;
- route, controller, or API files;
- auth, session, JWT, OIDC, SAML middleware;
- config files;
- Dockerfiles;
- deployment manifests;
- CI/CD workflow files;
- Terraform, Helm, Bicep, ARM, or equivalent IaC files;
- files whose names include auth, policy, token, jwt, saml, oidc, session, user, role, invite, callback.

For each selected repository, also produce a short **No-Finding / Not-Promoted Coverage Seed** listing the route groups, auth surfaces, upload or parsing paths, message handlers, CI/CD surfaces, and deployment or IaC surfaces sampled during triage. The next prompt must expand this into a no-finding coverage statement if no issue is promoted.

## Attribution collection

Collect attribution for every enumerated repository where practical, and always for every shortlisted candidate.

Canonical attribution fields, in priority order of source:

1. Repository custom properties or approved attribution registry: high confidence.
2. Active IaC or deployment literal tags: medium confidence.
3. Active IaC or deployment values resolved from variables or tfvars: medium confidence with environment context.
4. Unresolved IaC or deployment expressions: low-confidence candidate only.
5. Commented IaC or deployment tags: low-confidence historical or template signal only.
6. Repository topics, README, CODEOWNERS, catalog files, or documentation: routing signals only unless an approved mapping validates them.

Do not infer portfolio, product, or service-line values from topics unless an approved mapping exists. Preserve attribution source, confidence, conflicts, gaps, and owner-confirmation requirements.

For each selected repository, emit:

- selected attribution values;
- confidence for each selected value;
- evidence source for each selected value;
- candidate evidence not selected;
- conflicts;
- gaps;
- validation required.

If sources disagree, prefer the higher-priority source, preserve the conflicting evidence, and mark `conflict_detected`.

## Prioritisation rubric

Score each candidate on **Priority** and **Confidence** separately.

| Priority | Criteria |
|---|---|
| **P1 - Highest** | Shared security library, identity service, auth or token or policy component, or internet-facing service with sensitive data or ecosystem blast radius. |
| **P2 - High** | Internet-facing service handling sensitive workflows, user data, admin functions, or identity integration without central ecosystem role. |
| **P3 - Medium** | Likely internet-facing service with moderate data sensitivity, unclear exposure, or conventional framework protections. |
| **P4 - Low** | Internal tooling, IaC, deployment, or support code with limited direct external reach but possible inherited risk. |
| **P5 - Lowest** | Narrow candidate with minimal observed attack surface. |

| Confidence | Meaning |
|---|---|
| **High** | Direct code or config evidence: routes, auth middleware, deployment manifests, or security-sensitive implementation. |
| **Medium** | Some source read, but priority partly inferred from README, structure, or framework conventions. |
| **Low** | Metadata-heavy assessment with limited source evidence. |

Tie-breakers:

1. Shared auth, identity, or security components first.
2. Explicit internet exposure before inferred exposure.
3. Ecosystem or multi-service blast radius before single-service risk.
4. Sensitive data before low-impact data.
5. Lower attacker effort before higher attacker effort.

### Attacker-relevance filter

Prioritise repositories that could plausibly contribute to one of these outcomes if a defect is found:

- unauthorised data access;
- unauthorised data mutation;
- identity, session, or account compromise;
- privilege escalation;
- credential, token, secret, or cloud access exposure;
- CI/CD compromise or deployment tampering;
- remote code execution;
- externally reachable authentication or authorisation bypass.

Do not over-prioritise repositories where the only observed concerns are generic maintenance issues, third-party dependency age, static content, cosmetic hardening, or documentation-only risk.

## Required output sections

### 1. Executive triage summary

Include totals enumerated, excluded, candidates considered, shortlist returned, cut-off criterion, completeness caveats, and top systemic themes. Include attribution coverage: repositories with high-confidence attribution, partial attribution, low-confidence routing signals only, and no attribution found.

### 2. Candidate matrix

Sort by Priority P1–P5, then Confidence High–Low, then repository name.

Each row must include:

- Rank;
- Repository name and URL;
- Default branch (if known);
- Commit or HEAD SHA (if known);
- Attribution fields, source, and confidence, or `UNKNOWN`;
- Attribution gaps or validation required;
- Priority;
- Confidence;
- Suspected exposure surface;
- Primary risk hypothesis (weakness class and consequence class);
- Likely vulnerability classes (OWASP or CWE);
- Attacker preconditions from the run scope;
- Potential impact (CIA and blast radius);
- Evidence basis (specific files read);
- First-look files for the next prompt;
- Depth recommendation (Full / Focused / Limited).

Use `UNKNOWN` only as a table or register value. Do not create folders or filenames named `UNKNOWN`. Route missing ownership through a stable routing confirmation ID.

### 3. Exclusion register

Group excluded repositories by reason with counts and notable examples.

### 4. Watchlist / excluded but noted

For excluded but notable repositories, include repository, reason excluded, why notable, and evidence that would bring it back into scope.

### 5. Handoff manifest

Self-contained. For each selected repository include:

```yaml
- rank:
  repository:
  url:
  default_branch:
  commit_or_head_sha:
  attribution:
    selected:
      portfolio: {value:, source:, confidence:, evidence:}
      service_line: {value:, source:, confidence:, evidence:}
      product: {value:, source:, confidence:, evidence:}
    routing_context:
      service:
      service_offering:
      parent_business:
      topics:
      codeowners:
    candidates:
    conflicts:
    gaps:
    validation_required:
  priority:
  confidence:
  repo_type:
  suspected_exposure:
  likely_frameworks:
  likely_auth_components:
  likely_sensitive_data:
  files_sampled:
  must_review:
  should_sample:
  may_skip:
  primary_risk_hypotheses:
  likely_vulnerability_classes:
  known_scope_caveats:
  suggested_execution_order_reason:
```

### 6. Execution order

Recommended assessment order — shared security libraries first, then consumers. Explain deviations from rank order.

### 7. Stop / continue decision

End with a decision recommendation:

| Decision | Use when |
|---|---|
| Continue broad triage | Candidate discovery is incomplete or new high-risk repository classes are still appearing. |
| Proceed to full SAST | The shortlist has enough evidence and attribution to support the next prompt. |
| Narrow to a targeted pattern hunt | Repeated themes have emerged and broad discovery is producing diminishing returns. |
| Pause SAST and move to validation | Existing findings are sufficient and the highest-value question is whether they are reachable in deployed environments. |

State the evidence behind the recommendation and what would change it.

## Critical rules

- This is triage, not SAST.
- Do not present vulnerabilities as confirmed.
- Do not write PoCs.
- Do not generate exploit chains.
- Do not infer deployment as fact without approved source, configuration, runtime, owner, or operator evidence.
- Do not fabricate commit SHAs, line numbers, or coverage.
- Record uncertainty as uncertainty.
- The handoff must be complete enough that a new analyst can begin the next prompt without redoing triage.
