---
last_verified: 2026-07
reverify_by: 2026-Q4
---

# GEO/AEO — AI 답변엔진 최적화 (2026 심화)

> Generated 2026-07-01 · Owner: Henry Oh (KORVIA) · ⚠ 콘솔/API·엔진 검색 백엔드·크롤러 정책은 조용히 바뀐다. 절차/엔드포인트/한도/지원여부는 **사용 전 공식 1차 소스로 재확인**.

이 문서는 `korvia-seo-skill/docs/`의 **AI 답변엔진(GEO/AEO) 심화 참조편**이다. 아래 기존 문서와 **중복하지 않고** 상호링크하며, 새 정보·심화·2026 갱신만 담는다.

- [`./AEO_GEO_PLAYBOOK_2026.md`](./AEO_GEO_PLAYBOOK_2026.md) — 상위 전략(왜/무엇/근거 수치). **이 문서가 그 플레이북의 일부 STALE 항목을 갱신한다(§Δ 참조).**
- [`./WEB_PLAYBOOK.md`](./WEB_PLAYBOOK.md) — Next.js 기술(robots/sitemap/schema/CLS/헤더) 구현.
- [`./NEW_PAGE_CHECKLIST.md`](./NEW_PAGE_CHECKLIST.md) — 새 페이지 17항목 발행 체크리스트.
- 운영 에이전트: `.claude/agents/geo-optimizer.md`, `.claude/agents/technical-seo.md`. (`seo-harness-team` 스킬)

**대상 4사:** Passome(Next.js/Vercel) · Korvia(Next.js·YMYL 비자/취업) · Kimchi Mobile(WordPress·YMYL 통신/eSIM) · ACT Art Center(Next.js·미술교육). 전부 한/영 이중언어. 청중: 한국인(Naver 지배) + 한국 거주/방문 외국인(Google/ChatGPT 영어 지배).

> **기반 원칙 (Google 공식):** Google AI 최적화 가이드(`developers.google.com/search/docs/fundamentals/ai-optimization-guide`, 페이지 표기 **Last updated 2026-06-29**)는 **AI 검색(생성형 기능 포함) 노출에 새 기계판독 파일·"AI 텍스트 파일"(llms.txt류)·특수 마크업·마크다운이 불필요**하고, **콘텐츠를 잘게 청킹할 필요도, AI 전용 문체로 쓸 필요도 없다**고 명시한다("The best practices for SEO continue to be relevant because our generative AI features on Google Search are rooted in our core Search ranking and quality systems"). 즉 **표준 SEO가 토대**다. 아래 심화 항목은 그 토대 위의 GEO/AEO 증분일 뿐, 별도 트랙이 아니다. ([V2] 확인 2026-07 — 단 공식 어휘는 'llms.txt'가 아닌 'AI text files'로 포괄 표현.)

---

## 0. 자동 적용 규칙 (Auto-apply)

작성자·에이전트가 **기계적으로** 따를 항목. 발행 게이트(§9)에서 검증.

### 콘텐츠·인용
1. **Answer-first**: 모든 페이지·H2 섹션은 첫 40~60단어 안에 질문에 직접 답하는 **자기완결 문장**(주어 포함)을 둔다. 핵심 결론을 페이지 상단 20~30%에 배치. (AI 인용의 44~55%가 상단에서 나옴 — 상관치, §A)
2. **검증가능성 신호**를 각 핵심 주장에 최소 하나: (a)명명된 출처, (b)통계/숫자, (c)인용문. 모든 수치에 `출처 + "as of YYYY-MM-DD"` 병기. (Princeton 레버, §B)
3. **시맨틱 HTML 강제**: 단일 `<h1>`, 논리적 `<h2>/<h3>`, 비교=`<table>`, 절차=번호 리스트, 정의=정의 리스트. 스캔가능 포맷이 AIO·Naver AI Briefing 인용의 선호형.
4. **최신성**: 화면 노출 타임스탬프 + JSON-LD `datePublished`/`dateModified`. 콘텐츠 변경 시 `dateModified` **실제** 갱신. (인용 유지율이 낮은 엔진 다수 — 정기 리프레시)
5. **FAQ**: 리치결과 목적이 아니라 **AI 파싱 목적**으로 유지 — 질문형 H2 + 직접답변 본문 + `FAQPage` 스키마(넣되 본문과 일치). (⚠ FAQ 리치결과는 2026-05-07 종료, §D·§Δ)
6. **과장·미근거 수치 금지**: AI가 우리 문장을 왜곡·미근거 인용할 수 있으므로(응답의 50~90%가 소스 미완전지지) 각 사실을 오해불가한 단문으로.

### 엔티티·브랜드
7. **Organization JSON-LD + `sameAs`**로 Wikidata·LinkedIn·공식 SNS 연결. **NAP(상호·주소·연락처)와 영/국문 표기를 4사 전체에서 철자까지 일관**.
8. YMYL·저자 있는 글엔 **Person + `sameAs`**(실명·직함·자격). YMYL은 E-E-A-T 스크루티니 증폭.

### 기술·크롤러
9. **robots.txt에 검색·인용 봇을 명시 Allow**(OAI-SearchBot, ChatGPT-User, PerplexityBot, Claude-SearchBot, Claude-User, DuckAssistBot, Applebot, Googlebot, Bingbot, Yeti, **Daumoa**[Daum]). **절대 Disallow하거나 `User-agent:* Disallow:/` 범위에 포함하지 말 것** — 차단=답변에서 소멸. (robots 템플릿 단일 SSOT는 [`ROBOTS_SITEMAP_RSS_TEMPLATES.md`](./ROBOTS_SITEMAP_RSS_TEMPLATES.md) §A; §D.6은 그 사본.)
10. **학습봇**(GPTBot, Google-Extended, CCBot, Applebot-Extended, Meta-ExternalAgent, Bytespider, cohere-ai) 차단 여부는 검색봇과 **분리 결정**. 차단해도 검색 노출/랭킹 무영향. KORVIA 기본=허용(브랜드 각인). 변경 시 Henry 승인.
11. **Cloudflare `Block AI bots` 토글 OFF**(검색봇까지 죽임). CF 신규 등록 도메인(특히 Kimchi)은 `AI Crawl Control > Manage AI crawlers`에서 검색·인용 봇 Allow 여부 **실확인·복구**. (⚠ CF 2025-07-01부터 신규 도메인 기본 차단, §D)
12. **본문은 SSR/SSG**로 서버 HTML에 텍스트 존재. JS-only 본문·로그인/쿠키월 뒤 YMYL 가이드 금지.
13. **IndexNow ping 자동화**(발행/수정 시 Bing+Naver 즉시 색인). Google용은 XML 사이트맵 + GSC 유지(Google은 IndexNow 미지원). (§E)
14. **hreflang 상호(return) 링크 완비** + `x-default`. 코드는 `ko`/`en`/`ko-KR`(지역 단독 `KR` 금지, 영국=`GB`). (구현: WEB_PLAYBOOK §3.1)

### 한국·YMYL
15. **한국인 타깃 콘텐츠는 Naver 블로그 병행 발행**(동일 주제 지속 축적=C-rank). Naver AI Briefing 소스의 ~70%가 자사 UGC라 외부 홈페이지만으론 인용 진입 어려움. (§F)
16. **YMYL(Korvia 비자/취업, Kimchi 통신/결제)은 정부·공식 1차 출처를 본문에 직접 링크**하고 정확히 요약(HiKorea, 법무부 출입국, EPIK, 과기정통부 등). AI는 official 도메인을 우선 인용 → 우리 페이지는 "신뢰 다리"로 인용가치를 얻음.
17. **폐기 사실 금지**: (a) llms.txt로 색인/랭킹 이득 없음(Google 공식 무시) — 트래픽·인용을 여기 귀속 금지. (b) Naver Cue:/Clova X는 **2026-04-09 종료**(2025-04 아님). 한국 AI 인용 대상=Naver AI Briefing + wrtn(뤼튼).

---

## Δ. 기존 AEO_GEO_PLAYBOOK_2026.md에서 이 문서가 갱신하는 항목

> 원본은 **수정하지 않는다**(2026-05-29 lock). 아래는 그 후 확인된 변경. 충돌 시 **이 문서(2026-07-01)가 우선**.

