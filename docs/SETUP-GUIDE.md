# Setup Guide

Get Codebase Navigator running with Claude Code. Total setup time: ~10 minutes.

## Prerequisites

1. **Claude Desktop app** with the **Claude Code tab** — Download from [claude.ai/download](https://claude.ai/download)
2. **Git** — Should be pre-installed on macOS. Verify with `git --version`
3. **uv** (optional, for AWS MCP) — Install with:
   ```
   curl -LsSf https://astral.sh/uv/install.sh | sh
   ```

## Step 1: Clone This Repo

```bash
git clone git@github.com:Cooperbuilt/codebase-navigator.git
cd codebase-navigator
```

## Step 2: Configure GitHub Access

You need a GitHub Personal Access Token (PAT) with read access to your team's repos.

1. Go to [github.com/settings/tokens](https://github.com/settings/tokens)
2. Click **Generate new token (classic)**
3. Name it `codebase-navigator-readonly`
4. Set expiration (90 days recommended)
5. Select scope: **`repo`** (needed for private repo access)
6. Click **Generate token** and copy it

Open `.mcp.json` and replace `<YOUR_GITHUB_PAT>` with your token:

```json
"Authorization": "Bearer ghp_your_actual_token_here"
```

## Step 3: Configure AWS Access (Optional)

AWS access enables live log/metric queries and deployed resource inspection. Skip this if you only need code access.

### Get Read-Only Credentials

**Send this message to your engineering team:**

> Hi! I'm setting up a read-only tool to investigate codebase questions without bothering you. Could you create a read-only AWS IAM user for me with these permissions?
>
> - CloudWatch Logs: read-only (logs:Describe*, logs:Get*, logs:FilterLogEvents)
> - CloudWatch Metrics: read-only (cloudwatch:Get*, cloudwatch:List*, cloudwatch:Describe*)
> - CloudFormation: read-only (cloudformation:Describe*, cloudformation:Get*, cloudformation:List*)
>
> I need an **access key ID**, **secret access key**, and the **AWS region** to use. Thanks!

### Add AWS to .mcp.json

Open `.mcp.json` and add the `aws` server entry with your credentials:

```json
"aws": {
  "command": "uvx",
  "args": ["awslabs.aws-api-mcp-server@latest"],
  "env": {
    "AWS_REGION": "us-east-1",
    "AWS_ACCESS_KEY_ID": "AKIA...",
    "AWS_SECRET_ACCESS_KEY": "your-secret-key-here",
    "READ_OPERATIONS_ONLY": "true"
  }
}
```

Replace the region, access key ID, and secret access key with the values from your engineering team. `READ_OPERATIONS_ONLY=true` ensures the server only performs read operations for safety.

If you're skipping AWS, just leave the `aws` entry out of `.mcp.json`.

## Step 4: Open in Claude Code

1. Open Claude Desktop
2. Switch to the **Claude Code** tab
3. Open the `codebase-navigator` project folder
4. Claude Code will detect `.mcp.json` and prompt you to approve the MCP servers

## Step 5: Run Setup

Type `/setup` in the chat. This will:
- Verify your MCP server connections
- Scan your GitHub repos
- Ask which ones to include
- Generate config files with your team's specific context

After setup, try `/ask what repositories do I have access to?` to verify everything works.

## Troubleshooting

**GitHub MCP won't connect**
- Verify your token: `curl -H "Authorization: Bearer <your-token>" https://api.github.com/user` — you should see your GitHub username
- Does the token have the `repo` scope?
- Restart Claude Code after editing `.mcp.json`

**AWS MCP won't connect**
- Is `uv` installed? Run `uv --version`
- Are `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` set correctly in `.mcp.json`?
- Is the region correct in `.mcp.json`?

**"Config files not found" warnings**
- Run `/setup` to generate them. This is expected on first use.

**MCP servers not detected**
- Make sure `.mcp.json` has no syntax errors (no trailing commas, all quotes matched)
- Restart Claude Code after any `.mcp.json` changes
