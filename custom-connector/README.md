# AgentRails Custom Connector

A Power Platform custom connector that wraps the full AgentRails API. Once imported, every operation is available in Power Automate, Power Apps, and Copilot Studio.

## Files

| File | Purpose |
|------|---------|
| `agentrails-connector.swagger.json` | Swagger 2.0 connector definition (24 operations) |
| `apiProperties.json` | Connector branding and authentication metadata |

## Operations

### Agents (6 operations)
- **ListAgents** - List all AI agents
- **CreateAgent** - Create a new agent with budget
- **GetAgentInfo** - Get agent details by ID
- **RunAgent** - Execute a task with an agent
- **DeleteAgent** - Remove an agent
- **GetAgentDashboard** - Agents with aggregate stats

### Payments (3 operations)
- **GetPaymentStats** - Payment volume, settled/failed counts
- **GetPayments** - Payment history with filters
- **GetPricing** - Endpoint pricing and supported networks

### Wallets (3 operations)
- **GetTotalBalance** - Total USDC across all chains
- **GetBalanceByChain** - Per-network breakdown
- **GetSupportedChains** - Available blockchain networks

### Policies (5 operations)
- **ListPolicies** - All spending policies
- **CreatePolicy** - Create a spending policy
- **GetPolicy** - Policy details
- **UpdatePolicy** - Modify a policy
- **DeletePolicy** - Remove a policy
- **TogglePolicy** - Enable/disable a policy

### Billing & Logs (4 operations)
- **GetBillingUsage** - Transaction count, fees, volume
- **GetAgentTransactions** - Agent transaction history
- **GetLogs** - Execution logs (filter by level)
- **GetErrorLogs** - Error logs only

## Import Guide

### Option 1: Power Automate / Power Apps

1. Go to [make.powerapps.com](https://make.powerapps.com) or [make.powerautomate.com](https://make.powerautomate.com)
2. Navigate to **Data** > **Custom connectors**
3. Click **+ New custom connector** > **Import an OpenAPI file**
4. Upload `agentrails-connector.swagger.json`
5. On the **General** tab, verify the host is `api.agentrails.io`
6. On the **Security** tab, confirm:
   - Authentication type: **API Key**
   - Parameter label: `API Key`
   - Parameter name: `X-API-Key`
   - Parameter location: **Header**
7. Click **Create connector**
8. Go to the **Test** tab, create a new connection with your API key, and test an operation

### Option 2: Copilot Studio

1. In [Copilot Studio](https://copilotstudio.microsoft.com), open your copilot
2. Go to **Actions** > **+ Add an action** > **Connector**
3. Search for your custom connector name, or upload the swagger file directly
4. Configure the API key connection
5. Select which operations to expose to the copilot

### Option 3: paconn CLI

```bash
# Install the Power Platform connector CLI
pip install paconn

# Login
paconn login

# Create the connector
paconn create --api-def agentrails-connector.swagger.json --api-prop apiProperties.json
```

## Authentication

The connector uses API Key authentication. Your key is sent in the `X-API-Key` header with every request.

| Key Prefix | Environment | Use Case |
|------------|------------|----------|
| `ar_test_` | Sandbox (testnet) | Development and testing |
| `ar_live_` | Production (mainnet) | Real USDC transactions |

Get an API key:
- **Dashboard:** [app.agentrails.io](https://app.agentrails.io)
- **API:** `POST https://sandbox.agentrails.io/api/signup` with `{email, password}`
