---
last_verified: 2026-07
reverify_by: 2026-Q4
ssot_owner: "robots.txt 템플릿 · 검색/AI 봇 목록 (§A)"
---

# robots.txt / sitemap / RSS / IndexNow 템플릿

> Generated 2026-07-01 · Owner: Henry Oh (KORVIA) · ⚠ 콘솔/API 변경은 사용 전 재확인
>
> **SSOT**: 이 문서 **§A가 robots.txt 템플릿·검색/AI 봇 목록의 유일 원본(owner)**이다. 다른 문서(MULTI_ENGINE·CONTENT_AUTORULES·GEO_AEO 등)는 값 재기입 대신 여기로 링크한다. (IndexNow 참여 엔진 목록의 owner는 [`BING_YAHOO_INDEXNOW.md`](./BING_YAHOO_INDEXNOW.md) — 이 문서는 [V8] 값+링크만 보유.)
>
> 이 문서는 `korvia-seo-skill/docs/`의 **기계적 기술 토대 + 바로 쓰는 템플릿** 전담이다.
> 콘텐츠/AEO/GEO **전략**(왜/무엇/Princeton 근거·플랫폼별 인용 성향)은 [`AEO_GEO_PLAYBOOK_2026.md`](./AEO_GEO_PLAYBOOK_2026.md),
> 페이지 위생 체크리스트는 [`WEB_PLAYBOOK.md`](./WEB_PLAYBOOK.md)·[`NEW_PAGE_CHECKLIST.md`](./NEW_PAGE_CHECKLIST.md),
> CWV 해석·CLS 원인은 [`CRUX_LIGHTHOUSE_INTERPRETATION.md`](./CRUX_LIGHTHOUSE_INTERPRETATION.md)·[`CLS_SEVEN_SOURCES.md`](./CLS_SEVEN_SOURCES.md)를 참조한다.
> 여기서는 그 문서들과 **중복하지 않고**, robots.txt·sitemap·RSS·IndexNow의 **복사-붙여넣기 산출물과 엔진별 제출 절차**만 담는다.

---

## 0. 자동 적용 규칙 (Auto-apply)

작성자/에이전트가 기계적으로 따를 항목. (근거·정정은 본문 각 섹션 참조.)

1. **검색/인용봇은 절대 Disallow 금지** — `Googlebot`, `Bingbot`, `Yeti`(Naver), `Daumoa`(Daum), `OAI-SearchBot`, `PerplexityBot`, `Claude-SearchBot`, `Applebot`. 이들 차단 = 검색·AI 답변 노출에서 소멸.
2. **학습봇 차단은 사업 판단으로만·경로 한정** — `GPTBot`, `ClaudeBot`, `Google-Extended`, `Applebot-Extended`, `CCBot`, `Meta-ExternalAgent`. KORVIA 기본 = **허용**(브랜드가 모델 지식에 편입 = 장기 GEO 이득, 차단해도 검색·인용 노출엔 무영향). 유료/독점 경로(`/pricing-internal/` 등)만 예외 Disallow.
3. **robots.txt로 못 막는 fetcher** = `Perplexity-User`, `ChatGPT-User`(2025-12 정책변경 이후 robots 미준수). **`Claude-User`는 robots 준수**하므로 막고 싶으면 robots로 막힌다 → §A.2 정정 참조. 진짜 차단이 필요하면 Cloudflare WAF/AI Crawl Control(§A.5).
4. **Bytespider**는 robots를 무시하는 사례가 문서화됨 → robots에 Disallow 명시 + **Cloudflare WAF 병행**.
5. **모든 robots.txt에 `Sitemap:` 절대 URL 지시**를 넣는다. Naver도 robots의 Sitemap 지시를 참고.
6. **noindex는 크롤 허용 상태에서만** — `Disallow` + `noindex` 동시 금지(크롤이 막히면 noindex를 못 읽음, 안티패턴).
7. **사이트맵 한도** = 파일당 최대 50,000 URL / 50MB(비압축). 초과 시 **sitemap index**로 분할. index 중첩 금지.
8. **Naver는 사이트당 사이트맵 1개만 읽음** → 항상 **단일 최상위 index URL**을 유지해 그것 하나를 Search Advisor에 제출.
9. **`<lastmod>`는 실제 콘텐츠 변경 시각으로만** 갱신. `priority`/`changefreq`는 Google이 무시하므로 신뢰 신호로 쓰지 말 것.
10. **IndexNow 핑은 변경분만**(신규/수정/삭제 URL). 전체 사이트맵 재핑 금지(429·스팸). 요청당 최대 10,000 URL.
11. **IndexNow 키 = a-z/A-Z/0-9/대시 8~128자**(⚠ "16진수만" 아님, §F.1). `{key}.txt`를 루트에 UTF-8로.
12. **Google·Daum은 IndexNow 미참여** → Google은 sitemap + GSC로만 처리. IndexNow 참여 엔진 = **Bing·Naver·Yandex·Seznam.cz·Yep·Amazon**([V8], indexnow.org/faq 확인 2026-07-02; 한 엔진에 핑하면 참여 엔진 전체로 자동 전파). 참여 목록의 SSOT는 [`BING_YAHOO_INDEXNOW.md`](./BING_YAHOO_INDEXNOW.md).
13. **한국인 청중 확실 커버**는 IndexNow 제네릭 핑 + **Naver 직접 엔드포인트** 병행(§F.3, 제네릭→Naver 실전파는 공식 미확인).
14. **CWV good** = LCP ≤2.5s / INP ≤200ms / CLS ≤0.1(필드 75퍼센타일). ⚠ "LCP 2.0s 강화"·"Engagement Reliability"는 **비공식 미확인 → 채택 금지**(§H).
15. **YMYL(Korvia 비자/취업, Kimchi 통신/금융)**은 **정확성 게이트 통과 후에만** IndexNow 핑 — 부정확한 최신 버전이 AI(Copilot/Yahoo/Naver)에 빠르게 인용되는 것을 막는다.

---

## 1. 4개 프로젝트 매핑

| 사이트 | 도메인 | 스택 | robots/sitemap 산출 | IndexNow | 주요 청중·특이 |
|---|---|---|---|---|---|
| **Passome** | `www.passome.com` | Next.js/Vercel | `app/robots.ts`+`app/sitemap.ts`(§D) | API route(§D.3) | AX/미술심리. 비-YMYL |
| **Korvia** | `www.korvia.com` | Next.js/Vercel | `app/robots.ts`+`app/sitemap.ts` (기존 운영) | API route | **YMYL 비자/취업**·한영 이중언어·Cloudflare 뒤 |
| **Kimchi Mobile** | `www.kimchimobile.com` | WordPress | Yoast/RankMath(§E) | 공식 IndexNow 플러그인(§E) | **YMYL 통신/금융**·외국인 |
| **ACT Art Center** | `actartcenter.com` | Next.js | `app/robots.ts`+`app/sitemap.ts` | API route | 미술교육. 비-YMYL·로컬 |

> ⚠ `actartcenter.com`은 **apex(비-www)** 도메인일 수 있음. robots.txt·sitemap·canonical·hreflang을 **한 정규 호스트**(www 유무)로 통일하고, 다른 쪽은 301로 1홉 리다이렉트. 배포 전 실제 정규 호스트를 확인해 아래 템플릿의 `www.` 부분을 실호스트로 치환하라.

