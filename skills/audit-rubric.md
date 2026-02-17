# Audit Rubric

Scoring methodology for the `/audit` command. Produces a health assessment across 7 dimensions with scores backed by specific evidence from the codebase. See `references/audit-scoring-guide.md` for detailed per-dimension scoring criteria with examples.

## Audit Process

### Scope

Audit all repositories listed in `config/repos.md`. If no config exists, audit whatever repositories are cloned in `repos/` and note which repos were included.

### Per-Repository Analysis

For each repository, use local tools on `repos/{repo-name}/` to:

1. **Read project structure** — top-level directory layout, package manifests, README
2. **Identify language and framework** — determines which quality signals to check
3. **Sample code files** — read a representative sample, not every file. Prioritize:
   - Entry points (main files, route handlers, event handlers)
   - Core business logic (domain models, services, processors)
   - Data access layer (database queries, API clients)
   - Configuration and environment handling
   - Test files (to assess test quality)
4. **Read infrastructure code** — Terraform, CDK, CloudFormation, Dockerfiles, CI/CD config
5. **Check for tooling** — linters, formatters, type checkers, security scanners in CI

### Scoring

Each dimension scored 1-10. Every score MUST include:
- **2-3 specific positive findings** with file paths
- **2-3 specific issues** with file paths
- **One concrete recommendation** that would most improve the score

Scoring anchors:
- **1-3**: Critical problems. Missing fundamental practices.
- **4-5**: Below average. Basics present but significant gaps.
- **6-7**: Solid. Good practices with room for improvement.
- **8-9**: Strong. Consistent quality with minor gaps.
- **10**: Exceptional. Would use as a reference for other teams.

## The Seven Dimensions

### 1. Code Quality
What to assess: consistency, complexity, naming, DRY, linting, type safety.

Signals: linter config (.eslintrc, ruff.toml, .prettierrc), type checking (TypeScript strict, Python type hints + mypy/pyright), code complexity (deep nesting, long functions, god classes), naming consistency, copy-paste patterns.

### 2. Architecture
What to assess: separation of concerns, dependency direction, coupling, module boundaries.

Signals: directory structure (feature/domain vs flat), import patterns (abstractions vs concrete), circular dependencies, layer violations (HTTP handlers with business logic, DB queries in controllers), service scope.

### 3. Scalability
What to assess: statelessness, concurrency, resource limits, caching, queue usage.

Signals: hardcoded limits, in-memory state, database query patterns (N+1, missing indexes), async/queue usage for long operations, caching strategy, infrastructure scaling config.

### 4. Error Handling
What to assess: coverage, specificity, propagation, recovery, observability.

Signals: bare/generic exception catches, swallowed errors, error propagation with context, retry logic (backoff, limits), graceful degradation, user-facing error quality.

### 5. Documentation
What to assess: README quality, inline comments, API docs, type coverage, onboarding.

Signals: README (purpose, run, deploy), API docs (OpenAPI/Swagger), inline comments for non-obvious logic, type definitions, architecture docs (diagrams, ADRs), onboarding friction.

### 6. Security
What to assess: secrets management, auth patterns, input validation, dependency health, IAM scope.

Signals: secrets in code (report existence, not values), .gitignore coverage, input validation, auth/authz patterns, dependency vulnerabilities, IAM permissions scope, HTTPS/TLS enforcement.

### 7. Technical Debt
What to assess: TODO density, deprecated usage, dead code, workarounds, version staleness.

Signals: TODO/FIXME/HACK/WORKAROUND comments, deprecated API usage, dead code, dependency staleness, half-completed migrations, test coverage gaps.

## Output Format

See `references/audit-output-template.md` for the full report template. Overall score is the unweighted mean of all 7 dimensions, rounded to one decimal. If `config/guardrails.md` specifies custom weights, use those instead.
