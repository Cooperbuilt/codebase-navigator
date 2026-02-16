---
name: infra-awareness
description: Methodology for answering questions involving deployed infrastructure. Compares IaC intent vs live cloud reality.
---

# Infrastructure Awareness — Detail

Detailed methodology for answering questions that involve deployed infrastructure. This skill is referenced by commands when deep infrastructure analysis is needed.

For core infrastructure reconciliation patterns (intent vs reality, graceful degradation), see the navigation strategy rules which are loaded automatically.

## Capability Detection

At the start of a session, determine available infrastructure context:

| Source | How to detect | What it provides |
|---|---|---|
| **IaC files** | Terraform (.tf), CDK, CloudFormation found in local repos (`repos/`) | Intended architecture, resource configuration, relationships |
| **AWS API MCP Server** | AWS API extension installed in Cowork (Settings > Extensions) | Live logs, metrics, alarms, deployed resource state, drift detection — all AWS services via CLI |

## Two-Source Methodology

When both IaC and live cloud access exist, always compare:

### Intent (IaC Files)
IaC files are the authoritative source for:
- **What was designed** — Resource types, configurations, relationships
- **Why it was designed that way** — Comments, variable names, module structure
- **What should exist** — The expected set of resources and their properties

When reading IaC, pay attention to:
- Variable files (`variables.tf`, `terraform.tfvars`, `.env`) — actual parameter values
- Module structure — how infrastructure is organized and reused
- Output values — what one stack exposes to others
- Resource naming patterns — help map IaC to live resources

### Reality (Live Cloud)
Live cloud APIs are the authoritative source for:
- **What actually exists** — Resources may have been created manually or drifted
- **Current state** — Actual configuration that may differ from IaC
- **Runtime behavior** — Logs, metrics, alarms, error rates, latency

### Reconciliation
When intent and reality diverge, this IS the answer to many questions:
- "Why doesn't Y work?" — Often because live config doesn't match IaC
- "How does X work?" — IaC says one thing, but manual changes mean different behavior
- "What capabilities do we have?" — IaC shows intent; live state shows what's running

Always surface divergence with specifics.

## Question-Type Patterns

### "What capabilities do we have?"
1. Start with IaC to map intended architecture
2. Cross-reference with live resources via the AWS API extension to confirm what's deployed
3. Check application code for feature flags or conditional logic
4. Present as a capability inventory, not a resource list

### "How does X work?"
1. Trace data flow in application code
2. Map code references to infrastructure: which Lambda, which queue, which database
3. Pull IaC config for resource settings (timeouts, memory, concurrency, scaling)
4. If the AWS API extension is available, include recent performance characteristics

### "Why doesn't Y work?" / Customer issue investigation
1. Check CloudWatch via the AWS API extension for recent errors, alarm state, metric anomalies
2. Correlate timestamps — when did the issue start? What changed?
3. Check recent commits with `git -C repos/<repo> log --oneline -20` for code changes around that time
4. Check IaC for recent changes that might affect behavior
5. Look for the specific error path in application code

### "How much would it cost?"
Infrastructure cost questions are NOT effort estimates (which are off-limits). Provide factual data:
- Current resource configuration from IaC or live state
- AWS pricing model for those resources (if known)
- Scaling characteristics that affect cost
- Flag for finance/engineering to calculate precisely
