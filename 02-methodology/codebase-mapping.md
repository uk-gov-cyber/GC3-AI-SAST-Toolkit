# Codebase mapping

Codebase mapping gives the agent and reviewer a working understanding of the repository before vulnerability scanning begins. It also fixes the attacker model and trust boundaries the run will use.

## Aim

Produce a concise map of:

- application entrypoints;
- external interfaces (HTTP, RPC, message queues, file inputs, scheduled jobs);
- authentication and authorisation flows;
- trust boundaries;
- data stores and sensitive data flows;
- file upload and parsing paths;
- outbound network calls;
- privileged operations;
- high-risk dependencies;
- test coverage.

## Threat model

State the attacker classes the run will consider, using values recorded in the run scope (see `../01-pre-requisites/repository-selection.md`). Examples include unauthenticated internet users, self-registered users, authenticated ordinary users, authenticated privileged users, insiders, and compromised dependencies. The map should record, per entrypoint, which attacker classes can reach it and under what preconditions.

## Suggested process

1. Ask the agent to summarise the repository structure.
2. Identify languages, frameworks, and runtime assumptions.
3. Identify entrypoints and trust boundaries.
4. Identify security-sensitive flows.
5. Rank files or components by likelihood of containing meaningful vulnerabilities.
6. Use the map to guide targeted scanning.

## File ranking

A simple 1-5 scale is sufficient.

- 1: unlikely to contain meaningful security logic.
- 2: low-risk helper or static code.
- 3: business logic with limited external input.
- 4: security-sensitive logic or external input handling.
- 5: direct handling of authentication, authorisation, parsing, execution, database queries, file paths, deserialisation, or outbound requests.

Start scanning with the highest-ranked files.

## Output

Create a short mapping note for each repository containing:

- repository purpose;
- main technologies;
- entrypoints and trust boundaries;
- attacker classes considered;
- high-risk areas;
- top files or components for scanning;
- assumptions and unknowns.
