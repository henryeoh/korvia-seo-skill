---
last_verified: 2026-07
reverify_by: 2026-Q4
---

# 멀티엔진 색인·노출 마스터 (허브) — 2026

> Generated 2026-07-01 · Owner: Henry Oh (KORVIA) · ⚠ 콘솔/API 변경은 사용 전 재확인
>
> **이 문서의 역할**: KORVIA 4개 사이트(Passome / Korvia / Kimchi Mobile / ACT)를 Google(외국인·영어) + Naver(한국인) + Bing/Daum + AI 답변엔진에 **한 번 발행 → 모든 콘솔에 제출**하는 운영 관문(navigation hub)이다. 여기서 "무엇을·어디에·어떻게·한도"를 결정하고, 콘텐츠 전략(왜)은 [`AEO_GEO_PLAYBOOK_2026.md`](./AEO_GEO_PLAYBOOK_2026.md), 페이지 기술 구현(SSR/CLS/schema 코드)은 [`WEB_PLAYBOOK.md`](./WEB_PLAYBOOK.md) · [`NEW_PAGE_CHECKLIST.md`](./NEW_PAGE_CHECKLIST.md)로 위임한다. **중복 금지 — 이 문서는 색인·제출·모니터링 기계 절차 전담.**

---

## 0. 자동 적용 규칙 (Auto-apply)

작성자·에이전트가 **기계적으로** 따를 항목. 판단 불필요.

> **런타임 실행 카드**: 이 §0과 [`CONTENT_AUTORULES.md`](./CONTENT_AUTORULES.md) 마스터 게이트에서 파생된 발행 실행 카드는 [`GATE_CARD.md`](./GATE_CARD.md)(생성됨 2026-07-02)다. GATE_CARD는 파생된 실행 카드일 뿐 **승인 주체가 아니며**, 발행 반려/승인 권한은 독립 체인(**Quality Harness [AUTO] 실행 + slop-judge [JUDGE] 판정**, author≠judge)에만 있다.

1. **한 URL = 한 canonical(절대 https URL)**. 언어판은 각자 self-canonical(ko는 ko를 가리킴). 정규 도메인(www/non-www, trailing slash) 사이트 전역 통일.
2. **robots.txt에 `Sitemap:` 절대 URL 라인 필수** + Yeti(Naver)·Daumoa(Daum)·Googlebot·Bingbot·인용봇(OAI-SearchBot·PerplexityBot·Claude-SearchBot) **Disallow 금지**. `Disallow: /` 회귀 점검(전체 색인 제외 방지).
3. **소유확인 수단(DNS TXT / HTML 파일 / meta 태그) 절대 삭제 금지.** Naver 소유확인은 **유효기간 1년**(만료 30일 전 이메일) → 연장 캘린더 태스크 등록.
4. **Naver·Google 소유확인 meta·검증파일은 서버 렌더 `<head>`에 유지**(클라이언트 전용 렌더로 빼면 검증 실패). `<body>`/`<frame>`/meta refresh/JS 리다이렉트 페이지는 Naver 검증에서 제외됨.
5. **발행/수정 직후 IndexNow POST**(변경 URL만) → **Bing·Naver·Yandex·Seznam·Yep·Amazon** 참여 엔진에 즉시 통보(한 참여 엔진 엔드포인트에 제출하면 나머지 참여 엔진으로 자동 공유·전파, +Cloudflare CDN native 통합). **Google·Daum은 IndexNow 미참여** → Google은 sitemap `lastmod`+GSC로, Daum은 `webmaster.daum.net` 수집요청으로 커버. (참여 엔진 목록 SSOT=[`BING_YAHOO_INDEXNOW.md`](./BING_YAHOO_INDEXNOW.md); IndexNow 공식 FAQ 확인 2026-07 [V8])
6. **sitemap = canonical·색인대상(200·noindex 아님)만**. 파일당 **50,000 URL / 50MB(비압축)** 초과 시 sitemap index 분할. **Naver·Daum은 사이트당 최상위 sitemap 1개**만 제출 → 단일 index URL 유지.
7. **Indexing API(Google)는 JobPosting / BroadcastEvent 페이지에만.** 일반 페이지 색인 가속에 쓰면 수동조치(manual action) 리스크.
8. **죽은 리치결과 스키마 신규 생성 금지**: Google FAQPage·HowTo·Sitelinks Searchbox·Course Info·Estimated Salary·Learning Video·Special Announcement·Vehicle Listing·ClaimReview·Practice Problem (§4 표 참조). **단 Naver에선 FAQ·HowTo가 여전히 유효** → 엔진별 분리 표기.
9. **YMYL 페이지(Korvia 비자/취업, Kimchi 통신/금융)는 정확성 게이트 통과 후에만 IndexNow 핑.** 부정확한 최신본이 AI에 빠르게 인용되는 것을 차단. 본문에 `As of YYYY-MM` + 1차출처 링크.
10. **발행 전 SSR 검증**: `curl -A "OAI-SearchBot" <url> | grep <본문 키워드>` — 빈 `<div id=root>`면 크롤러·AI에 투명(실패).
11. **한국어 페이지는 Naver 트랙, 영어 페이지는 Google 트랙**으로 최적화하되 **동일 sitemap.xml을 양쪽에 제출**. Naver는 hreflang 무시 → Naver 대상 페이지는 위젯·메뉴·이미지 텍스트까지 전부 한국어.
12. **llms.txt에 자원 투입 금지**(2026 검증: 주요 검색·AI 엔진 랭킹/인용 미반영, IDE 코딩 에이전트만 fetch). 정적 저비용 부수효과로만. → §7 및 아래 ⚠.

---

## 1. 왜 멀티엔진인가 — 청중 매핑

KORVIA 4사이트는 **3개 청중**을 동시에 서빙한다. 엔진 하나만 최적화하면 청중 하나를 버린다.

