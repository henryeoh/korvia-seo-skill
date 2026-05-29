# AEO & GEO Strategy Playbook — 2026

> AI 검색 시대 마케팅 전략 레퍼런스. 운영 체크리스트는 `.claude/agents/geo-optimizer.md`(GEO)와
> `.claude/agents/technical-seo.md`(schema/llms.txt/robots)를 사용하고, 이 문서는 **왜 / 무엇을 / 근거 수치**를 담는 상위 전략 레이어다.
>
> Locked in: 2026-05-29 · Owner: Henry Oh (KORVIA)

---

## 0. TL;DR

- **SEO / AEO / GEO 는 대체 관계가 아니라 누적 레이어다.** 생성형 엔진도 기존 검색의 권위·관련성 신호를 그대로 쓰므로 SEO 토대 없이는 AEO/GEO 도 안 된다.
- 구글 검색의 **60%+ 가 클릭 없이 종료**(zero-click). SEO만 하면 "검색은 되는데 트래픽이 사라지는" 상황이 온다.
- GEO 인용을 늘리는 검증된 레버(Princeton 연구): **전문가 인용문 +41% / 통계 +30% / 출처 인용 +30%.** → "주장만 쓰지 말고 숫자·전문가 말·출처를 박아라."
- **플랫폼마다 인용 방식이 다르다.** ChatGPT=백과사전·Wikipedia(인용의 ~48%), Perplexity=최신성·Reddit(~47%), Google AI Overviews=기존 상위 랭킹. ChatGPT/Perplexity 양쪽에 동시 인용되는 도메인은 **11%뿐**.
- 기술 토대: **모든 핵심 페이지 JSON-LD + 루트 `llms.txt`** (둘은 역할 분담, 같이 쓴다).

---

## 1. 3-레이어 정의

| 약어 | 풀네임 | 목표 | 대상 엔진 | 핵심 단위 |
|------|--------|------|-----------|-----------|
| **SEO** | Search Engine Optimization | 순위 상위 노출(클릭 유도) | Google, Naver | 키워드·백링크·CWV |
| **AEO** | Answer Engine Optimization | 답변 박스에 내 문장이 직접 발췌 (제로클릭) | Google AI Overviews, 피처드 스니펫, 음성비서 | 단일 질문 → 단일 답변 |
| **GEO** | Generative Engine Optimization | 생성형 AI가 답변 합성 시 내 브랜드를 출처로 인용 | ChatGPT, Perplexity, Claude, Gemini | 복합 질문 → 합성 답변 |

### AEO vs GEO — 실행 단위 차이
- **AEO** = 단일 질문→단일 답변. "한국에서 영어 가르치려면 비자 뭐 필요해?" 같은 직답형에서 **내 문장이 그대로 발췌**되게. → FAQ, 명확한 정의 문장, 표, How-to.
- **GEO** = 복합 질문→합성 답변. "한국 취업 비자 종류 비교해줘"에서 **AI가 나를 신뢰 출처로 선택**하게. → 권위, 통계, 전문가 인용, 1차 데이터.
- 업계에서는 "사실상 같다"고도 보지만, 콘텐츠 제작 단위에서는 위처럼 나눠 설계한다.
- **둘 다 잘 먹히는 콘텐츠 유형**: 리서치 리포트, 데이터 기반 글, 종합 FAQ, How-to 가이드, 구조화된 비교표.

---

## 2. GEO 정량 근거 (Princeton GEO Study)

| 전술 | AI 가시성 향상 |
|------|---------------|
| 전문가 인용문(quotes) 추가 | **약 +41%** |
| 통계(statistics) 추가 | **약 +30%** |
| 출처 인용(cite sources) 추가 | **약 +30%** |

→ 실행 원칙: 검증 가능한 신호(숫자·전문가 말·출처)가 붙은 문장을 AI가 우선 인용한다.

---

## 3. 플랫폼별 인용 성향 (one-size 안 됨)

| 엔진 | 인용 성향 | 공략법 |
|------|----------|--------|
| **ChatGPT** | 백과사전형·확립된 권위. 인용의 약 **48% Wikipedia** | 사실 정합성, 구조적 콘텐츠, Wikipedia 정합. GPTBot 허용 |
| **Perplexity** | **최신성 + 커뮤니티 사례.** 소스의 약 **47% Reddit** | 최신 업데이트, 실제 사례·후기, 날짜/버전 명시. PerplexityBot 허용 |
| **Google AI Overviews** | **기존 상위 랭킹 콘텐츠** 우선 | 전통 SEO 직결 + Quick Answer Block + FAQ Schema |
| **Claude** | 사실 정확성 cross-check | ClaudeBot 허용 + llms.txt + 명확한 섹션 헤더 |

> 6.8억 건 인용 분석(The Digital Bloom): ChatGPT·Perplexity 동시 인용 도메인 **11%뿐** → 플랫폼별 전략 분리 필수.

---

## 4. 기술 실행 — 무엇을 깔아야 하나

### (1) 구조화 데이터 (Schema / JSON-LD) — 필수
- **ChatGPT 검색 인용 페이지 거의 전부가 schema 보유.** AI에게 "이 페이지가 무엇인지" 기계어로 번역.
- 우선순위: `FAQPage`, `Article`, `HowTo`, `Organization`, `Course`, `Product`, `BreadcrumbList`.
- 템플릿: `.claude/skills/seo-harness-team/resources/schema-templates/`.

