# Infrastructure Awareness

Methodology for answering questions that involve deployed infrastructure. Combines two complementary sources: Infrastructure-as-Code (IaC) files found through ~~code repository, and live cloud state from ~~cloud observability and ~~cloud resources when available.

## Capability Detection

At the start of a session, determine available infrastructure context:

| Source | How to detect | What it provides |
|---|---|---|
| **IaC files** | Terraform (.tf), CDK, CloudFormation in ~~code repository | Intended architecture, resource configuration, relationships |
| **~~cloud observability** | CloudWatch, Datadog, or similar MCP connected | Live logs, metrics, alarms, recent errors |
| **~~cloud resources** | CloudFormation, Cloud Control, or similar MCP connected | Actual deployed resource state, properties, drift |

Adapt answers based on what's available. See "Graceful Degradation" below.

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
- Resource naming patterns — these help map IaC to live resources

### Reality (Live Cloud)

Live cloud APIs are the authoritative source for:
- **What actually exists** — Resources may have been created manually or drifted
- **Current state** — Actual configuration that may differ from IaC
- **Runtime behavior** — Logs, metrics, alarms, error rates, latency

### Reconciliation

When intent and reality diverge, this IS the answer to many PM questions:
- "Why doesn't Y work?" → Often because the live config doesn't match what the IaC declares
- "How does X work?" → IaC says one thing, but manual changes or drift mean it behaves differently
- "What capabilities do we have?" → IaC shows the intended architecture; live state shows what's actually running

Always surface divergence: "The Terraform config declares a 5-minute timeout for this Lambda, but the live function is configured with 15 minutes. Someone likely changed this manually — the team should decide whether to update the Terraform or revert the live config."

## Question-Type Patterns

### "What capabilities do we have?"

1. Start with IaC to map the intended architecture
2. Cross-reference with live resources to confirm what's deployed
3. Check application code for feature flags or conditional logic that enables/disables capabilities
4. Present as a capability inventory, not a resource list — PMs care about "we can process webhooks" not "we have an API Gateway with a POST /webhooks route"

### "How does X work?"

1. Trace the data flow in application code (see codebase-navigation skill)
2. Map code references to infrastructure: which Lambda, which queue, which database
3. Pull IaC config for resource settings (timeouts, memory, concurrency, scaling rules)
4. If ~~cloud observability is available, include recent performance characteristics: "This Lambda typically completes in 2-3 seconds based on recent metrics"

### "Why doesn't Y work?" / Customer issue investigation

1. Check ~~cloud observability for recent errors, alarm state, metric anomalies
2. Correlate timestamps — when did the issue start? What changed?
3. Check recent commits in relevant repos for code changes around that time
4. Check IaC for recent changes that might affect behavior
5. Look for the specific error path in application code

### "How much would it cost?"

Infrastructure cost questions are NOT effort estimates (which are off-limits). Provide factual data:
- Current resource configuration from IaC or live state
- AWS/cloud pricing model for those resources (if known)
- Scaling characteristics that affect cost
- Flag this for finance/engineering to calculate precisely

## Graceful Degradation

| Available sources | Behavior |
|---|---|
| IaC + live cloud | Full capability. Compare intent vs. reality. Provide concrete metrics. |
| IaC only | Answer from intended architecture. Note: "This is based on the infrastructure code — I can't verify live state without cloud access configured." |
| Live cloud only | Answer from actual state. Note: "I can see what's deployed but not the design intent. Connecting your IaC repo would give more complete answers." |
| Neither | Answer from application code references to infrastructure (service names, connection strings, SDK usage). Note what additional connectors would help. |

Never error or refuse because a source is unavailable. Always provide the best answer possible with available context, and note what's missing factually without being preachy about it.
