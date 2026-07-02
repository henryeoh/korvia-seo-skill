---
last_verified: 2026-07
reverify_by: 2026-Q4
---

# Google Search Console + 색인(Indexing) 파이프라인

> Generated 2026-07-01 · Owner: Henry Oh (KORVIA) · ⚠ 콘솔/API 변경은 사용 전 재확인
> (지식 컷오프 2026-01 이후 콘솔 UI·API·쿼터·프로토콜은 바뀔 수 있음 — 절차 실행 전 §17 Sources의 공식 URL로 현행 재확인)

이 문서는 `korvia-seo-skill/docs/` 확장이다. **"색인이 실제로 되게 만드는 Google 운영 파이프라인"** 레이어를 담당한다. Naver·Daum·Bing/IndexNow·멀티엔진 발행은 **전용 자매 문서로 위임**하고 여기서는 링크만 건다(중복 금지).

- **전략/왜(SEO vs AEO vs GEO, 플랫폼별 인용, llms.txt/robots AI 크롤러 정책)** → [`AEO_GEO_PLAYBOOK_2026.md`](./AEO_GEO_PLAYBOOK_2026.md)
- **페이지-레벨 구현(metadata/canonical/schema/CLS/CWV 코드, sitemap.ts/robots.ts 패턴)** → [`WEB_PLAYBOOK.md`](./WEB_PLAYBOOK.md) · [`NEW_PAGE_CHECKLIST.md`](./NEW_PAGE_CHECKLIST.md) · [`PROJECT_STACK_PLAYBOOK.md`](./PROJECT_STACK_PLAYBOOK.md)
- **robots.txt/sitemap/RSS 복붙 템플릿** → [`ROBOTS_SITEMAP_RSS_TEMPLATES.md`](./ROBOTS_SITEMAP_RSS_TEMPLATES.md)
- **리치결과/JSON-LD 스키마(JobPosting 등)** → [`SCHEMA_JSONLD_CATALOG.md`](./SCHEMA_JSONLD_CATALOG.md) · **Google SERP·AI Overviews·리치결과 생사·hreflang SERP** → [`GOOGLE_SERP_STRUCTURED_AI_2026.md`](./GOOGLE_SERP_STRUCTURED_AI_2026.md)
- **CWV 실측 해석(CrUX vs Lighthouse)·CLS 7대 원인** → [`CRUX_LIGHTHOUSE_INTERPRETATION.md`](./CRUX_LIGHTHOUSE_INTERPRETATION.md) · [`CLS_SEVEN_SOURCES.md`](./CLS_SEVEN_SOURCES.md)
- **한국·타엔진(한 번 발행 → 모든 콘솔 제출 허브)** → [`MULTI_ENGINE_MASTER_2026.md`](./MULTI_ENGINE_MASTER_2026.md) · Naver=[`NAVER_SEARCHADVISOR_PLAYBOOK.md`](./NAVER_SEARCHADVISOR_PLAYBOOK.md) · Daum/Kakao=[`DAUM_KAKAO_SEARCH.md`](./DAUM_KAKAO_SEARCH.md) · Bing/IndexNow=[`BING_YAHOO_INDEXNOW.md`](./BING_YAHOO_INDEXNOW.md)
- **이 문서** → GSC 콘솔 운영 · 사이트맵 제출 한도 · Page Indexing 리포트 사유별 트러블슈팅 · Indexing API 지원범위·쿼터·우회 위험 ⚠ · 크롤예산 · 모바일 색인 · Core Web Vitals · 삭제도구 · hreflang/국제타겟팅.

**적용 대상 4개 사이트**
| 사이트 | 스택 | YMYL | 주 청중/엔진 |
|---|---|---|---|
| Korvia (www.korvia.com) | Next.js | ✅ 비자/취업 | 외국인→Google-EN, 한국인→Naver |
| Kimchi Mobile (www.kimchimobile.com) | WordPress | ✅ 통신/금융 | 외국인→Google-EN, 한국인→Naver |
| Passome (www.passome.com) | Next.js/Vercel | — | 한/영 |
| ACT Art Center (actartcenter.com) | Next.js | — | 로컬(한국인)→Naver + Google |

---

## 0. 자동 적용 규칙 (Auto-apply) — 작성자/에이전트가 기계적으로 따를 항목

작성자·에이전트는 페이지를 만들거나 고칠 때, 그리고 색인 작업을 할 때 아래를 **무조건** 준수한다. (근거는 §번호)

1. **사이트맵엔 canonical·색인대상 URL만.** noindex / 리다이렉트 / 파라미터 / 중복 URL은 절대 넣지 않는다. (§4, §5)
2. **사이트맵 상한 = 1개당 50,000 URL 또는 50MB(비압축) 중 먼저 도달.** 초과 시 분할 후 sitemap index로 묶는다. gzip 권장(50MB는 비압축 기준). (§4)
3. **robots.txt에 `Sitemap:` 라인 항상 명시** — Google·Bing·Naver 공통 발견 경로. (§4, §13, §14)
4. **모든 신규/수정 페이지에 절대 canonical(`https://` 전체 URL).** 이중언어 페이지는 hreflang **양방향 + 자기 자신** 선언. 코드 = `ko` / `en` / `x-default(영어판)`; 지역코드(en-US 등)는 콘텐츠가 실제로 다를 때만. (§11)
5. **언어별 canonical은 자기 언어판을 가리킨다** (`ko` 페이지 canonical = `ko` URL). 한 언어로 canonical을 몰지 않는다. (§11)
6. **URL 검사 "색인 생성 요청"은 하루 한도가 있으니 아껴 쓴다** — 신규 핵심 랜딩·대규모 수정 페이지만. 같은 URL 반복 제출 금지(빨라지지 않음). 대량 색인은 사이트맵으로. (§3)
7. **Indexing API는 `JobPosting` 또는 `VideoObject` 내 `BroadcastEvent` 구조화 데이터가 있는 페이지에만.** 일반 블로그/가이드/랜딩 색인 가속용으로 **절대 호출 금지** (수동 조치 리스크, §6 ⚠).
8. **구인/채용 페이지를 만들면 `JobPosting` JSON-LD를 붙여** Google Jobs 노출 + Indexing API 정당 사용 경로를 확보한다. (§6 — Korvia 원어민 채용/EPIK)
9. **삭제/오발행/민감(PII·잘못된 비자·잘못된 가격) 페이지**는 GSC 임시삭제(약 180일)로 즉시 숨기고, **동시에** 404/410 또는 noindex(메타 또는 `X-Robots-Tag` HTTP 헤더)로 영구 처리. robots.txt 차단만으로 색인 제거를 시도하지 않는다. (§10)
10. **모바일 색인 기준: 모바일에서 접히는 콘텐츠도 DOM에 반드시 존재.** `display:none`은 허용, lazy-mount로 DOM에서 빼면 색인 손실. title/description/구조화데이터/본문은 모바일=데스크톱 동일. (§8)
11. **Cloudflare/WAF가 Googlebot(Smartphone·Desktop) 및 Google-InspectionTool을 차단하지 않게** verified Googlebot 허용. Naverbot(`Yeti`)도 robots에서 차단 금지. (§8, §12, §13)
12. **GSC 소유확인 수단(DNS TXT/HTML 파일/meta 태그)은 절대 삭제하지 않는다** — 삭제 시 속성 소유확인 해제. 가능하면 **도메인 속성(DNS)**을 기본 등록. (§1, §2)
13. **한국어 대상 페이지는 GSC뿐 아니라 Naver Search Advisor에도 등록** + sitemap·RSS 제출, 핵심 페이지는 수동 수집 요청. (§13)
14. **발행·수정 시 IndexNow로 Bing/Naver 즉시 통보** (WordPress=IndexNow 플러그인, Next.js=배포 훅에서 IndexNow API). Google은 IndexNow 미지원 → 사이트맵 lastmod로 커버. (§14)
15. **Vercel 프리뷰 도메인(`*.vercel.app`)·스테이징은 색인 차단** — **Vercel Deployment Protection(인증/401)** 또는 **noindex 단독**(robots `Disallow`와 병용 금지). ⚠ 이미 발견된 프리뷰 URL에 robots 차단을 걸면 Googlebot이 크롤을 못 해 `noindex`를 못 읽어 URL-only로 색인에 잔존한다(§10 모순 주의). robots 차단 단독은 **아직 발견 안 된 신규 도메인**에만. (프로덕션 canonical 희석 방지 — §0.1, §10, §15)
16. **"Crawled/Discovered – currently not indexed"가 늘면 재제출 금지** — 콘텐츠 품질·내부링크·크롤예산(불필요 파라미터 URL 정리)을 개선. (§5, §7)
17. **CWV는 색인 여부가 아니라 랭킹 타이브레이커**임을 전제. 성능 이슈를 색인 실패 원인으로 오인 금지. INP(≤200ms good)를 FID 대신 관리. (§9)

