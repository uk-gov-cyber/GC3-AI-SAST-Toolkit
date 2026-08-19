# Reviewer feedback checklist

Use this checklist when reviewing the reporting method, prompts, templates, and generated outputs.

## What to review

- `README.md` (toolkit root);
- `02-methodology/`;
- `03-prompt-guidance/`;
- `04-reporting-bundle/README.md`, `reporting-standards.md`, and `templates/`;
- generated reports and machine-readable artefacts.

## Questions for engineers and developers

### Evidence

- Are repository URL, commit SHA, file path, line range, and code excerpts sufficient to locate the issue?
- Are source, sink, and path explanations clear enough to reproduce the reasoning locally?
- Are test gaps or missing fixture evidence stated clearly?
- Is the static PoC or demonstration safe and useful?

### Interpretation

- Is the distinction between confirmed source defect and unvalidated exploitability clear?
- Is the conditional severity language accurate and hard to misquote?
- Are assumptions, preconditions, and validation gaps explicit enough?
- Does the negative-control section fairly describe possible false-positive conditions?

### Remediation

- Are remediation actions specific enough to start engineering work?
- Are remediation and validation kept separate?
- Are ownership expectations realistic?
- Are any recommendations too broad, too narrow, or likely to cause unnecessary churn?

### Validation

- Are validation actions assignable to the right team type?
- Is the required evidence clear enough for engineers or operators to report back?
- Are PASS / FAIL / INCONCLUSIVE outcomes meaningful?
- Are any validation steps unsafe, impractical, or better suited to a scoped test environment?

### Attribution and routing

- Are portfolio, service line, product, and owner-confidence fields useful?
- Are attribution gaps and owner-confirmation actions clear?
- Are custom properties, IaC tags, topics, CODEOWNERS, and README signals weighted appropriately?

### Artefact fit

- Is SARIF useful for developer ingestion?
- Is OPTRS useful as the structured report container?
- Is VXDF non-issuance clear for unvalidated chains?
- Is the whole-exercise report structure useful for senior readers?
- Is the report reconciliation register useful for tracking where each issue went?
- Are engineering work items and owner delivery packs clear enough to hand off without losing evidence-state wording?

### Packaging

- Are excluded files appropriate for sharing?
- Are cloned repositories, raw secrets, local tokens, raw scanner archives, scratch files, and temporary files absent?
- Are checksums and manifests useful without exposing internal working-directory noise?

## Feedback format

For each comment, please include:

| Field | Guidance |
|---|---|
| File | Path being reviewed. |
| Section | Heading or line if known. |
| Issue | What is unclear, missing, inaccurate, or unhelpful. |
| Impact | Why it matters for engineering, validation, routing, or decision-making. |
| Suggested change | Preferred wording, structure, evidence, or process change. |

## Review outcomes

Use one of:

- `Accept as-is`;
- `Accept with minor edits`;
- `Needs revision before use`;
- `Not suitable for this audience`.

Where possible, separate format feedback from disagreement with the underlying security conclusion.
