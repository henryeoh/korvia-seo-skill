---
name: Swiper 슬라이드 내 next/image 올바른 패턴
description: Swiper `<SwiperSlide>` 안에서 `<Image fill>`은 quirks 있음 → 고정 크기 `<Image width height>`는 문제 없음. raw `<img>` 유지보다 항상 우월.
type: feedback
originSessionId: 10654995-dff2-4b5c-a086-7c644c9a3bb1
---
Swiper 12 + Next.js 15+ 조합에서 이미지 최적화 패턴.

## 기존 오해
"Swiper 슬라이드 안에 `<Image>` 쓰면 레이아웃 깨짐 → raw `<img>` 유지" ← **절반만 맞음**

## 정확한 룰
- **`<Image fill>` (절대 위치 + 부모 relative)** → Swiper 슬라이드 안에서 quirks (translate3d transform에 fill 계산 꼬임)
- **`<Image width={N} height={M}>` (고정 크기)** → Swiper 슬라이드 안에서 **문제 없음**. 오히려 AVIF/WebP 자동 변환 + srcset으로 3~10배 작은 파일 서빙

## 실제 사례 (TestimonialCarousel)
**Before** (raw `<img>`):
```tsx
<img
  src="https://cdn.korvia.com/.../image-icon-testimonial-michaelklish.webp"
  alt="Michael K."
  width="36"
  height="36"
  className="w-9 h-9 rounded-full object-cover"
/>
```
문제: CDN 원본 250×250 WebP (~15KB) → 36×36 표시 = 6.9× 낭비

**After**:
```tsx
<Image
  src="https://cdn.korvia.com/.../image-icon-testimonial-michaelklish.webp"
  alt="Michael K."
  width={36}
  height={36}
  sizes="36px"
  className="w-9 h-9 rounded-full object-cover"
/>
```
결과: `/_next/image` 경로로 AVIF 72px (retina 2x) 서빙 → ~3KB/아바타 → **페이지당 72KB 절감**

## 적용 조건
- `next.config.ts`에 CDN 도메인이 `images.remotePatterns` 등록되어 있어야 함
- `sizes` 속성 반드시 명시 (srcset 최적 선택 유도)
- `unoptimized` 플래그 제거 (AVIF 변환 활성화)

## 예외 — 여전히 raw `<img>` 유지
- 애니메이션 GIF / animated WebP (Next.js 변환 불가)
- `data:` URI 인라인 이미지
- 매우 작은 (< 1KB) placeholder

## 검증
`curl -I` 로 `cache-control: public, max-age=31536000, immutable` 확인 (next.config.ts Cache-Control 규칙 적용됨)

## 교훈
"Swiper에서 next/image 안 됨" 이라는 1세대 Next.js 시절 미신을 그대로 따르지 말 것. **fill 모드만 quirks, 고정 크기는 안전.**
