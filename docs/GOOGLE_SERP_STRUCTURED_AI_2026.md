# Google SERP 구조화데이터 · AI Overviews (2026)

> Generated 2026-07-01 · Owner: Henry Oh (KORVIA) · ⚠ 콘솔/API 변경은 사용 전 재확인
>
> 이 문서는 `korvia-seo-skill`의 자매 문서를 **보완**한다(중복 금지, 상호링크):
> - 범용 AI 답변엔진 전략·플랫폼별 인용·llms.txt·측정 → [`AEO_GEO_PLAYBOOK_2026.md`](./AEO_GEO_PLAYBOOK_2026.md)
> - Next.js 퍼블리싱/기술 구현(schema 주입 코드·robots·CLS·hreflang 구현 위치) → [`WEB_PLAYBOOK.md`](./WEB_PLAYBOOK.md)
> - 새 페이지 최소 체크리스트 → [`NEW_PAGE_CHECKLIST.md`](./NEW_PAGE_CHECKLIST.md)
>
> **이 문서만의 범위**: Google SERP 표면 자체 — (1) 어떤 리치결과 스키마가 2024–2026에 죽고 살았나, (2) 살아있는 타입의 정확한 요건, (3) E-E-A-T·Helpful Content·코어/스팸 업데이트, (4) AI Overviews/AI Mode 인용 조건과 zero-click, (5) 피처드 스니펫/PAA, (6) hreflang, (7) 브랜드 SERP. Naver·한국 검색 표면은 별도 Naver 문서로 위임.
>
> **정확성 주의**: 아래 타입/날짜는 Google Search Central 공식 changelog·문서로 대조했다(2026-07 확인). AI Overviews·zero-click·피처드 스니펫 수치는 **전부 3자 벤더 추정(Semrush/Pew/BrightEdge/Keywords Everywhere)이며 Google 공식 통계가 아니다** — 방향성만 신뢰하고 인용 시 출처·기간을 병기한다. 지식 컷오프(2026-01) 이후 콘솔 UI·API는 사용 전 재확인.

---

## 0. 자동 적용 규칙 (Auto-apply)

작성자/에이전트가 **기계적으로** 따를 항목. 신규 페이지·리라이트·감사에 그대로 적용한다.

| # | 규칙 | 이유 |
|---|---|---|
| A1 | `FAQPage`·`HowTo`·`Sitelinks Searchbox` JSON-LD를 **새로 생성하지 말 것**. 기존 발견 시 제거 권장(남겨도 SC 에러는 아님, 리치결과만 안 뜸). | 2023–2026 전부 폐지. §1.1 |
| A2 | `Course Info`·`Estimated Salary`·`Learning Video`·`Special Announcement`·`Vehicle Listing`·`ClaimReview`·`Practice Problem`·`Nutrition facts`·`Nearby offers`·`Vacation rental` 스키마 **사용 금지**. | 2025-09 ~ 2026-01 폐지. §1.1 |
| A3 | **`Book Actions`는 계속 사용 가능** — 2025-11 폐지 철회됨. "사용 금지" 아님. | ⚠ §1.1 correction |
| A4 | 모든 사이트 홈에 `Organization`(또는 `['Organization','ProfessionalService']`) 1개: `name`·`url`·`logo`(정사각 고해상)·`sameAs`(공식 SNS)·`knowsAbout`·`founder`·`foundingDate`. | 로고·지식패널·사이트명. §1.3 |
| A5 | `Article/BlogPosting`엔 실명 `author`(Person) + `author.url` 또는 `sameAs`(저자 바이오/자격 링크) + `datePublished` + `dateModified`(ISO 8601+타임존). 저자 다수는 개별 필드 분리. | E-E-A-T 핵심. §1.3 |
| A6 | YMYL 페이지(Korvia 비자/취업, Kimchi 통신/금융)엔 저자 자격·1차 출처·최종 업데이트일 **신뢰 블록**을 본문에 표면화. | Trust가 랭킹·AIO 게이트. §2.2 |
| A7 | Korvia 채용 페이지: `JobPosting` 필수 5속성 + `validThrough` + 만료 시 즉시 내림(과거화/404·410/마크업 제거). **리스트 페이지엔 부착 금지**. | §1.3 |
| A8 | Kimchi 상품 페이지: `Product` + `offers`(`price`·`priceCurrency`·`availability`·`priceValidUntil`). 요금·재고 항상 최신. | §1.3 |
| A9 | AIO/AI Mode용 `llms.txt`·AI 전용 파일·특수 스키마를 **만들지 말 것**(Google 공식상 무의미). 대신 색인 + 스니펫 표시 자격(크롤 가능, `noindex` 미차단)만 보장. | §3.2. ※ `AEO_GEO_PLAYBOOK`의 llms.txt는 **Google이 아닌 ChatGPT/Perplexity/Claude용** — 목적이 다르니 둘 다 유지 |
| A10 | 정보성 페이지는 질문/헤딩 바로 아래 **40–60단어 직답 문단** + 절차=번호 리스트 + 정의=짧은 문단 + 비교=표 + 명확한 H2/H3. | 피처드 스니펫·AIO 발췌. §3.3·§4 |
| A11 | PAA/사용자 질문은 본문 하위 헤딩·인라인 Q&A로 커버하되 **`FAQPage` 마크업은 붙이지 말 것**(콘텐츠는 유효, 마크업은 죽음). | §4 |
| A12 | KR/EN 이중언어: reciprocal hreflang(모든 변형이 서로 + 자기 자신을 절대 URL로) + `x-default`, 각 언어판 self-canonical. 언어=ISO 639-1 소문자, 지역=ISO 3166-1 Alpha-2 대문자(영국=**GB**, UK 아님). | §5 |
| A13 | 이미지=고해상(≥50K px)·본문 관련(로고 아님)·16:9/4:3/1:1 3종 제공. 영상은 AIO 멀티모달 노출 위해 최적화. | §3.2 |
| A14 | 사이트 전체 얇은/AI-슬롭 콘텐츠 비중을 낮게. `site reputation abuse`(제3자 콘텐츠를 도메인 권위에 얹기) 금지. slop-judge 게이트 연계. | §2.1 |
| A15 | 사이트명 제어는 `WebSite` 스키마 `name`/`alternateName`. 사이트링크는 명확한 네비 계층·서술적 제목·내부링크로 간접 유도(마크업 불가). | §6 |
| A16 | Naver 대상 콘텐츠는 hreflang 무시됨 → 위젯·메뉴·푸터·이미지 텍스트까지 **전부 한국어**(Google용 EN과 분리). | §5 |

