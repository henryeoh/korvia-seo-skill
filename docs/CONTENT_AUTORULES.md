---
last_verified: 2026-07
reverify_by: 2026-Q4
---

# 콘텐츠 자동적용 규칙 (발행 전 게이트) — CONTENT_AUTORULES

> Generated 2026-07-01 · Owner: Henry Oh (KORVIA) · ⚠ 콘솔/API/봇 user-agent/스키마 지원 여부는 사용 전 각 공식 문서에서 재확인.

이 문서는 `korvia-seo-skill/docs/`의 **콘텐츠 작성(how-to-write) 게이트** 레이어다. 신규 기사·홈페이지·랜딩에 **작성자 에이전트가 read-path로 적용**하는 **번호형 발행-전 게이트(규칙 SSOT = §12)**를 담으며, page-type별 적용·[AUTO]/[JUDGE] 판정 방식은 §12가 원본이다. 이 문서는 *무엇을 써야 하나(WHAT)*만 다루고, *어떻게 구현/제출하나(HOW)*는 아래 참조 레이어로 라우팅한다(중복 금지·상호링크).

**게이트 레이어(이 문서 그룹)**

| 문서 | 담당 |
|---|---|
| **이 문서 (CONTENT_AUTORULES)** | **프로즈를 어떻게 써야 순위+AI 인용을 동시에 얻나 — 발행 전 게이트** |
| `slop-judge` 스킬 | 발행 전 무결성 심사(Writing Quality 등급 + AI-SLOP 신호 + YMYL 하드페일) |
| [`AEO_GEO_PLAYBOOK_2026.md`](./AEO_GEO_PLAYBOOK_2026.md) | 전략(why/what·근거 수치·플랫폼별 인용 성향) |

**참조 레이어(구현·제출·엔진별 심화 — 이 게이트가 라우팅하는 목적지)**

| 문서 | 담당 | 이 문서에서 라우팅 |
|---|---|---|
| [`MULTI_ENGINE_MASTER_2026.md`](./MULTI_ENGINE_MASTER_2026.md) | 멀티엔진 색인·노출 허브(발행→전 콘솔 런북) | §9 |
| [`GOOGLE_SERP_STRUCTURED_AI_2026.md`](./GOOGLE_SERP_STRUCTURED_AI_2026.md) | Google 리치결과 생사·AIO/AI Mode·PAA | §1.6·§4 |
| [`SCHEMA_JSONLD_CATALOG.md`](./SCHEMA_JSONLD_CATALOG.md) | JSON-LD 복붙 템플릿·스키마 생사표 | §1.6 |
| [`NAVER_SEARCHADVISOR_PLAYBOOK.md`](./NAVER_SEARCHADVISOR_PLAYBOOK.md) | 네이버 콘솔·C-Rank/D.I.A·AI 브리핑 | §5 |
| [`DAUM_KAKAO_SEARCH.md`](./DAUM_KAKAO_SEARCH.md) | Daum/Kakao 색인·백엔드·소유권 변동 | §6.2·§9 |
| [`GOOGLE_SEARCH_CONSOLE_INDEXING.md`](./GOOGLE_SEARCH_CONSOLE_INDEXING.md) | GSC 등록·색인요청·사이트맵 | §9 |
| [`BING_YAHOO_INDEXNOW.md`](./BING_YAHOO_INDEXNOW.md) | Bing/Yahoo·IndexNow·ChatGPT 색인 경로 | §9 |
| [`ROBOTS_SITEMAP_RSS_TEMPLATES.md`](./ROBOTS_SITEMAP_RSS_TEMPLATES.md) | robots·sitemap·RSS·IndexNow 코드 템플릿 | §3.6·§9 |
| [`PROJECT_STACK_PLAYBOOK.md`](./PROJECT_STACK_PLAYBOOK.md) | Next.js/WordPress 구현·소유확인 매핑 | 프로젝트 노트 |
| [`WEB_PLAYBOOK.md`](./WEB_PLAYBOOK.md) · [`NEW_PAGE_CHECKLIST.md`](./NEW_PAGE_CHECKLIST.md) · [`../../KORVIA_WEB_PLAYBOOK.md`](../../KORVIA_WEB_PLAYBOOK.md) | 퍼블리싱/기술 표준(CLS·메타·a11y·보안헤더)·새 페이지 17항목·대규모 웹 작업 | §1·§7·§8 |

서빙 대상 4개 사이트: **Passome**(passome.com, Next.js/Vercel) · **Korvia**(korvia.com, Next.js, YMYL 비자·취업) · **Kimchi Mobile**(kimchimobile.com, WordPress, YMYL 통신·금융) · **ACT Art Center**(actartcenter.com, Next.js, 로컬 미술교육). 청중: ① 한국인→Naver, ② 재한·방한 외국인→Google(영어), ③ AI 답변엔진→GEO/AEO.

**근본 원리 (Google 공식, 2026-05-15)**: "생성형 AI 검색 최적화 = 검색 경험 최적화 = **여전히 SEO**." AI 전용 리라이팅·콘텐츠 청킹·`llms.txt`·특수 스키마는 Google 생성형 기능에 **불필요**. → "AI를 위한 별도 글"을 쓰지 말고, **잘 쓴 SEO 글**에 아래 발췌·신뢰 패턴을 겹쳐 쌓는다. (출처: developers.google.com/search/docs/fundamentals/ai-optimization-guide, 2026-05-15)

---

## 0. 자동 적용 규칙 (Auto-apply) — 작성자 에이전트 read-path용 요약

> **§12 마스터 게이트(규칙 SSOT)에서 파생된 작성자 에이전트 read-path용 요약**이다. 각 항목은 명령형이나 일부만 기계 검사 가능(AUTO/JUDGE 구분과 page-type 적용은 §12가 원본). 이 요약 자체에는 **판정·반려 권한이 없다** — 실제 반려/승인은 독립 체인(Quality Harness `[AUTO]` 실행 + slop-judge `[JUDGE]`, author≠judge)이 §12 기준으로 수행한다.

