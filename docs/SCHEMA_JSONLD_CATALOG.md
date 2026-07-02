---
last_verified: 2026-07
reverify_by: 2026-Q4
---

# JSON-LD 구조화데이터 카탈로그 (복붙 템플릿)

> Generated 2026-07-01 · Owner: Henry Oh (KORVIA) · ⚠ 콘솔/API 변경은 사용 전 재확인
>
> 페이지 타입별 **복사-붙여넣기 JSON-LD 템플릿**을 모은 실무 카탈로그다. 각 타입마다 (1) 언제 쓰나, (2) 필수/권장 필드, (3) 어떤 엔진이 인정하나(Google 리치결과 + Naver ⚠), (4) 검증 도구, (5) 4개 프로젝트(Passome·Korvia·Kimchi·ACT) 예시를 담는다.
>
> **역할 분담 (중복 금지 · 상호링크):**
> - 전략/근거·GEO 왜/무엇 → [`AEO_GEO_PLAYBOOK_2026.md`](./AEO_GEO_PLAYBOOK_2026.md)
> - 페이지 기술 셋업(metadata·CLS·a11y·헬퍼함수 `buildBreadcrumbJsonLd`/`buildFaqJsonLd`/`buildServiceJsonLd`/`buildArticleJsonLd`) → [`WEB_PLAYBOOK.md`](./WEB_PLAYBOOK.md) §2, [`NEW_PAGE_CHECKLIST.md`](./NEW_PAGE_CHECKLIST.md) §B
> - **이 문서 = "스키마 원본(raw JSON-LD)" 카탈로그.** WordPress(Kimchi)처럼 Next.js 헬퍼가 없는 스택에서도 그대로 붙여 쓰는 소스이자, 2026 리치결과 생사(生死) 판정의 단일 기준.
> - 플레이스홀더 원본(placeholder JSON) → [`../.claude/skills/seo-harness-team/resources/schema-templates/`](../.claude/skills/seo-harness-team/resources/schema-templates/) (organization/article/faqpage/localbusiness). 이 카탈로그는 그 4종을 최신화·확장하고 9종을 추가한다.

---

## 0. 자동 적용 규칙 (Auto-apply)

작성자/에이전트가 기계적으로 따를 항목. 페이지에 스키마를 넣기 전 이 규칙을 먼저 통과시킨다.

1. **죽은 스키마를 새로 생성하지 마라.** `FAQPage`·`HowTo`·`WebSite > potentialAction(SearchAction, Sitelinks Searchbox)`·`Course`(Course *Info*)·`Special Announcement`·`Estimated Salary`·`Learning Video`·`Vehicle Listing`·`ClaimReview`·`Practice Problem` = Google 리치결과 폐지(§2). 기존 것은 제거하되, 남겨도 Search Console 에러는 아님.
2. **모든 사이트 홈에 `Organization` 1개** — `name`·`url`·`logo`(정사각 고해상)·`sameAs`·`knowsAbout`·`founder`·`foundingDate`·`contactPoint`. `@id`로 그래프 앵커(§6.1).
3. **모든 색인 페이지에 `BreadcrumbList`**(§6.4).
4. **블로그/가이드 = `Article`/`BlogPosting`** + 실명 `author`(Person) + `author.url`/`sameAs` + `datePublished`/`dateModified`(ISO 8601 + 타임존). 저자 여러 명은 개별 필드로 분리(§6.5, §6.13).
5. **Korvia 채용 상세 = `JobPosting`** 필수 5필드 + `validThrough` + 만료 시 제거. **리스트/검색 페이지엔 붙이지 마라**(§6.7).
6. **Kimchi eSIM 상품 = `Product` + `offers`**(`price`·`priceCurrency`·`availability`·`priceValidUntil`). **자사가 파는 Product에는 진짜 고객이 남긴 `review`/`aggregateRating` 별점 마크업이 허용된다**(Google 공식 지원 → Kimchi eSIM 정품 고객 리뷰 수집 시 상품 별점 리치 획득 권장). self-serving 별점 금지는 **Organization/LocalBusiness에만** 적용(§6.10~6.11).
7. **ACT 센터 = `LocalBusiness`** + `Course`는 캐러셀(`ItemList`)로만 리치 기대(§6.2, §6.8).
8. **포맷은 항상 JSON-LD**(Google 권장). `<script type="application/ld+json">`. Microdata/RDFa 신규 도입 금지.
9. **엔티티 값은 페이지 가시 콘텐츠와 일치.** 화면에 없는 정보를 스키마에만 넣지 마라(정책 위반).
10. **발행 전 2-도구 검증**: Rich Results Test(리치결과 자격) + Schema Markup Validator(문법). 통과 못 하면 반려(§4).
11. **⚠ Naver 리치결과는 JSON-LD로 보장되지 않는다**(공식 미문서화). JSON-LD는 **Google + AI 인용(AI Overviews/AI 브리핑/ChatGPT 등) 목적**으로만 삽입하고, Naver 노출은 **Open Graph + 한국어 본문**으로 별도 확보(§3, §8).
12. **`og:image` 값은 5MB 이하 · 1200×630**로 통일(§8). `og:url` = canonical.

---

## 1. 2026 한눈 요약 — "무엇을 넣고, 무엇을 버리나"

| 넣어라 (리치결과 살아있음) | 버려라 (리치결과 폐지 · 신규 생성 금지) |
|---|---|
| Organization, LocalBusiness, BreadcrumbList | ~~FAQPage~~ (2026-05-07 표시 종료) |
| Article / BlogPosting / NewsArticle | ~~HowTo~~ (2023~ 폐지) |
| JobPosting (만료관리 필수) | ~~WebSite > SearchAction (Sitelinks Searchbox)~~ (2024-11-21) |
| Product + Offer / Merchant listing | ~~Course Info~~ (2025-09-09) — Course *list* 캐러셀만 잔존 |
| Review snippet / AggregateRating (Product=실고객 별점 허용 / Org·Local=자사 통제 리뷰 부적격) | ~~Estimated Salary / Learning Video / Special Announcement / Vehicle Listing / ClaimReview~~ (2025-09-09) |
| Event, VideoObject | ~~Practice Problem / Nutrition facts / Nearby offers / Vacation rental~~ (2026-01 배치) |
| Course *list* 캐러셀(ItemList), Person(author), WebSite(name/alternateName) | (Dataset = Google **Dataset Search** 전용, 일반 Search 리치 아님 → 4개 사이트 비적용) |