| 플레이북 서술 | 2026-07 갱신 | 근거 |
|---|---|---|
| "우선순위: FAQPage, **HowTo**" 스키마 | **HowTo 리치결과 폐지(2023-09), FAQ 리치결과 폐지(2026-05-07)**. 스키마는 리치결과가 아니라 **AI 파싱용**으로만. 신규 페이지 신설 금지. | Google Search Central changelog |
| 통계 "+30%" / "+41% 인용문" 류 % 단정 | 원논문(Table 1) 재검증: **단일 최고=Quotation(~42~44%), Statistics ~34%, 상위 3레버 30~40% 대역** — 특정 %의 단일 레버 귀속 금지·3레버 병용. 절대 %=2024 컷오프 역사치(§B.1 인용 규칙). | KDD 2024 원문 대조(적대검증 2026-07-02) |
| "llms.txt = 신규 표준, schema와 같이 쓴다" | **Google은 공식적으로 검색이 llms.txt를 무시**(Illyes 2025-07). OpenAI 미지원. 채택률 ~10%(다수 stub). = **저비용 헤지**로만. | §D |
| ChatGPT "48% Wikipedia" / Perplexity "47% Reddit" | 방향성 유효하나 **벤더 집계·시점 변동 큼**(ChatGPT Reddit은 2025-09 ~10%까지 하락 관측). 절대치 근사로만. | §A |
| Google AI Overviews = "기존 상위 랭킹" | 여전히 맞음 + **AI Mode(별도 대화형)** 신설, query fan-out. **특수 스키마·최적화 불필요**(색인+스니펫 자격이 유일 요건, Google 공식). | §A |

---

## A. 엔진별 인용 메커니즘 (심화)

전통 SEO는 "10개 링크 중 몇 위"였다. GEO는 두 층이다:
1. **Retrieval(어느 인덱스에서 후보를 긁나)** — 엔진마다 다름. 여기서 색인이 안 되면 인용 불가.
2. **Rerank/Selection(후보 중 무엇을 인용하나)** — 구조·권위·신선도·엔티티로 재정렬.

### A.1 엔진 요약표

| 엔진 | 검색 백엔드 | 최우선 소스 성향 | 인용 특징 | 우리 액션 게이트 |
|---|---|---|---|---|
| **ChatGPT Search** | Bing 인덱스 + `OAI-SearchBot`(⚠ OpenAI 공식 단정 아님) | Wikipedia·Reddit·대형 퍼블리셔 | 후보 중 ~15%만 인용, 상단 편향 | **Bing 색인 + IndexNow** |
| **Google AIO/AI Mode** | Google 인덱스 + query fan-out | Google 랭크 상위·YouTube·Reddit | AI Mode 응답 다수 인용(벤더치), AIO와 도메인 상당 오버랩 | **색인 + 스니펫 자격**, 답변블록 |
| **Perplexity** | 자체 + Bing/Google, RAG, trust-seed | Reddit(최고 커뮤니티 편향)·BBC·Wikipedia | 인라인 각주 다수, 신선도↑ | 통계/출처/인용 레버 |
| **Gemini(앱/Google)** | Google grounding | Google풀·Forbes | 회전 빠름(인용 유지율 낮음) | AIO와 동일 |
| **Claude 웹검색** | Brave(⚠ 업계 보고, 공식 미명시) | **정부·학술·확립뉴스**(가장 기관 편향) | 스팸·SEO 리스티클 배제, 신선도 민감 | 1차 권위소스·저자표기·최신 날짜 |
| **Grok (xAI)** ⚠ | 자체 인덱스 + X(구 트위터) 실시간 스트림 | X 게시물·실시간 트렌드·커뮤니티 | 신선도·실시간/커뮤니티 편향(⚠ 방법론 검증 부족) | 표준 GEO 레버 + **크롤러 통제는 WAF 전용**(문서화 UA 빈약, §D.5 각주·추적 STALE_REGISTER S5) |

### A.2 엔진별 소스 선택 메커니즘

- **ChatGPT / ChatGPT Search**: 라이브 층은 **Bing 인덱스**를 통해 검색(⚠ 다수 보고, OpenAI 공식 단정은 아님 — **분기별 재확인**). → **Bing Webmaster Tools 등록 + IndexNow 제출이 사실상 ChatGPT 진입 전제.** 자체 크롤러 `OAI-SearchBot` 병행. 후보 페이지 중 약 15%만 실제 인용, 페이지 상단 편향, Q&A 구조·정의문 선호.
- **Google AIO + AI Mode**: **Google 자체 인덱스 + query fan-out**(복합 질의를 하위 질의로 쪼개 병렬 검색 후 합성). AIO=일반 SERP 인라인, **AI Mode=Gemini 기반 별도 대화형 검색**(랭크 링크 대신 다단계 합성 + 접이식 Sources 패널). **공식 입장**: AIO/AI Mode 노출에 **추가 요건·특수 최적화·특수 스키마 불필요, 색인+스니펫 노출 자격만** 필요. Google 랭크가 강한 상류 필터.
  - ✅ 공식 확인 수치: AI Mode **월 10억+ 사용자**, 기본모델 **Gemini 3.5 Flash**(Google I/O 2026).
  - ⚠ AIO 노출률 **~47%(2026 Q1, 전년 32%→)는 '적격 쿼리' 기준·벤더/추정치**(Google 공식 단정 아님·재확인 필요). 이는 **전체 쿼리 기준 15~25% 추정**(GOOGLE_SERP_STRUCTURED_AI_2026 §3.1)과 **모수가 다르다**(적격 쿼리 ⊂ 전체 쿼리) — 두 수치를 섞거나 성과 근거로 단정하지 말 것.
  - ⚠ "AI Mode가 전체 쿼리의 40% 처리"는 **벤더 추정치** — 성과 근거로 쓰지 말 것.
- **Perplexity**: 자체 인덱스 + 외부 검색 API(Bing/Google 혼합 보고) + RAG. "trust seeds"(사람이 검증한 신뢰 도메인)로 편향. Reddit 편향 최고, 편집 책임성 있는 소스(BBC 등) 우대. 답변당 다수 인라인 각주. **Princeton GEO 레버가 특히 잘 먹히는 엔진**(원논문 재현처).
- **Gemini**: Google grounding 파이프라인 공유(`groundingMetadata`로 소스 첨부). AIO/AI Mode와 소스풀 상당 겹침. 인용 회전 빠름(유지율 낮음) → **정기 갱신이 인용 유지 레버**.
- **Claude 웹검색(Anthropic)**: 검색 제공사는 **Anthropic 공식 문서에 미명시**(⚠ 업계에선 Brave로 널리 보고 — 추정). 웹검색 도구는 응답에 **인라인 citations** 포함 + `allowed_domains`/`blocked_domains` 지원. **소스 기준이 가장 엄격** — 정부·학술·확립 뉴스·업계 표준기관 우선, 상업 블로그·키워드 스터핑·SEO 리스티클 배제. 신선도 민감. → **YMYL 페이지는 1차 출처·저자·최신 날짜를 특히 강화**하면 Claude 인용에 유리.

### A.3 위치·집중 편향 (⚠ 전부 3rd-party 상관치, as of 2026)

- **도메인 극단 집중**: 상위 15개 도메인이 전체 AI 인용의 ~68%(5W Index 2026, 680M 인용 종합). **Reddit이 모든 엔진 통틀어 #1(~40%)**, Wikipedia는 ChatGPT top-10의 26~48%, YouTube는 영상 소스 200x 우위, LinkedIn은 B2B에서 급부상.
- **위치 편향**: 페이지 상단 30%가 인용의 44~55%. → **결론을 맨 위로**.
- **인용 ≠ 사실**: SourceCheckup(Stanford, Nature Comms 2025) — 응답의 50~90%가 인용 소스로 완전히 뒷받침 안 됨. → **핵심 사실을 오해불가한 단문**으로(왜곡 방어).
- **플랫폼 고유성**: 인용 도메인의 78~85%가 단일 엔진에만 등장 → **멀티엔진 커버 필요**. (원 플레이북 "동시 인용 11%"와 같은 방향)
- **커뮤니티 편향 활용**: owned 페이지만으론 한계 → Reddit(r/korea 등)·Quora·전문 커뮤니티·LinkedIn(B2B)에서 **진성 언급/토론**을 확보해 인용 풀에 진입.