> ⚠ **기존 문서 갱신 고지**: `WEB_PLAYBOOK.md` §2.2·§2.3과 `NEW_PAGE_CHECKLIST.md` §B의 **FAQPage / HowTo 주입 권장은 이 문서로 superseded**. 두 스키마는 리치결과가 사라졌으므로 신규 생성하지 않는다(A1). 본문 Q&A·프로세스 콘텐츠 자체는 계속 유효(마크업만 제거).

---

## 0.5. 4개 사이트별 한 줄 요약

| 사이트 | 스택 | Google에서 결정적인 것 |
|---|---|---|
| Passome (passome.com) | Next.js/Vercel | `Organization` 엔티티 + `Article`(저자 E-E-A-T). AI 컨설팅=경쟁 키워드 → AIO 인용 노림 |
| **Korvia (korvia.com)** | Next.js | **YMYL(비자/취업)**. `JobPosting`(EPIK/원어민) + `Article` + 저자 신뢰. E-E-A-T가 랭킹·AIO 인용 게이트 |
| **Kimchi (kimchimobile.com)** | WordPress | **YMYL(통신/금융/eSIM)**. `Product`/`Offer` + `Review` + `Organization`. 정확·최신 요금이 신뢰 신호 |
| ACT Art Center (actartcenter.com) | Next.js | `LocalBusiness`(미술센터/교육) + `Event`(워크숍/전시) + Course *List* carousel(단 Course *Info* 리치결과는 폐지) |

---

## 1. 리치결과(구조화데이터) — 무엇이 죽고 무엇이 살았나

Google은 2025-06 "Simplifying the search results page" 기조로 **잘 안 쓰이거나 추가가치 낮은 리치결과를 대량 정리** 중이다. 핵심: 이 정리들은 **랭킹에 영향이 없다**(Google 명시). 크롤·색인은 그대로, 시각적 리치 표시만 사라진다. 따라서 죽은 스키마는 유지비만 발생 → **신규 생성 안 함**.

### 1.1 죽은/축소된 타입 (마크업 남겨도 무해하나 리치결과 안 뜸)

