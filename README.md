# Contract Review Agent (계약 검토 에이전트)

파트너십 계약서를 자동으로 분석하는 AI 도구입니다. Salesforce, Gmail, Slack에서 관련 이력을 검색하고, 법적 위험을 식별하여 수정 제안을 포함한 한국어 보고서를 Google Docs로 생성합니다.

**Cafe24 글로벌 파트너십팀** 전용 도구입니다.

---

## 이 도구는 무엇을 하나요?

계약서 초안 (Google Docs 링크)을 입력하면:

1. **이력 검색** — Salesforce 계약 기록, Gmail 이메일, Slack 메시지에서 해당 파트너에 대한 모든 정보를 찾습니다
2. **법적 위험 분석** — 위험한 조항, 누락된 보호 장치를 식별하고 구체적인 수정 문구를 제안합니다
3. **교차 검증** — 별도의 에이전트가 누락된 정보가 없는지 다시 한번 확인합니다
4. **한국어 보고서 작성** — 격식체 한국어로 구조화된 보고서를 작성하여 Google Docs에 게시합니다

전체 소요 시간은 약 **25~40분**입니다.

---

## 시작하기 전에 (최초 1회 설정)

아래 설정은 처음 한 번만 하면 됩니다.

### 1단계: Claude Code 설치

Claude Code가 아직 설치되지 않은 경우:

1. **터미널**을 엽니다 (`Cmd + Space` 누르고 "Terminal" 입력 후 Enter)
2. 아래 명령어를 복사해서 붙여넣고 Enter를 누릅니다:
   ```
   npm install -g @anthropic-ai/claude-code
   ```
3. 설치가 완료될 때까지 기다립니다

> `npm not found` 오류가 나오면 Node.js를 먼저 설치해야 합니다.
> https://nodejs.org 에서 LTS 버전을 다운로드하여 설치한 후 다시 시도하세요.

### 2단계: 프로젝트 다운로드

1. **터미널**을 엽니다
2. 아래 명령어를 복사해서 붙여넣고 Enter를 누릅니다:
   ```
   cd ~/Documents && git clone https://github.com/molamella/contract-review-agent.git
   ```
   Documents 폴더에 프로젝트가 다운로드됩니다.

> `git not found` 오류가 나오면 아래 명령어를 실행하세요:
> ```
> xcode-select --install
> ```
> 설치 후 위의 clone 명령어를 다시 실행하세요.

### 3단계: Claude Code에서 프로젝트 열기

1. **터미널**에서 아래 명령어를 실행합니다:
   ```
   cd ~/Documents/contract-review-agent && claude
   ```
2. Claude Code가 이 프로젝트 환경에서 시작됩니다

### 4단계: 앱 연결 (최초 1회)

Claude Code가 시작되면 MCP 서버 승인을 요청합니다. 업무 앱과의 연결 설정입니다.

**아래와 같은 메시지가 표시됩니다:**
- `"rube" MCP server wants to connect` → **y** 를 입력하고 Enter
- `"Salesforce DX" MCP server wants to connect` → **y** 를 입력하고 Enter

**이후 처음 계약 검토를 실행하면 인증을 요청합니다:**
- **Gmail** — 표시된 링크를 클릭하고 @cafe24corp.com 계정으로 로그인
- **Slack** — 표시된 링크를 클릭하고 Cafe24 워크스페이스 접근 권한 승인
- **Google Docs** — 표시된 링크를 클릭하고 Google 계정으로 로그인

한번 인증하면 이후에는 자동으로 연결됩니다.

---

## 계약 검토 실행 방법

### 1단계: Google Docs에서 계약서 준비

검토할 계약서가 Google Docs에 있어야 합니다. 브라우저에서 URL을 복사하세요.

URL은 아래와 같은 형태입니다:
```
https://docs.google.com/document/d/1AbCdEfGhIjKlMnOpQrStUvWxYz/edit
```

### 2단계: Claude Code 시작

**터미널**을 열고 아래 명령어를 실행합니다:
```
cd ~/Documents/contract-review-agent && claude
```