1. 각 섹션 헤딩 **직후 40~60단어 자기완결 즉답**을 둔다(모바일 타깃이면 36~44단어). 서론으로 답을 미루지 않는다.
2. 페이지 최상단에 **TL;DR/요약 박스** 1개(LLM 우선 픽업).
3. H1은 페이지당 1개, 핵심 엔티티+키워드 front-load. 주요 H2/H3는 **실제 사용자 쿼리(질문형)**.
4. 모든 핵심 주장 문장에 (검증가능 숫자 / 전문가 직접 인용문 / 명시적 1차 출처) 중 **최소 1개**를 물리적으로 붙인다. 글 전체 통계 **≥3곳**, 인용문 **≥1개**, 핵심 사실마다 출처.
5. "많다/빠르다/저렴하다" 같은 모호어 → 연도·%·금액·법령조문으로 치환.
6. 비교·자격·요금·등급은 **표 또는 번호 리스트**로 구조화(AI 발췌 단위).
7. 대명사·모호어(it/this/우리 서비스) 대신 **고유명**(브랜드/프로그램/제품명)을 반복.
8. 저자 블록(실명·직함·자격·프로필 링크) + 발행일 + **최종 수정일**을 가시 텍스트 + Article JSON-LD 양쪽에.
9. **1차 경험/실사례/실측 ≥1개**(직접 배치·개통·방문·테스트 — AI가 못 만드는 차별화).
10. YMYL(비자/취업/통신/금융/법령) 수치·절차는 **공식 1차 소스와 대조**, 과장·미검증 주장 0.
11. **FAQPage·HowTo JSON-LD를 SEO 리치결과 자산으로 홍보하지 말 것** — Google 리치결과 폐지(정정1·GOOGLE_SERP). 온페이지 Q&A 구조·파싱 목적만.
12. **`llms.txt`를 Google AEO 필수로 취급 금지** — Google 미사용(공식). 저ROI 실험 항목으로만(정정2).
13. KR/EN 짝은 **별도 URL + 왕복 hreflang(x-default 포함) + 올바른 ISO 코드**.
14. 한국어(Naver) 자산 = 경험형·소제목·구조; 영어(Google/GEO) 자산 = 즉답·통계·출처·질문형 헤딩. 엔진별 각도 분리.
15. robots.txt에서 **인용 검색봇 미차단** 확인: `OAI-SearchBot`·`PerplexityBot`·`Claude-SearchBot`·`Googlebot`·`Bingbot`·`Yeti`(Naver)·`Daumoa`(Daum). ⚠ `ChatGPT-User`·`Claude-User`는 user-fetch로 robots.txt를 무시하므로(출처: developers.openai.com/api/docs/bots) 검사 대상 아님. `Google-Extended`·`GPTBot`·`ClaudeBot`은 학습·그라운딩봇 — 차단해도 검색/AIO 노출에 무영향이므로 **사업 선택**(차단 가능·게이트 필수 아님). 봇 목록 SSOT = [`ROBOTS_SITEMAP_RSS_TEMPLATES.md`](./ROBOTS_SITEMAP_RSS_TEMPLATES.md) §A.
16. 발행 후 **GSC 색인요청 + IndexNow(Bing/Naver 계열) + Naver 서치어드바이저 수집요청 + Daum 별도 제출**(webmaster.daum.net 수집요청·register.search.daum.net — IndexNow로 커버 안 됨).
17. 발행 전 **§12 게이트 통과 + slop-judge(목표 Writing Quality A+, AI-SLOP 신호 0, YMYL 하드페일 0)**.

---

## [0] MUST-DO — 모든 신규 페이지 공통 요약 블록

> **§12 마스터 게이트에서 파생된 read-path 요약**이다(반려 권한 없음). page-type별 예외는 §12·'페이지 타입별 변형' 표를 따른다(예: homepage는 0.2 저자 블록·0.5 FAQ 불요).

작성 시작 시 아래 6개 블록이 **article 페이지** 골격에 **반드시** 존재해야 한다(homepage/landing은 page-type 변형 적용).

- **0.1** 상단 **TL;DR 박스** — 40~80단어. 페이지 전체 결론을 자기완결적으로. (LLM·featured snippet·AI Overviews 우선 픽업 단위)
- **0.2** **저자 블록** — 실명 + 직함 + 자격 1줄 + 프로필/바이오 링크. (E-E-A-T 게이트)
- **0.3** **발행일 + 최종 수정일** 가시 표기 — "2026-07-01 발행 / 2026-07-01 최신" + `datePublished`/`dateModified` JSON-LD.
- **0.4** **시점 명시** — "2026년 기준", "As of 2026-07" 등. 요금/법령/통계는 반드시.
- **0.5** **하단 FAQ 블록** — Q(실제 질문) / A(40~60단어 즉답) 3개 이상. (PAA·AIO 인용 유효. 단 FAQPage 리치결과 기대 X → 정정1)
- **0.6** **내부링크 3~5개** — 관련 페이지 + 프로그램/제품 허브, 서술형 앵커.

측정 기준(참고): 위 6개(page-type 변형 적용 후) 중 하나라도 없으면 **미완성**. 실제 판정·반려는 §12 마스터 게이트 기준으로 독립 체인(Quality Harness + slop-judge)이 수행한다.

---

## [1] Meta / Head

### 1.1 Title
- **명령(EN)**: 50~60자, **≤600px(데스크톱)**, 키워드 front-load(잘려도 핵심 생존).
- **명령(KR/Naver 최우선)**: 한글은 전각이라 50~60자면 ~700px+로 예산 초과 → **~25~33자**로 픽셀·Naver 표시 예산을 맞춘다(글자수 아니라 **픽셀 렌더가 최종 기준**, KR/EN 공통).
- layout 템플릿이 `| Brand`를 자동 부착하면 per-page title에 브랜드 **중복 금지**(이중 브랜드 방지).
- ⚠ 51~55자 구간에서 Google Title 재작성률이 높다는 관측 있음(벤더 추정, 공식 강제 아님 — 픽셀 렌더가 실제 기준). 출처: scalenut.com/blogs/meta-title-length-best-practices-2026

### 1.2 Description
- **명령(EN)**: 데스크톱 155~160자(≤~920px), **모바일 핵심은 첫 110~120자**(≤~680px)에. CTA 포함.
- **명령(KR)**: 한글 155자는 데스크톱 ~920px 절단선을 크게 초과 → 데스크톱 ~70~80자, **핵심(로드베어링)은 앞 ~40~45자**에. (픽셀 렌더 기준)
- AI 인용을 직접 늘리진 않으나 CTR·스니펫 후보에 영향. Google이 60~70% 재작성하므로 "핵심을 앞에".

### 1.3 Canonical
- **명령**: `alternates.canonical` = 절대 URL. **모든 페이지 self-canonical**(단일언어 페이지 포함 — 각 페이지가 자기 URL을 canonical로; hreflang와 canonical 충돌 금지). 게이트는 KR/EN 짝 여부와 무관하게 전 페이지의 canonical 존재·자기참조를 검사(§12 item15).

### 1.4 OG / Twitter
- `NEW_PAGE_CHECKLIST.md` A항 준수(중복 셋업 금지). 이미지 절대 URL, `type: article`이면 `publishedTime`·locale(`en_US`/`ko_KR`).

### 1.5 hreflang
- KR/EN 짝일 때만. 상세는 [6] 참조.

### 1.6 JSON-LD (스키마)
- **살아있는(2026 지원)**: `Article`/`BlogPosting`(+저자·`datePublished`·`dateModified`), `BreadcrumbList`(전 페이지 최소), `Organization`/`WebSite`(루트 1회, `app/layout.tsx`에 이미 있으면 **재주입 금지**), 타입별 `Product`/`JobPosting`/`LocalBusiness`/`Course(list carousel)`/`Event`/`Video`.
- **죽은/축소(리치결과 폐지)**: `FAQPage`·`HowTo`·`Sitelinks Searchbox`·`Course Info`·`Estimated Salary`·`Learning Video`·`Special Announcement`·`Vehicle Listing`·`ClaimReview`·`Practice Problem`. → 신규 생성 금지(상세 정정1·GOOGLE_SERP).
- **⚠ Book Actions는 "폐지 철회"** — 2025-11 Google이 폐지 배너를 제거, 여전히 지원. "사용 금지" 아님. (출처: etavrian.com/news/google-deprecates-practice-problem-structured-data)
- 권장 포맷 = **JSON-LD**(Google 공식). schema.org 800+ 타입 중 Google 리치결과 지원은 부분집합.
- → **복붙 템플릿·스키마 생사표**: [`SCHEMA_JSONLD_CATALOG.md`](./SCHEMA_JSONLD_CATALOG.md). **리치결과 생사·AIO 발췌 조건**: [`GOOGLE_SERP_STRUCTURED_AI_2026.md`](./GOOGLE_SERP_STRUCTURED_AI_2026.md). (여기선 넣을지/뺄지 규칙만, 코드는 카탈로그에서.)

