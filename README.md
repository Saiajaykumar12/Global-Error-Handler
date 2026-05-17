# 🚨 Global Error Handler — n8n Workflow

A reusable error-handling workflow for n8n that automatically catches failures across all your workflows and sends formatted alerts via **Gmail** and **Slack**.

---

## 📋 What It Does

When any workflow in your n8n instance fails, this workflow:

1. **Captures** the full error details — error type, message, cause, HTTP status, API response body, failed node, and stack trace
2. **Formats** a rich HTML email and a structured Slack message
3. **Waits 10 seconds** (to avoid alert floods during rapid retries)
4. **Sends both alerts simultaneously** — email to Gmail and message to Slack

---

## 🔁 Workflow Flow

```
Error Trigger → Format Error Details → Wait (10s) → Send Email Alert
                                                  ↘ Send Slack Message
```

---

## ⚙️ Nodes

| Node | Type | Purpose |
|------|------|---------|
| Error Trigger | `n8n-nodes-base.errorTrigger` | Fires when any connected workflow fails |
| Format Error Details | `n8n-nodes-base.code` | Extracts and formats all error info into email HTML + Slack message |
| Wait | `n8n-nodes-base.wait` | 10-second delay before sending alerts |
| Send Email Alert | `n8n-nodes-base.gmail` | Sends a styled HTML error report via Gmail |
| Send a message | `n8n-nodes-base.slack` | Posts a formatted alert to a Slack channel |

---

## 🚀 Setup Instructions

### 1. Import the Workflow
- In n8n, go to **Workflows → Import**
- Upload `global-error-handler.json`

### 2. Configure Credentials
- **Gmail**: Connect your Gmail account via OAuth2
- **Slack**: Connect your Slack workspace via OAuth2

### 3. Update Settings
- In **Send Email Alert**: Change the `sendTo` email address to your own
- In **Send a message**: Select your desired Slack channel

### 4. Set as Error Workflow
- Go to each workflow you want monitored
- Open **Settings → Error Workflow**
- Select **🚨 Global Error Handler**

### 5. Activate
- Toggle the workflow to **Active**

---

## 📬 Alert Contents

### Email Alert (HTML)
- Workflow name, ID, and direct link
- Error type, message, cause, and HTTP status
- Failed node name and execution ID
- Full stack trace
- Direct links to open the workflow and view the execution

### Slack Alert
- Same information in Slack markdown format
- Clickable links to the workflow and execution

---

## 🛠️ Customization

| What to change | Where |
|----------------|-------|
| Alert email address | `Send Email Alert` node → `sendTo` field |
| Slack channel | `Send a message` node → `channelId` |
| Wait duration | `Wait` node → `amount` (in seconds) |
| n8n instance URL | `Format Error Details` node → `n8nBaseUrl` variable |
| Timezone | `Format Error Details` node → `toLocaleString` locale/timezone |

---

## 📌 Notes

- The workflow is set as its own error workflow (`errorWorkflow: 942T0nOXDOQ1sYZA`) to prevent infinite error loops
- Caller policy is set to `workflowsFromSameOwner` — only workflows under the same owner can trigger it
- The 10-second wait helps prevent duplicate alerts if a workflow retries quickly

---

## 🏷️ Tags

`Error Handling` · `n8n` · `Automation` · `Gmail` · `Slack`
