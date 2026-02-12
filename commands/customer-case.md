# Customer Case

Investigate a customer-reported issue or behavior. Combines code analysis with infrastructure observability to explain what likely happened and provide context for engineering to investigate further.

## Usage

```
/customer-case <description of what the customer reported>
```

## Examples

```
/customer-case customer says their order was charged twice but they only see one order in their dashboard
/customer-case enterprise client reports that SSO login stopped working yesterday afternoon
/customer-case webhook deliveries to partner X are failing since last Tuesday
/customer-case user uploaded a CSV but the import shows 0 records processed
```

## Workflow

1. **Apply XY problem detection** — but tuned for customer issues. The PM's framing may mix customer-reported symptoms with assumed causes. Separate observation from hypothesis.

2. **Identify the code path.** From the symptom description, determine:
   - Which service(s) handle this flow
   - What the expected happy-path behavior is
   - Where in the flow the reported behavior could originate

3. **Search for failure modes.** In the relevant code:
   - Error handling and edge cases on the identified path
   - Race conditions or timing-dependent behavior
   - Retry logic that could cause duplicates
   - Validation logic that could silently reject input
   - Feature flags or config that could change behavior
   - Recent code changes to the relevant paths (check commit history)

4. **Check infrastructure** (if ~~cloud observability is connected):
   - Recent errors or exceptions in relevant services
   - Metric anomalies around the reported timeframe
   - Alarm state changes
   - Deployment events or config changes

5. **Compose the investigation summary:**

   ```
   ## What the customer likely experienced
   [Plain-language explanation of the behavior from the user's perspective]

   ## Most likely cause(s)
   [Ranked by likelihood, each with code/infra evidence]

   ## Code path involved
   [The relevant flow with file references]

   ## What to check next
   [Specific logs, metrics, or database queries an engineer should check.
   Frame as "here's what to look at" not "here's what to run."]
   ```

## Notes

- This command is NOT for live debugging. It's for building a hypothesis from code and available infrastructure data that an engineer can verify.
- If ~~cloud observability is not connected, be explicit: "I can show you the code paths that could cause this behavior, but I can't check live logs or metrics. An engineer would need to check [specific things] to confirm."
- Never speculate about data-level issues ("the customer's record might be corrupted") without evidence. Describe the code behavior that could produce the symptom.
- If the issue could be caused by multiple independent code paths, list them all ranked by likelihood rather than guessing.
- Time-sensitive language from the PM ("since yesterday," "started Tuesday") is a strong signal to check recent commits and infrastructure changes in that window.