### 3단계: 검토 명령어 실행

Claude Code에서 아래와 같이 입력합니다:
```
/review-contract https://docs.google.com/document/d/여기에_문서_링크/edit
```

`여기에_문서_링크` 부분을 실제 Google Docs 링크로 바꿔주세요.

### 4단계: 기다리기

검토는 4단계로 진행됩니다:

| 단계 | 진행 내용 | 소요 시간 |
|------|----------|----------|
| Agent 1 | Salesforce, Gmail, Slack 검색 | ~8분 |
| Agent 2+3 | 법적 위험 분석 + 검증 (동시 진행) | ~15-20분 |
| Agent 4 | 한국어 보고서 작성 + Google Docs 게시 | ~10분 |

**총 소요 시간: 약 25~40분.** 실행 중 자리를 비워도 됩니다.

실행 중 Claude Code가 권한 승인을 요청할 수 있습니다 (파일 읽기, 검색 실행 등). **y** 를 입력하여 승인하거나, **Shift+Tab** 을 눌러 자동 승인 모드로 전환하면 매번 y를 누르지 않아도 됩니다.

### 5단계: 보고서 확인

완료되면 Claude Code가 **Google Docs 링크**를 표시합니다. 클릭하면 한국어 보고서를 확인할 수 있습니다.

보고서에 포함된 내용:
- 요약 (Executive Summary) — 전체 위험 등급 + 서명 전 필수 조치
- 파트너십 배경 및 이력 — Salesforce/Gmail/Slack 기반 이력 정리
- 위험 분석 — 각 위험 항목별 계약서 인용문 + 수정 제안 (영문)
- 누락된 규정 — 계약서에 빠져 있는 조항
- 협상 우선순위 테이블 — 협상력 점수 + 전략

---

## 지원 파트너

파트너별 자동 Slack 채널 매핑:

| 파트너 | Slack 채널 |
|--------|-----------|
| TikTok | #tiktok_gb |
| Stripe | #issue-stripe |
| Meta | #meta_gb |
| Google | #google-aif |
| YouTube | #youtube_gb |
| PayPal | #기타-제휴 |

위 목록에 없는 파트너는 Slack 전체 채널에서 자동으로 검색합니다.

---

## 문제 해결

### "MCP server not connected" 오류
검토를 다시 실행해 보세요. 계속 실패하면 Claude Code를 재시작합니다 (`Ctrl+C` 로 종료 후 `claude` 로 재시작). MCP 서버 승인 메시지가 나오면 **y** 를 입력하세요.

### Gmail/Slack/Google Docs "Connection not active" 오류
인증 링크가 표시됩니다. 링크를 클릭하여 로그인한 후 Claude Code로 돌아오면 자동으로 계속 진행됩니다.

### 검토가 너무 오래 걸릴 때 (60분 이상)
Slack 채널에 메시지가 많으면 시간이 더 걸릴 수 있습니다. 그대로 두면 완료됩니다. 완전히 멈춘 것 같으면 `Ctrl+C` 를 누르고 처음부터 다시 시작하세요.

### Google Docs 보고서가 이상하게 보일 때
표가 찌그러지거나 제목이 표 안에 들어가 있으면 검토를 다시 실행해 주세요. Google Docs API 특성상 간헐적으로 발생할 수 있습니다.

### Salesforce "Permission denied" 또는 "not authorized" 오류
Salesforce 계정에 Contracts 객체 접근 권한이 있는지 확인하세요. 권한이 없으면 Salesforce 관리자에게 요청하세요.

---

## 빠른 참고

| 작업 | 명령어 |
|------|--------|
| Claude Code 시작 | `cd ~/Documents/contract-review-agent && claude` |
| 계약 검토 실행 | `/review-contract https://docs.google.com/document/d/.../edit` |
| Claude Code 종료 | `Ctrl + C` |
| 최신 버전으로 업데이트 | `cd ~/Documents/contract-review-agent && git pull` |

---

## 문의

Mina (mhcho@cafe24corp.com) 에게 문의하거나 Slack #tiktok_gb 채널에 메시지를 남겨주세요.
