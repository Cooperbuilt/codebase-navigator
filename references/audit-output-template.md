# Audit Output Template

Use this structure for `/audit` results. Adapt section depth to findings — don't pad thin sections.

```markdown
# Codebase Health Audit

**Date:** [YYYY-MM-DD]
**Repositories audited:** [list]
**Languages/frameworks detected:** [list]

---

## Overall Score: X.X / 10

| Dimension        | Score | Key Signal |
|-----------------|-------|------------|
| Code Quality     | X/10  |            |
| Architecture     | X/10  |            |
| Scalability      | X/10  |            |
| Error Handling   | X/10  |            |
| Documentation    | X/10  |            |
| Security         | X/10  |            |
| Technical Debt   | X/10  |            |

---

## Executive Summary

[3-5 sentences. What's the overall health? What are the biggest risks?
What's the single most impactful thing the team could do?]

---

## Top 3 Strengths

**1. [Strength title]**
[Evidence with file paths. Why this matters.]

**2. [Strength title]**
[Evidence with file paths. Why this matters.]

**3. [Strength title]**
[Evidence with file paths. Why this matters.]

## Top 3 Risks

**1. [Risk title]** — Affects: [which dimension(s)]
[Evidence with file paths. What could go wrong. Recommended action.]

**2. [Risk title]** — Affects: [which dimension(s)]
[Evidence with file paths. What could go wrong. Recommended action.]

**3. [Risk title]** — Affects: [which dimension(s)]
[Evidence with file paths. What could go wrong. Recommended action.]

---

## Detailed Findings

### Code Quality — X/10

**Strengths:**
- [finding with file path]
- [finding with file path]

**Issues:**
- [finding with file path]
- [finding with file path]

**Top recommendation:** [single most impactful improvement]

[Repeat for each dimension]

---

## Recommended Priority Actions

Ordered by impact. Each action maps to one or more dimension scores.

1. **[Action]** → Improves: [dimensions]. Effort signal: [low/medium/high complexity, NOT time estimate].
2. **[Action]** → Improves: [dimensions]. Effort signal: [low/medium/high complexity].
3. **[Action]** → Improves: [dimensions]. Effort signal: [low/medium/high complexity].
4. **[Action]** → Improves: [dimensions]. Effort signal: [low/medium/high complexity].
5. **[Action]** → Improves: [dimensions]. Effort signal: [low/medium/high complexity].
```

## Template Notes

- "Effort signal" uses complexity (low/medium/high), NOT time estimates. Low = isolated change, medium = touches multiple files/services, high = architectural change.
- Every finding must include at least one file path as evidence.
- "Key Signal" in the summary table is ≤8 words capturing the score's primary driver.
- Top risks must include a recommended action, not just a description of the problem.
- If auditing multiple repos, group findings per-repo within each dimension when the finding is repo-specific. Cross-cutting findings (e.g., inconsistent patterns across repos) should be called out separately.
