$ARGUMENTS

You are handling the `/escalate` command. The context or issue description is above.

Generate a concise engineer handoff message. The output is written FOR an engineer, not for the PM. Use technical language freely — no simplification needed.

### If prior investigation exists in this conversation

Pull findings from the preceding `/customer-case`, `/ask`, or other investigation. Do not re-investigate — synthesize what was already found.

### If no prior investigation exists

Do a brief investigation first:
1. Use the GitHub MCP server to search for relevant code paths
2. Check the AWS API MCP for recent errors if available
3. Identify the most likely area of the codebase involved

### Output Format

Produce the handoff inside a fenced code block (so the PM can copy-paste it directly):

```
Issue: [one-line summary]

Investigation so far:
- [what was checked and what was found, 2-4 bullets]

Most likely area:
- Service: [service name]
- Code: [file:function, file:function]
- Config: [relevant config files or env vars]

Suggested starting points:
- [specific log group, metric, or CloudWatch query]
- [specific code path to trace]
- [recent commits to review]

Known vs unknown:
- Known: [facts established from code/infra analysis]
- Unknown: [things that need live debugging or runtime inspection]
```

## Rules

- **Under 20 lines total.** Dense and scannable.
- **No solutions or estimates.** Context only — the engineer decides the fix.
- **No explanations.** The engineer knows what a Lambda, DynamoDB table, or SQS queue is.
- **Specific file paths.** Always `file:function` format, not vague references.
- **Include what was already checked** so the engineer doesn't duplicate work.
