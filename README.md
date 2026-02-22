# AgentRails Power Platform Demo

Power Platform integration assets for [AgentRails](https://www.agentrails.io) x402 payment infrastructure. One custom connector unlocks the full AgentRails API across Power Automate, Power Apps, and Copilot Studio.

## Why Power Platform?

AgentRails is the only x402 payment infrastructure with native Power Platform support. For the 450M+ Microsoft 365 seats, this means:

- **Power Automate** - Automate agent workflows, payment notifications, and compliance reporting
- **Power Apps** - Build internal apps for agent management without code
- **Copilot Studio** - Natural-language agent operations in Microsoft Teams
- **Dataverse** - Sync payment data for Power BI dashboards and model-driven apps

## What's Included

```
agentrails-powerplatform-demo/
├── README.md                              # This file
├── custom-connector/
│   ├── agentrails-connector.swagger.json  # Full API connector (24 operations)
│   ├── apiProperties.json                 # Connector branding & auth config
│   └── README.md                          # Import guide
├── security-roles/
│   └── README.md                          # RBAC guide with role matrix
└── LICENSE
```

## Quick Start

### 1. Import the Custom Connector

1. Go to [make.powerautomate.com](https://make.powerautomate.com) > **Data** > **Custom connectors**
2. Click **+ New custom connector** > **Import an OpenAPI file**
3. Upload `custom-connector/agentrails-connector.swagger.json`
4. Set authentication to **API Key** with header `X-API-Key`
5. Create the connector

### 2. Create a Connection

1. Go to the **Test** tab on the connector
2. Click **+ New connection**
3. Enter your AgentRails API key
4. Test with the **GetPaymentStats** operation

### 3. Use in a Flow

Create a Power Automate flow that uses the connector:

**Example: Daily payment summary email**
1. Trigger: Recurrence (daily at 9 AM)
2. Action: AgentRails > GetPaymentStats
3. Action: AgentRails > GetBillingUsage
4. Action: Send email with payment volume and fees

**Example: Agent budget alert**
1. Trigger: Recurrence (hourly)
2. Action: AgentRails > GetAgentDashboard
3. Condition: If any agent balance < $1.00
4. Action: Post to Teams channel with agent name and balance

## Connector Operations (24 total)

| Category | Operations | Description |
|----------|-----------|-------------|
| **Agents** | 6 | Create, list, run, delete agents + dashboard |
| **Payments** | 3 | Stats, history, pricing |
| **Wallets** | 3 | Total balance, per-chain, supported chains |
| **Policies** | 6 | CRUD + toggle for spending policies |
| **Billing** | 2 | Usage summary, agent transactions |
| **Logs** | 2 | Execution logs, error logs |

See [custom-connector/README.md](custom-connector/README.md) for the full operation list and import guide.

## Security Roles

The [security-roles/](security-roles/) directory contains a role matrix and configuration guide for controlling access to AgentRails data in Dataverse:

| Role | Access Level |
|------|-------------|
| Admin | Full access to all resources |
| Finance | Read all + full billing |
| Operator | Full agent management |
| Auditor | Read-only for compliance |
| Viewer | Minimal read access |

Includes column-level security recommendations for sensitive fields like wallet addresses and API keys.

## Related Repos

| Repo | Description |
|------|-------------|
| [agentrails-copilot-actions](https://github.com/kmatthewsio/agentrails-copilot-actions) | Copilot Studio action definitions (FinanceOps + Agent Executor) |
| [agentrails-dataverse-integration](https://github.com/kmatthewsio/agentrails-dataverse-integration) | Dataverse table schemas and Power Automate sync flows |
| [AgenticCommerce](https://github.com/kmatthewsio/AgenticCommerce) | Core x402 payment infrastructure |

## Prerequisites

- Power Platform environment (any tier)
- AgentRails API key ([sign up free](https://sandbox.agentrails.io/api/signup))
- For security roles: Dataverse database provisioned in your environment

## License

MIT - see [LICENSE](LICENSE)