| 타입 | 상태 | 공식 시점 | 근거 |
|---|---|---|---|
| **HowTo** | 완전 폐지(모바일·데스크톱 마크업 무시) | 2023-08 축소 → 2024 완전 제거 | Search Central Blog 2023/08 |
| **FAQPage** | 완전 폐지 (2단계, 아래 ⚠) | 2023-08 정부·보건만 잔존 → **2026-05-07 검색 미표시** → 문서 제거 2026-06-15 | changelog 2026-05/06 |
| **Sitelinks Searchbox** | 폐지(시각요소·SC 리포트·Rich Results Test 제거) | 예고 2024-10-21 → **2024-11-21 전역 폐지** | Search Central Blog 2024/10 |
| **Course Info** | 폐지 (Course *List* carousel은 잔존 ⚠) | 예고 2025-06-12 → 제거 2025-09-09 | changelog |
| **Estimated Salary** | 폐지 | 2025-06-12 → 2025-09-09 | changelog |
| **Learning Video** | 폐지 | 2025-06-12 → 2025-09-09 | changelog |
| **Special Announcement** | 폐지 | 2025-06-12 → 2025-09-09 | changelog |
| **Vehicle Listing** | 폐지 | 2025-06-12 → 2025-09-09 | changelog |
| **ClaimReview / Fact Check** | 폐지 | 2025-06-12 → 2025-09-09 | changelog |
| **Practice Problem** | 폐지 | 예고 2025-11-05 → 제거 2026-01(≈01-06) | changelog |
| **Nutrition facts / Nearby offers / Vacation rental** | 폐지(Practice Problem과 같은 2026-01 배치) | 2025-11-05 예고 → 2026-01 | changelog |

> ⚠ **FAQ 폐지는 2단계**: (a) 2023-08부터 **정부·보건 사이트 한정**으로만 잔존 노출, (b) 2026-05-07 그 예외까지 **검색 미표시** → 05-08 폐지 노트 → **06-15 문서 제거** → **SC 리포트·Rich Results Test 6월, API 지원은 2026-08까지 순차 종료**. 즉 "완전 종료" 시점이 표면별로 다르다. — 출처: `developers.google.com/search/updates`
>
> ⚠ **Book Actions는 죽지 않았다 (correction)**: 2025-06-12에 폐지 예고된 **7종 중 2025-09-09에 실제 제거된 것은 6종**(Course Info·Estimated Salary·Learning Video·Special Announcement·Vehicle Listing·ClaimReview)이다. **Book Actions만 9월 제거에서 빠졌고, 2025-11 Google이 폐지 배너를 제거해 "여전히 검색 기능이 이 마크업을 사용한다"고 확인**했다 → **현재 지원 유지, 사용 금지 아님**. ("7종 예고 ≠ 7종 제거"에 유의.) — 출처: searchengineland.com/google-drops-reporting-on-several-structured-data-types-461744

### 1.2 살아있는(현재 지원) 리치결과 타입 — Search Gallery 기준

`/search/docs/appearance/structured-data/search-gallery`에 2026-07 현재 노출되는 타입:

- **Article** (Article/NewsArticle/BlogPosting) — 톱스토리·리치 헤드라인
- **Breadcrumb** — 경로 표시(전 사이트 필수급)
- **Product / Merchant listing** — 가격·재고·리뷰 별점 → Kimchi eSIM
- **Review snippet**(별점) — Product/Recipe/Book/Movie 등에 부착
- **JobPosting** + **Employer aggregate rating** → Korvia
- **Organization** — 로고·지식패널·사이트명 → 4개 전부
- **Profile page / Q&A / Discussion forum** — UGC·포럼(Reddit식)
- **Local business** — 로컬팩·지식패널 → ACT
- **Event** — 이벤트 리치결과 → ACT 워크숍/전시
- **Video** (+ key moments/clip/seek) — 영상 인덱싱
- **Image metadata / Speakable**(뉴스 TTS)
- **Course *List* (carousel)** — Course *Info*는 폐지됐지만 Course *List* 캐러셀은 잔존 ⚠(어떤 마크업이 유효한지 경계는 Course 문서 원문 재확인 권장)
- **Book Actions** — 지원 유지(§1.1 참조)
- **Math solver / Education Q&A / Movie carousel / Subscription·Paywalled content / Carousel(beta) / Vacation rental(→ 2026-01 폐지 확인, 사용 안 함)**

> ⚠ **Dataset은 일반 Search 리치결과가 아니다**: `Dataset` 구조화데이터는 **Google Dataset Search 전용**으로만 작동한다(2025-11 Google 클라리피케이션). **4개 사이트엔 비적용** — 일반 SERP 리치결과처럼 취급하지 말 것.

### 1.3 사이트별 핵심 스키마 요건 (정확 스펙 + 코드)

