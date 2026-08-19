# Network restrictions

The sandbox network posture should be default-deny with a narrow allowlist.

## Allow

Allow only what the exercise requires:

- the department's chosen model endpoint (and any proxy or gateway in front of it);
- approved source-code hosting locations for cloning in-scope repositories;
- approved package repositories where dependencies are required for local validation;
- approved internal artefact stores if used for tooling.

## Block or avoid

Block or ensure the sandbox cannot reach:

- production systems;
- staging or pre-production systems that are not explicitly in scope;
- unrelated internal networks;
- cloud metadata endpoints (for example the `169.254.169.254` link-local address and equivalents);
- administrative interfaces;
- email, chat, or document systems;
- unrelated file shares;
- unrelated third-party APIs.

## Live-system interaction

The agent must not interact with deployed services, live APIs, or production infrastructure. AI SAST is a source-level activity. Runtime or deployed validation, where it is performed at all, must happen under separate authorisation, in an approved environment, and by the responsible owner — not by the AI agent.

## Local validation

Where local validation is performed inside the sandbox — for example running a unit test, harness, or local container — treat it as a source-level activity supported by a local runtime. Record it as `locally-validated` in the evidence state. Do not label it as production or deployed evidence.

## Egress logging

Log egress traffic from the sandbox for the duration of the exercise. Reconcile the log against the allowlist during and after the run. Investigate any traffic to destinations outside the allowlist.
