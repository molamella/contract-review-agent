# 글비 지식구축시스템 - 계약서 AI 리뷰 레시피 가이드

## 개요

파트너 계약서 초안을 AI가 자동으로 리뷰해주는 자동화 레시피입니다. Salesforce에 저장된 과거 계약서/미팅 이력, Slack 채널 대화, Gmail 이메일을 모두 참고하여 **Claude(변호사 정은 페르소나)와 Gemini 두 개의 AI가 독립적으로 분석**한 결과를 제공합니다.

분석 결과는:
- **Google Docs 리포트**로 자동 생성 (팀/법무팀 공유용)
- **Google Sheet**에 자동 기록 (감사 추적용)

---

## 사용 방법

### 1. 레시피 접속

아래 링크로 접속하세요:
**https://rube.app/recipe-hub/recipe-1770004032255**

### 2. 입력값 작성

| 입력 항목 | 필수 여부 | 설명 | 예시 |
|-----------|-----------|------|------|
| `google_doc_url` | **필수** | 리뷰할 계약서 초안의 Google Docs URL | `https://docs.google.com/document/d/1abc.../edit` |
| `partner_name` | 선택 | 파트너사 이름 (비워두면 계약서에서 자동 감지) | `Meta (Facebook)` |
| `slack_channel` | 선택 | 파트너 Slack 채널명 (`#` 제외, 비워두면 기본 매핑 사용) | `meta_gb` |
| `review_sheet_id` | **필수** | 리뷰 결과를 기록할 Google Sheet ID (기본값 설정됨) | — |
| `window_months` | 선택 | 검색할 과거 이력 기간 (기본값: 6개월) | `12` |
| `gemini_api_key` | **필수** | Gemini API 키 (기본값 설정됨) | — |

> **참고:** `review_sheet_id`와 `gemini_api_key`는 기본값이 설정되어 있으므로 별도로 입력하지 않아도 됩니다.

### 3. 실행

**"Run"** 버튼을 클릭하면 자동으로 실행됩니다. 클라우드에서 실행되므로 로컬 환경 설정이 필요 없습니다.

---

## 실행 흐름 (8단계)

```
Google Docs → 파트너 감지 → Salesforce 조회 → Slack/Gmail 검색 → Claude(변호사 정은) 분석 → Gemini 분석 → Google Docs 리포트 생성 → Google Sheet 기록
```

### Step 1. 계약서 가져오기
- Google Docs URL에서 문서 ID를 추출하여 계약서 전문을 가져옵니다.

### Step 2. 파트너 자동 감지
- 계약서 본문에서 상대방(파트너사) 이름, 계약 유형, 핵심 조건을 자동으로 파악합니다.
- `partner_name`을 직접 입력한 경우 해당 이름을 우선 사용합니다.

### Step 3. Salesforce 조회 (병렬 실행)
- 감지된 파트너명으로 Salesforce Account를 검색합니다.
- 매칭된 Account ID를 기반으로 다음을 **병렬**로 조회합니다:
  - 해당 파트너의 모든 과거 계약서 (Contract)
  - 설정된 기간 내 미팅 이벤트 (Event)

### Step 4. Slack/Gmail 커뮤니케이션 검색 (병렬 실행)
- 파트너 Slack 채널에서 관련 메시지를 검색합니다.
- Gmail에서 해당 파트너 관련 이메일 스레드를 검색합니다.
- 두 검색을 병렬로 실행합니다.

### Step 5. Claude AI 분석 (변호사 정은 페르소나)

Claude가 **20년 경력의 기업 법무 자문 변호사 "정은"** 페르소나로 계약서를 조항별로 심층 분석합니다.

**분석 구조:**

**1) 계약 개요**
- 계약 유형, 당사자, 주요 목적, 계약 기간, 총 금액/수수료 구조

**2) 조항별 심층 분석** (각 조항마다 5개 항목 포함)
- **현재 초안 내용**: 해당 조항의 핵심 내용 요약
- **과거 계약서 대비 변경사항**: 이전 계약 대비 무엇이 달라졌는지
- **리스크 평가**: Critical / Important / Minor 등급 부여
- **구체적 수정 제안**: 변경/추가/삭제해야 할 문구
- **근거**: Salesforce 기록, Slack 대화, Gmail, 미팅 노트 등 출처 명시

**3) 오타 및 문구 수정 제안**
- 법적 용어 오용, 오타, 문구 개선 제안

> **핵심 원칙**: 모든 조항이 분석 대상이며, 누락 없이 전수 검토합니다.

### Step 6. Gemini AI 분석

Claude와 독립적으로 Gemini가 별도의 관점에서 분석합니다:
- **Top 3 리스크**: 가장 우려되는 계약 조건
- **누락된 보호 조항**: 빠져 있는 표준 보호 조항
- **협상 포인트**: 서명 전 재협상 권장 조건
- **불균형 조항**: 일방에게 과도하게 유리한 조건
- **종합 평가**: Cafe24에 유리한지/균형적인지/불리한지 평가

