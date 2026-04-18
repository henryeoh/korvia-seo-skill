---
name: CLS 원인 7대 패턴 (Next.js + Tailwind v4 기준)
description: www.korvia.com에서 실제 발견한 CLS 기여자 7개 패턴. 각 패턴의 impact×distance 계산, 감지 방법, 수정 코드. CrUX 0.89→≤0.1 목표용 체크리스트.
type: feedback
originSessionId: 10654995-dff2-4b5c-a086-7c644c9a3bb1
---
Next.js App Router + Tailwind v4 + Swiper 조합에서 실제 발견한 CLS 원인 7대 패턴. 새 페이지마다 이 7개를 체크.

## 1. 🔴 최대 — Swiper 다중 슬라이드 pre-hydration 스택
**증상**: `<Swiper>` 안에 N개 `<SwiperSlide>` — styled-jsx로 선언한 `height: 100vh`는 client-side라 SSR HTML엔 적용 안 됨 → 7개 슬라이드가 수직으로 쌓여 700vh → JS 하이드레이션 후 100vh로 collapse
**impact×distance**: ~0.85 (단일 소스로 0.89 CLS 달성 가능)
**How apply**: `globals.css` (critical path)에 `.swiper-wrapper {position:relative}`, `.swiper-slide {position:absolute; inset:0}`, `.swiper-slide:not(:first-child) {visibility:hidden}` 선언. Swiper가 init 후 visibility 자동 override.

## 2. 🟠 Raw `<img>` width/height 누락
**증상**: `<img src="..." />` 만 있고 `width height` HTML attribute 없음 → 이미지 로드 전 0×0 → 로드 후 실제 크기로 팽창
**감지**: `grep -rn "<img " src --include="*.tsx" | grep -v "next/image" | grep -v "width=" | grep -v "height="`
**How apply**: `<img width={36} height={36}>` HTML attribute (CSS width/height NO). 또는 `<Image width={N} height={M}>` 고정 크기 next/image

## 3. 🟡 라이크한 Intl API 로케일 누락 → #418 → 부분 re-render → shift
**증상**: `(10000).toLocaleString()` → 서버 en-US "10,000", 독일 사용자 브라우저 de-DE "10.000" → hydration mismatch → React 부분 re-render → 텍스트 폭 변화
**How apply**: 모든 `.toLocaleString()`, `.toLocaleDateString()`에 명시적 locale: `.toLocaleString("en-US")` 또는 `"ko-KR"`

## 4. 🟡 카운터 애니메이션 숫자 폭 성장
**증상**: `useEffect`로 0 → 10000 카운트업. `whitespace-nowrap`만 있고 min-width 없으면 수평 텍스트 확장 → 옆 요소 밀림
**How apply**: `tabular-nums` (monospaced digits) + `style={{ minWidth: \`${finalLabel.length}ch\` }}` + `inline-block`. 최종값 폭을 paint-0부터 예약.

## 5. 🟡 애니메이션 레이아웃 속성 사용
**증상**: `@keyframes`에 `top`, `bottom`, `margin`, `padding`, `width`, `height` → 매 프레임 layout 재계산
**허용 속성**: `transform` (translateY, scale, rotate), `opacity`, `filter: blur()` — GPU composited
**How apply**: globals.css의 `@keyframes` 전수 검토. 레이아웃 영향 속성은 transform으로 rewrite.

## 6. 🟡 폰트 swap 시 reflow
**증상**: `display: swap` + fallback 폰트 metric과 웹폰트 metric 다름 → 텍스트 reflow
**Next.js 해결**: `next/font/google`의 `adjustFontFallback: true` (default) — 자동 폰트 metric 보정. 절대 `false`로 override 금지.

## 7. 🟡 비동기 Client Component 섹션 출현
**증상**: `{data.length > 0 && <section>}` — server render 시 data 없으면 렌더 안 됨, client에서 다시 fetch 후 렌더 → 아래 모든 콘텐츠 밀림
**How apply**: 
- Server Component로 유지 (async fetch at render time, SSR HTML에 굳음)
- 또는 섹션 자리에 `min-height: 400px` 폴백

## 추가 함정
- **3rd-party 스크립트 DOM 주입**: GTM이 Google Ads pixel, Clarity iframe 삽입 → `lazyOnload`로 2~5s 지연시켜 critical path 제거
- **브라우저 확장 DOM 주입**: 우리가 못 고침. 원인 불명 #418 지속 시 `suppressHydrationWarning` 안전망

## 검증 순서
1. DevTools Performance → Record → 새로고침 → Stop → Layout Shifts 확인 (빨간 박스로 쉬프트 영역 시각화)
2. Lighthouse mobile/desktop synthetic (즉시 반영)
3. CrUX (28일 롤링, 배포 후 4주 뒤 재확인)

## 실적 (www.korvia.com 2026-04-17~18)
CrUX 0.89 → (수정 배포 완료, 4주 후 재측정) Lighthouse 합성 CLS 0 달성.