---

## 0.1 색인 파이프라인 전체 그림 (사이트 → 검색 노출)

```
[페이지 발행]
   │
   ├─(A) robots.txt 허용 + <meta noindex> 없음        ← 크롤/색인 게이트
   │
   ├─(B) 발견 Discovery: 내부링크 + XML 사이트맵 + (선택) URL검사 색인요청
   │
   ├─(C) 크롤 Crawl: Googlebot Smartphone 렌더 → 콘텐츠 파싱
   │
   ├─(D) 색인 Index: canonical 통합 → 품질 판단 → 색인 여부 결정
   │
   └─(E) 서빙 Serving: 랭킹 + AI Overview/스니펫
```

GSC는 B~E 각 단계 상태를 리포트로 노출한다. **"발행했는데 검색이 안 됨"의 90%는 B(발견 안 됨)·C(크롤 안 됨)·D(색인 제외) 중 하나**이고, GSC "페이지 색인" 리포트의 *이유(reason)*가 정확히 어느 단계인지 알려준다(→ §5 표로 조치).

사이트별 주의점:
- **Kimchi(WordPress)**: 사이트맵은 플러그인(Yoast/RankMath/AIOSEO)이 `sitemap_index.xml`로 자동 생성. IndexNow 플러그인으로 Bing/Naver 즉시 통보.
- **Passome/Korvia/ACT(Next.js)**: `app/sitemap.ts`로 동적 생성. **Vercel 프리뷰 도메인은 절대 색인시키지 말 것**(인증/401 또는 noindex 단독 — robots `Disallow` 병용 금지, §10) — 프로덕션 canonical 중복/희석 방지.

---

## 1. 속성(Property) 등록 — Domain vs URL-prefix

GSC 속성은 두 종류. KORVIA는 **도메인 속성(Domain property)을 기본 권장**(www/non-www, http/https, 모든 서브도메인 통합 집계).

| 구분 | Domain property | URL-prefix property |
|---|---|---|
| 입력 형태 | `korvia.com`(프로토콜/서브도메인 없음) | `https://www.korvia.com/`(정확한 프리픽스) |
| 커버 범위 | 도메인의 **모든 서브도메인 + 모든 프로토콜**(http/https, www/non-www, m., blog. 등) 전부 | 입력한 프리픽스와 **정확히 일치**하는 URL만 |
| 소유확인 | **DNS 레코드(TXT/CNAME)만 가능** | HTML 파일, HTML 태그, GA, GTM, DNS 모두 |
| 언제 쓰나 | 사이트 전체를 하나로(기본 권장) | 특정 하위경로/서브도메인만 분리 추적 |

> 공식: 루트 도메인을 소유확인하면 모든 서브도메인이 자동 확인되지만, 서브도메인 확인은 상위 도메인을 확인하지 않는다. (support.google.com/webmasters/answer/9008080)

**KORVIA 권장 세팅**
- 각 사이트에 **도메인 속성 1개**(전체 집계) + 필요 시 **URL-prefix 속성**(예: `https://www.korvia.com/en/`, `https://blog.kimchimobile.com/`)을 병행해 언어/섹션별 세분 추적.
- Vercel 배포(Passome/Korvia/ACT)는 Cloudflare/도메인 DNS에 TXT 넣기가 쉬우므로 도메인 속성이 최적.

---

## 2. 소유확인(Verification) 방법 6종

| 방법 | 대상 속성 | 구현 | KORVIA 적용 |
|---|---|---|---|
| **DNS 레코드**(TXT/CNAME) | Domain + URL-prefix | 도메인 등록기관/Cloudflare DNS에 `google-site-verification=...` TXT 추가 | **도메인 속성 필수**. Cloudflare에서 즉시 추가 |
| **HTML 파일 업로드** | URL-prefix만 | 루트에 `googleXXXX.html` | WordPress(Kimchi) 루트 업로드. Next.js는 `public/`에 두면 됨 |
| **HTML 태그(meta)** | URL-prefix만 | `<head>`에 `<meta name="google-site-verification" content="...">` | Next.js `app/layout.tsx`의 `metadata.verification.google`로 주입 |
| **Google Analytics** | URL-prefix만 | 사이트에 GA(gtag.js) + edit 권한 | GA4 이미 있으면 원클릭 |
| **Google Tag Manager** | URL-prefix만 | GTM 컨테이너 + publish 권한 | GTM 쓰는 사이트에 유용 |
| **도메인 등록기관(자동 DNS)** | Domain | 지원 등록기관 자동 TXT 주입 | Cloudflare 연동 시 반자동 |

> 여러 방법을 동시에 등록하면 한 방법이 깨져도 소유확인이 유지된다. **verification 파일/태그/DNS 레코드는 절대 지우지 말 것** — 지우면 속성 소유확인이 해제되고 데이터 접근이 끊긴다.

**Next.js 태그 주입 예** (`app/layout.tsx`):
```ts
export const metadata = {
  verification: { google: "abcd1234..." }, // → <meta name="google-site-verification" content="abcd1234...">
}
```