> ⚠ 위 %(Reddit 40%, top-15 68%, AI Mode 97%/~7도메인, 뉴스편향 OpenAI=wire/Perplexity=BBC/Gemini=Forbes)는 방법론·시점 혼재·엔진별 변동이 크다. **근사·방향성**으로만, 성과 약속으로 쓰지 말 것.

---

## B. Princeton GEO 레버 + 최신 재현·반박

### B.1 원논문 (Aggarwal et al., **ACM KDD 2024** — 첫 피어리뷰 GEO 연구)
9개 최적화 기법 × 10,000 쿼리, Bing Chat 모사 시스템 → Perplexity 검증.

| 레버 | 논문 보고치(상대·역사치) |
|---|---|
| **명명된 출처 직접 인용(quotation) 추가** | **~42~44%** — Table 1(Position-Adjusted Word Count) 기준 **단일 최고 레버** |
| **출처(cite sources) 추가** | 상위권(30~40% 대역) · Table 2 기준 **SERP 5위 사이트에서 +115.1%** 상대 가시성 |
| **통계(statistics) 추가** | **~34%** |

> ⚠ **인용 규칙(적대검증 2026-07-02 확정)**: 위 %는 **논문 보고 역사치**(2023 실험·KDD 2024 발표)로 "현재 성과"나 "보장 레버"로 단정 금지. 상위 3레버(인용문·출처·통계)는 **대역 내 근접 — 순위 단정보다 3레버 병용**이 견고한 결론. 특정 %를 단일 레버에 확정 귀속하는 서술(예: "통계 +41% 최대") 금지 — 과거 이 문서·BING 문서가 통계↔인용문을 뒤바꿔 실었던 오류가 검증에서 교정됨.

유창성·전문용어 개선도 유효. **키워드 스터핑은 역효과.**

### B.2 2026 재현·확장
- **GEO-16 (arXiv 2509.10762, 2025-09) — ⚠ 프리프린트(미피어리뷰)**: 16개 GEO 필러로 재측정. 방향성으로 **메타데이터&신선도·시맨틱HTML·구조화데이터가 모두 인용과 강한 상관**(보고된 r≈0.63~0.68로 순서는 근사 — 소수점 차이로 레버 우선순위를 바꿀 만큼 견고하지 않음). 보고된 `GEO≥0.70 & 필러≥12 → 교차엔진 인용률 78%, 고-GEO 오즈비 4.2배`·엔진별 인용률(Brave 78% / Google AIO 72% / Perplexity 45%)은 **프리프린트 관측치**이므로 성과 근거로 단정 금지.
- ⚠ **증거 등급 구분**: B.1 원논문(KDD 2024)은 **피어리뷰**, GEO-16(2509.10762)은 **프리프린트**다 — 같은 정밀도로 취급하지 말 것. 정밀 r값·오즈비·순위는 방향성으로만 사용한다.
- ⚠ **캐비앗**: Princeton 절대 수치는 **2024 컷오프**. **상대적 우선순위(통계 > 인용 > 출처)는 견고**하나 절대 %는 이동했을 수 있음(원저자·해설자 다수 동의). **현재 성과치로 단정 금지.**

### B.3 재해석
레버의 공통분모는 **검증가능성 신호**: 숫자·명명 출처·인용문·타임스탬프·구조. 엔진은 "이 문장은 근거가 붙어 있다"를 인용가치로 읽는다.

---

## C. 엔티티/브랜드 신호 (인용의 사전 게이트)

인용은 **지식그래프에서 이미 해소(resolved)된 엔티티**에게 우선 간다.

- **Wikidata 항목**: 조직명·산업·설립일·본사·대표·공식 웹사이트 properties 등록 → AI에 명확한 참조점. (⚠ 벤더치 "Wikidata 검증 브랜드가 Knowledge Panel 3.2x·AIO 인용 2.7x" = **1차 학술 아님**, 방향성만.)
- **Organization 스키마 + `sameAs`**: 홈 Organization JSON-LD에 `sameAs`로 위키데이터·링크드인·공식 SNS 연결 → 엔티티 동일성 신호. 별도 Logo 리치결과는 Organization으로 통합됨(로고 노출은 `logo`가 좌우).
- **일관 NAP**: 모든 채널에서 상호·주소·연락처·영/국문 표기 **철자까지 일관**. KORVIA 4사는 "코비아컨설팅"/"KORVIA"/"Korvia Consulting" 표기를 통일.
- **제3자 언급(unlinked mentions 포함)**: 뉴스·디렉토리·커뮤니티의 브랜드 언급이 엔티티 권위를 쌓음.

### C.1 구조화데이터 — AI 파싱/엔티티용 (Google 리치결과 카탈로그는 별도 문서 범위)
> Google 공식: "AI 기능에 특수 스키마 불필요. 단 **넣은 구조화데이터는 반드시 화면 노출 텍스트와 일치**해야." → 마크업-본문 불일치는 리스크.

**KORVIA 스키마 우선순위(페이지가 실제로 무엇인지에만 맞춰 선택, 남발 금지):**
- 홈 = `Organization`(name/url/logo/sameAs/contactPoint) — **4사 필수**
- 모든 하위 = `BreadcrumbList`
- 글 = `Article`/`BlogPosting`(author/datePublished/dateModified/headline/image)
- 저자 = `Person` + `sameAs` / `ProfilePage`(YMYL E-E-A-T)
- **Korvia**: 개별 채용 페이지에만 `JobPosting`(목록 페이지 금지) + `Course`(EPIK/원어민 트레이닝) + `EmployerAggregateRating`
- **Kimchi**: `Product`+`Offer`(eSIM 요금·가용성, 이미지 ≥500×500px) — 구매 페이지=merchant listing / 리뷰형=product snippet
- **ACT**: `LocalBusiness` 정확 하위유형(`EducationalOrganization`/`ArtGallery`) + `Course`(+`CourseInstance`) + `Event`
- **Passome**: `Organization` + `SoftwareApplication`(AI 도구) + `Event`(웨비나)

**⚠ 신규 페이지에 넣지 말 것(폐지):** `FAQPage` 리치결과 목적, `HowTo`, Sitelinks Searchbox(`SearchAction`/`potentialAction`). schema.org 타입 자체는 유효(방치 무해)하나 시각적 리치결과 없음.

---

## D. llms.txt 사양 vs 현실 (냉정) + AI 크롤러 robots 전략

### D.1 llms.txt 사양 (llmstxt.org, Jeremy Howard 2024-09)
- **위치**: 루트 `/llms.txt`. 개별 페이지는 URL 뒤 `.md`(마크다운 버전) 권장(`/visa-guide.md`).
- **포맷(마크다운, 순서 고정)**: H1(사이트명, **유일 필수**) → blockquote 요약 → (선택)본문 → `## H2` 파일리스트(`[name](url): 설명`) → `## Optional`(스킵 가능 2차).
- **llms-full.txt**: 주요 콘텐츠 전문(全文)을 하나로 연결(concatenated). LLM이 링크 안 따라가고 한 파일로 맥락 확보.

### D.2 현실 — 냉정
- **채택률**: 연구별 2~10%(Ranqo 10.13%, SE Ranking 30만 도메인 스터디), 그중 **~39.6%가 플러그인 stub**(내용 없음). Ahrefs 137K 사이트: **97%가 아무 리더도 없음**; 500M AI봇 방문 중 llms.txt 타깃 408건.
- **소비 여부**: ⚠ **Google은 공식적으로 검색이 llms.txt를 무시한다고 명시**(Gary Illyes 2025-07). OpenAI 공식 미지원. **Anthropic·Perplexity만 공개 지원 확인**. 실사용은 IDE 에이전트(Cursor/Claude Code 등)에 국한.
- **역설**: 발행자(Anthropic·OpenAI 문서 사이트, Stripe 등)는 늘었지만 소비자(색인 크롤러)는 확인 안 됨.

