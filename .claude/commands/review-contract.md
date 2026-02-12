---
name: review-contract
description: Multi-agent contract review with risk analysis, context verification, and Korean deliverables via Google Docs
---

# Contract Review — Multi-Agent Team

Review a contract draft using a 4-agent team that searches Salesforce, Gmail, and Slack for partnership context, identifies risks with redline suggestions, verifies completeness, and produces a consolidated Korean report in Google Docs.

**Input:** Google Docs link to the contract draft
**Output:** Google Docs report in Korean with structured risk analysis

---

## Slack Channel Mapping

Use these channels when searching Slack for partner context:

| Partner | Slack Channel |
|---------|--------------|
| TikTok | #tiktok_gb |
| Stripe | #issue-stripe |
| Meta | #meta_gb |
| Google | #google-aif |
| YouTube | #youtube_gb |
| PayPal | #기타-제휴 |
| 미국법인 | #미국법인_gb |

For partners not listed, search broadly or ask the user.

---

## Team Structure

Create a team named `contract-review` with 4 tasks and the following dependency chain:

```
Agent 1 (Context Analyst)
    ├── Agent 2 (Risk Advisor)     [parallel, blocked by Agent 1]
    └── Agent 3 (Context Reviewer) [parallel, blocked by Agent 1]
            └── Agent 4 (Final Reviewer) [blocked by Agent 2 AND Agent 3]
```

**Critical:** When Agent 3 completes, forward its findings to Agent 2 via SendMessage before Agent 2 finishes. This ensures Agent 2 incorporates any newly discovered context.

---

## Agent 1: Context Analyst

**Role:** Search Salesforce, Gmail, and Slack for all historical context about the partner company.

**Prompt:**
```
You are the Context Analyst for a contract review. Your job is to search Salesforce, Gmail, and Slack to gather ALL historical context about the partner company named in this contract.

CONTRACT TEXT:
{contract_text}

SEARCH STRATEGY:

1. SALESFORCE (Contracts object):
   - Search by partner company name AND variations (English, Korean, abbreviations, parent company)
   - Search by partner domain (e.g., bytedance.com for TikTok)
   - Use SOSL for broad text search across Contract name, description fields
   - Search Contacts linked to the partner Account
   - For each contract found, note: contract number, name, start/end dates, status, key terms

2. GMAIL:
   - Search by partner domain: "from:{domain} OR to:{domain}"
   - Search by key people names found in Salesforce contacts
   - Search for contract-related terms: "subject:(agreement OR contract OR amendment) {partner_name}"
   - Search for attachments: "has:attachment (from:{domain} OR to:{domain})"
   - Check last 7 days separately (recent context is critical)

3. SLACK:
   - Search the mapped channel: {slack_channel}
   - Search terms: "{partner_name}", "contract OR agreement", "revenue share OR RS", "MDF OR marketing fund"
   - Also search #기타-제휴 for cross-channel mentions
   - Pay special attention to messages from the last 14 days
   - Look for meeting notes, negotiation updates, internal discussions

OUTPUT FORMAT:
Write results to {output_file} as markdown with these sections:
- Partnership Overview (company info, relationship timeline)
- Salesforce Records (accounts, contracts table, contacts)
- Email Summary (threads, key people, key discussions)
- Slack Summary (channel activity, critical messages, recent updates)
- Key People (contact table with names, roles, emails, source)
- Search Limitations (what you couldn't find and why)
```

---

## Agent 2: Risk Advisor (Legal Skill)

**Role:** Analyze the contract using the `contract-review` skill for structured risk identification with redline suggestions.

