# Contract Review Agent

AI-powered contract review tool that automatically analyzes partnership contracts by searching Salesforce, Gmail, and Slack for context, identifies legal risks with specific fix suggestions, and produces a Korean report in Google Docs.

Built for the **Cafe24 Global Partnership team**.

---

## What Does This Do?

When you give it a contract draft (as a Google Docs link), it:

1. **Searches your history** — Looks through Salesforce contracts, Gmail emails, and Slack messages to find everything about the partner
2. **Analyzes legal risks** — Identifies dangerous clauses, missing protections, and suggests exact wording changes
3. **Double-checks itself** — A separate agent verifies nothing was missed
4. **Writes a Korean report** — Produces a structured report in 격식체 Korean and publishes it directly to Google Docs

The whole process takes about 25-40 minutes.

---

## Before You Start (One-Time Setup)

You only need to do this once.

### Step 1: Install Claude Code

If you don't have Claude Code installed yet:

1. Open **Terminal** (press `Cmd + Space`, type "Terminal", press Enter)
2. Copy and paste this command, then press Enter:
   ```
   npm install -g @anthropic-ai/claude-code
   ```
3. Wait for it to finish installing

> If you get an error about `npm not found`, you need to install Node.js first.
> Go to https://nodejs.org and download the LTS version, install it, then try again.

### Step 2: Download This Project

1. Open **Terminal**
2. Copy and paste this command, then press Enter:
   ```
   cd ~/Documents && git clone https://github.com/molamella/contract-review-agent.git
   ```
   This downloads the project to your Documents folder.

> If you get an error about `git not found`, install it by running:
> ```
> xcode-select --install
> ```
> Then try the clone command again.

### Step 3: Open the Project in Claude Code

1. In **Terminal**, run:
   ```
   cd ~/Documents/contract-review-agent && claude
   ```
2. Claude Code will start up in this project

### Step 4: Connect Your Apps (First Time Only)

When Claude Code starts, it will ask you to approve MCP servers. These are the connections to your work apps.

**You will see prompts like:**
- `"rube" MCP server wants to connect` → Type **y** and press Enter
- `"Salesforce DX" MCP server wants to connect` → Type **y** and press Enter

**Then, the first time you run a review, you'll be asked to authenticate:**
- **Gmail** — Click the link, sign in with your @cafe24corp.com account
- **Slack** — Click the link, authorize Cafe24 workspace access
- **Google Docs** — Click the link, sign in with your Google account

After authenticating once, it remembers your connections for future runs.

---

## How to Run a Contract Review

### Step 1: Open the Contract in Google Docs

Make sure the contract you want to review is in a Google Doc. Copy the URL from your browser.

It should look something like:
```
https://docs.google.com/document/d/1AbCdEfGhIjKlMnOpQrStUvWxYz/edit
```

### Step 2: Start Claude Code

Open **Terminal** and run:
```
cd ~/Documents/contract-review-agent && claude
```

### Step 3: Run the Review Command

In Claude Code, type:
```
/review-contract https://docs.google.com/document/d/YOUR_DOC_LINK_HERE/edit
```

Replace `YOUR_DOC_LINK_HERE` with your actual Google Docs link.

### Step 4: Wait

The review runs through 4 stages:

| Stage | What's Happening | Time |
|-------|-----------------|------|
| Agent 1 | Searching Salesforce, Gmail, Slack | ~8 min |
| Agent 2 + 3 | Legal analysis + verification (parallel) | ~15-20 min |
| Agent 4 | Writing Korean report + publishing to Google Docs | ~10 min |

**Total: ~25-40 minutes.** You can leave it running and come back.

Claude Code will ask for permissions during the run (reading files, running searches). You can type **y** to approve each one, or type **shift+tab** to switch to auto-approve mode so you don't have to keep pressing y.

### Step 5: Get Your Report

When it's done, Claude Code will show you a **Google Docs link**. Click it to see your Korean report.

The report includes:
- 요약 (Executive Summary)
- 파트너십 배경 및 이력
- 위험 분석 (with specific redline suggestions in English)
- 누락된 규정
- 협상 우선순위 테이블

---

## Supported Partners

The tool automatically finds the right Slack channel for each partner:

| Partner | Slack Channel |
|---------|--------------|
| TikTok | #tiktok_gb |
| Stripe | #issue-stripe |
| Meta | #meta_gb |
| Google | #google-aif |
| YouTube | #youtube_gb |
| PayPal | #기타-제휴 |

For other partners, the tool will search broadly across channels.

---

## Troubleshooting

### "MCP server not connected" error
Run the review again. If it still fails, restart Claude Code (`Ctrl+C` to quit, then `claude` to restart) and approve the MCP servers when prompted.

### "Connection not active" for Gmail/Slack/Google Docs
The tool will give you an authentication link. Click it, sign in, then go back to Claude Code. The review will continue.

### Review is taking too long (60+ minutes)
This can happen if the Slack channel has a lot of messages. Let it finish — it's working. If it seems completely stuck, press `Ctrl+C` and start over.

### Google Docs report looks weird
If tables are squished or headings appear inside tables, run the review again. The formatting rules are designed to prevent this, but Google Docs can occasionally misbehave.

### "Permission denied" or "not authorized" for Salesforce
Make sure you have a Salesforce account with access to the Contracts object. Ask your Salesforce admin to grant access if needed.

---

## Quick Reference

| Action | Command |
|--------|---------|
| Start Claude Code | `cd ~/Documents/contract-review-agent && claude` |
| Run a review | `/review-contract https://docs.google.com/document/d/.../edit` |
| Quit Claude Code | `Ctrl + C` |
| Update to latest version | `cd ~/Documents/contract-review-agent && git pull` |

---

## Questions?

Ask Mina (mhcho@cafe24corp.com) or message in #tiktok_gb.