---

## [2] 온페이지 구조 (answer-first / 발췌 유도)

### 2.1 헤딩
- **명령**: H1 페이지당 1개, 핵심 엔티티+주요 키워드 front-load.
- H2/H3를 **사용자 쿼리(질문형)**로: "How much can I earn teaching English in Korea?", "E-2 비자 발급에 필요한 서류는?" — passage-based indexing이 헤딩↔쿼리를 매칭.

### 2.2 Answer-first / 즉답 블록
- **명령**: 각 섹션 헤딩 **직후** 자기완결 즉답. **문단 스니펫 최적 = 40~60단어**(Google passage 추출 타깃).
- ⚠ 2026 벤더 관측치는 데스크톱 ~42단어/모바일 ~38단어로 더 타이트해지는 경향(Google 공식은 40~60단어까지만 — 추정으로 표기). 출처: dataenriche.com/best-content-length-for-featured-snippets/
- **정보 밀도**: 한 즉답 문단에 핵심 사실 5~7개.
- 데이터포인트는 긴 문단이 아니라 **표 행 또는 볼드 단독 클레임**으로(추출률↑).

### 2.3 발췌 유도 포맷
- **표(비교표)**: 가격/등급/자격 → AI가 구조화 사실로 인용. (Kimchi eSIM 요금, Korvia EPIK Level×급여 매트릭스)
- **번호형 리스트 / 단계형 How-to**: "Step 1 … Step N …" (리치결과는 사라졌지만 **발췌·파싱 가치 유지**).
- **패시지 길이**: 40~60단어 답변 블록 또는 100~300단어 자기완결 패시지(AIO 추출 스위트스팟 ~134~167단어).
- **엔티티 명시**: 대명사 대신 고유명 반복("we/it" → "Korvia's EPIK placement service").

### 2.4 TL;DR / 최신성
- 상단 TL;DR 박스(§0.1). 발행일+수정일 가시(§0.3). "2026년 기준" 시점 명시(§0.4).

---

## [3] GEO / AEO — 통계·인용문·출처·엔티티·즉답

### 3.1 Princeton GEO 레버 (핵심 원리)
출처: Aggarwal et al., *GEO: Generative Engine Optimization*, arXiv:2311.09735, KDD 2024.
- **확정 사실**: GEO 기법이 생성엔진 답변 내 가시성을 **최대 ~40%** 상승, **효과는 도메인별 상이**(arXiv abstract 확정 문구).
- **상위 레버(방향)**: 통계 추가 · 전문가 인용문 추가 · 출처 인용 추가 = 상위군. **키워드 스터핑 = baseline보다 -10%(역효과, 절대 금지)**.
- **⚠ 개별 퍼센트는 단정 금지**: 논문 본문 기준 상위 3개 방법이 PAWC 지표에서 30~40%, 단일 최고 = **Quotation Addition ~43%**, Cite Sources는 상위군 중 오히려 낮은 **~28%**. 흔히 도는 "Cite Sources +115%"는 **하위랭크(5위) 사이트 하위표 값**일 뿐 헤드라인이 아니다. 기존 `AEO_GEO_PLAYBOOK_2026.md` §2의 "인용문 +41%/통계 +30%/출처 +30%"는 **업계 요약본**이며 지표·도메인별로 다르다 — "연구상 상위 레버"로만 서술. (출처: arxiv.org/html/2311.09735v3)

### 3.2 작성 규칙화 (측정가능)
- **명령**: 주장 문장마다 (숫자 / 전문가 직접 인용문 / 명시적 1차 출처) 중 ≥1. 글 전체 통계 ≥3곳, 인용문 ≥1개, 핵심 사실마다 출처.
- "고용노동부 통계에 따르면 …", "「출입국관리법 시행령」 제23조" 처럼 **기관명·조문·연도**를 박는다.

### 3.3 즉답 40~80단어
- 질문형 헤딩 직후 40~60단어 즉답(§2.2). 상단 Quick Answer/TL;DR은 40~80단어까지 허용(AEO_GEO_PLAYBOOK §4.4와 정합).

### 3.4 엔티티 신호 (저비용·고효과)
- 우선순위: ① 엔티티 홈(About/회사 정식 페이지) → ② **Wikidata QID** → ③ `sameAs` 스키마(공식 SNS·사업자정보) → ④ 본문 엔티티 링킹 → ⑤ 언급 빌딩.
- ⚠ "브랜드 언급이 백링크보다 AIO 가시성 예측력 높음(상관 0.664 vs 0.218)"은 3자 분석(digitalapplied.com) — 방향 신호로만.

### 3.5 엔진별 각도 분리 (인용 파편화)
- ⚠ ChatGPT·Perplexity 동시 인용 도메인은 **~11%뿐** → 엔진별 전략 분리 필수(3자 분석, leapd.ai). 
- **ChatGPT**(Bing 인덱스 기반): 사실 정합·확립된 권위·구조. **Perplexity**(실시간 웹검색, 최신성 편향 최강): 최근 사례·후기·"Updated" 날짜. **Google AIO**(Googlebot 인덱스): 상위 랭킹+패시지 품질(전통 SEO 병행). **Claude**(web_search, 인용 강제·`cited_text ≤150자`): 깨끗한 시맨틱 HTML·명확한 섹션 헤더.
- ⚠ 인용 도메인 점유율(Reddit ~40~47%, Wikipedia ChatGPT 26~48%, YouTube 부상, 상위15 도메인=68%)은 스터디별 편차 극심(ChatGPT의 Reddit 인용 2025 60%→10% 급락) — **순위·방향으로만** 사용. (출처: semrush.com/blog/most-cited-domains-ai/)

