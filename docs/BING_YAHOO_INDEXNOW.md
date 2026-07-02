# Bing / Yahoo + IndexNow — 색인 제출·프로토콜·GEO Grounding 완전 가이드

> Generated 2026-07-01 · Owner: Henry Oh (KORVIA) · ⚠ 콘솔/API 변경은 사용 전 재확인
> 대상 사이트: **Passome**(Next.js/Vercel) · **Korvia**(Next.js, YMYL 비자/취업) · **Kimchi Mobile**(WordPress, YMYL 통신/금융) · **ACT Art Center**(Next.js)

이 문서는 `korvia-seo-skill/docs/`의 다음 문서를 **보완**하며 내용을 중복하지 않는다:

- 전략 레이어(왜 GEO/AEO 하는가, 플랫폼별 인용 성향, llms.txt) → [`./AEO_GEO_PLAYBOOK_2026.md`](./AEO_GEO_PLAYBOOK_2026.md)
- Google robots.txt / sitemap.xml / JSON-LD 빌드 헬퍼(`buildFaqJsonLd` 등) / CSP / CLS → [`./WEB_PLAYBOOK.md`](./WEB_PLAYBOOK.md)
- 새 페이지 스키마·메타데이터 최소 체크리스트 → [`./NEW_PAGE_CHECKLIST.md`](./NEW_PAGE_CHECKLIST.md)

**분업 원칙:** Google 색인(Search Console·사이트맵·Indexing API)은 위 문서들이 담당한다. 이 문서는 **Bing 계열(Bing·Yahoo·DuckDuckGo·Copilot) + IndexNow 프로토콜**만 다룬다. 스키마 마크업의 *만드는 법*은 WEB_PLAYBOOK을 참조하고, 여기서는 *Bing이 무엇을 지원·검증하는가*만 추가한다.

---

## 0. 자동 적용 규칙 (Auto-apply)

작성자/에이전트가 새 페이지 발행·수정 시 기계적으로 따를 항목. (근거·상세는 아래 섹션)

