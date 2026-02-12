# Connectors

Plugin files use `~~category` as a placeholder for whatever tool the user connects in that category. This plugin is tool-agnostic — it describes workflows in terms of capabilities rather than specific products.

## Connector Categories

### ~~code repository (REQUIRED)

Access to source code repositories. The plugin cannot function without this.

| Provider | MCP Server | Notes |
|----------|-----------|-------|
| **GitHub** | [github/github-mcp-server](https://github.com/github/github-mcp-server) | Official. Supports read-only mode (`GITHUB_READ_ONLY=1`). Recommended. |
| **GitLab** | Community MCP servers available | Check [MCP server registry](https://modelcontextprotocol.io/) for options |

**Required capabilities:** List repos, read file contents, search code, view commit history.

**Recommended setup:** Use a read-only personal access token scoped to your organization's repos. The plugin never needs write access.

### ~~cloud observability (OPTIONAL)

Live logs, metrics, and alarms from your cloud provider. Enables "why doesn't Y work?" and customer case investigation.

| Provider | MCP Server | Notes |
|----------|-----------|-------|
| **AWS CloudWatch** | [awslabs/cloudwatch-mcp-server](https://awslabs.github.io/mcp/servers/cloudwatch-mcp-server) | Log analysis, metric queries, alarm status |
| **Datadog** | Community MCP servers available | |

**Without this connector:** The plugin still answers questions from code and IaC files, but cannot correlate with live runtime behavior. It will note when live observability data would improve the answer.

### ~~cloud resources (OPTIONAL)

Read-only access to deployed cloud resource state. Enables comparing intended architecture (from IaC) with actual deployed state.

| Provider | MCP Server | Notes |
|----------|-----------|-------|
| **AWS CloudFormation** | [awslabs/cfn-mcp-server](https://awslabs.github.io/mcp/servers/cfn-mcp-server) | Use `--readonly` flag. Lists and reads deployed resources. |
| **AWS Cloud Control** | [awslabs/cfn-mcp-server](https://awslabs.github.io/mcp/servers/cfn-mcp-server) | Same server, broader resource coverage |

**Without this connector:** The plugin answers infrastructure questions from IaC files only and notes that live state was not verified.

## Capability Levels

| Level | Connectors | Capabilities |
|-------|-----------|-------------|
| **Code Only** | ~~code repository | `/ask` about code, `/explain-service`, `/audit`. No live infra. |
| **Code + IaC** | ~~code repository + Terraform/CDK in repos | Above + architecture questions from IaC files |
| **Full** | All three categories | Above + live log/metric correlation, drift detection, customer case investigation with runtime data |

The plugin automatically detects which connectors are available and adjusts its behavior. No configuration needed — just connect the MCP servers and the skills adapt.