### 3.6 AI 크롤러 접근 (인용의 전제조건)
robots.txt 허용(차단하면 인용 자체 불가). ⚠ **버전 문자열은 수시 갱신 — 발행/감사 시점에 각 공식 봇 문서로 재확인.**
- OpenAI: `GPTBot`(학습), `OAI-SearchBot`(ChatGPT 검색 인용 색인 — **차단=인용 탈락**), `ChatGPT-User`(유저 fetch). (출처: developers.openai.com/api/docs/bots)
- Anthropic: `ClaudeBot`(학습), `Claude-SearchBot`(검색 인용 — 독립 제어), `Claude-User`(유저 fetch). (출처: support.claude.com)
- Perplexity: `PerplexityBot`(인용 색인), `Perplexity-User`.
- Google: `Google-Extended`(⚠ **Gemini 학습·그라운딩만 제어 — AI Overviews는 통제하지 못한다**. AIO는 Googlebot 인덱스 사용), `Googlebot`.
- Bing: `Bingbot`(Copilot 색인 경유). ⚠ Daum은 Bing 백엔드가 아니라 **자체 Daumoa 엔진**(구 Bing 제휴는 종료로 알려짐 — 불확실) → `Bingbot` 허용이 Daum 색인을 커버하지 않음(§6.2·§9.4).
- **KORVIA는 인용 극대화 목적** → 위 검색·인용 봇 전면 허용. 학습봇(`GPTBot`/`ClaudeBot`/`CCBot`)은 정책 판단(검색 인용과 무관 — 인용 목적이면 굳이 차단 불필요).
- ⚠ **`Bytespider`(ByteDance)는 robots.txt를 무시하는 것으로 관측** → robots.txt `Disallow`만으로는 못 막는다. 차단이 필요하면 **CF WAF 등 서버단**에서. (봇 목록·차단 룰 SSOT = [`ROBOTS_SITEMAP_RSS_TEMPLATES.md`](./ROBOTS_SITEMAP_RSS_TEMPLATES.md) §A)
- ⚠ **AIO 인용을 원하면 `nosnippet`/`data-nosnippet`/`max-snippet:0`을 절대 걸지 말 것** — 스니펫 포기 = AIO 탈락.
- robots.txt 패치: `korvia-seo-skill/.claude/skills/seo-harness-team/resources/robots-txt-ai-patch.txt`. → **스택별 robots 템플릿·AI 봇 allowlist 코드**: [`ROBOTS_SITEMAP_RSS_TEMPLATES.md`](./ROBOTS_SITEMAP_RSS_TEMPLATES.md).
- ⚠ "OpenAI 봇이 AI 트래픽 ~69%"는 **측정 의존·상충**(Cloudflare 크롤 점유율로는 GPTBot ~11.5%). 절대 단정 금지, "출처·측정방식에 따라 상이"로. (출처: technologychecker.io/blog/chatgpt-statistics)

---

## [4] E-E-A-T / YMYL (신뢰 하드게이트 — Korvia/Kimchi 필수)

### 4.1 E-E-A-T
- **Experience·Expertise·Authoritativeness·Trust**, 그중 **Trust 최상위**. Google은 "EEAT 점수"를 직접 매기지 않고 **간접 신호**(도메인 평판·저자 명확성·출처 품질)로 판단. (출처: developers.google.com/search/docs/fundamentals/creating-helpful-content)
- **저자(Author)**: 검증 가능한 실명·자격·경력·프로필 링크 → `Article.author`(Person) + `author.url`/`sameAs`. 익명·무출처는 더 엄격히 평가.
- **Experience(첫 E)**: AI가 못 만드는 **1차 경험**(직접 배치 사례, 실제 개통 후기, 방문·테스트) = slop 홍수 속 최대 차별화.

### 4.2 YMYL 범위 (2025-09 QRG 확장)
- 건강·금융·법률·안전 + **정부·시민·이민** 포함으로 확장. → **Korvia(비자·취업·E-2·D-10)·Kimchi(통신·금융 결제·요금)**는 최고 EEAT 기준.

### 4.3 YMYL 하드 요구 (측정가능)
- **명령**: 모든 변동 사실(비자 요건·수수료·기한·요금·약관)은 **"As of YYYY-MM" + 공식 1차 소스 링크**(HiKorea·법무부 출입국·EPIK/NIIED·과기정통부·통신사 공식).
- Organization + 일관 NAP(Name/Address/Phone). KORVIA 권위 근거(2013 서울시장 표창, 2018/2021 고용노동부 우수기관 인증) 일관 명시.
- **부정확·과장 = 하드페일**(slop-judge YMYL 게이트 연계, §12).
- **[하드룰] 생성형 AI 응답 조작·대량 AI 양산 금지**: Google 검색 스팸정책(2026-05-15)은 "구글 검색의 생성형 AI 응답을 조작하려는 시도"와 "가치 없는 대량 AI 생성 페이지(scaled content abuse)"를 스팸으로 명시하며, 위반 사이트는 **순위 강등 또는 색인 제외(de-index)** 대상이다. AIO/AI 인용을 노린 프롬프트 인젝션·숨김 텍스트·대량 스핀 콘텐츠 절대 금지. (출처: developers.google.com/search/docs/essentials/spam-policies, 2026-05-15)

---

## [5] 네이버 특이 (한국어 자산 — Google과 이원화)

> ⚠ 아래 정량치는 대부분 한국 SEO 업체 **추정치**(Naver 알고리즘 미공개). 기사·산출물에 **단정 인용 금지**, "추정" 라벨.
> → **콘솔 실무(등록·소유확인·수집요청 한도·C-Rank/D.I.A·AI 브리핑 심화)**: [`NAVER_SEARCHADVISOR_PLAYBOOK.md`](./NAVER_SEARCHADVISOR_PLAYBOOK.md). 여기선 콘텐츠 작성 규칙만.

### 5.1 랭킹 3대 엔진
- **C-Rank**(출처 신뢰) = 블로그/작성자의 주제 집중도·신뢰·인기(3C: Context·Content·Chain). **D.I.A**(문서 경험·정보성·원본성), **D.I.A+**(문서×질의의도). 랭킹 ≈ C-Rank × D.I.A/D.I.A+.
- ⚠ "C-Rank+D.I.A+가 랭킹의 80%+", "주제전문성 40%/지속성 30%…" 가중치는 **비공식 추정**. (출처: ascentkorea.com, twinword.co.kr)

### 5.2 에어서치·스마트블록
- 개인화·주제단위 노출. 라이프스타일 키워드 상당수가 스마트블록으로 노출 → **하위 의도별 소제목·즉답**을 갖춰 여러 블록에 걸리게.

### 5.3 콘텐츠 형식 (원본성/이미지/구조)
- **명령**: 정보성 한국어 본문은 소제목 여러 개로 충분한 깊이. ⚠ "평균 2,000~3,000자·체류 2.5~3분+"는 추정치 — **글자수 채우기가 아니라 세부의도 충족+구조**로 정당화.
- **직접 촬영 원본 이미지·스크린샷·표** 필수(스톡/AI 이미지 단독 남발 감점 — D.I.A 원본성). 파일명 서술형 + alt + 캡션.
- 소제목을 **실제 검색 질의문** 형태로("E-2 비자에 재직증명서가 필요한가요?"). ⚠ 네이버 **'연관검색어' 서비스는 2026-04-30 종료**(약 20년 만; 전자신문 등 다수 보도) → 연관검색어 블록 타깃팅은 무효. **자동완성(입력 중 드롭다운)·'관련 질문'·AI 브리핑용 실제 질의문을 소제목으로 흡수**(대체 표면). (출처: etnews.com/20260407000435)

### 5.4 외부 홈페이지의 한계
- KORVIA 4사이트는 전부 **외부 홈페이지** → 네이버 실질 노출 영역 = **①웹사이트/웹문서 ②AI 브리핑 출처**뿐. 블로그·카페·인플루언서·지식iN 영역은 **네이버 자산(공식 블로그/포스트/플레이스)을 위성으로** 별도 운영해야 진입.

