# Cafe24 Global Partnership Tools

This project contains Claude Code commands for Cafe24's Global Partnership team workflows.

## Available Commands

| Command | Description |
|---------|-------------|
| `/review-contract` | Multi-agent contract review with risk analysis, context verification, and Korean report via Google Docs |
| `/draft-email` | Draft professional email from Slack message using Gmail history and Salesforce context |
| `/meeting-notes` | Transform meeting transcript into structured Korean notes and save to Salesforce |

## Setup Requirements

### MCP Servers Required

The following MCP server connections must be configured in your Claude Code settings before using these commands:

1. **Composio / RUBE** — Provides access to:
   - Gmail (search emails, read threads)
   - Slack (search messages, read channels)
   - Google Docs (read/write/update documents)
   - Google Drive (file access)

2. **Salesforce DX** — Provides access to:
   - SOQL queries (Contracts, Contacts, Accounts)
   - Org management

### Composio Connection Setup

Before running `/review-contract`, ensure active connections for:
- `gmail` — connected to your @cafe24corp.com account
- `slack` — connected to Cafe24 workspace
- `googledocs` — connected to your Google account
- `googledrive` — connected to your Google account

If a connection is inactive, the agent will prompt you to authenticate via a redirect URL.

### Slack Channel Mapping

The `/review-contract` command uses these channels for partner context:

| Partner | Slack Channel |
|---------|--------------|
| TikTok | #tiktok_gb |
| Stripe | #issue-stripe |
| Meta | #meta_gb |
| Google | #google-aif |
| YouTube | #youtube_gb |
| PayPal | #기타-제휴 |
| 미국법인 | #미국법인_gb |

## Usage

### Contract Review

```
/review-contract https://docs.google.com/document/d/{DOC_ID}/edit
```

This spawns a 4-agent team:
1. **Context Analyst** — Searches Salesforce, Gmail, Slack for partnership history
2. **Risk Advisor** — Legal risk analysis with redline suggestions
3. **Context Reviewer** — Verifies completeness of Agent 1's findings
4. **Final Reviewer** — Cross-checks all agents, produces Korean report, publishes to Google Docs

Output: Korean report published to the same Google Doc + local files in `reviews/{partner}-{YYYY-MM-DD}/`

Typical runtime: 25-40 minutes.

## File Structure

```
.claude/commands/          — Claude Code slash commands
reviews/                   — Contract review outputs (gitignored, sensitive)
docs/                      — Design documents and guides
```
