# Setup Guide

Get Codebase Navigator running in Cowork. Total setup time: ~10 minutes.

## Prerequisites

1. **Cowork app** — Download from [claude.ai/download](https://claude.ai/download) if you don't have it
2. **Git** — Should be pre-installed on macOS. Verify with `git --version`
3. **GitHub access** — You need read access to your team's repositories
4. **AWS CLI** (optional, for AWS infrastructure access) — Install from [aws.amazon.com/cli](https://aws.amazon.com/cli/)

## Step 1: Install the Plugin

In Cowork, add the marketplace:
1. Go to plugin settings
2. Add marketplace: `Cooperbuilt/codebase-navigator`
3. Install the `codebase-navigator` plugin

## Step 2: Run Setup

Type `/setup` in the chat. The setup wizard will:

1. **Check GitHub access** — Verifies you can connect to GitHub via SSH
2. **Set up SSH key** (if needed) — Walks you through generating a key and adding it to GitHub
3. **Clone repositories** — Asks which repos to include and clones them locally
4. **Generate config files** — Scans your repos and creates config files for the plugin

### GitHub Access Options

**SSH (recommended):** The setup wizard will check for an existing SSH key and help you create one if needed. You'll add the public key to your GitHub account at [github.com/settings/keys](https://github.com/settings/keys).

**HTTPS with token:** If you prefer HTTPS, create a Personal Access Token at [github.com/settings/tokens](https://github.com/settings/tokens) with `repo` scope. The setup wizard supports this as an alternative.

## Step 3: AWS Access (Optional)

AWS access enables live log/metric queries and deployed resource inspection. Skip this step if you only need code access.

### Install the AWS API Extension

1. In Cowork, go to **Settings > Extensions**
2. Find and install **AWS API MCP Server** (by Amazon Web Services)
3. Follow any prompts to configure

### Get Read-Only AWS Credentials

**Send this message to your engineering team:**

> Hi! I'm setting up a read-only tool to investigate codebase questions without bothering you. Could you create a read-only AWS IAM user for me with these permissions?
>
> - CloudWatch Logs: read-only (logs:Describe*, logs:Get*, logs:FilterLogEvents)
> - CloudWatch Metrics: read-only (cloudwatch:Get*, cloudwatch:List*, cloudwatch:Describe*)
> - CloudFormation: read-only (cloudformation:Describe*, cloudformation:Get*, cloudformation:List*)
>
> I need an **access key ID**, **secret access key**, and the **AWS region** to use. Thanks!

### Configure AWS CLI

Once you have credentials, run in Terminal:
```
aws configure
```
Enter the access key ID, secret access key, and region when prompted. The AWS API extension uses these credentials automatically.

**Important:** Use read-only credentials to prevent accidental changes to infrastructure.

## Step 4: Verify

After setup, try `/ask what repositories do I have access to?` to verify everything works.

## Troubleshooting

**SSH key not working**
- Run `ssh -T git@github.com` in Terminal to test the connection
- Make sure your public key is added at [github.com/settings/keys](https://github.com/settings/keys)
- Check `~/.ssh/config` for GitHub host configuration

**Git clone fails**
- Verify you have read access to the repository on GitHub
- For private repos, ensure your SSH key or token has `repo` scope
- Check your network connection

**AWS API extension not working**
- Is the extension installed? Check Settings > Extensions in Cowork
- Is AWS CLI configured? Run `aws sts get-caller-identity` in Terminal to verify credentials
- Are the credentials correct? Double-check with your engineering team
- Verify the AWS region is correct (`aws configure get region`)

**"Config files not found" warnings**
- Run `/setup` to generate them. This is expected on first use.

**"repos/ doesn't exist" warnings**
- Run `/setup` to clone repositories. This is expected on first use.
