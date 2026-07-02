# Korvia Web Playbook — SEO + Performance + Accessibility
> Distilled from 2026-04-17~18 www.korvia.com audit/implementation session (21 commits)
> Use as checklist for any new Next.js page or site-wide audit
>
> ⚠ **2026-07 갱신 안내**: schema 일부(FAQPage/HowTo/SearchAction)는 이후 Google 리치결과 폐지로 **정정**됨. 스키마=[`SCHEMA_JSONLD_CATALOG.md`](./SCHEMA_JSONLD_CATALOG.md), robots/sitemap=[`ROBOTS_SITEMAP_RSS_TEMPLATES.md`](./ROBOTS_SITEMAP_RSS_TEMPLATES.md), 발행 전 게이트=[`CONTENT_AUTORULES.md`](./CONTENT_AUTORULES.md)를 최신 기준으로 따르라.

---

## 0. Pipeline 원칙 — "감사 먼저, 수정 나중"

Henry의 느낌대로 바로 수정하지 않는다. 패턴:

```
[외부/내부 감사] → [실행 Agent 병렬] → [감사 Agent 독립 검증] → [내가 최종 빌드+커밋]
```

- Worker 한 명이 자기 일 평가하면 과신함 → 감사 Agent 독립 분리
- 실행 Agent는 "의도"만 보고함, 실제는 감사 Agent가 확인
- 대량 작업 (20+ 파일)일수록 병렬 필수 — 세션 시간 60% 단축

---

## 1. SEO — robots/sitemap/llms.txt

### 1.1 robots.txt
- [ ] AI 크롤러 명시 허용 (리드 비즈니스면): `GPTBot`, `OAI-SearchBot`, `ChatGPT-User`, `ClaudeBot`, `anthropic-ai`, `Claude-Web`, `PerplexityBot`, `Perplexity-User`, `Google-Extended`, `CCBot`, `Applebot-Extended`
- [ ] 알려진 악성: `Bytespider` → Disallow
- [ ] 각 User-Agent마다 `Allow: /` + `Disallow: ['/api/', '/_next/', '/admin/']`
- [ ] **환경변수는 항상 `.trim()`** — trailing newline이 `Sitemap: https://...\n/path` 식으로 깨짐 (실제 버그)

### 1.2 sitemap.xml
- [ ] 카테고리별 priority 차별화: Transactional=1.0w / ContentHub=0.9w / ProgramHub=0.9m / Subpage=0.8m / Blog=0.7m / Legal=0.3y
- [ ] 동적 페이지는 filesystem 또는 DB에서 enumerate (하드코딩 drift 주의)
- [ ] 저가치 WP-legacy URL은 `SITEMAP_PRUNED_SLUGS` set으로 제외 (crawl budget)
- [ ] 숫자 슬러그 (`/3972-2`), 날짜 지난 공지, 이벤트 recap = prune 대상
- [ ] 페이지는 200 응답 유지 (soft-delete = sitemap에서만 제거)

### 1.3 llms.txt (llmstxt.org 규격)
- [ ] `/public/llms.txt` 생성 — AI 크롤러 전용 discovery 파일
- [ ] 포함: Entity facts, canonical URLs, preferred attribution, crawl policy
- [ ] 특히 "Preferred Attribution" 섹션 — AI가 인용할 때 쓸 정확한 문구

---

## 2. Schema.org — 엔티티 그래프

### 2.1 Root layout (사이트 전역 주입)
- [ ] **Organization** — `@type: ['Organization', 'ProfessionalService']` 듀얼 타입 + `@id`
  - `name`, `alternateName` (법인명 포함), `logo`, `image`, `url`
  - `foundingDate`, `founder` (Person), `address` (PostalAddress)
  - `areaServed`, `knowsAbout` (entity list), `slogan`
  - `sameAs` (SNS), `contactPoint`, `hasMap`
- [ ] **WebSite** — `@id` + `publisher: { @id: org-id }`
  - `potentialAction: SearchAction` (Google sitelinks search box)

