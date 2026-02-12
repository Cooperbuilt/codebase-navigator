# Audit Rubric

Scoring methodology for the `/audit` command. Produces a health assessment across 7 dimensions with scores backed by specific evidence from the codebase. See `references/audit-scoring-guide.md` for detailed per-dimension scoring criteria with examples.

## Audit Process

### Scope

Audit all repositories listed in `config/repos.md`. If no config exists, audit whatever repositories are accessible through ~~code repository and note which repos were included.

### Per-Repository Analysis

For each repository:

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

Signals to search for:
- Linter config (.eslintrc, .flake8, ruff.toml, .prettierrc)
- Type checking (TypeScript strict mode, Python type hints + mypy/pyright)
- Code complexity — deeply nested conditionals, functions over 50 lines, god classes
- Naming consistency — do similar things have similar names across the codebase?
- Copy-paste patterns — duplicated logic that should be extracted

### 2. Architecture

What to assess: separation of concerns, dependency direction, coupling, module boundaries.

Signals to search for:
- Directory structure — organized by feature/domain vs. flat/arbitrary
- Import patterns — do modules depend on abstractions or concrete implementations?
- Circular dependencies — modules importing each other
- Layer violations — HTTP handlers containing business logic, database queries in controllers
- Service boundaries — are services appropriately scoped or do some do too much?

### 3. Scalability

What to assess: statelessness, concurrency, resource limits, caching, queue usage.

Signals to search for:
- Hardcoded limits (connection counts, batch sizes, timeouts)
- In-memory state that would break with multiple instances
- Database query patterns — N+1 queries, missing indexes, full table scans
- Async/queue usage — are long operations pushed to background processing?
- Caching strategy — is there one? Is it appropriate?
- Infrastructure scaling config — auto-scaling rules, concurrency limits, reserved capacity

### 4. Error Handling

What to assess: coverage, specificity, propagation, recovery, observability.

Signals to search for:
- Bare/generic exception catches (catch(e), except Exception)
- Swallowed errors — catch blocks that log nothing or return silently
- Error propagation — do errors bubble up with context or get lost?
- Retry logic — present where needed? With backoff? With limits?
- Graceful degradation — what happens when a dependency fails?
- User-facing error quality — do errors help or confuse?

### 5. Documentation

What to assess: README quality, inline comments, API docs, type coverage, onboarding.

Signals to search for:
- README — does it explain what the service does, how to run it, how to deploy it?
- API documentation — OpenAPI/Swagger specs, documented endpoints
- Inline comments — present for non-obvious logic, absent for obvious code
- Type definitions — TypeScript interfaces, Python dataclasses/Pydantic models, JSDoc
- Architecture docs — any diagrams, ADRs, or design docs?
- Onboarding — could a new engineer understand this repo in a day?

### 6. Security

What to assess: secrets management, auth patterns, input validation, dependency health, IAM scope.

Signals to search for:
- Secrets in code — API keys, passwords, tokens in source files (report existence, not values)
- .gitignore — are sensitive files excluded?
- Input validation — are user inputs validated before processing?
- Auth/authz patterns — are endpoints protected? Is authorization checked?
- Dependency vulnerabilities — outdated packages with known CVEs (check lock files)
- IAM/permissions — are cloud permissions scoped or overly broad?
- HTTPS/TLS — is encryption enforced?

### 7. Technical Debt

What to assess: TODO density, deprecated usage, dead code, workarounds, version staleness.

Signals to search for:
- TODO/FIXME/HACK/WORKAROUND comments — count and assess severity
- Deprecated API usage — framework or library features marked deprecated
- Dead code — unused functions, unreachable branches, commented-out blocks
- Dependency staleness — how old are the dependencies? Major versions behind?
- Migration state — half-completed migrations, dual codepaths, legacy compatibility layers
- Test gaps — areas with zero test coverage alongside well-tested areas

## Output Format

See `references/audit-output-template.md` for the full report template. Summary structure:

```
# Codebase Health Audit — [Date]

## Overall Score: X.X/10

## Score Summary
| Dimension        | Score | Trend Signal |
|-----------------|-------|--------------|
| Code Quality     | X/10  | [brief note] |
| Architecture     | X/10  | [brief note] |
| Scalability      | X/10  | [brief note] |
| Error Handling   | X/10  | [brief note] |
| Documentation    | X/10  | [brief note] |
| Security         | X/10  | [brief note] |
| Technical Debt   | X/10  | [brief note] |

## Top 3 Strengths
[with evidence]

## Top 3 Risks
[with evidence and recommended action]

## Per-Dimension Detail
[detailed findings for each dimension]

## Recommended Priority Actions
[ordered list of highest-impact improvements]
```

Overall score is the unweighted mean of all 7 dimensions, rounded to one decimal. If `config/guardrails.md` specifies custom weights, use those instead.