### 5.5 2025~2026 대전환 (반드시 반영)
- **AI 브리핑**: 통합검색 20%+ 적용, 정보형 쿼리 발동. 채택 = 요약가능 구조 + C-Rank 출처신뢰 + AEO(질문형·즉답·표).
- ⚠ **AI 탭은 "예정"이 아니라 LIVE** — 2026-04 멤버십 베타 → **2026-06 정식 출시 완료**. 외부 홈페이지가 AI 탭/브리핑의 **출처로 인용되는 것**을 실전 과제로. (출처: newspim.com/news/view/20260625000973)
- ⚠ **Cue:/ClovaX는 2026-04-09 종료 완료** — 죽은 서비스, 별도 GEO 타깃 금지. 네이버 생성형 노출은 **AI 브리핑/AI 탭**으로 수렴.
- ⚠ 네이버 검색 점유율 62.86%는 "**3년 만에 60%대 재돌파(Internet Trend 2025 연평균)**"로만 표기 — "8년 최고치"는 근거 불명. (출처: tech42.co.kr)
- ⚠ 네이버 JSON-LD 리치결과 지원은 **공식 문서 없음** — "별점/FAQ 리치스니펫이 뜬다" 단정 금지. **OG 태그만 확실**, JSON-LD는 구글·AI브리핑 대비 목적으로만.

### 5.6 기술 (서치어드바이저)
- 콘솔 `https://searchadvisor.naver.com/`. 크롤러 UA = **`Yeti`**(차단 금지). 사이트맵/RSS 제출, `요청 > 웹페이지 수집`으로 신규 URL 직접 제출(수집 1~4주). ⚠ 일일 제출 한도는 콘솔 로그인 후 실측(공개 문서 불명).

---

## [6] 한/영 이중언어

### 6.1 URL·hreflang (측정가능)
- **명령**: 한/영은 **각각 별도 URL**(`/en/`·`/ko/` 서브디렉토리 권장 — 권위 통합).
- **왕복(reciprocal) hreflang 필수**: A→B면 B→A도. **자기참조 포함**(각 페이지가 자기 자신+모든 변형 링크). **절대(fully-qualified) URL**만. `x-default` 포함(폴백).
- **ISO 코드**: 언어 = ISO 639-1 소문자(`ko`,`en`), 지역 = ISO 3166-1 Alpha-2 대문자(`KR`; 영국은 **`GB`**, UK 아님). 지역타깃 필요시 `ko-KR`·`en-KR`, 언어만이면 `ko`·`en`. (출처: developers.google.com/search/docs/specialty/international/localized-versions)
- ⚠ **클러스터 내 한 개라도 틀리면 Google이 전체 무시** — 구현의 ~75%가 오류(왕복 누락·잘못된 코드). hreflang은 힌트(hint), canonical·콘텐츠 유사도가 실제 표시 버전을 좌우.

### 6.2 언어별 최적화 축 분리
- **한국어 자산 = Naver 규칙**(경험형·소제목·원본 이미지·AI 브리핑). **영어 자산 = Google/GEO 규칙**(즉답·통계·출처·질문형 헤딩·ChatGPT/Perplexity).
- ⚠ **Naver는 hreflang 미준수** — 네이버 대상 콘텐츠는 위젯·메뉴·푸터·이미지 내 텍스트까지 **전부 한국어**여야.
- ⚠ **Daum ≠ Bing 백엔드** — Daum은 **자체 Daumoa 엔진**이며 **IndexNow 미참여** → Bing 최적화·IndexNow가 Daum을 커버하지 못한다. Daum은 **webmaster.daum.net 수집요청 + register.search.daum.net 검색등록**으로 **반드시 별도 처리**(§9.4). (⚠ 구 Bing 제휴는 종료로 알려짐 — 시점·현황 재확인) → **Daum 색인 경로·소유권 변동·실효성 판단**: [`DAUM_KAKAO_SEARCH.md`](./DAUM_KAKAO_SEARCH.md).

---

## [7] 내부링크 · 앵커

- **명령**: 본문에 관련 페이지 **3~5개 + 프로그램/제품 허브** 링크, **서술형 앵커**(URL·"여기" 금지).
- 허브 URL 규칙 통일(`/korvia-{program}` vs `/{program}-full-name` 혼용 금지 — `WEB_PLAYBOOK.md` §9). Header mega-menu ↔ Footer rail 링크 대조.
- **오펀 방지**: JS "load more" 뒤 콘텐츠는 SSR로 전부 렌더(`visibleCount = posts.length`), 필터는 CSS `hidden` 토글.
- 허브 페이지 body 링크 2~3개 미만 = dead-end → 관련 articles/sub-pages 렌더.

---

## [8] 이미지 alt / 최적화

- **명령**: 모든 `<img>`/`<Image>`에 **서술형 alt**(내용 설명 + 키워드; 장식은 `alt=""`). 멀티모달 AI·이미지 검색·AIO 멀티모달 노출 대응.
- 고해상(Article `image` ≥50K px), 본문 관련(로고 아님), 16:9/4:3/1:1 3종 제공.
- 기술 최적화(width/height·`next/image`·AVIF/WebP·`sizes`·CLS)는 `WEB_PLAYBOOK.md` §4·§6, `NEW_PAGE_CHECKLIST.md` C항 준수(중복 금지).
- **원본 이미지 우선**(직접 촬영/제작) — Naver D.I.A 원본성 + AI 멀티모달.

---

## [9] 발행 후 제출 (색인 가속 = AI 인용 타임라인 단축)

> → **한 번 발행 → 전 콘솔 엔드투엔드 런북**: [`MULTI_ENGINE_MASTER_2026.md`](./MULTI_ENGINE_MASTER_2026.md) §5. 아래는 게이트용 최소 요약이며 콘솔별 절차·API·한도는 각 참조 문서로.

- **9.1 Google**: Search Console에 sitemap 재제출 + 신규/변경 URL 'URL 검사 → 색인 요청'. (⚠ Google은 IndexNow **미지원** — sitemap+GSC 유지.) → 상세 [`GOOGLE_SEARCH_CONSOLE_INDEXING.md`](./GOOGLE_SEARCH_CONSOLE_INDEXING.md).
- **9.2 Bing + IndexNow**: Bing Webmaster 등록 + **IndexNow 핑**. IndexNow 참여 엔진(2026-07-02 확인) = **Bing·Naver·Yandex·Seznam.cz·Yep·Amazon**(ChatGPT는 Bing 인덱스 경유 간접). ⚠ **Google·Daum은 미참여.** 한 참여 엔진의 `/indexnow` 엔드포인트로 제출하면 나머지 참여 엔진에 자동 전파. Next.js는 배포 훅에서 IndexNow API POST, WordPress(Kimchi)는 플러그인. (출처: indexnow.org/faq) → **참여 목록 SSOT = [`BING_YAHOO_INDEXNOW.md`](./BING_YAHOO_INDEXNOW.md)**, 코드 템플릿 [`ROBOTS_SITEMAP_RSS_TEMPLATES.md`](./ROBOTS_SITEMAP_RSS_TEMPLATES.md) §D.
  - ChatGPT가 Bing 인덱스를 쓰므로 **IndexNow 제출 = ChatGPT 인용 가속(간접)**.
