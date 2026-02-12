# XY Problem Detection

Non-technical stakeholders frequently ask about an assumed solution (Y) when their real need is understanding the underlying problem (X). This skill intercepts questions before answering and determines whether clarification will produce a significantly better answer.

## When This Skill Activates

Apply this reasoning on every question received through `/ask` or `/customer-case` commands. Do NOT apply during `/explain-service` (user explicitly requested a service overview) or `/audit`.

## Detection Methodology

Before answering, silently perform these steps:

### Step 1: Gather Context First

Search ~~code repository for terms in the user's question BEFORE deciding whether to clarify. Use file names, function names, class names, config keys, and service names mentioned or implied. This context informs whether the question is well-formed or misdirected.

### Step 2: Classify the Question

Assign one of three classifications:

**CLEAR** — Answer directly. No clarification needed. Indicators:
- Question maps cleanly to specific code, config, or infrastructure
- Only one reasonable interpretation exists given the codebase context
- Question is scoped (asks about a specific service, file, flow, or behavior)

**AMBIGUOUS** — Clarify before answering. Indicators:
- Multiple unrelated code paths could be "the answer" depending on intent
- Question uses vague terms ("the API", "the database") when multiple exist
- Question asks "how" but the useful answer depends on whether they want architecture overview vs. debugging a specific issue vs. evaluating a change

**MISDIRECTED** — The question assumes something false about the codebase. Indicators:
- Question references a service, pattern, or behavior that doesn't exist in the code
- Question assumes component A talks to component B, but the code shows otherwise
- Question conflates two different systems

### Step 3: Act on Classification

**CLEAR** → Proceed to answer. No preamble about the classification.

**AMBIGUOUS** → Offer 2-3 reframings as concrete options. Format:

```
I found [what you found in the code]. To give you the most useful answer, which of these are you after?

1. [Reframing A] — e.g., "How the retry logic works architecturally"
2. [Reframing B] — e.g., "Why customer X might have seen duplicate events"
3. [Reframing C] — e.g., "Whether we could safely increase the retry window"

Or just say "all of the above" or tell me what I'm missing.
```

Each reframing must reference actual code/infrastructure context discovered in Step 1. Never offer generic reframings disconnected from the codebase.

**MISDIRECTED** → Gently correct the false assumption with evidence, then offer the right question:

```
Quick note: [the thing they assumed] actually works differently — [brief evidence from code]. 

You might be looking for: [reframed question based on how it actually works]

Want me to go with that, or were you asking about something else?
```

## Critical Constraints

- Never classify more than 20% of questions as AMBIGUOUS or MISDIRECTED. Most questions have a reasonable default interpretation — use it.
- Never ask more than one round of clarification. If the user answers your clarification, proceed with the answer even if residual ambiguity remains.
- If the user says "just answer it" or expresses impatience with clarification, immediately proceed with the most likely interpretation. Note your assumption briefly.
- Clarification questions must demonstrate that you already looked at the code. "What do you mean by the API?" is lazy. "We have three APIs — the public REST API in `api-gateway`, the internal gRPC service in `worker-service`, and the webhook handler in `integrations`. Which one?" is useful.
- Do not explain the XY problem concept to the user. Do not say "this might be an XY problem." Just do the reframing naturally.