---

## 3. URL 검사(URL Inspection) + 색인 생성 요청

**경로**: GSC 좌측 상단 검색창(또는 좌측 메뉴 "URL 검사") → 대상 URL 붙여넣기.

**보여주는 것**
- **색인 여부**: "URL is on Google" / "URL is not on Google".
- **커버리지**: 발견 경로(referring page/sitemap), 마지막 크롤 시각, **크롤에 사용된 user-agent**(모바일/데스크톱), 크롤 허용 여부, fetch 성공 여부.
- **Canonical**: 사용자 선언 canonical vs **Google이 선택한 canonical**(둘이 다르면 중복 신호).
- 구조화 데이터 / 향상된 결과(rich result) 감지 여부.

**두 가지 액션**
1. **색인 생성 요청(Request Indexing)**: 크롤 대기열 등록. 공식 — 색인은 보통 하루 정도지만 훨씬 오래 걸릴 수 있고, **요청이 색인을 보장하지 않는다**.
2. **게시된 URL 테스트(Test Live URL)**: 현재 라이브 페이지를 실시간 크롤/렌더해 색인 가능성/문제를 즉석 진단(발행 직후 noindex/robots/렌더 오류 점검용).

**한도(⚠ 정확한 숫자 비공개)**: 공식은 "하루 제출 한도가 있다"고만 명시하고 **정확한 숫자를 공개하지 않는다**. 커뮤니티 관측치(약 10건/일/속성)는 **비공식·변동 가능** → 단정 금지. 대량 색인은 색인요청이 아니라 **사이트맵**으로. (support.google.com/webmasters/answer/9012289)

> **⚠ URL Inspection API ≠ 색인 요청.** 프로그램용 **URL Inspection API**(속성당 **2,000 쿼리/일 · 600 쿼리/분**)는 **색인 상태·canonical·마지막 크롤을 조회하는 전용 API**이며, 여기서 "색인 생성 요청"을 프로그램으로 날릴 수 없다. 대량 색인 자동화 수단으로 오해 금지 — 색인 트리거는 여전히 사이트맵/내부링크다. (developers.google.com/webmaster-tools/limits)

**KORVIA 규칙**: 색인요청은 (1) 신규 핵심 랜딩/캠페인, (2) 중요 콘텐츠 대규모 수정 후 재크롤 유도에만 아껴 쓴다. 나머지는 사이트맵 + 내부링크에 맡긴다.

---

## 4. 사이트맵(Sitemap) 제출

**상한(공식)**
- 사이트맵 1개당 **최대 50,000 URL 또는 50MB(비압축)** 중 먼저 도달. 초과 시 여러 사이트맵으로 분할하고 **사이트맵 인덱스**로 묶는다.
- gzip 압축 권장(전송 크기 절감, 50MB는 비압축 기준).
- **UTF-8, 절대 URL만.** `<priority>`·`<changefreq>`는 **Google이 무시**. `<lastmod>`는 정확할 때만 유효 신호(거짓 lastmod는 신뢰 훼손).

**형식/타입**
- XML(가장 범용) — 확장으로 **이미지 사이트맵, 비디오 사이트맵, 뉴스 사이트맵, 다국어(hreflang) 대체 버전** 지원.
- RSS/mRSS/Atom 1.0 — 비디오 정보 포함 가능. (Naver는 RSS를 신선도 신호로 활용 → §13)
- 텍스트 사이트맵 — URL 목록만.

**제출 방법 4가지**
1. GSC **Sitemaps 리포트**에 사이트맵 URL 입력.
2. **Search Console API**.
3. **robots.txt**에 `Sitemap: https://www.korvia.com/sitemap.xml` 라인.
4. WebSub(Atom/RSS 피드).

**KORVIA 적용**
- **Next.js(Passome/Korvia/ACT)**: `app/sitemap.ts`(또는 sitemap index 라우트). 다국어 사이트는 **각 URL 엔트리에 `xhtml:link` hreflang 대체 버전**을 넣어 사이트맵-기반 hreflang 구현(HTML 태그보다 유지보수 쉬움 — §11). sitemap.ts의 priority/카테고리 차별화 패턴은 [`WEB_PLAYBOOK.md` §1.2](./WEB_PLAYBOOK.md) 참조(여기서 중복 안 함).
- **WordPress(Kimchi)**: Yoast/RankMath가 `sitemap_index.xml` 자동 생성 → GSC엔 **인덱스 URL 하나만** 제출.
- 모든 사이트: robots.txt에 `Sitemap:` 라인 반드시 명시. **복사-붙여넣기 robots.txt·sitemap·RSS 템플릿과 엔진별 제출 절차**는 [`ROBOTS_SITEMAP_RSS_TEMPLATES.md`](./ROBOTS_SITEMAP_RSS_TEMPLATES.md)(여기서 중복 안 함).
- **noindex/canonical-타 페이지/파라미터 URL은 사이트맵에 넣지 말 것** — 사이트맵은 "색인시키고 싶은 canonical URL"만.

**이미지/비디오 사이트맵**
- 이미지: XML 엔트리에 `<image:image><image:loc>` 확장. 이미지가 JS로 로드되거나 CDN 분리 도메인일 때 발견 보조.
- 비디오: `<video:video>`(제목·설명·thumbnail_loc·content_loc/player_loc·duration). ACT/Passome이 강의·소개 영상을 자체 호스팅하면 유효.

**뉴스 사이트맵** ⚠: **Google News 승인 퍼블리셔 대상**이며 최근 **2일 이내** 발행 기사만(사이트맵당 ~1,000 URL 권장). **KORVIA 4개 사이트는 뉴스 퍼블리셔가 아니므로 일반적으로 불필요.** (세부 한도는 이번 세션 1차 재확인 대상 아님 → 필요 시 Google News Publisher Center 문서 확인)

---

## 5. 페이지 색인(Page indexing) 리포트 — 상태별 해석 + 조치

**경로**: GSC → 색인 → **페이지(Pages)**. "색인 생성됨" vs "색인이 생성되지 않음"으로 나뉘고, 후자에 **이유(reason)**가 붙는다.

