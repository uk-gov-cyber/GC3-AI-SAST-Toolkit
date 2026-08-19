# Reporting principles

Reporting turns validated candidate findings into artefacts that engineering owners, security teams, and reviewers can act on. The principles below apply to every report produced through this toolkit.

## Core principles

- Keep individual findings and attack chains separate, linked by stable IDs.
- Make each human report self-contained: repository, commit, file, line, code evidence, abuse case, feasible attacker outcome, evidence state, validation gaps, remediation direction, and safe wording.
- Separate source-confirmed behaviour from runtime-validated behaviour and deployed-environment validation.
- Do not describe a finding as proven production compromise unless the report contains that evidence.
- Preserve lower-severity, duplicate, disconfirmed, downgraded, not-promoted, and out-of-scope decisions in registers rather than silently dropping them.
- Require reachability reasoning before promotion: entrypoint, source, sink, authentication or authorisation boundary, guard conditions, abuse case, and validation dependency.
- Treat scanner output as candidate discovery and file ranking until human source-to-sink review promotes a finding.
- Keep remediation tasks separate from validation tasks.

## Evidence states

Every artefact should carry an explicit evidence label. Detailed vocabulary is defined in `../03-prompt-guidance/staged/00-run-control-and-evidence-model.md`. In short:

- source-confirmed;
- locally-validated;
- runtime or deployed validated;
- scanner-supported;
- conditional (impact depends on a named dependency);
- source-disconfirmed;
- not-promoted.

## Claims-control wording

Use safe wording that matches the available evidence. Prefer:

- "source evidence shows";
- "local validation showed";
- "deployed reachability not established";
- "configuration-dependent";
- "owner or operator validation required".

Do not claim production exploitability, production compromise, or live impact unless the report records authorised runtime or deployed evidence.

## Severity, attacker model, and threshold

The department sets:

- the severity scale used (typically CVSS, sometimes with an environmental adjustment);
- the attacker classes in scope;
- the threshold above which a finding is reported through the departmental route.

The toolkit does not fix these values. Report authors apply the department's values and record them in the report metadata.

## Coverage records

Preserve a no-finding / not-promoted coverage record covering areas that were reviewed but did not produce a promoted finding. Include the reason (disconfirmed by source review, below threshold, out of scope, duplicate of another candidate, awaiting owner validation, and so on). A template is provided at `../04-reporting-bundle/templates/no-finding-coverage.md`.

## Reconciliation

Every candidate finding and chain must trace to exactly one of:

- a standalone human report;
- an entry in a combined issue-family report;
- an entry in the no-finding / not-promoted coverage register;
- an entry marked disconfirmed, downgraded, duplicate, out of scope, or awaiting owner validation.

This traceability is a control, not a severity gate.

## Post-remediation

After remediation, re-run the relevant staged prompt against the fixed code, update the evidence state, and record the verification outcome. Closing a finding on remediation alone, without verification, is not sufficient.

## Documentation-only repository

Do not commit vulnerability reports, candidate findings, scan outputs, prompt transcripts, exploit evidence, source code, secrets, personal data, or departmental vulnerability data to this toolkit repository. Reports must be handled through the agreed departmental reporting route.