**Prompt:**
```
You are the Risk Advisor for a contract review. Use the contract-review skill to perform a structured legal analysis.

CONTRACT TEXT:
{contract_text}

CONTEXT FROM AGENT 1:
Read the file at {agent1_output_file} for partnership history and context.

ADDITIONAL CONTEXT FROM AGENT 3 (if available via message):
Incorporate any findings forwarded to you by the team lead from Agent 3's verification.

YOUR POSITION: Provider (Cafe24 Corp.)

Invoke the contract-review skill and follow its output format exactly:
- Pre-Signing Alerts
- Executive Summary
- Key Terms table
- Red Flags Quick Scan table
- Risk Analysis (🔴 Critical, 🔴 High, 🟡 Important) with redline suggestions for each
- 🟢 Reviewed & Acceptable
- Missing Provisions
- Payment/Merchant Agreement Checklist
- Internal Consistency Issues
- Negotiation Priority table with leverage points

For each risk, include:
- Quoted contract text
- Issue description
- Risk impact
- Market standard
- Negotiability rating
- Specific redline language (exact replacement text in English)
- Fallback position if primary ask is rejected

Write results to {output_file}.
```

---

## Agent 3: Context Reviewer

**Role:** Verify Agent 1's completeness by running independent searches to catch anything missed.

**Prompt:**
```
You are the Context Reviewer. Your job is to VERIFY that Agent 1 didn't miss any important historical context from Salesforce, Gmail, or Slack.

AGENT 1'S REPORT:
Read the file at {agent1_output_file}.

VERIFICATION CHECKLIST:

1. SALESFORCE GAP ANALYSIS:
   - [ ] SOSL search for "Campaign Schedule" across all objects
   - [ ] SOSL search for the formal agreement name (e.g., "Merchant Platform Agreement")
   - [ ] Account search using Korean name of partner (e.g., "틱톡")
   - [ ] Contact search for ALL people mentioned in Agent 1's email/Slack findings
   - [ ] Search for partner variations: parent company, "Pte", "Ltd", "Corp"
   - [ ] Check for duplicate contact records

2. GMAIL GAP ANALYSIS:
   - [ ] Search "subject:(campaign schedule) from:{domain} OR to:{domain}"
   - [ ] Search "(revenue share OR MDF OR marketing fund) ({partner_name} OR {domain})"
   - [ ] Search "has:attachment (from:{domain} OR from:{alternate_domain})"
   - [ ] Verify Agent 1's email count and thread coverage

3. SLACK GAP ANALYSIS:
   - [ ] Search "contract OR agreement OR campaign schedule" in primary channel
   - [ ] Search "{partner_name}" in #기타-제휴 (cross-channel check)
   - [ ] Search "{partner_name} revenue share OR {partner_name} RS" in primary channel
   - [ ] Check messages from last 48 hours specifically (Agent 1 may have missed very recent posts)

4. CROSS-REFERENCE:
   - [ ] Every person in Agent 1's email findings → check Salesforce contacts
   - [ ] Every contract in Salesforce → check if discussed in Slack
   - [ ] Any unidentified people mentioned in Slack → search Salesforce/Gmail

OUTPUT FORMAT:
Write results to {output_file} as markdown with:
- Salesforce Verdict (gaps found or confirmed complete)
- Gmail Verdict (gaps found or confirmed complete)
- Slack Verdict (gaps found or confirmed complete, flag NEW findings)
- Additional Context Discovered (anything Agent 1 missed)
- Confidence Score (1-5)
- Summary of Differences table (Item | Agent 1 Status | My Finding | Impact)
```

---

## Agent 4: Final Reviewer (Korean Output)

**Role:** Cross-check all 3 agents' work and produce the final consolidated report directly in Korean, then publish to Google Docs.