**Organization (4개 전부 — 브랜드 SERP·지식패널)**
```json
{
  "@context": "https://schema.org",
  "@type": ["Organization", "ProfessionalService"],
  "@id": "https://www.korvia.com/#org",
  "name": "KORVIA Consulting",
  "alternateName": "코비아컨설팅",
  "url": "https://www.korvia.com/",
  "logo": "https://www.korvia.com/logo-square.png",   // 정사각·고해상
  "founder": { "@type": "Person", "name": "Henry Oh" },
  "foundingDate": "2013",
  "knowsAbout": ["Teaching English in Korea", "EPIK", "E-2 visa", "recruitment"],
  "sameAs": [
    "https://www.linkedin.com/company/korvia",
    "https://www.instagram.com/korvia",
    "https://www.facebook.com/korvia"
  ],
  "contactPoint": { "@type": "ContactPoint", "contactType": "customer support", "email": "henry@korvia.com" }
}
```
→ 로고·지식패널·사이트명 노출에 직접 반영. 엔티티 일관성(동일 명칭·로고·바이오)이 핵심.

**Article / BlogPosting (Passome·Korvia·ACT 블로그·가이드)**
- **필수 속성 없음** — "적용되는 속성을 넣어라". 실무 권장:
  - `headline`(짧게, 긴 제목은 잘림), `image`(≥50K px, 16:9/4:3/1:1 3종, 로고 아닌 본문 이미지)
  - `author`: `Person`/`Organization` + `author.name`(직함·매체명 제외) + **`author.url` 또는 `sameAs`(저자 프로필/바이오 링크)** ← **E-E-A-T 핵심 신호**
  - `datePublished` / `dateModified`(ISO 8601 + 타임존). 저자 다수는 **개별 필드로 분리**(합치지 말 것)
```json
{
  "@type": "BlogPosting",
  "headline": "E-2 Visa for Teaching English in Korea: 2026 Guide",
  "image": ["https://.../16x9.jpg","https://.../4x3.jpg","https://.../1x1.jpg"],
  "author": { "@type": "Person", "name": "Henry Oh",
              "url": "https://www.korvia.com/about/henry-oh" },   // 바이오·자격 페이지
  "datePublished": "2026-07-01T09:00:00+09:00",
  "dateModified":  "2026-07-01T09:00:00+09:00",
  "publisher": { "@id": "https://www.korvia.com/#org" }
}
```

**JobPosting (Korvia — EPIK/원어민 채용)**
- **필수 5속성**: `datePosted`(ISO 8601), `description`(HTML 전체 상세), `hiringOrganization`(회사명, 지점 아님), `jobLocation`(**반드시 `addressCountry` 포함**), `title`(코드·급여·주소 제외 직무명)
- **권장**: `baseSalary`(currency + unit=HOUR/DAY/WEEK/MONTH/YEAR), `employmentType`, `validThrough`(마감 ISO 8601), `identifier`, `applicantLocationRequirements`, `jobLocationType:"TELECOMMUTE"`(100% 원격), `directApply`(bool)
- **가이드라인(강제)**: 만료 채용은 반드시 내림 — `validThrough` 과거화 **또는** 페이지 404/410 **또는** 마크업 제거. **개별 채용 상세 페이지에만** 부착(리스트/검색결과 페이지 금지 → 위반 시 manual action 경고).

**Product / Merchant listing (Kimchi eSIM)**
- `Product` + `offers`(`price`·`priceCurrency`·`availability`·`priceValidUntil`), `brand`, `image`, `sku`/`gtin`
- ⚠ 셀프 판매상품의 **자체 `aggregateRating`/`review` 별점은 정책 제한** 대상 — 제3자·독립 리뷰 위주(셀프-서빙 리뷰 정책·review snippet 세부는 Product/Review 공식 문서로 재확인 권장). Merchant listing(판매) 페이지엔 `Offer`, 리뷰/비교 페이지엔 review 부착.

**LocalBusiness (ACT / Korvia·Kimchi 오프라인 접점)**
- `name`·`address`·`geo`·`telephone`·`openingHoursSpecification`·`priceRange`·`image`. (한국 로컬은 Naver 지도/플레이스가 실질 지배 → Naver 문서 병행.)

**WebSite (사이트명 제어 — Sitelinks Searchbox 대체)**
```json
{ "@type": "WebSite", "@id": "https://www.korvia.com/#website",
  "name": "KORVIA", "alternateName": "코비아컨설팅",
  "url": "https://www.korvia.com/", "publisher": { "@id": "https://www.korvia.com/#org" } }
```
→ ⚠ `potentialAction: SearchAction`(Sitelinks Searchbox)는 **넣지 말 것**(2024-11-21 폐지). `WebSite` 사이트명 제어만 유효.

