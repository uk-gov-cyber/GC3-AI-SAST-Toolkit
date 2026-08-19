# No-finding / not-promoted coverage register

Use this register to record reviewed repositories, files, routes, and candidate issues that did not become final vulnerability reports. The purpose is to preserve coverage and negative evidence without inflating the final findings list.

## Summary

| Field | Value |
|---|---|
| Assessment / batch |  |
| Analyst / model run |  |
| Assessment period |  |
| Source scope |  |
| Reviewed commit range |  |
| Promotion threshold | A vulnerability report requires traceable source, sink, reachable route or explicit reachability gap, abuse case, impact, validation gap, and remediation direction, and must meet the department's severity threshold recorded in the run scope. |

## Repository coverage

| Repo ID | Repository | Commit SHA | Areas reviewed | Result | Reason no finding was promoted | Follow-up |
|---|---|---|---|---|---|---|
| `R###` |  |  |  | No finding / not promoted / duplicate / disconfirmed / deferred |  |  |

## Candidate disposition

| Candidate ID | Repo ID | Candidate title | Initial signal | Disposition | Evidence supporting disposition | Reopen condition |
|---|---|---|---|---|---|---|
| `CAN-###` | `R###` |  |  | Not promoted / duplicate / disconfirmed / needs private validation |  |  |

## Negative evidence notes

Record only evidence actually reviewed. Do not claim absence from a single empty search unless the searched pattern, files, branch, and commit are listed.

| Evidence ID | Repo ID | Search / files reviewed | Result | Limitation |
|---|---|---|---|---|
| `NEG-###` | `R###` |  |  |  |

## Carry-forward risks

| Repo ID | Risk or uncertainty | Why it was not promoted now | Required future evidence |
|---|---|---|---|
| `R###` |  |  |  |
