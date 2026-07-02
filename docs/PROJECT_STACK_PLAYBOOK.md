---
last_verified: 2026-07
reverify_by: 2026-Q4
---

# 프로젝트 스택별 구현 (Next.js / WordPress)

> Generated 2026-07-01 · Owner: Henry Oh (KORVIA) · ⚠ 콘솔/API 변경은 사용 전 재확인
>
> **대상 사이트**
> | 사이트 | 스택 | 호스팅 | 청중·YMYL |
> |---|---|---|---|
> | Passome (www.passome.com) | Next.js App Router | Vercel | AI/AX·미술심리 (B2B 위주) |
> | Korvia (www.korvia.com) | Next.js App Router | Vercel | 외국인 취업/EPIK — **YMYL(비자·취업)** |
> | ACT Art Center (actartcenter.com) | Next.js App Router | Vercel | 미술교육·로컬 |
> | kftra.org | Next.js App Router | Vercel 여부 확인 ⚠ | Site Migration 이관 중 (⚠배선 전 범위 확인) |
> | Kimchi Mobile (www.kimchimobile.com) | WordPress | **WP Engine** | 외국인 통신/eSIM — **YMYL(금융·통신)** |
> | jobinkorea.co.kr | WordPress | — | 구직 (레거시) |
>
> **이 문서의 위치**: `korvia-seo-skill/docs/`의 실행 레시피 레이어. **중복 금지·상호링크**한다.
> - ⚠ **레포별 Next.js 버전 = §A-0 버전 핀표(SSOT)** — 단일 '16.2.x' 기준 적용 금지(Passome는 15.x).
> - 왜/무엇/GEO 전략·플랫폼별 인용 → [`./AEO_GEO_PLAYBOOK_2026.md`](./AEO_GEO_PLAYBOOK_2026.md)
> - korvia.com 감사 산출 심화(CLS·hydration·보안헤더·성능·JSON-LD 빌더 함수) → [`./WEB_PLAYBOOK.md`](./WEB_PLAYBOOK.md)
> - 새 페이지 17항목 최소 체크 → [`./NEW_PAGE_CHECKLIST.md`](./NEW_PAGE_CHECKLIST.md)
>
> 본 문서 = **스택별 구현 메커니즘 + 멀티엔진(Google/Naver/Daum/Bing) 소유확인·제출**. 성능/CLS/보안헤더는 재설명하지 않고 위 문서로 넘긴다.

---

## 0. 자동 적용 규칙 (Auto-apply)

작성자/에이전트가 기계적으로 따를 항목. (⚠ = 최신 상태 재확인 필요)

1. **Next.js(Passome/Korvia/ACT)**: robots는 `app/robots.ts`, sitemap은 `app/sitemap.ts`로 **코드 생성**한다. `public/`에 정적 `robots.txt`/`sitemap.xml`을 두지 않는다(라우트와 충돌).
2. root `app/layout.tsx`에 `metadataBase: new URL('https://www.도메인')`를 **항상** 설정. 미설정 시 상대경로 canonical/og/hreflang이 **빌드 에러**.
3. 모든 페이지 metadata에 `alternates.canonical` 명시. 다국어는 `alternates.languages`에 `ko`/`en` + **`x-default` 반드시** 포함.
4. 검색엔진 소유확인 메타는 코드로: 구글=`verification.google`, 네이버=`verification.other['naver-site-verification']`, 빙=`verification.other['msvalidate.01']`. **토큰은 env 주입, 평문 커밋 금지.**
5. JSON-LD는 `<script type="application/ld+json">` + `dangerouslySetInnerHTML`, `JSON.stringify(x).replace(/</g,'\\u003c')`로 XSS 이스케이프.
6. sitemap 항목마다 `lastModified`를 **실제 콘텐츠 갱신 시각**으로 채운다. 구글은 `changefreq`/`priority` 무시(lastmod만 사용) — Naver/Bing엔 유효하니 유지는 무해.
7. ⚠ **FAQPage/HowTo 스키마는 더 이상 Google 리치리절트를 만들지 않는다**(FAQ 2026-05-07 종료). 마크업은 **AEO/LLM 인입 목적으로만** 사용. "리치스니펫 확보" 문구 금지.
8. robots AI봇: 검색·인용봇(`OAI-SearchBot`,`PerplexityBot`,`Claude-SearchBot`)은 **allow**. `ChatGPT-User`/`Claude-User`는 **사용자 유발 fetch**(검색 인덱싱봇 아님) — allow 유지하되 라벨 구분. 학습봇(`GPTBot`,`ClaudeBot`,`CCBot`,`Google-Extended`,`Applebot-Extended`)은 **하우스 기본 = 허용(브랜드 각인)**, `Bytespider`만 차단(opt-out은 사업 판단 시에만). ⚠ **`Claude-Web`/`anthropic-ai`는 폐기 식별자** — 사용 금지.
9. **WordPress(Kimchi/jobinkorea)**: Rank Math와 Yoast를 **동시 활성 금지**. XML 사이트맵은 플러그인 자동(`/sitemap_index.xml`) 사용, 그 URL을 4개 콘솔에 제출.
10. WordPress **네이버 소유확인**은 플러그인 전용 필드가 없으므로 (a)`naverXXXX.html`을 WP 루트 업로드 또는 (b)헤더삽입 플러그인 메타. 테마 `header.php` 직접 편집 금지.
11. 배포 후: robots/sitemap 200 확인 → GSC 제출(⚠ **구글 ping 엔드포인트 폐지** — 호출 금지) → Bing은 GSC import → 네이버 서치어드바이저 → Daum register.
12. GSC는 **도메인 속성 + Cloudflare DNS TXT**로 확인(가장 안정, 코드 변경에 안 날아감).
13. IndexNow(Bing/Yandex/**Naver**/Seznam/Yep) 활성화 — 구글은 미참여이니 IndexNow에 의존하지 말 것.

