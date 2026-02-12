# Setup

Guided onboarding that auto-generates the plugin's config files by scanning your connected repositories and infrastructure. Run this once when first installing the plugin, or re-run to update after architectural changes.

## Usage

```
/setup
/setup --refresh
```

- No arguments: full setup from scratch
- `--refresh`: re-scan and update existing config files, preserving manual edits where possible

## Workflow

### Step 1: Detect Connected MCP Servers

Check which MCP servers are available:
- ~~code repository (GitHub, GitLab, etc.)
- ~~cloud observability (CloudWatch, Datadog, etc.)
- ~~cloud resources (CloudFormation, Cloud Control, etc.)

Report what's connected and what additional connectors would unlock more capability. Do not block setup on missing connectors.

### Step 2: Discover Repositories

Using ~~code repository:
1. List accessible repositories in the user's organization
2. For each repo, read: README, package manifest (package.json, pyproject.toml, go.mod, etc.), top-level directory structure
3. Present the list and ask: "Which of these repos should I include? Are there any I should skip (deprecated, internal tooling, forks)?"

### Step 3: Generate config/repos.md

For each included repo, generate an entry:

```markdown
## <repo-name>

- **Purpose:** [extracted from README or inferred from code]
- **Language/Framework:** [detected from package manifest]
- **Type:** [api | worker | frontend | infrastructure | library | monorepo]
- **Key entry points:** [main files, route handlers, event handlers]
```

Present the draft to the user for review. They may correct descriptions, add context, or flag relationships between repos.

### Step 4: Generate config/services.md

If IaC files are found (Terraform, CDK, CloudFormation):
1. Parse resource definitions to map deployed services
2. Identify service-to-service connections (queues, API calls, shared databases)
3. Generate a service dependency map

If no IaC is found, infer from application code:
1. Look for HTTP client calls, SDK usage, queue producers/consumers
2. Look for database connection configurations
3. Build a best-effort service map

Present the draft for review.

### Step 5: Generate config/team-conventions.md

Scan repos for:
- Linter/formatter configs → coding style conventions
- Branch naming patterns → branching strategy
- CI/CD pipeline definitions → deployment process
- .env.example files → configuration patterns
- Feature flag usage → toggle system identification

Generate a conventions summary. Present for review.

### Step 6: Generate config/guardrails.md

Create with sensible defaults:

```markdown
# Guardrails

## Never Provide
- Effort or time estimates (hours, days, story points, t-shirt sizes)
- Build/buy/feasibility recommendations
- Production debugging commands or instructions
- Security-sensitive details (keys, secrets, connection strings, vulnerability specifics)

## Always Escalate To Engineering When
- The question requires live production debugging
- The answer depends on runtime state that can't be determined from code
- A potential security vulnerability is discovered

## Team-Specific Rules
[User adds their own rules here]
```

Ask the user if they want to add any team-specific rules.

### Step 7: Summary

Report what was generated:
```
Setup complete. Generated:
  ✓ config/repos.md — X repositories mapped
  ✓ config/services.md — X services, X dependencies mapped
  ✓ config/team-conventions.md — conventions detected
  ✓ config/guardrails.md — default guardrails applied

You can edit these files anytime. Run /setup --refresh to re-scan.
Try /ask or /explain-service to get started.
```

## Notes

- Setup is conversational — ask for confirmation at each step, don't dump everything at once.
- Prefer auto-detection with user correction over asking the user to fill in everything manually.
- If --refresh is used, diff new findings against existing config and present changes for approval rather than overwriting.
- Config files should be human-readable and human-editable. No JSON, no YAML — use clean markdown.
