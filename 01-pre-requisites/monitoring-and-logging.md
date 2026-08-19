# Monitoring and logging

Prompt, response, and artefact activity must be logged so the exercise is auditable and defensible.

## What to log

- prompts sent to the model, including staged and one-shot prompts, and any custom prompts;
- responses received from the model;
- tool calls and file reads or writes performed by the agent inside the sandbox;
- outbound network traffic from the sandbox (see `network-restrictions.md`);
- start and end times of each run;
- model name, model version, and prompt-pack version used for the run;
- reviewer identity and repository under review.

Do not log live secrets or personal data that appears inside prompts or responses. Redact secret-like values as `<REDACTED:purpose>` at capture time where the logging pipeline allows.

## Retention

Set an explicit retention window for prompt and response logs. The window should be long enough to support audit, incident response, and re-verification of past findings, and short enough to satisfy data-minimisation obligations. Document the window in the run scope.

State up front who may read the logs, under what conditions, and how a subject access request that touches prompt history would be handled.

## Data protection

Before the first substantive run, complete or update a data-protection impact assessment covering:

- the categories of data that may pass through prompts and responses (source code, configuration, PII if any is inadvertently included, telemetry);
- the model provider, deployment surface, and processing location;
- retention of prompts, responses, and artefacts;
- who has access;
- the legal basis for processing;
- transfer arrangements if the provider processes data outside the UK.

Follow departmental information-management policy and consult the departmental data-protection officer.

## Version pinning

Every run must record:

- model name and version;
- prompt-pack version;
- prompt IDs used in the run;
- agent client name and version.

Behaviour changes across model and prompt versions. Pinning these values is what makes a past finding reproducible and defensible.

## Reconciliation

At the end of each run, reconcile the log against the artefacts produced:

- every finding in a report has an entry in the log showing the prompt and response that produced it;
- every no-finding / not-promoted entry has an entry showing that the area was reviewed;
- every egress connection was to an allowlisted destination.

Investigate and record any inconsistency.
