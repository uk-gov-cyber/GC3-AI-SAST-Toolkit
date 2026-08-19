# Private dependency validation map

Use this map when source-only SAST depends on private repositories, private packages, cloud configuration, identity-provider settings, secrets, runtime route maps, telemetry, or deployment state that was not available to the analyst.

## Summary

| Field | Value |
|---|---|
| Assessment / batch |  |
| Assessment period |  |
| Repositories reviewed |  |
| Private assets required |  |
| Highest evidence state available without private validation | Source-supported / hypothesis / disconfirmed |
| Owner confirmation required | Yes / No |

## Dependency map

| Finding / chain ID | Source evidence | Private dependency | Why it matters | Owner to validate | Required evidence | Expected result format |
|---|---|---|---|---|---|---|
| `R###-F###` / `C###` |  |  |  |  |  | PASS / FAIL / INCONCLUSIVE with evidence reference |

## Configuration dependencies

| Finding / chain ID | Configuration area | Unvalidated assumption | Validation method | Safe claim until validated |
|---|---|---|---|---|
| `R###-F###` / `C###` | Identity provider / secret store / pipeline / runtime / telemetry / database / network |  |  |  |

## Private code dependencies

| Finding / chain ID | Private repo / package / service | Required review question | Blocking status | Route |
|---|---|---|---|---|
| `R###-F###` / `C###` |  |  | Blocking / non-blocking / optional strengthening |  |

## Validation outcomes

| Validation ID | Linked ID | Outcome | Evidence reference | Effect on report |
|---|---|---|---|---|
| `V###` | `R###-F###` / `C###` | PASS / FAIL / INCONCLUSIVE |  | Severity raised / severity reduced / chain validated / chain disconfirmed / unchanged |
