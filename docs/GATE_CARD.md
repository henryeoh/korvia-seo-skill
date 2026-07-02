---
last_verified: 2026-07
reverify_by: 2026-Q4
---

# GATE_CARD — 발행 실행 카드 (런타임 tier-1, ≤~900토큰)

> Derived 2026-07 · [`CONTENT_AUTORULES.md`](./CONTENT_AUTORULES.md) 마스터 게이트 + [`MULTI_ENGINE_MASTER_2026.md`](./MULTI_ENGINE_MASTER_2026.md) §0 + 엔진별(NAVER/DAUM/GOOGLE/BING/GEO_AEO) §0의 중복제거 합본.

- **권한(승인 주체 아님)**: 파생 실행 카드일 뿐 **반려/승인 권한 없음**. 승인=독립 체인만 — **Quality Harness가 `[AUTO]` 실행 + slop-judge가 `[JUDGE]` 판정**(author≠judge). 규칙 SSOT=`CONTENT_AUTORULES.md`; 값 충돌 시 **마스터 우선**.
- **로드**: 훅 자동적용 아님 → 작성/기술 **에이전트 read-path**. 이 카드 먼저 → 엔진 타깃 시 해당 §0 → 실패 시 전문.
- **태그**: `[AUTO]`=기계검사(regex/DOM/카운트) · `[JUDGE]`=slop-judge 위임(경험·원본성·YMYL 정확성).

## 0. page-type 분기 (실행 전 먼저 판정)

타입 확정 후 해당 항목만 적용. 전체 변형표 SSOT=`CONTENT_AUTORULES.md`; 핵심 델타:

- **article**: 저자 필수(Article+저자·dateModified+Breadcrumb) · ≥3 통계 + 1차 경험 필수 · 하단 FAQ 필수 · YMYL 전 사실 출처+As of.
- **homepage**: 저자 불요(Org+WebSite 루트 1회·재주입 금지) · 권위 근거(수상·인증)·실적 · FAQ 선택 · 회사 실체·NAP.
- **landing**: 저자 불요(Service/Product/JobPosting/LocalBusiness/Event) · 요금·자격·결과 표 + 배치/개통 결과 · FAQ **필수** · 요금·비자 공식 소스.

## 1. `[AUTO]` — Quality Harness 기계 검사 (전 타입, 표기 예외만)

1. **[H1]** 1개·엔티티+키워드 front-load.
2. **[질문형 헤딩]** H2/H3 쿼리형. *(article 필수·home 최소·landing=FAQ)*
3. **[즉답·TL;DR]** 섹션 직후 40~60단어(모바일 36~44) 자기완결 답 + 상단 TL;DR 30~80단어. *(home=히어로 1문장)*
4. **[표/리스트]** 비교·자격·요금은 표/번호 리스트.
5. **[메타]** Title 50~60자·≤600px·front-load(자동 `| Brand` 시 중복 금지) / Desc 155~160자·모바일 첫 110자 / 전 이미지 서술형 alt.
6. **[정규화·국제화]** canonical=절대 URL + 언어판 self-canonical. KR/EN 짝이면 왕복(자기참조)+x-default hreflang·절대 URL·ISO(`ko`/`en`·지역 `KR`·**영국 `GB`≠UK**).
7. **[스키마 생사]** 살아있는 타입만 신규. **죽은 리치결과 스키마 신규 금지**(FAQPage·HowTo 포함 10종) — FAQPage/HowTo는 파싱 목적만·Book Actions는 폐지 아님. 전체 생사표 SSOT=[`SCHEMA_JSONLD_CATALOG.md`](./SCHEMA_JSONLD_CATALOG.md).
8. **[robots 미차단]** 인용·색인 검색봇을 `Disallow` 안 함(전체 목록 SSOT=[`ROBOTS_SITEMAP_RSS_TEMPLATES.md`](./ROBOTS_SITEMAP_RSS_TEMPLATES.md) §A) — 특히 한국 최우선 **`Yeti`(Naver)·`Daumoa`(Daum)** 누락 금지 + `nosnippet`/`max-snippet:0` 미설정. ⚠ `ChatGPT-User`는 robots 무시(사용자 개시 fetch, [V7]) → **검사 제외**. 학습봇(`Google-Extended`·`GPTBot`·`ClaudeBot`·`CCBot`)은 차단해도 검색/AIO 무영향 → **사업 판단**(게이트 필수 아님).
9. **[SSR]** `curl -A "OAI-SearchBot" <url>`에 본문 존재(빈 root=실패).
10. **[내부링크]** 관련 3~5 + 허브, 서술형 앵커(URL·"여기" 금지). *(home=rail)*
11. **[저자/날짜/시점]** 저자 블록(실명·직함·자격·링크)+발행일+**최종 수정일** 가시+Article JSON-LD; "2026년 기준"·"As of YYYY-MM"(요금·법령·통계). *(article 필수·home/landing=Org 대체)*
12. **[통계 카운트]** 검증가능 숫자(연도·%·금액) ≥3곳. *(article 필수)*

## 2. `[JUDGE]` — slop-judge 위임 (주관·무결성)

- **[밀도]** *AUTO 프록시*: "핵심 사실 5~7개" 대신 **표 행/볼드 단독 클레임 ≥3** 카운트. *(article)*
- **[출처·인용문]** 핵심 사실마다 1차 출처 + 전문가/공식 직접 인용문 ≥1(품질). YMYL은 전 사실 출처+As of.
- **[경험]** 1차 경험/사례/실측 ≥1. *(§0 타입별)*
- **[YMYL 정확성]** 공식 1차 소스와 일치·과장·미검증 0 = **하드페일**.
- **[원본성]** 고유명 반복·non-commodity 1차 관점·일반론 회피·1,500자+ 깊이. Naver 한국어 자산=경험형·소제목·직접촬영 이미지(정량치는 "추정" 라벨).
- **[최종 판정]** slop-judge로 **Writing Quality A+ · AI-SLOP 0 · YMYL 하드페일 0**. 실패 시 P0/P1/P2 fix-list를 writer 패스(kimchi-blog/korvia-content-pm/seo-harness-team)로 반려 후 재게이트. slop-judge=read-only(재작성 안 함).

## 3. 발행 후 (게이트 통과조건 아님 — 런북 이관)

발행 전 게이트는 발행 후 행위를 검증 못 함 → 게이트 시점엔 **"제출 자동화 구성됨"만 확인**, 실행은 [`MULTI_ENGINE_MASTER_2026.md`](./MULTI_ENGINE_MASTER_2026.md) §5 STEP2:

- GSC 색인요청(Google IndexNow 미지원) · **IndexNow 핑**(참여 엔진 SSOT=[`BING_YAHOO_INDEXNOW.md`](./BING_YAHOO_INDEXNOW.md) §4.1 [V8]; **Google·Daum 미참여**) · Naver 수집요청 · **Daum 별도 필수**(자체 Daumoa·IndexNow 미참여 → `webmaster.daum.net` 수집요청 + `register.search.daum.net` 검색등록).
