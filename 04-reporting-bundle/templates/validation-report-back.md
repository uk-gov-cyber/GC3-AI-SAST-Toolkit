# Validation report-back template

Use this template to report engineering, platform, identity, or operations validation results back to the assessment owner.

Return one completed template per validation task.

## Validation summary

| Field | Response |
|---|---|
| Validation ID | `V###` |
| Linked finding IDs | `R###-F###`, `R###-F###` |
| Linked chain IDs | `C###` |
| Team / service |  |
| Owning engineering team |  |
| Confirmed portfolio |  |
| Confirmed service line |  |
| Confirmed product |  |
| Repository URL |  |
| Commit tested |  |
| Branch tested |  |
| Environment | Local / non-production / production config review / other |
| Tester |  |
| Date | YYYY-MM-DD |
| Result | PASS / FAIL / INCONCLUSIVE |

## Validation performed

Describe exactly what was checked.

```text
<summary of validation performed>
```

## Evidence captured

List evidence captured. Do not include secrets, tokens, personal data, or live exploit artefacts.

| Evidence item | Location / reference | Notes |
|---|---|---|
|  |  |  |

## Result detail

### PASS

Use PASS when the validation evidence supports the assessed concern or confirms the missing condition.

```text
<what passed, what was observed, and why it validates the concern>
```

### FAIL

Use FAIL when the validation evidence disconfirms the assessed concern or proves the missing condition is not present.

```text
<what failed to reproduce or what control disconfirmed the concern>
```

### INCONCLUSIVE

Use INCONCLUSIVE when the test or review did not produce enough evidence either way.

```text
<why the result is inconclusive and what is still needed>
```

## Remediation status

| Field | Response |
|---|---|
| Remediation action IDs addressed | `A###`, `A###` |
| Status | Not started / In progress / Complete / Deferred |
| PR / change reference |  |
| Deployment status | Not deployed / Non-production / Production / Not applicable |
| Residual risk |  |

## Attribution confirmation

| Field | Response |
|---|---|
| Attribution properties checked? | Yes / No |
| `portfolio` populated or corrected? | Yes / No / Not applicable |
| `service_line` populated or corrected? | Yes / No / Not applicable |
| `product` populated or corrected? | Yes / No / Not applicable |
| IaC tag values differ from repository attribution? | Yes / No / Unknown |
| If different, explain why |  |

## Suggested classification update

| Field | Response |
|---|---|
| Suggested finding status | Unchanged / Confirmed / Disconfirmed / Needs more evidence |
| Suggested chain status | Unchanged / Validated / Disconfirmed / Needs more evidence |
| Suggested severity change | None / Increase / Decrease |
| Rationale |  |

## Safe sharing notes

State any wording that should or should not be used after this validation.

```text
<safe wording and caveats>
```