---

## A. robots.txt 템플릿

### A.1 검색봇 허용 (Googlebot / Bingbot / Yeti / Daumoa)

4개 엔진의 **색인 크롤러는 전부 명시 허용**한다. 아래는 각 봇을 명시적으로 보여주기 위한 예시 블록이다.

> ⚠ **이 블록만 떼어 쓰지 말 것**: 이렇게 이름별 그룹을 두고 뒤에 `User-agent: * / Disallow: …`를 붙이면, 이름 그룹들은 **자기 그룹만 읽고 `*`의 Disallow를 상속하지 않는다**(RFC 9309 — 봇은 매칭되는 그룹 하나만 따름) → 차단하려던 운영 경로가 검색봇에 그대로 노출된다. **완성 robots.txt는 §A.3**(전면 허용 봇을 `User-agent: *` 하나로 흡수)를 쓰라.

```txt
# ── 검색 색인 크롤러 (전면 허용) — ⚠ 예시. 완성본은 §A.3 ──
User-agent: Googlebot
Allow: /

User-agent: Bingbot
Allow: /

User-agent: Yeti            # Naver 크롤러 (UA: Mozilla/5.0 (compatible; Yeti/1.1; +https://naver.me/spd))
Allow: /

User-agent: Daumoa          # Daum/Kakao→(2026 Upstage 소유) 크롤러
Allow: /

User-agent: Applebot        # Apple 검색(Siri/Spotlight)
Allow: /
```