**Prompt:**
```
You are the Final Reviewer. Read all 3 previous agents' outputs and produce a consolidated contract review report DIRECTLY IN KOREAN (격식체).

INPUT FILES:
- Agent 1 (Context): {agent1_output_file}
- Agent 2 (Risk Analysis): {agent2_output_file}
- Agent 3 (Context Review): {agent3_output_file}

YOUR TASKS:

1. CROSS-CHECK: Verify consistency across all 3 reports. Flag any contradictions.

2. INCORPORATE AGENT 3 FINDINGS: If Agent 3 found context that Agent 1 missed, ensure it's reflected in the final risk assessment. Particularly check for:
   - Newly discovered meeting notes or Slack posts
   - Missing Salesforce contacts or contracts
   - Discrepancies between contract text and verbal/written agreements

3. PRODUCE FINAL REPORT IN KOREAN following this structure:

## 한국어 작성 규칙 (CRITICAL — FOLLOW EXACTLY):
- 모든 분석, 설명, 테이블 헤더, 권장사항은 한국어(격식체)로 작성
- 고유명사는 영어로 유지: TikTok, Cafe24, MPA, JBP, RS, MDF, GTM, Campaign Schedule, Revenue Share, Marketing Fund, Net Revenue, Qualified Advertiser, BATNA
- 계약서 직접 인용문(> blockquote)은 영어 원문 유지
- Redline 수정 제안 문구는 영어로 유지 (법률팀이 영문 계약서에 직접 반영해야 하므로)
- 금액($), 퍼센트(%), 날짜는 원문 그대로 유지
- 마크다운 서식, 이모지(🔴🟡🟢⚠️✓), 테이블 구조 유지

## 보고서 구조:

### 1. 요약 (Executive Summary)
- 전체 위험 평가 등급
- 서명 전 필수 조치 상위 3개

### 2. 파트너십 배경 및 이력
- Salesforce 계약 이력 테이블
- 주요 커뮤니케이션 요약
- 관계 타임라인

### 3. 계약 체결 전 주의사항
- 누락된 첨부 문서
- 계약/협상 불일치

### 4. 주요 조건 테이블

### 5. 위험 신호 (빠른 검색) 테이블

### 6. 위험 분석
- 🔴 Critical (각각 인용문, 문제점, 위험, 시장 표준, 수정 제안 포함)
- 🔴 중요
- 🟡 중요
- 🟢 검토 및 수용 가능

### 7. 누락된 규정 테이블

### 8. 내부 일관성 문제

### 9. 협상 우선순위 테이블 + 협상력

### 10. 검토 완성도 평가
- 신뢰도 점수
- 데이터 소스 분석
- 알려진 격차

### 면책 조항
"본 검토는 정보 제공 목적으로만 사용됩니다. 중요한 계약 조건은 자격을 갖춘 법률 고문에 의해 검토되어야 합니다."

4. SAVE locally to {output_file}

5. PUBLISH TO GOOGLE DOCS:
   - Create or update the Google Doc at {google_doc_id}
   - Due to API limitations (one table per API call), split content into sections
   - First call: GOOGLEDOCS_UPDATE_DOCUMENT_MARKDOWN with header + Section 1 (no tables, or only the first table)
   - Subsequent calls: GOOGLEDOCS_UPDATE_DOCUMENT_SECTION_MARKDOWN to append each section

   CRITICAL FORMATTING RULES TO PREVENT TABLE MERGING:
   - NEVER place two tables back-to-back. Always insert at least one full paragraph of text, a section heading (## or ###), or a horizontal rule (---) between any two tables.
   - Each section upload MUST start with its section heading (e.g., "### 4. 주요 조건 테이블") before any table content. This ensures Google Docs renders a visual break.
   - After every table, add a blank line and then the next heading or paragraph before the next table.
   - Do NOT duplicate content across section uploads — each section should contain only its own content.
   - When a section contains multiple sub-tables (e.g., Risk Analysis has tables per risk), separate each sub-table with the risk heading and description text.
   - Keep numbered/bulleted lists as text paragraphs, NOT inside tables.

   CRITICAL SECTION BOUNDARY RULES TO PREVENT HEADING ABSORPTION:
   - When Google Docs receives a GOOGLEDOCS_UPDATE_DOCUMENT_SECTION_MARKDOWN call, if the PREVIOUS section ended with a table, the NEW section's heading can get absorbed INTO that table as a row.
   - To prevent this: EVERY GOOGLEDOCS_UPDATE_DOCUMENT_SECTION_MARKDOWN call MUST begin with a horizontal rule (---) on the FIRST line, THEN a blank line, THEN the section heading. Example:
     ```
     ---

     ### 7. 누락된 규정 테이블
     ```
   - NEVER start a section upload directly with a heading (### ...) — ALWAYS prefix it with "---\n\n".
   - Each section that contains a table MUST be its own separate API call. Do NOT combine two table-containing sections into one API call.
   - After uploading ALL sections, verify with GOOGLEDOCS_GET_DOCUMENT_PLAINTEXT that EVERY section heading (1-10 + 면책 조항) appears as STANDALONE text, NOT inside a table cell.

   CRITICAL TABLE CELL LENGTH RULES TO PREVENT COLUMN SQUISHING:
   - Google Docs auto-distributes column widths based on content length. If one column has very long text, other columns get compressed and text appears vertical/unreadable.
   - MAXIMUM 3 columns per table whenever possible. If more columns are needed, split into multiple tables or restructure.
   - Keep ALL cell content SHORT — maximum 20 characters per cell. Use abbreviations:
     * "$10,000/분기 ($40,000/연)" → "$10K/Q ($40K/Y)"
     * "신규 + 휴면/비활성 계정" → "신규+휴면 계정"
     * "대폭 확대" or "4배 연장" → use as-is (already short)
   - For comparison tables (e.g., CS1 vs CS2), use a 3-column layout: "항목 | 이전 | 현재" and put the evaluation/assessment BELOW the table as a bullet list, NOT as a 4th column.
   - If a table MUST have 4+ columns, ensure every cell is under 15 characters. Longer content should be moved to footnotes or description text below the table.
   - Column headers should also be short: "2025 (CS1)" → "CS1", "2026 (CS2)" → "CS2"

   - Verify final output with GOOGLEDOCS_GET_DOCUMENT_PLAINTEXT — check for:
     (a) No duplicated paragraphs
     (b) No merged/squished tables (all columns should have readable horizontal text)
     (c) Every section heading is present and precedes its content
     (d) No cell content exceeds 20 characters — if it does, abbreviate or restructure
```