### D.3 KORVIA 권고: 저비용 헤지로만
- 비용 ~0(정적 파일 1개), 미래 채택 가능성 → **발행은 하되 기대는 낮게**. 트래픽·인용 지표를 여기 귀속 금지.
- Next.js 3사(Passome/Korvia/ACT): `app/llms.txt/route.ts` 또는 `public/llms.txt`, 빌드 시 사이트맵과 동기화. WordPress(Kimchi): 정적 업로드 또는 SEO 플러그인, **stub 자동생성물 지양(수작업 큐레이션)**.
- **llms-full.txt는 Korvia/Kimchi YMYL 핵심 가이드(비자 절차, eSIM 개통법)에 한해**. **PII·요금표 등 변동 정보는 넣지 말 것**(캐시 오염).

### D.4 AI 크롤러 3분류 원칙 (가장 중요)
차단 결정을 **반드시 분리**한다:
1. **학습(Training)** — 모델 학습용. 차단해도 검색 노출/인용 무영향(대개). 저작권·데이터주권 관점.
2. **검색·인용(Search/Retrieval)** — AI 답변에 사이트를 **노출·인용**. **차단하면 ChatGPT/Perplexity/Gemini 답변에서 소멸.** KORVIA 절대 금지.
3. **사용자 유발(User-triggered)** — 사용자가 URL/질문을 던져 실시간 fetch. 일부는 robots.txt를 **무시**(사용자 행동 논리).

> ✅ **robots.txt 차단은 인용을 못 막는다(실측 확인)**: 대규모 연구(BuzzStream/Citation Labs 'XOFU', 2026 게재 — 원문 4월 8일, 일부 2차 3월 19일; 10개 산업·3,600 프롬프트·약 400만 인용)에서 상위 50개 뉴스 퍼블리셔 기준 **ChatGPT-User 차단 사이트의 70.6%, OAI-SearchBot 82.4%, GPTBot 88.2%, Google-Extended 92.3%가 여전히 AI 답변에 인용**됐다. 원인=사전 색인(차단 전 크롤분)·Common Crawl 아카이브·SERP 수준 추출(제목/URL/스니펫)로 오리진 접근을 우회. → **크롤러 차단은 인용을 막지 못하고 가시성만 잃으므로, 콘텐츠 보호는 robots.txt가 아니라 라이선스/법적 수단으로 접근**한다. ⚠ 표본이 고권위 뉴스 퍼블리셔라 소규모 사이트로의 정량 일반화는 주의(방향성은 동일). ([confirmed] 2026-07, buzzstream.com/blog/news-block-ai-bots-citations)

### D.5 공식 확인 User-Agent 표 (as of 2026-07, 1차 소스)

| 봇 (UA 토큰) | 운영사 | 유형 | robots.txt 준수 | IP 공개 JSON |
|---|---|---|---|---|
| `GPTBot/1.3` | OpenAI | 학습 | 예 | openai.com/gptbot.json |
| `OAI-SearchBot/1.3` | OpenAI | **검색(인용)** | 예 | openai.com/searchbot.json |
| `ChatGPT-User/1.0` | OpenAI | 사용자 유발 | **아니오**(⚠ 현행 공식=No) | openai.com/chatgpt-user.json |
| `OAI-AdsBot/1.0` | OpenAI | 광고 랜딩 검증 | 제출 페이지만 | openai.com/adsbot.json |
| `ClaudeBot` | Anthropic | 학습 | 예 | claude.com/crawling/bots.json |
| `Claude-SearchBot` | Anthropic | **검색(인용)** | 예 | 동일 |
| `Claude-User` | Anthropic | 사용자 유발 | **예** | 동일 |
| `anthropic-ai`, `Claude-Web` | Anthropic | (deprecated) | — | — |
| `Googlebot` | Google | 검색 색인 | 예 | Google IP 목록 |
| `Google-Extended` | Google | **robots 토큰만**(Gemini 학습/grounding 제어) | 예 | — |
| `Google-CloudVertexBot` | Google | 소유자 요청 Vertex AI 에이전트 | 예 | — |
| `PerplexityBot/1.0` | Perplexity | **검색(인용)** | 예 | perplexity.com/perplexitybot.json |
| `Perplexity-User/1.0` | Perplexity | 사용자 유발 | **아니오**(무시) | perplexity.com/perplexity-user.json |
| `Applebot` | Apple | Siri/Spotlight 색인 | 예 | Apple 공개 |
| `Applebot-Extended` | Apple | 학습 opt-out 토큰 | 예 | — |
| `Bytespider` | ByteDance | 학습 | **자주 무시**(악명) | — |
| `CCBot` | Common Crawl | 데이터셋(다수 LLM 학습 재사용) | 예 | — |
| `Amazonbot` | Amazon | Alexa/AI | 예 | Amazon 공개 |
| `Meta-ExternalAgent` | Meta | 학습 | 예 | — |
| `Meta-ExternalFetcher` | Meta | 사용자 유발 | 부분 | — |
| `DuckAssistBot` | DuckDuckGo | AI 어시스트(검색) | 예 | — |
| `Bingbot` | Microsoft | 검색 색인(**Copilot=Bing 인덱스**) | 예 | Bing 공개 |
| `Yeti` | Naver | **한국 검색 색인**(구조화데이터·메타·링크 중시) | 예 | Naver 공개 |
| `Daumoa` | Daum(Kakao) | **한국 검색 색인**(Daum 자체 크롤러) | 예 | ⚠ IP JSON 미공개 → **UA 토큰 기준**(Daum 소유이전으로 IP 하드코딩 금지) |
| `GrokBot`, `xAI-Grok`, `Grok-DeepSearch` ⚠ | xAI | 검색/사용자(실시간) | **불확실·무시 사례 보고** | ⚠ xAI 공식 봇/robots 문서 UA 미확인 → **robots로 통제 곤란, WAF 전용** |

> ⚠ **봇별로 robots 준수가 다르다**: OpenAI `ChatGPT-User`=**미준수**(현행 공식 페이지 명시), Anthropic `Claude-User`=준수, Perplexity `Perplexity-User`=미준수. 사용자 유발 봇의 robots 준수는 업계 전반 회색지대. 공식 봇 문서(`developers.openai.com/api/docs/bots`, `docs.perplexity.ai/docs/resources/perplexity-crawlers`, Anthropic 8896518)의 **현행 상태를 사용 전 재확인**.
> ⚠ **OpenAI 봇 문서 날짜 [V7]**: `developers.openai.com/api/docs/bots`에는 **명시적 개정일 타임스탬프가 없다** — 현행 UA 버전 표기는 **OAI-SearchBot/1.3 · GPTBot/1.3 · OAI-AdsBot/1.0**(2026-07 확인). OAI-SearchBot은 ChatGPT 검색 노출용이며 학습 데이터 수집은 하지 않는다(학습은 GPTBot). '2025-12경 개정' 같은 날짜는 페이지에서 직접 확인 불가 → '경'으로만 근사.
> ⚠ **`Google-Extended`는 검색 노출 제어가 아님 [V3]**(Google 공식): AI Overviews·AI Mode 노출은 **일반 검색과 동일한 로봇 제어(nosnippet·data-nosnippet·max-snippet·noindex)로만** 관리되고 "추가 요건 없음". `Google-Extended`는 **별도 토큰**으로 Gemini 앱·Vertex AI 등 Google의 **다른 시스템**에서의 학습·그라운딩을 제한할 뿐, AIO/AI Mode의 검색 노출·랭킹은 통제하지 못한다. → AIO 억제 목적으로 `nosnippet`을 걸면 일반 스니펫까지 죽으니 금지.
> ⚠ **Grok(xAI) [추적]**: STEP1 미검증 항목 — xAI 공식 봇 문서로 UA·robots 준수가 확인되지 않았다(추적: STALE_REGISTER S5). 대응 채널은 사실상 **WAF뿐**. `Meta AI`(Meta-ExternalAgent/Fetcher)·`DeepSeek`도 후속 검토 대상.

### D.6 KORVIA robots.txt 템플릿 (검색·인용 허용 / 학습 정책적)