---

## 2. 랭킹 시스템 — Helpful Content 통합 · E-E-A-T · 코어/스팸 업데이트

### 2.1 Helpful Content System → 코어 흡수 (2024-03)
- 2024-03 코어 업데이트에서 **별도 HCU 분류기가 코어 랭킹에 통합**됐다. 이제 "헬프풀함"은 특정 업데이트 때만이 아니라 **상시** 다수 코어 시스템이 평가한다.
- **사이트 전체 평판 원리**: 사이트에 저품질/비도움 콘텐츠 비중이 높으면 **고품질 페이지까지 동반 하락**. → 얇은/AI-슬롭 페이지 양산 금지(slop-judge 게이트 연계, A14).
- 같은 2024-03 스팸 업데이트가 신설한 대상: `scaled content abuse`, **`site reputation abuse`(=parasite SEO)**, `expired domain abuse`.
- ⚠ "저품질 45% 감소" 등 구체 수치는 Google이 목표치로 언급했으나 3자·산업 블로그 검증 강도가 낮다 — 방향성만 신뢰.

### 2.2 E-E-A-T (Experience·Expertise·Authoritativeness·Trustworthiness)
- 직접적 랭킹 "요인"이 아니라 품질평가자 가이드라인 개념이지만, Google 시스템이 근사하는 **신뢰 신호의 종합**이다. **Trust가 최상위**.
- **YMYL(Korvia 비자/취업, Kimchi 금융/통신)에서 특히 결정적** — 잘못된 정보는 사용자에 실질 피해 → Google이 신뢰 낮은 소스를 강등.
- 실무 신호: 실명 저자 + 바이오/자격 페이지(`author.url`/`sameAs`) · 1차 경험 서술(Experience) · 외부 인용·백링크 · 최신성(`dateModified`) · 저자·조직 엔티티 일관성. → **본문 신뢰 블록**(A6): 저자 자격 1줄 + 1차 출처 링크 + "최종 업데이트: YYYY-MM-DD".

### 2.3 코어/스팸 업데이트 타임라인 (2024–2026)
| 시점 | 유형 | 요지 |
|---|---|---|
| 2024-03-05 ~ 04-19 (45일) | 코어 + 스팸 | HCU 코어 통합. 스팸: scaled content / site reputation / expired domain abuse 신설 |
| 2024-08-15 ~ 09-03 | 코어 | 고품질 승격·저가치 SEO 콘텐츠 강등 |
| 2024-12-12 ~ 12-18 | 코어 | + 12-19 스팸 업데이트 연속 |
| 2025-03-13 ~ 03-27 | 코어 | 2025 첫 대형, 높은 변동성 |
| 2025-06-30 ~ | 코어 (June 2025 Core) | 헬프풀 콘텐츠 우선 |
| 2026-03-24 ~ 03-25 | 스팸 | 20시간 미만 초고속 완료 |
| 2026-03-27 ~ **2026-04-08** | 코어 (March 2026 Core) | 스팸 3일 뒤 릴리스, **4/8 롤아웃 완료(12일)** — 진행중 아님, 완료됨 |

> 실무 규칙: 코어 업데이트 후 하락은 **개별 페이지 벌점이 아니라 상대 재평가**. 대응 = 삭제/개선(HCU 원리)·신뢰 강화, 꼼수 없음.

---

## 3. AI Overviews · AI Mode · SGE — 인용/발췌 조건과 zero-click

### 3.1 현황(규모) — ⚠ 전부 3자 추정, 절대치 단정 금지
- **AI Overviews 노출률**: 2025-01 ~6.5% → 2025 중반 **정점 ~25%(Semrush 10M 키워드 기준)** → 2025-11 **~15.7%로 소폭 하락(pullback)**. 데이터셋·쿼리군에 따라 15~50%로 편차가 크다. ⚠ **"검색의 50%+"는 특정 쿼리군의 상단치를 전체로 일반화한 과장** — 전체 쿼리 기준으로는 **15~25% 범위(2025 중반 정점 후 하락)**로 서술하고 절대수치엔 출처·기간 병기. (출처: searchengineland.com/google-ai-overviews-surge-pullback-data-466314, Semrush)
  - ⚠ **모수 주의**: GEO_AEO_AI_ENGINES_2026 §A.2/§I의 **'적격 쿼리 기준 ~47%'(2026 Q1, 벤더/추정치)**는 여기 **전체 쿼리 기준 15~25%**와 **모수가 다른 별개 수치**(적격 쿼리 ⊂ 전체 쿼리)다. 둘을 섞어 쓰지 말고, 47%는 Google 공식 단정이 아니므로 성과 근거로 쓰지 말 것.