---

## 1. 스택 판별 & 한 줄 규칙

| 사이트 | robots | sitemap | 소유확인 파일 서빙 | 메타태그 주입 |
|---|---|---|---|---|
| Passome / Korvia / ACT | `app/robots.ts` | `app/sitemap.ts` | `public/` (루트로 서빙) | root `app/layout.tsx` metadata |
| Kimchi / jobinkorea | Rank Math/Yoast robots UI | `/sitemap_index.xml` (플러그인 자동) | WP 루트(FTP/파일매니저) | SEO 플러그인 Webmaster 필드 or 헤더삽입 플러그인 |

**핵심 원칙**: 4개 사이트 모두 **(1) Google=GSC, (2) Bing=GSC import + IndexNow, (3) Naver=서치어드바이저, (4) Daum=검색등록** — 4곳 등록. 한국인(Naver 지배) + 외국인 영어(Google 지배)를 동시에 커버해야 하므로 **네이버 등록은 선택이 아니라 필수**.

> ⚠ **엔진별 갈리는 3곳만 예외처리**하면 하나의 구현이 전 엔진 커버: ① Naver는 사이트당 XML 사이트맵 **1개만** 읽음 → 항상 단일 최상위 index 유지 ② robots 크롤러 이름(Yeti/Bingbot/AI봇) ③ hreflang·정규화의 엔진별 신뢰도(Google 강, Naver 약·`<html lang>` 위주).

---

## A. Next.js App Router 구현 (Passome / Korvia / ACT / kftra)

> ⚠ **단일 '16.2.x' 검증 기준 폐기** — 레포마다 Next 메이저가 다르다(§A-0 버전 핀표 = SSOT). `app/sitemap.ts`(v13.3.0+ 도입), `alternates.languages` 지역화(v14.2.0+)는 아래 전 레포 공통. `generateSitemaps` id의 Promise화는 **v16.0.0+ 전용** — 15.x 레포(Passome)에 적용 금지. (nextjs.org/docs · 16 stable = 2025-10, docs lastUpdated 2026-03-03)

### A-0. 레포별 Next.js 버전 핀표 (버전 핀표 — SSOT)

⚠ v16 전용 API를 15.x 레포에 그대로 적용하면 빌드 에러가 난다. 배선 전 각 레포 `package.json`으로 라이브 재확인(아래는 2026-07-02 실측).

| 레포 | Next | React | 확인 소스 (package.json) | v16 전용 API |
|---|---|---|---|---|
| Passome (www.passome.com) | **15.5.4** | 19.1.1 | `PASSOME/www.passome.com/package.json` | ❌ 미적용(15.x) |
| ACT (actartcenter.com) | **16.2.3** | 19.2.4 | `Site Migration/actartcenter.com/site/package.json` | ✅ 적용 |
| kftra.org | **16.2.6** | 19.2.4 | `Site Migration/www.kftra.org/package.json` | ✅ 적용 |
| Korvia (www.korvia.com) | ⚠ **미검증** | — | Next 앱 리포 부재(이 워크스페이스에 없음) → 배선 전 확인 | 확인 후 결정 |

