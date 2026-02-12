# Ask

Ask any question about your codebase, infrastructure, or a customer-reported issue. The plugin searches your code and infrastructure to find the answer.

## Usage

```
/ask <your question>
```

## Examples

```
/ask how does the checkout flow handle failed payments?
/ask what happens when a user uploads a file larger than 50MB?
/ask why are some webhook deliveries arriving twice?
/ask what authentication method does the partner API use?
/ask what environment variables does the order service need?
```

## Workflow

1. Apply XY problem detection (skill: xy-problem-detection). Silently search ~~code repository for context around the question. If the question is ambiguous or misdirected, offer 2-3 reframings before answering.

2. Navigate the codebase (skill: codebase-navigation). Follow entry points → data flow → configuration → cross-service references.

3. Check infrastructure context (skill: infra-awareness). If ~~cloud observability or ~~cloud resources are connected, cross-reference code findings with live infrastructure state.

4. Compose the answer (skill: semi-technical-voice). Use correct technical terms, include file references, provide confidence indicators. Respect guardrails — no estimates, no feasibility judgments.

## Notes

- If the question requires live production debugging, say so and provide context to bring to engineering.
- If the question is purely a business decision ("should we build X?"), redirect: explain what currently exists and what would need to change, but don't recommend a course of action.
- For questions about cost, provide factual infrastructure information but direct to finance/engineering for actual calculations.
