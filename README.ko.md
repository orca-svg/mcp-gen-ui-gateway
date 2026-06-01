<div align="center">
  <h1>MCP Gen UI Gateway</h1>
  <p><strong>Claude Desktop을 위한 맥락 인식 공공서비스 GenUI — 프리토타입 단계의 MCP 프로젝트</strong></p>

  <a href="https://github.com/koi2026/mcp-gen-ui-gateway/actions/workflows/ci.yml"><img src="https://github.com/koi2026/mcp-gen-ui-gateway/actions/workflows/ci.yml/badge.svg" alt="CI"></a>
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-Apache--2.0-blue.svg" alt="License"></a>
  <a href="https://pnpm.io"><img src="https://img.shields.io/badge/pnpm-9-orange.svg" alt="pnpm"></a>
  <img src="https://img.shields.io/badge/TypeScript-5-blue?logo=typescript&logoColor=white" alt="TypeScript">
  <img src="https://img.shields.io/badge/status-pretotype-orange" alt="Status: Pretotype">
  <a href="README.md"><img src="https://img.shields.io/badge/README-English-blue" alt="English"></a>
</div>

---

**MCP Gen UI Gateway**는 한국 공공서비스를 위한 맥락 인식 **GenUI(Generated UI)** — Claude HTML Artifact로 렌더링되며 추가 인프라가 필요 없는 — 를 향해 나아가는 오픈소스 [MCP(Model Context Protocol)](https://modelcontextprotocol.io) 프로젝트입니다.

아직 **초기 단계**입니다. 지금 직접 실행하고 확인할 수 있는 것은 [`pretotype/genui-demo`](https://github.com/koi2026/mcp-gen-ui-gateway/tree/pretotype/genui-demo) 브랜치의 **동작하는 프리토타입**입니다 — 3개 고정 페르소나 시나리오로 `Claude → MCP → GenUI Artifact` 전 과정을 실증합니다. `main` 자체는 게이트웨이 도구의 초기·미검증 **G-1 MVP**를 담고 있습니다. 맥락 랭킹 "Matrix" 엔진, 실시간 공공데이터 소스, Federation은 **[로드맵](#로드맵)** — 설계 의도이지 출시된 기능이 아닙니다.

> **현재 상태 — 프리토타입 단계 (2026-06).** 실행 가능한 데모는 **`pretotype/genui-demo`** 브랜치에 있습니다(Stage 0, 고정 아티팩트). `main`은 게이트웨이 트랙 골격 — `schema` · `core` · `mcp-server`(G-1 MVP, 검증 대기) — 을 담습니다. Stage 0 이후의 모든 것(Matrix 채점, 실시간 소스, Federation)은 설계 의도입니다. 기능 존재를 가정하기 전에 [지금 동작하는 것](#지금-동작하는-것)과 [로드맵](#로드맵)을 읽으세요.

**[English README →](README.md)**

---

## 왜 MCP Gen UI Gateway인가?

한국 공공서비스는 수십 개의 포털(정부24, 홈택스, data.go.kr, 법령정보원)에 분산되어 있습니다. 이사를 처음 가는 신혼부부, 프리랜서, 박사후연구원 모두 같은 근본 문제에 직면합니다: 같은 정보가 존재하지만 **무엇이 중요하고 무엇을 먼저 해야 하는지는 맥락에 따라 완전히 다릅니다**.

게이트웨이는 세 가지 아이디어를 중심으로 설계되고 있습니다:

| 문제 | 지향하는 접근법 |
|------|----------------|
| 수많은 포털, 과도한 인지 부하 | MCP 도구 한 번의 호출로 사용자 상황에 맞는 컴포넌트만 반환 |
| 규칙 기반 필터링은 새 페르소나마다 폭발적으로 증가 | Matrix 채점 알고리즘 $O(i,c) = \sum_f S(i,f) \times W(f,c)$으로 모든 맥락에 일반화 *(로드맵: G-2)* |
| GenUI 화면은 신뢰할 수 있는 출처가 필요 | 모든 렌더링 블록이 공식 정부 API 또는 문서를 명시 |

프리토타입은 고정 콘텐츠로 그 아이디어의 **전달 경로와 사용감**을 증명합니다. 콘텐츠를 동적으로 선택하는 두뇌는 지금은 의도적으로 동결되어 있습니다 — [로드맵](#로드맵)을 보세요.

---

## 지금 동작하는 것

정직한 인벤토리 — 실제로 실행되는 것 대 아직 설계 의도인 것.

- ✅ **프리토타입 — Stage 0** *(브랜치: `pretotype/genui-demo`)* — exact-tag 라우팅(`[신혼부부]` / `[프리랜서]` / `[박사후연구원]`)이 3개 고정 self-contained HTML 아티팩트 중 하나를 반환하고, Claude Artifact로 그대로 열립니다. 회귀 동결 기준선입니다. 같은 브랜치는 **추가적 실험 슬라이스**(handoff 출처 검증, 규칙 기반 맥락 랭킹, `GenUIResponse` 봉투 + 동적 렌더러)도 담고 있으나 — 단위 테스트된 탐색일 뿐 출시된 게이트웨이가 아닙니다.
- ⚙️ **게이트웨이 G-1 MVP** *(브랜치: `main`)* — `schema` / `core` / `mcp-server`의 스키마 기반 MCP 도구 + Zod 계약 + SQLite 저장소. 구현은 됐으나 **검증·정리 대기** 상태입니다.
- 🚧 **그 외 모든 것** — Matrix 채점 엔진, 실시간 공공데이터 소스, Federation, React 소비자 렌더러 — 은 **로드맵**이며 아직 만들어지지 않았습니다.

이 줄 아래의 모든 내용은 이미 동작하는 것이 아니라 **프로젝트가 나아갈 방향**을 설명합니다.

---

## 비전: 완전한 게이트웨이의 동작 방식

> 이것은 **목표 아키텍처**입니다. Stage 0 프리토타입은 이 랭킹 엔진을 *동결*하고 대신 exact-tag로 라우팅합니다 — 실시간 오케스트레이션이 존재하기 전에 엔드투엔드 Artifact 경로를 증명하기 위함입니다.

```
사용자 발화
      │
      ▼
┌──────────────────────┐
│  맥락 추출기          │  inferContextVector(utterance)
│  (context-ranking)   │  → ContextVector (7차원):
│                      │    지역 · 생활사건 · 가구유형
└──────────┬───────────┘    직업상태 · 주거상태 · 긴급도 · 리스크
           │
           ▼
┌──────────────────────┐
│   Matrix 채점기       │  O(i,c) = Σ S(i,f) × W(f,c)   ← 로드맵 (G-2)
│   (core 패키지)       │  rankComponentCandidates(vector, pool)
└──────────┬───────────┘
           │  상위 k개 컴포넌트 선택
           ▼
┌──────────────────────┐
│   GenUI 렌더러        │  5-블록 팔레트:
│   (demo-ui / MCP)    │  hero · cta · checklist · risk · evidence
└──────────┬───────────┘
           │
           ▼
   Claude HTML Artifact
   (정부24 스타일 UI)
```

### Matrix 알고리즘 (목표 설계)

$$O(i,c) = \sum_f S(i,f) \times W(f,c)$$

| 기호 | 의미 |
|-----|------|
| `i` | 사용자 의도 (발화에서 추출한 맥락 벡터) |
| `c` | UI 컴포넌트 후보 (hero, checklist, 위험 카드 등) |
| `f` | 피처 차원 (긴급도, 실행 가능성, 리스크, 증거 신뢰도 등) |
| `S(i, f)` | 의도 `i`가 피처 `f`를 활성화하는 강도 |
| `W(f, c)` | 피처 `f`가 컴포넌트 `c`에 기여하는 가중치 |

**상위 k개 O(i, c)**가 화면에 표시될 컴포넌트를 결정하고 나머지는 숨겨집니다. 핵심은 이것입니다: 새 페르소나나 도메인 추가가 수백 개의 IF-THEN 규칙이 아니라 새 **가중치 벡터** 하나가 됩니다. 이것은 로드맵의 **G-2** 마일스톤이며, 출시된 프리토타입에는 연결되어 있지 않습니다.

---

## 데모: 하나의 프롬프트, 세 개의 페르소나

> **`pretotype/genui-demo`** 브랜치에서 실행됩니다([빠른 시작](#빠른-시작--claude-desktop-프리토타입) 참고).

프리토타입은 **고정** 콘텐츠로 핵심 개념을 보여줍니다: *같은 상황, 다른 최적 화면*. 아래 차이는 작성된 것이지 아직 실시간 랭킹으로 생성된 것이 아닙니다 — 그것을 동적으로 만드는 것이 바로 로드맵입니다.

**공통 프롬프트:**
> `대전 유성구로 이사 왔어요. 이사 관련 행정·세무·우리 동네 데이터를 한 곳에서 확인하고 싶어요.`

| 태그 | 페르소나 | 아티팩트 주요 내용 |
|-----|---------|------------------|
| `[신혼부부]` | 신혼부부 | 전세대출 현황, 전입신고, 아이행복카드 체크리스트 |
| `[프리랜서]` | 프리랜서 | 사업장 주소 변경, 세금계산서 유효성, 건강보험 |
| `[박사후연구원]` | 박사후연구원 | 소속기관 주소 변경, 연구비 이전 지원 |

---

## 빠른 시작 — Claude Desktop (프리토타입)

> 실행 가능한 프리토타입은 `main`이 아니라 **`pretotype/genui-demo`** 브랜치에 있습니다. 아래 클론 명령이 그 브랜치를 체크아웃합니다.

### 사전 요구사항

- [Claude Desktop](https://claude.ai/download) 로그인 상태
- Node.js **22 LTS** 또는 **24 LTS**
- pnpm 9+ (`corepack enable && corepack prepare pnpm@9 --activate`)

> ⚠️ **Node 26 Current 사용 금지** — `better-sqlite3`는 LTS 전용 사전 빌드 바이너리가 필요합니다.

### 1. 클론 및 빌드

```bash
git clone -b pretotype/genui-demo https://github.com/koi2026/mcp-gen-ui-gateway.git
cd mcp-gen-ui-gateway
pnpm install
pnpm --filter pretotype-mcp-gen-ui-gateway build
```

### 2. Claude Desktop에 MCP 서버 등록

`~/Library/Application Support/Claude/claude_desktop_config.json`을 열고 다음을 추가합니다:

```json
{
  "mcpServers": {
    "pretotype-mcp-gen-ui-gateway": {
      "command": "node",
      "args": ["<레포_절대경로>/packages/pretotype-server/dist/pretotype-index.js"]
    }
  }
}
```

`<레포_절대경로>`는 레포 루트에서 `pwd`를 실행한 결과로 교체합니다. Claude Desktop을 완전히 종료 후 재시작합니다.

### 3. 호스트 지시문 추가

Claude 대화 또는 프로젝트에 다음을 붙여넣습니다:

```
You render a public portal GenUI pretotype.

If the user includes exactly one of [신혼부부], [프리랜서], or [박사후연구원],
call render_pretotype_scenario with { "utterance": "<full user utterance>" }.

Render the returned HTML verbatim as a Claude HTML Artifact. Do not summarize,
rewrite, or redesign it. Official links are handoff URLs only — no login or
submission happens inside the artifact.

If the tag is missing or ambiguous, ask for exactly one of the three tags.
```

### 4. 데모 프롬프트 실행

```
[신혼부부] 대전 유성구로 이사 왔어요. 이사 관련 행정·세무·우리 동네 데이터를 한 곳에서 확인하고 싶어요.
```

Claude가 정부24 스타일 HTML Artifact를 열어야 합니다. 클론한 `pretotype/genui-demo` 브랜치에는 전체 설정과 문제 해결을 담은 `docs/claude-desktop-pretotype-connector.md`가 포함되어 있습니다.

---

## 로드맵

제품은 **버전마다 능력 하나씩** 성장합니다. 0.5와 0.6은 동일한 GenUI 렌더러를 공유하며 — *누가 데이터를 가져오는가*만 달라집니다:

```text
0.5   Claude (글루) ─► korean-law-mcp + pretotype-genui ─► GenUI Artifact
0.6   Claude ─► gateway ─► korean-law-mcp ─► GenUI ─► Artifact
```

**0.5**에서는 Claude가 호스트 글루로서 두 형제 커넥터를 호출하고, **0.6**에서는 게이트웨이가 하위 MCP 서버를 직접 오케스트레이션하므로 Claude에게는 커넥터가 하나로 보입니다. (라이브 상태 보드와 도메인 용어집은 version-ladder 문서와 함께 활성 작업 브랜치에 있습니다.)

| 버전 | 새로 더하는 능력 | 오케스트레이션 | 커넥터 | 상태 |
|------|------------------|----------------|--------|------|
| **0** | 고정 공공서비스 아티팩트 (컨텍스트 태그 3종) | — | pretotype | ✅ 출시 · 동결 |
| **0.5** | korean-law `action_plan` 5단계 UX를 GenUI Artifact로 | Claude가 글루(호스트) | korean-law + pretotype-genui (2개) | 🔜 다음 |
| **0.6** | **Federation** — 게이트웨이가 korean-law-mcp의 MCP *클라이언트*가 됨 | 게이트웨이 내부 | 게이트웨이 (1개) | ⬜ 예정 |
| **G-1–4** | Ranking Pipeline 채점 · 멀티소스 · 배포 | 게이트웨이 | 1개 | ⬜ 예정 |

- **"동결(Frozen)"**은 Stage 0 아티팩트 자체가 불변(회귀 기준선)임을 뜻합니다. 프리토타입 *가족*은 여전히 가산적으로 성장합니다(0.5, 0.6).
- **Federation**이 "게이트웨이"라는 이름값을 합니다: 커넥터 하나가 하위 MCP 서버(korean-law-mcp 우선)의 API를 재구현하지 않고 재사용하여, 형제 커넥터끼리 서로 호출할 수 없는 호스트 한계를 넘어섭니다.
- [비전](#비전-완전한-게이트웨이의-동작-방식)의 **Matrix 채점**은 Federation 위에 얹히는 **G-2** 단계입니다 — 0.5나 0.6의 일부가 아닙니다.

---

## 프로젝트 구조

`main`은 게이트웨이 트랙입니다. 실행 가능한 프리토타입(고정 3-페르소나 아티팩트 + Vite React `demo-ui`)은 여기가 아니라 **`pretotype/genui-demo`** 브랜치에 있습니다.

```
mcp-gen-ui-gateway/  (main 브랜치)
├── packages/
│   ├── schema/         Zod 스키마 — 의도 타입, 컴포넌트 팔레트, MCP I/O 계약
│   ├── core/           도구 서비스 + 초기 추천 스켈레톤 (G-1 MVP; Matrix는 G-2 예정)
│   ├── mcp-server/     MCP 게이트웨이 엔트리포인트 — 도구 + SQLite 저장소 (G-1 MVP)
│   └── browser-assist/ 실험적 Playwright 경계, core와 격리
├── docs/
│   ├── git-workflow.md · git-workflow.ko.md   브랜치 명명, 커밋, PR 규칙
│   ├── host-prompts.md                        호스트 지시문 변형
│   └── prd.md                                 제품 요구사항
├── CONTRIBUTING.md · CONTRIBUTING.ko.md
├── SECURITY.md
└── LICENSE                                    Apache-2.0
```

| 패키지 | 책임 | 성숙도 |
|--------|------|-------|
| `schema` | Zod 타입 정의 + JSON Schema 내보내기 | 지원 |
| `core` | 도구 서비스 + 초기 추천 스켈레톤, 외부 의존 없음; Matrix는 G-2 예정 | G-1 MVP, 미검증 |
| `mcp-server` | MCP 도구 등록, SQLite 변경 로그 | G-1 MVP, 미검증 |
| `browser-assist` | 실험적 gov24 실시간 소스 경계 | 실험적, 격리 |

---

## 개발 환경

```bash
pnpm install        # 모든 워크스페이스 의존성 설치
pnpm build          # 전체 빌드
pnpm test           # 전체 테스트
pnpm typecheck      # TypeScript 타입 검사
pnpm mcp            # 게이트웨이 MCP 서버 실행 (stdio)
pnpm schemas        # Zod 정의에서 JSON Schema 내보내기
```

> `dev`(React `demo-ui`)와 `pretotype:*` 스크립트는 해당 패키지가 위치한 **`pretotype/genui-demo`** 브랜치에서 실행됩니다.

---

## MCP 도구

`main`에서 `mcp-server`는 게이트웨이 G-1 MVP 도구를 등록합니다. 프리토타입 전용 도구는 `pretotype/genui-demo` 브랜치에 있습니다.

| 도구 | 위치 | 상태 |
|------|------|------|
| `searchBenefits` | `mcp-server` (main) | ⚙️ G-1 MVP — 비식별 프로필 조건으로 혜택 후보 검색 |
| `getBenefitDetail` | `mcp-server` (main) | ⚙️ G-1 MVP — 혜택의 구조화된 상세 정보 |
| `buildChecklist` | `mcp-server` (main) | ⚙️ G-1 MVP — 신청 준비 항목 생성 |
| `getApplicationGuide` | `mcp-server` (main) | ⚙️ G-1 MVP — 단계별 신청 안내 |
| `getChangeLog` | `mcp-server` (main) | ⚙️ G-1 MVP — 기록된 스냅샷·변경 이벤트 |
| `render_pretotype_scenario` | pretotype 브랜치 | ✅ exact 태그 하나에 대해 고정 self-contained HTML 아티팩트 반환 |
| `compose_dynamic_genui_response` · `render_dynamic_genui_template` | pretotype 브랜치 | 🧪 실험적 동적 GenUI 슬라이스 |

서버는 LLM을 포함하지 않습니다. MCP 호스트가 자연어, 후속 질문, 도구 호출을 오케스트레이션할 것으로 기대합니다.

---

## 기여하기

버그 리포트, 기능 제안, 아키텍처 RFC, 코드, 문서 기여를 환영합니다.

| 유형 | 방법 |
|------|------|
| 버그 | [버그 리포트 템플릿](.github/ISSUE_TEMPLATE/bug_report.md) |
| 아키텍처 | [RFC 템플릿](.github/ISSUE_TEMPLATE/rfc.md) |
| 코드/문서 | [CONTRIBUTING.ko.md](CONTRIBUTING.ko.md) · [docs/git-workflow.ko.md](docs/git-workflow.ko.md) |
| 브랜치 정책 | [docs/branch-retirement-policy.md](docs/branch-retirement-policy.md) — 머지 후 PR 브랜치 은퇴 기준 |

이슈 템플릿은 [`.github/ISSUE_TEMPLATE/`](.github/ISSUE_TEMPLATE)에서 확인하세요.

---

## 팀 역할

| 역할 | 영역 | 책임 |
|------|------|------|
| **A** | 소스 / MCP 어댑터 | 공공 데이터 연동 (gov24, RSS, SRT), MCP 도구 스키마 설계 |
| **B** | 의사결정 / Matrix / 렌즈 | 의도 파싱, Claude API 오케스트레이션, 가중치 보정 |
| **C** | 렌더러 / 데모 | GenUI React 컴포넌트 (KRDS), 데모 UI, 배포 |

---

## 라이선스

[Apache License 2.0](LICENSE) — Copyright 2026 MCP-Gen UI Gateway contributors.
