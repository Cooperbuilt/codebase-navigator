---
name: navigation-strategy
description: Codebase search and navigation methodology. Defines how to find answers by tracing entry points, data flows, config, and cross-service dependencies. Use when investigating any codebase question.
---

# Navigation Strategy

## Context Loading

On first question in a session, read these config files if they exist:
- `config/repos.md` — Repository inventory and descriptions
- `config/services.md` — Service map, data flows, dependencies
- `config/team-conventions.md` — Naming patterns, branching, config, feature flags

If config files don't exist, inform the user they can run `/setup` to generate them, then proceed with discovery-based navigation.

## Search Strategy

When answering a question, search the codebase in this order. Stop when you have sufficient context.

### 1. Entry Point Discovery
Identify the most likely entry point:
- **Service/feature name mentioned** — Search for matching repo, directory, or module via the GitHub MCP server
- **User-facing behavior described** — Start at API routes, event handlers, or UI components
- **Error or symptom described** — Search for error messages, exception handlers, log statements
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

- **Start in the most likely repo** based on the question and repo descriptions
- **Cross-repo searches** — If a service in repo A calls repo B, search both. Mention which repo has which code.
- **Shared libraries** — Check them when imports can't be resolved in the current repo
- **IaC repo** — Always cross-reference when answering about deployment, scaling, or environment config

## Infrastructure Reconciliation

When both IaC files and live cloud access (CloudWatch, CloudFormation MCP) are available:
- **Intent (IaC)** — What was designed: resource types, configurations, relationships
- **Reality (Live)** — What actually exists: current state, runtime behavior, metrics
- **Divergence** — When intent and reality differ, surface it. This often IS the answer.

Example: "The Terraform config declares a 5-minute timeout, but the live function is configured with 15 minutes. Someone likely changed this manually."

## Graceful Degradation

| Available Sources | Behavior |
|---|---|
| GitHub + CloudWatch + CloudFormation | Full capability. Compare intent vs reality. Include live metrics. |
| GitHub + IaC files only | Answer from intended architecture. Note: can't verify live state. |
| GitHub only | Answer from code. Note what cloud access would add. |

Never refuse because a source is unavailable. Provide the best answer with available context and note what's missing.

## Answer Assembly

- **Synthesize, don't dump.** Combine findings into a coherent explanation.
- **Include file paths** for the most relevant code so engineers can locate it.
- **Distinguish code from config from infrastructure.**
- **Note staleness signals.** If a file hasn't been modified recently and the question is about current behavior, mention this.
