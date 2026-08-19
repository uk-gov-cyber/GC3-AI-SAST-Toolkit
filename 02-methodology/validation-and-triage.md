# Validation and triage

Validation determines whether a candidate finding is real, reachable, and severe enough to promote.

## Validation questions

For each candidate finding, answer:

- What is the attacker-controlled source?
- What is the dangerous sink?
- What path connects them?
- What checks, guards, or sanitisation exist?
- Is the path reachable in a realistic deployment for the attacker classes in scope?
- What privileges are required?
- What impact is realistically achievable?
- Is there a safe local proof of concept or test?
- What would make this a false positive?
- Does the evidence avoid unnecessary personal data, secrets, and git metadata?

## Evidence requirements

Every code-level claim should have a `file:line` citation.

Minimum useful evidence:

- source;
- sink;
- path;
- relevant guards or absence of guards;
- tests or absence of tests;
- reachability against the attacker classes in scope;
- impact explanation.

Do not include live secrets, unnecessary personal data, commit author names, or commit author email addresses in findings or evidence.

## Confirmation

Use a capable coding agent to challenge and confirm the finding. Where possible, use a different model or tool from the one that discovered the candidate. At minimum, run:

- a source-to-sink trace;
- a sanitisation or guard check;
- a type-specific confirmation appropriate to the vulnerability class;
- a negative-control prompt.

## Negative-control check (mandatory)

Always ask the agent to argue the strongest case that the finding is a false positive.

If the negative-control check identifies a real guard, constraint, or environmental condition that prevents exploitation, reassess the finding. If the finding is disconfirmed, record it as `source-disconfirmed` or `not promoted` with the reason and preserve it in the coverage register — do not silently drop it.

## Local validation

Local validation — running a unit test, harness, or local container inside the sandbox to reproduce the behaviour — is a useful strengthener where safe. Label the outcome as `locally-validated`. Do not use local validation as shorthand for production exploitability. Production or deployed claims require separately authorised runtime evidence held by the responsible owner.

## Triage

Apply the severity threshold and attacker model recorded in the run scope. The toolkit does not set these values; the department does.

If a candidate does not meet the department's threshold, record it in the no-finding / not-promoted coverage register with a short reason and its evidence state. If it does meet the threshold, promote it to a report using `04-reporting-bundle/`.

## Chained findings

Composed chains — where multiple lower-severity issues combine into higher-impact behaviour — should be recorded as a chain report with stable IDs linking to the constituent findings. Chains follow the same evidence gates as individual findings.