### Step 7. Google Docs 리포트 생성

Claude와 Gemini의 분석 결과를 하나의 **Google Docs 문서**로 자동 생성합니다.

- 문서 제목: `[글비 지식구축시스템] 계약서 리뷰 - {파트너명} - {날짜}`
- **Part 1**: Claude(변호사 정은) 분석 결과 전문
- **Part 2**: Gemini 독립 분석 결과 전문
- 생성된 문서 URL이 화면에 출력되며, Google Sheet에도 기록됩니다.

> 이 문서를 팀원 및 법무팀과 공유하여 리뷰에 활용할 수 있습니다.

### Step 8. Google Sheet 기록

리뷰 결과가 자동으로 다음 시트에 기록됩니다:
**https://docs.google.com/spreadsheets/d/1aa2M4pOS3E7rtR35Z0UDEqz4QbTNnc2Mxnrr5EPrZgQ/edit**

기록되는 항목:

| 열 | 내용 |
|----|------|
| Date | 리뷰 실행 날짜 |
| Reviewer | 리뷰어 (mhcho) |
| Partner Name | 파트너사 이름 |
| Contract Number | 문서 ID |
| Contract Type | 계약 유형 (revenue share, API license 등) |
| Key Risks Identified | 식별된 주요 리스크 요약 |
| Recommended Changes | 권장 변경 사항 |
| Comparison to Previous | 과거 계약서 대비 차이점 |
| Sources Referenced | 참조한 데이터 소스 (SF 계약서 수, 미팅 수, Slack 메시지 수, Gmail 스레드 수) |
| AI Confidence Score | AI 신뢰도 (High/Medium/Low) |
| **Gemini Feedback** | Gemini AI의 독립적인 리뷰 결과 요약 |
| Status | 리뷰 상태 (Pending Review) |
| **Report Doc URL** | 생성된 Google Docs 리포트 링크 |

---

## Slack 채널 매핑 (자동)

파트너명을 입력하지 않거나 Slack 채널을 비워두면, 아래 매핑에 따라 자동으로 채널을 검색합니다:

| 파트너 | Slack 채널 |
|--------|------------|
| Meta (Facebook) | `meta_gb` |
| TikTok | `tiktok_gb` |
| Google | `google-aif` |
| YouTube | `youtube_gb` |
| Stripe | `issue-stripe` |
| PayPal | `기타-제휴` |

위 매핑에 없는 파트너의 경우, `slack_channel` 입력 항목에 직접 채널명을 입력해주세요.

---

## 데이터 소스

이 레시피는 다음 3가지 소스에서 과거 이력을 수집합니다:

| 소스 | 수집 내용 |
|------|-----------|
| **Salesforce** | 과거 계약서 (Contract), 미팅 이벤트 (Event), Account 정보 |
| **Slack** | 파트너 전용 채널의 관련 메시지 |
| **Gmail** | 파트너 관련 이메일 스레드 |

> Google Drive는 검색 대상이 아닙니다. 데이터 소스는 Salesforce, Slack, Gmail 세 가지입니다.

---

## 연동된 서비스

이 레시피는 아래 서비스에 연결되어 있어야 합니다. Rube 계정에서 최초 1회 연결 설정이 필요합니다.

| 서비스 | 용도 |
|--------|------|
| Google Docs | 계약서 초안 가져오기 + 리뷰 리포트 생성 |
| Salesforce | 과거 계약서, 미팅, Account 조회 |
| Slack | 파트너 채널 메시지 검색 |
| Gmail | 관련 이메일 스레드 검색 |
| Google Sheets | 리뷰 결과 기록 |
| Gemini API | 두 번째 AI 리뷰 (API 키로 직접 호출) |

> **팀원 연동**: 레시피 소유자가 연결한 서비스는 팀원도 그대로 사용할 수 있습니다. 별도 연결이 필요 없습니다.

---

## 산출물

레시피 실행 후 다음 산출물이 생성됩니다:

| 산출물 | 설명 |
|--------|------|
| **Google Docs 리포트** | Claude + Gemini 분석 결과가 담긴 공유 가능한 문서 |
| **Google Sheet 행** | 리뷰 이력 기록 (리포트 URL 포함) |

---

## 주의사항

- 계약서는 반드시 **Google Docs URL**로 제공해야 합니다 (로컬 파일 불가).
- 실행 시간 제한이 **4분**입니다. 대부분의 경우 충분하지만, 데이터가 매우 많은 파트너의 경우 일부 결과가 잘릴 수 있습니다.
- Gemini API 오류 발생 시 Claude 분석만 제공되며, Gemini Feedback 열에 오류 메시지가 기록됩니다.
- Claude 분석은 **한국어**로 출력됩니다 (변호사 정은 페르소나).
- Gemini 분석은 영문으로 출력됩니다.
- 모든 리뷰는 반드시 Google Sheet에 기록되므로 감사 추적(audit trail)이 가능합니다.

---

## 문의

설정 또는 사용 관련 문의: **조민하 (mhcho)**