- **Zero-click**: AIO 노출 쿼리 평균 zero-click ~**83%**(비AIO ~59–60%). Pew(2025-07) 기준 **AIO 내 링크 클릭률 ~1%**. → 트래픽보다 **브랜드/엔티티 가시성** 게임으로 이동.

### 3.2 Google 공식 입장 — "AI 검색도 그냥 검색이다" (가장 중요)
Google `ai-optimization-guide` / `ai-features` 문서 원칙(2025 발행):
- **AIO/AI Mode에 뜨기 위한 추가 요건·특수 최적화가 없다.** 새 기계판독 파일·**`llms.txt`**·AI 텍스트 파일·특수 schema.org 마크업 **불필요**(Google은 `llms.txt`를 명시적으로 "무시하며 도움·손해 없음"이라 기술).
- **자격 조건**: 페이지가 **색인되어 있고, 스니펫과 함께 Google 검색에 표시될 자격**이 있으면 됨(=Search 기술요건 충족). "그것 외 추가 기술요건 없음."
- 콘텐츠가 **크롤 가능**해야 함(생성 모델은 공개·크롤 가능 콘텐츠 사용). `robots`/`noindex`로 막으면 인용 불가.
- **멀티모달**: AIO는 관련 이미지·영상을 끌어옴 → 텍스트 링크 외 노출 기회. 이미지·영상 최적화 권장(A13).
- 구조화데이터는 AI 필수는 아니지만 **리치결과 자격 유지 목적으로 계속 권장**.

> 즉 "AEO/GEO는 여전히 SEO"(Google·SEJ 표현). 이 엔진의 **Google-AIO 전략 = 정상 SEO 최상급 + 발췌 친화 구조. 별도 마법 없음.**
> ※ 혼동 주의: [`AEO_GEO_PLAYBOOK_2026.md`](./AEO_GEO_PLAYBOOK_2026.md)가 권장하는 `llms.txt`는 **ChatGPT/Perplexity/Claude 등 비-Google 엔진용**이다 — 목적이 다르므로 둘 다 유지한다. **Google AIO에는 llms.txt가 무의미**할 뿐이다.

### 3.3 AI Mode(대화형)의 작동 — GEO 인용 표면
- AI Mode는 하나의 질문을 여러 하위 쿼리로 쪼개 병렬 검색하는 **query fan-out** + RAG로 답을 합성하고, 각 근거를 링크로 인용한다. → 인용 표면을 넓히려면 **하위 질문 각각에 답하는 세분화된 페이지·섹션**(하위 헤딩·명확한 엔티티)이 유리. 세부 GEO 전술·프롬프트·플랫폼별 인용 성향은 [`AEO_GEO_PLAYBOOK_2026.md`](./AEO_GEO_PLAYBOOK_2026.md)로 위임(중복 금지).

### 3.4 인용 확률을 높이는 것 (⚠ 3자 연구 종합, 확정 아님)
- 명확한 바이라인·자격 페이지, 외부 인용, 강한 백링크(BrightEdge: 인용의 상당수가 top-10 밖에서도 나옴 → 순위 밖도 인용 가능).
- **발췌 가능한 답변 블록**: 질문 바로 아래 40–60단어 직답, 명확한 H2/H3, 번호 리스트(절차)·불릿·비교 표(A10).
- 브랜드가 AIO에 인용되면 가시성 자체가 가치(연구별 유기·유료 클릭 상승 보고) — 단, 수치는 벤더별 편차 큼.

---

## 4. 피처드 스니펫 · People Also Ask (PAA)

- **피처드 스니펫 급감**: SERP 가시성이 크게 하락(단일 벤더 Keywords Everywhere 기준 2025-01 15.41% → 2025-06 5.53%, ⚠ 단일 소스라 검증강도 낮음). 같은 기간 AIO 상승. **피처드 스니펫과 AIO는 대개 동시 노출되지 않는다**(Google이 택일) — 방향성은 다수 3자 연구가 지지.
- 여전히 **정의형·팩트형·리스트형** 쿼리엔 유효("What is~", "How many~"). 획득법:
  - 상단에 40–60단어 직답 문단(Google이 "clip"하기 쉽게)
  - How-to = 번호 리스트, What-is = 짧은 문단, 비교 = 표, 명확한 H2/H3