```txt
# --- 검색/색인/인용: 반드시 허용 (KORVIA 핵심 유입) ---
User-agent: Googlebot
Allow: /
User-agent: Bingbot
Allow: /
User-agent: OAI-SearchBot
Allow: /
User-agent: ChatGPT-User
Allow: /
User-agent: PerplexityBot
Allow: /
User-agent: Claude-SearchBot
Allow: /
User-agent: Claude-User
Allow: /
User-agent: DuckAssistBot
Allow: /
User-agent: Applebot
Allow: /
User-agent: Yeti
Allow: /
User-agent: Daumoa
Allow: /

# --- 학습 전용: 회사 정책 (KORVIA 기본=허용 유지 → 브랜드 각인) ---
# 차단하려면(검색 인용엔 무영향):
# User-agent: GPTBot
# Disallow: /
# User-agent: Google-Extended
# Disallow: /
# User-agent: CCBot
# Disallow: /
# User-agent: Applebot-Extended
# Disallow: /
# User-agent: Meta-ExternalAgent
# Disallow: /
# User-agent: Bytespider
# Disallow: /

Sitemap: https://www.korvia.com/sitemap.xml
```

- **핵심 실수 방지**: `User-agent: * Disallow:` 밑 광범위 차단이 신규 AI 봇까지 막는다. AI 노출을 원하면 **명시적 Allow 블록**을 두고 와일드카드 차단 회피.
- 검색봇엔 `Crawl-delay` 걸지 말 것(색인 지연). 서버 부하 시만.
- **IP+UA 이중검증**: 스푸핑 방지 위해 WAF에서 각 사 공개 IP JSON으로 대조. 특히 `Perplexity-User`/`ChatGPT-User`는 UA 위조 흔함. **`Yeti`(Naver)·`Daumoa`(Daum)는 UA 토큰 기준으로 매칭**하고, 특히 Daum은 공개 IP JSON이 없어(소유이전 이력) **IP 하드코딩 금지**.
- ⚠ **이 템플릿은 사본**: robots 템플릿·봇 목록의 단일 SSOT는 [`ROBOTS_SITEMAP_RSS_TEMPLATES.md`](./ROBOTS_SITEMAP_RSS_TEMPLATES.md) §A. 실제 배포 전 그 원본과 대조(불일치 시 §A 우선).
- Next.js는 `app/robots.ts`의 `rules: []`로 User-Agent별 규칙 정의(WEB_PLAYBOOK §11.4). **환경변수 `Sitemap` 값은 `.trim()`**(trailing newline 버그, WEB_PLAYBOOK §1.1).

### D.7 Cloudflare 함정 (Kimchi가 CF 뒤면 특히)
- ⚠ **`Block AI bots` 토글 절대 OFF**: 학습봇 **뿐 아니라 OAI-SearchBot·PerplexityBot 등 검색·인용 봇까지 전부 차단** → ChatGPT/Perplexity 답변에서 소멸. (현행 토글은 3옵션: '광고 있는 호스트만 차단' / '전체 차단' / off — 어느 것도 검색/학습을 **구분 안 함**.)
- ⚠ **CF는 2025-07-01부터 신규 가입 도메인에 AI 크롤러 기본 차단(default block)** 도입. CF에 신규 등록한 사이트는 `AI Crawl Control > Manage AI crawlers`에서 검색·인용 봇을 **명시적 Allow로 되돌려야** 함(계정/플랜별 상이 → 대시보드 실확인).
- 세분 제어는 `AI Crawl Control`에서 크롤러 단위 allow/block. `Crawlers` 탭은 봇별 요청수·robots 위반 로그 제공 → **측정 소스로도 활용**(§E). `pay per crawl`은 수익화용, KORVIA는 노출 목적이라 미적용.
- ⚠ **Perplexity 스텔스 크롤러 논란**(Cloudflare 2025-08 고발): robots 차단 시 미신고 우회 크롤러로 접근 관측 → robots.txt만 믿지 말고 **IP+UA 이중검증** 필수.

### D.8 LLM 파싱 친화 마크업 (llms.txt보다 확실한 레버)
- 크롤러가 **이미 읽는** 표준 신호가 진짜 레버: 시맨틱 HTML(§0.3), Organization/Article/FAQPage 스키마(§C), SSR/SSG 서버 렌더 텍스트.
- AI봇 상당수는 JS 실행이 제한적 → **서버 HTML에 텍스트가 있어야** 함(Next.js RSC/SSG, WordPress 클라이언트 전용 위젯 남용 금지).
- 크롤 가능성 기본기: `robots` 메타 `index,follow`, canonical, hreflang(ko/en), XML 사이트맵 최신, 4xx/5xx·리다이렉트 체인 제거, Core Web Vitals(LCP/INP/CLS).

---

## E. 측정 — AI 가시성·인용 추적 + GSC/로그 AI봇

> **측정 3계층 (이 §E = 계층별 상세·전용 도구·Naver 측정 공백의 SSOT — 값은 여기서만 보유; MULTI_ENGINE_MASTER는 여기로 링크):**
> 1. **검색 노출** — GSC 'Search 생성형 AI 성능 리포트'(impressions-only, [V1] §E.1) + Bing 'AI Performance' 인용 리포트(값은 [`BING_YAHOO_INDEXNOW.md`](./BING_YAHOO_INDEXNOW.md)).
> 2. **AI 참조 트래픽(사람 클릭)** — GA4 기본 채널 그룹 **'AI Assistants'**(2026-05-13 추가, [V5] §E.2) + 인용·가시성 추적 SaaS(Otterly.AI·Profound 등, [V9] §E.3).
> 3. **봇 크롤 실측** — 서버 액세스 로그 AI봇 UA + Cloudflare **AI Crawl Control**(전 플랜 무료 포함, [V11] §E.2).
> ⚠ **Naver 측정 공백**: Naver는 **AI Briefing 인용을 보여주는 공식 콘솔 데이터가 없다** — 인용 여부는 대표 질의를 **수동 관측**하거나 자사 UGC(블로그/카페) 유입으로 간접 추정할 뿐, GSC/Bing 같은 노출 리포트가 없다.

### E.1 GSC — 'Search 생성형 AI 성능 리포트' (2026-06-03 출시)
- **무엇**: AI Overviews·AI Mode·Discover의 생성형 기능 **노출(impressions)**을 별도 뷰로. 차원=Pages·Countries·Devices·Dates.
- ⚠ **한계**: **클릭 데이터 미포함**(현 버전), **AI Overviews와 AI Mode는 합산 제공**(분리 필터 없음), 일부 사이트 점진 롤아웃, 표준 성능리포트의 1,000행 제한 상속. → **노출을 클릭으로 오해 금지**. AI 노출↔유입 인과는 GSC만으론 못 봄.
- KORVIA: 4사 모두 GSC 등록·주간 모니터.

### E.2 서버 로그 / Cloudflare(봇 크롤) + GA4 'AI Assistants'(참조 트래픽)
- **[계층3·봇 크롤] 서버 액세스 로그**에서 UA로 `GPTBot`·`OAI-SearchBot`·`ClaudeBot`·`PerplexityBot`·`Yeti`·`Bytespider` 요청수·상태코드·경로 집계 → "어떤 AI봇이 어디를 얼마나 크롤하나" 사실 확인.
- **[계층3] Cloudflare `AI Crawl Control`**(구 AI Audit, 2025 리브랜딩): **전 Cloudflare 플랜에서 제공(무료 포함)·자동 작동**. AI 크롤러를 자동 식별해 회사별(OpenAI/Microsoft/Google/ByteDance/Anthropic/Meta)·크롤러별(GPTBot/ClaudeBot/Bytespider 등)로 분류, `Metrics` 탭에서 크롤러별 요청 추이·드릴다운, **"Download CSV"로 내보내기**. `Crawlers` 탭은 봇별 요청수·allow/block·robots 위반 로그 제공. ([V11] 확인 2026-07 — ⚠ "20+개 크롤러"라는 구체 수치는 공식 문서 미명시. `developers.cloudflare.com/ai-crawl-control`)
- **[계층2·사람 참조 트래픽] GA4 기본 채널 'AI Assistants'**: GA4는 **2026-05-13** 기본 채널 그룹에 **'AI Assistants'** 채널을 추가했다 — ChatGPT·Gemini·Deepseek·Copilot·Grok 등에서 유입된 **사람 클릭**을 referrer 기반으로 자동 분류(별도 설정 불필요, 광범위 롤아웃 ~2026-06-07). ⚠ **Google 자체의 AI Overviews·AI Mode는 Organic Search로 분류되어 이 채널에서 제외**. ([V5] 확인 2026-07, `support.google.com/analytics/answer/9756891`)
- ⚠ **혼동 금지**: 위 GA4 'AI Assistants'는 **AI 어시스턴트發 사람 클릭(참조 트래픽)**을 재는 것이고, **AI봇 크롤 실측은 여전히 로그/CF가 정답**이다(GA4는 봇 트래픽을 필터링 → GA4로 AI'봇'을 재지 말 것).
- **UA 위조 대조**: §D.5 공개 IP JSON으로 실제 IP 검증(스푸핑 배제).