| 상태(이유) | 공식 의미 | 심각도 | 조치 |
|---|---|---|---|
| **Page is indexed** | 색인됨, 노출 가능 | ✅ | 유지 |
| **Discovered – currently not indexed**(발견됨-현재 색인 안 됨) | URL은 찾았으나 **아직 크롤 안 함**. 크롤하려 했으나 사이트 과부하 우려로 재스케줄됨 | ⚠️높음 | 서버 응답속도/크롤예산·내부링크·콘텐츠 품질 신호. 사이트맵 lastmod 갱신, 얇은 페이지 정리, 내부링크 강화 |
| **Crawled – currently not indexed**(크롤됨-현재 색인 안 됨) | 크롤했으나 **Google이 색인 안 하기로 선택**(재제출 불필요) | ⚠️높음(품질) | **콘텐츠 품질/독창성 문제**가 대다수. 얇은 콘텐츠 보강, 중복 통합, EEAT 신호 추가. 재제출 무의미 |
| **Duplicate without user-selected canonical**(중복-사용자 canonical 없음) | canonical 미지정 중복. Google이 다른 페이지를 canonical로 선택 | 중 | 명시적 `<link rel="canonical">` 지정 |
| **Duplicate, Google chose different canonical than user**(중복-Google이 다른 canonical 선택) | 사용자가 canonical 지정했으나 **Google이 다른 URL을 더 낫다고 판단** | 중 | 내부링크/사이트맵/hreflang가 일관되게 의도 canonical을 가리키는지 점검. 내용 차별화 |
| **Alternate page with proper canonical tag**(적절한 canonical 태그 있는 대체 페이지) | canonical이 다른(색인된) 페이지를 정상적으로 가리킴 | ✅정상 | 조치 불필요(의도된 동작 — hreflang 대체 언어판이 흔히 여기) |
| **URL marked 'noindex'**(noindex 표시) | `noindex` 지시문 발견 | 상황 | **의도면 정상.** 의도 안 했는데 뜨면 실수 noindex(Next.js metadata robots, WP 설정) 제거 |
| **Blocked by robots.txt**(robots.txt 차단) | robots.txt가 크롤 차단 | 상황 | 색인시켜야 할 페이지면 robots 허용. **주의: robots 차단은 색인 방지 수단이 아님**(§10) |
| **Soft 404** | "not found" 성격인데 **HTTP 200** 반환 | 중 | 진짜 없는 페이지면 404/410. 빈/얇은 페이지면 콘텐츠 채우기 |
| **Not found (404)** | 404 반환 | 낮음(정상일 수 있음) | 삭제 의도면 정상. 살아야 할 페이지면 링크/URL 수정 |
| **Page with redirect**(리다이렉트 페이지) | 비-canonical URL이 리다이렉트(대상이 색인됨) | 정상 | 조치 불필요(리다이렉트 체인만 짧게) |
| **Blocked due to unauthorized request (401)** | 인증 요구로 접근 불가 | 중 | 스테이징/보호 페이지면 정상. 공개해야 하면 인증 해제 |
| **Blocked due to access forbidden (403)** | 서버 403 | 중 | Googlebot 차단(WAF/Cloudflare) 여부 점검 — Googlebot UA/IP 허용(§12) |
| **Server error (5xx)** | 서버 오류 | 높음 | 인프라/타임아웃 문제 |

**KORVIA 운영 규칙**: "Crawled/Discovered – currently not indexed"가 늘면 **콘텐츠 품질 + 크롤예산 신호** → 얇은/중복 페이지 통합·삭제(§7). **YMYL 사이트(Korvia 비자/취업, Kimchi 통신/금융)는 EEAT·1차 근거·정확한 사실이 색인 여부를 좌우** — 근거 없는 얇은 가이드는 색인 탈락 위험이 특히 크다.

---

## 6. Indexing API — JobPosting·BroadcastEvent **전용** (⚠ 오남용 금지)

**공식 제약**: Indexing API는 **`JobPosting` 또는 `VideoObject`에 임베드된 `BroadcastEvent`** 페이지만 크롤 요청 가능. **그 외 페이지 타입은 공식 지원 안 됨.**

**⚠ 2025년 오남용 단속 강화**: Google(J. Mueller, 2025-05)은 "**문서화된 use-case에만** 사용" 권고, G. Illyes는 "**지원하지 않는 포맷은 예고 없이 지원 중단될 수 있다**"고 경고. 스패머 표적으로 감시 강화됨. → **"우회로 일반 페이지도 색인 가속된다"는 블로그 팁은 채택 금지**(정책 위반·수동 조치 리스크). (developers.google.com/search/apis/indexing-api/v3/quota-pricing)

**쿼터(공식·기간 명시됨)**
- **publish 요청: 200 / 일** (태평양시 자정 리셋).
- **read(`getMetadata`): 180 / 분.**
- **전체 엔드포인트 합산: 380 / 분.**
- 초과분은 별도 승인 필요.

**엔드포인트/절차**
1. Google Cloud 프로젝트 + **서비스 계정** 생성 → GSC 속성에 서비스 계정을 **소유자(Owner)**로 추가 → `Indexing API` 활성화.
2. 발행/갱신 통보:
   ```
   POST https://indexing.googleapis.com/v3/urlNotifications:publish
   { "url": "https://www.korvia.com/jobs/epik-spring-2027", "type": "URL_UPDATED" }
   ```
   삭제: `"type": "URL_DELETED"`.
3. 상태 조회: `POST .../urlNotifications:getMetadata`.

**KORVIA 판단**
- **일반 페이지(가이드/블로그/랜딩) 색인 가속용으로 Indexing API를 쓰지 말 것.**
- **적용 가능 케이스**: Korvia가 **`JobPosting` 구조화 데이터**([`SCHEMA_JSONLD_CATALOG.md`](./SCHEMA_JSONLD_CATALOG.md)의 JobPosting 템플릿)를 붙인 채용/구인 페이지(원어민 채용·EPIK 포지션)를 운영하면 **정당하게 Indexing API 사용 가능** + Google 구인정보(Google for Jobs) 노출과 결합. 이 경우에만 파이프라인 구축(n8n 자동화 후보).
- 일반 페이지의 즉시 통보가 필요하면 **IndexNow(Bing/Naver)**(§14)로 대체. Google은 IndexNow 미지원이므로 Google 측은 사이트맵 lastmod + (핵심만) URL검사 색인요청에 맡긴다.

---

## 7. 크롤링 통계(Crawl Stats) + 크롤 예산(Crawl Budget)

**크롤 통계 경로**: GSC → 설정(Settings) → **크롤링 통계**. 총 크롤 요청 수, 다운로드 바이트, 평균 응답시간, 응답코드(200/301/404/5xx)·파일유형·목적(발견 vs 새로고침)·Googlebot 유형별 분해.

**크롤 예산 = Google이 크롤할 수 있고(capacity) + 크롤하고 싶은(demand) URL 집합.**
- **Crawl capacity limit**: 서버 감당 가능 동시 연결 — 응답 빠르면↑, 5xx/느리면↓.
- **Crawl demand**: 인벤토리(중복/불필요 URL 많으면 낭비)·인기도·변경 빈도.

**언제 신경 쓰나(공식 임계)**
- **대형**: 100만+ 고유 페이지(주간 변경).
- **중대형**: 10,000+ 고유 페이지(**일간** 급변).
- 또는 "Discovered – currently not indexed" URL 비중이 클 때.
- 공식: 빠르게 변하는 페이지가 대량이 아니거나, 발행 당일 크롤되고 있으면 이 가이드는 읽을 필요 없다.