| 청중 | 지배 채널 | 언어 | 핵심 엔진 작업 | 해당 사이트 |
|------|----------|------|---------------|------------|
| **한국인** (구직·B2B·학부모) | **Naver** | 한국어 | Naver Search Advisor + C-Rank/AEO + AI 브리핑/AI 탭 | 전 사이트 KO 페이지, Kimchi B2B, ACT 학부모 |
| **한국 거주/방문 외국인** | **Google (영어)** | 영어 | GSC + surviving rich results + hreflang | Korvia(EPIK/비자), Kimchi(eSIM) EN |
| **AI 답변엔진 사용자** | **ChatGPT·Perplexity·Google AIO·Claude·Naver AI 브리핑** | 한/영 | GEO 인용(answer-first·통계·1차출처·엔티티) + 인용봇 크롤 허용 | 전 사이트 정보성 페이지 |

### 1.1 KR 검색엔진 점유율 (2025 연평균, Internet Trend)

| 엔진 | 점유율 | 비고 |
|------|--------|------|
| **Naver** | **62.86%** | 전년 58.14%→62.86%(+4.72%p), **3년 만에 60%대 재돌파(2022 이후 최고)**, 2년 연속 상승 |
| **Google** | ~29.55% | 외국인·영어 쿼리에서 사실상 지배적 |
| **Bing** | ~3.12% | 다음을 근소하게 제치고 3위(⚠ 소수점은 출처별 편차) |
| **Daum** | ~2.94% | 4위. 네이트가 다음 결과 공유(2014~) → 합산 시 무시 못 함 |

