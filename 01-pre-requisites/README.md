# Pre-requisites

Complete every item in this section before running any prompt. Record completion in the run scope.

## Checklist

1. **Model access** — the department has a commercial agreement with an approved AI provider and holds the API credentials. See `model-access.md`.
2. **Sandbox hardening** — a dedicated VM, container, or approved lab environment is ready with only the tooling the exercise requires. See `sandbox-hardening.md`.
3. **Network restrictions** — an egress allowlist is in place and the environment cannot reach production systems, cloud metadata endpoints, or unrelated internal networks. See `network-restrictions.md`.
4. **Monitoring and logging** — prompt, response, and artefact logging is enabled with a documented retention window and a data-protection assessment has been completed. See `monitoring-and-logging.md`.
5. **Repository selection** — the repository is approved for the exercise, in-scope, and owned by a team available to support validation. See `repository-selection.md`.
6. **Repository preparation** — a sanitised local scan copy has been prepared with personal data, secrets, and git metadata removed or excluded. See `repository-preparation.md`.

Record which items are complete and who signed off each item before the first prompt is run.
