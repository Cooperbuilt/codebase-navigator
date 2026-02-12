# Codebase Navigation

Methodology for answering questions about codebases accessed through ~~code repository. This skill provides the search strategy and reasoning patterns. Company-specific context (which repos exist, what each one does, how services connect) lives in config files.

## Context Loading

On first question in a session, read these config files if they exist:
- `config/repos.md` — Repository inventory and descriptions
- `config/services.md` — Service map, data flows, dependencies
- `config/team-conventions.md` — Naming patterns, branching, config, feature flags

If config files do not exist, inform the user they can run `/setup` to generate them automatically, then proceed with discovery-based navigation.

## Search Strategy

When answering a question, search the codebase in this order. Stop when you have sufficient context — not every question requires all steps.

### 1. Entry Point Discovery

Identify the most likely entry point for the question:
- **Service/feature name mentioned** → Search for matching repo, directory, or module
- **User-facing behavior described** → Start at API routes, event handlers, or UI components
- **Error or symptom described** → Search for error messages, exception handlers, log statements
- **Infrastructure component mentioned** → Check IaC files (Terraform, CDK, CloudFormation) first, then application code that interacts with it

### 2. Follow the Data Flow

From the entry point, trace the execution path:
- What triggers this code? (HTTP request, queue message, cron, event)
- What does it read from? (database, cache, external API, config)
- What does it write to? (database, queue, external API, log)
- What can go wrong? (error handling, retry logic, fallback behavior)

### 3. Check Configuration

Many behavioral questions are answered by configuration, not code:
- Environment variables and `.env` files
- Feature flags and toggle systems
- Infrastructure config (Terraform variables, CDK props, CloudFormation parameters)
- CI/CD pipeline definitions

### 4. Cross-Reference Services

If the answer spans multiple services, map the interaction:
- How do they communicate? (HTTP, gRPC, queue, event bus, shared database)
- What data is passed between them?
- What happens if the downstream service is unavailable?

## Multi-Repo Navigation

When config/repos.md lists multiple repositories:

- **Start in the repo most likely to contain the answer** based on the question and repo descriptions
- **Cross-repo searches** — If a service in repo A calls a service in repo B, search both to give a complete answer. Mention which repo each piece of code lives in.
- **Shared libraries** — If config/repos.md identifies shared/common libraries, check them when you encounter imports you can't resolve in the current repo
- **IaC repo** — If a dedicated infrastructure repo exists, always cross-reference it when answering questions about deployment, scaling, or environment configuration

## When Repos Are Not Configured

If no `config/repos.md` exists:
1. Use ~~code repository to list available repositories
2. Read top-level README files to understand each repo's purpose
3. Look for `package.json`, `pyproject.toml`, `go.mod`, `Cargo.toml` for language/framework identification
4. Look for Terraform, CDK, or CloudFormation directories for infrastructure
5. Build a mental model from these signals and proceed

## Answer Assembly

After gathering context:
- **Synthesize, don't dump.** Combine findings from multiple files into a coherent explanation. Don't list every file you looked at.
- **Include file paths** for the most relevant code so an engineer can locate it: "The retry logic is in `worker-service/src/handlers/payment.py`, specifically the `process_with_retry()` function."
- **Distinguish code from config from infrastructure.** "The timeout is set to 30s in the code (`api/config.py`), but the API Gateway timeout in Terraform (`infra/api.tf`) is 29s — which means the gateway will kill the request before the code's own timeout fires."
- **Note staleness signals.** If a file hasn't been modified in a long time and the question is about current behavior, mention this: "This handler was last modified 8 months ago — the behavior may have shifted if the upstream service changed."