### E.3 AI 가시성·인용 추적 SaaS
LLM은 API로 노출을 안 알려줌 → 도구들은 **대표 프롬프트를 반복 질의 → 답변에서 브랜드 언급·인용 URL·감성 파싱**.

| 도구 | 특징 | 가격(⚠ 급변, 도입 전 공식 페이지 재확인 — 아래는 [V9] 2026-07 확인) |
|---|---|---|
| **Ahrefs Brand Radar** | **무료 티어 有**, Ahrefs 데이터 통합 | 무료 + 유료 $129/mo~ |
| **Otterly.AI** | 프롬프트 추적·AIO 링크 추적. 기본 4개 엔진(ChatGPT·Google AIO·Perplexity·MS Copilot; Claude·Google AI Mode·Gemini는 애드온) | **Lite $29/mo**(프롬프트 15) · **Standard $189/mo**(100) · **Premium $489/mo**(400) |
| **Peec AI** | 유럽발, 프롬프트 모니터·경쟁 벤치 | 중소($99~수백/mo) |
| **Profound** | 인용 DB·프롬프트 볼륨 최대·API. 누적 $155M, 2026-02 Series C $96M @ $1B, Fortune500 다수 | **Starter $99/mo**(연간결제, ChatGPT 1개 엔진) · **Growth $399/mo**(ChatGPT·Perplexity·Google AIO 3개) · 상위=엔터프라이즈 |
| **Semrush AI Visibility Toolkit** | 261M+ LLM 프롬프트, share of voice | 애드온 $99/mo(+본 플랜) |

- **KORVIA 시작점**: **Ahrefs Brand Radar 무료 티어 + GSC AI 리포트 + 서버로그**로 베이스라인. 인용 KPI가 사업상 중요해지면(Korvia 비자·Kimchi eSIM) Otterly(저가)/Peec 도입.
- **대표 프롬프트 셋(한/영)**: `"best eSIM for foreigners in Korea"`, `"how to get E-2 visa Korea"`, `"EPIK application help"`, `"한국 외국인 취업 비자 종류"`, `"외국인 한국 유심 추천"` — 월간 인용 여부 기록.

### E.4 IndexNow — Naver·Bing 즉시 색인 푸시
- **지원**: Bing · Yandex · **Naver(2023-07)** · Seznam · Yep. **Google 미지원**(2021부터 테스트만) → Google은 사이트맵/GSC 유지.
- **의미**: 한 엔진에 ping하면 참여 엔진 전체 공유. KORVIA 한국 타깃엔 **Naver+Bing 즉시 색인**이 사실상 무료(발행/수정 즉시, 수주 → 분/시간 단축). ChatGPT=Bing 인덱스 경로 진입도 여기서.
- **엔드포인트**: `https://api.indexnow.org/indexnow?url=<URL>&key=<KEY>`. 배치는 POST JSON로 **최대 10,000 URL**.
- **키**: 8~128자 `[a-zA-Z0-9-]`. 루트에 `<key>.txt` 호스팅해 소유확인(서브디렉토리에 두면 그 경로 하위만 제출 가능).
- **응답코드**: 200 성공 / 202 검증대기 / 400 잘못된요청 / 403 키무효 / 422 URL불일치(host/키 스키마) / 429 레이트리밋.
- KORVIA: n8n으로 발행/수정 시 ping 자동화(Next.js=배포 훅, WordPress=플러그인/훅). **Naver 서치어드바이저 사이트맵/RSS 제출·수집요청은 별개로 병행**(별도 Naver/Daum 콘솔 문서 참조).

### E.5 검증 도구
- **Rich Results Test**(search.google.com/test/rich-results): 지원 리치결과만 검증(FAQ/HowTo/Searchbox 하이라이트 제거됨).
- **Schema Markup Validator**(validator.schema.org): 문법 검증(구글 지원 여부와 무관).
- **GSC > 리치결과 리포트 / URL 검사**: 색인 여부·렌더링(AIO 자격 전제).

---

## F. 한국 AI(Cue:/뤼튼) + 외국인 YMYL 신뢰

### F.1 ⚠ 폐기 사실 — Naver Cue: / Clova X
- **Naver Cue:는 통합검색 답변에서 제거, Cue:·Clova X 실험 서비스는 2026-04-09 종료**(Cue:는 2026-03-02부터 신규 신청 중단). → **이 KB 서빙 시점(2026-07)엔 "2026-04 종료됨"으로 정확히 서술.** "Cue: 최적화"는 폐기. (⚠ 훈련기억·일부 2차 소스가 "2025-04"로 1년 앞당겨 오기한 것을 인용하지 말 것.)
- 현재 한국어 AI 인용 대상 = **Naver 'AI Briefing' + wrtn(뤼튼) + (한국어로 쓰는) ChatGPT/Gemini/Perplexity**.

### F.2 Naver AI Briefing (한국인 청중 최우선)
- **출시 2025-03-27**(발표 2025-03-24), 통합검색 상단에 요약 + 원문 출처 인용.
- ⚠ **소스 풀의 약 70%가 Naver 자사 UGC**(블로그·카페) → **Naver 생태계 내 콘텐츠가 인용의 관문**. 외부 홈페이지만으론 인용 어려움.
- **랭킹 3축**(⚠ Naver 미전량공개, 2차 분석): (1)질의-콘텐츠 의미유사도, (2)권위·신뢰(백링크·포맷 다양성·갱신빈도) — **C-rank(주제 지속 전문성)**·D.I.A.+가 Smart Block에 통합, (3)사용자 인게이지먼트.
- **인용 포맷**: 리스트·표·번호·단계별 가이드(스캔가능).
- **Naver Mate 창작자 보상**: AI Briefing 인용 빈도에 따라 매월 10개 카테고리 약 3,000명에 보상(5년 1조원 투자).
- **Smart Block**: VIEW 탭 흡수 → 블로그(개인)·카페(커뮤니티) 재편, 의도별 블록.
- **KORVIA 함의**: Korvia/Kimchi의 한국인 대상 정보(외국인 채용 절차, eSIM 한국어 안내)는 **Naver 블로그 병행 발행**해야 AI Briefing 인용 진입.
- ⚠ Naver의 생성형 AI용 **공개 '크롤러 opt-out 표준'(Google-Extended 대응물)은 2026-07 현재 미확인** — 있다고 단정 금지. (Naver 후속 'AI탭' 통합검색 확장 진행 중 → 시의성 재확인.)

### F.3 wrtn(뤼튼) — 무료·무제한 AI 검색
- RAG + 브라우징 에이전트, **출처 명시**형. 최신정보 질의 성장. 한국어 사용자 대안 답변엔진.
- 최적화 = 명확 출처·구조·최신성(글로벌 GEO 레버와 동일 방향).

### F.4 글로벌 엔진의 한국어 질의
- ChatGPT/Gemini/Perplexity에 한국어로 물어도 §A 메커니즘 적용. 단 **한국어 풀에서 Reddit 대응물 = Naver 카페/블로그, 나무위키** 편향 관측. 한/영 이중언어 페이지는 양쪽 질의 커버.

### F.5 Daum/Kakao
- 현행 콘솔 = **Daum 웹마스터도구**(`webmaster.daum.net`) — 사이트 소유확인 후 사이트맵 등록·수집/색인/검색 통계(구 `register.search.daum.net` 검색등록과 별개). 독립 생성형 답변엔진 비중은 Naver보다 낮으나 **색인 등록은 필수 위생**. ⚠ Daum은 2025 Kakao에서 분사·소유/전략 급변 중 → '현행 콘솔' 서술에 변동성 주의(상세는 별도 Naver/Daum 콘솔 문서).

### F.6 외국인 대상 한국 YMYL(비자/취업/통신/금융) 신뢰
KORVIA 4사 중 **Korvia(비자·취업)·Kimchi(통신·결제)**가 YMYL 최고강도.

