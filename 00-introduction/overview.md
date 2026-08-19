# Overview

The GC3 AI SAST Toolkit supports government departments running AI-assisted static application security testing (SAST) against their own bespoke source code.

## Aim

Use a coding agent to identify, validate, and report realistic, reachable, security-relevant vulnerabilities in departmental repositories, with defensible evidence and traceable coverage records.

## What this toolkit provides

- pre-requisite guidance for safe local scanning environments;
- a methodology for mapping, scanning, validating, and reporting;
- a staged prompt pack based on a pilot approach that has been shown to work in departmental use, plus a one-shot fallback;
- a reporting bundle with reporting standards, review checklist, and templates.

## What this toolkit does not provide

- an AI model or model access — departments are responsible for their own commercial agreement with an AI provider and hold the API credentials themselves;
- validation of findings — AI-generated output is candidate analysis until a human reviewer has validated it;
- remediation or risk-acceptance decisions.

## Core principles

- Analyse sanitised local repository copies only.
- Remove or exclude personal data, secrets, and git metadata before scanning.
- Do not expose commit author names, email addresses, or commit history to the model.
- Do not interact with live systems unless explicitly authorised.
- Use a dedicated, hardened sandbox where possible.
- Restrict network access to what the exercise requires.
- Prefer evidence-led findings over speculative ones. Cite `file:line` for every code claim.
- Separate source-confirmed behaviour from runtime-validated behaviour.
- Challenge every candidate finding with a negative-control prompt that argues the strongest case that the finding is a false positive.
- Preserve reviewed-but-not-promoted candidates so future reviewers know what was covered.
- Set the severity threshold, attacker model, and reporting threshold for each run in the run scope. The toolkit does not fix these values.

## Scope of AI SAST

AI SAST is a useful complement to established controls, not a replacement. Departments should continue to run their existing DAST, software composition analysis, secret scanning, manual code review, and threat-modelling activities.

The toolkit focuses on realistic vulnerabilities with practical impact — for example remote code execution, injection classes, authentication or authorisation flaws, server-side request forgery, insecure deserialisation, path traversal, template injection, XML external entity issues, cryptographic failures with practical impact, hard-coded credentials, and composed chains. This list is illustrative, not exhaustive; departments may broaden or narrow the target classes in the run scope.

## Human ownership

AI output is a starting point. Departments remain responsible for repository selection, sanitisation, environment security, validation, remediation, risk acceptance, and handling of departmental data. Findings must be handled through the agreed departmental reporting route.

## Post-remediation

After remediation, re-run the relevant staged prompt against the fixed code and update the evidence state and reporting decision. Do not close a finding on remediation alone — record the verification evidence.