- **PAA**: 실제 사용자 관련 질문의 신호 → 콘텐츠 확장/하위 헤딩·본문 FAQ 섹션(마크업 없이 Q/A)로 커버. **FAQ 리치결과는 죽었지만 본문 FAQ 콘텐츠는 PAA·AIO 인용에 유효**(A11).

---

## 5. 다국어(KR/EN) hreflang — 정확 구현

> 구현 위치·Next.js `alternates.languages` 코드·`.trim()` 버그 등 **기술 디테일은 [`WEB_PLAYBOOK.md`](./WEB_PLAYBOOK.md) §3.1을 참조**(중복 금지). 여기서는 **정확성 규칙**만.

- **형식**: `hreflang="언어[-지역]"`, 언어 = ISO 639-1(소문자 2자, `ko`,`en`), 지역 = ISO 3166-1 Alpha-2(대문자 2자). **영국은 `GB`**(`UK` 아님 — `uk`는 우크라이나 언어코드라 혼동 주의). 한국 지역타깃 필요시 `ko-KR`,`en-KR`. 언어만 타깃이면 `ko`,`en` 권장.
- **상호(reciprocal) 필수**: A→B면 B→A도. 리턴태그 누락이 최다 실패원인. **클러스터 내 하나라도 틀리면 Google이 클러스터 전체를 무시**.
- **자기참조 포함**: 각 페이지가 자기 자신 포함 모든 변형을 링크. 절대(fully-qualified) URL만.
- **x-default**: 언어·지역 매칭 안 되는 사용자용 폴백(보통 언어선택/영문 글로벌 페이지).
```html
<link rel="alternate" hreflang="en" href="https://www.korvia.com/e2-visa" />
<link rel="alternate" hreflang="ko" href="https://www.korvia.com/ko/e2-visa" />
<link rel="alternate" hreflang="x-default" href="https://www.korvia.com/e2-visa" />
```
- **구현 위치 택1 일관**: (a) `<head>` `<link rel="alternate" hreflang>`(Next.js=metadata `alternates.languages`), (b) HTTP 헤더(비HTML), (c) XML 사이트맵. Kimchi(WordPress)는 플러그인, Next.js 3사는 App Router `alternates.languages`.
- **Google 취급 = 힌트(hint)**: canonical·사이트구조·콘텐츠 유사도·색인 여부가 실제 표시 버전을 좌우(2025-05 재확인). **canonical과 hreflang 충돌 금지 → 각 언어판 self-canonical**(A12).
- ⚠ **Naver는 hreflang 미준수** → Naver 대상 콘텐츠는 위젯·메뉴·푸터·이미지 내 텍스트까지 **전부 한국어**여야(A16, 별도 Naver 문서).

---

## 6. 브랜드 SERP · 사이트링크 · 지식패널

- **사이트링크**: 브랜드 검색 시 자동 생성(수동 마크업 불가). 영향 요소 = 명확한 사이트 구조·네비게이션 계층·서술적 페이지 제목·내부링크. (Sitelinks *Searchbox* 스키마는 2024-11-21 폐지 — 넣지 말 것.)
- **사이트명**: `WebSite` + `name`/`alternateName`으로 검색결과 표기 사이트명 제어 — **여전히 지원**(Searchbox만 죽음). §1.3 코드.
- **지식패널/로고**: `Organization` 스키마(logo·sameAs·founder·foundingDate)로 **간접 영향**. Google은 Wikipedia/Wikidata/공식사이트/스키마/SNS/Crunchbase 등 다중 소스 종합. **엔티티 일관성**(동일 명칭·로고·바이오)이 핵심.
- **엔티티 강화 실무**: Wikidata 항목·일관된 NAP(Name/Address/Phone)·`sameAs`로 SNS 연결·`knowsAbout`로 전문영역 선언.

---

## 7. 적용 우선순위 매트릭스

