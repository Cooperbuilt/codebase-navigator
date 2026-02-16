# Audit Scoring Guide

Detailed criteria for each score range within each dimension. Use this to calibrate scores consistently.

## Table of Contents
1. [Code Quality Scoring](#code-quality-scoring)
2. [Architecture Scoring](#architecture-scoring)
3. [Scalability Scoring](#scalability-scoring)
4. [Error Handling Scoring](#error-handling-scoring)
5. [Documentation Scoring](#documentation-scoring)
6. [Security Scoring](#security-scoring)
7. [Technical Debt Scoring](#technical-debt-scoring)

---

## Code Quality Scoring

**1-3 (Critical)**:
- No linter or formatter configured
- Inconsistent naming across the codebase (camelCase mixed with snake_case arbitrarily)
- Functions routinely exceed 100 lines with deep nesting
- No type checking; any/unknown used pervasively
- Widespread copy-paste duplication

**4-5 (Below Average)**:
- Linter exists but many rules disabled or warnings ignored
- Naming mostly consistent within files but varies across modules
- Some long functions; complexity concentrated in specific areas
- Partial type coverage; key interfaces untyped
- Some duplication in non-critical paths

**6-7 (Solid)**:
- Linter and formatter configured and enforced in CI
- Consistent naming conventions followed across the codebase
- Functions generally focused; occasional complexity hotspots
- Good type coverage; core domain types well-defined
- Minimal duplication; shared utilities extracted

**8-9 (Strong)**:
- Strict linting with zero tolerance; CI blocks on violations
- Naming is self-documenting; code reads like prose
- Low cyclomatic complexity throughout; complex logic extracted into well-named helpers
- Near-complete type coverage including edge cases and error types
- DRY principle followed consistently; abstractions are appropriate (not premature)

**10 (Exceptional)**:
- All of the above plus: code style guide documented and followed, consistent patterns across all contributors, would serve as a reference implementation

## Architecture Scoring

**1-3**: Monolithic file structure, circular dependencies, business logic in HTTP handlers, no clear module boundaries, god objects or god services.

**4-5**: Some structure exists but inconsistently applied. Some services are well-bounded, others aren't. Layer violations in non-critical paths. Dependencies mostly flow in one direction.

**6-7**: Clear directory organization by feature or domain. Dependency injection or clear abstraction boundaries. Services are appropriately scoped. Layer violations are rare.

**8-9**: Clean architecture or hexagonal patterns consistently applied. Dependencies flow inward. Each module has a clear public interface. Easy to understand where new code should go.

**10**: Architecture is documented with ADRs, consistently followed, and actively maintained. New team members can understand the system structure from the code organization alone.

## Scalability Scoring

**1-3**: In-memory state assumed single-instance. Synchronous processing for long operations. No caching. Hardcoded connection limits. Database queries would fail at 10x current scale.

**4-5**: Mostly stateless but some in-memory assumptions. Background processing exists but inconsistently used. Basic caching present. Some queries need optimization.

**6-7**: Stateless services. Async processing for appropriate operations. Caching strategy present. Database queries use indexes. Auto-scaling configured.

**8-9**: Horizontally scalable by design. Queue-based architecture for decoupling. Read replicas or caching layers for read-heavy paths. Load tested with documented capacity limits.

**10**: Designed for 100x current scale. Comprehensive caching strategy. Circuit breakers. Graceful degradation under load. Capacity planning documented.

## Error Handling Scoring

**1-3**: Bare exception catches everywhere. Errors silently swallowed. No retry logic. User sees raw stack traces or generic "something went wrong."

**4-5**: Error handling present in critical paths but generic catches common. Some retry logic. Error logging exists but lacks context. User error messages unhelpful.

**6-7**: Specific error types caught. Errors propagate with context. Retry with backoff for external calls. Structured error logging. User errors are actionable.

**8-9**: Comprehensive error taxonomy. Circuit breakers for external dependencies. Dead letter queues for failed async operations. Error rates monitored with alarms.

**10**: Error handling is a first-class architectural concern. Chaos engineering or fault injection testing. Graceful degradation documented and tested per dependency.

## Documentation Scoring

**1-3**: No README or a default template README. No API docs. No inline comments. No type definitions for core domain objects.

**4-5**: README exists with basic setup instructions. Some API routes documented. Sporadic comments. Types for some interfaces.

**6-7**: README covers setup, deployment, and architecture overview. API documented with examples. Comments on non-obvious logic. Core domain fully typed.

**8-9**: Comprehensive README with architecture diagrams. OpenAPI/Swagger specs. ADRs for major decisions. Inline docs explain why, not what. Full type coverage.

**10**: Onboarding guide that gets a new engineer productive in a day. Living documentation that stays current. API docs auto-generated from code. Runbooks for operational scenarios.

## Security Scoring

**1-3**: Secrets committed to source. No input validation. No auth on internal endpoints. Dependencies years out of date with known CVEs.

**4-5**: Secrets in environment variables but .env files occasionally committed. Basic input validation. Auth present but authorization checks incomplete. Some outdated dependencies.

**6-7**: Secrets managed through a secrets manager or vault. Input validation on all user-facing endpoints. Auth and authz consistently applied. Dependencies regularly updated. Security scanning in CI.

**8-9**: Defense in depth. Least-privilege IAM. Rate limiting. CORS properly configured. Dependency scanning with automated PRs for vulnerabilities. Security headers present.

**10**: Security threat model documented. Penetration testing performed. CSP headers. Audit logging. Encryption at rest and in transit for all sensitive data. SOC2/compliance-ready.

## Technical Debt Scoring

**1-3**: 50+ TODOs. Multiple deprecated APIs in use. Significant dead code. Dependencies 3+ major versions behind. Active workarounds for known bugs.

**4-5**: 20-50 TODOs. Some deprecated usage. Dead code in non-critical areas. Dependencies 1-2 major versions behind. A few documented workarounds.

**6-7**: Under 20 TODOs, most with context. Minimal deprecated usage. Dead code regularly cleaned. Dependencies mostly current. Workarounds have associated tickets.

**8-9**: Under 10 TODOs, all with tracking tickets. No deprecated API usage. No dead code. Dependencies current. Technical debt tracked and prioritized in backlog.

**10**: Near-zero untracked debt. Proactive dependency updates. Automated deprecation detection. Regular refactoring cadence documented and followed.
