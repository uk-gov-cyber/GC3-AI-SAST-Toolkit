# Repository preparation

Scanning is performed against a sanitised local clone of an approved in-scope repository. The clone should contain the code under review and nothing more.

## Before cloning

Confirm that:

- the repository is approved for the exercise;
- the repository owner is aware of the activity;
- the sandbox is ready (see `sandbox-hardening.md`);
- local storage is appropriate for the sensitivity of the code.

## Clone

Clone the repository into the sandbox using departmental procedures. Record:

- repository URL;
- branch;
- commit SHA;
- date cloned;
- person or team performing the scan.

## Sanitisation — remove or exclude before scanning

Personal data, secrets, and repository metadata must not be exposed to the model. Before the first prompt, remove or exclude:

- **Git metadata.** Remove or exclude `.git/`, `.gitignore` shim files that expose author metadata, and anything else that would reveal commit history, commit author names, or commit author email addresses. A common approach is to check out the target commit and delete the `.git/` directory in the scan copy.
- **Live secrets and credentials.** Search the working tree for tokens, API keys, database URLs with embedded credentials, private keys, and certificates. Redact any live value found as `<REDACTED:purpose>` in the scan copy and follow the departmental secret-rotation procedure for the original.
- **Personal data.** Remove or redact test fixtures, sample data, seeded database dumps, or configuration that contains real personal data.
- **Environment files.** Remove `.env`, `.envrc`, local development configuration, and IDE workspace files that may contain credentials or personal information.
- **Unrelated files.** Remove any file, directory, or archive that is not part of the code under review.

## Working tree hygiene

Before scanning:

- confirm the branch and commit under review;
- avoid unrelated local edits;
- do not scan multiple unrelated repositories in the same agent context — clear the context between them;
- exclude files that are explicitly out of scope for the run.

## Dependencies

Some validation activity may require dependencies or test fixtures. Departments should decide whether to:

- install dependencies locally inside the sandbox;
- run tests in an isolated container;
- perform static-only review;
- ask maintainers to support runtime validation outside the AI SAST run.

Do not install or execute dependencies unless this is consistent with departmental security policy. Dependency installation must respect the network allowlist in `network-restrictions.md`.

## Suspected hard-coded secrets

If a suspected hard-coded secret is discovered during scanning:

- do not paste the live value into prompts, notes, or reports;
- redact the value as `<REDACTED:purpose>`;
- record the discovery through the departmental incident and secret-rotation procedure;
- treat the discovery as a candidate finding that requires confirmation and severity assessment against the department's threshold.

## Per-scan record

For each repository, maintain a short scan record containing:

- repository name;
- commit SHA;
- sandbox identifier;
- model name and version;
- prompt-pack version and prompt IDs used;
- date of scan;
- findings promoted;
- candidates reviewed and not promoted (see `04-reporting-bundle/templates/no-finding-coverage.md`).