- **9.3 Naver**: 서치어드바이저 `요청 > 웹페이지 수집` 직접 제출(+ IndexNow로도 전파). → 상세 [`NAVER_SEARCHADVISOR_PLAYBOOK.md`](./NAVER_SEARCHADVISOR_PLAYBOOK.md).
- **9.4 Daum**(별도 필수): Daum=**자체 Daumoa 엔진 + IndexNow 미참여** → 9.2(Bing/IndexNow)로 **커버 안 됨**. **webmaster.daum.net에서 수집요청(사이트/URL) + register.search.daum.net 검색등록**을 **반드시 별도 수행**. → 상세 [`DAUM_KAKAO_SEARCH.md`](./DAUM_KAKAO_SEARCH.md).
- **9.5 검증**: `site:도메인`(색인 확인), `curl -sL <url> | grep -oE '<link rel="canonical"[^>]*>|"@type":"[A-Za-z]+"'`(canonical+schema), Rich Results Test(단 FAQ 필터는 제거됨 — 정정1).

---

## 페이지 타입별 변형 (article / homepage / landing)

| 항목 | **article**(가이드/블로그) | **homepage**(사이트 루트) | **landing**(프로그램/제품 허브) |
|---|---|---|---|
| 즉답/TL;DR | 상단 TL;DR + 섹션별 40~60단어 즉답 | 히어로에 1문장 가치명제 | 오퍼 즉답(무엇을·누구에게·가격) |
| H1 | 질문/주제형 1개 | 브랜드+핵심 카테고리 | 프로그램/제품명 + 핵심 혜택 |
| 질문형 H2 | 필수(다수) | 최소(주요 서비스 Q) | FAQ 섹션에 집중 |
| 통계·출처 | 필수(≥3 통계, 핵심마다 출처) | 권위 근거(수상·인증·연혁) | 요금·자격·결과 데이터(표) |
| 저자 블록 | 필수 | 불요(Organization로 대체) | 불요(제공자=Organization) |
| 스키마 | Article(+저자·수정일)+Breadcrumb | Organization+WebSite(루트 1회) | Service/Product/Course/JobPosting/LocalBusiness/Event + Breadcrumb |
| FAQ 블록 | 하단 필수 | 선택 | **필수**(비교·자격·요금 Q&A) |
| 내부링크 | 관련글 3~5 + 허브 | 허브/카테고리 rail | 서브페이지 전부 body에 + 상위 허브 |
| 1차 경험 | 사례·후기·실측 필수 | 실적·포트폴리오 | 실제 배치/개통/수업 결과 |
| YMYL(해당 시) | 전 사실 출처+As of | 회사 실체·NAP | 요금/비자/약관 공식 소스 링크 |

- **article** = §[0]~[9] 전면 적용.
- **homepage** = 저자 블록·Article 대신 **Organization/WebSite 엔티티**(루트 1회, 재주입 금지) + 권위 근거 + `sameAs`.
- **landing** = **Service/Product/Course/JobPosting** 스키마 + FAQ 블록 강화 + 서브페이지 body 링크(authority) + 오퍼 즉답.

---

## §12 발행 전 게이트 (마스터 번호 체크리스트 · 규칙 SSOT) — Quality Harness `[AUTO]` + slop-judge `[JUDGE]` 연계

> 이 체크리스트가 **규칙 SSOT**다(§0·§[0]은 여기서 파생된 read-path 요약). 각 항목에 **판정 방식**과 **적용 page-type**을 태그한다.
> - **판정**: `[AUTO]` = 기계 검사(H1 수·단어수·canonical·hreflang ISO 코드·스키마 존재 등 → Quality Harness가 실행) · `[JUDGE]` = 주관 판정(경험·원본성·YMYL 정확성 → item23 slop-judge로 위임). "측정가능" 단언은 `[AUTO]` 항목에만 유효.
> - **page-type**: `A`=article · `H`=homepage · `L`=landing. 태그에 없는 page-type에서는 해당 항목을 **skip**('페이지 타입별 변형' 표 준수 — 정당한 homepage를 오반려하거나 억지 저자블록·통계 스터핑·가짜 사례를 넣게 만들지 말 것).
> - **반려/승인 권한**: 이 문서(작성자 자기점검 근거)가 아니라 **독립 체인**(Quality Harness `[AUTO]` + slop-judge `[JUDGE]`, author≠judge)에 있다.

작성 완료 후 발행 전, page-type에 해당하는 항목을 순서대로 점검.

