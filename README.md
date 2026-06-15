# 🚨 Global Error Handler — n8n Workflow

> A production-ready, reusable error monitoring workflow for n8n that instantly alerts you via **Gmail** and **Slack** whenever any workflow fails — with full debug context in every alert.

---

## 🧩 The Problem It Solves

In production automation setups, workflows fail silently — and you only find out hours later when a client complains or data goes missing. This workflow acts as a **central nervous system** for your entire n8n instance: attach it once, and every failure across every workflow gets reported immediately with full context for debugging.

---

## ⚙️ How It Works

```
Any Workflow Fails
       ↓
Error Trigger (catches all failures)
       ↓
Format Error Details (extracts node, trace, HTTP status, cause)
       ↓
Wait 10s (prevents duplicate alerts on rapid retries)
       ↓
┌──────────────────────┬──────────────────────┐
│  Gmail HTML Alert    │   Slack Message       │
│  (full debug report) │   (instant ping)      │
└──────────────────────┴──────────────────────┘
```

---

## 📬 What Each Alert Contains

| Field | Description |
|---|---|
| Workflow Name & ID | Direct link to open it in n8n |
| Error Type & Message | Exact error for fast diagnosis |
| Failed Node | Which node broke and why |
| HTTP Status & API Response | Full upstream error context |
| Stack Trace | Complete trace for debugging |
| Execution ID | Link to the exact failed run |
| Timestamp | When it happened (timezone-aware) |

---

## 🔁 Workflow Nodes

| Node | Type | Purpose |
|---|---|---|
| Error Trigger | `n8n-nodes-base.errorTrigger` | Fires on any connected workflow failure |
| Format Error Details | `n8n-nodes-base.code` | Builds rich HTML email + Slack payload |
| Wait | `n8n-nodes-base.wait` | 10s delay to prevent alert floods |
| Send Email Alert | `n8n-nodes-base.gmail` | Sends styled HTML debug report |
| Send a message | `n8n-nodes-base.slack` | Posts formatted alert to Slack channel |

---

## 🚀 Setup (5 Steps)

### 1. Import the Workflow
- In n8n go to **Workflows → Import**
- Upload `global-error-handler.json`

### 2. Configure Credentials
- **Gmail**: OAuth2 connection
- **Slack**: OAuth2 workspace connection

### 3. Update Alert Destinations
- **Send Email Alert** node → set your `sendTo` email
- **Send a message** node → select your Slack channel

### 4. Attach to Your Workflows
- Open each workflow → **Settings → Error Workflow**
- Select **🚨 Global Error Handler**

### 5. Activate
- Toggle this workflow to **Active**

> ✅ Done — every future failure will now alert you automatically.

---

## 🛠️ Customization

| What to change | Where |
|---|---|
| Alert email address | `Send Email Alert` node → `sendTo` |
| Slack channel | `Send a message` node → `channelId` |
| Wait duration | `Wait` node → `amount` (seconds) |
| n8n instance URL | `Format Error Details` node → `n8nBaseUrl` |
| Timezone | `Format Error Details` node → `toLocaleString` |

---

## 📌 Design Decisions

- **Self-referencing error workflow**: This workflow is set as its own error handler (`errorWorkflow: 942T0nOXDOQ1sYZA`) to prevent infinite alert loops if the handler itself fails.
- **10-second wait**: Prevents duplicate Slack/email floods when a workflow retries multiple times in quick succession.
- **Caller policy**: Set to `workflowsFromSameOwner` — only your workflows can trigger it, preventing cross-account noise.