1. **발행/수정 직후 변경 URL만 IndexNow 핑** → `POST https://api.indexnow.org/indexnow`. **전체 사이트맵 재핑 금지**(429/스팸 신호). — §4
2. IndexNow POST body에는 `host`, `key`, `keyLocation`, `urlList` 4개를 반드시 포함하고, `urlList`의 모든 URL 도메인이 `host`와 **완전히 일치**해야 한다(불일치 = 422). — §4.3
3. **1 요청당 URL 최대 10,000개.** 초과 시 배치 분할. — §4.3
4. IndexNow 키 파일 `{key}.txt`(UTF-8, 내용=키 문자열)를 사이트 **루트**에 호스팅. Next.js는 `public/{key}.txt`. 키 값은 **서버 환경변수(`process.env.INDEXNOW_KEY`)로만** 다뤄 클라이언트 번들 노출 금지. — §4.2, §7.1
5. **핑 응답이 200/202가 아니면(400/403/422/429) 로그로 남기고 실패로 처리.** 성공 간주 금지. — §4.4
6. **Google은 IndexNow로 처리하지 않는다**(Google 미참여). Google 색인은 GSC/사이트맵(다른 문서). IndexNow는 Bing·Naver·Yandex·Seznam·Yep 계열 전파로만 취급. — §4.1
7. **한국인 대상 페이지도 IndexNow 커버.** Naver가 IndexNow 참여. ⚠ 단, 제네릭 엔드포인트의 Naver 실전파는 공식 미확인 → **한국 트래픽 핵심 페이지는 Naver 직접 엔드포인트 병행 핑 권장**. — §4.1
8. **모든 신규 페이지에 JSON-LD** + 사이트별 타입(Korvia=`Article`+`FAQPage`+`HowTo` / Kimchi=`Product`·`Offer`+`FAQPage` / ACT=`LocalBusiness`·`Course`·`Event` / Passome=`Organization`·`Service`·`Article`). 각 타입의 required 속성(날짜/이름/가격 등) 필수 — 누락 시 Bing이 무시. (빌드 헬퍼는 WEB_PLAYBOOK) — §5
   - ⚠ **`FAQPage`·`HowTo`는 Google 리치결과 폐지** → 신규는 **Google 노출 목적으로 생성 금지**. Naver/AI(Bing·Copilot) 파싱 목적이면 ⚠ 달고만 사용. (출처: https://developers.google.com/search/blog/2023/08/howto-faq-changes)
9. 발행 후 **BWT URL Inspection → Markup Validator**로 스키마 유효 인식 확인. — §5
10. **신규 사이트는 Bing URL Submission API 쿼터가 낮게 시작**하므로 대량 일괄 제출 대신 **IndexNow 실시간 핑을 주 채널**로 사용. — §3.1
11. 새 사이트는 BWT에 **GSC Import(동일 Google 계정)** 또는 `msvalidate.01` meta 태그로 등록하고 `/sitemap.xml` 제출. — §2
12. **YMYL 페이지(Korvia 비자/취업 · Kimchi 통신/요금)는 정확성 게이트 통과 후에만 IndexNow 핑.** 부정확한 최신 버전이 Copilot/Yahoo에 빠르게 인용되는 것을 막는다. — §6, §9
13. `robots.txt`에서 **`bingbot`과 `OAI-SearchBot`을 차단하지 않는다**(AI 가시성). AI 크롤러 허용 목록 자체는 WEB_PLAYBOOK §1.1 관리. — §6
14. Bing **AI Performance 리포트의 Grounding Queries**를 주기 확인 → AI가 실제 쓰는 검색 표현에 맞춰 헤딩·FAQ 문구 조정. — §6.1
15. **WordPress(Kimchi)**는 Bing 공식 IndexNow 플러그인 또는 RankMath로 자동 핑 ON. **Yoast 단독은 IndexNow 미지원** → 별도 플러그인 병행. — §7.2

---

## 1. 왜 Bing 색인이 4개 사이트에 중요한가 (Executive Rationale)

Bing은 검색 점유율만 보면 작지만, **아래 소비 채널들의 공통 백엔드**라 실질 도달이 점유율보다 훨씬 크다.

1. **Yahoo** — 2009년 Microsoft-Yahoo 제휴 이후 Yahoo Search의 유기 결과는 Bing이 생성한다(미국 검색 점유율 약 3.2%, 2025-08 기준). Yahoo용 **별도 색인 제출 채널은 없다** → Bing에 색인되면 Yahoo에 노출.
   - ⚠ **단, Yahoo는 Bing "전속"이 아니다.** 공급자 유연성을 확보해(데스크톱 광고의 51%만 Bing 의무, 과거 2015~2018 일부 Google 결과 사용) 자체/타 공급자를 혼용할 수 있다. "Bing 색인 = Yahoo 자동 노출"은 **유기 결과에 한한 근사**로 해석. (출처: https://en.wikipedia.org/wiki/Yahoo_Search)
2. **DuckDuckGo** — 전통적으로 Bing을 주요 결과 소스로 사용(자체 크롤러 병행). ⚠ 오랜 정설이나 2026 시점 DDG 공식 재확인 소스는 미확보 → "대체로 그렇다" 수준. (출처: https://en.wikipedia.org/wiki/DuckDuckGo)
3. **Microsoft Copilot / Bing 검색 내 AI 요약 (GEO 핵심)** — Copilot의 웹 grounding은 **Bing index를 Prometheus/Bing Orchestrator로 검색**해 후보 페이지를 고르고 인라인 인용한다. 즉 **Bing에 색인·상위 노출된 페이지가 Copilot 답변에 인용될 확률이 높다.** (Prometheus = Bing index + GPT 결합, 출처: https://blogs.bing.com/search-quality-insights/february-2023/Building-the-New-Bing)
   - ℹ 명칭 최신화: 구 "Bing Chat"은 **Copilot / Copilot Search**로 리브랜딩됨. 문서/UI에서 "Copilot"으로 통일.

> **YMYL 주의(Korvia 비자/취업, Kimchi 통신/금융):** AI 답변 인용은 신뢰 신호(구조화 데이터·저자·출처·정확성)에 민감하다. Bing 색인 + 스키마 + IndexNow 빠른 재색인을 묶어야 AI 답변에서 **최신·정확 버전**이 인용된다. → §6, §9.

**한/영 이중언어 관점:** 한국인 청중은 Naver 지배적이라 **IndexNow의 Naver 전파**가 레버(§4.1)다. 한국 거주/방문 외국인은 영어 Google + AI 답변엔진 비중이 커서 **Bing→Copilot grounding**(§6)이 레버다. 두 청중이 다른 채널로 갈리므로 **Bing 색인 + Naver IndexNow를 둘 다** 챙긴다.

---

## 2. Bing Webmaster Tools (BWT) — 등록·소유확인·사이트맵

BWT 콘솔: `https://www.bing.com/webmasters`

### 2.1 사이트 추가 — 2가지 경로

| 경로 | 방법 | 권장 대상 |
|---|---|---|
| **A. GSC에서 Import** | BWT 첫 화면 "Import your sites from GSC" → 동일 Google 계정 로그인 → GSC 검증 속성 일괄 가져오기. **Bing이 GSC 계정으로 주기 재검증하므로 코드 삽입 불요.** | 4개 사이트가 이미 GSC 등록돼 있으면 가장 빠름 |
| **B. 수동 추가 + 소유확인** | "Add a site manually"에 URL 입력 후 아래 3중 택1 | GSC 미연동 시 |

### 2.2 소유확인 3방법 (수동 경로)

| 방법 | 구현 | Next.js (Passome/Korvia/ACT) | WordPress (Kimchi) |
|---|---|---|---|
| **XML 파일** | `BingSiteAuth.xml` 다운로드 → 루트 업로드 | `public/BingSiteAuth.xml` → `/BingSiteAuth.xml` 접근 확인 | 루트 FTP 업로드 또는 SEO 플러그인 |
| **Meta 태그** | `<meta name="msvalidate.01" content="{CODE}" />` 를 홈 `<head>`에 | `app/layout.tsx`의 `metadata.verification.other['msvalidate.01']` 또는 `<head>` 직접 | RankMath/AIOSEO "Webmaster Tools" 필드 |
| **CNAME (DNS)** | 호스트=Bing 제공 값, 대상=`verify.bing.com` | Cloudflare DNS에 CNAME 추가 | 동일 |

- **가장 간단 = Meta 태그.** GSC를 이미 쓰면 Import(A)가 최선.
- ⚠ **CNAME DNS 전파는 최대 72시간** 소요될 수 있다(과거 "48h"로 알려졌으나 공식 기준은 최대 72h). 급하면 meta 태그로 우회. (출처: https://www.bing.com/webmasters/help/add-and-verify-site-12184f8b)

**Next.js meta 태그 예시 (App Router):**
```ts
// app/layout.tsx
export const metadata = {
  verification: {
    other: { 'msvalidate.01': process.env.BING_SITE_VERIFICATION ?? '' },
  },
};
```

### 2.3 사이트맵 제출

BWT → 좌측 **Sitemaps** → "Submit sitemap" → `https://www.example.com/sitemap.xml` **전체 URL** 입력.
- Next.js는 `app/sitemap.ts`가 생성한 `/sitemap.xml`(생성 규칙·priority는 WEB_PLAYBOOK §1.2).
- WordPress(Kimchi)는 RankMath/Yoast/AIOSEO 사이트맵.

---

## 3. Bing URL Submission API + 수동 URL 제출 (색인 가속 채널 ①)

### 3.1 일일 URL 제출 쿼터

| 항목 | 값 | 출처/주의 |
|---|---|---|
| **표준 일일 한도** | 인증 사이트당 **최대 10,000 URL/일** | 2019년 100→10,000 상향, 월 한도 폐지 (https://blogs.bing.com/webmaster/january-2019/bingbot-Series-...) |
| **신규/신설 사이트** | 낮은 한도에서 시작해 신뢰·연차 누적 시 상향 | ⚠ **"초기 100"은 서드파티 수치** — Bing 공식 고정값 아님. 실값은 BWT **"Submit URLs"** 페이지에서 확인 |
| **쿼터 단위** | **도메인 레벨** (서브도메인 합산 아님) | |
| **리셋** | 매일 자정 (UTC) | |
| **API로 잔여 확인** | `GetUrlSubmissionQuota` 메서드 | https://learn.microsoft.com/en-us/dotnet/api/microsoft.bing.webmaster.api.interfaces.iwebmasterapi.geturlsubmissionquota |

> **주의:** Bing에서 "URL Submission API"와 "IndexNow"는 사실상 통합됐다. Bing의 `bing.com/webmasters/url-submission-api` 페이지가 IndexNow로 안내한다. **신규 사이트에서는 IndexNow가 마찰이 더 적다**(사이트 등록/검증 없이 키만으로 제출 가능) → **IndexNow를 주 채널로**(Auto-apply #10).

### 3.2 수동(콘솔) URL 제출

BWT → **"Submit URLs"** → URL 목록 붙여넣기. 한도는 위 쿼터와 동일. 단발성 긴급 색인용.

### 3.3 URL Inspection / Markup Validator

BWT → **URL Inspection** → URL 입력 → "Inspect". 색인 상태·문제 진단 + **구조화 데이터 Markup Validator**(§5) 제공.

---

## 4. IndexNow 프로토콜 (색인 가속 채널 ②, 핵심)

IndexNow = 콘텐츠 추가/수정/삭제를 참여 검색엔진에 **실시간 push**하는 무료 오픈 프로토콜(Microsoft+Yandex 공동 창안). 한 엔진에 핑하면 **참여 엔진 전체에 자동 전파**된다.

### 4.1 참여 검색엔진 (2026-07 라이브 확인 — `indexnow.org/searchengines.json`)

| 엔진 | IndexNow 엔드포인트(호스트) | 비고 |
|---|---|---|
| **Bing** | `www.bing.com/indexnow` | Copilot/Yahoo/DuckDuckGo 백엔드 |
| **Naver** | `searchadvisor.naver.com/indexnow` | 🇰🇷 2023-07 지원 시작 — **한국인 청중 핵심** |
| **Yandex** | `yandex.com/indexnow` | |
| **Seznam** | `search.seznam.cz/indexnow` | 체코 |
| **Yep** | `indexnow.yep.com/indexnow` | |
| **Internet Archive** | `web-static.archive.org/indexnow` | |
| **AmazonBot** | `indexnow.amazonbot.amazon/indexnow` | |
| **Google** | ❌ **미참여** | 2021년부터 테스트만, 2026년 현재 미채택. Google은 GSC/사이트맵/(제한적) Indexing API로 별도 |

> **권장 엔드포인트 = 제네릭 `https://api.indexnow.org/indexnow`** — 여기로 한 번 POST하면 모든 참여 엔진에 자동 분배된다. 개별 엔진에 각각 핑할 필요 없음.
>
> ⚠ **Naver 전파 플래그:** 프로토콜상 "한 엔진 핑 = 전체 공유"가 원칙이고 Naver가 참여 목록에 있으나, **제네릭 엔드포인트가 Naver까지 실제 전파하는지에 대한 Naver/IndexNow의 명시적 공식 문구는 미확인**이다. **한국 트래픽 핵심 페이지는 Naver 직접 엔드포인트로 병행 핑을 권장**(Auto-apply #7):
> ```
> GET https://searchadvisor.naver.com/indexnow?url={url}&key={key}
> ```
> (출처: https://www.indexnow.org/searchengines.json , https://searchengineland.com/naver-korean-search-engine-now-supports-indexnow-429880)

### 4.2 키 생성·호스팅 (한 번만)

1. **키 생성**: 8~128자. ⚠ **허용 문자셋은 실제로 `a-z`, `A-Z`, `0-9`, 하이픈(`-`) 전체**다. 공식 문서가 "hexadecimal characters"라 부르지만 이는 문서 자체의 부정확한 표현이므로 **"16진수만"으로 오해하지 말 것**(유효 키를 과도 제약하게 됨). 예시 키가 hex라 무해할 뿐. BWT의 IndexNow 섹션에서 자동 생성하거나 UUID를 써도 된다. (출처: https://www.indexnow.org/documentation)
2. **키 파일 호스팅**: 루트에 `{key}.txt`(UTF-8, 내용 = 키 문자열 그 자체). 예: `https://www.example.com/a1b2c3d4e5f6.txt` → 파일 내용 `a1b2c3d4e5f6`.
   - 대안: 다른 경로에 두고 `keyLocation` 파라미터로 지정(단, 그 경로 **하위** URL만 제출 가능).

### 4.3 핑 방법

**단건(GET):**
```
https://api.indexnow.org/indexnow?url=https://www.example.com/new-page&key=a1b2c3d4e5f6
```

**대량(POST JSON, 1 요청당 최대 10,000 URL):**
```
POST /indexnow HTTP/1.1
Host: api.indexnow.org
Content-Type: application/json; charset=utf-8

{
  "host": "www.example.com",
  "key": "a1b2c3d4e5f6",
  "keyLocation": "https://www.example.com/a1b2c3d4e5f6.txt",
  "urlList": [
    "https://www.example.com/url1",
    "https://www.example.com/url2"
  ]
}
```

### 4.4 응답 코드

| 코드 | 의미 | 조치 |
|---|---|---|
| **200** | 성공 | OK |
| **202** | 접수됨, 키 검증 대기 | 정상(키 파일 접근 검증 중) |
| **400** | 잘못된 형식 | JSON/URL 형식 확인 |
| **403** | 키 무효 | 키 파일 접근 불가/불일치 |
| **422** | URL이 host/key 스키마 불일치 | `urlList`의 도메인이 `host`와 같은지 |
| **429** | 레이트 리밋 초과 | 배치·백오프 |

### 4.5 운영 원칙

- **변경분만 핑**(신규/수정/삭제된 URL). 전체 사이트맵을 매번 던지지 말 것 — 스팸 신호·429 위험.
- 동시 다건 발행 시 **배치**(1 요청에 여러 URL).
- 셋업 후 `curl`로 단건 GET → 200/202 확인 후 자동화.
  ```bash
  curl -i "https://api.indexnow.org/indexnow?url=https://www.example.com/test&key=$INDEXNOW_KEY"
  ```

---

## 5. Bing 구조화 데이터 / 스키마 지원 (AI 인용 신뢰신호)

> 스키마를 **만드는 방법**(`buildFaqJsonLd`, `buildBreadcrumbJsonLd`, Organization/WebSite 엔티티 그래프 등)은 [`./WEB_PLAYBOOK.md`](./WEB_PLAYBOOK.md) §2 및 [`./NEW_PAGE_CHECKLIST.md`](./NEW_PAGE_CHECKLIST.md)를 그대로 사용한다. 여기서는 **Bing이 무엇을 지원·검증하는가**만 추가한다.

Bing이 지원하는 마크업 형식(크롤러가 특정 형식을 선호하지 않음, 택1):

| 형식 | 지원 | 비고 |
|---|---|---|
| **JSON-LD** | ✅ | **권장** — Next.js/WordPress 모두 삽입 용이. (ℹ Bing JSON-LD 지원은 **2018-08부터의 장기 확립 기능**이지 최근 신규가 아님) |
| Schema.org (Microdata) | ✅ | |
| Microformats | ✅ | |
| RDFa | ✅ | |
| OpenGraph | ✅ | 소셜/미리보기 |

(출처: https://www.bing.com/webmasters/help/marking-up-your-site-with-structured-data-3a93e731 , 2018-08 도입: https://blogs.bing.com/webmaster/august-2018/Introducing-JSON-LD-Support-in-Bing-Webmaster-Tools)

- **검증 도구**: BWT → **URL Inspection** → Inspect (Markup Validator 통합). 감지된 스키마 타입 계층(Article, Product, Event, Organization 등) 표시.
- **필수 필드 누락 시 무시**: 날짜 없는 Event, 이름 없는 Person, 가격 없는 Offer 등은 무효 처리 → 각 타입 required 속성을 반드시 채운다.

**사이트별 권장 타입 (Bing/AI 인용 최적화 관점):**
- **Passome**(컨설팅/서비스) → `Organization`, `Service`, `Article`(블로그), `FAQPage`⚠
- **Korvia**(취업/비자, YMYL) → `Organization`, `FAQPage`⚠, `HowTo`⚠(EPIK 절차), `Article`(**저자·날짜 필수**), `JobPosting`(해당 시)
- **Kimchi**(통신/eSIM, YMYL) → `Product`/`Offer`(eSIM 요금제·가격·통화 필수), `FAQPage`⚠, `Organization`
- **ACT**(미술교육) → `Organization`/`LocalBusiness`(주소·좌표·영업시간), `Course`, `Event`(전시/수업)

> ⚠ **`FAQPage`·`HowTo`(위 ⚠ 표시)는 Google 리치결과 폐지** (2023-08, HowTo 전면·FAQ는 소수 권위 도메인 외 폐지) → **Google SERP 노출 목적으로 신규 생성 금지**. Bing/Copilot·Naver·AI 파싱 신뢰신호 목적이면 ⚠ 달고만 사용하고, Google 리치결과 기대치는 두지 말 것. (출처: https://developers.google.com/search/blog/2023/08/howto-faq-changes)

---

## 6. Bing 색인 ↔ ChatGPT/Copilot Grounding 연결고리 (GEO 핵심)

> 플랫폼별 인용 성향(ChatGPT=백과사전형, Perplexity=최신성 등)의 전략적 배경은 [`./AEO_GEO_PLAYBOOK_2026.md`](./AEO_GEO_PLAYBOOK_2026.md) §3에 있다. 여기서는 **Bing 색인이 어느 AI 채널에 얼마나 연결되는가**만 다룬다.

| AI 채널 | Bing 색인 의존 | 근거/상태 (2026-07) |
|---|---|---|
| **Microsoft Copilot (Web/Bing AI 요약)** | ✅ **강한 의존** | Copilot 웹 grounding = Bing index를 Prometheus로 검색해 인용. Bing 상위 노출 ↑ = Copilot 인용 확률 ↑ |
| **Yahoo / DuckDuckGo** | ✅ (유기 결과) | Yahoo=Bing 결과(전속 아님), DDG=Bing 주요 소스 |
| **ChatGPT Search** | ⚠ **전환 진행형·불확실** | 2023-10부터 Bing 결과 사용. OpenAI가 자체 **OAI-SearchBot** 색인으로 전환 진행 중이나, **2026-07 현재도 Bing이 여전히 primary index**라는 라이브 컨센서스(전환은 진행형). → **Bing SEO를 ChatGPT 대응에서 배제하지 말 것.** 동시에 `robots.txt`에서 **OAI-SearchBot 허용도 별도 전제조건**(둘 다 필요) |

(출처: https://www.ai-advisors.ai/blog/how-to-get-cited-by-microsoft-copilot , https://www.seroundtable.com/openai-chatgpt-crawler-oai-searchbot-update-40558.html , https://developers.openai.com/api/docs/bots)

### 6.1 Bing AI Performance 리포트 (2026-02-10 public preview)

⚠ 이 기능은 지식 컷오프(2026-01) 이후 출시되었으나 라이브로 실재 확인됨(공식 블로그). 사용 전 콘솔에서 현재 제공 여부 재확인 권장.

BWT의 신규 리포트. 추적 지표:
- **Total Citations** — 선택 기간 내 AI 답변에 소스로 표시된 총 인용 수
- **Grounding Queries** — AI가 내부적으로 만든 검색어 샘플(사용자 원문 프롬프트 아님)
- **Page-level citation** — URL별 인용 수, 인용 상위 페이지 식별
- **커버 surfaces** — Microsoft Copilot, Bing 검색 내 AI 요약, "select partner integrations"

> ⚠ **핵심 한계(절대치 해석 주의):**
> - 데이터는 **샘플**이며 Copilot vs Bing AI 요약 vs 파트너 인용을 **서로 구분하지 못한다.**
> - **인용 수만 보이고 실제 클릭/방문(AI CTR)은 측정 불가**(클릭 지표·API는 2026 하반기 예정으로 알려짐).
> - "select partner integrations"에 **ChatGPT가 포함되는지는 미공개**(Bing 공식 발표가 파트너를 명시하지 않음).
>
> (출처: https://blogs.bing.com/webmaster/February-2026/Introducing-AI-Performance-in-Bing-Webmaster-Tools-Public-Preview)

**시사점:** 4개 사이트 전부 BWT 등록 후 이 리포트를 모니터링 → grounding query = "AI가 우리를 찾을 때 쓰는 실제 검색어" → 콘텐츠 헤딩/FAQ를 그 표현에 맞춰 최적화하면 AI 인용률 상승. (단 절대 인용 수는 샘플이므로 추세로만 해석.)

### 6.2 Bing 인덱스 역할 진화 — 2026 grounding 재편 (타임라인·리스크)

⚠ 아래 항목은 지식 컷오프(2026-01) 이후 사건이 다수다. **1차 소스로 인용**하고, 정확 일자·수치는 배포 전 재확인할 것. Bing 인덱스가 "사람용 랭킹"에서 "AI grounding"으로 재편되는 흐름을 인지하면 GEO 콘텐츠 설계 방향(문서 랭킹 → **문장 단위 검증가능성**)이 바뀐다.

| 시점 | 사건 | GEO 함의 |
|---|---|---|
| **2025-08-11** | **Bing Search API(raw 웹검색) 완전 은퇴** — 전 티어(F1/S1~S9, Custom Search F0/S1~S4) decommission | ⚠ **신규 GEO/검색 파이프라인을 이 API 위에 짓지 말 것.** 대체 = Azure의 **"Grounding with Bing Search"**(아래) |
| **2026-02** | Bing Search 블로그 **"Elevating the Role of Grounding on the AI Web"** — 인덱스를 AI grounding 프레임으로 재정의 | 2026-05 발표의 선행 맥락(grounding 프레임워크 부상) |
| **2026-05** | Bing Search 블로그 **"Evolving role of the index: From ranking pages to supporting answers"** — 인덱스가 **사람용 랭킹 vs AI용 grounding** 두 역할로 분리, 가치 단위가 **'문서(document)' → '검증 가능한 개별 사실(discrete verifiable facts)'로 이동** | ⚠ **1차 소스로 인용**([blogs.bing.com](https://blogs.bing.com/search/May-2026/Evolving-role-of-the-index)). **정확 일자(예: 05-06)는 블로그상 월 단위만 확인**되므로 확정 표기 지양. 2차매체(rankeo.io 등)는 참고만 |
| **Build 2026** | **Microsoft Web IQ** 발표 — AI 에이전트에 **구조화된 웹 근거**를 제공해 환각을 줄이는 Bing 기반 grounding API | 'Bing → AI grounding' 파이프라인의 최신 진화 → GEO 자체 구축 시 주시 |

**Azure "Grounding with Bing Search" (은퇴 API의 유일한 공식 후속) — 리스크:**
- **Azure AI Agents**의 grounding 기능으로 편입. ⚠ **제약**: 원본 raw 콘텐츠 접근 불가(**인용만 반환**), 비용 상승 관측(구 API 대비 **약 40~483%↑**), Azure 라이선스/아키텍처 종속.
- → GEO 파이프라인을 **자체 구축**하려는 팀에 중요한 리스크. 소규모 4개 사이트에는 과잉 — 우선은 **Bing 색인 + IndexNow + grounding 친화 콘텐츠**(무료 레버)로 충분.

**실무 결론(YMYL 특히):**
1. Bing 색인·IndexNow는 여전히 AI 가시성의 **저비용 필수 기본기**.
2. "Bing만 하면 AI 인용 보장"은 **아님** — **사실 단위·출처 명시·최신성**(grounding 친화 콘텐츠)이 문서 랭킹보다 중요해짐.
3. **YMYL 사이트(Korvia=비자/취업, Kimchi=통신/금융)** 는 **문장 단위 검증가능성 + 출처·저자·발행/수정 날짜**를 강화해야 grounding 대상이 됨. GEO 인용 레버(+41% 전문가 인용/+30% 통계·출처)는 [`./AEO_GEO_PLAYBOOK_2026.md`](./AEO_GEO_PLAYBOOK_2026.md) §2 참조.

> (출처: Bing Search API 은퇴 = https://learn.microsoft.com/en-us/lifecycle/announcements/bing-search-api-retirement · 인덱스 역할 진화 = https://blogs.bing.com/search/May-2026/Evolving-role-of-the-index)

---

## 7. 구현 — Next.js (Passome/Korvia/ACT) vs WordPress (Kimchi)

### 7.1 Next.js (App Router) — 발행 후 IndexNow 자동 핑

1. **키 파일**: `public/{key}.txt`(내용=키) 배치 → 배포 후 `https://site/{key}.txt` 접근 확인.
2. **핑 유틸**(서버 전용, 키는 `process.env`로 클라이언트 노출 방지). 한국 핵심 페이지는 Naver 직접 엔드포인트 병행:
```ts
// lib/indexnow.ts  (서버 전용 — 'use server' 컨텍스트나 route handler에서만 호출)
const HOST = 'www.example.com';
const KEY = process.env.INDEXNOW_KEY!; // .env(.local) — 절대 클라이언트 번들 유입 금지

export async function pingIndexNow(urls: string[]) {
  const body = JSON.stringify({
    host: HOST,
    key: KEY,
    keyLocation: `https://${HOST}/${KEY}.txt`,
    urlList: urls.slice(0, 10000), // 변경분만, 1요청 10k 상한
  });
  const headers = { 'Content-Type': 'application/json; charset=utf-8' };

  const generic = fetch('https://api.indexnow.org/indexnow', { method: 'POST', headers, body });
  // ⚠ 제네릭→Naver 실전파 미확인 → 한국 핵심 페이지는 Naver 직접 병행
  const naver = fetch('https://searchadvisor.naver.com/indexnow', { method: 'POST', headers, body });

  const [g, n] = await Promise.allSettled([generic, naver]);
  for (const r of [g, n]) {
    if (r.status !== 'fulfilled' || ![200, 202].includes(r.value.status)) {
      console.error('[indexnow] non-2xx or failure', r); // 성공 간주 금지 (Auto-apply #5)
    }
  }
}
```
3. **트리거**: (a) CMS 발행 webhook에서 **신규/수정 URL만** 호출, 또는 (b) `next build` 후 **post-build 스크립트**가 sitemap diff를 읽어 신규 URL 제출. Vercel Deploy Hook/webhook 연동 가능. **절대 전체 사이트맵 재핑 금지.**
4. **BWT 등록**은 GSC Import 또는 meta 태그로(§2). IndexNow 자체는 사이트 등록 없이 키만으로 동작하지만, AI Performance 리포트/사이트맵을 위해 BWT 등록 권장.

### 7.2 WordPress (Kimchi Mobile)

| 옵션 | 설명 | 권장 |
|---|---|---|
| **Bing 공식 IndexNow 플러그인** | Bing Webmaster팀 개발. Plugins→Add New→"IndexNow"→Settings»IndexNow→"Get Started" 클릭 시 **키 자동 생성·설정**. 글 생성/수정/삭제 감지 → 백그라운드 자동 제출. **사이트 등록/검증 없이도 동작** (https://wordpress.org/plugins/indexnow/) | ✅ 가장 간단 |
| **RankMath (Free 포함)** | IndexNow 내장, Google+Bing 자동 핑 옵션 | 이미 RankMath 쓰면 이걸로 |
| **AIOSEO** | IndexNow(Bing/Yandex)+Google Indexing API | 대안 |
| **Yoast** | ❌ **IndexNow 미지원**(사이트맵 핑만). Yoast만 쓰면 별도 플러그인 필요 | Bing 공식 IndexNow 플러그인 병행 |

> **먼저 확인:** Kimchi가 현재 RankMath/Yoast/AIOSEO 중 무엇을 쓰는지 → Yoast면 Bing 공식 IndexNow 플러그인 병행(Auto-apply #15).

### 7.3 (옵션) Cloudflare 레벨 자동 IndexNow

Cloudflare를 쓰는 사이트는 대시보드 토글로 **CDN 레벨에서 IndexNow를 자동 통합**할 수 있다(Next.js/WordPress 코드 변경 없이 엣지에서 핑). 4개 사이트가 Cloudflare 뒤에 있으면(Korvia IRS 스택은 Cloudflare Tunnel/R2 사용) 이 옵션을 검토 — 단 **변경분만 핑** 원칙은 동일하게 유지되는지 대시보드 설정에서 확인.

---

## 8. 4개 사이트 실행 순서 (Runbook)

1. **BWT 등록**: 4개 사이트 전부 GSC Import(동일 Google 계정) 또는 `msvalidate.01` meta 태그 검증. (§2)
2. **사이트맵 제출**: BWT Sitemaps에 각 `/sitemap.xml`. (§2.3)
3. **IndexNow 키 셋업**: 사이트별 키 생성 → `{key}.txt` 루트 호스팅 → Next.js 3사이트는 `lib/indexnow.ts` + 발행 트리거, Kimchi는 플러그인. (§7)
4. **핑 채널**: 제네릭 `api.indexnow.org/indexnow` + **한국 핵심 페이지는 Naver 직접 병행**, 변경 URL만(전체 재핑 금지). (§4.1, §4.5)
5. **스키마 삽입**: JSON-LD, 사이트별 타입(§5) → BWT URL Inspection Markup Validator로 검증.
6. **AI Performance 모니터링**: Total Citations/Grounding Queries 주기 확인(추세로만) → grounding query에 맞춰 헤딩·FAQ 조정. (§6.1)
7. **ChatGPT는 Bing에만 의존하지 말 것**: `robots.txt`에서 `OAI-SearchBot` 허용(WEB_PLAYBOOK §1.1) + 콘텐츠 품질/스키마로 대응. (§6)
8. **YMYL 게이트**: Korvia(비자/취업)·Kimchi(통신/요금) 페이지는 정확성 검수 통과 후에만 핑. (§9)

---

## 9. 한계·주의 (Flags)

- **⚠ Google은 IndexNow·Bing 채널과 무관** — Google 색인은 GSC/사이트맵/(제한적) Indexing API로 별도 관리(WEB_PLAYBOOK). IndexNow로 Google을 다루려 하지 말 것.
- **⚠ IndexNow ≠ 색인 보장** — 빠른 "발견/재크롤" push일 뿐, 순위·색인 채택은 별개.
- **⚠ 제네릭 엔드포인트의 Naver 실전파 공식 미확인** — 한국인 청중 확실 커버는 `searchadvisor.naver.com/indexnow` 병행 핑.
- **⚠ IndexNow 키 문자셋은 `a-z/A-Z/0-9/-`** (공식 문서가 "hex"라 부르지만 모순). "16진수만"으로 오해 금지.
- **⚠ 신규 사이트 Bing 일일 쿼터 "100"은 서드파티 수치** — 실값은 BWT "Submit URLs"에서 확인.
- **⚠ AI Performance 리포트는 샘플 데이터** — Copilot/Bing요약/파트너 인용 구분 불가, 클릭 수 없음. 절대치 해석 주의, 추세로만.
- **⚠ ChatGPT↔Bing 상관은 "약화 중"이지만 2026-07 현재 Bing이 여전히 primary index**(전환 진행형) — Bing SEO를 ChatGPT 대응에서 배제하지 말 것. 동시에 `OAI-SearchBot` robots.txt 허용도 필수.
- **⚠ Yahoo는 Bing 전속이 아님**(공급자 유연성 보유) — "자동 노출"은 유기 결과에 한한 근사.
- **⚠ YMYL 위험(Korvia 비자·Kimchi 통신/요금)** — 오래된 절차/가격이 IndexNow로 빠르게 색인되면 오히려 AI(Copilot/Yahoo)가 **부정확한 최신 버전**을 인용할 위험. **발행 전 정확성 게이트 통과 후 핑.**
- **⚠ 429/스팸 방지** — 변경분만, 배치, 중복 핑 자제.
- **⚠ Bing Search API(raw 웹검색)는 2025-08-11 완전 은퇴** — 신규 GEO/검색 파이프라인을 이 API 위에 짓지 말 것. 대체 = Azure "Grounding with Bing Search"(raw 콘텐츠 접근 불가·인용만 반환·비용 40~483%↑). (§6.2)
- **⚠ 2026-05 "Bing 인덱스 = AI grounding용" 발표는 1차 소스(공식 Bing 블로그 May 2026 "Evolving role of the index")로 인용**하고, 정확 일자(05-06)는 확정 표기 지양(블로그상 월 단위만 확인). 가치 단위가 '문서'→'검증 가능한 사실'로 이동 → **문장 단위 검증가능성·출처·저자·날짜 강화**. (§6.2)
- **⚠ IndexNow 사용 사이트 수는 "약 60M+"로 표기** — "80M+"는 근거가 약함.
- **⚠ Bing Markup Validator·GSC→BWT 임포트·BWT API 키(사용자당 1개)는 장기 유지 기능이나 콘솔/도구 개편 이력** — 배포 전 콘솔 실화면으로 최종 확인.

---

## Sources

**IndexNow (공식)**
- IndexNow Protocol Documentation — https://www.indexnow.org/documentation
- Participating search engines (list) — https://www.indexnow.org/searchengines
- Participating search engines meta JSON (라이브) — https://www.indexnow.org/searchengines.json
- IndexNow FAQ — https://www.indexnow.org/faq
- Bing: How to add IndexNow to your website — https://www.bing.com/indexnow/getstarted

**Bing Webmaster Tools (공식)**
- Why IndexNow / URL Submission API — https://www.bing.com/webmasters/url-submission-api
- GetUrlSubmissionQuota Method — https://learn.microsoft.com/en-us/dotnet/api/microsoft.bing.webmaster.api.interfaces.iwebmasterapi.geturlsubmissionquota
- Submit up to 10,000 URLs per day (2019-01) — https://blogs.bing.com/webmaster/january-2019/bingbot-Series-Get-your-content-indexed-fast-by-now-submitting-up-to-10,000-URLs-per-day-to-Bing
- Access to Instant Indexing: Bing URL submission API (2021-09) — https://blogs.bing.com/webmaster/September-2021/Access-to-Instant-Indexing-Bing-URL-submission-API
- Introducing AI Performance in BWT (Public Preview, 2026-02-10) — https://blogs.bing.com/webmaster/February-2026/Introducing-AI-Performance-in-Bing-Webmaster-Tools-Public-Preview
- Marking Up Your Site with Structured Data — https://www.bing.com/webmasters/help/marking-up-your-site-with-structured-data-3a93e731
- Introducing JSON-LD Support (2018-08) — https://blogs.bing.com/webmaster/august-2018/Introducing-JSON-LD-Support-in-Bing-Webmaster-Tools
- URL Submission (help) — https://www.bing.com/webmasters/help/URL-Submission-62f2860b
- Add and Verify Site (help) — https://www.bing.com/webmasters/help/add-and-verify-site-12184f8b
- Building the New Bing (Prometheus/Bing Orchestrator, 2023-02) — https://blogs.bing.com/search-quality-insights/february-2023/Building-the-New-Bing
- ⚠ Evolving role of the index (인덱스 역할 분리: 문서→검증가능한 사실, 2026-05) — https://blogs.bing.com/search/May-2026/Evolving-role-of-the-index
- Bing Search API retirement (2025-08-11, 대체=Azure Grounding with Bing Search) — https://learn.microsoft.com/en-us/lifecycle/announcements/bing-search-api-retirement

**WordPress / Next.js 구현**
- IndexNow Plugin (official, by Bing Webmaster team) — https://wordpress.org/plugins/indexnow/
- How to Use IndexNow With Rank Math — https://rankmath.com/kb/how-to-use-indexnow/
- How to Index Next.js Apps Faster Using IndexNow (freeCodeCamp) — https://www.freecodecamp.org/news/how-to-index-nextjs-pages-with-indexnow/
- How to Add IndexNow in WordPress (WPBeginner) — https://www.wpbeginner.com/wp-tutorials/how-to-add-indexnow-in-wordpress-to-speed-up-seo-results/

**AI / GEO grounding**
- Overview of OpenAI Crawlers (OAI-SearchBot) — https://developers.openai.com/api/docs/bots
- OpenAI Updates ChatGPT Crawler OAI-SearchBot (2025-12) — https://www.seroundtable.com/openai-chatgpt-crawler-oai-searchbot-update-40558.html
- How to Get Cited by Microsoft Copilot (Prometheus/Bing index) — https://www.ai-advisors.ai/blog/how-to-get-cited-by-microsoft-copilot

**배경/서드파티**
- Naver now supports IndexNow (Search Engine Land, 2023-07) — https://searchengineland.com/naver-korean-search-engine-now-supports-indexnow-429880
- IndexNow — Microsoft & Yandex initiative (SEL, 2021-10) — https://searchengineland.com/indexnow-new-initiative-by-microsoft-and-yandex-to-push-content-to-search-engines-375247
- Bing Allows Submit Up to 10,000 URLs/Day (SEJ, 2019-04) — https://www.searchenginejournal.com/bing-allows-site-owners-to-submit-up-to-10000-urls-per-day/290801/
- IndexNow — Wikipedia — https://en.wikipedia.org/wiki/IndexNow
- Yahoo Search — Wikipedia — https://en.wikipedia.org/wiki/Yahoo_Search
- DuckDuckGo — Wikipedia — https://en.wikipedia.org/wiki/DuckDuckGo

---

> **분업 재확인:** AI 크롤러 robots.txt 허용 목록 = [`./WEB_PLAYBOOK.md`](./WEB_PLAYBOOK.md) §1.1 · JSON-LD 빌드 헬퍼 = WEB_PLAYBOOK §2 / [`./NEW_PAGE_CHECKLIST.md`](./NEW_PAGE_CHECKLIST.md) · GEO 전략/플랫폼별 인용 = [`./AEO_GEO_PLAYBOOK_2026.md`](./AEO_GEO_PLAYBOOK_2026.md) · llms.txt = AEO_GEO_PLAYBOOK §4 / WEB_PLAYBOOK §1.3. 이 문서는 그 위에 **Bing 계열 + IndexNow**만 얹는다.
