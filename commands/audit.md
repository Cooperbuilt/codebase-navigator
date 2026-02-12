# Audit

Run a comprehensive codebase health assessment across all configured repositories. Produces a scored report across 7 dimensions with specific evidence and actionable recommendations.

## Usage

```
/audit
/audit <specific-repo-name>
/audit --dimensions security,technical-debt
```

## Arguments

- No arguments: audit all repos in `config/repos.md`
- Repo name: audit only that repository
- `--dimensions`: comma-separated list to audit only specific dimensions (code-quality, architecture, scalability, error-handling, documentation, security, technical-debt)

## Workflow

1. **Scope confirmation.** List the repos and dimensions that will be audited. Ask the user to confirm before proceeding — this is a long-running operation.

2. **Per-repo analysis.** Follow the audit process defined in the audit-rubric skill:
   - Read project structure and identify language/framework
   - Sample representative code files (entry points, core logic, data access, tests, config)
   - Read infrastructure code
   - Check for quality tooling in CI

3. **Score each dimension** using the rubric in `references/audit-scoring-guide.md`. Every score must include positive findings, issues, and a top recommendation — all with file paths.

4. **Generate the report** using the template in `references/audit-output-template.md`.

5. **Deliver the report.** Present the summary table and executive summary inline. Offer to save the full report as a markdown file.

## Notes

- Do NOT apply XY problem detection or semi-technical voice calibration. The audit produces a technical report intended for engineering leadership, not PM-level conversation.
- The audit is read-only. Never suggest or make code changes.
- "Effort signal" in recommendations uses complexity (low/medium/high), NOT time estimates. This is the one place where relative complexity is appropriate because it helps prioritize, but it must be framed as complexity of the change, not effort to implement it.
- If a repository is very large, state which areas were sampled and note that the audit is representative, not exhaustive.
- Security findings should be flagged but not expose specific secrets or vulnerability details in the report. Say "API keys found in source files" not "found AWS key AKIA... in config.py".