1. **[H1]** 페이지당 H1 1개, 핵심 엔티티+키워드 front-load. `[AUTO]` {A·H·L}
2. **[질문형 헤딩]** 주요 H2/H3가 실제 사용자 쿼리 형태. `[JUDGE]` {A}(H=주요 서비스 Q 최소·L=FAQ 집중)
3. **[즉답/TL;DR]** 상단 TL;DR 박스 존재(40~80단어) + 각 섹션 헤딩 직후 40~60단어(모바일 36~44) 자기완결 답. `[AUTO]`(TL;DR·단어수) + `[JUDGE]`(자기완결) {A(섹션 즉답)·H·L(TL;DR/히어로 즉답)}
4. **[밀도]** 핵심 단락의 사실을 **표 행 또는 볼드 단독 클레임 ≥3개**로 노출(‘문단당 5~7개’는 프록시일 뿐 — 40~60단어 즉답과 물리 충돌하므로 카운트 강제 금지). `[JUDGE]` {A}
5. **[통계]** 주장 문장에 검증가능 숫자(연도·%·금액·건수) 최소 3곳. `[JUDGE]` {A}(H=권위근거 수치·L=요금/자격/결과 데이터로 치환)
6. **[출처]** 각 핵심 사실에 1차 출처. YMYL은 전 사실 출처 + "As of YYYY-MM". `[JUDGE]` {A·L}(YMYL 시 H 포함)
7. **[인용문]** 전문가/공식 직접 인용문 ≥1. `[JUDGE]` {A}
8. **[표/리스트]** 비교·자격·요금은 표 또는 번호 리스트로 구조화. `[JUDGE]` {A·L}
9. **[FAQ]** 하단 Q&A 블록(Q=실제 질문, A=40~60단어 즉답). **리치결과 기대 X**(정정1). `[JUDGE]` {A(하단 필수)·L(필수)}(H=선택)
10. **[엔티티 명시]** 대명사·모호어 대신 고유명 반복. `[JUDGE]` {A·H·L}
11. **[E-E-A-T]** 저자 블록(이름·직함·자격·링크) + 발행일 + **최종 수정일** 가시. `[AUTO]`(발행/수정일·저자블록 존재) + `[JUDGE]`(자격 신뢰) {A}(H/L=저자블록 불요 → Organization/WebSite 엔티티로 대체)
12. **[경험]** 1차 경험/사례/실측 ≥1(AI가 못 만드는 신호). `[JUDGE]` {A}(H=실적/포트폴리오·L=실제 배치/개통/수업 결과)
13. **[YMYL 정확성]** 비자/통신/금융/취업 수치·절차가 공식 소스와 일치, 과장·미검증 주장 0. **생성형 AI 응답 조작·대량 AI 양산 = 스팸(§4)**. `[JUDGE]` {A·H·L, YMYL 해당 시}
14. **[내부링크]** 관련 3~5개 + 허브, 서술형 앵커. `[AUTO]`(개수·앵커 텍스트) {A·H·L}
15. **[메타]** Title(EN 50~60자/≤600px·KR ~25~33자, front-load) / Description(EN 155~160자·KR ~70~80자, 로드베어링 앞부분) / 이미지 alt / **self-canonical 절대 URL 존재·자기참조(전 페이지 — 단일언어 포함)**. `[AUTO]` {A·H·L}
16. **[스키마]** Article(+저자·수정일)·BreadcrumbList·타입별. **FAQPage/HowTo는 리치결과 기대 없이만.** Book Actions는 폐지 아님(§1.6). `[AUTO]`(존재·@type) {A·H·L}
17. **[이중언어]** KR/EN 짝이면 별도 URL + 왕복 hreflang(x-default) + 올바른 ISO 코드(GB≠UK). `[AUTO]` {A·H·L, 짝일 때}
18. **[Naver]**(한국어 자산) 소제목 다수·경험형·직접촬영 이미지. 정량치는 추정으로만. **KR 자산은 UI/메뉴/푸터/이미지 내 텍스트까지 영어 혼입 0**(Naver는 hreflang 미준수 → 언어 혼합 시 최적화 붕괴, §6.2). `[JUDGE]` {A·H·L, KR 자산}
19. **[분량/원본성]** 총 1,500자+ 깊이, non-commodity(1차 관점), 상품화된 일반론 회피. `[JUDGE]` {A}
20. **[크롤러]** robots.txt에서 **인용 검색봇** `OAI-SearchBot`/`PerplexityBot`/`Claude-SearchBot`/`Googlebot`/`Bingbot`/`Yeti`/`Daumoa` 미차단 + `nosnippet` 미설정 확인. ⚠ `ChatGPT-User`·`Claude-User`(user-fetch, robots 무시)와 `Google-Extended`·`GPTBot`·`ClaudeBot`(학습·그라운딩봇 — 차단해도 검색/AIO 노출 무영향)은 **필수 아님·사업 선택**. `Bytespider`는 robots 무시 → 차단은 WAF에서(§3.6). `[AUTO]` {A·H·L}
21. **[최신성]** "2026년 기준" 시점 명시, 요금/법령/통계 최신 반영, 수정일 갱신. `[AUTO]`(시점 문구·dateModified) + `[JUDGE]`(실제 최신성) {A·H·L}
22. **[제출 준비]** 발행 후 제출(GSC 색인요청·IndexNow[Bing/Naver 계열]·Naver 수집요청·**Daum 별도 제출** webmaster.daum.net·register.search.daum.net)은 **발행 이후 런북** 소관이라 pre-publish 게이트에서 pass/fail 판정 불가 → 게이트에서는 **제출 자동화/배포 훅이 구성돼 있음**만 확인. 실제 절차: [`MULTI_ENGINE_MASTER_2026.md`](./MULTI_ENGINE_MASTER_2026.md) §5(=§9 요약). `[AUTO]`(훅/자동화 구성 확인) {A·H·L}
23. **[slop-judge]** 발행 전 `slop-judge`로 **Writing Quality 등급(목표 A+) + AI-SLOP 신호 0 + YMYL 하드페일 0** 확인. 실패 시 fix-list를 writer 패스(kimchi-blog / korvia-content-pm / seo-harness-team)로 반려 후 재게이트. `[JUDGE]`(독립 심사) {A·H·L}

> **slop-judge 연계 원칙**: item1~22는 writer가 **자체 점검**하고, **item23은 독립 심사**(작성자≠심사자). 실제 반려/승인은 **독립 체인**(Quality Harness `[AUTO]` 실행 + slop-judge `[JUDGE]`)이 수행하며 이 문서는 판정 근거일 뿐이다. slop-judge는 read-only 심판 — 재작성하지 않고 P0/P1/P2 fix-list만 반환. YMYL 하드페일(출처 없는 수치·ghost citation·과장·마케팅 위장·AI 응답 조작)이 하나라도 있으면 발행 차단.

---

## 4개 프로젝트 적용 노트

| 사이트 | 스택 | 주 엔진 | 핵심 자동규칙 |
|---|---|---|---|
| **Korvia** (korvia.com) | Next.js | ChatGPT/Perplexity/Google(영문, **YMYL 비자·취업**) | Article+Person 저자 + JobPosting(EPIK/원어민, 필수 5속성·만료 제거) + 비자/EPIK **1차 법령·HiKorea 출처+As of**. 한국어는 네이버 공식 블로그 위성. IndexNow(Bing). |
| **Kimchi** (kimchimobile.com) | WordPress | ChatGPT/Perplexity/Google(영문, **YMYL 통신·금융**) | Product+Offer(price·currency·availability·priceValidUntil, **요금 최신**) + Review + "As of" 날짜 + eSIM 요금/단계 비교표(AIO 친화). IndexNow 플러그인. 실사례·후기(Perplexity 최신성). |
| **Passome** (passome.com) | Next.js/Vercel | ChatGPT/Perplexity(AI/AX B2B) | Organization+엔티티 홈+Wikidata + **통계·1차 데이터 리서치 콘텐츠**(Princeton 레버) + 주제 순도(C-Rank). |
| **ACT Art Center** (actartcenter.com) | Next.js | Google/Naver(로컬·교육) | LocalBusiness + Course(list)/Event + **Naver 스마트플레이스 최적화**(업종·사진·수업정보·영수증 리뷰·방문 행동데이터) + 서치어드바이저 등록 + 한/영. |

**JobPosting 필수(Korvia)**: `datePosted`(ISO 8601) · `description`(HTML 전체) · `hiringOrganization` · `jobLocation`(**`addressCountry` 포함**) · `title`. 권장: `baseSalary`(currency+unit) · `employmentType` · `validThrough`. **만료 채용은 반드시 제거**(validThrough 과거화 / 404·410 / 마크업 제거). **리스트 페이지에 부착 금지**(개별 상세만). (출처: developers.google.com/search/docs/appearance/structured-data/job-posting)

> → **스택별 구현(Next.js `app/robots.ts`·`sitemap.ts`·메타·소유확인 vs WordPress 플러그인)**: [`PROJECT_STACK_PLAYBOOK.md`](./PROJECT_STACK_PLAYBOOK.md). **JSON-LD 복붙 코드**: [`SCHEMA_JSONLD_CATALOG.md`](./SCHEMA_JSONLD_CATALOG.md).

---

## 기존 문서 대비 정정 요약 (읽고 반영할 것)