---

## Execution Flow

1. **Read the contract** from the provided Google Docs link using GOOGLEDOCS_GET_DOCUMENT_PLAINTEXT
2. **Identify the partner name** from the contract text (e.g., "tiktok", "stripe", "meta")
3. **Create review directory** at `reviews/{partner}-{YYYY-MM-DD}/` (e.g., `reviews/tiktok-2026-02-11/`)
4. **Save contract text** locally to `reviews/{partner}-{date}/contract-draft.txt`
5. **Create team** named `contract-review`
6. **Create 4 tasks** with dependencies:
   - Task 1: Agent 1 (no dependencies)
   - Task 2: Agent 2 (blocked by Task 1)
   - Task 3: Agent 3 (blocked by Task 1)
   - Task 4: Agent 4 (blocked by Tasks 2 and 3)
7. **Spawn Agent 1** → wait for completion
8. **Spawn Agent 2 + Agent 3** in parallel
9. **When Agent 3 completes:** Forward findings to Agent 2 via SendMessage
10. **When both Agent 2 and Agent 3 complete:** Spawn Agent 4
11. **Agent 4 produces Korean report** and publishes to Google Docs
12. **Return Google Docs link** to user

---

## File Naming Convention

Each review gets its own directory under `reviews/` using the pattern `reviews/{partner}-{YYYY-MM-DD}/`:

```
reviews/tiktok-2026-02-11/
  ├── contract-draft.txt        — extracted contract text
  ├── agent1-context-summary.md — Agent 1 output
  ├── agent2-legal-skill-review.md — Agent 2 output (legal skill format)
  ├── agent3-context-review.md  — Agent 3 output
  └── agent4-final-report-kr.md — Agent 4 final report (Korean)
```

The partner name is extracted from the contract text (lowercased, e.g., "tiktok", "stripe", "meta"). If multiple reviews of the same partner happen on the same day, append a counter: `reviews/tiktok-2026-02-11-2/`.
