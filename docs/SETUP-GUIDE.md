# Setup Guide

Get Codebase Navigator running in Cowork. Total setup time: ~15 minutes.

## Prerequisites

1. **Cowork app** — Download from [claude.ai/download](https://claude.ai/download) if you don't have it
2. **uv** (optional, for AWS MCP servers) — Install with:
   ```
   curl -LsSf https://astral.sh/uv/install.sh | sh
   ```

## Step 1: GitHub Token

You need a read-only GitHub Personal Access Token (PAT).

1. Go to [github.com/settings/tokens](https://github.com/settings/tokens)
2. Click **Generate new token (classic)**
3. Name it something like `codebase-navigator-readonly`
4. Set expiration (90 days recommended)
5. Select scopes: **`repo`** (Full control of private repositories — needed for read access to private repos)
6. Click **Generate token**
7. Copy the token — you'll need it in Step 3

## Step 2: AWS Credentials (Optional)

AWS access enables live log/metric queries and deployed resource inspection. Skip this step if you only need code access.

**Send this message to your engineering team:**

> Hi! I'm setting up a read-only tool to investigate codebase questions without bothering you. Could you create a read-only AWS IAM user for me with these permissions?
>
> - CloudWatch Logs: read-only (logs:Describe*, logs:Get*, logs:FilterLogEvents)
> - CloudWatch Metrics: read-only (cloudwatch:Get*, cloudwatch:List*, cloudwatch:Describe*)
> - CloudFormation: read-only (cloudformation:Describe*, cloudformation:Get*, cloudformation:List*)
>
> I need an **access key ID**, **secret access key**, and the **AWS region** to use. Thanks!

Hold onto those three values — you'll paste them into `.mcp.json` in the next step.

## Step 3: Configure MCP Servers

Open `.mcp.json` in the project root and replace the placeholders:

- `<YOUR_GITHUB_PAT>` — The token from Step 1 (in the `Authorization` header value, after `Bearer `)
- `<YOUR_AWS_ACCESS_KEY_ID>` — The access key ID from Step 2
- `<YOUR_AWS_SECRET_ACCESS_KEY>` — The secret access key from Step 2
- `<YOUR_AWS_REGION>` — The AWS region from Step 2 (e.g., `us-east-1`)

If you're skipping AWS, delete the `aws-cloudwatch` and `aws-cfn` entries from the file entirely.

## Step 4: Open in Cowork

1. Open the Cowork app
2. Open this project folder (`codebase-navigator/`)
3. Check that MCP server indicators show green in the bottom bar
   - GitHub should always be green
   - AWS servers are green only if you configured them in Step 2-3

## Step 5: Run Setup

Type `/setup` in the chat. This will:
- Scan your GitHub repos
- Ask which ones to include
- Generate config files with your team's specific context

After setup, try `/ask what repositories do I have access to?` to verify everything works.

## Troubleshooting

**GitHub MCP won't connect**
- Is the token correct? Verify it by running `curl -H "Authorization: Bearer <your-token>" https://api.github.com/user` in Terminal — you should see your GitHub username in the response.
- Does the token have the `repo` scope? Tokens without it can't access private repositories.

**AWS MCP won't connect**
- Is `uv` installed? Run `uv --version` in Terminal.
- Are the credentials correct? Double-check the access key ID, secret access key, and region with your engineering team.
- Are there extra spaces or missing quotes in `.mcp.json`? Each value should be wrapped in double quotes with no leading/trailing spaces inside.

**"Config files not found" warnings**
- Run `/setup` to generate them. This is expected on first use.

**MCP servers show red/disconnected**
- Close and reopen the project in Cowork
- Check that `.mcp.json` has no syntax errors (no trailing commas, all quotes matched)
