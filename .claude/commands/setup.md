$ARGUMENTS

You are handling the `/setup` command. If `--refresh` is passed above, update existing config files instead of creating from scratch.

## Instructions

### Step 1: Detect MCP Servers

Check which MCP servers are available:
- **GitHub MCP** (`github`) — Required for code access
- **AWS CloudWatch MCP** (`aws-cloudwatch`) — Optional, enables live observability
- **AWS CloudFormation MCP** (`aws-cfn`) — Optional, enables deployed resource inspection

Report what's connected and what additional servers would unlock. Do not block setup on missing servers.

### Step 2: Discover Repositories

Using the GitHub MCP server:
1. List accessible repositories in the user's organization
2. For each repo, read: README, package manifest (package.json, pyproject.toml, go.mod, etc.), top-level directory structure
3. Present the list and ask: "Which of these repos should I include? Any I should skip (deprecated, internal tooling, forks)?"

### Step 3: Generate config/repos.md

For each included repo, generate an entry with: purpose, language/framework, type (api/worker/frontend/infrastructure/library/monorepo), key entry points. Present the draft for user review.

### Step 4: Generate config/services.md

If IaC files are found, parse resource definitions to map deployed services and connections. If no IaC, infer from application code (HTTP clients, SDK usage, queue producers/consumers, database configs). Generate a service dependency map. Present for review.

### Step 5: Generate config/team-conventions.md

Scan repos for: linter/formatter configs, branch naming patterns, CI/CD pipeline definitions, .env.example files, feature flag usage. Generate a conventions summary. Present for review.

### Step 6: Generate config/guardrails.md

Create with sensible defaults (no estimates, no prod debugging instructions, no secrets, escalation rules). Ask the user if they want to add team-specific rules.

### Step 7: Summary

Report what was generated:
```
Setup complete. Generated:
  config/repos.md — X repositories mapped
  config/services.md — X services, X dependencies mapped
  config/team-conventions.md — conventions detected
  config/guardrails.md — default guardrails applied

You can edit these files anytime. Run /setup --refresh to re-scan.
Try /ask or /explain-service to get started.
```

## Notes

- Setup is conversational — ask for confirmation at each step.
- Prefer auto-detection with user correction over manual input.
- For `--refresh`, diff new findings against existing config and present changes for approval.
- Config files are clean markdown — human-readable and human-editable.
