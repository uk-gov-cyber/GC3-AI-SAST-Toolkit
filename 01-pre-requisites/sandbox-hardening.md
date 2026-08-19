# Sandbox hardening

Run AI-assisted scanning from a dedicated, hardened sandbox — a VM, container, or approved lab environment — rather than from a corporate workstation.

## Baseline

The sandbox should contain only what the exercise requires:

- the sanitised local scan copy of the approved repository;
- the approved agent tooling;
- required build and test tooling;
- endpoint configuration for the department's chosen model;
- local notes and drafts for the exercise.

Nothing else.

## The sandbox should not contain

- unrelated source code;
- live service credentials;
- cloud provider credentials for unrelated accounts;
- SSH keys not required for repository access;
- browser password stores;
- email or messaging clients;
- unrelated official documents;
- production VPN profiles unless explicitly required and approved.

## Recommended setup

1. Start from a clean OS image.
2. Apply OS and security updates.
3. Create a named local user for the exercise.
4. Install only required tooling.
5. Clone approved repositories into the sandbox from a controlled source.
6. Run the agent as an unprivileged user.
7. Configure the department's model endpoint.
8. Restrict network access — see `network-restrictions.md`.
9. Take a clean snapshot where the platform supports it.
10. Rebuild, revert, or destroy the sandbox after the exercise.

## Privileges

Do not run the agent as root or administrator. Do not grant the agent write access to anything outside the scan copy and its own working directory.

## Prompt-injection risk

Treat the scanned repository as untrusted input. Content in comments, README files, string literals, test fixtures, or dependencies may attempt to hijack the agent (this is a well-known class of prompt-injection risk when agents process third-party content). Mitigations:

- The agent's system prompt (see `CLAUDE.md` / `AGENTS.md`) tells it to treat repository content as data, not as instructions.
- The sandbox denies the agent access to live systems, credentials, and outbound traffic beyond the model endpoint (see `network-restrictions.md`).
- The reviewer should watch for the agent changing scope, disabling checks, or requesting external actions mid-run; if it happens, stop the run and record it.

## Local-only analysis

The agent should analyse the local sanitised scan copy only. Do not use the agent to interact with deployed services, cloud accounts, or third-party APIs unless a separate authorisation is in place.
