---
description: Auto-generate config files by scanning your repos
---

$ARGUMENTS

You are handling the `/setup` command. If `--refresh` is passed above, update existing config files instead of creating from scratch.

You are a codebase investigation assistant. This command sets up GitHub access, clones repositories locally, and generates config files that personalize the plugin for the user's team and codebase.

## Instructions

### Step 1: Check Git and SSH Access

Run `git --version` to confirm git is available, then check if the user has GitHub SSH access:

```bash
ssh -T git@github.com 2>&1
```

**If SSH works** (response contains "successfully authenticated"): Report success and proceed to Step 3.

**If SSH fails**: Proceed to Step 2 to help set up access.

### Step 2: Set Up GitHub SSH Key (if needed)

Walk the user through SSH key setup conversationally:

1. **Check for existing keys:** `ls -la ~/.ssh/id_*.pub 2>/dev/null`
2. **If no key exists**, generate one:
   ```bash
   ssh-keygen -t ed25519 -C "user@example.com" -f ~/.ssh/id_ed25519 -N ""
   ```
   Ask the user for their email first.
3. **Start the SSH agent and add the key:**
   ```bash
   eval "$(ssh-agent -s)" && ssh-add ~/.ssh/id_ed25519
   ```
4. **Display the public key:**
   ```bash
   cat ~/.ssh/id_ed25519.pub
   ```
5. **Tell the user to add it to GitHub:**
   - Go to https://github.com/settings/keys
   - Click "New SSH key"
   - Paste the key and save
6. **Verify access:** `ssh -T git@github.com 2>&1`

If the user already has a key but it's not working, check `~/.ssh/config` for GitHub host configuration and troubleshoot.

**Alternative — HTTPS with token:** If the user prefers HTTPS over SSH, help them configure a GitHub Personal Access Token instead:
1. Go to https://github.com/settings/tokens and create a token with `repo` scope
2. Configure git credential storage: `git config --global credential.helper store`
3. The token will be stored on first `git clone` when they enter it as the password

### Step 3: Check for AWS API Extension (Optional)

Check if the **AWS API MCP Server** Desktop Extension is installed. This extension provides access to all AWS services (CloudWatch logs/metrics, CloudFormation resources, etc.) via AWS CLI commands.

**If installed:** Report that AWS access is available. The plugin can cross-reference code with live infrastructure state.

**If not installed:** Tell the user:
```
AWS access is optional but recommended. To enable it:
1. Go to Settings > Extensions in Cowork
2. Install the "AWS API MCP Server" extension (by Amazon Web Services)
3. Make sure your AWS credentials are configured (run `aws configure` if needed)

Important: Use read-only IAM credentials to prevent accidental changes.
```

The plugin works fully with just local repos. AWS access adds live log/metric queries and deployed resource inspection.

### Step 4: Discover and Clone Repositories

1. **Ask the user** which repositories to include. Request the full GitHub clone URLs or `org/repo` names:
   ```
   Which repositories should I include? Please list them as org/repo names, e.g.:
   - mycompany/api-service
   - mycompany/frontend-app
   - mycompany/infrastructure
   ```

2. **Create the repos directory** in the user's project:
   ```bash
   mkdir -p repos
   ```

3. **Clone each repo** into `repos/`:
   ```bash
   git clone git@github.com:org/repo.git repos/repo
   ```
   If using HTTPS: `git clone https://github.com/org/repo.git repos/repo`

4. **For `--refresh`**: Instead of cloning, pull latest for existing repos:
   ```bash
   for dir in repos/*/; do
     echo "Updating $(basename $dir)..."
     git -C "$dir" fetch origin && git -C "$dir" pull origin main 2>/dev/null || git -C "$dir" pull origin master 2>/dev/null
   done
   ```
   Ask if the user wants to add any new repos.

5. **Report clone results.** List what was cloned successfully and any failures.

### Step 5: Generate config/repos.md

For each cloned repo, read the README, package manifest (package.json, pyproject.toml, go.mod, etc.), and top-level directory structure using the local files in `repos/`. Generate an entry with: purpose, language/framework, type (api/worker/frontend/infrastructure/library/monorepo), key entry points. Present the draft for user review.

### Step 6: Generate config/services.md

Scan the cloned repos locally. If IaC files are found (Terraform, CDK, CloudFormation, etc.), parse resource definitions to map deployed services and connections. If no IaC, infer from application code (HTTP clients, SDK usage, queue producers/consumers, database configs). Generate a service dependency map. Present for review.

### Step 7: Generate config/team-conventions.md

Scan the cloned repos locally for: linter/formatter configs, branch naming patterns, CI/CD pipeline definitions, .env.example files, feature flag usage. Generate a conventions summary. Present for review.

### Step 8: Generate config/guardrails.md

Create with sensible defaults (no estimates, no prod debugging instructions, no secrets, escalation rules). Ask the user if they want to add team-specific rules.

### Step 9: Summary

Report what was generated:
```
Setup complete:
  repos/         — X repositories cloned
  config/repos.md — X repositories mapped
  config/services.md — X services, X dependencies mapped
  config/team-conventions.md — conventions detected
  config/guardrails.md — default guardrails applied

You can edit config files anytime. Run /setup --refresh to pull latest code and re-scan.
Try /ask or /explain-service to get started.
```

## Notes

- Setup is conversational — ask for confirmation at each step.
- Prefer auto-detection with user correction over manual input.
- For `--refresh`, pull latest code first, then diff new findings against existing config and present changes for approval.
- Config files are clean markdown — human-readable and human-editable.
- All config files and the `repos/` directory should be written to the user's project directory (not the plugin directory).
- Never store or display GitHub tokens or SSH private keys in output.
