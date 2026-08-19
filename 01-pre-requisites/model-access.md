# Model access

Departments are responsible for their own AI model access. The toolkit is agnostic to model, provider, and deployment surface.

Before running any prompt, confirm that the department has:

- a live commercial agreement with an approved AI provider covering the models to be used;
- assurance that the agreement, provider, and deployment region are consistent with departmental data-handling and residency policy;
- API credentials or equivalent authentication issued to a named team, not to individuals or shared inboxes;
- a documented process for rotating and revoking credentials.

## Approved deployment surfaces

Any of the following are acceptable, provided the department's commercial and assurance position covers them:

- a direct provider API (for example a first-party Anthropic, OpenAI, or equivalent endpoint);
- a hyperscaler-hosted model (for example an AWS Bedrock or Azure OpenAI deployment);
- a self-hosted or gateway deployment approved by the department.

The toolkit does not prescribe a route. Departments should use the route their commercial agreement, assurance, and information-management position supports.

## Configuration

Configure the agent client (Claude Code, Codex, or equivalent) to use the department's endpoint using the client's supported environment variables or configuration files. Do not paste credentials into prompts, code, notes, or reports. Do not commit credentials to any repository.

Record for the run scope:

- provider;
- deployment surface;
- model name and version;
- endpoint base URL (without credentials);
- credential owner;
- date credentials were last rotated.

## Verification

Before the first substantive prompt, run a short non-sensitive test prompt to confirm:

- the client is using the intended endpoint;
- the client is using the intended model;
- traffic is going through the department's approved route.

Record the verification result and date in the run scope.

## Key handling

Do not:

- share credentials outside the authorised team;
- commit credentials to repositories;
- paste credentials into prompts, notes, chat messages, or reports;
- reuse credentials for unrelated work;
- store credentials in shared documents.

If a credential is exposed, revoke and rotate it immediately, record the incident, and follow the departmental incident-response procedure.

## Version pinning

Record the exact model name, version, and prompt-pack version used for each scan. Model behaviour changes across versions and departments must be able to reproduce, defend, and re-run any historical assessment.
