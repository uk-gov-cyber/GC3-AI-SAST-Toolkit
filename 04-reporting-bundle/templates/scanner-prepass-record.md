# Scanner pre-pass record

Use this before LLM-assisted SAST when a repository or fresh source drop is available. The scanner pre-pass is not a replacement for manual or source-to-sink review; it is a file-ranking, duplicate-detection, and coverage aid.

## Scope

| Field | Value |
|---|---|
| Assessment / batch |  |
| Assessment period |  |
| Repositories |  |
| Commit SHAs |  |
| Exclusions |  |
| Scanner profile | Secrets / dependency / IaC / SAST / container / DAST config |

## Recommended scanner set

| Scanner | Purpose | Required? | Output path |
|---|---|---|---|
| Secret scanner (for example gitleaks or equivalent) | Detect committed token or secret patterns for authorised handling | Yes | |
| Dependency scanner (for example trivy, grype, npm audit, bundler audit) | Rank vulnerable dependency hotspots | Yes where lockfiles exist | |
| SAST rule engine (for example Semgrep or equivalent) | Find language or framework patterns and route candidates | Yes where the language is supported | |
| IaC scanner (for example Checkov, tfsec, trivy config) | Review Terraform, Kubernetes, or cloud config | Yes where IaC exists | |
| Dockerfile linter (for example hadolint) | Container hardening and build risks | Optional | |
| DAST config review (for example ZAP baseline plan) | Prepare authorised dynamic testing candidates | Optional; do not run without approval | |

## Findings triage

| Scanner finding ID | Tool | Repo | File / line | Severity | Disposition | Linked report / candidate |
|---|---|---|---|---|---|---|
|  |  |  |  |  | Promote / duplicate / false positive / needs review / no action |  |

## LLM review inputs

| Input | Path | How to use |
|---|---|---|
| Scanner raw output |  | Use for file ranking and pattern discovery. Do not copy secrets into reports. |
| Scanner triage summary |  | Feed into the SAST prompt as a candidate list. |
| No-finding register seed |  | Record reviewed scanner results that were not promoted. |

## Safety controls

- Do not include raw secrets in final reports.
- Preserve scanner false positives in the no-finding coverage register where they affected assessment scope.
- Treat dependency CVEs as candidates until application reachability and deployed version are checked.
- Do not run DAST, fuzzing, brute force, or authenticated probing unless explicitly authorised for the named environment.
