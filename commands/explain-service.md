# Explain Service

Get a comprehensive explanation of how a specific service, system, or feature works — its purpose, architecture, data flow, dependencies, and configuration.

## Usage

```
/explain-service <service or feature name>
```

## Examples

```
/explain-service checkout-api
/explain-service the notification system
/explain-service user authentication
/explain-service the data pipeline
/explain-service webhook processing
```

## Workflow

1. **Locate the service.** Search ~~code repository for the named service. Check `config/repos.md` and `config/services.md` for mapped names. If the name is ambiguous (multiple matches), list the options and ask which one.

2. **Read the service top-down:**
   - README and package manifest → purpose and dependencies
   - Entry points → what triggers this service (HTTP routes, event handlers, cron jobs, queue consumers)
   - Core logic → the main processing flow
   - Data access → what it reads from and writes to
   - Configuration → environment variables, feature flags, external service URLs
   - Error handling → what happens when things fail
   - Tests → what behaviors are tested (reveals what the team considers important)

3. **Map infrastructure.** Using IaC files and/or ~~cloud resources:
   - How is this service deployed? (Lambda, ECS, EC2, etc.)
   - What cloud resources does it use? (databases, queues, caches, storage)
   - Scaling configuration? (concurrency limits, auto-scaling rules, reserved capacity)
   - Networking? (VPC, security groups, public/private endpoints)

4. **Map dependencies.** What other services does this service call? What calls it? Show the interaction as a data flow:
   ```
   [Trigger] → [This Service] → [Downstream Dependencies]
                     ↓
              [Data Stores]
   ```

5. **Compose the explanation** following the semi-technical-voice skill. Structure:
   - **What it does** — one paragraph purpose statement
   - **How it works** — data flow from trigger to output
   - **Key dependencies** — what it relies on, what relies on it
   - **Configuration** — critical settings that affect behavior
   - **Known considerations** — anything notable from the code (workarounds, TODOs, commented-out features, feature flags)

## Notes

- Skip XY problem detection for this command — the user is explicitly asking for a service overview.
- If the service spans multiple repos, cover all of them and note which code lives where.
- Include infrastructure context alongside code context — PMs care about "this runs as a Lambda with 256MB memory and a 30s timeout" because it explains behavioral constraints they observe.
