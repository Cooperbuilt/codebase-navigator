$ARGUMENTS

You are handling the `/setup` command. If `--refresh` is passed above, update existing config files instead of creating from scratch.

This command configures MCP servers and generates config files that personalize the plugin for the user's team and codebase.

## Instructions

### Step 1: Check MCP Server Connections

Verify which MCP servers are connected:

**GitHub MCP** (required):
- Try a simple GitHub API call to verify the connection
- If not connected, help the user configure `.mcp.json`:
  1. They need a GitHub PAT from https://github.com/settings/tokens with `repo` scope
  2. Replace `<YOUR_GITHUB_PAT>` in `.mcp.json` with their token
  3. Restart Claude Code to pick up the new config

**AWS API MCP** (optional):
- Check if the AWS MCP server is available
- If not connected, explain what it adds (live logs, metrics, deployed resource state) and how to set it up:
  1. Install `uv`: `curl -LsSf https://astral.sh/uv/install.sh | sh`
  2. Get a read-only AWS access key ID, secret access key, and region from their engineering team
  3. Add the `aws` entry to `.mcp.json` with credentials in the `env` block (see `docs/SETUP-GUIDE.md`)
  4. Restart Claude Code

Report what's connected. Do not block setup on missing AWS — GitHub is sufficient.

### Step 2: Discover Repositories

Using the GitHub MCP server:
1. List accessible repositories in the user's organization
2. For each repo, read: README, package manifest (package.json, pyproject.toml, go.mod, etc.), top-level directory structure
3. Present the list and ask: "Which of these repos should I include? Any I should skip (deprecated, internal tooling, forks)?"

### Step 3: Clone Selected Repos

Clone each user-selected repo to `repos/{repo-name}/`:

1. Extract the GitHub PAT from `.mcp.json` (the `Authorization: Bearer <token>` header)
2. Create the `repos/` directory if it doesn't exist
3. For each selected repo, clone via HTTPS:
   ```
   git clone https://{token}@github.com/{org}/{repo}.git repos/{repo-name}
   ```
4. If `--refresh` was passed and the repo already exists in `repos/`, run `git pull --ff-only` instead of re-cloning
5. Report which repos were cloned successfully and any failures. Continue past failures — partial clones are fine.

Never display the token in output. Use full clones (not `--depth 1`) so `git log` works for commit history investigation.

### Step 4: Generate config/repos.md

For each included repo, read from the local clone in `repos/{repo-name}/` to generate an entry with: purpose, language/framework, type (api/worker/frontend/infrastructure/library/monorepo), key entry points. Present the draft for user review.

### Step 5: Generate config/services.md

If IaC files are found in the local repos, parse resource definitions to map deployed services and connections. If no IaC, infer from application code (HTTP clients, SDK usage, queue producers/consumers, database configs). Generate a service dependency map. Present for review.

### Step 6: Generate config/team-conventions.md

Scan local repos for: linter/formatter configs, branch naming patterns, CI/CD pipeline definitions, .env.example files, feature flag usage. Generate a conventions summary. Present for review.

### Step 7: Generate config/guardrails.md

Create with sensible defaults (no estimates, no prod debugging instructions, no secrets, escalation rules). Ask the user if they want to add team-specific rules.

### Step 8: Summary

Report what was generated:
```
Setup complete. Generated:
  repos/ — X repositories cloned
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
- All config files should be written to the project directory.
- Never display GitHub tokens or AWS credentials in output.