### 2.2 Per-page schema
- [ ] **BreadcrumbList** — 모든 hub/detail 페이지
- [ ] **Article/BlogPosting** — 블로그/매거진 (datePublished, dateModified, author, publisher)
- [ ] **FAQPage** — `{q, a}` 섹션 있는 모든 페이지 (버튼 없어도 OK, JSX에 있으면 schema로 노출)
- [ ] **Event** — 행사 (Place + PostalAddress + Offers + Organizer)
- [ ] **HowTo** — 프로세스/타임라인 페이지 (STEPS 배열을 step: HowToStep[]로 매핑)
- [ ] **Service** — 서비스/프로그램 허브 (provider @id 참조)
- [ ] **CollectionPage** — 블로그 인덱스 (about: Thing[] 엔티티 리스트)

### 2.3 FAQPage 주입 패턴
- 데이터가 `{q, a}` 형식이면 `buildFaqJsonLd(FAQ.map(({q,a}) => ({question: q, answer: a})))`
- JSX `.map()` 안에 inline되어 있으면 `const FAQ = [...]`로 hoist 필수
- Article과 FAQPage 공존 가능 (각자 별도 `<JsonLd>` 노드)

---

## 3. Metadata — OpenGraph + Twitter

### 3.1 Per-page metadata (Next.js App Router)
- [ ] `title`: 50~60자, 키워드 front-loaded. Template이 `| Brand` 붙이면 per-page에 또 붙이지 말 것 (이중 브랜드 방지)
- [ ] `description`: **155자 이내** (Google SERP 절단)
- [ ] `alternates.canonical`: 절대 URL (https://... 전체)
- [ ] `alternates.languages`: `{en: ..., ko: ..., "x-default": ...}` (짝이 있을 때만)
- [ ] `openGraph` 전체 필드:
  - title, description (shorter), url, siteName, type, locale
  - images: `[{url, width: 1200, height: 630, alt}]`
- [ ] `twitter`:
  - card: `summary_large_image`
  - title, description (≤120자), images

### 3.2 OpenGraph 함정
- [ ] openGraph에 `type: 'article'`만 있고 images/url/siteName 누락 = 소셜 프리뷰 깨짐 (partnership 페이지 케이스)
- [ ] images URL은 **절대 URL**만 (상대 경로 금지)
- [ ] publishedTime (article type) + locale (en_US/ko_KR 구분)

---

## 4. CLS — 레이아웃 안정성 (최우선)

### 4.1 이미지
- [ ] **Raw `<img>`**: 반드시 `width={N} height={M}` HTML 속성 (CSS 말고 HTML attr)
- [ ] **next/image**: `fill` + 부모 `position: relative` + `aspect-[w/h]`
- [ ] **LCP 이미지**: `priority` + `fetchPriority="high"` + `sizes`
- [ ] **Above-fold 이미지에 `loading="lazy"` 절대 금지**
- [ ] `unoptimized` 제거 (CDN이 remotePatterns에 있으면) → next/image AVIF/WebP 자동 변환 → 평균 113KB/페이지 절감

### 4.2 Swiper / 캐러셀 (치명적 CLS 소스)
- [ ] `loop` 대신 `rewind` 사용 (loop는 DOM 복제 → hydration mismatch)
- [ ] Swiper CSS는 **globals.css (critical path)** 에 핵심 높이 규칙 선언
  - styled-jsx는 **client-side만** 로드되어 SSR HTML에 적용 안 됨
- [ ] Pre-hydration 보호: `.swiper-slide { position: absolute; inset: 0 }` + `.swiper-slide:not(:first-child) { visibility: hidden }` (7개 슬라이드가 700vh로 쌓이는 것 방지 — 실제 발견한 dominant CLS 원인)

### 4.3 애니메이션
- [ ] **허용**: `transform` (translateY, scale, rotate), `opacity`, `filter: blur()` — GPU composited
- [ ] **금지**: `top`, `bottom`, `left`, `right`, `margin*`, `padding*`, `width`, `height` — layout 재계산 유발
- [ ] 장식적 `margin-left` 애니메이션은 absolutely-positioned 엘리먼트 한정으로만 사용 (document flow 밖)

### 4.4 숫자 카운터
- [ ] `tabular-nums` 클래스 (monospaced digits)
- [ ] `inline-block` + inline `style={{ minWidth: \`${finalLabel.length}ch\` }}` — 최종 값 폭 paint-0부터 예약
- [ ] `toLocaleString('en-US')` 명시 (브라우저 locale에 따라 `10,000` vs `10.000` 차이 → #418)

### 4.5 폰트
- [ ] `next/font/google` + `display: swap` + `adjustFontFallback: true` (default) — 변경 금지
- [ ] `subsets: ['latin']` 등 필요 범위만

---

## 5. Hydration (React #418) 제거

### 5.1 흔한 원인
- [ ] `toLocaleString()` / `toLocaleDateString()` locale 누락
- [ ] `new Date()` / `Date.now()` / `Math.random()` 렌더 중 사용
- [ ] `typeof window !== 'undefined'` 분기가 JSX 안에서 직접 렌더 변경
- [ ] 쿠키/localStorage 기반 조건부 렌더

### 5.2 해결
- [ ] Intl API: 명시적 locale (`'en-US'`, `'ko-KR'`)
- [ ] 브라우저 전용 로직: `useEffect` 안으로만
- [ ] 완전 client-only: `dynamic(..., { ssr: false })`
- [ ] 시간 기반 UI: 서버 타임존 (UTC) 고정 후 클라이언트에서 local 변환

---

## 6. 성능 — JS/CSS/Image

### 6.1 Third-party 스크립트
- [ ] GTM/Clarity/Bing UET: `next/script strategy="lazyOnload"` + `<noscript>` iframe fallback (body 첫 자식)
- [ ] `@next/third-parties/google` GoogleTagManager는 `afterInteractive` 고정 — strategy 커스텀 불가, 필요 시 수동 구현
- [ ] `preconnect` 실제 사용 도메인만 (과다 금지)

### 6.2 Bundle
- [ ] `package.json` browserslist: `[">0.5%", "not dead", "not op_mini all", "not IE 11", "last 2 years"]` — 모던 브라우저 타겟 → legacy JS -14KB
- [ ] `next.config.ts`: `experimental: { optimizeCss: true }` + `critters` devDep → render-blocking CSS -170ms
- [ ] 대상 데모그래픽에 IE/구형 Safari <0.5%일 때만 적용

### 6.3 이미지
- [ ] `next.config.ts` → `images: { formats: ['image/avif', 'image/webp'], minimumCacheTTL: 31536000, remotePatterns: [...] }`
- [ ] CDN 이미지 `unoptimized` 제거 (AVIF/WebP 변환 활성화)
- [ ] 반응형: `sizes="(max-width: 640px) 100vw, (max-width: 1024px) 50vw, 33vw"`

### 6.4 CDN (Cloudflare R2)
- [ ] Cache Rule: Edge TTL + **Browser TTL override** 1년
  - Browser TTL "Respect origin" 상태면 브라우저 헤더 안 바뀜 (실제 발견한 함정)
- [ ] 컨텐츠 해시 URL 없으면 파일명 버저닝 (예: `poster-v2.webp`) — 1년 캐시와 상충

---

## 7. 보안 헤더 (next.config.ts)

### 7.1 즉시 추가 안전
- [ ] `Strict-Transport-Security: max-age=63072000; includeSubDomains; preload` (선언만, 제출은 별도 결정)
- [ ] `Cross-Origin-Opener-Policy: same-origin`
- [ ] `Cross-Origin-Resource-Policy: same-site`
- [ ] `X-Frame-Options: DENY`, `X-Content-Type-Options: nosniff`, `Referrer-Policy: strict-origin-when-cross-origin`, `Permissions-Policy: camera=(), microphone=(), geolocation=()`

### 7.2 CSP — Report-Only 우선
- [ ] `Content-Security-Policy-Report-Only` (NOT enforcing) 먼저
- [ ] 1~2주 모니터링 → 위반 없으면 enforcing 전환
- [ ] 사전에 `report-uri`/`report-to` 엔드포인트 구성 필요
- [ ] 써드파티 allowlist 모두 포함: GTM, GA, Bing UET, Clarity, CDN, Strapi, 임베드(Zoho/YouTube/Naver map)
- [ ] 당장은 `'unsafe-inline'` + `'unsafe-eval'` 허용 (Next.js 런타임 + GTM 요구) → 추후 nonce 도입 시 제거

### 7.3 HSTS preload 제출 주의
- [ ] `hstspreload.org` 제출은 **비가역** — 전 서브도메인 HTTPS 2년 이상 강제
- [ ] 1+주 안정 관찰 후 Henry 결정

---

## 8. Accessibility

### 8.1 컬러 대비
- [ ] 본문 보조 텍스트 (`.text-secondary`): 흰 배경에서 **4.5:1 이상** (WCAG AA)
  - Apple gray `#86868b` = 3.57:1 (AA fail) → `#636369` = 6.0:1 권장
- [ ] 브랜드 컬러 뱃지 (`bg-color/10 + text-color`): 3.38:1 정도 — AA-Large만 통과, 본문 fail
  - 브랜드 보존 우선 시 의도적 허용 (decorative 라벨 한정)
  - AA까지 올리려면 tint 대신 solid 배경 + white text

### 8.2 이미지 alt
- [ ] 모든 `<img>` + `next/image`에 alt (장식이면 `alt=""`)
- [ ] 내용 설명 + 키워드 포함 ("Korean classroom — EPIK teaching program" 스타일)

---

## 9. 내부 링크 구조

### 9.1 허브 일관성
- [ ] 프로그램 허브 URL 규칙 통일 — `/korvia-{program}` vs `/{program}-full-name` 혼용 금지
  - **실제 발견**: `/korvia-gepik`이 nav에서 고아 (header/footer가 `/gepik-gyeonggi-...`로 링크) → EPIK/SMOE는 `/korvia-*` 사용
- [ ] Header mega-menu + Footer Programs rail 링크 대조 확인

### 9.2 Load-more 패턴 주의
- [ ] JS "load more" 뒤에 숨긴 컨텐츠는 Googlebot에 오펀
  - 해결: 초기 `visibleCount = posts.length`로 SSR 전부 렌더
  - 카테고리 필터는 CSS `hidden` 토글 (DOM에는 존재)

### 9.3 Orphan 감지
- [ ] Sitemap URLs vs 실제 hub 페이지 body links 대조
- [ ] 감사 샘플: top 10 hub의 body 링크 합집합 = reachable 집합
- [ ] Sitemap - reachable = orphan 후보 리스트

### 9.4 Dead-end 허브
- [ ] 허브 페이지 body 링크가 2~3개 미만이면 기능 못 함
- [ ] 해결: 관련 articles / sub-pages / archived events 렌더링

---

## 10. 외부 데이터 vs 합성 측정 이해

### 10.1 CrUX (Chrome User Experience Report)
- **28일 롤링 윈도우** — 수정 후 반영까지 2~4주
- **실사용자 디바이스/네트워크** — 합성보다 나쁘게 나옴
- 특히 **CLS는 실제 RUM이 현실**
- Lighthouse desktop은 synthetic, CrUX는 real-user — 불일치 정상

### 10.2 Lighthouse
- 합성 테스트 (Chrome headless, 고정 CPU/network throttle)
- **즉시 반영** — 배포 5분 뒤 측정 가능
- SEO/a11y는 static signal이라 Lighthouse가 정확
- CWV는 Lighthouse < CrUX (실사용자가 더 엄격)

### 10.3 Search Console
- Sitemap 재제출 후 재크롤: 1~3일
- Core Web Vitals 리포트: CrUX 기반 (28일)
- 색인 상태: 1~7일

---

## 11. Next.js 함정 모음

### 11.1 styled-jsx는 client-only
- styled-jsx 안에 선언한 CSS는 **SSR HTML에 없음**
- Critical sizing/layout은 `globals.css`에만
- 애니메이션 keyframes 정도는 styled-jsx OK

### 11.2 Tailwind v4 + `@theme inline`
- CSS 변수 → Tailwind 클래스 자동 매핑
- 토큰 한 번 바꾸면 사이트 전역 반영
- 소수점 arbitrary value (`text-[14.5px]`) 불안정 — globals.css 유틸리티로

### 11.3 `@next/third-parties`
- GoogleTagManager component는 `afterInteractive` 고정
- strategy 커스텀 필요 시 `next/script` 수동
- 제거 후 package.json 정리

### 11.4 MetadataRoute
- `app/robots.ts` + `app/sitemap.ts` 타입 안정적 방식
- `rules: []` 배열로 User-Agent별 규칙 정의
- `sitemap` 필드는 반드시 절대 URL 단일 문자열

### 11.5 `optimizeCss + critters`
- Tailwind v4 + 동적 클래스에서 간혹 FOUC
- Vercel preview 스모크 테스트 필수
- 롤백: `experimental.optimizeCss: false`

### 11.6 Image optimization
- `images.remotePatterns`에 도메인 넣고 `unoptimized` 제거
- 차이: `<img src="cdn.../x.webp">` (원본) vs `<Image src="cdn.../x.webp">` (AVIF 변환 + srcset)
- 평균 절감: 113KB/페이지

---

## 12. 공격적 허용 정책 (Korvia 특화)

### 12.1 AI 크롤러
- Korvia는 ESL 리드 비즈니스 → AI 인용 = 잠재 리드
- 11개 AI bot ALLOW + 1개 차단 (Bytespider)

### 12.2 브랜드 컬러 보존
- `#3288B3` Korvia Blue 변경 금지 (Henry 결정)
- 대비 개선이 필요하면 뱃지 패턴 변경 (solid vs tint) 또는 `.text-secondary` 같은 보조 토큰만 조정

### 12.3 한/영 병렬
- EN 메인 + `/ko` 세컨더리
- OG locale: page content 언어 기준 (`en_US` vs `ko_KR`)
- hreflang 짝은 실제 editorial equivalent 있을 때만

---

## 13. 실수 막는 체크리스트 (새 페이지 만들 때)

각 새 페이지에 최소한 이것들:
- [ ] `metadata.title` + `metadata.description` (≤155자)
- [ ] `metadata.alternates.canonical` (절대 URL)
- [ ] `metadata.openGraph` 전체 필드 + twitter card
- [ ] `<JsonLd data={buildBreadcrumbJsonLd([...])} />`
- [ ] 이미지 모두 `<Image width height priority sizes />` or `<img width height>`
- [ ] `<Image>`에 `unoptimized` 없음 (CDN이 remotePatterns에 있으면)
- [ ] Q&A 섹션 있으면 `<JsonLd data={buildFaqJsonLd(...)} />`
- [ ] 허브 페이지면 `<JsonLd data={buildServiceJsonLd(...)} />` 또는 CollectionPage
- [ ] 애니메이션은 transform/opacity만
- [ ] 클라이언트 상태 값은 SSR/CSR 동일 (Intl.locale 명시)

---

## 14. 체크리스트 — 외부 감사 받은 후

1. [ ] 감사 리포트 읽고 항목별 분류 (Critical / Warning / Note)
2. [ ] 각 항목의 실제 소스 코드 위치 확인 (감사는 증상, 코드는 원인)
3. [ ] 병렬 가능한 수정은 Agent 병렬 실행
4. [ ] 수정 후 감사 Agent로 독립 검증
5. [ ] 빌드 + 3-5분 뒤 live 확인
6. [ ] Search Console sitemap 재제출
7. [ ] CrUX는 28일 후 재확인 (조급해 하지 않기)
8. [ ] 리포트 저장: `reports/{task-name}/` — README + raw + execution-log

---

## 15. 모바일 추가 학습 (2026-04-18 mobile Lighthouse)

### 15.1 CrUX는 기다릴 수 없다
- 실사용자 데이터 CLS 0.89 표시됐을 때 이미 수정은 끝남 → **28일 롤링**이라 즉시 반영 안 됨
- 절대 수치에 집착 말고 **수정 후 Lighthouse synthetic + DevTools Layout Shift Regions**로 검증
- "CLS 0.89 알림이 여전히 온다"는 "수정 전 4주 데이터가 아직 살아있다"는 뜻

### 15.2 TestimonialCarousel 패턴 (Swiper + 아바타)
**기존 우려**: Swiper 슬라이드 안에 `<Image fill>`은 quirks 있음 → raw `<img>` 유지
**실제 문제**: raw `<img>`가 CDN 원본 사이즈(250x250) 그대로 → 36x36 표시에 6.9× 낭비
**해결**: `<Image width={36} height={36} sizes="36px">` (fill 아님, 고정 크기) → Swiper 안에서도 문제 없음, AVIF 자동 변환으로 **페이지당 72KB 절감**

### 15.3 Preconnect 오버유즈
- Lighthouse "Unused preconnect" 경고 = preconnect 선언했지만 초기 몇 초 안에 사용 안 함
- dns-prefetch는 거의 공짜 (DNS만) → 항상 유지 OK
- preconnect는 비용 있음 (TCP + TLS) → 실제로 첫 100ms 안에 쓰는 도메인만
- GTM lazyOnload 적용 후 `googletagmanager.com` preconnect는 **제거 필수** (2~5초 뒤에나 사용)

### 15.4 hero 이미지 `sizes` 정밀화
- `sizes="100vw"`는 편하지만 와이드 데스크탑에서 1920w fetch → 19KB 낭비
- 실제 hero 표시 최대폭에 맞춰 cap: `sizes="(max-width: 640px) 100vw, (max-width: 1440px) 100vw, 1440px"`
- `object-cover` + 어두운 오버레이가 있으면 upscale 약간 OK

### 15.5 React #418 최후의 수단
- 모든 deterministic 원인 제거했는데도 #418 지속 → 크롬 확장 DOM 주입 또는 Swiper 내부 attribute mutation 가능성
- 루트 원인 못 찾을 때는 **`suppressHydrationWarning`** 단일 엘리먼트 수준 (자식 아님)
- 적용 우선: Swiper 래퍼, `usePathname()` 의존 조건부 렌더, 3rd-party 위젯 자리
- 함정: 실제 UI 불일치도 숨겨짐 → 배포 후 수동 QA 필수

### 15.6 3rd-party 비용 인지
- GTM 자체 282KB 중 125KB unused → Google이 제공하는 만큼만 줄이기 (태그 정리는 GTM 대시보드)
- Clarity 1-day cache TTL → 외부 설정이라 우리 못 고침
- Array.prototype.at / flat / flatMap / Object.fromEntries 폴리필 14KB → browserslist modern 타겟으로 제거

### 15.7 Vercel 배포 지연 주의
- git push 후 빌드 2~3분, 엣지 전파 30초~1분
- 합성 Lighthouse 돌리기 전 최소 **5분 대기** 후 bundle 해시 바뀐 것 확인
- 로컬 npm run build 통과했지만 프로덕션 bundle에 fix 반영 안 된 경우 = 배포 지연. 커밋 해시 vs 현재 bundle 해시 대조.

---

## 16. 레퍼런스 커밋 (www.korvia.com 2026-04-17~18)

```
31e6545  Tier 0 + 매거진/events/partnership/ko-blog 기초
40c85b8  ko/blog 19편 canonical + BlogPosting
1edc135  FAQPage 14페이지
9f3a36a  desc 클램프 + title 중복 제거
412463b  Entity graph (founder + address + WebSite)
feb0d6a  Service JSON-LD 4 허브
366e0c5  /magazine + /events CollectionPage
8f7dbb6  /ko BreadcrumbList
a7a8de1  CRITICAL: robots.txt Sitemap .trim() 버그 수정
324260f  /korvia-gepik 고아 + ko/blog SSR + /events LCP
6b5ebc2  WP-legacy 44개 prune
13dbc1b  MagazineGrid next/image
92cf92b  React #418 hydration fix
195817e  FAQPage 26 파리티
0d5b743  text-secondary 대비 AA
0a32fe9  OG blocks 22 + HowTo 4
42a7d9d  CLS 1차 (30 img width/height)
42f7621  GTM lazyOnload
22437ac  HSTS + COOP + CORP + CSP
e1dffdd  Desktop CLS (Swiper pre-hydration + AnimatedCounter)
599cf4c  browserslist + optimizeCss + unoptimized 제거
db5f141  CSP 확장 (Clarity subdomain + Google Ads + Consent Mode)
5dfa444  모바일 follow-up (TestimonialCarousel 72KB + preconnect 정리 + #418 safety net)
```

각 커밋의 commit message에 **"Why (문제) + What (수정) + Impact (예상 효과)" 3단 구성** — 1년 뒤 봐도 이해 가능하도록.