> ✅ **Book Actions는 살아있다** — 2025-06 폐지 예고 대상이었으나 **2025-11 폐지가 철회**(Google이 문서의 deprecation 배너 제거, "여전히 이 마크업을 사용")됨. "사용 금지"로 잘못 알려진 케이스이니 4개 사이트에 해당하면(도서 액션) 계속 사용 가능. 출처: [etavrian.com/news](https://www.etavrian.com/news/google-deprecates-practice-problem-structured-data).

---

## 2. 스키마 생사표 (Google 공식 changelog 기준)

⚠ 아래 폐지는 **랭킹에 영향 없음**(색인·크롤은 그대로, 시각적 리치 표시만 사라짐). 죽은 스키마는 유지비만 발생.

| 타입 | 상태 | 공식 시점 | 근거 |
|---|---|---|---|
| **FAQPage** | ⚠ 리치결과 **완전 폐지** (문법·파싱은 유효) | 검색 미표시 **2026-05-07** → 문서 제거 2026-06-15 → RRT 필터 제거 2026-06 → **API 지원 2026-08 종료** | [Search Central changelog](https://developers.google.com/search/updates) · [FAQPage docs](https://developers.google.com/search/docs/appearance/structured-data/faqpage) |
| **HowTo** | ⚠ 완전 폐지(마크업 무시) | 2023-08 축소 → 완전 제거 | [howto-faq-changes](https://developers.google.com/search/blog/2023/08/howto-faq-changes) |
| **WebSite > SearchAction** (Sitelinks Searchbox) | ⚠ 폐지 (SC 리포트·RRT 하이라이트 제거) | **2024-11-21** (예고 10-21) | [sitelinks-search-box](https://developers.google.com/search/blog/2024/10/sitelinks-search-box) |
| **Course Info** | ⚠ 폐지 (Course *list* 캐러셀만 잔존) | 예고 2025-06-12 → 제거 **2025-09-09** | Search Central changelog |
| **Estimated Salary / Learning Video / Special Announcement / Vehicle Listing / ClaimReview** | ⚠ 폐지 (5종, 9/9 배치) | 2025-06-12 → **2025-09-09** | [Search Engine Land](https://searchengineland.com/google-drops-reporting-on-several-structured-data-types-461744) |
| **Book Actions** | ✅ **살아있음 (폐지 철회)** | 2025-06 예고 → **2025-11 철회** | [etavrian.com](https://www.etavrian.com/news/google-deprecates-practice-problem-structured-data) |
| **Practice Problem** (+ Nutrition facts, Nearby offers, Vacation rental) | ⚠ 폐지 (2026-01 배치) | 예고 2025-11-05 → 제거 **2026-01** | [Search Engine Land](https://searchengineland.com/google-to-remove-more-search-features-including-practice-problems-nutrition-facts-nearby-offers-and-more-464255) |
| Organization / LocalBusiness / BreadcrumbList / Article / JobPosting / Product / Review / Event / Video / Course *list* / Profile·Q&A·Discussion | ✅ 유효 | 2026-07 Search Gallery 확인 | [Search Gallery](https://developers.google.com/search/docs/appearance/structured-data/search-gallery) |

> Google 기조: **"검색결과 페이지 단순화"**([2025-06 블로그](https://developers.google.com/search/blog/2025/06/simplifying-search-results)) — 저활용·저가치 리치결과 대량 정리 중. **AI Overviews/AI Mode에 뜨려고 특수 스키마·llms.txt를 만들 필요 없음**(Google 공식). 구조화데이터는 리치결과 자격 유지 목적으로만 계속 권장.
>
> **[검증 2026-07]** 폐지 5종(**Course Info · Estimated Salary · Learning Video · Special Announcement · Vehicle Listing**)의 2025-09 폐지와 **Practice Problem**의 2026-01 폐지는 Google Search Central 'What's new' changelog로 재확인됨(출처: [developers.google.com/search/updates](https://developers.google.com/search/updates)). ⚠ **Estimated Salary 경고**: 이 리치결과 유형은 폐지됐으므로 신규 생성하지 마라 — Korvia 채용 페이지의 급여 정보는 JobPosting의 `baseSalary`(실제 급여)로만 표기하고 별도 Estimated Salary 스키마와 혼동·중복 삽입하지 않는다.

---

## 3. 엔진 인정 매트릭스 (Google vs Naver ⚠)

| 스키마 | Google 리치결과 | Naver | 이 카탈로그 처리 |
|---|---|---|---|
| Organization | ✅ 로고·지식패널·사이트명 | ⚠ 미문서화 (OG·소셜채널 마크업은 인식) | 넣음(§6.1) |
| LocalBusiness | ✅ 로컬팩·지식패널 | ⚠ 실질은 **스마트플레이스**가 지배 | 넣되 Naver는 플레이스 병행(§6.2) |
| WebSite (name/alternateName) | ✅ 사이트명 | ⚠ 미문서화 | 넣음. SearchAction은 넣지 마라(§6.3) |
| BreadcrumbList | ✅ 경로 표시 | ⚠ 미문서화 | 넣음(§6.4) |
| Article/BlogPosting | ✅ 톱스토리·저자 | ⚠ 미문서화 (Naver는 C-Rank·D.I.A로 별도 평가) | 넣음(§6.5) |
| FAQPage | ⚠ **폐지** | ⚠ 미문서화 | **신규 생성 금지**, 본문 Q&A만(§6.6) |
| JobPosting | ✅ 채용 리치 | ⚠ 미문서화 | 넣음(§6.7) |
| Course | ⚠ Info 폐지 / list 캐러셀만 ✅ | ⚠ 미문서화 | 캐러셀로만(§6.8) |
| Event | ✅ 이벤트 리치 | ⚠ 미문서화 | 넣음(§6.9) |
| Product + Offer | ✅ 상품 스니펫·머천트 | ⚠ 미문서화 (Naver는 쇼핑 별도 생태계) | 넣음(§6.10) |
| Review / AggregateRating | ✅ 별점 — Product는 실고객 리뷰면 자사도 허용 / Org·LocalBusiness 자사 통제 리뷰만 부적격 | ⚠ 미문서화 | Product 실고객 별점 권장·Org/Local self-serving 금지(§6.11) |
| VideoObject | ✅ 영상 리치·키모먼트 | ⚠ 미문서화 (Naver TV 별도) | 넣음(§6.12) |
| Person (author) | ✅ 저자 신뢰·프로필 페이지 | ⚠ 미문서화 | 넣음(§6.13) |

> ⚠ **UNVERIFIABLE — Naver의 schema.org JSON-LD 리치결과 렌더 지원 범위는 Naver 공식 문서에 없다.** 영문 대행사 가이드가 "FAQ/Product/Review 스키마가 도움된다"고 하나 1차 근거 없음. **확실한 것은 Open Graph 태그 권장뿐.** 따라서 JSON-LD는 Google/AI-인용용으로 삽입하고, Naver 노출은 [`AEO_GEO_PLAYBOOK_2026.md`](./AEO_GEO_PLAYBOOK_2026.md) 및 별도 Naver 랭킹 문서(C-Rank·D.I.A+·스마트블록·AI 브리핑)를 따른다. Naver는 **hreflang 미준수** → 한국어 자산은 위젯·메뉴·이미지 텍스트까지 전부 한국어(§8).

---

## 4. 검증 워크플로 (도구)

| 목적 | 도구 | URL | 비고 |
|---|---|---|---|
| **리치결과 자격** 확인 | Rich Results Test | https://search.google.com/test/rich-results | ⚠ 2026-06부터 **FAQ 필터 제거** — FAQPage는 여기서 리치 안 뜸(정상) |
| **문법(스키마 유효성)** 검증 | Schema Markup Validator | https://validator.schema.org/ | schema.org 공식. 리치결과와 무관하게 구조 검증 |
| 배포 후 실측 | Google Search Console → 향상(Enhancements) 리포트 | https://search.google.com/search-console | 색인·리치결과 상태. 반영 1~7일 |
| Naver 색인/사이트맵 | 네이버 서치어드바이저 | https://searchadvisor.naver.com/ | JSON-LD 리치 검증 도구는 없음. `site:` 연산자로 색인 확인 |
| 대규모 감사 | Screaming Frog (Structured Data 탭) | — | 사이트 전체 스키마 누락/에러 크롤 |

**발행 전 게이트(복붙):**
```bash
# 배포된 페이지에서 canonical + 주입된 @type 목록 확인
# (공백/pretty-print "@type": "Article" 와 듀얼타입 배열 ["Organization","ProfessionalService"] 모두 포착)
curl -sL https://<site>/<page> | grep -oE '<link rel="canonical"[^>]*>|"@type"[[:space:]]*:[[:space:]]*(\[[^]]*\]|"[^"]+")'
# 더 견고: <script type="application/ld+json"> 블록을 jq/JSON-LD 추출기로 파싱해 @type 목록을 뽑는 방식 권장.
```

---

## 5. 공통 삽입 규칙

- **배치**: `<head>` 또는 `<body>` 어디든 OK. `<script type="application/ld+json">{ ... }</script>`. 페이지당 여러 개 가능(Article + Breadcrumb 공존).
- **@id 그래프**: 루트에서 `Organization`·`WebSite`에 `@id`(예: `https://site.com/#org`)를 주고, 하위 페이지 스키마가 `publisher`/`provider`/`author.worksFor`에서 `{ "@id": "https://site.com/#org" }`로 참조 → 엔티티 일관성 신호. Kimchi(WordPress)도 동일하게 `#org`를 재사용.
- **Next.js (Passome/Korvia/ACT)**: [`WEB_PLAYBOOK.md`](./WEB_PLAYBOOK.md) §2의 `<JsonLd data={...} />` + `buildXxxJsonLd()` 헬퍼 사용. 루트 `Organization`/`WebSite`는 `app/layout.tsx`에 1회만(재주입 금지). 이 카탈로그의 raw JSON은 헬퍼 입력값 설계 참조로 사용.
- **WordPress (Kimchi)**: 테마 `header.php` 또는 Yoast/Rank Math 스키마 블록에 삽입. 플러그인 자동 Organization/WebSite와 **중복 주입 금지**(하나만).
- **날짜**: 전부 ISO 8601 + 타임존. 예: `2026-07-01T09:00:00+09:00`.
- **이미지**: 절대 URL, 고해상. Article 대표이미지는 16:9/4:3/1:1 3종 제공 권장(≥50K px).

---

## 6. 카탈로그 — 페이지 타입별 템플릿

각 항목: **언제 · 필수 · 권장 · 엔진 · 검증 · 4프로젝트 예시**.

---

### 6.1 Organization — 브랜드 SERP · 로고 · 지식패널

- **언제**: 모든 사이트 홈(루트)에 정확히 1개.
- **필수**: `name`, `url`. **강력 권장**: `logo`(정사각 고해상), `sameAs`(공식 SNS/LinkedIn 배열), `knowsAbout`(전문 주제), `founder`, `foundingDate`, `contactPoint`, `address`.
- **엔진**: Google ✅(로고·지식패널·사이트명 노출에 직접 반영). Naver ⚠ 미문서화(소셜채널 연결 마크업·OG는 인식).
- **검증**: Schema Validator(문법) + RRT(로고·조직).
- **팁**: WEB_PLAYBOOK 패턴대로 `@type: ["Organization", "ProfessionalService"]` 듀얼 타입 + `@id` 권장. YMYL(Korvia/Kimchi)은 `knowsAbout`로 전문영역을 명시해 신뢰 강화.

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": ["Organization", "ProfessionalService"],
  "@id": "https://www.korvia.com/#org",
  "name": "Korvia Consulting",
  "alternateName": "코비아컨설팅",
  "url": "https://www.korvia.com",
  "logo": {
    "@type": "ImageObject",
    "url": "https://www.korvia.com/logo-512.png",
    "width": 512,
    "height": 512
  },
  "description": "Recruitment and visa consulting for foreigners seeking jobs in South Korea (EPIK, native English teaching).",
  "foundingDate": "2013",
  "founder": { "@type": "Person", "name": "Henry Oh" },
  "address": {
    "@type": "PostalAddress",
    "addressLocality": "Seoul",
    "addressCountry": "KR"
  },
  "contactPoint": {
    "@type": "ContactPoint",
    "contactType": "customer service",
    "email": "henry@korvia.com",
    "availableLanguage": ["English", "Korean"]
  },
  "knowsAbout": ["EPIK", "E-2 visa", "teaching English in Korea", "native teacher recruitment"],
  "sameAs": [
    "https://www.linkedin.com/company/korvia",
    "https://www.instagram.com/korvia",
    "https://www.facebook.com/korvia"
  ]
}
</script>
```

**4프로젝트 예시 (핵심 필드만):**
| 사이트 | @id | knowsAbout (예) | 비고 |
|---|---|---|---|
| Passome | `passome.com/#org` | `["AI consulting","AX transformation","art psychotherapy"]` | 컨설팅 엔티티 강화, 저자 E-E-A-T와 연결 |
| Korvia | `korvia.com/#org` | `["EPIK","E-2 visa","native teacher recruitment"]` | YMYL, `founder`·`foundingDate`로 신뢰 |
| Kimchi | `kimchimobile.com/#org` | `["eSIM Korea","prepaid SIM for foreigners","mobile plans"]` | YMYL(통신/결제), 정확 요금정보 신뢰 |
| ACT | `actartcenter.com/#org` | `["art education","drawing classes","art center"]` | LocalBusiness와 `@id` 공유 |

---

### 6.2 LocalBusiness — 로컬팩 · 지도 · 지식패널

- **언제**: 오프라인 접점이 있는 사이트(ACT 센터, Korvia/Kimchi 사무실).
- **필수**: `name`, `address`. **권장**: `geo`, `telephone`, `openingHoursSpecification`, `priceRange`, `image`, `url`, `@id`(Organization와 동일 앵커 재사용 가능).
- **엔진**: Google ✅. **Naver ⚠ — 한국 로컬은 스마트플레이스가 실질 지배**. JSON-LD보다 네이버 플레이스 등록·리뷰·행동데이터가 노출을 좌우.
- **검증**: Schema Validator + RRT(Local business).
- **세부 타입**: 미술센터는 `@type: ["LocalBusiness","EducationalOrganization"]` 조합 가능.

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": ["LocalBusiness", "EducationalOrganization"],
  "@id": "https://actartcenter.com/#org",
  "name": "ACT Art Center",
  "url": "https://actartcenter.com",
  "image": "https://actartcenter.com/center-photo.jpg",
  "description": "Art education center offering drawing and painting classes.",
  "telephone": "+82-2-000-0000",
  "priceRange": "₩₩",
  "address": {
    "@type": "PostalAddress",
    "streetAddress": "...",
    "addressLocality": "Seoul",
    "addressRegion": "Seoul",
    "postalCode": "0XXXX",
    "addressCountry": "KR"
  },
  "geo": { "@type": "GeoCoordinates", "latitude": "37.xxxx", "longitude": "127.xxxx" },
  "openingHoursSpecification": [{
    "@type": "OpeningHoursSpecification",
    "dayOfWeek": ["Monday","Tuesday","Wednesday","Thursday","Friday"],
    "opens": "10:00", "closes": "19:00"
  }],
  "sameAs": ["https://www.instagram.com/actartcenter"]
}
</script>
```
> ACT는 이 스키마 + **네이버 스마트플레이스 최적화**(업종 카테고리 정확·사진·수업정보·영수증 리뷰·방문/전화/길찾기 행동데이터)를 병행해야 한국 로컬 노출이 잡힌다. 참조: 별도 Naver 랭킹 문서.

---

### 6.3 WebSite — 사이트명 (⚠ SearchAction/Sitelinks Searchbox는 폐지)

- **언제**: 루트 1개. 검색결과 표기 사이트명 제어.
- **필수**: `name`, `url`. **권장**: `alternateName`(한/영), `publisher: { @id: #org }`.
- **⚠ 넣지 마라**: `potentialAction: SearchAction`(Sitelinks Searchbox) — **2024-11-21 폐지**. 시각요소·SC 리포트·RRT 하이라이트 모두 제거됨. 남겨도 무해하나 신규 생성 불필요.
- **엔진**: Google ✅(사이트명은 여전히 지원). Naver ⚠.

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "WebSite",
  "@id": "https://www.kimchimobile.com/#website",
  "name": "Kimchi Mobile",
  "alternateName": "김치모바일",
  "url": "https://www.kimchimobile.com",
  "publisher": { "@id": "https://www.kimchimobile.com/#org" },
  "inLanguage": ["en", "ko"]
}
</script>
```
> 사이트링크(브랜드 검색 시)는 마크업으로 못 만든다 — 명확한 네비게이션 계층·서술적 페이지 제목·내부링크로 간접 유도.

---

### 6.4 BreadcrumbList — 경로 표시 (전 페이지 필수급)

- **언제**: 모든 hub/detail 페이지.
- **필수**: `itemListElement`(각 `ListItem`에 `position`, `name`, `item`[마지막 항목은 `item` 생략 가능]).
- **엔진**: Google ✅. Naver ⚠.

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "BreadcrumbList",
  "itemListElement": [
    { "@type": "ListItem", "position": 1, "name": "Home", "item": "https://www.korvia.com" },
    { "@type": "ListItem", "position": 2, "name": "Programs", "item": "https://www.korvia.com/programs" },
    { "@type": "ListItem", "position": 3, "name": "EPIK" }
  ]
}
</script>
```
> Next.js는 `buildBreadcrumbJsonLd([...])` 헬퍼 사용([`WEB_PLAYBOOK.md`](./WEB_PLAYBOOK.md) §2.2). 3층 예: Home → [Program] Hub → (현재).

---

### 6.5 Article / BlogPosting / NewsArticle — 저자 · 최신성 · E-E-A-T

- **언제**: 블로그·가이드·매거진. 시의성 저널리즘은 `NewsArticle`.
- **필수 속성 없음**(Google) — "적용되는 속성을 넣어라". **실무 권장(사실상 필수)**:
  - `headline`(짧게), `image`(고해상, 로고 아닌 본문 이미지)
  - `author`: `Person`/`Organization` + `author.name`(직함·매체명 제외) + **`author.url` 또는 `sameAs`(저자 바이오/자격 링크)** ← **E-E-A-T 핵심**
  - `datePublished` / `dateModified` (ISO 8601 + 타임존)
  - `publisher: { @id: #org }`, `mainEntityOfPage`
  - 저자 여러 명 → **개별 `author` 필드로 분리**(합치지 말 것)
- **엔진**: Google ✅. Naver ⚠(Naver는 C-Rank/D.I.A로 별도 평가 → JSON-LD보다 원본성·경험·체류가 결정).
- **검증**: RRT(Article) + Schema Validator.

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "BlogPosting",
  "headline": "E-2 비자 발급에 필요한 서류 총정리 (2026년 기준)",
  "description": "한국에서 원어민 교사로 일하려는 외국인을 위한 E-2 비자 서류 체크리스트.",
  "image": [
    "https://www.korvia.com/blog/e2-visa-16x9.jpg",
    "https://www.korvia.com/blog/e2-visa-4x3.jpg",
    "https://www.korvia.com/blog/e2-visa-1x1.jpg"
  ],
  "author": {
    "@type": "Person",
    "name": "Henry Oh",
    "url": "https://www.korvia.com/authors/henry-oh",
    "jobTitle": "CEO, Korvia Consulting",
    "worksFor": { "@id": "https://www.korvia.com/#org" }
  },
  "publisher": { "@id": "https://www.korvia.com/#org" },
  "datePublished": "2026-07-01T09:00:00+09:00",
  "dateModified": "2026-07-01T09:00:00+09:00",
  "mainEntityOfPage": { "@type": "WebPage", "@id": "https://www.korvia.com/blog/e2-visa-documents" },
  "inLanguage": "ko"
}
</script>
```

**4프로젝트 예시:**
| 사이트 | 타입 | 저자 신뢰 포인트 |
|---|---|---|
| Passome | Article | AI/AX 전문가 바이오(`author.url`), AIO 인용 노림 |
| Korvia | BlogPosting | **YMYL** — 저자 자격 + 1차 법령 인용(본문) + `dateModified` |
| Kimchi | BlogPosting | **YMYL** — 요금/약관 최신성, `dateModified` 갱신 필수 |
| ACT | Article | 강사/작가 프로필 연결 |

---

### 6.6 FAQPage — ⚠ 리치결과 폐지 (신규 생성 금지)

- **⚠ 상태**: Google FAQ 리치결과 **2026-05-07 검색 미표시**, 문서 제거 2026-06-15, API 지원 2026-08 종료. **새 FAQPage JSON-LD를 생성하지 마라.** 문법 자체는 유효하고 Google이 파싱은 하나 **리치결과 자산으로 홍보 금지**.
- **대신**: 본문에 **Q&A 텍스트 구조**를 유지하라 — PAA(People Also Ask)·AI Overviews·피처드 스니펫 인용에 **여전히 유효**. 질문형 소제목 + 40~60단어 즉답.
- **⚠ 파싱 뉘앙스**: "Google이 FAQPage를 파싱한다"는 것은 **문법을 읽어 콘텐츠를 이해**한다는 의미일 뿐, FAQPage 스키마가 있어야 검색 이해나 AI Overviews/AI Mode 인용이 잘 된다는 뜻은 **아니다**. Google 공식(AI 최적화 가이드, 갱신 2026-06-29)은 **생성형 AI 노출에 특수 마크업·기계판독 파일·콘텐츠 청킹이 불필요**하고 표준 SEO·본문 텍스트가 토대라고 명시한다 → 인용/이해 목적으로 FAQPage를 새로 만들 이유도 없다(본문 Q&A 텍스트로 충분). 출처: [ai-optimization-guide](https://developers.google.com/search/docs/fundamentals/ai-optimization-guide).
- **엔진**: Google ⚠(리치 없음, 파싱만). Naver ⚠.
- **정정 노트**: [`WEB_PLAYBOOK.md`](./WEB_PLAYBOOK.md) §2.2/§2.3과 [`NEW_PAGE_CHECKLIST.md`](./NEW_PAGE_CHECKLIST.md) §B-8의 "FAQPage 주입" 항목은 이 2026 폐지 이전 작성분 → **"리치결과 폐지, 본문 Q&A만"으로 하향** 적용.

```html
<!-- 참고용(리치결과 안 뜸). LLM 파싱 목적으로만 남길 수 있으나 신규 권장 아님 -->
<!-- 권장: 아래처럼 마크업 없는 본문 Q&A로 작성 -->
<h2>E-2 비자에 재직증명서가 필요한가요?</h2>
<p>네. E-2 비자 신청 시 최종 학력 증명과 함께 …(40~60단어 즉답)…</p>
```

---

### 6.7 JobPosting — 채용 리치결과 (Korvia 핵심)

- **언제**: **개별 채용 상세 페이지에만.** ⚠ 리스트/검색결과 페이지엔 붙이지 마라(가이드라인 위반).
- **필수 5**: `datePosted`(ISO 8601), `description`(HTML 전체 상세), `hiringOrganization`(회사명, 지점 아님), `jobLocation`(**반드시 `addressCountry` 포함**), `title`(코드·급여·주소 제외한 순수 직무명).
- **권장**: `baseSalary`(`currency` + `value.unitText`=HOUR/DAY/WEEK/MONTH/YEAR), `employmentType`(FULL_TIME 등), `validThrough`(마감일 ISO 8601), `identifier`, `directApply`(bool).
- **⚠ 원격 전용 필드**: `jobLocationType`="TELECOMMUTE" + `applicantLocationRequirements`(원격 근무자가 **거주 가능한** 국가/지역, 유효 국가명 최소 1개)는 **100% 재택/원격 채용에만** 세트로 사용한다. 물리적 근무지(서울 EPIK 등) on-site 채용엔 붙이지 마라 — 근무지는 `jobLocation`만으로 표기. (TELECOMMUTE만 넣고 `applicantLocationRequirements`를 빠뜨리면 Search Console 오류.) 출처: [Job posting docs](https://developers.google.com/search/docs/appearance/structured-data/job-posting).
- **⚠ 만료 관리(강제)**: 만료 채용은 반드시 내려야 함 — `validThrough` 과거화 / 페이지 404·410 / 마크업 제거 중 하나. 안 지키면 수동 조치(manual action) 경고.
- **엔진**: Google ✅(Google Jobs). Naver ⚠.
- **검증**: RRT(Job posting) + Schema Validator.

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "JobPosting",
  "title": "Native English Teacher (EPIK, Public School)",
  "description": "<p>Full-time English teaching position at a Korean public school through the EPIK program. Responsibilities: …</p><ul><li>…</li></ul>",
  "identifier": { "@type": "PropertyValue", "name": "Korvia", "value": "EPIK-2026-SPR-001" },
  "datePosted": "2026-07-01",
  "validThrough": "2026-09-30T23:59:59+09:00",
  "employmentType": "FULL_TIME",
  "hiringOrganization": {
    "@type": "Organization",
    "name": "Korvia Consulting",
    "sameAs": "https://www.korvia.com",
    "logo": "https://www.korvia.com/logo-512.png"
  },
  "jobLocation": {
    "@type": "Place",
    "address": {
      "@type": "PostalAddress",
      "addressLocality": "Seoul",
      "addressRegion": "Seoul",
      "addressCountry": "KR"
    }
  },
  "baseSalary": {
    "@type": "MonetaryAmount",
    "currency": "KRW",
    "value": { "@type": "QuantitativeValue", "minValue": 2100000, "maxValue": 2700000, "unitText": "MONTH" }
  },
  "directApply": true
}
</script>
```
> Korvia 전용. EPIK/원어민 채용 상세에만. 배치 완료·마감 시 `validThrough` 과거화 또는 페이지 410. ⚠ **해외/외국인 지원 환영은 `description` 본문·자격요건 문구로 표기하라** — `applicantLocationRequirements`는 '외국인 지원 허용'이 아니라 '원격 근무자 거주 허용 지역'을 뜻하는 원격 전용 필드이므로 on-site EPIK 채용엔 쓰지 않는다(`name:"Worldwide"`도 유효한 국가/지역명 아님).

---

### 6.8 Course — ⚠ Course Info 폐지, 캐러셀(list)만 잔존

- **언제**: 교육 과정(ACT 미술수업, Passome 워크숍, Korvia 준비과정). ⚠ 단일 Course 상세 리치결과(**Course Info**)는 2025-09-09 폐지. **Course *list* 캐러셀**(여러 Course를 `ItemList`로)만 리치 잔존.
- **필수**(캐러셀): `ItemList` > 각 `Course`에 `name`, `description`, **`provider`**(Organization/@id), `url`. 개별 Course엔 `hasCourseInstance`(CourseInstance: `courseMode`, `startDate`, `location`) 권장.
- **엔진**: Google — 단일 Course ⚠(리치 없음, 파싱만) / Course 캐러셀 ✅. Naver ⚠.
- **검증**: RRT(Course info/Course list) + Schema Validator.

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "ItemList",
  "itemListElement": [
    {
      "@type": "ListItem", "position": 1,
      "item": {
        "@type": "Course",
        "name": "Beginner Drawing Class",
        "description": "8-week foundational drawing course for adults.",
        "provider": { "@id": "https://actartcenter.com/#org" },
        "url": "https://actartcenter.com/courses/beginner-drawing",
        "hasCourseInstance": {
          "@type": "CourseInstance",
          "courseMode": "onsite",
          "location": { "@type": "Place", "name": "ACT Art Center, Seoul" },
          "startDate": "2026-09-01"
        }
      }
    },
    {
      "@type": "ListItem", "position": 2,
      "item": {
        "@type": "Course",
        "name": "Watercolor Intensive",
        "description": "4-week watercolor techniques workshop.",
        "provider": { "@id": "https://actartcenter.com/#org" },
        "url": "https://actartcenter.com/courses/watercolor-intensive"
      }
    }
  ]
}
</script>
```

---

### 6.9 Event — 이벤트 리치결과

- **언제**: 워크숍·전시·설명회(ACT 전시, Korvia 채용설명회, Passome 세미나).
- **필수**: `name`, `startDate`(ISO 8601), `location`(오프라인=`Place`+`address` / 온라인=`VirtualLocation`+`url`). **권장**: `endDate`, `eventAttendanceMode`, `eventStatus`, `image`, `description`, `offers`(price/currency/availability/url/validFrom), `organizer`, `performer`.
- **엔진**: Google ✅. Naver ⚠.
- **검증**: RRT(Event) + Schema Validator.

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Event",
  "name": "ACT Art Center Spring Exhibition",
  "startDate": "2026-09-10T10:00:00+09:00",
  "endDate": "2026-09-20T19:00:00+09:00",
  "eventAttendanceMode": "https://schema.org/OfflineEventAttendanceMode",
  "eventStatus": "https://schema.org/EventScheduled",
  "image": "https://actartcenter.com/events/spring-2026.jpg",
  "location": {
    "@type": "Place",
    "name": "ACT Art Center",
    "address": { "@type": "PostalAddress", "addressLocality": "Seoul", "addressCountry": "KR" }
  },
  "organizer": { "@id": "https://actartcenter.com/#org" },
  "offers": {
    "@type": "Offer",
    "price": "0",
    "priceCurrency": "KRW",
    "availability": "https://schema.org/InStock",
    "url": "https://actartcenter.com/events/spring-2026",
    "validFrom": "2026-08-01T00:00:00+09:00"
  }
}
</script>
```

---

### 6.10 Product + Offer — 상품 스니펫 · 머천트 (Kimchi eSIM 핵심)

- **언제**: 판매 상품 페이지(Kimchi eSIM/요금제).
- **필수**: `name`, `offers`(`price`, `priceCurrency`, `availability`). **권장**: `image`, `description`, `brand`, `sku`/`gtin`, `offers.priceValidUntil`, `offers.url`.
- **✅ 리뷰 정책 (2026-07 공식 재확인)**: 자사가 **파는 Product**에 **진짜 고객이 남긴** `review`/`aggregateRating`을 마크업하는 것은 Google이 **명시적으로 허용**한다(별점 리치결과 유효). self-serving 별점 금지는 **Organization/LocalBusiness 타입에만** 적용(회사·브랜드 자체에 대한 자사 통제 리뷰)이며 Product엔 적용되지 않는다. Product에서 실제 금지되는 것은 **조작·자작·미검증(가짜) 리뷰**뿐. 출처: [Review snippet docs](https://developers.google.com/search/docs/appearance/structured-data/review-snippet), [2019-09 발표](https://developers.google.com/search/blog/2019/09/making-review-rich-results-more-helpful) (§6.11).
- **Merchant listing(판매) vs Product snippet(정보)**: 판매 페이지엔 `Offer`, 비교/리뷰 페이지엔 review 부착.
- **엔진**: Google ✅. Naver ⚠(Naver 쇼핑은 별도 생태계).
- **검증**: RRT(Product/Merchant listing) + Schema Validator. ⚠ Product의 self-serving 리뷰 정책 세부는 [Product docs](https://developers.google.com/search/docs/appearance/structured-data/product)로 재확인.

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Product",
  "name": "Korea Travel eSIM — 10GB / 30 Days",
  "image": "https://www.kimchimobile.com/products/esim-10gb.jpg",
  "description": "Prepaid eSIM for travelers and residents in South Korea. 10GB data, 30-day validity, instant QR delivery.",
  "brand": { "@type": "Brand", "name": "Kimchi Mobile" },
  "sku": "KM-ESIM-10GB-30D",
  "offers": {
    "@type": "Offer",
    "url": "https://www.kimchimobile.com/products/esim-10gb",
    "price": "29900",
    "priceCurrency": "KRW",
    "priceValidUntil": "2026-12-31",
    "availability": "https://schema.org/InStock",
    "itemCondition": "https://schema.org/NewCondition"
  }
}
</script>
```
> Kimchi 전용. **요금·재고 정보를 항상 최신으로 유지**(YMYL 신뢰 신호). `priceValidUntil` 지나면 갱신. **정품 고객 리뷰를 수집하면 자사 eSIM 상품에 `aggregateRating` 별점 리치를 붙일 수 있다**(Google 지원) — 단 조작·자작 리뷰 금지, 실제·검증 가능한 고객 후기만.

---

### 6.11 Review / AggregateRating — 별점 (Product 실고객 허용 · Org/Local 자사 통제 금지)

- **언제**: 리뷰 대상에 대한 별점. **Product**는 자사가 파는 상품이라도 **진짜 고객이 남긴** 리뷰/aggregateRating이면 허용(별점 리치 유효). **Organization/LocalBusiness**(회사·브랜드·기관 자체)에 대해 자사가 통제하는 리뷰를 자기 사이트에 마크업하면 = ⚠ **self-serving, 별점 리치결과 부적격**(수동 조치는 없음). 공통 금지 = 조작·handpicked testimonial·미검증(가짜) 리뷰.
- **필수**(Review): `itemReviewed`(리뷰 대상 + `name`), `reviewRating`(`ratingValue`, `bestRating`), `author`. AggregateRating: `ratingValue`, `ratingCount`/`reviewCount`, `itemReviewed`.
- **엔진**: Google ✅(정책 준수 시). Naver ⚠.
- **검증**: RRT(Review snippet) + Schema Validator.

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Review",
  "itemReviewed": { "@type": "Product", "name": "Some Third-Party eSIM" },
  "author": { "@type": "Person", "name": "Reviewer Name" },
  "reviewRating": { "@type": "Rating", "ratingValue": "4", "bestRating": "5" },
  "reviewBody": "Independent hands-on review …",
  "datePublished": "2026-07-01"
}
</script>
```
> **[검증 2026-07]** self-serving 범위는 1차 문서로 확인됨: 제한은 **LocalBusiness/Organization(및 하위 타입)에만** 적용되고 **Product에는 적용되지 않는다**(자사 상품 실고객 별점 허용). 출처: [Review snippet docs](https://developers.google.com/search/docs/appearance/structured-data/review-snippet), [Making Review Rich Results more helpful (2019-09)](https://developers.google.com/search/blog/2019/09/making-review-rich-results-more-helpful).

---

### 6.12 VideoObject — 영상 리치 · 키모먼트

- **언제**: 영상 있는 페이지(수업 소개, 후기 영상, 제품 데모). AIO 멀티모달 노출 기회.
- **필수**: `name`, `description`, `thumbnailUrl`, `uploadDate`(ISO 8601). **권장**: `contentUrl` 또는 `embedUrl`, `duration`(ISO 8601 예 `PT2M30S`), `hasPart`(Clip/key moments), `publisher`.
- **엔진**: Google ✅(영상 리치·키모먼트·seek). Naver ⚠(Naver TV 별도).
- **검증**: RRT(Video) + Schema Validator.

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "VideoObject",
  "name": "How Kimchi Mobile eSIM works — 2-minute setup",
  "description": "Step-by-step eSIM activation for foreigners arriving in Korea.",
  "thumbnailUrl": ["https://www.kimchimobile.com/video/esim-setup-thumb.jpg"],
  "uploadDate": "2026-07-01T09:00:00+09:00",
  "duration": "PT2M10S",
  "contentUrl": "https://www.kimchimobile.com/video/esim-setup.mp4",
  "embedUrl": "https://www.youtube.com/embed/XXXXXXXX",
  "publisher": { "@id": "https://www.kimchimobile.com/#org" }
}
</script>
```

---

### 6.13 Person (author) — 저자 엔티티 (E-E-A-T)

- **언제**: 저자 프로필/바이오 페이지, Article `author` 참조 대상. UGC/크리에이터 프로필은 **Profile Page** 리치결과 대상.
- **필수**(실무): `name`. **권장**: `url`(프로필), `sameAs`(외부 프로필/LinkedIn), `jobTitle`, `worksFor`(@id), `knowsAbout`, `image`, `alumniOf`/자격.
- **엔진**: Google ✅(저자 신뢰·프로필 페이지). Naver ⚠.
- **YMYL 필수**: Korvia/Kimchi의 비자·통신·금융 글은 저자 자격·경력이 표면화돼야 함(Trust 신호).

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Person",
  "@id": "https://www.korvia.com/authors/henry-oh#person",
  "name": "Henry Oh",
  "url": "https://www.korvia.com/authors/henry-oh",
  "jobTitle": "CEO",
  "worksFor": { "@id": "https://www.korvia.com/#org" },
  "knowsAbout": ["EPIK", "E-2 visa", "recruitment for foreigners in Korea"],
  "sameAs": ["https://www.linkedin.com/in/henry-oh"]
}
</script>
```

---

### 6.14 HowTo — ⚠ 완전 폐지 (참고)

- **⚠ 상태**: 2023-08 축소 후 완전 폐지. 마크업 무시됨. **신규 생성 금지.** 프로세스/단계는 본문 번호 리스트로(발췌·파싱 유효).
- **정정 노트**: [`WEB_PLAYBOOK.md`](./WEB_PLAYBOOK.md) §2.2·[`NEW_PAGE_CHECKLIST.md`](./NEW_PAGE_CHECKLIST.md)의 "HowTo schema(step: HowToStep[])" 항목은 폐지 이전 작성분 → **적용 금지**. 단계 콘텐츠는 마크업 없이 `<ol><li>`로.

---

## 7. 프로젝트별 스키마 세트 (권장 조합)

| 사이트 | 스택 | 필수 세트 | 페이지 타입별 |
|---|---|---|---|
| **Passome** | Next.js/Vercel | Organization·WebSite·BreadcrumbList | Article + Person(author), Event(세미나), Course(캐러셀·워크숍) |
| **Korvia** (YMYL) | Next.js | Organization·WebSite·BreadcrumbList | **JobPosting**(EPIK/원어민·만료관리), BlogPosting + Person(YMYL 저자), Course(준비과정 캐러셀). 본문 FAQ(마크업 X) |
| **Kimchi** (YMYL) | WordPress | Organization·WebSite·BreadcrumbList | **Product+Offer**(eSIM·최신 요금), VideoObject(셋업), BlogPosting + Person, 제3자 Review만. 본문 FAQ(마크업 X) |
| **ACT** | Next.js | Organization·LocalBusiness(@id 공유)·BreadcrumbList | **LocalBusiness + 스마트플레이스**, Course(캐러셀), Event(전시), VideoObject |

**우선순위 (전 사이트 공통):**
- P0: Organization / BreadcrumbList / 실명 Person 저자
- P0(YMYL): Korvia·Kimchi 신뢰블록(자격·1차출처·dateModified)
- P1: Korvia JobPosting / Kimchi Product+Offer / Article
- P2: Event / LocalBusiness / VideoObject / Course 캐러셀

---

## 8. 이중언어(KR/EN) · 외국인 대상 · YMYL 특이사항

- **JSON-LD `inLanguage`**로 각 언어판 언어 표시(`"ko"` / `"en"`). 각 언어판은 **자기 언어판을 canonical**로, hreflang로 서로 연결(상세: [`NEW_PAGE_CHECKLIST.md`](./NEW_PAGE_CHECKLIST.md) §A-6, [`WEB_PLAYBOOK.md`](./WEB_PLAYBOOK.md) §12.3). hreflang 코드: 언어=ISO 639-1 소문자(`ko`,`en`), 지역=ISO 3166-1 Alpha-2 대문자(**영국=`GB`**, UK 아님). 상호·자기참조·절대 URL·`x-default` 필수.
- **⚠ Naver는 hreflang 미준수** → 한국어 자산은 위젯·메뉴·푸터·이미지 텍스트까지 전부 한국어. JSON-LD의 Naver 리치 효과는 **미문서화(UNVERIFIABLE)** → Naver 노출은 OG + C-Rank/D.I.A+/스마트블록/AI 브리핑로 별도 확보.
- **`og:image` 값은 5MB 이하 · 1200×630**, `og:url` = canonical, 다국어는 `og:locale`(`ko_KR`/`en_US`) + `og:locale:alternate`. OG는 카카오톡/네이버 공유 미리보기도 커버 → 국내·글로벌 SNS 동시 대응.
- **YMYL(Korvia 비자/취업, Kimchi 통신/금융)**: 스키마만으로 신뢰가 서지 않는다. **Person(author) 자격 + Article `dateModified` + 본문 1차 출처(법령·공식기관)**를 함께 표면화해야 E-E-A-T(특히 Trust)가 랭킹·AI 인용 게이트를 통과. 부정확·과장은 하드페일 → 발행 전 [`slop-judge`](../../.claude/skills/) 스킬로 YMYL 하드페일 0 확인.

---

## 9. 정정 노트 (기존 문서 대비 — 2026 사실 반영)

이 카탈로그가 기존 문서의 아래 항목을 **정정/하향**한다(기존 문서는 폐지 이전 작성분):

1. **FAQPage** — [`WEB_PLAYBOOK.md`](./WEB_PLAYBOOK.md) §2.2/§2.3, [`NEW_PAGE_CHECKLIST.md`](./NEW_PAGE_CHECKLIST.md) §B-8, [`AEO_GEO_PLAYBOOK_2026.md`](./AEO_GEO_PLAYBOOK_2026.md) §4(1)의 "FAQPage 우선/주입"은 **2026-05-07 리치결과 폐지**로 무효 → 본문 Q&A(마크업 X)로 전환.
2. **HowTo** — 위 문서들의 "HowTo schema(step: HowToStep[])"는 **완전 폐지** → 본문 번호 리스트로.
3. **WebSite SearchAction(Sitelinks Searchbox)** — [`WEB_PLAYBOOK.md`](./WEB_PLAYBOOK.md) §2.1의 `potentialAction: SearchAction`은 **2024-11-21 폐지** → 신규 생성 금지(`WebSite` name/alternateName만 유지).
4. **Course** — 단일 Course(Course Info) 리치결과 폐지 → 캐러셀(`ItemList`)로만.
5. **AI Overviews 노출률** — 리서치 번들의 "검색의 50%+"는 전체 쿼리 기준 과장. ⚠ **Semrush 기준 2025 중반 정점 ~25% 후 late-2025 ~16%** — 데이터셋별 15~25%(특정 쿼리군은 더 높음). 절대수치는 3자 벤더 추정으로 출처·기간 병기 필요. (적격 쿼리 기준 ~47% 벤더 추정치는 [`GEO_AEO_AI_ENGINES_2026.md`](./GEO_AEO_AI_ENGINES_2026.md) §A.2 참조 — 전체 쿼리 15~25%와 **모수가 다름**(적격 ⊂ 전체).)
6. **Book Actions** — "폐지"가 아니라 **2025-11 폐지 철회 → 여전히 지원**. "사용 금지" 지침은 오류.
7. **llms.txt** — Google Search는 **사용하지 않음**(공식) → "Google AEO 필수"로 표기 금지. 다른 LLM 파이프라인엔 저비용 선택이나 성과 근거 약함.

---

## Sources

**Google 공식 (Search Central):**
- Search Gallery (지원 타입): https://developers.google.com/search/docs/appearance/structured-data/search-gallery
- Changelog(폐지 이력): https://developers.google.com/search/updates
- 검색결과 단순화(2025-06): https://developers.google.com/search/blog/2025/06/simplifying-search-results
- Sitelinks Searchbox 폐지(2024-10): https://developers.google.com/search/blog/2024/10/sitelinks-search-box
- HowTo/FAQ 변경(2023-08): https://developers.google.com/search/blog/2023/08/howto-faq-changes
- FAQPage docs: https://developers.google.com/search/docs/appearance/structured-data/faqpage
- JobPosting: https://developers.google.com/search/docs/appearance/structured-data/job-posting
- Article: https://developers.google.com/search/docs/appearance/structured-data/article
- Organization: https://developers.google.com/search/docs/appearance/structured-data/organization
- Product: https://developers.google.com/search/docs/appearance/structured-data/product
- Review snippet: https://developers.google.com/search/docs/appearance/structured-data/review-snippet
- 구조화데이터 일반 가이드라인(정책): https://developers.google.com/search/docs/appearance/structured-data/sd-policies
- AI 최적화 가이드(llms.txt 불필요·"still SEO"): https://developers.google.com/search/docs/fundamentals/ai-optimization-guide

**검증 도구:**
- Rich Results Test: https://search.google.com/test/rich-results
- Schema Markup Validator: https://validator.schema.org/
- schema.org 타입 사전: https://schema.org/docs/full.html
- 네이버 서치어드바이저: https://searchadvisor.naver.com/

**폐지/정정 근거 (3자):**
- 2025-09-09 6종 제거: https://searchengineland.com/google-drops-reporting-on-several-structured-data-types-461744
- Practice Problem 등 2026-01 배치: https://searchengineland.com/google-to-remove-more-search-features-including-practice-problems-nutrition-facts-nearby-offers-and-more-464255
- Book Actions 폐지 철회: https://www.etavrian.com/news/google-deprecates-practice-problem-structured-data
- AI Overviews 수치 재조정: https://searchengineland.com/google-ai-overviews-surge-pullback-data-466314

**크로스링크(스킬 내부):**
- 플레이스홀더 원본: `../.claude/skills/seo-harness-team/resources/schema-templates/`
- AI 크롤러 robots 패치: `../.claude/skills/seo-harness-team/resources/robots-txt-ai-patch.txt`
- 전략/GEO: [`AEO_GEO_PLAYBOOK_2026.md`](./AEO_GEO_PLAYBOOK_2026.md) · 기술 셋업: [`WEB_PLAYBOOK.md`](./WEB_PLAYBOOK.md) · 페이지 게이트: [`NEW_PAGE_CHECKLIST.md`](./NEW_PAGE_CHECKLIST.md)
