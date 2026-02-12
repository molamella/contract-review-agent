# Contract Review Skill Design

## Overview

A Claude Code skill (`/review-contract`) that reviews partner contract drafts by gathering full historical context from Salesforce, Slack, and Gmail, then delivering structured AI feedback and logging it to a Google Sheet.

## Team Context

- **Team:** Global Business Team (Cafe24)
- **Salesforce Org:** mhcho@cafe24corp.com (cafe24america3)
- **Key Objects:** Standard `Contract`, `Event`, `Account`
- **Partners:** TikTok, Meta (Facebook), Google, YouTube, Stripe, PayPal, eBay Japan, CROOZ SHOPLIST, AXES Payment, etc.

## Invocation

```
/review-contract ./drafts/tiktok-2026-renewal.pdf
/review-contract ./drafts/meta-api-agreement.docx --partner "Meta (Facebook)"
/review-contract ./contracts/google-ad-rev-share.pdf --partner Google --window 12
```

### Parameters

| Parameter | Required | Default | Description |
|-----------|----------|---------|-------------|
| file path | Yes | — | Local path to the contract draft (PDF, DOCX, text) |
| `--partner` | No | Auto-detected from draft | Salesforce Account name to match |
| `--window` | No | 6 | Months of Slack/meeting history to pull |

## Execution Flow

### Step 1: Read & Understand the Draft

- Read the file from the provided local path
- Extract: counterparty name, contract type (revenue share, API license, partnership, etc.), key terms, effective dates, obligations
- Auto-match the counterparty to a Salesforce Account name
- If `--partner` is provided, use that instead of auto-detection

### Step 2: Find Similar Contracts (Narrow Search)

- Query Salesforce Contracts for the matched Account
- Use AI to rank which past contracts are most similar in subject matter to the draft
- Pull full details of top matches for deep comparison

### Step 3: Broaden to Full History

- Pull all remaining contracts for that partner
- Query Events (meetings) linked to that Account
- Check memo__c for meeting notes

### Step 4: Pull Communications

- Look up the partner's Slack channel using the channel mapping
- Search recent messages within the --window period
- Search Gmail for threads with that partner's contacts

### Step 5: Assemble & Analyze

Bundle all context and perform:

1. **Term Comparison** — How do draft terms compare to previous contracts?
2. **Risk Identification** — Unusual terms, missing protections, changed pricing
3. **Historical Pattern Check** — Known issues from Slack/meetings that should be reflected
4. **Consistency Check** — Does draft align with recent discussions?
5. **Recommendation Summary** — Prioritized list of suggested changes

### Step 6: Log to Google Sheet

Append a row to "AI Contract Review Log" sheet:

| Column | Description |
|--------|-------------|
| Date | Run date |
| Reviewer | Who ran the skill |
| Partner Name | Matched partner |
| Contract Number | Draft identifier or SF contract number |
| Contract Type | Detected type |
| Key Risks Identified | Summarized risks |
| Recommended Changes | Prioritized change list |
| Comparison to Previous | Diff summary vs similar past contracts |
| Sources Referenced | Which SF records, Slack messages, meetings were used |
| AI Confidence Score | High / Medium / Low |
| Status | Defaults to "Pending Review" |

## Slack Channel Mapping

| Partner | Slack Channel |
|---------|---------------|
| Meta (Facebook) | meta_gb |
| TikTok | tiktok_gb |
| Google | google-aif |
| YouTube | youtube_gb |
| Stripe | issue-stripe |
| PayPal | 기타-제휴 |
| Internal (general) | 강글비 |
| US subsidiary (미국법인) | 미국법인_gb |

If a partner is not in the map, the skill asks the user to specify the channel and offers to save it.

## Output Format (Chat)

```
Partner: TikTok
Contract Type: Ad Revenue Share Renewal
Similar Past Contract: #00000132 (2023-03-24)

Critical Issues (N)
  - [description]

Important Notes (N)
  - [description]

Minor Suggestions (N)
  - [description]

Sources Referenced:
  - SF Contract #00000132
  - Slack: tiktok_gb (12 relevant messages)
  - 3 meetings from last 6 months

Logged to: [Google Sheet URL]
```

## MCP Integrations Required

- **Salesforce DX** — SOQL queries for Contract, Event, Account
- **Composio (Rube)** — Slack message search, Gmail search, Google Sheets creation/append
- **Local filesystem** — Read contract draft files
