# Repository selection

Departments are responsible for selecting repositories for AI SAST activity. The aim is to select repositories where AI-assisted review is likely to identify meaningful security issues and where findings can be validated by appropriate technical owners.

## Selection criteria

Prioritise repositories that:

- contain bespoke departmental code;
- support important public, operational, or internal services;
- process sensitive data, authentication, authorisation, payments, casework, or workflow decisions;
- expose APIs, web interfaces, message consumers, or file upload paths;
- have known complexity, legacy components, or limited recent security review;
- have technical owners available to support validation.

## Lower-priority repositories

Deprioritise repositories that:

- are inactive or obsolete;
- contain only static content, documentation, or configuration with no meaningful execution path;
- are entirely third-party code;
- cannot be validated by current maintainers;
- are out of scope for departmental approval.

## Third-party code

The activity should focus on bespoke code. If a potential vulnerability is identified in third-party code, package code, or an external dependency, do not treat it as an ordinary departmental finding. Record the issue and follow the agreed route for third-party vulnerability handling — typically supplier disclosure, upstream reporting, or software composition analysis triage.

## Attacker model and severity threshold

For each run, record:

- the assumed attacker classes (for example unauthenticated internet user, self-registered user, authenticated ordinary user, authenticated privileged user, insider, compromised dependency);
- the severity threshold for reporting through the departmental route.

These values are set by the department, not by the toolkit. The agent will apply the values the reviewer provides.

## Minimum information to record per repository

- department;
- repository name;
- repository URL;
- system or service owner;
- technical point of contact;
- purpose of the repository;
- sensitivity of code and data;
- expected validation owner;
- assumed attacker classes;
- severity threshold for reporting;
- reason for selection;
- exclusions or constraints.

## Output

Create a shortlist of approved repositories before scanning begins. Only scan repositories that are explicitly in scope, and only scan from a sanitised local clone.