- **정정 1 — FAQPage/HowTo 리치결과 폐지(2026)**: FAQ 리치결과는 **2026-05-07 검색 미표시, 문서 제거 2026-06-15**(SC 필터 2026-06, API 2026-08 순차 종료), HowTo도 소멸. 스키마 자체는 유효·Google 파싱은 계속하나 **리치결과 이득 0**. `AEO_GEO_PLAYBOOK_2026.md` §4(1)의 "FAQPage 우선·필수", `WEB_PLAYBOOK.md` §2.2·`NEW_PAGE_CHECKLIST.md` B의 FAQPage/HowTo 항목은 **"파싱·온페이지 구조 목적만"으로 하향**(SEO 리치결과 자산으로 홍보 금지). (출처: developers.google.com/search/docs/appearance/structured-data/faqpage)
- **정정 2 — `llms.txt`는 Google 미사용(공식)**: `AEO_GEO_PLAYBOOK_2026.md` §0·§4(2)의 "필수·같이 쓴다", `WEB_PLAYBOOK.md` §1.3의 llms.txt 항목은 **"선택·비-Google 목적·저ROI 실험"으로 정정**. OpenAI/Anthropic/Perplexity도 자동 판독 커밋 없음 → "인용 지렛대"로 단정 금지. (출처: developers.google.com/search/docs/fundamentals/ai-optimization-guide)
- **정정 3 — GEO 개별 퍼센트**: `AEO_GEO_PLAYBOOK_2026.md` §2의 "인용문 +41%/통계 +30%/출처 +30%"는 업계 요약본. 확정치는 "최대 40%(전체)"뿐, 단일 최고 = Quotation ~43%, Cite Sources ~28%, "+115%"는 하위랭크 하위표. → "상위 레버(방향)"로만 서술.
- **정정 4 — AI Overviews 노출률**: "검색의 50%+"는 전체 쿼리 기준 과장. Semrush 기준 2025 중반 정점 ~25% 후 late-2025 ~16%. **"데이터셋별 15~25%(특정 쿼리군은 더 높음)"**로, 절대수치엔 출처·기간 병기.

**상호링크(중복 금지)**: 전략·수치 배경 → [`AEO_GEO_PLAYBOOK_2026.md`](./AEO_GEO_PLAYBOOK_2026.md); 멀티엔진 발행 런북 → [`MULTI_ENGINE_MASTER_2026.md`](./MULTI_ENGINE_MASTER_2026.md); Google 리치결과·AIO → [`GOOGLE_SERP_STRUCTURED_AI_2026.md`](./GOOGLE_SERP_STRUCTURED_AI_2026.md); JSON-LD 템플릿 → [`SCHEMA_JSONLD_CATALOG.md`](./SCHEMA_JSONLD_CATALOG.md); 네이버 콘솔 → [`NAVER_SEARCHADVISOR_PLAYBOOK.md`](./NAVER_SEARCHADVISOR_PLAYBOOK.md); Daum → [`DAUM_KAKAO_SEARCH.md`](./DAUM_KAKAO_SEARCH.md); GSC/Bing/IndexNow → [`GOOGLE_SEARCH_CONSOLE_INDEXING.md`](./GOOGLE_SEARCH_CONSOLE_INDEXING.md)·[`BING_YAHOO_INDEXNOW.md`](./BING_YAHOO_INDEXNOW.md)·[`ROBOTS_SITEMAP_RSS_TEMPLATES.md`](./ROBOTS_SITEMAP_RSS_TEMPLATES.md); 스택별 구현 → [`PROJECT_STACK_PLAYBOOK.md`](./PROJECT_STACK_PLAYBOOK.md); 기술 셋업(metadata/CLS/a11y) → [`NEW_PAGE_CHECKLIST.md`](./NEW_PAGE_CHECKLIST.md)·[`WEB_PLAYBOOK.md`](./WEB_PLAYBOOK.md); 대규모 웹 작업 → [`../../KORVIA_WEB_PLAYBOOK.md`](../../KORVIA_WEB_PLAYBOOK.md); 발행 게이트 심사 → `slop-judge` 스킬 + `seo-harness-team`.

---

## Sources

공식 1차 (Google/OpenAI/Anthropic/schema/IndexNow):
- https://developers.google.com/search/docs/fundamentals/ai-optimization-guide (Optimizing for generative AI features, 2026-05-15)
- https://developers.google.com/search/docs/appearance/ai-features
- https://developers.google.com/search/docs/fundamentals/creating-helpful-content (E-E-A-T / helpful content)
- https://developers.google.com/search/docs/appearance/structured-data/search-gallery (지원 리치결과 타입)
- https://developers.google.com/search/docs/appearance/structured-data/faqpage (FAQ 폐지 changelog)
- https://developers.google.com/search/blog/2023/08/howto-faq-changes
- https://developers.google.com/search/blog/2024/10/sitelinks-search-box
- https://developers.google.com/search/blog/2025/06/simplifying-search-results
- https://developers.google.com/search/docs/appearance/structured-data/job-posting
- https://developers.google.com/search/docs/appearance/structured-data/article
- https://developers.google.com/search/docs/appearance/structured-data/organization
- https://developers.google.com/search/docs/specialty/international/localized-versions (hreflang)
- https://developers.google.com/search/updates (구조화데이터 폐지 changelog)
- https://arxiv.org/abs/2311.09735 · https://arxiv.org/html/2311.09735v3 (Princeton GEO, KDD 2024)
- https://developers.openai.com/api/docs/bots (GPTBot/OAI-SearchBot/ChatGPT-User)
- https://platform.claude.com/docs/en/agents-and-tools/tool-use/web-search-tool
- https://support.claude.com/en/articles/8896518 (ClaudeBot/Claude-User/Claude-SearchBot)
- https://ai.google.dev/gemini-api/docs/google-search (Google-Extended 그라운딩)
- https://indexnow.org · https://en.wikipedia.org/wiki/IndexNow
- https://searchadvisor.naver.com/ (네이버 서치어드바이저)

2차(업계·검증 — ⚠ 방향/추정으로만):
- https://www.searchenginejournal.com/googles-new-ai-search-guide-calls-aeo-and-geo-still-seo/575026/
- https://www.searchenginejournal.com/google-drops-faq-rich-results-from-search/574429/
- https://searchengineland.com/google-drops-reporting-on-several-structured-data-types-461744
- https://www.etavrian.com/news/google-deprecates-practice-problem-structured-data (Book Actions 철회)
- https://searchengineland.com/google-ai-overviews-surge-pullback-data-466314 (AIO 15~25%·zero-click)
- https://www.semrush.com/blog/most-cited-domains-ai/ · https://www.leapd.ai/blog/ai-visibility/how-chatgpt-google-ai-overviews-and-perplexity-source-information-in-2026
- https://www.dataenriche.com/best-content-length-for-featured-snippets/ (⚠ 42/38단어 추정)
- https://www.scalenut.com/blogs/meta-title-length-best-practices-2026
- https://technologychecker.io/blog/chatgpt-statistics (⚠ 봇 트래픽 점유 상충)
- https://www.digitalapplied.com/blog/entity-seo-knowledge-graph-optimization-guide-2026
- https://ipullrank.com/eeat-ymyl-ai-search (YMYL 2025-09 확장)
- https://www.ascentkorea.com/naver_seo_strategies_2/ · https://www.twinword.co.kr/blog/naver-seo-d-i-a/ (⚠ C-Rank/D.I.A 추정)
- https://www.newspim.com/news/view/20260625000973 (Naver AI 탭 정식 출시)
- https://zdnet.co.kr/view/?no=20260225180559 (Cue:/ClovaX 2026-04-09 종료)
- https://www.tech42.co.kr/ (네이버 62.86% 점유율 맥락)
- https://www.theegg.com/seo/korea/naver-seo-guide-understanding-the-korean-search-engine/ (Yeti·hreflang 미준수)
- https://www.weglot.com/guides/hreflang-tag (⚠ ~75% 오류율)