**KORVIA 현실**: 4개 사이트 모두 대체로 소규모(수백~수천 페이지) → **크롤 예산은 대개 문제 아님.** 단, 인벤토리 팽창은 관리:
- WordPress(Kimchi)의 태그/아카이브/파라미터 URL 양산, Next.js의 프리뷰/필터 파라미터 → robots 차단 또는 canonical 통합.
- 삭제 페이지 → **404/410**(soft 404 금지).
- 사이트맵 `<lastmod>` 정확히.
- 리다이렉트 체인 최소화.

---

## 8. 모바일 색인(Mobile Indexing)

- **⚠ 용어 변화**: Google은 2024-07-05 마이그레이션 최종 완료 후 **"mobile-first indexing" 용어 자체를 폐기**했다(이제 모바일 크롤이 기본·표준). 실질은 동일 — "Google은 스마트폰 에이전트로 크롤한 **모바일 버전** 콘텐츠로 색인·랭킹한다." 데스크톱만 있고 모바일이 빈약하면 **모바일에 보이는 콘텐츠만 색인**.
- **⚠ Googlebot Desktop은 완전 폐기 아님**: **Google for Jobs·상품목록** 등 일부 특수 작업에 여전히 사용됨. WAF에서 Googlebot **Desktop UA도 차단하지 말 것**(§12).
- **Googlebot Smartphone UA(공식 문자열)**:
  ```
  Mozilla/5.0 (Linux; Android 6.0.1; Nexus 5X Build/MMB29P) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/W.X.Y.Z Mobile Safari/537.36 (compatible; Googlebot/2.1; +http://www.google.com/bot.html)
  ```
- **콘텐츠 패리티 필수**: 모바일=데스크톱 동일 본문·제목(h1~h3)·title/meta description·**구조화 데이터**·이미지/alt/동영상. 반응형(Next.js/현대 WP 테마)이면 자동 충족되나, 조건부 렌더로 모바일에서 콘텐츠를 숨기면 색인 손실.

**KORVIA 규칙**: 모바일에서 접히는 아코디언/탭 콘텐츠도 **DOM에 존재**해야 함(`display:none`은 OK, lazy-mount로 아예 DOM에서 빼면 색인 안 됨). CLS/모바일 성능 구현 디테일은 [`WEB_PLAYBOOK.md` §15](./WEB_PLAYBOOK.md).

---

## 9. 페이지 경험 / Core Web Vitals 리포트

**경로**: GSC → 환경(Experience) → **Core Web Vitals**(URL 그룹별 Good/Needs improvement/Poor, 모바일·데스크톱 분리). 데이터 소스 = **CrUX(실사용자 필드 데이터, 28일 롤링)**.

| 지표 | Good | Needs improvement | Poor |
|---|---|---|---|
| **LCP**(로딩) | ≤ 2.5s | ≤ 4s | > 4s |
| **INP**(반응성) | ≤ 200ms | ≤ 500ms | > 500ms |
| **CLS**(시각 안정성) | ≤ 0.1 | ≤ 0.25 | > 0.25 |

- **INP는 2024-03-12부터 FID를 대체**하는 공식 Core Web Vital(발표 2024-01-31). **FID는 폐기됨.**
- **⚠ 통과 기준 = 75번째 백분위수(p75).** 위 임계값은 단순 평균이 아니라 **28일 CrUX 실사용자 방문의 최소 75%가 "Good"** 이어야 URL 그룹이 "Good"으로 판정된다(각 지표 독립, **최악 지표가 그룹 상태를 지배**). 소수의 느린 세션(구형 모바일·저속망)이 p75를 끌어내릴 수 있으므로 평균값에 안주하지 말 것.
- **⚠ LCP "2.0초 강화" 루머 반영 금지.** 2026년 일부 SEO 블로그가 LCP Good 임계를 2.0s로 낮췄다고 주장하나 **비공식**이다. 공식 Google 기준은 **여전히 LCP ≤ 2.5s / INP ≤ 200ms / CLS ≤ 0.1** (developers.google.com/search/docs/appearance/core-web-vitals).
- 페이지-레벨 최적화(LCP priority 이미지, CLS 방어, INP=메인스레드 블로킹 감소)와 **CrUX vs Lighthouse 해석·CLS 7대 원인**은 [`WEB_PLAYBOOK.md` §4·§10·§15](./WEB_PLAYBOOK.md) · [`NEW_PAGE_CHECKLIST.md` C·D](./NEW_PAGE_CHECKLIST.md) · [`CRUX_LIGHTHOUSE_INTERPRETATION.md`](./CRUX_LIGHTHOUSE_INTERPRETATION.md) · [`CLS_SEVEN_SOURCES.md`](./CLS_SEVEN_SOURCES.md)에 이미 상세 → **여기서 재기술 안 함.** 이 리포트는 그 구현의 **실사용자 검증 계기판**.

> ⚠ 인과 주의: **CWV는 랭킹의 소형 타이브레이커이며 색인 여부를 결정하지 않는다.** "CWV Poor라서 색인 안 됨"은 잘못된 인과 — 색인은 §5 품질/크롤 신호가 지배.

---

## 9.1 검색 생성형 AI 실적 리포트 (Search Generative AI performance report) — AIO/AI Mode 노출 1차 측정

**신규(2026-06-03 출시, 공식 Search Central 블로그 "Introducing Search Generative AI performance reports in Search Console").** GSC에 사이트 URL이 **Search의 생성형 AI 기능(AI Overviews·AI Mode)** 및 **Discover의 생성형 AI 기능**에서 노출된 횟수(impressions)를 보여주는 리포트가 추가됐다. 어떤 URL이 노출됐는지도 제공한다.

- **지표 = impressions만.** 클릭·CTR·평균 게재순위·**쿼리별 분해는 아직 제공 안 함** → AIO 클릭/전환은 여전히 3자 추정 의존(§17).
- **분해 축**: 페이지 · 국가 · 기기 · 날짜. **Search와 Discover는 별도 리포트**로 나뉜다.
- **⚠ 단계적 롤아웃**: 출시 초기에는 일부(영국 우선) 사이트 대상이며 **전체 대상이 아니다** — 콘솔에 아직 리포트가 안 보일 수 있음(재확인 대상: reverify 2026-Q4, §17).
- **의미**: KORVIA 4개 사이트가 노리는 **AI Overviews 인용 노출을 이제 GSC 1차 데이터(impressions)로 측정 가능**해졌다(클릭은 아직 3자 추정). SERP 레벨 AIO 노출률/리치결과 상세는 [`GOOGLE_SERP_STRUCTURED_AI_2026.md`](./GOOGLE_SERP_STRUCTURED_AI_2026.md).

**AI 기능 옵트아웃 컨트롤(동시 도입)**: AI Overviews·AI Mode·Discover 내 AI Overviews에서의 **자사 콘텐츠 미노출**을 선택하는 컨트롤이 함께 제공된다. 공식 — **이 옵트아웃은 일반 Google 검색 순위·Discover 피드 노출에 영향을 주지 않는다(순위 페널티 없음).** (Google-Extended 학습 제어와는 별개 — §12)