### (2) llms.txt — 신규 표준
- 사이트 루트의 Markdown 파일. 사이트가 무엇에 관한 곳인지 LLM에 깔끔한 텍스트로 요약 → 환각 대신 **검증된 브랜드 사실** 사용 유도.
- **schema 와 역할 분담**: llms.txt = 사이트 전체 고수준 지도 / schema = 페이지별 세부. 둘을 **같이** 쓴다.
- 실증: 핵심 페이지 JSON-LD + 큐레이션 llms.txt → 색인 후 **하루 만에** 구글 AI 답변 인용 사례.

### (3) robots.txt — AI 크롤러 허용
- GPTBot, ClaudeBot, PerplexityBot, Google-Extended 허용. 패치: `resources/robots-txt-ai-patch.txt`.

### (4) 콘텐츠 구조 (한국어 NLP 고려)
- 질문형 H2 제목 + **즉답형 첫 문단**(질문 바로 아래 40~80단어 Quick Answer Block).
- 토픽 중심, Q&A 형태, 표·리스트로 추출 용이하게. 멀티모달 + 지속 업데이트(최신성 신호).
- 운영 디테일은 `geo-optimizer.md` 5 Pillars(Information Gain / Entity / Freshness / Conversational / Fan-out) 참조.

---

## 5. 측정 — AI 가시성 추적

> 툴 시장은 미성숙. **수동 테스트로 감 잡기 → 전문 툴 추가** 순서.

| 용도 | 툴 |
|------|-----|
| 쿼리/색인 (llms.txt 제출 가능) | Google Search Console |
| 참여도 | GA4 |
| AI Overview 추적 | SEMrush, Ahrefs |
| Schema 검증 + AI 크롤러 접근성 대규모 감사 | Screaming Frog |
| LLM 전용 인용 추적 (엔터프라이즈) | Profound, Scrunch, LLMrefs |
| 수동 베이스라인 | 핵심 쿼리 N개를 ChatGPT/Perplexity/Gemini에 직접 던져 "인용되는지" 월간 기록 |

---

## 6. 한국 시장 특이사항

- **네이버**가 한국 검색의 큰 축 → SEO는 Google + Naver 이원화.
- ChatGPT 일일 쿼리 ~25억 건(구글 140억 대비) — 비영어·전문 영역 침투 빠름.
- 한국어 AEO/GEO: 한국어 NLP 특성 고려 토픽 중심 + 명확한 Q&A 구조가 관건.
- KORVIA 타겟(외국인 구직자/교사 지망생)은 **영어 쿼리 비중 높음** → 영문 GEO(ChatGPT/Perplexity)가 특히 고효율.

---

## 7. KORVIA 적용 플랜

**Phase 1 — 기반 (즉시)**
1. 핵심 페이지 schema 감사: FAQPage / Article / Course / Organization JSON-LD 누락 점검 (TEFL·비자·EPIK 페이지 우선).
2. 각 도메인 `llms.txt` 생성 — 회사 정의, 핵심 서비스, 권위 근거(2013 서울시장 표창, 2018/2021 고용노동부 우수기관 인증) 명시.

**Phase 2 — 콘텐츠 GEO화**
3. 비자/취업 가이드를 **질문형 제목 + 즉답 + 통계 + 출처 인용** 구조로 리라이트 (+41%/+30% 레버).
4. EPIK Level·급여 매트릭스 등 **1차 데이터·비교표**를 명시 — AI 인용 친화 구조화 사실.

**Phase 3 — 플랫폼별 + 측정**
5. ChatGPT: 사실 정합·Wikipedia 정합. Perplexity: 최신성 + 실제 합격/배치 사례·후기.
6. 베이스라인: "teach English in Korea", "한국 취업 비자 종류" 등 핵심 쿼리 10개를 ChatGPT/Perplexity에 던져 현재 인용 여부 기록 → 월간 추적.

대상 자산: korvia.com, kftra.org, jobinkorea.co.kr, passome 등.

---

## 8. Sources

- [Jasper — GEO vs AEO vs SEO Guide 2026](https://www.jasper.ai/blog/geo-aeo)
- [Stackmatix — AEO vs SEO vs GEO Complete Guide 2026](https://www.stackmatix.com/blog/aeo-seo-geo)
- [Profound — AEO vs GEO](https://www.tryprofound.com/blog/aeo-vs-geo)
- [Pixis — ChatGPT vs Perplexity vs Gemini GEO Guide](https://pixis.ai/blog/chatgpt-vs-perplexity-vs-gemini-how-each-ai-engine-cites-differently-and-how-to-optimize-for-each/)
- [Frase — What is GEO 2026](https://www.frase.io/blog/what-is-generative-engine-optimization-geo)
- [Yotpo — What Is llms.txt](https://www.yotpo.com/blog/what-is-llms-txt/)
- [Discoverability — Schema Markup Guide 2026](https://discoverability.co/resources/schema-markup-guide/)
- [너드보드 — AEO & GEO 완벽 가이드](https://nerdboard.kr/blog/aeo-geo-optimization)
- [리드젠랩 — GEO vs AEO vs SEO 용어 정리](https://blog.lead-gen.team/geo-aeo-seo-comparison-guide)
