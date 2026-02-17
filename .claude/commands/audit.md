$ARGUMENTS

You are handling the `/audit` command. Arguments above may specify a repo name and/or `--dimensions` flag.

Read `skills/audit-rubric.md` for the full scoring methodology and dimension definitions.

## Argument Parsing

- No arguments: audit all repos in `config/repos.md`
- Repo name: audit only that repository
- `--dimensions security,technical-debt`: audit only specified dimensions
- Valid dimensions: code-quality, architecture, scalability, error-handling, documentation, security, technical-debt

## Instructions

1. **Scope confirmation.** List the repos and dimensions that will be audited. Ask the user to confirm before proceeding — this is a long-running operation.

2. **Per-repo analysis.** Use local tools (Read, Grep, Glob) on `repos/{repo-name}/` to:
   - Read project structure, package manifests, README
   - Identify language and framework
   - Sample representative code files (entry points, core logic, data access, tests, config)
   - Read infrastructure code (Terraform, CDK, CloudFormation, Dockerfiles, CI/CD)
   - Check for quality tooling in CI

3. **Score each dimension** 1-10 per the rubric. Every score must include:
   - 2-3 specific positive findings with file paths
   - 2-3 specific issues with file paths
   - One concrete recommendation

4. **Generate the report** using the template in `references/audit-output-template.md`.

5. **Deliver.** Present the summary table and executive summary inline. Offer to save the full report as a markdown file.

## Notes

- Do NOT apply XY problem detection or voice calibration. The audit produces a technical report for engineering leadership.
- "Effort signal" uses complexity (low/medium/high), NOT time estimates.
- If a repo is very large, state which areas were sampled and note the audit is representative, not exhaustive.
- Overall score is the unweighted mean of all 7 dimensions, rounded to one decimal.
