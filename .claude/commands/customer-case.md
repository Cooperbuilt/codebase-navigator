$ARGUMENTS

You are handling the `/customer-case` command. The customer issue description is above.

## Instructions

1. **XY Problem Detection.** The PM's framing may mix customer-reported symptoms with assumed causes. Separate observation from hypothesis. Use Grep to search `repos/` for terms before deciding if clarification is needed. If CLEAR, proceed. If AMBIGUOUS, offer reframings. If MISDIRECTED, gently correct with evidence.

2. **Identify the code path.** From the symptom description, use Grep and Read on `repos/` to determine:
   - Which service(s) handle this flow
   - What the expected happy-path behavior is
   - Where in the flow the reported behavior could originate

3. **Search for failure modes.** In the relevant code, look for:
   - Error handling and edge cases on the identified path
   - Race conditions or timing-dependent behavior
   - Retry logic that could cause duplicates
   - Validation logic that could silently reject input
   - Feature flags or config that could change behavior
   - Recent code changes to the relevant paths (use `git log` in local repo or GitHub MCP for commit context)

4. **Check infrastructure** (if the AWS API MCP server is available):
   - Recent errors or exceptions in relevant services
   - Metric anomalies around the reported timeframe
   - Alarm state changes
   - Deployment events or config changes

5. **Compose the investigation summary.** Structure:

   ## What the customer likely experienced
   [Plain-language explanation from the user's perspective]

   ## Most likely cause(s)
   [Ranked by likelihood, each with code/infra evidence]

   ## Code path involved
   [The relevant flow with file references]

   ## What to check next
   [Specific logs, metrics, or areas for an engineer to check. Frame as "here's what to look at" not "here's what to run."]

## Notes

- This command is NOT for live debugging. It builds a hypothesis from code and available data that an engineer can verify.
- If the AWS API MCP is not available, be explicit: "I can show you the code paths that could cause this, but I can't check live logs or metrics."
- Never speculate about data-level issues without code evidence. Describe the code behavior that could produce the symptom.
- Time-sensitive language ("since yesterday," "started Tuesday") is a signal to check recent commits and infrastructure changes in that window.
- If the investigation warrants engineering follow-up, suggest using `/escalate` to generate a handoff message.