- **Google 공식**: YMYL(이민=Legal/Civic/Government)엔 신뢰·권위 소스만 노출하는 **더 높은 기준**. 섹터 오버랩(2026): 헬스 24% / 파이낸스 11%(전문 소스 65.7%가 top-100 밖) / 법률·정부는 AIO 커버리지 **축소 중**(시의성 질의 신중).
- **AI가 읽는 신뢰 패턴**: 저자 신원, 1차 출처 링크, 온페이지 사실 일관성, 도메인 평판, 웹 전반 인용 빈도, established consensus 정렬.
- **KORVIA YMYL 인용 게이트**:
  1. **1차·공식 출처 직접 링크**: 비자=HiKorea·법무부 출입국·외국인정책본부·EPIK(epik.go.kr)·각 비자 고시. 통신=과기정통부·알뜰폰 허브·통신사 공식. → 우리 페이지가 그 1차 소스를 **정확 요약+링크**하면 "신뢰 다리"로 인용가치↑.
  2. **저자 전문성 표기**: 실명·직함·자격(예: 외국인 채용 컨설턴트 경력), `Person`/`Article` `author`.
  3. **날짜·개정 이력 노출**: 비자·요금은 자주 바뀜 → 화면 타임스탬프 + `dateModified` 갱신(신선도=YMYL 인용 유지 축).
  4. **과장·미근거 수치 금지**: 각 수치에 출처 병기, `"as of {날짜}"`.
  5. **영/한 이중 + 명확 정의문**: 외국인=영어 Google/ChatGPT, 한국인=Naver — 두 트랙 병행.
- **엔진 궁합**: Claude/Perplexity가 정부·학술·확립뉴스·신선도를 강하게 우대 → YMYL 페이지는 1차 출처·저자·최신 날짜를 특히 강화하면 이들 엔진 인용에 유리.

---

## G. KORVIA 4사 적용 매트릭스

| 사이트 | 청중/엔진 우선 | 인용 진입 게이트 |
|---|---|---|
| **Korvia** (Next.js, YMYL 비자/취업) | 외국인=Google/ChatGPT 영어; 한국인=Naver | Bing+Google 색인, 정부 1차 출처 링크 + `Person` 저자표기, 영/한 answer-first, `JobPosting`(개별 페이지)·EPIK/HiKorea 참조, Naver 블로그 병행 |
| **Kimchi Mobile** (WP, YMYL 통신) | 외국인=Google/Perplexity/Reddit 영어 | Reddit(r/korea 등) 진성 언급, eSIM 비교표 + `Product`/`Offer`(이미지 ≥500×500), 요금 타임스탬프, Bing 색인, **CF Block AI bots OFF** 실확인 |
| **Passome** (Next.js, AI/AX·미술치료) | 한/영 혼합, B2B=LinkedIn | LinkedIn 존재감, `Organization`+Wikidata `sameAs`, 통계/케이스 인용, `SoftwareApplication`, route handler llms.txt(헤지) |
| **ACT Art Center** (Next.js, 교육) | 로컬 한국인=Naver, LocalBusiness | Naver 블로그/플레이스, `LocalBusiness`(정확 하위유형)·`Course`·`Event`, NAP 일관 |

---

## H. 발행 전 인용 게이트 (기계 적용 체크)

1. [ ] Google Search Console + **Bing Webmaster Tools 양쪽 색인** 확인, **IndexNow(Bing+Naver) ping**.
   - [ ] **Daum**: `webmaster.daum.net`에 수집 Seed URL(사이트맵/RSS) 등록 확인 + **신규 URL 수집요청**. ⚠ **Daum은 IndexNow 미참여 → 자동 push 경로 없음, 수동 제출만**이 유일 색인 경로(§F.5·[`DAUM_KAKAO_SEARCH.md`](./DAUM_KAKAO_SEARCH.md)). 한국 타깃 페이지가 Daum 색인 없이 발행되지 않도록.
2. [ ] 페이지 상단 30%에 answer-first 블록(핵심 결론 1문장 + 뒷받침 표/리스트).
3. [ ] 통계·명명된 출처·인용문 최소 각 1개(Princeton 레버), 각 수치에 `출처 + as of 날짜`.
4. [ ] 시맨틱 HTML 계층 + `datePublished`/`dateModified` + `Article`/`Organization` JSON-LD(본문과 일치).
5. [ ] FAQ 섹션(질문형 H2 + 직접답변) + `FAQPage` 스키마(리치결과 아닌 **AI 파싱 목적**).
6. [ ] `Organization` `sameAs`로 Wikidata·LinkedIn·공식 SNS 연결, NAP 일관.
7. [ ] robots.txt에 검색·인용 봇 명시 Allow(§D.6), 와일드카드 차단에 포함 안 됨. **CF Block AI bots OFF** 확인.
8. [ ] 한국인 타깃이면 Naver 블로그 병행 발행(C-rank 주제 일관).
9. [ ] YMYL이면 정부/공식 1차 출처 직접 링크 + `Person` 저자 전문성 표기.
10. [ ] hreflang 상호(return) 링크 + `x-default`, 서버 HTML(SSR/SSG)에 본문 텍스트 존재.
11. [ ] Rich Results Test + Schema Validator 통과(폐지 타입 경고 없음).

---

## I. 미해결·주의 (uncertainty — 사용 전 재확인)

- ⚠ **ChatGPT=Bing 인덱스 / Claude=Brave**는 **널리 보고**되나 공급사가 조용히 바뀔 수 있음(OpenAI 자체 인덱스 이동 가능, Anthropic 공식 미명시). **분기별 재확인.**
- ⚠ **벤더/2차 집계 수치**(Reddit 40%, AI Mode 97%/~7도메인, top-15 68%, Wikidata 2.7x/3.2x, 뉴스편향 OpenAI=wire/Perplexity=BBC/Gemini=Forbes, AI Mode 40% 커버리지)는 방법론·시점 혼재. **근사·방향성**으로만, 성과 약속 금지. 공식 확인치는 AI Mode 10억+ 사용자·Gemini 3.5 Flash. **AIO 노출률 ~47%(2026 Q1)는 '적격 쿼리' 기준·벤더/추정치(Google 공식 단정 아님)**로, **전체 쿼리 기준 15~25% 추정과 모수가 다르다**(적격 ⊂ 전체) → 공식치로 단정 금지·재확인.
- ⚠ **llms.txt**: Google 공식 무시(Illyes 2025-07), OpenAI 미지원, Anthropic·Perplexity만 확인, 채택률 ~10% → **인용 확보 레버로 과대평가 금지**(IDE/에이전트 툴링 용도).
- ⚠ **Princeton GEO 절대 %**는 2024 컷오프 역사치(원문 Table 1: 단일 최고=Quotation ~42~44%·Statistics ~34%·상위 3레버 30~40% 대역, Table 2: 5위 사이트 Cite Sources +115.1%) — **특정 % 단일 귀속·순위 단정 금지, 3레버(인용문·출처·통계) 병용**만 견고(§B.1 인용 규칙).
- ⚠ **ChatGPT-User는 robots.txt 미준수**(현행 OpenAI 공식). `Claude-User`=준수, `Perplexity-User`=미준수 — 봇별 상이. + Perplexity 스텔스 크롤러 고발 이력 → **IP+UA 이중검증**.
- ⚠ **Cloudflare**: 2025-07-01부터 신규 도메인 AI 크롤러 기본 차단. CF 신규 등록 사이트(Kimchi 등)는 검색·인용 봇 Allow 실확인·복구. `Block AI bots`는 3옵션이나 검색/학습 미구분.
- ⚠ **GSC 생성형 AI 리포트**: AIO+AI Mode 합산·클릭 미포함·점진 롤아웃 — 노출을 클릭으로 오해 금지.
- ⚠ **FAQ 폐지 다단계**: 예고 2026-05-08 문서추가 → 리치결과 미노출 2026-05-07 → SC 리포트·RRT 지원 제거 2026-06(문서 6-15) → **SC API의 FAQ 데이터는 2026-08까지 유지**(리포팅/측정 마이그레이션에 영향). `FAQPage` 스키마 타입 자체는 유효.
- ⚠ **Book Actions**는 '완전 폐지'가 아닌 예외(폐지 배너 철회, 잔존 기능) — '완전 종료'로 서술 금지. 신규 페이지엔 굳이 안 넣음.
- ⚠ **2026-03 코어 '홀리스틱 CWV 합성 스코어링'**은 **구글 미확인·3rd-party 상충 해석**. 사실로 쓰지 말 것. 실무 지침(LCP/INP/CLS 3지표 모두 통과 목표)은 유효하나 근거를 이 '합성 스코어'에 걸지 말 것.
- ⚠ **AI 가시성 SaaS 가격/투자/밸류**는 2차 보도·급변 → 도입 전 각 벤더 공식 가격 페이지 재확인.
- ⚠ **Naver 생성형 AI용 공개 크롤러 opt-out 표준**은 2026-07 현재 미확인(Google-Extended 대응물 부재).
- ⚠ **Google '특별 스키마·llms.txt 불필요'는 Google 검색/AIO 한정** — 타 AI 답변엔진(ChatGPT/Perplexity/Claude) GEO는 별개 정책이므로 일반화 금지.

