# Navigation Strategy

## Context Loading

On first question in a session, read these config files if they exist:
- `config/repos.md` — Repository inventory and descriptions
- `config/services.md` — Service map, data flows, dependencies
- `config/team-conventions.md` — Naming patterns, branching, config, feature flags

If config files don't exist, inform the user they can run `/setup` to generate them, then proceed with discovery-based navigation.

## Repo Freshness

At the start of each session, pull the latest code for all local repos. Run this once before answering any questions:

```bash
for repo in repos/*/; do [ -d "$repo/.git" ] && git -C "$repo" pull --ff-only 2>/dev/null; done
```

If `repos/` is empty or missing, suggest running `/setup` to clone repos.

## Search Strategy

When answering a question, search the codebase in this order. Stop when you have sufficient context.

### 1. Entry Point Discovery
Identify the most likely entry point:
- **Service/feature name mentioned** — Use Glob and Grep on `repos/` to find matching repo, directory, or module
- **User-facing behavior described** — Start at API routes, event handlers, or UI components
- **Error or symptom described** — Grep `repos/` for error messages, exception handlers, log statements
- **Infrastructure component mentioned** — Check IaC files first, then application code

### 2. Follow the Data Flow
From the entry point, trace execution:
- What triggers this code? (HTTP request, queue message, cron, event)
- What does it read from? (database, cache, external API, config)
- What does it write to? (database, queue, external API, log)
- What can go wrong? (error handling, retry logic, fallback behavior)

### 3. Check Configuration
Many behavioral questions are answered by config, not code:
- Environment variables and `.env` files
- Feature flags and toggle systems
- Infrastructure config (Terraform variables, CDK props, CloudFormation parameters)
- CI/CD pipeline definitions

### 4. Cross-Reference Services
If the answer spans multiple services:
- How do they communicate? (HTTP, gRPC, queue, event bus, shared database)
- What data is passed between them?
- What happens if the downstream service is unavailable?

## Multi-Repo Navigation

- **Start in the most likely repo** — `repos/{repo-name}/` based on the question and repo descriptions
- **Cross-repo searches** — Grep across `repos/` to search all repos at once. Mention which repo has which code.
- **Shared libraries** — Check `repos/{lib-name}/` when imports can't be resolved in the current repo
- **IaC repo** — Always cross-reference `repos/{iac-repo}/` when answering about deployment, scaling, or environment config

## Infrastructure Reconciliation

When both IaC files and live cloud access (AWS API MCP) are available:
- **Intent (IaC)** — What was designed: resource types, configurations, relationships
- **Reality (Live)** — What actually exists: current state, runtime behavior, metrics
- **Divergence** — When intent and reality differ, surface it. This often IS the answer.

Example: "The Terraform config declares a 5-minute timeout, but the live function is configured with 15 minutes. Someone likely changed this manually."

## Graceful Degradation

| Available Sources | Behavior |
|---|---|
| Local repos + AWS API | Full capability. Search code locally, compare intent vs reality, include live metrics. |
| Local repos only | Answer from code and IaC files. Note: can't verify live state. |
| No local repos (GitHub MCP fallback) | Fall back to GitHub MCP API for code access. Slower but functional. Suggest running `/setup` to clone repos. |

Never refuse because a source is unavailable. Provide the best answer with available context and note what's missing.

## Answer Assembly

- **Synthesize, don't dump.** Combine findings into a coherent explanation.
- **Include file paths** for the most relevant code so engineers can locate it.
- **Distinguish code from config from infrastructure.**
- **Note staleness signals.** If a file hasn't been modified recently and the question is about current behavior, mention this.