> ⚠ **"62.86% = 8년 최고치"는 부정확.** 정확 표현은 위 표(3년 만에 재돌파). **시점 병기 필수 — 2025 연평균 62.86%, 2026 상반기 ~64%(2026-01~03 기준 ~64.39%로 추가 상승)**. 출처: [tech42](https://www.tech42.co.kr/네이버-2025년-국내-검색-점유율-62-86-3년-만에-60-재돌파/) · [economidaily](https://www.economidaily.com/view/20260104132353160)
> ⚠ Bing vs 다음 소수점(예: Bing 3.12% / 4.12%)은 출처마다 다름 → "다음 약 3%·4위, Bing 근소 우위 3위"로만 서술.
>
> ⚠ **측정 방법론 차이 병기 (이 문서 = 한국 점유율 서술 SSOT; NAVER·DAUM 문서는 여기로 링크)**: 위 표는 **Internet Trend**(국내 패널·로그 기반, Naver 우위). 글로벌 트래킹-코드 기반 **StatCounter**는 상반된 그림을 준다 — **2026-06 기준 Google 45.91% > Naver 43.68%(격차 약 2.2%p)로 Google 근소 우위**, 이하 Bing 6.28%·CocCoc 1.22%·Yandex 1.14%·Daum 1.14%(확인 2026-07 [V10]). 두 소스는 측정 모수(국내 표본 vs 글로벌 방문자)가 달라 순위가 뒤바뀔 만큼 차이가 크다 → **단일 %를 단정하지 말고 소스·시점·방법론을 반드시 병기**. 출처: [StatCounter South Korea](https://gs.statcounter.com/search-engine-market-share/all/south-korea)

### 1.2 2025~2026 KR 대전환 (실전 영향)

- **Naver AI 브리핑**: 통합검색 쿼리 20%+에 적용, 연내 2배 확대 목표. 정보형 쿼리에서 권위성·최신성·구조화 종합 평가로 상단 요약.
- **Naver AI 탭**: ⚠ **'예정' 아님 — 2026-06-26경 전체 이용자 대상 정식 출시(LIVE)**. PC 메인 검색창·AI 브리핑 하단·쇼핑/플레이스 적용. 출처: [newspim](https://www.newspim.com/news/view/20260625000973). → **외부 홈페이지는 AI 브리핑/AI 탭 '출처 인용'을 실전 타깃**.
- **Naver Cue: / ClovaX 종료(2026-04-09)**: 죽은 서비스 → 별도 GEO 타깃 최적화 금지. 통합검색 AI 브리핑/AI 탭으로 수렴.
- **Daum 소유권 대변동**: 카카오 → 2026-01-29 주식교환 MOU → 2026-05-06 카카오 이사회 매각 의결 → **2026-05-07 본계약 체결(운영사 AXZ, ⚠ 공정위 기업결합 심사 잔여 — "인수 완료"로 단정 금지)**. 업스테이지가 Solar LLM을 다음 검색에 결합해 AI 포털로 재편 중. → §3 STALE 경고. 출처: [v.daum(2026-05-07 본계약)](https://v.daum.net/v/3XLOilB3r7) · [뉴시스 MOU](https://v.daum.net/v/3eTgG0YCGW?f=p)

---

## 2. THE 크로스엔진 색인·노출 파이프라인 (한 번 발행 → 모든 콘솔)

```
                          ┌─────────────────────────────────────────────┐
                          │  0. PRE-PUBLISH GATE                          │
                          │  · 정확성/YMYL 검수 (As of + 1차출처)          │
                          │  · SSR 렌더 확인 (curl -A bot | grep 본문)    │
                          │  · canonical(self) · hreflang(양방향) · OG4   │
                          │  · surviving schema만 (§4) · robots 미차단    │
                          └───────────────────────┬─────────────────────┘
                                                  │ 발행 (sitemap lastmod 갱신)
                                                  ▼
        ┌──────────────────────┬──────────────────────┬──────────────────────┬─────────────────────┐
        ▼                      ▼                      ▼                      ▼                     ▼
  ┌───────────┐        ┌───────────────┐      ┌────────────────┐     ┌────────────────┐    ┌──────────┐
  │  GOOGLE   │        │   BING (WMT)  │      │ IndexNow 1 POST│     │ NAVER Search   │    │  DAUM    │
  │   GSC     │        │               │      │ (Bing·Naver·   │     │  Advisor       │    │          │
  │           │        │               │      │  Yandex·Seznam │     │                │    │          │
  │ sitemap↑  │        │ sitemap↑      │      │  ·Yep·Amazon   │     │ sitemap+RSS↑   │    │ 검색등록 │
  │ URL검사→  │        │ URL Submit    │      │  자동전파)     │     │ 웹페이지수집→  │    │ +수집요청│
  │ 색인요청  │        │ (≤10k/day)    │      │ 변경 URL만     │     │ (한도 준수)    │    │ (Seed)   │
  │ (핵심만)  │        │ ★IndexNow 1순위│      │ ≤10,000/req    │     │ ★IndexNow도지원│    │          │
  └─────┬─────┘        └───────┬───────┘      └───────┬────────┘     └───────┬────────┘    └────┬─────┘
        │                      │                      │                      │                  │
        └──────────────────────┴──────────────────────┴──────────────────────┴──────────────────┘
                                                  │
                                                  ▼
                          ┌─────────────────────────────────────────────┐
                          │  VERIFY & MONITOR                             │
                          │  · GSC 색인 커버리지 + Gen-AI 리포트          │
                          │  · Bing AI Performance(인용) · Markup Validator│
                          │  · Naver 사이트 진단(월 1회, 4분류)          │
                          │  · IndexNow 응답코드 로깅(200/202만 성공)     │
                          │  · 서버로그 AI봇 UA 추적                       │
                          └─────────────────────────────────────────────┘
```

### 2.1 발행 시 실제 제출 체크리스트 (복붙용)

- [ ] **sitemap `<lastmod>`** 실제 변경시각으로 갱신(priority/changefreq는 Google 무시 → 신뢰신호로 쓰지 않음).
- [ ] **IndexNow POST** → `https://api.indexnow.org/indexnow` (변경 URL만). 한국인 청중 확실 커버가 필요하면 **Naver 직접 엔드포인트 병행**: `https://searchadvisor.naver.com/indexnow?url={url}&key={key}` (Naver는 IndexNow **공식 지원** — 2023-07~, `guide/indexnow-request`; 제네릭 엔드포인트 제출도 참여 엔진 자동 공유로 Naver에 전파됨. 참여 엔진 SSOT=[`BING_YAHOO_INDEXNOW.md`](./BING_YAHOO_INDEXNOW.md)).
- [ ] **GSC**: sitemap은 자동 발견하지만, **핵심 랜딩·대규모 수정만** URL 검사 → 색인 생성 요청(하루 한도 있음, 같은 URL 반복 금지).
- [ ] **Bing WMT**: IndexNow가 1순위(Microsoft 권장). 추가로 URL Submit(검증 사이트 ≤10,000/day).
- [ ] **Naver**: `요청 > 웹페이지 수집`에 신규/중요 갱신 URL 등록(한도 준수 — §3 표). 블로그/뉴스형은 `요청 > RSS 제출`도.
- [ ] **Daum**: 신규 도메인·핵심 페이지만 `webmaster.daum.net` 수집요청. 최초 공개 시 `register.search.daum.net` 검색등록 1회.

---

## 3. 결정 매트릭스 (엔진 → 콘솔 → 소유확인 → 사이트맵/RSS → 개별 제출 → API/한도)

| 엔진 | 콘솔 URL | 소유확인 | 사이트맵 / RSS | 개별 URL 제출 | API / 한도 |
|------|----------|----------|----------------|---------------|-----------|
| **Google** | `search.google.com/search-console` | **Domain 속성=DNS TXT만**(전 서브도메인/프로토콜 커버, 권장) · URL-prefix=HTML파일/meta/GA/GTM/DNS | sitemap 50,000 URL/50MB, RSS·Atom도 sitemap으로 인정. `lastmod`만 신뢰(priority/changefreq 무시) | URL 검사 → **색인 생성 요청**(하루 한도 비공개·미보장, 대량은 sitemap) | **Indexing API=JobPosting/BroadcastEvent 전용.** publish **200/일**(태평양 자정 리셋), read 180/분, 전체 380/분. **IndexNow 미참여** |
| **Bing / Yahoo** | `bing.com/webmasters` (Yahoo=Bing 인덱스) | XML파일(BingSiteAuth.xml)/meta(msvalidate.01)/CNAME(verify.bing.com, DNS 전파 ≤72h) · **GSC Import 자동** | sitemap 50,000/50MB, index당 자식 50,000 | URL Submit **≤10,000/일**(신규 사이트는 낮게 시작, ~6개월 후 상향) | **IndexNow 1순위 권장.** AI Performance 리포트(2026-02-10, 인용 추적) |
| **Naver** | `searchadvisor.naver.com` | HTML 파일 업로드 / **`<head>` meta 태그**. **유효기간 1년**(만료 30일 전 이메일). meta refresh/JS 리다이렉트 제외 | `요청 > 사이트맵 제출`(sitemaps.org 표준, 50,000 분할). `요청 > RSS 제출`(`<description>`에 본문 전문). **사이트당 최상위 1개** | `요청 > 웹페이지 수집`(개별 URL) | **IndexNow 지원**(2023-07-25~, `guide/indexnow-request`). ⚠ 일일 수집한도 50/100 **공식 미노출 → 콘솔 실화면 기준** |
| **Daum / Kakao** | `register.search.daum.net`(검색등록·5일 심사) + `webmaster.daum.net`(웹마스터도구 Beta) | PIN 코드 발급 / meta / 파일 | Seed URL로 sitemap·RSS·Atom·목록페이지. 50,000 URL/50MB, **동일 호스트만** | 상단 `수집요청` 메뉴 | 공개 API 없음. ⚠ 네이트 결과 공유(중복 등록 금지) |

### 3.1 엔진별 크롤러 UA (robots/WAF 매칭은 **토큰 기준**, 버전·IP 아님)

| 엔진 | 크롤러 | UA 토큰 매칭 | 비고 |
|------|--------|-------------|------|
| Google | Googlebot(Smartphone 기본) | `Googlebot` | 모바일 우선 색인 마이그레이션 2024-07 완료(용어 폐기). Desktop봇은 Jobs/상품목록에 잔존 → 차단 금지 |
| Naver | **Yeti** | `Yeti` | `Mozilla/5.0 (compatible; Yeti/1.1; +https://naver.me/spd)` ⚠ **https로 갱신**(http 구식). Chrome 렌더러 토큰 변형도 존재 |
| Daum | **Daumoa** | `Daumoa` / `daum` | 이미지=`Daumoa-image/1.0`. ⚠ 소유권 이전으로 IP 대역 변동 가능 → **UA 토큰으로 허용**(CIDR 미공표) |
| Bing | Bingbot | `bingbot` | |
| ChatGPT | OAI-SearchBot | `OAI-SearchBot` | 차단 시 ChatGPT 검색 인용 탈락. GPTBot(학습)은 차단해도 검색 무영향 |
| Perplexity | PerplexityBot | `PerplexityBot` | Perplexity-User(유저 fetch)는 robots 무시 → 차단 불가 |
| Claude | Claude-SearchBot | `Claude-SearchBot` | ⚠ **Claude-User는 robots.txt 준수**(ChatGPT-User·Perplexity-User와 달리 차단 가능) |

### 3.2 ⚠ Daum STALE 경고 (감시 주기 반기→분기)

- **소유권**: 2026-07 현재 다음 운영사 AXZ는 **카카오 → 업스테이지로 매각 본계약 체결(2026-05-07)**, ⚠ **공정위 기업결합 심사 잔여**(완전 완료 아님). `register/webmaster.daum.net`은 작동하나 URL/정책/UA/IP **대개편 한가운데** → 분기별 재확인. 출처: [fnnews](https://www.fnnews.com/news/202605070819041556) · [v.daum](https://v.daum.net/v/3XLOilB3r7)
- **AI화 진행형**: 업스테이지가 Solar LLM을 다음 검색에 결합 추진 → "다음은 생성형 AI 미도입" 전제 무효화 임박.
- **카카오데이터트렌드 종료(2025-08-29, 과거형)**: 트렌드는 **네이버 데이터랩 / 구글 트렌드** 사용.
- **투자 원칙**: 점유율 ~3% → 다음 전용 콘텐츠 과투자 금지. 등록·sitemap·robots 허용의 **최소 위생**만.

---

## 4. 살아있는 리치결과 vs 죽은 스키마 (엔진별 유효성)

> **핵심 차별**: Google이 폐지한 FAQ·HowTo를 **Naver는 여전히 지원**한다. 이중 트랙 문서에서 엔진별 유효성을 반드시 구분 표기.

| 스키마 | Google (검색 리치결과) | Naver | 조치 |
|--------|----------------------|-------|------|
| **Article / BlogPosting** | ✅ 유효(author→Person+url/sameAs, datePublished/Modified ISO8601) | ✅ 수집·활용 | 유지 |
| **BreadcrumbList** | ✅ | ✅ 리치결과 노출 | 전 페이지 필수 |
| **Product / Offer** | ✅(price·priceCurrency·availability·priceValidUntil) | ✅ 상품 이미지 | Kimchi eSIM |
| **JobPosting** | ✅(datePosted·description·hiringOrganization·jobLocation[addressCountry]·title, validThrough 만료 처리, 리스트페이지 금지) | ✅ | Korvia 채용 + Indexing API 정당 경로 |
| **Organization / LocalBusiness / Event / Video / Review / Profile / Q&A / Discussion Forum** | ✅ 유효 | 부분 | 홈=Organization 1개(logo·sameAs·knowsAbout·founder·foundingDate) |
| **FAQPage** | ❌ **2026-05-07 검색 미표시, 06-15 문서 제거**(API는 ~2026-08 잔존). 파싱은 계속되나 리치결과 이득 0 | ✅ **여전히 유효** | Google=신규 생성 이득 없음(제거 불요). **Naver·AI 대비 목적이면 유지** |
| **HowTo** | ❌ 2023-08 축소→완전 제거(마크업 무시) | ✅ **전용 공식 가이드 존재**(`guide/structured-data-howto`) | Naver용은 유지, Google 이득 기대 금지 |
| **Sitelinks Searchbox** | ❌ 2024-11-21 전역 폐지 | — | 신규 금지(WebSite name/alternateName은 유지) |
| **Course Info·Estimated Salary·Learning Video·Special Announcement·Vehicle Listing·ClaimReview** | ❌ 2025-09-09 6종 제거 | — | 사용 금지 |
| **Practice Problem** | ❌ 2026-01-06 제거 | — | 사용 금지 |
| **Book Actions** | ✅ ⚠ **폐지 철회(2025-11 배너 제거) — 여전히 지원** | — | (과거 "폐지" 정보는 오류) 필요 시 사용 가능 |

> ⚠ FAQPage/HowTo는 "Naver 유효, Google 폐지"의 대표 사례. Google용으로 리치결과 노출을 기대하지 말 것.
> ⚠ **네이버 JSON-LD 리치결과는 공식 문서 부재** → "별점/FAQ 스니펫이 뜬다"고 단정 금지. OG 태그만 확실. JSON-LD는 Naver 수집·구글·AI 대비 목적.
> ⚠ Book Actions "사용 금지"는 과거 오류 — 2025-11 폐지 철회됨. 출처: [etavrian](https://www.etavrian.com/news/google-deprecates-practice-problem-structured-data)

---

## 5. '새 페이지가 라이브됨' 엔드투엔드 런북

### STEP 0 — 발행 전 게이트 (막히면 발행 중단)
1. **정확성/YMYL**: 비자·수수료·기한·요금 등 변동 사실에 `As of YYYY-MM` + 공식 1차출처(HiKorea·출입국·EPIK/NIIED·과기정통부·통신사) 링크.
2. **SSR 확인**: `curl -A "OAI-SearchBot" https://<url> | grep "<본문 핵심 문구>"` → 비면 실패(CSR-only). 공개 콘텐츠는 SSR/SSG로.
3. **기술 토대**(상세 코드는 [`NEW_PAGE_CHECKLIST.md`](./NEW_PAGE_CHECKLIST.md)): self-canonical, 양방향 hreflang+x-default(ko/en, ISO 639-1 소문자·ISO 3166-1 대문자 GB), OG4(og:title/og:type/og:image[1200×630,≤5MB]/og:url), surviving schema만(§4).
4. **크롤 가능**: robots.txt에서 해당 경로·Yeti·Daumoa·Googlebot·Bingbot·인용봇 미차단.

### STEP 1 — 발행
5. sitemap `<lastmod>` 갱신. 신규 URL을 sitemap(또는 index)에 등록. **Naver/Daum은 최상위 index 1개** 유지.

### STEP 2 — 제출 (§2.1 체크리스트 실행)
6. **IndexNow POST**(변경 URL만) — 코드 §6.
7. **GSC** 핵심 랜딩만 URL 검사→색인요청. **Bing** IndexNow(자동)+필요 시 URL Submit.
8. **Naver** `웹페이지 수집`(한도 내). **Daum** 신규 도메인/핵심만 수집요청.

### STEP 3 — 검증 (제출 후 수 시간~수일)
9. `curl` 재확인(200·본문 존재). GSC **URL 검사**(색인 상태) + Naver **URL 검사** + Bing **URL Inspection**.
10. surviving schema는 Google **Rich Results Test** / Bing **Markup Validator**로 인식 확인.

### STEP 4 — 모니터 (주기)
11. **GSC**: 색인 커버리지(`Crawled/Discovered - currently not indexed` 추세) + **Gen-AI 성능 리포트**(2026-06-03; ⚠ **AI Overviews+AI Mode 합산 = 분리 필터 없음**, **impressions만·클릭 미포함** → 클릭으로 오해 금지; 차원=Pages·Countries·Dates·Devices; 한국 소재 속성엔 아직 미노출 가능 — 안 보여도 정상).
12. **Bing AI Performance**: Total Citations + Grounding Queries(AI 내부 검색어)에 맞춰 헤딩·문구 조정.
13. **Naver 사이트 진단**(월 1회): 색인/수집제한/색인제외/SEO 4분류 추적, 색인제외 URL 목록(최대 2,000) 원인별 교정.
14. **IndexNow 응답 로깅**: 200/202만 성공. 400/403/422/429는 형식·키파일·도메인 일치 점검(성공 간주 금지).

### 색인 반영 기대치 (SLA 아님)
- Google: 색인 1~7일(요청해도 미보장). Naver: 등록~색인 통상 1~4주(⚠ "수집 후 1주"는 낙관적 3자 취지값, 수집보류로 크게 지연 가능). Daum: 검색등록 5일 심사.

---

## 6. 코드 스니펫 (복붙)

### 6.1 robots.txt / Next.js `app/robots.ts` 공통 규칙
```
# 색인/인용봇 명시 허용 — Disallow: / 회귀 금지
User-agent: Googlebot
User-agent: Yeti
User-agent: Daumoa
User-agent: bingbot
User-agent: OAI-SearchBot
User-agent: PerplexityBot
User-agent: Claude-SearchBot
Allow: /
Disallow: /api/
Disallow: /_next/
Disallow: /admin/

# 학습봇 차단은 사업 판단으로 분리(차단해도 검색 노출 무영향)
# User-agent: GPTBot / ClaudeBot / Google-Extended / CCBot

Sitemap: https://www.example.com/sitemap.xml   # 절대 URL, .trim() 필수(trailing newline 버그)
```
> ⚠ Next.js `robots.ts`에서 env 변수는 항상 `.trim()` — trailing newline이 `Sitemap: https://...\n/path`로 깨진 실제 버그(참조: [`WEB_PLAYBOOK.md`](./WEB_PLAYBOOK.md) §1.1).

### 6.2 IndexNow POST (curl)
```bash
curl -X POST https://api.indexnow.org/indexnow \
  -H "Content-Type: application/json" \
  -d '{
    "host": "www.example.com",
    "key": "'"$INDEXNOW_KEY"'",
    "keyLocation": "https://www.example.com/'"$INDEXNOW_KEY"'.txt",
    "urlList": ["https://www.example.com/new-page", "https://www.example.com/updated"]
  }'
# 200=성공 / 202=키검증 대기 / 400=형식 / 403=키무효 / 422=host·key 불일치 / 429=레이트리밋
# urlList ≤10,000, 모든 URL 도메인=host 일치
```

### 6.3 IndexNow 키 파일 (사이트 루트)
- 파일명 `{key}.txt`, 내용=키 값, UTF-8. **키 문자셋 = a-z / A-Z / 0-9 / 하이픈, 8~128자**(⚠ 공식이 "hexadecimal"이라 부르나 실제는 이 셋 — "16진수만"으로 오해 금지).
- Next.js: `public/{key}.txt`. 키 값은 `process.env.INDEXNOW_KEY`로만(클라이언트 노출 금지).

### 6.4 Next.js 발행 훅에서 IndexNow (배포 후 revalidate/API route)
```ts
// app/api/indexnow/route.ts (POST 트리거)
export async function POST(req: Request) {
  const { urls } = await req.json();            // 변경 URL 배열(≤10,000)
  const key = process.env.INDEXNOW_KEY!;
  const host = "www.example.com";
  const r = await fetch("https://api.indexnow.org/indexnow", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ host, key, keyLocation: `https://${host}/${key}.txt`, urlList: urls }),
  });
  return Response.json({ ok: r.status === 200 || r.status === 202, status: r.status });
}
```

### 6.5 WordPress (Kimchi Mobile)
- **Bing 공식 IndexNow 플러그인**(Settings » IndexNow » Get Started → 키 자동생성, 글 생성/수정/삭제 자동 핑) 또는 RankMath(내장). **Yoast는 IndexNow 미지원 → 별도 플러그인 병행.**

### 6.6 sitemap index (50,000 초과 분할)
```xml
<?xml version="1.0" encoding="UTF-8"?>
<sitemapindex xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <sitemap><loc>https://www.example.com/sitemap-pages.xml</loc><lastmod>2026-07-01</lastmod></sitemap>
  <sitemap><loc>https://www.example.com/sitemap-blog.xml</loc><lastmod>2026-07-01</lastmod></sitemap>
</sitemapindex>
<!-- Naver/Daum에는 이 index URL 하나만 제출 -->
```

---

## 7. GEO(AI 답변엔진) 인용 — 기계 절차 요약

> 콘텐츠 전략(왜·레버)은 [`AEO_GEO_PLAYBOOK_2026.md`](./AEO_GEO_PLAYBOOK_2026.md). 여기서는 **크롤 허용·측정** 기계 절차만.

- **인용봇 허용**(차단=인용 탈락): OAI-SearchBot(ChatGPT), PerplexityBot, Claude-SearchBot, Applebot, Googlebot, Bingbot. **UA 토큰으로 매칭**(버전 숫자 X — GPTBot/1.1·OAI-SearchBot/1.0 등 갱신됨).
- **robots로 차단 가능/불가**: Claude-User=**준수(차단 가능)**, Perplexity-User·ChatGPT-User=무시(차단 불가). ⚠ ChatGPT-User 미준수는 2025-12 정책 변경(과거엔 준수) — moving target, 확인일 병기.
- **Google-Extended**: Gemini 학습/그라운딩만 제어. **AI Overviews는 통제 못 함**(Googlebot 라이브 인덱스 사용) → AIO 억제 목적의 nosnippet 금지(일반 스니펫까지 죽음). ⚠ Google 공식 vs 일부 3자 SEO 소스 상충 — Google 1차 소스 근거로 표기.
- **측정 = 3계층**(계층별 상세·전용 도구·Naver 측정 공백은 [`GEO_AEO_AI_ENGINES_2026.md`](./GEO_AEO_AI_ENGINES_2026.md) §E가 SSOT — 값은 거기서만 보유):
  1. **검색 노출** — GSC Gen-AI 성능 리포트(impressions-only) + Bing AI Performance(인용).
  2. **AI 참조 트래픽** — GA4 기본 채널 그룹 **'AI Assistants'**(2026-05-13 추가, referrer 기반 자동 분류; ⚠ Google AIO/AI Mode는 Organic Search로 분류되어 제외) + 전용 GEO 추적 도구(Otterly.AI·Profound 등).
  3. **봇 크롤** — 서버로그 AI봇 UA(`GPTBot|OAI-SearchBot|ClaudeBot|Claude-SearchBot|PerplexityBot`) + Cloudflare AI Crawl Control(전 플랜 제공, 크롤러별 요청·CSV). AI봇 위장 필터=벤더 공식 검증 JSON(claude.com/crawling/bots.json, perplexity.com/perplexitybot.json)의 IP 대조.
- **IndexNow 경유 AI 가속**: Bing·Naver 색인 → ChatGPT(Bing)·Naver AI 브리핑 인용 후보에 빠르게 진입.

> ⚠ **llms.txt 재평가**: [`AEO_GEO_PLAYBOOK_2026.md`](./AEO_GEO_PLAYBOOK_2026.md)는 llms.txt를 권장했으나, **2026-07 검증상 주요 검색·AI 엔진은 llms.txt를 랭킹/인용에 미반영**(Google 공식 불필요, OpenAI/Anthropic/Perplexity 자동 판독 커밋 없음, 500M 봇방문 中 조준 408건). 실사용=IDE/코딩 에이전트. → **"인용 지렛대" 아님, 저ROI 실험**으로 격하. 만들더라도 정적 저비용, sitemap·크롤러빌리티를 대체하지 않음. 출처: [SEJ](https://www.searchenginejournal.com/google-says-llms-txt-is-purely-speculative-for-now/577576/)

---

## 8. 4개 프로젝트 적용 노트

| 사이트 | 스택 | 색인 우선 | 특이사항 |
|--------|------|----------|----------|
| **Passome** (passome.com) | Next.js/Vercel | Google(EN) + Naver(KO) | AI/AX 컨설팅. Organization+Service 스키마. Vercel 프리뷰(*.vercel.app)는 noindex+robots 차단(canonical 희석 방지) |
| **Korvia** (korvia.com) | Next.js | Google(외국인 EN) 지배 + Naver(KO) | **YMYL(비자/취업)**: author→Person 자격·1차출처·최종 업데이트일 본문 표면화. **JobPosting**으로 Google Jobs + Indexing API 정당 경로. EPIK/비자 페이지 우선 |
| **Kimchi Mobile** (kimchimobile.com) | WordPress | Naver(KO) + Google(외국인 EN) | **YMYL(통신/금융)**: Product/Offer 요금·재고 최신. **Bing 공식 IndexNow 플러그인**. Cloudflare AI Crawl Control이 인용봇 과차단 않는지 감사 |
| **ACT** (actartcenter.com) | Next.js | Naver(KO, 로컬) | 미술교육. **LocalBusiness/Course/Event** 스키마. **Naver 스마트플레이스** 최적화(업종 카테고리·영수증/방문자 사진 리뷰·행동데이터) — 외부 홈페이지가 못 들어가는 로컬 영역 별도 위성 자산 |

### 공통
- **이중언어**: 각 언어판 self-canonical, 양방향 hreflang(ko/en/x-default). **Naver는 hreflang 무시** → KO 페이지는 위젯·메뉴·이미지 텍스트까지 전부 한국어(Google용 EN 페이지와 분리).
- **한국어 자산 한계**: Naver 블로그·카페·인플루언서·지식iN은 외부 홈페이지가 진입 불가 → 필요 시 Naver 공식 블로그/플레이스 위성 운영 후 홈페이지로 링크.
- **권위 신호**(전 사이트): 2013 서울시장 표창, 2018/2021 고용노동부 우수기관 인증 등을 About·Organization 스키마에 일관 명시(엔티티 신뢰 = AIO·AI 브리핑 인용 게이트).

---

## 9. ⚠ 발행 전 필수 재확인 플래그 (지식 컷오프 2026-01 · 콘솔 개편 잦음)

1. **Naver 콘솔 가이드 슬러그 개편**: 현행 `guide/request-crawl`(수집요청)·`guide/url-inspection`(URL 검사)·`guide/report-crawl-refine`(수집 현황). 유효 확인분: `guide/faq-start-register`·`guide/report-seo`·`guide/structured-data-intro`(+`-faq/-howto/-video/-channel`). **각 링크 404 여부 콘솔에서 실검증** 후 사용.
2. **Naver 일일 수집한도(50/100)**: 공식 미노출 3자 추정 → 단정 금지, 콘솔 실화면 표기만.
3. **GSC URL 색인요청 하루 한도**: Google 미공개("약 10건"류는 비공식·변동).
4. **Daum 소유권/UA/IP/정책**: 업스테이지 인수 대개편 중 → **분기별** 재확인. WAF는 UA 토큰(`Daumoa`/`daum`)으로 허용(CIDR 미공표).
5. **Naver AI 탭**: '예정' 아님 — 2026-06 정식 출시(LIVE). '출처 인용' 최적화를 실전 과제로.
6. **봇 UA 버전**: 발행/감사 시점에 각 공식 봇 문서에서 재확인(토큰으로 매칭).
7. **비공식 통계 단정 금지**: C-Rank 가중치 %·AI브리핑 "6,000~8,000자·출처 50%"·AIO "검색 50%+"(Semrush 기준 late-2025 ~16%, 정점 ~25%)·Princeton 정확 %·Reddit/Wikipedia/YouTube 인용 %는 3자 추정 → 방향성만, 출처·기간 병기.
8. **가짜 지표 채택 금지**: "LCP 2.0s 강화"·"Engagement Reliability"는 web.dev 미확인 → 현행 LCP≤2.5s / INP≤200ms(2024-03 FID 대체) / CLS≤0.1 유지.

---

## 10. 심화 문서 링크 인덱스

### 이 스킬 내 기존 문서 (상대경로)
- 전략(왜/무엇/근거): [`AEO_GEO_PLAYBOOK_2026.md`](./AEO_GEO_PLAYBOOK_2026.md)
- 페이지 기술 구현(SSR/schema/CLS/성능/보안헤더): [`WEB_PLAYBOOK.md`](./WEB_PLAYBOOK.md)
- 새 페이지 17항목 체크리스트: [`NEW_PAGE_CHECKLIST.md`](./NEW_PAGE_CHECKLIST.md)
- CLS 7대 원인: [`CLS_SEVEN_SOURCES.md`](./CLS_SEVEN_SOURCES.md)
- CrUX vs Lighthouse 해석: [`CRUX_LIGHTHOUSE_INTERPRETATION.md`](./CRUX_LIGHTHOUSE_INTERPRETATION.md)
- next/image + Swiper: [`NEXT_IMAGE_SWIPER.md`](./NEXT_IMAGE_SWIPER.md)
- CSP allowlist: [`CSP_ALLOWLIST.md`](./CSP_ALLOWLIST.md)
- 3-레이어 에이전트 파이프라인: [`3LAYER_AGENT_PIPELINE.md`](./3LAYER_AGENT_PIPELINE.md)

### 이 허브가 여는 엔진별 심화 (✅ = 현존, 같은 `docs/` 폴더)
- ✅ Naver ★: [`NAVER_SEARCHADVISOR_PLAYBOOK.md`](./NAVER_SEARCHADVISOR_PLAYBOOK.md) — 서치어드바이저 콘솔(요청/검증/리포트)·C-Rank/D.I.A+·에어서치·AI 브리핑/AI 탭 랭킹 (허브 §1.2·§3·§4 확장)
- ✅ Daum ★: [`DAUM_KAKAO_SEARCH.md`](./DAUM_KAKAO_SEARCH.md) — webmaster.daum.net 대시보드·PIN 소유확인·Daumoa·업스테이지 인수 감시 (허브 §3.2 확장)
- ✅ Google 색인: [`GOOGLE_SEARCH_CONSOLE_INDEXING.md`](./GOOGLE_SEARCH_CONSOLE_INDEXING.md) — GSC 속성·Indexing API·크롤예산·모바일우선·CWV·hreflang·삭제도구 (허브 §3 확장)
- ✅ Google SERP/AI: [`GOOGLE_SERP_STRUCTURED_AI_2026.md`](./GOOGLE_SERP_STRUCTURED_AI_2026.md) — 리치결과 25종·폐지 스키마·AI Overviews/AI Mode·Google-Extended (허브 §4 확장)
- ✅ Bing/IndexNow: [`BING_YAHOO_INDEXNOW.md`](./BING_YAHOO_INDEXNOW.md) — Bing WMT·IndexNow 프로토콜·Yahoo/DDG/Ecosia·ChatGPT grounding (허브 §3·§6 확장)
- ✅ GEO/AEO 심화: [`GEO_AEO_AI_ENGINES_2026.md`](./GEO_AEO_AI_ENGINES_2026.md) — 엔진별 인용 메커니즘·봇 매트릭스·llms.txt·인용추적 측정 (허브 §7 확장)

### 공통 기반·자동규칙·템플릿 (현존)
- ✅ robots/sitemap/RSS/IndexNow 템플릿: [`ROBOTS_SITEMAP_RSS_TEMPLATES.md`](./ROBOTS_SITEMAP_RSS_TEMPLATES.md)
- ✅ JSON-LD 구조화데이터 카탈로그(복붙): [`SCHEMA_JSONLD_CATALOG.md`](./SCHEMA_JSONLD_CATALOG.md)
- ✅ 콘텐츠 발행 전 게이트 자동규칙: [`CONTENT_AUTORULES.md`](./CONTENT_AUTORULES.md)
- ✅ 프로젝트 스택별 구현(Next.js/WordPress): [`PROJECT_STACK_PLAYBOOK.md`](./PROJECT_STACK_PLAYBOOK.md)

---

## Sources

**Naver**
- https://searchadvisor.naver.com/guide/faq-start-register (등록·소유확인, 2026-07-01)
- https://searchadvisor.naver.com/guide/structured-data-intro (+ `-faq`/`-howto`/`-video`/`-channel`)
- https://searchadvisor.naver.com/guide/request-crawl · `/guide/url-inspection` · `/guide/report-crawl-refine` (개편 슬러그)
- https://searchadvisor.naver.com/guide/report-diagnosis · `/guide/report-seo`
- https://searchadvisor.naver.com/guide/indexnow-request (2023-07-25~)
- https://help.naver.com/service/30010/contents/17629 (사이트맵) · `/17583` (웹마스터도구)
- https://www.newspim.com/news/view/20260625000973 (AI 탭 정식 출시)
- https://www.tech42.co.kr/네이버-2025년-국내-검색-점유율-62-86-3년-만에-60-재돌파/
- https://zdnet.co.kr/view/?no=20260225180559 (Cue:/ClovaX 종료 2026-04-09)

**Google**
- https://developers.google.com/search/docs/crawling-indexing/sitemaps/build-sitemap
- https://developers.google.com/search/apis/indexing-api/v3/quickstart · `/quota-pricing`
- https://support.google.com/webmasters/answer/9008080 (소유확인) · `/9012289` (색인요청) · `/9689846` (삭제)
- https://support.google.com/webmasters/answer/7440203 (Discovered/Crawled not indexed)
- https://developers.google.com/search/docs/crawling-indexing/mobile/mobile-sites-mobile-first-indexing
- https://developers.google.com/search/blog/2024/06/mobile-indexing-vlast-final-final.doc (2024-07 완료·용어 폐기)
- https://web.dev/blog/inp-cwv-march-12 (INP 2024-03-12) · https://web.dev/articles/vitals
- https://developers.google.com/search/updates (FAQ 2026-05/06 제거, 6종 2025-09-09, Practice Problem 2026-01)
- https://developers.google.com/search/blog/2023/08/howto-faq-changes
- https://developers.google.com/search/blog/2024/10/sitelinks-search-box (2024-11-21)
- https://www.etavrian.com/news/google-deprecates-practice-problem-structured-data (Book Actions 폐지 철회)
- https://searchengineland.com/google-drops-reporting-on-several-structured-data-types-461744
- https://developers.google.com/search/docs/fundamentals/ai-optimization-guide (AIO=특수마크업 불필요)
- https://developers.google.com/search/blog/2026/06/gen-ai-performance-reports (2026-06-03)
- https://developers.google.com/search/docs/appearance/structured-data/job-posting · `/article` · `/organization`
- https://developers.google.com/search/docs/specialty/international/localized-versions (hreflang)
- https://searchengineland.com/google-ai-overviews-surge-pullback-data-466314 (AIO late-2025 ~16%)

**Bing / IndexNow / Yahoo**
- https://www.indexnow.org/documentation · `/searchengines.json`
- https://blogs.bing.com/webmaster/February-2026/Introducing-AI-Performance-in-Bing-Webmaster-Tools-Public-Preview (2026-02-10)
- https://blogs.bing.com/webmaster/July-2025/Keeping-Content-Discoverable-with-Sitemaps-in-AI-Powered-Search
- https://www.bing.com/webmasters/help/marking-up-your-site-with-structured-data-3a93e731
- https://wordpress.org/plugins/indexnow/ (Bing 공식)
- https://en.wikipedia.org/wiki/IndexNow (참여 엔진 + Yep) · https://en.wikipedia.org/wiki/Yahoo_Search

**Daum / Kakao**
- https://register.search.daum.net/ · https://www.kakaocorp.com/page/detail/8807 (웹마스터도구)
- https://cs.daum.net/faq/15/4118.html (사이트맵/Daumoa) · `/faq/261/15308.html` (네이트 공유)
- https://www.fnnews.com/news/202605070819041556 (업스테이지 인수 2026-05)
- https://aboda.kr/entry/업스테이지-다음-인수 (Solar LLM)

**GEO / 봇 / 측정**
- https://developers.openai.com/api/docs/bots (OAI-SearchBot·GPTBot·ChatGPT-User)
- https://support.claude.com/en/articles/8896518 (Claude-User robots 준수·bots.json)
- https://platform.claude.com/docs/en/agents-and-tools/tool-use/web-search-tool (cited_text 150자)
- https://docs.perplexity.ai/guides/bots (Perplexity-User robots 무시)
- https://ai.google.dev/gemini-api/docs/google-search (Google-Extended=Gemini 그라운딩)
- https://arxiv.org/pdf/2311.09735 (Princeton GEO, KDD 2024)
- https://www.searchenginejournal.com/google-says-llms-txt-is-purely-speculative-for-now/577576/
- https://llmstxt.org/ · https://blog.cloudflare.com/introducing-pay-per-crawl/ (2025-07)

**공통 기술 토대**
- https://www.sitemaps.org/protocol.html (50,000/50MB) · https://ogp.me/ (OG4·1200×630)
- https://developers.google.com/crawling/docs/robots-txt/robots-txt-spec (user-agent/allow/disallow/sitemap, 500KiB)
- https://developers.google.com/search/docs/crawling-indexing/consolidate-duplicate-urls (canonical 강도)
- https://developers.google.com/search/docs/crawling-indexing/301-redirects (301/308 강 vs 302/307 약)
