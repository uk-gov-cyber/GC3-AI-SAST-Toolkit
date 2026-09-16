<!--
================================================================================
WORKED EXAMPLE — DO NOT SUBMIT.
This file is a fictitious filled-in vulnerability report illustrating the
expected style, depth of evidence, and tone. The repository, code, commit
SHA, and finding are invented.
Refer to it when filling out a real `vulnerability-report.md`.
================================================================================
-->

# Vulnerability Report: SQL injection in `/api/orders` search filter

## 1. Report metadata

| Field                  | Value                                              |
|------------------------|----------------------------------------------------|
| Report ID              | `<department scheme, e.g. DEPT-TEAM-YYYYMMDD-NNN>` |
| Submitted by           | `A. Reporter`                                      |
| Team                   | `Application Security`                             |
| Date submitted         | `2026-08-19`                                       |
| Discovery tool         | `Claude Code`                                      |
| Discovery model        | `<provider/model, e.g. Opus 4.7>`                  |
| Prompt-pack version    | `gc3-ai-sast v1.0 — prompt 02 (full SAST)`         |
| Status                 | `Submitted`                                        |
| Tracking ID            | _(left blank)_                                     |
| Human validation status          | _(Enter human validation status here)_                                   |

## 2. Summary

The `/api/orders` endpoint in the fictional `acme-shop` service builds its `WHERE` clause by string-interpolating the unvalidated `filter` query parameter directly into a raw SQL statement, then passing the result to `db.execute`. A request from an attacker class in scope can read or modify the entire `orders` and `users` tables via `UNION SELECT` payloads. The route is reachable in the default deployment with no feature flag gating.

## 3. Classification

- **Vulnerability type**: `SQL Injection`
- **CWE ID**: `CWE-89`
- **OWASP category**: `A03:2021 Injection`
- **Severity**: `Critical` (department scale)
- **CVSS v3.1 vector + score**: `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H` -> `9.8`
- **Attacker classes considered**: unauthenticated internet user; self-registered user
- **Composed issue?**: `No`

## 4. Affected location(s)

| Repo URL                                | Commit SHA                                 | File path                | Lines   | Function / symbol |
|-----------------------------------------|--------------------------------------------|--------------------------|---------|-------------------|
| `https://internal.example/acme-shop`    | `4f1a9c2b8d6e3a7f0c5b2d1e9f8a6c4b3d2e1f0a` | `src/api/orders.py`      | `42-58` | `search_orders`   |
| `https://internal.example/acme-shop`    | `4f1a9c2b8d6e3a7f0c5b2d1e9f8a6c4b3d2e1f0a` | `src/db/connection.py`   | `19-27` | `execute_raw`     |

## 5. Technical description

The HTTP handler `search_orders` reads the `filter` query parameter from `flask.request.args` and concatenates it into a SQL string via an f-string (`src/api/orders.py:48`). The resulting string is handed to `execute_raw` (`src/db/connection.py:24`), a wrapper around `sqlite3.Connection.executescript` that supports stacked statements and applies no parameter binding. There is no allowlisting, escaping, or typed parsing of `filter` anywhere along the path — the value reaches the sink unchanged.

The route is mounted in `src/app.py` outside the `auth_required` blueprint. Rate limiting applies at 60 RPM per IP but does not impede exploitation.

## 6. Evidence

### 6.1 Source (entrypoint that accepts attacker input)

`src/api/orders.py:42-50`

```python
@bp.route("/api/orders", methods=["GET"])
def search_orders():
    filter_clause = request.args.get("filter", "")
    if not filter_clause:
        return jsonify(load_recent_orders())
    sql = f"SELECT id, user_id, total FROM orders WHERE {filter_clause}"
    rows = execute_raw(sql)
    return jsonify(rows)
```

### 6.2 Sink (the dangerous operation)

`src/db/connection.py:19-27`

```python
def execute_raw(sql: str):
    with _connection() as conn:
        cursor = conn.cursor()
        cursor.executescript(sql)  # stacked statements permitted; no parameterisation
        return cursor.fetchall()
```

### 6.3 Path (intermediate calls connecting source to sink)

`src/api/orders.py:48` → `src/db/connection.py:19` (direct call, no intermediate hop).

### 6.4 Tests / fixtures

`tests/api/test_orders.py:12-20`

```python
def test_search_orders_happy_path(client):
    resp = client.get("/api/orders?filter=status='shipped'")
    assert resp.status_code == 200
```

The existing test asserts only status code and does not cover malicious `filter` values; injection payloads are not exercised.

## 7. Impact

A single HTTP GET can read or modify the entire `orders` and `users` tables. `UNION SELECT` payloads expose all row data; stacked statements permit `INSERT`, `UPDATE`, and `DELETE`. There is no downstream authorisation between this endpoint and the database.

## 8. Evidence status

| Field | Value |
|---|---|
| Source status | `SAST-confirmed` |
| Runtime status | `locally-validated` (unit test against local SQLite reproduced the injection) |
| Reachability status | `source-visible` |
| Exploitability status | `hypothesis` (locally validated; deployed reachability not established) |
| Impact severity if exploitable | `Critical` |
| Evidence confidence | `high` |
| Lifecycle state | `promoted-finding` |

## 9. Vulnerability disclosure status

