---
name: 새 Next.js 페이지 체크리스트 (재사용 템플릿)
description: 새 페이지 만들 때마다 최소 적용할 SEO+CLS+a11y+성능 체크리스트 17항목. www.korvia.com 21 커밋 라운드에서 도출. 다른 프로젝트(jobinkorea.co.kr, Korvia Portal, 신규 MVP)에도 동일 적용.
type: reference
originSessionId: 10654995-dff2-4b5c-a086-7c644c9a3bb1
---

> ⚠ **2026-07 갱신 안내**: §B(Schema)의 FAQPage/HowTo 항목은 이후 폐지로 **정정**됨. 새 페이지 발행 게이트의 최신·완전판은 [`CONTENT_AUTORULES.md`](./CONTENT_AUTORULES.md)(멀티엔진·GEO·한국엔진·이중언어 포함)를 사용하고, 이 17항목은 Next.js CLS/a11y 보조 체크로 병행.
**목적**: 새 Next.js 페이지나 섹션 만들 때마다 "최소한 이 17개는 체크"

## A. 메타데이터 (6개)
1. [ ] `metadata.title` — 50~60자, 키워드 front-loaded, layout template이 `| Brand` 붙이면 per-page에 또 붙이지 말 것
2. [ ] `metadata.description` — **155자 이내**, CTA 포함, Google SERP 절단 방지
3. [ ] `metadata.alternates.canonical` — 절대 URL (`https://...` 전체)
4. [ ] `metadata.openGraph` 전체 필드: `title`, `description`, `url`, `siteName`, `type`, `locale`, `images: [{url, width: 1200, height: 630, alt}]`
5. [ ] `metadata.twitter`: `card: "summary_large_image"`, title, description (≤120자), images
6. [ ] `metadata.alternates.languages` (EN↔KO 짝 있을 때만) — 상대방 canonical + `x-default`

## B. Schema.org JSON-LD (3개)
7. [ ] **BreadcrumbList** — `<JsonLd data={buildBreadcrumbJsonLd([...])} />` 모든 페이지 최소
8. [ ] **Page-specific schema** — 페이지 타입에 맞는 것 하나 이상:
   - 블로그/매거진 → `buildArticleJsonLd`
   - Q&A 섹션 有 → `buildFaqJsonLd` (추가 가능, Article과 공존 OK)
   - 허브 → `buildServiceJsonLd`
   - 이벤트 → inline Event schema (Place + PostalAddress + Offers + Organizer)
   - 프로세스/타임라인 → inline HowTo schema (step: HowToStep[])
   - 블로그 인덱스 → inline CollectionPage (about: Thing[] 엔티티 리스트)
9. [ ] 루트 Organization + WebSite는 `app/layout.tsx`에 이미 있음 (재주입 금지)

## C. 이미지 (4개)
10. [ ] 모든 `<Image>` 또는 `<img>`에 **명시적 `width` + `height`** (HTML attribute, CSS 말고)
11. [ ] **Hero/LCP 이미지**: `priority` + `fetchPriority="high"` + 정밀한 `sizes` (100vw 대신 실제 최대폭 cap)
12. [ ] Above-fold 이미지에 `loading="lazy"` 절대 금지
13. [ ] CDN 이미지 `unoptimized` 제거 (remotePatterns에 있으면) → AVIF/WebP + responsive srcset

## D. CLS 방어 (3개)
14. [ ] 애니메이션은 `transform` + `opacity` + `filter: blur()` 만 사용 (layout 속성 금지)
15. [ ] Intl API 로케일 명시: `.toLocaleString("en-US")`, `.toLocaleDateString("en-US", {...})`
16. [ ] 3rd-party 위젯 자리에 `min-height` 폴백 (출현/사라짐 CLS 방지)

## E. 접근성 (1개)
17. [ ] `<img alt="...">` — 내용 설명 + 키워드. 장식 이미지는 `alt=""`

---

## 페이지 타입별 추가 체크

### 블로그 포스트 (매거진/ko/blog 스타일)
- [ ] Article schema (`buildArticleJsonLd`) 주입
- [ ] `publishedTime` in openGraph (locale 맞춤: `en_US` or `ko_KR`)
- [ ] 본문 `<h1>` 하나, `<h2>` `<h3>` 계층 명확
- [ ] 내부 링크 3~5개 (관련 포스트 + 프로그램 허브)

### 프로그램 허브 (EPIK/GEPIK 스타일)
- [ ] Service JSON-LD (`buildServiceJsonLd`) + `provider: { @id: org-id }`
- [ ] FAQPage schema (Q&A 섹션 있을 때)
- [ ] 서브페이지 링크 전부 body에 (nav만 쓰면 authority 약함)
- [ ] Breadcrumb 3층: Home → [Program] Hub → (현재)

### 이벤트 페이지
- [ ] Event schema (Place + PostalAddress + Offers + Organizer + sponsor[])
- [ ] `sitemap.ts`의 EVENT_SUBPAGES 배열에 추가
- [ ] hero CSS background-image 대신 `<Image fill priority>` (LCP 위해)

### 프로세스 / How-To 페이지
- [ ] HowTo schema (step: HowToStep[] 기존 STEPS 배열 매핑)
- [ ] `totalTime` 실제 소요 시간 (ISO 8601, e.g., P6M)

---

## 빌드 전 검증 (배포 직전 3개)
- [ ] `npm run build` → 0 errors (prerender 에러는 `/preview-*` 같은 untracked만 허용)
- [ ] `curl -sL https://<site>/page | grep -oE '<link rel="canonical"[^>]*>|"@type":"[A-Za-z]+"'` — canonical + schema 확인
- [ ] Lighthouse Desktop + Mobile (Chrome DevTools) — Performance 90+, SEO 100, A11y 95+, BP 85+

---

## 대규모 작업 (N+10 페이지) 프로세스
1. 감사 먼저 (1 agent): 타겟 파일 목록 + 원인 매핑
2. 병렬 Worker (N agent): 도메인별 분리 실행 (CLS/SEO/Perf/a11y 각자)
3. 독립 Audit (1 agent): Worker 결과 검증 (0 fix 권한, 감사만)
4. 최종 검증 (사람): `git status` + `git diff --cached` 확인 후 논리적 커밋 분리
5. 배포 후 5분 뒤 Lighthouse 재측정

상세: [`./3LAYER_AGENT_PIPELINE.md`](./3LAYER_AGENT_PIPELINE.md) (패턴 SSOT)
전체 가이드: [`./WEB_PLAYBOOK.md`](./WEB_PLAYBOOK.md) — 스킬 이식성을 위해 상대경로만 사용(절대경로 금지)
