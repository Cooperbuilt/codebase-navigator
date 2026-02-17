$ARGUMENTS

You are handling the `/explain-service` command. The service or feature name is above.

## Instructions

1. **Locate the service.** Use the GitHub MCP server to search for the named service. Check `config/repos.md` and `config/services.md` for mapped names. If the name is ambiguous (multiple matches), list the options and ask which one.

2. **Read the service top-down:**
   - README and package manifest — purpose and dependencies
   - Entry points — what triggers this service (HTTP routes, event handlers, cron jobs, queue consumers)
   - Core logic — the main processing flow
   - Data access — what it reads from and writes to
   - Configuration — environment variables, feature flags, external service URLs
   - Error handling — what happens when things fail
   - Tests — what behaviors are tested (reveals what the team considers important)

3. **Map infrastructure.** Using IaC files found via the GitHub MCP server and/or the AWS API MCP server:
   - How is this service deployed? (Lambda, ECS, EC2, etc.)
   - What cloud resources does it use? (databases, queues, caches, storage)
   - Scaling configuration? (concurrency limits, auto-scaling rules, reserved capacity)
   - Networking? (VPC, security groups, public/private endpoints)

4. **Map dependencies.** What other services does this call? What calls it? Show as a data flow:
   ```
   [Trigger] -> [This Service] -> [Downstream Dependencies]
                      |
               [Data Stores]
   ```

5. **Compose the explanation.** Structure:
   - **What it does** — one paragraph purpose statement
   - **How it works** — data flow from trigger to output
   - **Key dependencies** — what it relies on, what relies on it
   - **Configuration** — critical settings that affect behavior
   - **Known considerations** — anything notable (workarounds, TODOs, commented-out features, feature flags)

## Notes

- Do NOT apply XY problem detection — the user is explicitly asking for a service overview.
- If the service spans multiple repos, cover all of them and note which code lives where.