| 우선 | 조치 | 대상 |
|---|---|---|
| P0 | FAQPage/HowTo/Sitelinks Searchbox JSON-LD **생성 금지**(기존 제거) | 4개 전부 |
| P0 | `Organization`(logo·sameAs·knowsAbout) 홈에 1개 | 4개 전부 |
| P0 | 실명 저자 + `author.url`/`sameAs` 바이오 링크 | Passome·Korvia·ACT 블로그 |
| P0 | YMYL 신뢰블록(자격·출처·dateModified) | Korvia(비자/취업)·Kimchi(통신/금융) |
| P1 | `JobPosting` 필수 5 + `validThrough`·만료제거·리스트 미부착 | Korvia 채용 |
| P1 | `Product`+`offers`(price/currency/availability/priceValidUntil) | Kimchi eSIM |
| P1 | 답변형 구조(40–60단어 직답·H2/H3·리스트·표) | 전 콘텐츠(AIO/스니펫) |
| P1 | reciprocal hreflang + x-default(self-canonical) | KR/EN 이중언어 전부 |
| P2 | Breadcrumb·Event·LocalBusiness·Video | 해당 페이지 |
| P2 | `WebSite` 사이트명 제어(name/alternateName) | 4개 전부 |

---

## 8. 크로스링크
- 범용 AI 답변엔진 전술·플랫폼별 인용·llms.txt·측정 → [`AEO_GEO_PLAYBOOK_2026.md`](./AEO_GEO_PLAYBOOK_2026.md)
- 퍼블리싱/기술 표준·schema 주입 코드·robots·CLS·hreflang 구현 위치 → [`WEB_PLAYBOOK.md`](./WEB_PLAYBOOK.md)
- 신규 페이지 최소 체크리스트 → [`NEW_PAGE_CHECKLIST.md`](./NEW_PAGE_CHECKLIST.md)
- 콘텐츠 무결성·슬롭 게이트(YMYL 하드페일) → `slop-judge` 스킬
- 실행 에이전트 팀(사업분석→키워드→SEO→기술감사→GEO→재작성) → `seo-harness-team` 스킬
- Naver/한국 검색 표면 → (별도 Naver 차원 문서)

---

## Sources

**Google 공식 (1차)**
- https://developers.google.com/search/docs/appearance/structured-data/search-gallery — Search Gallery(지원 타입)
- https://developers.google.com/search/updates — changelog(폐지 일자 원문)
- https://developers.google.com/search/blog/2025/06/simplifying-search-results — Simplifying the search results page
- https://developers.google.com/search/blog/2024/10/sitelinks-search-box — Farewell, Sitelinks Search Box
- https://developers.google.com/search/blog/2023/08/howto-faq-changes — HowTo/FAQ 축소
- https://developers.google.com/search/blog/2024/03/core-update-spam-policies — 2024-03 코어+스팸(HCU 통합)
- https://developers.google.com/search/docs/fundamentals/ai-optimization-guide — Optimizing for Generative AI Features
- https://developers.google.com/search/docs/appearance/ai-features — AI Features and Your Website
- https://developers.google.com/search/docs/appearance/structured-data/job-posting — JobPosting
- https://developers.google.com/search/docs/appearance/structured-data/article — Article
- https://developers.google.com/search/docs/appearance/structured-data/organization — Organization
- https://developers.google.com/search/docs/appearance/structured-data/product — Product
- https://developers.google.com/search/docs/specialty/international/localized-versions — hreflang
- https://developers.google.com/search/docs/appearance/featured-snippets — Featured Snippets
- https://developers.google.com/search/docs/appearance/structured-data/sd-policies — 구조화데이터 정책

**3자 (⚠ 추정·방향성만)**
- https://searchengineland.com/google-drops-reporting-on-several-structured-data-types-461744 — 9월 6종 제거(Book Actions 제외)
- https://www.etavrian.com/news/google-deprecates-practice-problem-structured-data — Practice Problem 폐지 + Book Actions 배너 제거
- https://searchengineland.com/google-to-remove-more-search-features-including-practice-problems-nutrition-facts-nearby-offers-and-more-464255 — 2026-01 배치
- https://searchengineland.com/google-ai-overviews-surge-pullback-data-466314 — AIO 노출·zero-click·Pew 1%
- https://www.semrush.com/blog/semrush-ai-overviews-study/ — Semrush AIO 연구
- https://searchengineland.com/google-march-2026-core-update-rollout-is-now-complete-473883 — March 2026 코어 완료(4/8)
- https://www.gsqi.com/marketing-blog/google-march-2024-core-update-helpful-content-system/ — HCU 코어 통합
- https://www.searchenginejournal.com/googles-new-ai-search-guide-calls-aeo-and-geo-still-seo/575026/ — "AEO/GEO still SEO"
- https://keywordseverywhere.com/blog/are-featured-snippets-still-a-thing-2025-seo-guide/ — 피처드 스니펫 하락(단일 소스)
- https://www.theegg.com/seo/korea/how-naver-ai-search-is-reshaping-the-korean-digital-landscape/ — Naver AI Briefing·hreflang 미준수