- **Yeti**: 매칭은 `Yeti` 토큰 기준. UA 도메인은 `https://naver.me/spd`(⚠ `http://` 표기는 구식). 크롬 렌더러 토큰을 포함한 모바일 변형(`...KHTML, like Gecko; compatible; Yeti/1.1; +https://naver.me/spd) Chrome/...Safari/537.36`)도 실재 — 토큰으로 매칭하면 모두 커버.
- **Daumoa**: `Daumoa`로 지정(로그엔 `Daum` 토큰으로도 찍힘). ⚠ Daum은 2025-12 카카오에서 분리(AXZ)→**2026-05 업스테이지(Upstage) 인수** 소유 이전 중이라 크롤러 IP 대역이 바뀔 수 있음 → **UA 토큰 기준으로 허용**하고 IP 하드코딩 회피. ([근거](https://www.fnnews.com/news/202605070819041556))

### A.2 AI봇 정책 표 — 색인 노출(허용) vs 학습 차단(선택)

**3계층 구분이 전략의 근간**: ① 학습봇(차단해도 검색·인용 노출 무영향) ② 검색/인용봇(차단 시 AI 답변 인용에서 소멸 → 항상 허용) ③ 실시간 사용자 fetcher(일부는 robots 무시).

| 봇 (user-agent) | 벤더 | 유형 | robots 준수 | KORVIA 권고 | 근거·정정 |
|---|---|---|---|---|---|
| **Googlebot** | Google | 검색+AI Overviews 원천 | 예 | **허용(필수)** | 차단 = 구글·AI Overviews 소멸 |
| **Google-Extended** | Google | 학습/그라운딩(Gemini 앱·Vertex AI) | 예 | 허용(기본) | ⚠ **[V3] Search·랭킹·AI Overviews·AI Mode 노출과 무관**(공식, ai-features 확인 2026-07). AIO·AI Mode 노출은 **일반 검색과 동일한 로봇 제어**(`nosnippet`/`data-nosnippet`/`max-snippet`/`noindex`)로만 관리 — Google-Extended는 **Gemini 앱·Vertex AI 등 Google의 다른 시스템** 학습·그라운딩 제한용이라 이걸로 검색 노출을 못 끈다. 일부 3rd-party는 반대 주장 → [ai-features(공식)](https://developers.google.com/search/docs/appearance/ai-features)·[common-crawlers](https://developers.google.com/search/docs/crawling-indexing/google-common-crawlers) 근거 채택 |
| **Bingbot** | Microsoft | 검색+Copilot | 예 | **허용(필수)** | Yahoo·DuckDuckGo·Copilot 백엔드 |
| **OAI-SearchBot** | OpenAI | 검색/인용 | 예 | **허용(필수)** | ChatGPT 검색 인용 자격 |
| **GPTBot** | OpenAI | 학습 | 예 | 허용(기본)/유료경로 차단 | 검색봇과 분리 토큰 → 차단해도 인용 유지 |
| **ChatGPT-User** | OpenAI | 사용자 fetch | ⚠ **미준수**(2025-12 정책변경) | robots로 못 막음 | 차단 필요 시 WAF. 과거엔 준수했음(moving target) |
| **OAI-AdsBot** | OpenAI | 광고 랜딩 검증 | 예 | 허용 | 광고 운용 시 |
| **ClaudeBot** | Anthropic | 학습 | 예(+Crawl-delay) | 허용(기본)/유료경로 차단 | 독립 토큰 |
| **Claude-SearchBot** | Anthropic | 검색/인용 | 예 | **허용(필수)** | Claude 검색 품질·인용 |
| **Claude-User** | Anthropic | 사용자 fetch | ✅ **준수** | 막으려면 robots로 막힌다 | ⚠ **정정**: ChatGPT-User/Perplexity-User와 달리 robots.txt 존중. `User-agent: Claude-User / Disallow: /`로 차단 가능 ([공식](https://support.claude.com/en/articles/8896518-does-anthropic-crawl-data-from-the-web-and-how-can-site-owners-block-the-crawler)) |
| **PerplexityBot** | Perplexity | 검색/인용 | 예 | **허용(필수)** | Perplexity 인용 |
| **Perplexity-User** | Perplexity | 사용자 fetch | ⚠ 미준수 | robots로 못 막음 → WAF | [공식](https://docs.perplexity.ai/guides/bots) "generally ignores robots.txt" |
| **Applebot-Extended** | Apple | 학습 | 예 | 허용(기본) | 학습만 차단 가능, 검색 무영향 |
| **CCBot** | Common Crawl | 학습 데이터셋(다수 LLM 원천) | 예 | 허용(기본) | 차단 시 다수 모델 학습서 제외 |
| **Amazonbot** | Amazon | 검색/Alexa | 예 | 허용 | |
| **Meta-ExternalAgent** | Meta | 학습/제품 | 예 | 허용(기본) | |
| **Bytespider** | ByteDance | 학습 | ❌ 무시 사례 | **Disallow + WAF** | robots만으론 못 막음 |

**핵심 원칙**: robots.txt는 "학습 차단" 신호일 뿐 **"인덱스/모델 회수"가 아니다** — 이미 학습·색인된 내용은 못 지운다.

### A.3 완성 robots.txt — 사이트 유형별 2버전 (복사-붙여넣기)

**버전 (i) — 브랜드 노출 극대화(전면 허용).** Passome·ACT 권장. AI 학습 편입 = 장기 브랜드 GEO 이득.

```txt
# robots.txt — www.passome.com / actartcenter.com (브랜드 노출 극대화)
#
# ⚠ robots.txt 그룹 규칙(RFC 9309): 크롤러는 자신과 매칭되는 "가장 구체적인 그룹 하나"만
#    따르고 User-agent: * 를 상속하지 않는다. 검색/인용봇마다 별도 'Allow: /' 그룹을 주면
#    * 의 Disallow(/admin/·/api/…)를 못 받아 운영 경로가 그대로 크롤된다. Allow 는 기본값이라
#    "전면 허용" 봇은 별도 그룹을 두지 말고 * 로 흡수한다.
#    전면 허용 대상(문서용 목록·차단 아님): Googlebot Bingbot Yeti Daumoa Applebot
#                                       OAI-SearchBot PerplexityBot Claude-SearchBot

# 전 봇 공통 — 검색/인용봇 포함 전면 허용 + 운영 경로만 차단
User-agent: *
Allow: /
Disallow: /admin/
Disallow: /api/
Disallow: /*?*sessionid=
# ⚠ /_next/ 는 Disallow 금지 — /_next/static 의 JS/CSS 차단 시 크롤러가 렌더 못 함(§A.4)

# 비준수 공격 크롤러 (별도 그룹 → WAF 병행)
User-agent: Bytespider
Disallow: /

Sitemap: https://www.passome.com/sitemap.xml
```

**버전 (ii) — YMYL 학습 선택 차단.** Korvia·Kimchi 권장. 인용봇 전면 허용 + 유료/독점 경로만 학습봇 차단.

```txt
# robots.txt — www.korvia.com / www.kimchimobile.com (YMYL, 인용 개방·유료경로 학습 차단)
#
# ⚠ RFC 9309: 봇은 자기와 매칭되는 그룹 하나만 따르고 User-agent: * 를 상속하지 않는다.
#    → 경로 제한이 필요 없는 검색/인용봇은 별도 그룹을 두지 말고 * 로 흡수하고,
#      별도 그룹이 꼭 필요한 봇(학습봇·Bytespider)에는 공용 운영경로 Disallow를 반드시 반복한다.
#    전면 허용 대상(문서용 목록·차단 아님): Googlebot Bingbot Yeti Daumoa Applebot
#                                       OAI-SearchBot PerplexityBot Claude-SearchBot

# 검색·인용봇 포함 전 봇 전면 허용 + 운영 경로만 차단
User-agent: *
Allow: /
Disallow: /admin/
Disallow: /api/
Disallow: /wp-admin/          # (Kimchi WordPress만)
Allow: /wp-admin/admin-ajax.php
# ⚠ /_next/ 는 Disallow 금지 — /_next/static 의 JS/CSS 차단 시 렌더 깨짐(§A.4)

# AI 학습봇 — 유료/독점 경로 차단. 별도 그룹은 * 를 상속하지 않으므로 공용 Disallow를 여기 반복.
# (여러 User-agent 줄 = 이 봇들에 공통 적용되는 한 그룹)
User-agent: GPTBot
User-agent: ClaudeBot
User-agent: Google-Extended
User-agent: Applebot-Extended
User-agent: CCBot
Allow: /
Disallow: /pricing-internal/
Disallow: /admin/
Disallow: /api/

# robots 무시 fetcher는 여기서 못 막음(→ Cloudflare WAF): Perplexity-User, ChatGPT-User
# Claude-User는 준수하므로 필요 시(별도 그룹이라 공용 Disallow도 반복해야 함):
# User-agent: Claude-User
# Disallow: /pricing-internal/
# Disallow: /admin/
# Disallow: /api/

# 비준수 공격 크롤러 (WAF 병행)
User-agent: Bytespider
Disallow: /

Sitemap: https://www.korvia.com/sitemap.xml
```

> ⚠ **Kimchi(WordPress) 감사 필수**: WP 보안 플러그인이 AI봇/검색봇을 **광범위 과차단**하고 있지 않은지 배포 전 확인(§E). `User-agent: *` 아래 광범위 `Disallow`가 인용봇까지 막으면 AI 노출이 죽는다.

### A.4 noindex 안티패턴 (색인 제외는 이렇게)

- `robots.txt Disallow`는 **크롤 차단**이지 색인 제외가 아니다. 색인에서 빼려면 페이지에 `<meta name="robots" content="noindex">` 또는 응답 헤더 `X-Robots-Tag: noindex`.
- **함정**: `Disallow` + `noindex` 동시 지정 = 크롤러가 페이지를 못 읽어 `noindex`를 못 봄 → 오히려 색인 잔존. **noindex 대상은 크롤을 허용**하라.
- **함정: 렌더 자산 차단 금지** — `Disallow: /_next/`(또는 CSS/JS 번들)로 Next.js 빌드 자산을 통째로 막으면 Googlebot·AI 크롤러가 페이지를 렌더할 리소스를 못 가져와 본문·레이아웃이 깨진 채 색인/평가된다(Google Search Central "CSS·JS를 차단하지 말라"). 정작 민감한 것은 데이터 라우트(`/api/`)이므로 그것만 좁게 차단하고 **`/_next/`(특히 `/_next/static`)는 열어둔다**. 크롤 예산이 정말 걱정되면 `/_next/image`(이미지 옵티마이저)만 선택적으로 Disallow.

### A.5 Cloudflare AI Crawl Control 주의 (Korvia·Kimchi 해당 시)

- 2025-07부터 Cloudflare 신규 도메인은 AI 크롤러 **기본 차단** + 봇별 Allow/Charge/Block(Pay-Per-Crawl). robots로 못 막는 `Perplexity-User`/`ChatGPT-User`를 실제로 막는 유일한 레버.
- ⚠ **KORVIA 목표는 노출** → CF WAF/봇 규칙이 **인용봇(OAI-SearchBot·PerplexityBot·Claude-SearchBot)과 Daumoa/Yeti까지 과차단**하지 않는지 감사. 차단은 **UA 토큰 기준**(IP 하드코딩 금지 — Daum 소유 이전으로 IP 변동 가능). Vercel(Passome/ACT)은 CF AI Crawl Control 미적용(별도 설정 시 예외).

---

## B. sitemap 템플릿

### B.1 하드 한도 (Google·Bing·sitemaps.org 공통)

| 항목 | 한도 |
|---|---|
| 파일당 URL | **최대 50,000** |
| 파일당 비압축 크기 | **최대 50MB (52,428,800 bytes)**, `.xml.gz` 허용 |
| index가 참조하는 자식 사이트맵 | 최대 50,000 |
| index 중첩(index가 index 참조) | **비권장/비지원** |

> ⚠ Naver의 바이트/URL 하드 한도는 공식 미공개 — 위 값은 sitemaps.org 표준이며 Naver도 이 표준을 참조. **확정 사실은 "Naver=사이트당 XML 1개"뿐**이므로, 여러 사이트맵이 필요하면 **반드시 sitemap index 1개로 통합**해 Naver에 그 하나만 제출.

### B.2 sitemap index (모든 사이트 최상위 = 이것 하나 유지)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<sitemapindex xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <sitemap>
    <loc>https://www.korvia.com/sitemaps/pages.xml</loc>
    <lastmod>2026-07-01T09:00:00+09:00</lastmod>
  </sitemap>
  <sitemap>
    <loc>https://www.korvia.com/sitemaps/blog.xml</loc>
    <lastmod>2026-07-01T09:00:00+09:00</lastmod>
  </sitemap>
  <sitemap>
    <loc>https://www.korvia.com/sitemaps/images.xml</loc>
    <lastmod>2026-06-28T12:00:00+09:00</lastmod>
  </sitemap>
</sitemapindex>
```

### B.3 표준 urlset + `<lastmod>` + hreflang(다국어 사이트맵)

`<xhtml:link>`로 언어 대체본을 사이트맵에 선언 = 페이지 `<head>` hreflang의 대안(대량 페이지에 유지보수 유리, §I).

```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9"
        xmlns:xhtml="http://www.w3.org/1999/xhtml">
  <url>
    <loc>https://www.korvia.com/ko/epik-guide</loc>
    <lastmod>2026-06-30</lastmod>
    <xhtml:link rel="alternate" hreflang="ko" href="https://www.korvia.com/ko/epik-guide"/>
    <xhtml:link rel="alternate" hreflang="en" href="https://www.korvia.com/en/epik-guide"/>
    <xhtml:link rel="alternate" hreflang="x-default" href="https://www.korvia.com/en/epik-guide"/>
  </url>
  <url>
    <loc>https://www.korvia.com/en/epik-guide</loc>
    <lastmod>2026-06-30</lastmod>
    <xhtml:link rel="alternate" hreflang="ko" href="https://www.korvia.com/ko/epik-guide"/>
    <xhtml:link rel="alternate" hreflang="en" href="https://www.korvia.com/en/epik-guide"/>
    <xhtml:link rel="alternate" hreflang="x-default" href="https://www.korvia.com/en/epik-guide"/>
  </url>
</urlset>
```

- `<lastmod>`: `YYYY-MM-DD` 또는 `2026-06-30T09:00:00+09:00`(W3C Datetime). **실제 변경 시각만** — 전부 today면 Google이 무시.
- `<priority>`/`<changefreq>`: Google 무시. 넣어도 무해하나 신뢰 신호 아님(내부 크롤 우선순위 힌트용으로만; WEB_PLAYBOOK §1.2의 카테고리 priority는 자체 운영 관례).
- `<loc>`: 절대 URL, XML 이스케이프(`&`→`&amp;`).

### B.4 이미지 sitemap (ACT 작품·Kimchi 상품컷·Passome 히어로)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9"
        xmlns:image="http://www.google.com/schemas/sitemap-image/1.1">
  <url>
    <loc>https://actartcenter.com/gallery/spring-2026</loc>
    <image:image>
      <image:loc>https://cdn.actartcenter.com/works/spring-2026-01.webp</image:loc>
    </image:image>
    <image:image>
      <image:loc>https://cdn.actartcenter.com/works/spring-2026-02.webp</image:loc>
    </image:image>
  </url>
</urlset>
```
> `image:caption`/`image:title` 태그는 2022년 이후 Google이 무시 → `<image:loc>`만 유효.

### B.5 뉴스 sitemap — ⚠ 4개 사이트 불필요

```xml
<!-- Google News 승인 매체 전용 · 48시간 이내 기사만 · KORVIA 4사이트는 뉴스 매체 아님 → 사용하지 말 것 -->
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9"
        xmlns:news="http://www.google.com/schemas/sitemap-news/0.9">
  <url>
    <loc>https://example.com/article</loc>
    <news:news>
      <news:publication><news:name>Publisher</news:name><news:language>ko</news:language></news:publication>
      <news:publication_date>2026-07-01T08:00:00+09:00</news:publication_date>
      <news:title>기사 제목</news:title>
    </news:news>
  </url>
</urlset>
```
블로그/공지의 신선도는 뉴스 사이트맵이 아니라 **RSS(§C)**로 보완한다.

---

## C. RSS / Atom 템플릿

Google/Bing/Naver/Daum 모두 RSS 2.0·Atom을 **최신 콘텐츠 갱신 알림**으로 수용(전체 구조 스냅샷은 XML sitemap). **자주 갱신되는 섹션(블로그/공지)만** RSS로 보완.

**RSS 2.0** (Kimchi WP는 기본 `/feed/` 존재 → 그대로 제출):

```xml
<?xml version="1.0" encoding="UTF-8"?>
<rss version="2.0">
  <channel>
    <title>KORVIA Blog</title>
    <link>https://www.korvia.com/blog</link>
    <description>EPIK·비자·한국 취업 가이드</description>
    <language>ko-kr</language>
    <lastBuildDate>Tue, 01 Jul 2026 09:00:00 +0900</lastBuildDate>
    <item>
      <title>2026 EPIK 지원 완전 가이드</title>
      <link>https://www.korvia.com/ko/blog/epik-2026-guide</link>
      <guid isPermaLink="true">https://www.korvia.com/ko/blog/epik-2026-guide</guid>
      <pubDate>Mon, 30 Jun 2026 10:00:00 +0900</pubDate>
      <description><![CDATA[본문 전문 또는 요약. Naver는 <description>에 글 전문(또는 일부) 포함을 권장.]]></description>
    </item>
  </channel>
</rss>
```

- **Naver 권장**: `<item>`의 `<description>`에 **글 전문**을 담으면 신규/최신 반영 촉진.
- 네이버 블로그를 운영하면 RSS 자동 제출 → 외부 홈페이지만 수동 제출.
- Atom 1.0(`<feed>`/`<entry>`)도 IndexNow/Daum Seed URL·검색엔진이 수용. Next.js는 아래 D.4에 route로.

---

## D. Next.js — `app/robots.ts` + `app/sitemap.ts` + IndexNow 코드

> Korvia는 이미 `MetadataRoute` 방식 사용(WEB_PLAYBOOK §11.4). **함정: 환경변수는 항상 `.trim()`** — trailing newline이 `Sitemap: https://...\n/path`로 깨진 실제 버그가 있었다(WEB_PLAYBOOK §1.1). `sitemap` 필드는 반드시 절대 URL 단일 문자열.

### D.1 `app/robots.ts`

```ts
// app/robots.ts
import type { MetadataRoute } from 'next';

const BASE = (process.env.NEXT_PUBLIC_SITE_URL ?? 'https://www.korvia.com').trim();

// ⚠ MetadataRoute.Robots 의 rules[] 는 각 객체가 별도 robots 그룹으로 출력된다.
//    robots.txt 그룹 규칙(RFC 9309): 봇은 자기와 매칭되는 그룹 하나만 따르고 { userAgent:'*' } 를
//    상속하지 않는다 → 검색/인용봇에 별도 rule을 주면 공용 Disallow를 못 받아 운영 경로가 노출된다.
//    ⇒ 전면 허용 봇은 rule을 따로 두지 말고 * 로 흡수하고, 경로 제한이 필요한 봇(학습봇·Bytespider)에만
//      별도 rule을 두되 공용 Disallow를 명시 반복한다.
//    ⚠ /_next/ 는 disallow 금지 — /_next/static 의 JS/CSS 차단 시 렌더 깨짐(§A.4).
const OPS_DISALLOW = ['/admin/', '/api/', '/*?*sessionid=']; // /_next/ 는 넣지 말 것

export default function robots(): MetadataRoute.Robots {
  return {
    rules: [
      // 검색·인용봇 포함 전면 허용 봇(Googlebot·Bingbot·Yeti·Daumoa·Applebot·
      //   OAI-SearchBot·PerplexityBot·Claude-SearchBot)은 * 로 흡수 + 운영 경로만 차단
      { userAgent: '*', allow: '/', disallow: OPS_DISALLOW },
      // AI 학습 봇 — 유료/독점 경로 차단 + 공용 Disallow 반복(그룹은 * 를 상속 안 함)
      {
        userAgent: ['GPTBot', 'ClaudeBot', 'Google-Extended', 'Applebot-Extended', 'CCBot'],
        allow: '/',
        disallow: ['/pricing-internal/', ...OPS_DISALLOW],
      },
      // 비준수 크롤러 (WAF 병행)
      { userAgent: 'Bytespider', disallow: '/' },
    ],
    sitemap: `${BASE}/sitemap.xml`,      // 절대 URL 단일 문자열
    host: BASE,
  };
}
```

### D.2 `app/sitemap.ts` (+ 5만 초과 시 index 분할)

```ts
// app/sitemap.ts
import type { MetadataRoute } from 'next';
import { getAllPages } from '@/lib/content'; // filesystem/DB에서 enumerate (하드코딩 drift 금지)

const BASE = (process.env.NEXT_PUBLIC_SITE_URL ?? 'https://www.korvia.com').trim();

export default async function sitemap(): Promise<MetadataRoute.Sitemap> {
  const pages = await getAllPages(); // [{ path, updatedAt, locales? }]
  return pages.map((p) => ({
    url: `${BASE}${p.path}`,
    lastModified: p.updatedAt,              // 실제 변경 시각만
    changeFrequency: 'weekly',              // Google 무시 (내부 힌트용)
    priority: 0.8,
    alternates: p.locales
      ? { languages: { ko: `${BASE}/ko${p.slug}`, en: `${BASE}/en${p.slug}` } } // hreflang을 sitemap에 주입
      : undefined,
  }));
}
```

**5만 URL 초과 시** — Next는 `generateSitemaps()`로 분할(각 청크가 자식 사이트맵, 최상위 index는 Next가 관리). Naver 대비 **단일 최상위 index URL을 항상 유지**:

```ts
// app/sitemap.ts (대형)
export async function generateSitemaps() {
  const total = await countPages();
  return Array.from({ length: Math.ceil(total / 45000) }, (_, id) => ({ id }));
}
export default async function sitemap({ id }: { id: number }): Promise<MetadataRoute.Sitemap> {
  const pages = await getPages({ offset: id * 45000, limit: 45000 });
  return pages.map((p) => ({ url: `${BASE}${p.path}`, lastModified: p.updatedAt }));
}
```

### D.3 IndexNow 자동 핑 route (발행 훅에서 호출)

```ts
// app/api/indexnow/route.ts  — 서버 전용, 키는 env로만(클라 노출 금지)
const BASE = (process.env.NEXT_PUBLIC_SITE_URL ?? 'https://www.korvia.com').trim();
const KEY = process.env.INDEXNOW_KEY!; // {KEY}.txt 를 public/에 호스팅 (§F)

export async function pingIndexNow(urls: string[]): Promise<boolean> {
  const host = new URL(BASE).host;
  const body = { host, key: KEY, keyLocation: `${BASE}/${KEY}.txt`, urlList: urls }; // 변경분만, ≤10,000
  // 1) 제네릭(Bing·Yandex·Seznam.cz·Yep·Amazon 전파 — [V8] 참여 엔진, Naver는 아래 별도 핑)
  const generic = await fetch('https://api.indexnow.org/indexnow', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json; charset=utf-8' },
    body: JSON.stringify(body),
  });
  // 2) ⚠ Naver 직접 핑 병행(제네릭→Naver 실전파는 공식 미확인, §F.3)
  await fetch('https://searchadvisor.naver.com/indexnow', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json; charset=utf-8' },
    body: JSON.stringify(body),
  }).catch(() => {});
  return generic.ok; // 200/202 기대. 그 외(400/403/422/429)는 로그로 남기고 성공으로 간주 금지
}
```

트리거: (a) CMS 발행 webhook에서 **신규/수정 URL만** 호출, 또는 (b) `next build` 후 post-build 스크립트가 sitemap diff를 읽어 신규 URL 제출. **YMYL은 정확성 게이트 통과 후에만 핑**(Auto-apply #15).

### D.4 (선택) `app/llms.txt` route — 저비용 부수효과로만

⚠ **랭킹/인용 효과 없음** — 2026-07 현재 어떤 주요 검색·AI 엔진도 llms.txt를 랭킹/인용에 사용하지 않는다(Google Illyes/Mueller 공개 부정, 500M+ 봇방문 中 조준 ~408건). 실사용 주체는 IDE/코딩 에이전트(Cursor·Claude Code)뿐 → **마케팅 사이트엔 사실상 불필요**. 만들더라도 정적 route로 두고 **자원 투입 금지**, 사이트맵·크롤러빌리티를 대체하지 않는다. llms.txt **전략**은 [`AEO_GEO_PLAYBOOK_2026.md` §4-(2)](./AEO_GEO_PLAYBOOK_2026.md)·[`WEB_PLAYBOOK.md` §1.3](./WEB_PLAYBOOK.md) 참조(이 냉정한 현실을 병기해 해석).

```ts
// app/llms.txt/route.ts  (원하면)
export const dynamic = 'force-static';
export function GET() {
  return new Response(
`# KORVIA
> 외국인 대상 한국 취업/EPIK/원어민 채용 컨설팅.

## Core
- [EPIK Guide](https://www.korvia.com/en/epik-guide): 지원 절차·급여·비자
- [Visa](https://www.korvia.com/en/visa): E-2 등 취업비자

## Optional
- [Blog](https://www.korvia.com/blog)
`, { headers: { 'Content-Type': 'text/plain; charset=utf-8' } });
}
```

---

## E. WordPress (Yoast / Rank Math) 레시피 — Kimchi Mobile

### E.1 사이트맵
- **Rank Math** 또는 **Yoast**가 `sitemap_index.xml`을 자동 생성(예: `https://www.kimchimobile.com/sitemap_index.xml`). → **이 index URL 하나**를 Google·Bing·Naver·Daum에 제출(Naver 단일 사이트맵 규칙 충족).
- 저가치 WP-legacy URL(숫자 슬러그·지난 이벤트)은 사이트맵에서 제외(crawl budget; WEB_PLAYBOOK §1.2 관례).

### E.2 robots.txt
- WP 기본 가상 robots.txt 대신 **정적 `robots.txt`를 루트에 배치**하거나 SEO 플러그인의 robots 편집기로 §A.3 (ii) 버전 적용. 
- ⚠ 보안/캐시 플러그인이 `Yeti`/`Daumoa`/인용봇을 과차단하지 않는지 확인. `Disallow: /wp-admin/` + `Allow: /wp-admin/admin-ajax.php`는 유지.

### E.3 IndexNow (자동 핑)
| 옵션 | 설명 | 권장 |
|---|---|---|
| **공식 IndexNow 플러그인** | Bing Webmaster팀 개발. Plugins→Add New→"IndexNow"→Settings»IndexNow→**"Get Started"** 클릭 시 키 자동 생성·호스팅. 글 생성/수정/삭제 감지 → 백그라운드 자동 제출. **사이트 등록/검증 없이 동작** | ✅ 가장 간단 |
| **Rank Math** | IndexNow 내장, Google(수동)+Bing/IndexNow 자동 핑 | 이미 RankMath면 이걸로 |
| **AIOSEO** | IndexNow(Bing/Yandex)+Google Indexing API | 대안 |
| **Yoast** | ❌ IndexNow 미지원(사이트맵 핑만) | Yoast면 공식 IndexNow 플러그인 병행 |

- ⚠ Kimchi가 현재 어떤 SEO 플러그인을 쓰는지 확인 후 결정. Yoast 단독이면 **공식 IndexNow 플러그인 병행**.

### E.4 소유확인 메타태그
- 네이버 `<meta name="naver-site-verification">`·Bing `<meta name="msvalidate.01">`·Google 인증을 Rank Math/AIOSEO "Webmaster Tools" 필드 또는 테마 `header.php` `<head>`에 넣고 캐시 퍼지. 클라이언트-only 렌더로 빠지지 않게(WP 기본은 서버렌더라 안전).

---

## F. IndexNow 키 파일 + 핑

### F.1 키 생성·호스팅 (한 번만)

- **키 문자셋**: ⚠ a-z / A-Z / 0-9 / 대시(-), **8~128자**. (indexnow.org 문서가 'hexadecimal'이라 부르지만 바로 아래에서 a-z/A-Z/대시 허용을 명시 — **문서 자체가 모순**. "16진수만"으로 단정 금지. UUID hex를 써도 무방하나 필수는 아님.) ([근거](https://www.indexnow.org/documentation))
- **키 파일**: 루트에 `{key}.txt`, UTF-8, **내용 = 키 문자열 그 자체**.
  - 예: `https://www.korvia.com/a1b2c3d4e5f6a7b8.txt` → 파일 내용 `a1b2c3d4e5f6a7b8`
  - Next.js: `public/{key}.txt`. WordPress: 공식 플러그인이 자동 호스팅.
  - 다른 경로에 두려면 POST의 `keyLocation`으로 지정(단 그 경로 하위 URL만 제출 가능).

키 파일 예시(`public/a1b2c3d4e5f6a7b8.txt`):
```txt
a1b2c3d4e5f6a7b8
```

### F.2 핑 방법 + 응답 코드

**단건(GET):**
```
https://api.indexnow.org/indexnow?url=https://www.korvia.com/new-page&key=a1b2c3d4e5f6a7b8
```

**대량(POST JSON, ≤10,000 URL/요청):**
```http
POST /indexnow HTTP/1.1
Host: api.indexnow.org
Content-Type: application/json; charset=utf-8

{
  "host": "www.korvia.com",
  "key": "a1b2c3d4e5f6a7b8",
  "keyLocation": "https://www.korvia.com/a1b2c3d4e5f6a7b8.txt",
  "urlList": ["https://www.korvia.com/a", "https://www.korvia.com/b"]
}
```

| 코드 | 의미 | 조치 |
|---|---|---|
| **200** | 성공 | OK |
| **202** | 접수됨, 키 검증 대기 | 정상 |
| **400** | 형식 오류 | JSON/URL 형식 확인 |
| **403** | 키 무효 | `{key}.txt` 접근/일치 확인 |
| **422** | urlList가 host/key 스키마 불일치 | 모든 URL 도메인이 host와 같은지 |
| **429** | 레이트 리밋 | 배치·백오프 |

**운영**: 변경분만·배치. 셋업 후 `curl`로 단건 GET → 200/202 확인 후 자동화.

### F.3 Naver 직접 엔드포인트 (한국인 청중 확실 커버)

- ⚠ 제네릭 `api.indexnow.org`가 **Naver까지 실전파**하는지는 공식 명시 미확인(프로토콜상 "한 엔진 핑=전체 공유"가 원칙이나 Naver 전파는 문서로 확정 못 함). **확실히 하려면 Naver 직접 엔드포인트 병행**:
  ```
  POST https://searchadvisor.naver.com/indexnow
  ```
  (§D.3 코드가 제네릭 + Naver 둘 다 핑.) Naver는 2023-07부터 IndexNow 참여.

---

## G. 엔진별 사이트맵 제출 엔드포인트·절차

| 엔진 | 콘솔/엔드포인트 | 제출 방법 | 특이·⚠ |
|---|---|---|---|
| **Google** | Search Console → 색인 생성 > **Sitemaps** | sitemap URL 입력 + robots `Sitemap:` 지시 | 다중·index 지원. IndexNow 미참여 → 급한 건 URL 검사>색인 요청(소량) |
| **Bing** | Bing Webmaster Tools → **Sitemaps** | sitemap URL 제출. 등록=GSC Import(동일 Google 계정) 또는 `msvalidate.01` meta | index당 자식 50k. URL 제출/수동=일 10,000(도메인 단위, UTC 자정 리셋). ⚠ 신규 사이트 초기 쿼터 "100"은 비공식 → BWT "Submit URLs"서 실값 확인 |
| **Naver** | Search Advisor → **요청 > 사이트맵 제출** | 도메인 하위 sitemap.xml URL 입력 → 확인 | **XML만·사이트당 1개** → index 1개로 통합. RSS는 **요청 > RSS 제출** 별도. ⚠ 개별 URL 색인='요청 > 웹페이지 수집'(일일 한도 50/100 **공식 미노출** → 콘솔 표기 기준) |
| **Daum/Kakao** | ① `register.search.daum.net` 검색등록(수동, 5일 심사) ② `webmaster.daum.net` 웹마스터도구(PIN 소유확인 → 수집요청 > **수집 Seed URL 등록(사이트맵)**) | Seed URL=사이트맵/RSS/Atom/목록페이지 | 파일당 5만 URL·50MB, **모든 URL 호스트=제출 호스트 동일**(크로스도메인 불가). 수집 수개월 가능. ⚠ 소유·플랫폼 대개편(→Upstage) 중 → URL/정책 분기별 재확인 |
| **IndexNow** (Bing·Naver·Yandex·Seznam.cz·Yep·Amazon) | `api.indexnow.org/indexnow`(제네릭) + `searchadvisor.naver.com/indexnow` | §F POST/GET | **Google·Daum 미참여**([V8], indexnow.org/faq 2026-07-02). 참여 목록 SSOT=[`BING_YAHOO_INDEXNOW.md`](./BING_YAHOO_INDEXNOW.md) |

**Naver 가이드 슬러그 (⚠ 콘솔 개편으로 재편 — 발행 전 404 여부 실검증):**
- 확인된 것: `/guide/faq-start-register`(등록·소유확인), `/guide/request-crawl`(수집요청), `/guide/url-inspection`(URL 검사), `/guide/report-crawl-refine`(수집 현황), `/guide/report-seo`(사이트 최적화), `/guide/structured-data-intro`(+`-faq`/`-howto`/`-video`/`-channel`).
- **Naver 소유확인 유효기간 = 1년**(만료 30일 전 이메일 알림, '소유확인 연장하기'로 갱신). **검증 파일/메타태그를 삭제하지 말고 상시 유지** + 연장 캘린더 태스크. ([근거](https://ppcle.com/faq/naver-verification-renewal))
- **Naver 구조화데이터는 FAQ/Breadcrumb보다 넓다** — `HowTo`·`VideoObject`·`FAQPage`·연관채널(`Person`/`Organization`+`sameAs`) 전용 공식 가이드 존재. ⚠ **엔진 차이**: `FAQPage`·`HowTo`는 **Naver에선 유효, Google에선 폐지/축소(2023)** → 이중 트랙 문서에서 엔진별 유효성 구분(Kimchi eSIM 설치=`HowTo`는 Naver 리치결과 기회, Google 리치결과 미노출).

---

## H. 검증 & 발행 전 게이트 (커맨드 포함)

**성능 목표(필드 75퍼센타일)** — LCP ≤2.5s / INP ≤200ms(2024-03 FID 대체) / CLS ≤0.1. ⚠ **"LCP 2.5s→2.0s 강화"·"Engagement Reliability" 신규지표는 비공식 블로그발 허위**(web.dev 미확인, 2026-07 재확인) → **채택 금지**. 상세 해석은 [`CRUX_LIGHTHOUSE_INTERPRETATION.md`](./CRUX_LIGHTHOUSE_INTERPRETATION.md)·[`CLS_SEVEN_SOURCES.md`](./CLS_SEVEN_SOURCES.md).

발행 전 기계 검증(모든 새 페이지/사이트):

```bash
# 1) canonical + schema 존재
curl -sL https://www.korvia.com/PAGE | grep -oE '<link rel="canonical"[^>]*>|"@type":"[A-Za-z]+"'

# 2) AI 크롤러가 JS 없이 본문을 읽는가 (빈 <div id=root> = AI 검색 투명인간)
curl -sL -A "OAI-SearchBot" https://www.korvia.com/PAGE | grep -c "핵심 본문 문구"

# 3) robots.txt에 Sitemap 지시 존재 + 검색/인용봇 미차단
#    (§A 신형 템플릿은 전면 허용 봇을 User-agent: * 로 흡수 → 봇 이름이 안 보이는 게 정상)
curl -sL https://www.korvia.com/robots.txt | grep -iE "^sitemap:|^user-agent:|^disallow:"
#    → 육안 확인: 'Disallow: /'(전면차단)는 Bytespider 그룹에만 있을 것,
#      Sitemap: 절대URL 존재, 검색/인용봇(Googlebot·Yeti·OAI-SearchBot 등)에 붙은 'Disallow: /' 없음

# 4) sitemap 200 + lastmod 유효
curl -sI https://www.korvia.com/sitemap.xml | head -1

# 5) IndexNow 키 파일 접근 + 단건 핑
curl -sI https://www.korvia.com/a1b2c3d4e5f6a7b8.txt | head -1
curl -s "https://api.indexnow.org/indexnow?url=https://www.korvia.com/PAGE&key=a1b2c3d4e5f6a7b8" -o /dev/null -w "%{http_code}\n"
```

발행 후: IndexNow POST(→ Bing/Naver/Yandex, §D.3·§F) + Google은 sitemap 갱신/ GSC 색인요청. GSC **Gen-AI 성능 리포트**([V1] 2026-06-03 출시, gen-ai-performance-reports)는 AI Overviews·AI Mode 등 Search 및 Discover의 생성형 AI 기능에서 사이트 URL이 노출된 **impressions만** 보여준다(clicks·CTR·평균순위·쿼리 분해 없음; 페이지·국가·기기·날짜별, Search/Discover 별도 리포트). ⚠ 초기 롤아웃이 일부(초기 영국) 속성 한정 → **한국 소재 속성엔 아직 안 뜰 수 있음(안 보여도 정상)**. **[V1] 함께 도입된 AI 기능 옵트아웃 토글**(오버뷰/AI Mode/Discover 내 AI Overviews 미노출)은 **일반 Google 검색 순위·Discover 피드 노출에 영향 없음**(순위 페널티 없음) — 상세는 [`GOOGLE_SEARCH_CONSOLE_INDEXING.md`](./GOOGLE_SEARCH_CONSOLE_INDEXING.md).

---

## I. YMYL · 이중언어 · 외국인 청중 특이사항

- **정규화·hreflang 일치**: 각 언어판은 **자기 언어판을 canonical**로(다른 언어판 canonical 지정 금지 — 치명 오류). hreflang는 언어=ISO 639-1(`ko`/`en`), 지역(선택)=ISO 3166-1 alpha-2(`KR`/`US`/`GB`). **`UK`/`EU`/`UN`·지역단독 금지**. 양방향 return-link·자기참조·절대 URL·`x-default` 필수. `<html lang>`도 페이지 언어와 일치. (구현 예: [`NEW_PAGE_CHECKLIST.md` A-6](./NEW_PAGE_CHECKLIST.md)·[`WEB_PLAYBOOK.md` §3](./WEB_PLAYBOOK.md).)
- **엔진 분리 트랙**: 한국어 콘텐츠 = **Naver(Yeti)** 노출 1차, 영어 콘텐츠 = **Google/Bing** 노출 1차. 같은 sitemap index를 **양쪽에 제출**. 외국인 청중은 영어 쿼리 비중이 높아 영문 GEO(ChatGPT/Perplexity)가 고효율(AEO_GEO_PLAYBOOK §6).
- **OG = 국내외 SNS 동시 커버**: `og:title`/`og:type`/`og:image`/`og:url` 필수 4 + `twitter:card=summary_large_image`. `og:image` **1200×630, ≤5MB**(⚠ 8MB 아님 — ogp.me는 하드 용량 미규정, 안전값 ≤5MB로 통일), 절대 URL, `og:url`=canonical. 다국어는 `og:locale`(`ko_KR`/`en_US`). **이 OG가 카카오톡 공유 미리보기도 커버**(Naver/Kakao는 OG 재사용).
- **YMYL 신뢰신호**: Korvia(비자/취업)·Kimchi(통신/금융)는 사업자정보·연락처·출처·저자·날짜를 본문에 명시(Naver 품질 게이트·AI 인용 정확성). **부정확한 최신 버전을 IndexNow로 빠르게 색인하면 AI가 오답을 인용** → 발행 전 정확성 게이트 통과 후에만 핑.
- **AI 파싱 레버**(진짜 자원 투입처): 공개 콘텐츠는 **SSR/SSG**로 초기 HTML에 본문 존재(다수 AI 크롤러는 JS 미실행), 시맨틱 HTML(질문형 H2/H3·표·리스트), 페이지에 **실제 존재하는 사실만** JSON-LD 마크업(Korvia=FAQPage/HowTo/JobPosting/Article+author, Kimchi=Product/Offer 가격·통화, ACT=LocalBusiness/Course/Event, Passome=Organization/Service/Article).

---

## ⚠ 재확인 필요 (발행 전 flags 모음)

1. **Naver 웹페이지 수집 일일 한도(50/100)** — 공식 미노출, 콘솔 실화면 표기 기준으로만.
2. **Naver 사이트맵 바이트/URL 하드 한도** — 공식 미공개. 확정은 "사이트당 XML 1개"뿐. 구체 수치 단정 금지.
3. **Naver 가이드 슬러그** — 콘솔 개편으로 재편 가능. §G 링크의 404 여부 실검증 후 인용.
4. **Bing 신규 사이트 초기 쿼터** — "100"은 비공식. BWT "Submit URLs"서 실값 확인.
5. **IndexNow 제네릭→Naver 실전파** — 공식 미확인. Naver 직접 엔드포인트 병행(§F.3).
6. **IndexNow 키 문자셋** — a-z/A-Z/0-9/대시(공식 문서가 'hex'라 부르지만 모순). "16진수만" 오해 금지.
7. **Daum 소유·플랫폼** — 2025-12 카카오 분리(AXZ)→2026-05 Upstage 인수, Solar LLM 결합 진행형. `register.search.daum.net`·`webmaster.daum.net` URL/정책/UA/IP 분기별 재확인. '카카오데이터트렌드'는 이미 종료(2025-08-29) → 트렌드는 Naver 데이터랩/구글 트렌드.
8. **Yeti UA** — `https://naver.me/spd`(http 아님), 매칭은 `Yeti` 토큰 기준.
9. **Claude-User** — robots **준수**(막을 수 있음). Perplexity-User·ChatGPT-User만 robots 무시.
10. **ChatGPT-User robots 미준수** — 2025-12 정책변경(moving target). 확인일·출처 병기.
11. **Google-Extended ↔ AI Overviews** — Google 공식=무영향. 3rd-party는 반대 주장(논쟁영역).
12. **CWV 2.0s·Engagement Reliability** — 비공식 허위, 채택 금지. LCP≤2.5s/INP≤200ms/CLS≤0.1 유지.
13. **GSC Gen-AI 리포트** — impressions-only, 초기 롤아웃 일부 속성 한정(한국 속성 미노출 가능, 안 보여도 정상).
14. **단일소스 통계는 예시로만** — llms.txt 408건, 에이전트 +6,900%, 스키마 인용률 +40~60% 등은 방향성만.

---

## Sources

**공식 1차**
- Sitemaps 표준: https://www.sitemaps.org/protocol.html
- Google — Build a sitemap: https://developers.google.com/search/docs/crawling-indexing/sitemaps/build-sitemap
- Google — robots.txt spec: https://developers.google.com/search/docs/crawling-indexing/robots/robots_txt
- Google — Canonicalization: https://developers.google.com/search/docs/crawling-indexing/consolidate-duplicate-urls
- Google — hreflang(localized versions): https://developers.google.com/search/docs/specialty/international/localized-versions
- Google — Redirects: https://developers.google.com/search/docs/crawling-indexing/301-redirects
- Google — Page Experience: https://developers.google.com/search/docs/appearance/page-experience
- Google — Common crawlers(Google-Extended): https://developers.google.com/search/docs/crawling-indexing/google-common-crawlers
- Google — AI features(AIO/AI Mode robots 제어, [V3] 확인 2026-07): https://developers.google.com/search/docs/appearance/ai-features
- Google — Gen-AI performance reports([V1] 2026-06-03): https://developers.google.com/search/blog/2026/06/gen-ai-performance-reports
- web.dev — Web Vitals(LCP/INP/CLS): https://web.dev/articles/vitals
- Open Graph protocol: https://ogp.me/
- IndexNow — Documentation: https://www.indexnow.org/documentation
- IndexNow — FAQ(참여 엔진 [V8], 확인 2026-07-02): https://www.indexnow.org/faq
- IndexNow — Participating engines(JSON): https://www.indexnow.org/searchengines.json
- Bing — IndexNow get started: https://www.bing.com/indexnow/getstarted
- Bing — Structured data: https://www.bing.com/webmasters/help/marking-up-your-site-with-structured-data-3a93e731
- Bing — AI Performance(2026-02-10): https://blogs.bing.com/webmaster/February-2026/Introducing-AI-Performance-in-Bing-Webmaster-Tools-Public-Preview
- Bing — 10,000 URLs/day: https://blogs.bing.com/webmaster/january-2019/bingbot-Series-Get-your-content-indexed-fast-by-now-submitting-up-to-10,000-URLs-per-day-to-Bing
- WordPress 공식 IndexNow 플러그인: https://wordpress.org/plugins/indexnow/
- Naver Search Advisor — 등록/소유확인: https://searchadvisor.naver.com/guide/faq-start-register
- Naver Search Advisor — 수집요청: https://searchadvisor.naver.com/guide/request-crawl
- Naver Search Advisor — 구조화데이터: https://searchadvisor.naver.com/guide/structured-data-intro (+ `-faq`/`-howto`/`-video`/`-channel`)
- Naver — 사이트맵/RSS 제출: https://help.naver.com/service/30010/contents/17629 · https://help.naver.com/service/30010/contents/17628
- Daum — 웹문서/사이트맵 도움말: https://cs.daum.net/faq/15/4118.html
- Daum 검색등록: https://register.search.daum.net/ · 웹마스터도구: https://webmaster.daum.net/
- OpenAI — Bots: https://developers.openai.com/api/docs/bots
- Anthropic — Crawler/robots: https://support.claude.com/en/articles/8896518-does-anthropic-crawl-data-from-the-web-and-how-can-site-owners-block-the-crawler
- Perplexity — Bots: https://docs.perplexity.ai/guides/bots
- Cloudflare — Pay per crawl / AI Crawl Control: https://blog.cloudflare.com/introducing-pay-per-crawl/ · https://developers.cloudflare.com/ai-crawl-control/
- llmstxt.org: https://llmstxt.org/

**보조/비공식 (⚠ 재확인 대상)**
- Naver 소유확인 유효기간(1년): https://ppcle.com/faq/naver-verification-renewal
- Daum→Upstage 인수: https://www.fnnews.com/news/202605070819041556
- Naver IndexNow 지원(2023-07): https://searchengineland.com/naver-korean-search-engine-now-supports-indexnow-429880
- Bing/Yahoo·Daum 점유율: https://seo.tbwakorea.com/blog/searchengine-ranking
- AI 봇 랜드스케이프 2026: https://nohacks.co/blog/ai-user-agents-landscape-2026
- ChatGPT-User robots 정책변경: https://ppc.land/openai-revises-chatgpt-crawler-documentation-with-significant-policy-changes/

---

**Cross-links (기존 skill docs — 중복 금지, 참조):**
[`AEO_GEO_PLAYBOOK_2026.md`](./AEO_GEO_PLAYBOOK_2026.md) · [`WEB_PLAYBOOK.md`](./WEB_PLAYBOOK.md) · [`NEW_PAGE_CHECKLIST.md`](./NEW_PAGE_CHECKLIST.md) · [`CRUX_LIGHTHOUSE_INTERPRETATION.md`](./CRUX_LIGHTHOUSE_INTERPRETATION.md) · [`CLS_SEVEN_SOURCES.md`](./CLS_SEVEN_SOURCES.md)
