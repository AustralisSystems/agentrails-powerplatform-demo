# Security Roles for AgentRails on Power Platform

This guide covers how to configure Dataverse security roles and column-level security for AgentRails data in your Power Platform environment. Use these patterns to control who in your organization can view, edit, and manage agent payment data.

## Role Matrix

| Role | Agents | Payments | Wallets | Policies | Billing | API Keys |
|------|--------|----------|---------|----------|---------|----------|
| **Admin** | Full | Full | Full | Full | Full | Full |
| **Finance** | Read | Read | Read | Read | Full | None |
| **Operator** | Full | Read | Read | None | Read | Read |
| **Auditor** | Read | Read | Read | Read | Read | None |
| **Viewer** | Read | Read | None | None | None | None |

## Role Definitions

### Admin
Full access to all AgentRails resources. Can create/delete agents, manage policies, view wallet addresses and API keys, and access billing.

**Assign to:** Platform administrators, DevOps leads

### Finance
Read access to all payment and agent data. Full access to billing dashboards and usage reports. Cannot manage agents or policies.

**Assign to:** Finance team members, CFO, controllers

### Operator
Full agent management (create, run, delete). Read access to payment history for troubleshooting. Cannot modify policies or access billing details.

**Assign to:** ML engineers, agent developers, SRE

### Auditor
Read-only access to agents, payments, and policies. Cannot view wallet private keys or API keys. Designed for compliance reviews.

**Assign to:** Compliance officers, external auditors

### Viewer
Minimal read access to agents and payment summaries. No access to wallet details, policies, or billing.

**Assign to:** Stakeholders, project managers

## Column-Level Security

Sensitive fields should have column-level security profiles applied in Dataverse.

### High Sensitivity (restrict to Admin only)

| Table | Column | Reason |
|-------|--------|--------|
| Agent | `walletAddress` | Blockchain wallet identity |
| Agent | `privateKey` | Never exposed via API, but if synced |
| API Key | `key` | Raw API key (only shown at creation) |
| Payment | `payerAddress` | Wallet address PII |
| Payment | `recipientAddress` | Wallet address PII |
| Payment | `transactionHash` | On-chain transaction identifier |

### Medium Sensitivity (restrict to Admin + Finance)

| Table | Column | Reason |
|-------|--------|--------|
| Agent | `budget` | Financial allocation |
| Agent | `currentBalance` | Current USDC balance |
| Billing | `unbilledFeesUsd` | Outstanding charges |
| Payment | `amountUsdc` | Transaction amounts |

## Setup in Dataverse

### Step 1: Create Security Roles

1. Go to [admin.powerplatform.microsoft.com](https://admin.powerplatform.microsoft.com)
2. Select your environment > **Settings** > **Users + permissions** > **Security roles**
3. Click **+ New role** for each role above
4. Configure table-level permissions per the role matrix

### Step 2: Configure Column Security Profiles

1. Go to **Settings** > **Users + permissions** > **Column security profiles**
2. Create profiles: `AgentRails-HighSensitivity`, `AgentRails-MediumSensitivity`
3. Add the columns listed above to each profile
4. Assign users/teams to the appropriate profiles

### Step 3: Apply to Business Units

For multi-BU organizations:
- Create a **root business unit** for AgentRails
- Assign security roles at the BU level
- Use **team-based** access for cross-BU visibility (e.g., central Finance team needs access to all BU payment data)

## Policy Enforcement Integration

AgentRails policies (via `/api/org-policies`) complement Dataverse security roles:

| Layer | What It Controls | Where It Runs |
|-------|-----------------|---------------|
| **Dataverse Security Roles** | Who can view/edit data in Power Platform | Power Platform |
| **AgentRails Policies** | Agent spending limits, approval workflows | AgentRails API |

**Example:** A Finance role user can *view* all payment data in Dataverse, but cannot *create* a new agent or *modify* a spending policy via the API. The AgentRails API enforces its own RBAC via JWT claims and API key scoping.

## Recommendations

1. **Start with Viewer** as the default role for all users, then elevate as needed
2. **Always enable column security** on wallet addresses and API keys
3. **Use Entra ID groups** to manage role assignments at scale
4. **Audit log** all role changes via Dataverse audit settings
5. **Review quarterly** as your agent fleet and team grow