> Source(CONFIRMED, 2026-07 확인): "Introducing Search Generative AI performance reports in Search Console" — https://developers.google.com/search/blog/2026/06/gen-ai-performance-reports

---

## 10. 삭제 도구(Removals)

**경로**: GSC → 색인 → **삭제(Removals)**. 3섹션:
1. **임시 삭제(Temporary Removals)**: 특정 URL을 검색결과에서 **약 180일(6개월)** 숨김. 공식 — 성공 요청은 약 6개월만 지속. **색인/크롤을 막는 게 아니라 표시만 숨김.**
2. **오래된 콘텐츠(Outdated Content)**: 비소유자가 제출한 삭제/갱신 요청 상태.
3. **SafeSearch 필터링**: 성인 콘텐츠 신고 상태.

**영구 삭제(공식 권장)**
- 페이지 제거 후 **404/410** 반환, 또는
- **비밀번호 보호(접근 차단)**, 또는
- **`noindex` 메타 태그**(덜 안전 — 재색인 여지), 또는
- **`X-Robots-Tag: noindex` HTTP 헤더** — HTML 메타를 넣을 수 없는 **비-HTML 자산(PDF 등)** 및 **엣지/서버 레벨 대량 noindex**(Cloudflare Transform Rules · Vercel `headers()` · WordPress 서버 헤더)의 공식 수단. 예: Kimchi의 PDF, 프리뷰/스테이징 도메인 전역 noindex.
- 공식 경고: **robots.txt를 색인 차단 수단으로 쓰지 말 것**(이미 색인된 URL이 스니펫 없이 남을 수 있음).
- **⚠ `noindex`(메타 또는 `X-Robots-Tag`)와 robots `Disallow`를 동시에 걸지 말 것** — 크롤이 막히면 Googlebot이 `noindex`를 못 읽어 오히려 URL-only 색인이 잔존한다. 프리뷰/스테이징은 **인증(401 · Vercel Deployment Protection)** 또는 **noindex 단독**으로 처리한다(§0.1, auto-rule #15).

**KORVIA 규칙(YMYL·PII 민감)**: 급히 숨겨야 할 민감/오발행 페이지(PII 유출, 잘못된 가격/비자정보) → **임시삭제로 즉시 숨김 + 동시에 404/410 또는 noindex로 영구 처리.** 임시삭제만 하고 방치하면 180일 후 부활.

---

## 11. hreflang / 국제 타겟팅 (이중언어 핵심)

KORVIA 전 사이트가 한/영 이중언어 → hreflang이 **잘못되면 엉뚱한 언어가 노출**되거나 중복으로 처리된다.

**3가지 구현(동등, 하나만 선택)**
1. **HTML `<link>` 태그**: 각 페이지 `<head>`에 모든 언어 버전 + 자기 자신.
   ```html
   <link rel="alternate" hreflang="ko" href="https://www.korvia.com/ko/epik" />
   <link rel="alternate" hreflang="en" href="https://www.korvia.com/en/epik" />
   <link rel="alternate" hreflang="x-default" href="https://www.korvia.com/en/epik" />
   ```
2. **HTTP `Link:` 헤더**(비-HTML 리소스용, 예: PDF).
3. **XML 사이트맵의 `xhtml:link`**(대규모/유지보수 우수 — **KORVIA 권장**).

**규칙(공식)**
- 코드 = **ISO 639-1 언어**(필수) + 선택적 **ISO 3166-1 Alpha-2 지역**. 예: `en`(전세계 영어), `en-US`(미국 영어). **지역 코드 단독 불가.**
- **양방향 필수**: 두 페이지가 서로를 가리키지 않으면 태그가 무시된다. A→B면 B→A도. **자기 자신도 포함.**
- **x-default**: 매칭 안 되는 사용자용 폴백(보통 언어선택 페이지 또는 영어판).

**KORVIA 이중언어 권장 매핑**
- `hreflang="ko"`(지역 무관 한국어) + `hreflang="en"`(전세계 영어) + `x-default="…/en/…"`. 세분 지역(en-US/en-GB)은 콘텐츠가 실제로 다르지 않으면 **쓰지 말 것**(관리 부담·오류원).
- 각 언어 URL은 **자기 canonical을 자기 언어판**으로(ko 페이지 canonical=ko URL). canonical을 한쪽 언어로 몰면 다른 언어가 색인 탈락.
- Next.js: `app/sitemap.ts`의 각 엔트리에 `alternates.languages`, 페이지 head의 `metadata.alternates.languages`(→ [`WEB_PLAYBOOK.md` §3.1](./WEB_PLAYBOOK.md), [`NEW_PAGE_CHECKLIST.md` #6](./NEW_PAGE_CHECKLIST.md))와 **일관**되게.
- ⚠ **Naver는 hreflang을 Google만큼 활용하지 않음** — 한국어 페이지는 Naver Search Advisor에 별도 최적화(§13).

**⚠ 국제 타겟팅(geotargeting) 리포트는 폐지됨** — GSC의 구 "국제 타겟팅(International Targeting)" 리포트(특정 국가 수동 지정 + hreflang 오류 진단)는 **2022-09-22 제거**되었다. 그러나 **hreflang 태그 자체는 Google이 계속 지원·사용**한다(콘솔 리포트만 사라졌을 뿐). 이제 hreflang 오류를 보여주는 전용 리포트가 없으므로, 문제는 **URL 검사 도구**와 **Page Indexing**의 중복/`Google chose different canonical` 신호로 산발 노출된다. → 유일한 방어는 발행 시 hreflang을 **처음부터 양방향·정확히** 넣는 것. SERP 레벨의 hreflang·리치결과 상세는 [`GOOGLE_SERP_STRUCTURED_AI_2026.md`](./GOOGLE_SERP_STRUCTURED_AI_2026.md). (support.google.com/webmasters/answer/12474899)

---

## 12. AI 크롤러 / Googlebot UA 참고 (robots 정책은 AEO 문서와 분담)

색인 파이프라인 관점의 **공식 UA 토큰**(robots.txt 규칙 작성·WAF 허용용):

| 크롤러 | robots.txt 토큰 | 용도 |
|---|---|---|
| Googlebot Smartphone | `Googlebot` | 모바일 색인(§8, 전체 UA 문자열 위) |
| Googlebot Desktop | `Googlebot` | ⚠ 폐기 아님 — Google for Jobs·상품목록에 잔존 |
| Googlebot-Image | `Googlebot-Image` / `Googlebot` | 이미지 색인 |
| Googlebot-News | `Googlebot-News` / `Googlebot` | 뉴스 |
| Google-InspectionTool | `Google-InspectionTool` / `Googlebot` | URL 검사/리치결과 테스트가 쓰는 UA |
| Storebot-Google | `Storebot-Google` | 쇼핑 |
| GoogleOther | `GoogleOther` | 범용(연구/내부) |
| Google-Extended | `Google-Extended` | Gemini/Vertex AI **학습 제어**(색인엔 영향 없음) |

> **⚠ WAF 주의**: Cloudflare WAF/봇 방어(Kimchi·Korvia가 Cloudflare 사용)가 Googlebot(Smartphone·**Desktop**)/Google-InspectionTool을 403으로 막으면 §5의 "403" 색인 탈락이 발생한다. **verified Googlebot 허용** 필수. AI 크롤러(GPTBot/ClaudeBot/PerplexityBot/Google-Extended) 허용 정책은 [`AEO_GEO_PLAYBOOK_2026.md` §4(3)](./AEO_GEO_PLAYBOOK_2026.md) + [`WEB_PLAYBOOK.md` §12.1](./WEB_PLAYBOOK.md) + [`ROBOTS_SITEMAP_RSS_TEMPLATES.md`](./ROBOTS_SITEMAP_RSS_TEMPLATES.md) 참조 — 여기서 중복 안 함.

> **⚠ robots.txt 파싱 한도**: Google은 robots.txt의 **처음 500KiB만** 파싱하고 초과분은 무시한다. 규칙이 비대해지지 않게 관리하고 `Sitemap:` 라인은 앞쪽에 둔다. (developers.google.com/search/docs/crawling-indexing/robots/robots_txt)

---

## 13. 한국 청중: Naver / Daum (★ 한국 최우선 — 전용 문서로 위임)

한국인 청중은 **Naver·Daum 지배** → **GSC만으로는 부족**하고, KORVIA에서 두 엔진은 Google만큼(국문 페이지는 그 이상) 중요하다. 이 문서는 Google 파이프라인 전담이므로, 두 콘솔의 **실제 대시보드 메뉴 기준 절차**(등록/소유확인, 수집요청 한도, 사이트맵/RSS, robots 검증, 색인·노출 진단)는 전용 문서로 위임하고 여기서는 **Google과 병행해야 한다는 경계 사실만** 남긴다.

- **Naver** → [`NAVER_SEARCHADVISOR_PLAYBOOK.md`](./NAVER_SEARCHADVISOR_PLAYBOOK.md): 서치어드바이저 콘솔(searchadvisor.naver.com/console/board) 등록·소유확인(HTML/메타), 사이트맵 + **RSS**(신선도 신호) 제출, 웹페이지 수집요청(⚠ 하루 한도 비공식), robots(`Yeti`) 검증, 스마트블록/VIEW 노출.
- **Daum / Kakao** → [`DAUM_KAKAO_SEARCH.md`](./DAUM_KAKAO_SEARCH.md): 현행 웹마스터 콘솔(webmaster.daum.net/dashboard) 등록·수집요청·사이트맵/RSS·robots 검증·색인·노출 현황, `Daumoa` UA 허용.
- **한 번 발행 → Google/Naver/Daum/Bing 모든 콘솔 제출** 오케스트레이션 → [`MULTI_ENGINE_MASTER_2026.md`](./MULTI_ENGINE_MASTER_2026.md).

**Google 관점 주의점**: (1) Naver는 hreflang을 Google만큼 활용하지 않으므로 한국어 페이지의 Naver 최적화는 별도 트랙(§11). (2) robots.txt에서 `Yeti`(Naver)·`Daumoa`(Daum)를 **절대 차단하지 말 것**(§12). (3) Naver·Daum은 색인 로직·랭킹 신호가 Google과 다르다(자체 통합검색·스마트블록 구조).

---

## 14. 즉시 통보: IndexNow (Bing·Naver·Yandex·Seznam·Yep; ⚠ Google·Daum 미참여) — 전용 문서로 위임

발행/수정 즉시 참여 엔진에 통보하는 IndexNow 프로토콜(키 파일 소유확인, 단일/배치 제출, 배치 최대 10,000 URL, 참여 엔진 목록, Bing 쿼터, WordPress 플러그인 / Next.js·n8n 자동화 구현)은 **[`BING_YAHOO_INDEXNOW.md`](./BING_YAHOO_INDEXNOW.md)** 가 전담한다. 여기서는 **Google 파이프라인과의 경계**만 명시한다:

- **⚠ Google은 IndexNow 영구 미참여**(2021 테스트만) → Google 측 즉시성은 **사이트맵 `lastmod` + (핵심 페이지만) URL 검사 색인요청**으로 커버한다(§3·§4).
- IndexNow는 **Bing·Naver·Yandex·Seznam·Yep**에 통보되므로 **한국어 페이지(특히 Naver)에 유효** → §13 + [`MULTI_ENGINE_MASTER_2026.md`](./MULTI_ENGINE_MASTER_2026.md).
- **⚠ Daum은 IndexNow 미참여** → IndexNow 통보만으로는 Daum에 반영되지 않는다. Daum은 webmaster.daum.net 수집요청 + register.search.daum.net 검색등록으로 **별도 처리** → [`DAUM_KAKAO_SEARCH.md`](./DAUM_KAKAO_SEARCH.md)(§13).
- 일반 페이지의 "즉시 색인"이 필요할 때 **Google Indexing API를 우회 사용하지 말고**(§6 ⚠) IndexNow(비-Google) + 사이트맵으로 처리한다.

---

## 15. KORVIA 사이트별 색인 파이프라인 요약

| 사이트 | 스택 | 사이트맵 | 소유확인 | 즉시통보 | 특이 리스크 |
|---|---|---|---|---|---|
| **Korvia** | Next.js | `app/sitemap.ts` + hreflang(ko/en) | 도메인속성 DNS | IndexNow(Bing/Naver) + `JobPosting`은 Indexing API 정당 | YMYL(비자/취업) → EEAT·1차근거로 "crawled-not-indexed" 방어 |
| **Kimchi Mobile** | WordPress | 플러그인 `sitemap_index.xml` | 도메인 DNS + 파일 | IndexNow 플러그인 | YMYL(통신/금융); 태그/아카이브 URL 팽창 → 크롤예산 정리 |
| **Passome** | Next.js/Vercel | `app/sitemap.ts` | 도메인 DNS | IndexNow | **Vercel 프리뷰 도메인 noindex 필수** |
| **ACT Art Center** | Next.js | `app/sitemap.ts` | 도메인 DNS | IndexNow | 로컬 비즈(미술센터) → LocalBusiness 스키마 + Naver 지도/플레이스 병행 |

---

## 16. 발행 후 색인 점검 루틴 (운영 체크리스트)

1. **발행 즉시**: robots 허용·noindex 없음 확인.
   ```bash
   curl -sL https://www.korvia.com/ko/epik | grep -iE 'noindex|rel="canonical"'
   ```
2. GSC **URL 검사 → 게시된 URL 테스트**로 렌더/색인가능성 확인(모바일 UA fetch 성공, Google 선택 canonical 일치).
3. 핵심 페이지만 **색인 생성 요청**(하루 한도 아껴서 — §3).
4. 사이트맵 lastmod 갱신 반영 확인(자동 생성이면 재배포).
5. **IndexNow로 Bing/Naver 통보**(Next.js=API 호출, WP=플러그인 — §14).
6. **Naver Search Advisor 수동 수집 요청**(한국어 핵심 페이지 — §13).
7. **3~7일 후** GSC 페이지 색인 리포트에서 상태 확인 → §5 표대로 조치.
8. **2~4주 후** CWV 리포트(실사용자 데이터 축적 후)로 성능 검증(CrUX 28일 롤링 — 조급 금지, [`WEB_PLAYBOOK.md` §10·§15.1](./WEB_PLAYBOOK.md)).
9. **(리포트 롤아웃된 속성만) 검색 생성형 AI 실적 리포트**로 AIO/AI Mode 노출(impressions) 확인 — 클릭/쿼리는 아직 미제공, 단계적 롤아웃이라 아직 안 보일 수 있음(§9.1).

---

## 17. Sources (공식 1차 소스 우선 — 사용 전 재확인)

**Google — 확정(CONFIRMED, 2026-07-01 확인)**
- Build and submit a sitemap (50,000 URL / 50MB) — https://developers.google.com/search/docs/crawling-indexing/sitemaps/build-sitemap
- Verify site ownership (Domain vs URL-prefix) — https://support.google.com/webmasters/answer/9008080
- Indexing API Quickstart — https://developers.google.com/search/apis/indexing-api/v3/quickstart
- Indexing API Quota & Pricing (publish 200/일·read 180/분·합산 380/분) — https://developers.google.com/search/apis/indexing-api/v3/quota-pricing
- URL Inspection Tool (하루 한도·색인 미보장) — https://support.google.com/webmasters/answer/9012289
- Page Indexing report (Discovered/Crawled – currently not indexed 등) — https://support.google.com/webmasters/answer/7440203
- Large site owner's guide to managing crawl budget (100만+/1만+ 임계) — https://developers.google.com/search/docs/crawling-indexing/large-site-managing-crawl-budget
- Mobile-first indexing best practices — https://developers.google.com/search/docs/crawling-indexing/mobile/mobile-sites-mobile-first-indexing
- Mobile indexing migration 완료 공지(2024-07-05) — https://developers.google.com/search/blog/2024/06/mobile-indexing-vlast-final-final
- Google common crawlers (user agents) — https://developers.google.com/search/docs/crawling-indexing/google-common-crawlers
- Core Web Vitals report — https://support.google.com/webmasters/answer/9205520
- Core Web Vitals 임계값 + p75 통과 기준(LCP≤2.5s/INP≤200ms/CLS≤0.1) — https://developers.google.com/search/docs/appearance/core-web-vitals
- INP becomes a Core Web Vital (2024-03-12) — https://web.dev/blog/inp-cwv-march-12
- Removals and SafeSearch reports tool (약 180일) — https://support.google.com/webmasters/answer/9689846
- Localized versions / hreflang — https://developers.google.com/search/docs/specialty/international/localized-versions
- International Targeting 리포트 폐지(2022-09-22)·hreflang 태그는 계속 유효 — https://support.google.com/webmasters/answer/12474899
- URL Inspection API 한도(2,000/일·600/분, 상태조회 전용) — https://developers.google.com/webmaster-tools/limits
- robots.txt 500KiB 파싱 한도 — https://developers.google.com/search/docs/crawling-indexing/robots/robots_txt
- Search Generative AI performance reports(2026-06-03 출시·AIO/AI Mode 노출 impressions-only·AI 기능 옵트아웃 순위 무영향·단계적 롤아웃) — https://developers.google.com/search/blog/2026/06/gen-ai-performance-reports

**Bing / IndexNow / Naver**
- IndexNow Documentation (배치 10,000 URL·{key}.txt) — https://www.indexnow.org/documentation
- IndexNow 참여 엔진(Bing/Yandex/Naver/Seznam/Yep) — https://en.wikipedia.org/wiki/IndexNow
- Bing Webmaster: submit up to 10,000 URLs/day — https://blogs.bing.com/webmaster/january-2019/bingbot-Series-Get-your-content-indexed-fast-by-now-submitting-up-to-10,000-URLs-per-day-to-Bing
- Bing: how to add IndexNow — https://www.bing.com/indexnow/getstarted
- Naver Search Advisor 사이트 등록/소유확인 — https://searchadvisor.naver.com/guide/faq-start-register
- Naver Search Advisor IndexNow 가이드(2023-07-25 지원) — https://searchadvisor.naver.com/guide/indexnow-request

**⚠ 비공식/미확인(단정 금지)**
- Naver 수동 수집 요청 "하루 50건" — 한국어 SEO 블로그 다수 서술이나 Naver 공식 1차 미확인. 예: https://www.thewordcracker.com/basic/네이버-방문자-유입-늘리는-방법-웹페이지-수집-요청/
- GSC URL 검사 색인요청 정확한 하루 한도 숫자 — Google 미공개(커뮤니티 "약 10건" 비공식).

---

### ⚠ 이 문서의 미확인/주의 플래그 요약
- **Indexing API 일반 페이지 우회 금지** — 2025년 Google 오남용 단속 강화, "미지원 포맷 예고 없이 중단" 경고(§6).
- **GSC 색인요청 하루 한도 정확 숫자** — Google 미공개("약 10건"은 비공식)(§3).
- **URL Inspection API는 상태 조회 전용**(2,000/일·600/분) — 프로그램적 색인요청 수단 아님(§3).
- **CWV 통과 = p75(실사용자 75%)가 Good**, 최악 지표가 그룹 상태 지배. **LCP "2.0s 강화"는 비공식 루머**(공식 2.5s 유지)(§9).
- **국제 타겟팅(geotargeting) 리포트 폐지(2022-09-22)** — hreflang 태그 자체는 계속 유효, 오류는 URL 검사/Page Indexing으로 산발 노출(§11).
- **Googlebot Desktop 완전 폐기 아님** — Google for Jobs/상품목록에 잔존, WAF 차단 금지(§8, §12).
- **robots.txt는 처음 500KiB만 파싱** — 초과분 무시(§12).
- **검색 생성형 AI 실적 리포트(2026-06-03 출시)** — AIO/AI Mode 노출 impressions 제공(클릭/CTR/쿼리 미제공), **단계적 롤아웃**이라 속성에 아직 안 보일 수 있음(§9.1).
- **프리뷰/스테이징은 noindex + robots `Disallow` 병용 금지** — 크롤 차단 시 noindex 미반영, 인증(401) 또는 noindex 단독 사용(§10, auto-rule #15).
- **Naver 수동 수집요청 한도·IndexNow 참여 엔진(Yep 포함)·Google 영구 미참여** 등 타엔진 세부 → [`NAVER_SEARCHADVISOR_PLAYBOOK.md`](./NAVER_SEARCHADVISOR_PLAYBOOK.md) · [`DAUM_KAKAO_SEARCH.md`](./DAUM_KAKAO_SEARCH.md) · [`BING_YAHOO_INDEXNOW.md`](./BING_YAHOO_INDEXNOW.md)(§13, §14).