- **v16 전용 파괴적 변경**: `generateSitemaps`의 `id`가 v16.0.x에서 `Promise<string>`로 변경(GH vercel/next.js#85632) → 15.x인 Passome에 v16 id-Promise 패턴을 넣으면 빌드 에러. `params`/`searchParams` Promise화는 v15+.
- 다른 문서(technical-seo 등)가 '버전 핀표'를 참조하면 **이 표가 SSOT**다.

### A-1. `app/robots.ts` — robots.txt 생성

```ts
// app/robots.ts
import type { MetadataRoute } from 'next'

const BASE = 'https://www.korvia.com'

export default function robots(): MetadataRoute.Robots {
  return {
    rules: [
      // 일반 검색엔진(Googlebot/Bingbot/Yeti/Yandex 등) — 전체 허용
      // ⚠ /_next/ 통째 disallow 금지 — /_next/static의 JS/CSS 청크가 막히면 Googlebot 렌더 실패(레이아웃 깨진 채 인덱싱).
      //    크롤 예산이 정말 걱정되면 /_next/static은 절대 막지 말고 /_next/image(이미지 옵티마이저)만 선택 disallow.
      { userAgent: '*', allow: '/', disallow: ['/admin/', '/api/', '/draft/'] },

      // AI 검색·인용 봇 — 허용(AEO 인용 자격 유지) ★ Claude-User/Claude-SearchBot (Claude-Web 아님)
      { userAgent: ['OAI-SearchBot', 'PerplexityBot', 'Claude-SearchBot'], allow: '/' },
      // ChatGPT-User / Claude-User = '사용자 유발 fetch'(검색 인덱싱봇 아님) — allow는 유지하되 라벨 구분
      { userAgent: ['ChatGPT-User', 'Claude-User'], allow: '/' },

      // AI '학습' 봇 — ★ 하우스 기본 = 허용(브랜드 각인). Bytespider만 차단.
      { userAgent: ['GPTBot', 'ClaudeBot', 'CCBot', 'Google-Extended', 'Applebot-Extended'], allow: '/' },
      { userAgent: 'Bytespider', disallow: '/' },   // 유일한 학습봇 차단(하우스 기본)

      // ── (선택) 학습봇을 전면 opt-out 해야 할 때만: 위 '학습봇 허용' 2줄을 아래로 교체 ──
      // ⚠ 하우스 기본과 정반대. 사업 판단으로 학습 opt-out이 명시적으로 필요할 때만 사용.
      // { userAgent: ['GPTBot', 'ClaudeBot', 'CCBot', 'Google-Extended', 'Applebot-Extended'], disallow: '/' },
      // { userAgent: 'Bytespider', disallow: '/' },
    ],
    sitemap: `${BASE}/sitemap.xml`,
    host: BASE,
  }
}
```

- `Robots` 타입 필드: `rules`(객체|배열; `userAgent|allow|disallow|crawlDelay`), `sitemap`(string|string[]), `host`. ⚠ Google은 `crawlDelay` 무시(Bing/Yandex는 인식), robots.txt 파일 한도 **500 KiB**(초과분 무시), UTF-8.
- ⚠ **`Google-Extended`/`Applebot-Extended` 차단은 Gemini/Apple AI 학습 opt-out일 뿐 구글/빙 검색 랭킹에 영향 없음**(공식). YMYL 사이트(Korvia·Kimchi)는 인용 트래픽이 중요하니 검색·인용봇은 반드시 allow.
- ⚠ **하우스 기본 = 학습/AI봇 허용(브랜드 각인) + Bytespider만 차단.** 위 코드 샘플은 이 하우스 기본을 그대로 반영한다(학습봇 allow, `Bytespider` disallow). WEB_PLAYBOOK §12.1의 "AI 인용=리드"(공격적 allow, Bytespider만 차단)와 **일치**한다. 코드 하단의 `disallow: '/'` 블록은 **주석 처리된 opt-out 변형**일 뿐 — 사업 판단으로 학습 opt-out이 명시적으로 필요할 때만 활성화하고, **기본값으로 학습봇을 차단하지 말 것**(하우스 정책 위반). **UA 문자열은 반드시 현행값(Claude-User/Claude-SearchBot)으로 갱신**하고, WEB_PLAYBOOK §1.1에 남은 `Claude-Web`·`anthropic-ai`는 폐기 식별자다. `ChatGPT-User`는 학습봇이 아니라 **사용자 유발 fetch**(2025-12 robots 미준수 전환)이므로 검색 인덱싱봇과 라벨을 구분해 둔다.
- **비준수 봇**(Bytespider, 일부 Perplexity 스텔스 등)은 robots.txt를 무시 → 진짜 차단은 **Cloudflare WAF**(korvia-ops-skill 연계).
- `Disallow`는 **크롤 차단이지 색인 차단 아님**. 색인 제외는 `meta robots noindex`/`X-Robots-Tag` — 단 이때 크롤은 허용해야 noindex를 읽는다(disallow+noindex 동시 = 안티패턴).

### A-2. `app/sitemap.ts` — sitemap.xml + hreflang

```ts
// app/sitemap.ts
import type { MetadataRoute } from 'next'

const BASE = 'https://www.korvia.com'

export default function sitemap(): MetadataRoute.Sitemap {
  return [
    {
      url: `${BASE}`,
      lastModified: new Date(),          // ★ 실제 콘텐츠 갱신 시각으로
      changeFrequency: 'weekly',
      priority: 1,
      alternates: {
        languages: {
          en: `${BASE}/en`,
          ko: `${BASE}/ko`,
          'x-default': `${BASE}`,        // ★ x-default 필수 (§A-4)
        },
      },
    },
    {
      url: `${BASE}/epik`,
      lastModified: new Date(),
      changeFrequency: 'monthly',
      priority: 0.8,
      alternates: { languages: { en: `${BASE}/en/epik`, ko: `${BASE}/ko/epik`, 'x-default': `${BASE}/epik` } },
    },
  ]
}
```

`alternates.languages`는 `<xhtml:link rel="alternate" hreflang="...">`로 출력된다.

- **한도**: 파일당 **50,000 URL / 50MB(비압축)**, gzip 허용(전송용, 한도는 비압축 기준). 초과 시 `generateSitemaps()`로 분할(`id*50000` 슬라이스 → `/sitemap/1.xml` 서빙) 또는 route segment별 중첩(`app/blog/sitemap.ts`). **KORVIA 사이트는 페이지 수가 적어 단일로 충분.**
- ⚠ **Naver는 사이트당 사이트맵 1개만** 읽으므로, 분할이 필요하면 **반드시 sitemap index 1개**로 통합해 그 하나만 서치어드바이저에 제출.
- 구글은 `changeFrequency`/`priority`를 **무시**하고 `lastmod`만(정확·일관할 때) 크롤 스케줄 신호로 사용. `priority`는 Naver/Bing엔 유효하니 WEB_PLAYBOOK §1.2의 카테고리별 priority 스킴은 유지해도 무해.
- `sitemap.js`는 기본 캐시되는 Route Handler. 동적 데이터(CMS fetch) 연결 시 배포마다 `lastModified` 갱신되도록.

### A-3. `metadata` / `generateMetadata` + 소유확인 주입

root `app/layout.tsx`:

```tsx
// app/layout.tsx
import type { Metadata } from 'next'

export const metadata: Metadata = {
  metadataBase: new URL('https://www.korvia.com'),   // ★ 필수 (미설정=빌드 에러)
  title: { default: 'Korvia — Teach & Work in Korea', template: '%s | Korvia' },
  description: 'EPIK, native English teaching, and foreigner recruiting consulting in Korea.',
  alternates: {
    canonical: '/',
    languages: { en: '/en', ko: '/ko', 'x-default': '/' },
  },
  openGraph: { type: 'website', siteName: 'Korvia', locale: 'en_US', images: '/og-image.png' },
  robots: {
    index: true, follow: true,
    googleBot: { index: true, follow: true, 'max-image-preview': 'large', 'max-snippet': -1, 'max-video-preview': -1 },
  },
  // ★ 소유확인 메타 — 값은 env로 주입, 평문 커밋 금지
  verification: {
    google: process.env.NEXT_PUBLIC_GSC_TOKEN,       // <meta name="google-site-verification">
    other: {
      'naver-site-verification': process.env.NEXT_PUBLIC_NAVER_TOKEN!, // <meta name="naver-site-verification">
      'msvalidate.01': process.env.NEXT_PUBLIC_BING_TOKEN!,            // Bing (GSC import 쓰면 불필요)
    },
  },
}
```

동적 라우트:

```tsx
// app/blog/[slug]/page.tsx
export async function generateMetadata({ params }): Promise<Metadata> {
  const { slug } = await params           // ⚠ v15+ params/searchParams는 Promise → await
  const post = await getPost(slug)
  return {
    title: post.title,
    description: post.excerpt,             // ≤155자 (WEB_PLAYBOOK §3.1)
    alternates: { canonical: `/blog/${slug}` },
    openGraph: { images: [post.ogImage] },
  }
}
```

- `metadata`/`generateMetadata`는 **Server Component 전용**, 같은 세그먼트에서 둘 다 export 불가.
- root→page **얕은 병합(shallow merge)**: 중첩 필드(openGraph/robots)는 마지막 세그먼트가 통째로 덮어씀 → 페이지 metadata에서 openGraph를 다시 쓰면 root 값 상속 안 됨(필요 필드 재기입).
- `verification.other`로 넣은 커스텀 메타는 `<head>`에 그대로 출력. **네이버는 이 방식이 표준**(전용 필드 `verification.google`엔 네이버 없음).

### A-4. hreflang / next-intl (한/영 이중언어 필수)

- 모든 로케일 페이지에 상호 hreflang + **`x-default` 필수**(부재 시 구글이 '모호'로 처리). 코드: 언어=ISO 639-1(`en`,`ko`), 지역(선택)=ISO 3166-1 alpha-2(`US`,`KR`,`GB`). **`UK`/`EU`/`UN`·지역단독 지정 금지.**
- **양방향 return-link 필수**(X→Y면 Y→X도), **자기참조 필수**(자기 언어판 포함 전 언어판 나열), **절대 URL**.
- 각 언어판은 **자기 언어판을 canonical**로 지정. **다른 언어판을 canonical로 지정 금지**(흔한 치명 오류).
- **next-intl 사용 시**: `generateMetadata`에서 명시적 `locale`을 넘겨 **정적 렌더 유지**. next-intl은 `getTranslations`/미들웨어로 `/ko`,`/en` 서브디렉토리를 만들고, hreflang URL은 실제 URL 구조를 그대로 반영해야 함.
- Bing은 `<html lang="ko">`/`content-language`도 참고 → **`<html lang>`을 페이지 언어와 정확히** 일치. (`layout.tsx`의 `<html lang={locale}>`)

### A-5. JSON-LD 삽입 (2026 실효성 반영)

Next.js 공식 가이드(`/docs/app/guides/json-ld`)는 `<script type="application/ld+json">`을 layout/page에 직접 렌더 권장. **XSS 방지: `<` → `<`.**

```tsx
// app/epik/page.tsx
export default function Page() {
  // ⚠ FAQPage = 리치리절트 미생성(2026-05-07 종료), AEO/LLM 인입 목적 전용(§A-6). "리치스니펫" 기대 금지.
  //    리치가 필요하면 BreadcrumbList·Article 등 살아있는 타입으로 교체.
  const jsonLd = {
    '@context': 'https://schema.org',
    '@type': 'FAQPage',
    mainEntity: [{ '@type': 'Question', name: 'Do I need a degree for EPIK?',
      acceptedAnswer: { '@type': 'Answer', text: '...' } }],
  }
  return (
    <>
      <script
        type="application/ld+json"
        dangerouslySetInnerHTML={{ __html: JSON.stringify(jsonLd).replace(/</g, '\\u003c') }}
      />
      {/* page content */}
    </>
  )
}
```

> Korvia 리포에는 이미 `buildBreadcrumbJsonLd`/`buildFaqJsonLd`/`buildServiceJsonLd` 헬퍼가 있다(WEB_PLAYBOOK §2). **신규 코드는 그 빌더를 재사용**하고, 신규 사이트(Passome/ACT)에서만 위 인라인 패턴을 쓴다.

**스키마 타입 배치**: root layout에 `Organization`+`WebSite`, 모든 하위에 `BreadcrumbList`, 페이지 타입별 추가.

### A-6. 구조화 데이터 실효성 재분류 (2026-07 확정) ⚠

FAQ 리치리절트 종료(2026-05-07)로 스키마의 목적이 갈렸다. **"리치리절트가 실제 렌더되는 타입"과 "마크업만 유효(리치 없음, AEO/LLM용)"를 구분해서 설계**한다.

| 목적 | 타입 | 2026 상태 | 사이트 적용 |
|---|---|---|---|
| ✅ 리치리절트 렌더 | `BreadcrumbList` | 유효 | 전 사이트 하위 |
| ✅ 리치리절트 렌더 | `Product` + `Offer` | 유효(가격/재고) | Kimchi eSIM 요금제 |
| ✅ 리치리절트 렌더 | `Article`/`BlogPosting` | 유효 | 블로그·매거진 |
| ✅ 리치리절트 렌더 | `LocalBusiness`, `Event` | 유효(주소/영업시간) | **ACT**(미술센터), Korvia 행사 |
| ✅ 리치리절트 렌더 | `Review`/`AggregateRating` | 유효(정책 준수 시) | 후기 |
| ⚠ 마크업만(리치 X) | `FAQPage` | **2026-05-07 리치 종료**. schema.org 유효, Google이 파싱은 함 | 비자/EPIK·eSIM FAQ → **AEO/LLM 인입 목적으로만** |
| ⚠ 마크업만(리치 X) | `HowTo` | 2023 데스크톱 종료 → 이후 완전 종료 | 프로세스 페이지 → AEO 목적만 |

- ⚠ **FAQPage/HowTo에 "리치스니펫 확보"를 기대하지 말 것.** Search Console FAQ 리포트·Rich Results Test는 2026-06, API 지원은 2026-08 순차 제거. 마크업 자체는 무해하고 **LLM/AI 답변엔진 인입엔 여전히 유효**하니 유지는 OK.
- Google 2025-06엔 7개 타입(Book/Course Info/Claim Review/Estimated Salary/Learning Video/Special Announcement/Vehicle Listing)도 검색 노출 종료 → 리치 목적이면 위 ✅ 타입에 집중.

### A-7. OG 이미지 / next/image / Vercel 소유확인 파일

- **OG 이미지**: 파일 기반 `app/opengraph-image.tsx`(`ImageResponse`) 권장(config export 불필요). 또는 `openGraph.images`에 **절대 URL**. 규격 **1200×630(1.91:1), ≤5MB**, `og:url`=canonical과 동일, 다국어는 `og:locale`(`ko_KR`/`en_US`). ⚠ WEB_PLAYBOOK/리서치 표기 중 8MB는 오기 — **≤5MB**로 통일. 이 OG는 **카카오톡·네이버 공유 미리보기도 커버**한다.
- **next/image·CLS·성능**: 재설명 생략 → WEB_PLAYBOOK §4·§6, NEW_PAGE_CHECKLIST §C 참조.
- **Vercel 소유확인 파일 서빙**: 정적 확인 파일(`naverXXXX.html`, `BingSiteAuth.xml`, `googleXXXX.html`)을 프로젝트 `public/`에 두면 Vercel이 루트(`https://site/naverXXXX.html`)로 그대로 서빙 — `robots.ts`/`sitemap.ts`와 충돌 없음. **단, 메타태그 방식(`metadata.verification`)이 코드로 버전관리되어 더 안정적**이니 되도록 메타태그로.

---

## B. WordPress 구현 (Kimchi Mobile / jobinkorea)

### B-1. SEO 플러그인 — Rank Math vs Yoast (2026)

| 항목 | Rank Math (무료) | Yoast (무료) |
|---|---|---|
| XML 사이트맵 자동 | ✅ `/sitemap_index.xml` | ✅ `/sitemap_index.xml` |
| 스키마 타입(무료) | 20+ 종 | 기본 소수 |
| GSC 연동 | 무료 | 프리미엄 게이트 |
| 리디렉션·다중 포커스 키워드 | 무료 | 프리미엄 게이트 |
| 초보 친화 | 보통 | 우수 |

- **권장**: 기능 대비 **Rank Math 무료판 우세**(스키마/GSC/리디렉션 무료). ⚠ **두 플러그인 동시 활성 금지**(메타/사이트맵/스키마 중복 충돌). Kimchi(YMYL 통신/eSIM)는 `Product`+`Offer`, `FAQPage`(리치 X·AEO용)를 Rank Math 스키마 빌더로 구성.
- XML 사이트맵은 두 플러그인 모두 자동. 인덱스 URL `/sitemap_index.xml`을 GSC/Bing/네이버/다음에 제출.

### B-2. WordPress 소유확인

- **Yoast**: `SEO → General → Webmaster Tools`에 **Google/Bing/Baidu/Yandex 4종 필드**(HTML tag의 content 값만 붙여넣기). ⚠ **네이버 전용 필드 없음.**
- **Rank Math**: `General Settings → Webmaster Tools`에 유사 필드(Google/Bing/Baidu/Yandex/Pinterest/Norton). ⚠ **역시 네이버 필드 없음.** (플러그인 최신판에서 필드 추가 여부는 설정에서 직접 확인 권장.)
- **네이버 소유확인 2택**:
  1. **HTML 파일 업로드**: `naverXXXX.html`을 WP **루트(public_html)**에 FTP/파일매니저 업로드 → 서치어드바이저에서 '소유확인'.
  2. **메타태그 주입**: "Insert Headers and Footers"/WPCode로 `<meta name="naver-site-verification" content="...">`를 `<head>`에 삽입.
  - ⚠ 테마 `header.php` 직접 편집은 **테마 업데이트 시 소실** → 비권장.
- 파일 vs 메타태그는 **동작 동일**.

### B-3. WordPress robots.txt

- 기본 가상 robots.txt는 SEO 플러그인이 관리(Rank Math/Yoast에 편집 UI). `Sitemap: https://www.kimchimobile.com/sitemap_index.xml` 라인 포함 확인. AI봇 규칙(§A-1)도 여기에 수동 추가 가능.

### B-4. WP Engine (Kimchi Mobile 호스팅) 특이사항 ⚠

Kimchi Mobile은 **WP Engine** 호스팅이다(클래식 FTP/cPanel 아님) — 일반 WP 절차와 다르다:
- **루트 파일 업로드**(`naverXXXX.html`·`BingSiteAuth.xml`·`googleXXXX.html`)은 **SFTP 또는 User Portal(File Manager)** 경유. 표준 FTP로 시도하면 실패할 수 있다.
- WP Engine **자체 CDN·페이지 캐시**로 소유확인 파일/robots 변경이 즉시 반영 안 될 수 있음 → 업로드 후 **캐시 퍼지** 필수(콘솔에서 200 확인).
- WP Engine는 **특정 캐싱 플러그인 금지**(자체 캐시와 충돌) → Rank Math/Yoast 외 캐시 플러그인·robots 처리 특이사항 도입 시 충돌 여부 확인.

---

## C. 스택별 소유확인 매핑 (파일 업로드 vs 메타태그)

| 검색엔진 | 확인 방법 | Next.js(Vercel) | WordPress |
|---|---|---|---|
| **Google GSC** | ①DNS TXT(도메인 속성=이것만) ②HTML 파일 ③메타태그 ④GA/GTM (URL prefix) | 메타=`verification.google`; 파일=`public/googleXXXX.html`; **권장=도메인 속성 → CF/Vercel DNS TXT** | 플러그인 Google 필드; 파일=루트; DNS TXT |
| **Bing** | ①XML(BingSiteAuth.xml) ②메타(`msvalidate.01`) ③CNAME/DNS ④Domain Connect ⑤**GSC import** | **GSC import가 최선(무검증)**; 아니면 `public/BingSiteAuth.xml` 또는 `verification.other['msvalidate.01']` | 플러그인 Bing 필드 or GSC import |
| **네이버 서치어드바이저** | HTML 파일 or 메타태그(동작 동일) | `verification.other['naver-site-verification']`(권장) or `public/naverXXXX.html` | 헤더삽입 메타 or 루트 파일(전용 필드 없음) |
| **Daum 검색등록** | 소유확인 없이 URL 등록 심사 | 해당없음(등록만) | 해당없음(등록만) |

- **GSC 속성 유형**: 도메인 속성(전 프로토콜/서브도메인 포괄) = **DNS TXT로만** 확인 / URL prefix = 파일·메타·DNS·GA·GTM. **KORVIA는 Cloudflare DNS 사용 → 도메인 속성 + DNS TXT 권장**(코드·플러그인 변경에 안 날아감).
- ⚠ **Bing 메타 name = `msvalidate.01`, 네이버 = `naver-site-verification`** — 안정적이나 실제 값·형식은 각 콘솔에서 복사·검증(변동 가능).

---

## D. 배포 후 멀티엔진 제출 체크리스트

배포 직후 순서대로:

1. **접근 확인** — `https://site/robots.txt`, `https://site/sitemap.xml`(Next) 또는 `/sitemap_index.xml`(WP) **200** + `Sitemap:` 라인 존재.
2. **Google Search Console**
   - 속성 추가(**도메인 속성 권장**) → DNS TXT 확인.
   - `Sitemaps` → 사이트맵 URL 제출. ⚠ **구글 ping 엔드포인트는 폐지**(2023-06 발표·약 6개월 뒤 종료; `https://google.com/ping?sitemap=`는 404) — **호출 금지**. robots.txt `Sitemap:` + GSC 제출만 유효.
   - `URL 검사` → 신규 URL '색인 요청'(소량 수동).
3. **Bing Webmaster Tools** — `My Sites → Import`로 GSC에서 사이트+사이트맵 **무검증 가져오기**(가장 빠름). 없으면 개별 확인 후 사이트맵 제출.
4. **네이버 서치어드바이저**(searchadvisor.naver.com)
   - `웹마스터 도구 → 사이트 등록` → 소유확인(파일/메타).
   - `요청 → 사이트맵 제출`: 단일 최상위 사이트맵/인덱스 URL. ⚠ 사이트당 **1개만**.
   - `요청 → 웹페이지 수집`: 핵심 URL 수동. ⚠ **1일 약 50개 한도**, 최초 수집 약 **14~16일**(네이버계 가이드 추정, 공식 수치 아님).
   - `검증 → robots.txt`로 **Yeti**(네이버 봇, `Mozilla/5.0 (compatible; Yeti/1.1; +http://naver.me/spd)`) 허용 확인.
5. **Daum 검색등록**(register.search.daum.net) — 카카오계정 로그인 → URL·제목·설명·디렉토리 입력 → 심사 약 5일(2차 가이드 기반). 소유확인 절차 없음.
   - ⚠ **Daum은 현재 Bing 백엔드가 아님**(2009 Bing-Daum 제휴 종료=STALE). Daum=카카오 자체검색 → 2026 Upstage 인수로 'AI 포털' 전환 중. 점유율 낮아 **Naver 다음 소수 채널**로만 취급.
6. **IndexNow**(§E) — Bing·Yandex·**Naver**·Seznam·Yep 즉시 통보(⚠ **구글 미지원**).
7. **검증** — 각 콘솔 색인/커버리지 리포트. 리치리절트는 Google Rich Results Test / Schema Markup Validator로 확인(⚠ FAQ/HowTo는 이제 리치 미생성).

---

## E. IndexNow 구현 (Next.js 배포 훅 권장)

- **참여 엔진**: Bing·Yandex·**Naver**(2023-07 지원)·Seznam·Yep. 한 엔진에 push하면 참여 엔진 간 자동 공유. ⚠ **구글 미참여**.
- **키**: ⚠ **8~128자, 문자셋 = a-z / A-Z / 0-9 / 대시(-)** (indexnow.org 문서가 'hexadecimal'로 오기하나 바로 아래에서 a-z/A-Z/대시 허용 명시 — '16진'으로 못박지 말 것). `https://site/{key}.txt`에 키 호스팅(Next=`public/{key}.txt`).
- **엔드포인트**:
  - POST `https://api.indexnow.org/indexnow` `Content-Type: application/json; charset=utf-8`, body `{ host, key, keyLocation, urlList:[...] }` (요청당 **최대 10,000 URL**).
  - GET 단건: `https://www.bing.com/indexnow?url=<url>&key=<key>`.
  - ⚠ **네이버 자체 엔드포인트 보유**: `https://searchadvisor.naver.com/indexnow` — 발행 파이프라인에서 Bing 경유뿐 아니라 **네이버 직접 ping**도 가능.
- **응답**: 200 성공 / 202 접수 / 400 / 403(키 오류) / 422 / 429(과다).

```ts
// scripts/indexnow.ts — Vercel Deploy Hook / CMS 발행 웹훅에서 호출
const KEY = process.env.INDEXNOW_KEY!         // ★ env 주입, 평문 커밋 금지
const HOST = 'www.korvia.com'

export async function pingIndexNow(urls: string[]) {
  const body = { host: HOST, key: KEY, keyLocation: `https://${HOST}/${KEY}.txt`, urlList: urls.slice(0, 10000) }
  await Promise.allSettled([
    fetch('https://api.indexnow.org/indexnow', { method: 'POST', headers: { 'Content-Type': 'application/json; charset=utf-8' }, body: JSON.stringify(body) }),
    fetch('https://searchadvisor.naver.com/indexnow', { method: 'POST', headers: { 'Content-Type': 'application/json; charset=utf-8' }, body: JSON.stringify(body) }),
  ])
}
```

> 변경 URL 자동 전송은 **n8n-project-harness**로 오케스트레이션 가능(CMS 발행 이벤트 → IndexNow POST). Google은 sitemap 갱신 + GSC 색인요청으로 별도 처리.

---

## F. 프로젝트별 청중 / 키워드 / YMYL 노트

| 사이트 | robots | sitemap | 소유확인 | 우선 스키마(✅리치) | YMYL·청중 특이사항 |
|---|---|---|---|---|---|
| **Korvia** | `app/robots.ts` | `app/sitemap.ts` (ko/en+x-default) | GSC DNS TXT, 네이버 `verification.other` | `Organization`,`Service`,`BreadcrumbList` (+ FAQPage=AEO용) | **YMYL 비자/취업** — 인용봇 allow, 절차·자격은 1차 소스(HiKorea/EPIK) 표기, 과장 금지. 외국인 영어 쿼리 비중 높음 → 영문 GEO 고효율 |
| **Passome** | `app/robots.ts` | `app/sitemap.ts` | 동일 | `Organization`,`Service`(AX컨설팅),`Article` | B2B 위주, 네이버 비중 높음. 미술심리는 '치료' 표방 시 규제 리스크 → 웰니스 포지셔닝(별도 콘텐츠 정책) |
| **ACT Art Center** | `app/robots.ts` | `app/sitemap.ts` | 동일 | `LocalBusiness`(주소/영업시간),`Course`,`Event` | **로컬 SEO** — 네이버 플레이스 병행, NAP 일관성. `LocalBusiness`는 리치 유효 |
| **kftra.org** | `app/robots.ts` | `app/sitemap.ts` | 동일 | `Organization`,`Article` | ⚠ Site Migration 이관 중 (Next 16.2.6 · §A-0) — 배선 전 레포/호스팅(Vercel 여부) 확인 |
| **Kimchi Mobile** | RankMath robots + `Sitemap:` | `/sitemap_index.xml`(RankMath) | 헤더삽입 플러그인(네이버), 플러그인 필드(구글/빙) | `Product`+`Offer`(eSIM 요금제),`BreadcrumbList` (+ FAQPage=AEO용) | **YMYL 통신/금융** — 요금·개통 조건 정확·최신, 외국인 영어 트래픽 지배 → 영어 우선 |

- 공통: 한/영 이중언어 → hreflang `ko`/`en`/`x-default` + `<html lang>` 정확. 카카오톡/네이버 공유 = OG 재사용이므로 OG 정확성으로 국내외 SNS 동시 커버.
- 콘텐츠/GEO 전략(질문형 H2 + 즉답 + 통계 + 출처 인용 레버)은 [`./AEO_GEO_PLAYBOOK_2026.md`](./AEO_GEO_PLAYBOOK_2026.md) §2~4 참조. 본 문서는 기계적 구현만 담당.

---

## G. ⚠ 재확인·주의 플래그 (사용 전 라이브 확인)

- ⚠ **FAQPage/HowTo = 리치리절트 미생성**(FAQ 2026-05-07 종료, SC 리포트 2026-06, API 2026-08). 마크업은 AEO/LLM용으로만 유지. "리치스니펫" 문구 금지.
- ⚠ **Anthropic `Claude-Web`/`anthropic-ai`는 폐기 식별자** → robots 허용목록은 `Claude-User` + `Claude-SearchBot`. 모든 AI봇 UA는 배포 직전 벤더 공식 문서(support.claude.com, platform.openai.com/bots, perplexity 크롤러 페이지)에서 현행 문자열 재확인(변동 잦음).
- ⚠ **llms.txt는 GEO 목적으로 추가 권장하지 말 것** — Google(Mueller/Illyes)·OpenAI·Anthropic 모두 콘텐츠 검색에 미사용, 실측 대부분 미읽힘. **robots.txt의 AI 검색봇 allow가 실효적 GEO 수단**. (내부 문서 AEO_GEO_PLAYBOOK §4·WEB_PLAYBOOK §1.3은 llms.txt를 권장하나, 2026-07 기준 소비되지 않으므로 '발행은 무해하나 효과 기대 금지'로 해석.)
- ⚠ **네이버 사이트맵 50,000 URL/50MB는 sitemaps.org 국제표준 추정값** — 네이버 공식 도움말 원문 미확인. '사이트당 XML 1개'만 확정 사실. 구체 수치 단정 금지, 콘솔에서 확인.
- ⚠ **네이버 웹페이지 수집 1일 ~50개 / 최초 14~16일**은 네이버계 가이드 추정(공식 아님). 계정·사이트 등급에 따라 변동.
- ⚠ **구글 ping 폐지 = '2024'가 아니라 2023-06 발표·약 6개월 뒤 종료**. `google.com/ping?sitemap=` 호출 금지(404).
- ⚠ **Daum ≠ Bing 백엔드**(2009 제휴 종료·STALE). 카카오 자체검색 → 2026 Upstage 인수 진행.
- ⚠ **IndexNow 키 = a-z/A-Z/0-9/대시 8~128자**(문서 자체 'hexadecimal' 문구 모순 주의). 네이버 자체 엔드포인트 별도.
- ⚠ **CWV 임계 = LCP≤2.5s / INP≤200ms / CLS≤0.1**(필드 75퍼센타일, 모바일/데스크톱 분리). 일부 블로그의 'LCP 2.0s 강화'·'Engagement Reliability 신규지표'는 **비공식·미확인 → 채택 금지**(web.dev 2026-07 재확인). 성능 상세는 WEB_PLAYBOOK §4·§10.
- ⚠ **소유확인 토큰·IndexNow 키는 각 콘솔에서 실제 값 복사·검증** 후 **env 주입, 평문 커밋 금지**.
- ⚠ **Next.js 버전 드리프트**: 16.2.x stable line 기준(문서 lastUpdated 2026-03-03). `params`/`searchParams` Promise화(v15+), `generateSitemaps` id Promise화(v16.0.0) 등 API는 사용 전 해당 버전 docs 확인.

---

## Sources

**Next.js (Vercel) 공식 — 2026-03-03 갱신**
- https://nextjs.org/docs/app/api-reference/file-conventions/metadata/sitemap
- https://nextjs.org/docs/app/api-reference/file-conventions/metadata/robots
- https://nextjs.org/docs/app/api-reference/functions/generate-metadata
- https://nextjs.org/docs/app/api-reference/functions/generate-sitemaps
- https://nextjs.org/docs/app/guides/json-ld
- https://github.com/vercel/next.js/releases (16.2.x stable line, 2026-06)
- https://next-intl.dev/docs/environments/actions-metadata-route-handlers

**Google Search Central**
- https://developers.google.com/search/blog/2023/06/sitemaps-lastmod-ping (ping 폐지)
- https://developers.google.com/search/blog/2023/08/howto-faq-changes (HowTo/FAQ 변경)
- https://developers.google.com/search/docs/appearance/structured-data/faqpage (FAQ 2026-05-07 종료 노트)
- https://support.google.com/webmasters/answer/9008080 (소유확인)
- https://developers.google.com/search/docs/crawling-indexing/consolidate-duplicate-urls (canonical)
- https://developers.google.com/search/docs/specialty/international/localized-versions (hreflang)
- https://developers.google.com/search/docs/crawling-indexing/301-redirects
- https://developers.google.com/search/docs/appearance/page-experience
- https://developers.google.com/search/docs/crawling-indexing/robots/robots_txt
- https://developers.google.com/search/docs/crawling-indexing/overview-google-crawlers
- https://web.dev/articles/vitals (CWV 임계, 2026-07 재확인)

**Bing / IndexNow**
- https://www.bing.com/indexnow
- https://www.indexnow.org/documentation
- https://blogs.bing.com/webmaster/July-2025/Keeping-Content-Discoverable-with-Sitemaps-in-AI-Powered-Search
- https://blogs.bing.com/webmaster/september-2019/Import-sites-from-Search-Console-to-Bing-Webmaster-Tools

**Naver / Daum**
- https://searchadvisor.naver.com/guide/sitemap-submit
- https://searchadvisor.naver.com/guide/crawl-robots (Yeti)
- https://searchadvisor.naver.com/indexnow (네이버 IndexNow 엔드포인트)
- https://register.search.daum.net/

**표준 / 스키마 / 소셜**
- https://www.sitemaps.org/protocol.html
- https://schema.org/
- https://ogp.me/

**WordPress SEO 플러그인**
- https://rankmath.com/kb/verify-your-site-with-bing-webmaster-tools/
- https://academy.yoast.com/ (General → Webmaster Tools)

**Anthropic 크롤러 (2026-07 재확인)**
- https://support.claude.com/en/articles/8896518-does-anthropic-crawl-data-from-the-web-and-how-can-site-owners-block-the-crawler
- https://www.seroundtable.com/anthropic-updates-its-crawler-docs-40978.html