| Field | Value |
|---|---|
| Disclosure classification | `locally-validated` |
| Evidence level | `local-harness` |
| Production impact | `not established` |
| Safe to say now | "Source review shows an SQL-injection defect; a local harness reproduced the injection against a sanitised test database." |
| Not safe to say | "Production data is exfiltrable"; "The live database is compromised"; "Attackers are actively exploiting this". |
| External disclosure handling | `internal only` |
| Owner validation required | `Yes — deployed reachability and rate-limit or WAF interaction must be confirmed by the service owner.` |

## 10. Proof of concept

```text
Local reproduction only. Payload run against a local SQLite instance
populated from the repository's test fixtures:

GET /api/orders?filter=1=1%20UNION%20SELECT%20id,email,'x'%20FROM%20users --

Response returned the full `users.email` column contents alongside the
orders result set.
```

No PoC was run against any deployed environment.

## 11. Coding-agent confirmation

### 11.1 Confirmation tool / model used

| Field                       | Value                                            |
|-----------------------------|--------------------------------------------------|
| Confirmation tool           | `Codex`                                          |
| Confirmation model          | `<provider/model, e.g. GPT-5.5>`                 |
| Prompt-pack version         | `gc3-ai-sast v1.0 — prompts 02 confirmation set` |
| Independent from discovery? | `Yes — different tool and model from discovery`  |

### 11.2 Generic prompts

#### G1. Source-to-sink trace

_Agent response summary:_ Confirmed `filter_clause` originates at `src/api/orders.py:44`, flows via f-string into `sql` at line 48, is passed to `execute_raw` at line 49, and reaches `cursor.executescript` at `src/db/connection.py:23`. No intermediate function touches the value.

#### G2. Sanitisation check

_Agent response summary:_ No sanitisation, escaping, allowlisting, or parameter binding between source and sink. The path is unsafe.

#### G3. Reachability

_Agent response summary:_ The blueprint at `src/app.py:31` mounts `orders_bp` without wrapping it in `auth_required`. No feature flag guards the route. Reachable in the default deployment for the attacker classes in scope.

### 11.3 Type-specific prompt (SQL injection)

_Agent response summary:_ Query is built by f-string interpolation. `executescript` is used, which permits stacked statements. No API-level parameter binding. Classic injection.

### 11.4 Negative-control prompt (mandatory)

_Agent response summary:_ Considered the possibilities that (a) a middleware or WAF rewrites `filter` before it reaches the handler, or (b) `executescript` normalises inputs. Neither holds in source: the `before_request` chain in `src/app.py:15-25` performs only rate limiting and request logging, and `sqlite3.Connection.executescript` in the standard library does not perform parameter binding. The finding still holds.

**Submitter assessment**: Finding holds after negative control. Local validation increases confidence but deployed reachability against real infrastructure (WAF, egress rules, live DB behaviour) is not established.

## 12. Suggested remediation

Replace the string-interpolated query with a parameterised query using the driver's binding API. For SQLite, use `cursor.execute("SELECT id, user_id, total FROM orders WHERE status = ?", (status,))` and treat `filter` as a set of allowlisted column-value pairs rather than a raw SQL fragment. Add negative test cases covering injection payloads. Consider whether `execute_raw` should exist at all — remove or restrict it if not needed.

## 13. Validation actions

| Validation ID | Owner type          | Action                                                                | Required evidence | Result format | Effect on severity |
|---------------|---------------------|-----------------------------------------------------------------------|-------------------|---------------|--------------------|
| `V001`        | Service owner       | Confirm route is reachable in the deployed environment without auth   | Deployment config or authorised request log | `PASS` / `FAIL` / `INCONCLUSIVE` | PASS raises to production-confirmed Critical |
| `V002`        | Platform operator   | Confirm WAF or egress rules do not neutralise the payload             | WAF ruleset review | `PASS` / `FAIL` / `INCONCLUSIVE` | FAIL may reduce practical impact |

## 14. Claims control

| Claim | Status | Reason |
|---|---|---|
| Safe to claim now | "An SQL-injection defect exists in `src/api/orders.py:48` and was reproduced in a local harness." | Directly supported by source and local validation. |
| Not safe to claim | "Production data has been exfiltrated." | No deployed evidence has been captured. |
| Safe only if validated | "The finding is exploitable against the deployed service." | Requires `V001` and `V002` to close. |

## 15. References

- CWE-89: https://cwe.mitre.org/data/definitions/89.html
- OWASP A03:2021 Injection

## 16. Submitter checklist

- [x] Severity meets the threshold recorded in the run scope for this exercise.
- [x] Vulnerability type field is filled.
- [x] Discovery tool, model, model version, and prompt-pack version are recorded.
- [x] Every code claim has a `file:line` citation.
- [x] Evidence status and Vulnerability Disclosure Status blocks are complete.
- [x] At least two coding-agent confirmation prompts have been run, output is included, and the confirmation tool, model, and prompt-pack version are recorded.
- [x] The negative-control prompt has been run and the finding still holds.
- [x] No live secrets pasted into the report body.
- [x] No unnecessary personal data or commit author metadata included.
- [x] PoC was not run against systems the team does not own.

## 17. Receiving-team notes

_(Left blank by submitter.)_