---

## Sources

### 공식 1차
- Google — AI features and your website: https://developers.google.com/search/docs/appearance/ai-features
- Google — AI optimization guide (Last updated 2026-06-29; 특수 파일·마크업 불필요): https://developers.google.com/search/docs/fundamentals/ai-optimization-guide
- Google Analytics 4 — Default channels ('AI Assistants' 채널 2026-05-13): https://support.google.com/analytics/answer/9756891
- Cloudflare — AI Crawl Control / Analyze AI traffic (전 플랜·CSV): https://developers.cloudflare.com/ai-crawl-control/features/analyze-ai-traffic/
- Otterly.AI 가격: https://otterly.ai/pricing · Profound 가격: https://www.tryprofound.com/pricing
- Google — Search generative AI performance reports (2026-06-03): https://developers.google.com/search/blog/2026/06/gen-ai-performance-reports
- Google — Structured Data Markup Google Search Supports (search-gallery): https://developers.google.com/search/docs/appearance/structured-data/search-gallery
- Google — FAQPage structured data (deprecation): https://developers.google.com/search/docs/appearance/structured-data/faqpage
- Google — Farewell, Sitelinks Search Box (2024-10-21): https://developers.google.com/search/blog/2024/10/sitelinks-search-box
- Google — Changes to HowTo and FAQ rich results (2023-08): https://developers.google.com/search/blog/2023/08/howto-faq-changes
- Google — JobPosting structured data: https://developers.google.com/search/docs/appearance/structured-data/job-posting
- Google — Common crawlers (Google-Extended): https://developers.google.com/search/docs/crawling-indexing/google-common-crawlers
- Google — Localized versions / hreflang: https://developers.google.com/search/docs/specialty/international/localized-versions
- Google — Grounding with Google Search (Gemini API): https://ai.google.dev/gemini-api/docs/google-search
- Google — Search I/O 2026 (AI Mode 10억+·Gemini 3.5 Flash): https://blog.google/products-and-platforms/products/search/search-io-2026/
- Google Merchant — product image ≥500×500 (2026-04-14): https://support.google.com/merchants/answer/16989427
- OpenAI — Crawlers and Bots (GPTBot/OAI-SearchBot/ChatGPT-User/OAI-AdsBot): https://developers.openai.com/api/docs/bots
- Anthropic — Web search tool: https://platform.claude.com/docs/en/agents-and-tools/tool-use/web-search-tool
- Anthropic — Does Anthropic crawl the web / block crawler (8896518): https://support.claude.com/en/articles/8896518-does-anthropic-crawl-data-from-the-web-and-how-can-site-owners-block-the-crawler
- Perplexity — Crawlers (PerplexityBot/Perplexity-User): https://docs.perplexity.ai/docs/resources/perplexity-crawlers
- Cloudflare — Manage AI crawlers: https://developers.cloudflare.com/ai-crawl-control/features/manage-ai-crawlers/
- Cloudflare — Block AI Bots: https://developers.cloudflare.com/bots/additional-configurations/block-ai-bots/
- Cloudflare — Control content use for AI training (managed robots.txt): https://blog.cloudflare.com/control-content-use-for-ai-training/
- IndexNow — Documentation / Search engines: https://www.indexnow.org/documentation · https://www.indexnow.org/searchengines
- llms.txt spec (Jeremy Howard): https://llmstxt.org/
- wrtn(뤼튼) 무료 무제한 AI 검색: https://wrtn.io/news/
- Daum 웹마스터도구: https://webmaster.daum.net/
- Naver — HyperCLOVA X: https://navercorp.com/en/tech/hyperclovax

### 2차·연구 (⚠ 방향성/근사 — 절대치는 재확인)
- Princeton GEO (KDD 2024): https://collaborate.princeton.edu/en/publications/geo-generative-engine-optimization/ · 해설: https://seenrank.com/blog/the-princeton-geo-study-explained-for-marketers/
- GEO-16 (arXiv 2509.10762) — ⚠ 프리프린트(미피어리뷰): https://arxiv.org/html/2509.10762v1
- BuzzStream/Citation Labs — AI 봇 차단해도 인용 안 막힘(70~92%): https://www.buzzstream.com/blog/news-block-ai-bots-citations/
- Stanford SourceCheckup / 6 studies: https://everything-pr.com/how-ai-engines-cite-the-web-the-six-studies-that-define-the-2026-evidence-base
- 5W/Everything-PR AI Platform Citation Source Index 2026: https://everything-pr.com/ai-platform-citation-source-index-2026
- CXL — Where Google AI Overviews cite from: https://cxl.com/blog/google-ai-overview-citation-sources/
- AEO Vision — Google AI Mode GEO statistics 2026: https://aeovision.ai/articles/google-ai-mode-geo-statistics-2026/
- AISO System — Perplexity sources (Reddit): https://www.aisosystem.com/en/blog/perplexity-sources-how-to-get-cited
- Stackmatix — SearchGPT ranking factors (ChatGPT=Bing): https://www.stackmatix.com/blog/searchgpt-ranking-factors
- Stridec — How to get cited in Claude: https://stridec.com/blog/how-to-get-cited-in-claude/
- Search Engine Land — YMYL in AI Overviews: https://searchengineland.com/guide/ai-overviews-ymyl
- SEO Strategy — FAQ schema after 7 May 2026: https://www.seostrategy.co.uk/learn/faq-schema-deprecation-2026-rich-result-vs-schema/
- Google says llms.txt "purely speculative" (Illyes): https://www.searchenginejournal.com/google-says-llms-txt-is-purely-speculative-for-now/577576/
- Ahrefs — llms.txt study (97% no readers): https://ahrefs.com/blog/llmstxt-study/
- Ranqo — llms.txt adoption 10.13%: https://ranqo.ai/blog/llms-txt-complete-guide
- Digital Strategy Force — llms.txt 2026: https://digitalstrategyforce.com/journal/does-your-site-need-llms-txt-to-get-cited-by-ai-search-in-2026/
- No Hacks — AI User-Agent landscape 2026: https://nohacks.co/blog/ai-user-agents-landscape-2026
- Search Engine Land — Naver supports IndexNow (2023-07): https://searchengineland.com/naver-korean-search-engine-now-supports-indexnow-429880
- Surmado — Best AI visibility tools 2026: https://www.surmado.com/blog/best-ai-visibility-tools-2026
- SiliconANGLE — Profound $96M @ $1B: https://siliconangle.com/2026/02/24/profound-raises-96m-1b-valuation-ai-discovery-monitoring-platform/
- 리드젠랩 — 네이버 AI 브리핑 C-rank·AEO: https://blog.lead-gen.team/naver-ai-briefing-seo-optimal-strategy
- Tech Times — Naver AI Search Fund (3,000 creators): https://www.techtimes.com/articles/317336/20260528/
- BigGo — Naver Clova X·Cue: 종료: https://finance.biggo.com/news/gvNplJwBTwP6zY3HwUyJ · ZDNet KR: https://zdnet.co.kr/view/?no=20260225180559
- Naver AI Briefing 출시(2025-03-27, 발표 03-24): https://www.etnews.com/20250324000077
- Digital Applied — Entity SEO / Knowledge Graph 2026: https://www.digitalapplied.com/blog/entity-seo-knowledge-graph-optimization-guide-2026

---

_문서 끝. 갱신 시 상단 Generated 날짜와 §Δ·§I를 함께 업데이트할 것._
