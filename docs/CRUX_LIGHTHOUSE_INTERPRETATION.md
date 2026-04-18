---
name: CrUX vs Lighthouse 해석 — 언제 무엇을 믿을까
description: 외부 감사 CLS 0.89인데 synthetic Lighthouse는 0일 때의 해석. CrUX 28일 롤링 함정, Lighthouse의 제약, 실제 실사용자 검증 방법.
type: feedback
originSessionId: 10654995-dff2-4b5c-a086-7c644c9a3bb1
---
www.korvia.com 감사 중 겪은 가장 혼란스러운 순간: **CrUX CLS 0.89 vs Lighthouse desktop CLS 0**. 이 불일치는 버그가 아니라 **측정 방식 차이**.

## 각 측정의 본질

### 1. CrUX (Chrome User Experience Report)
- **실사용자** 크롬 브라우저에서 수집 (RUM)
- **28일 롤링 윈도우** — 과거 4주간 누적
- 디바이스/네트워크/브라우저 버전 모두 다양
- **75th percentile** = 실제 75% 사용자가 경험한 값
- **CLS에서 가장 정확** — 실사용자 디바이스/네트워크 조건을 반영
- ⚠️ **수정해도 즉시 반영 안 됨** — 새 데이터가 28일 평균의 4주치 대체하려면 시간 필요

### 2. Lighthouse Synthetic
- 고정 조건 (Moto G Power + Slow 4G 또는 desktop + fast network)
- 단일 페이지 로드 1회 측정
- **즉시 반영** — 배포 5분 뒤 결과 바뀜
- CWV보다 **SEO/a11y/best-practices** 신호에 강점
- CLS는 "이 특정 조건에서는 괜찮다"만 의미 — 실사용 varietal 반영 안 됨

### 3. Chrome DevTools Performance Panel
- 로컬에서 직접 record — 가장 상세
- **Layout Shifts 섹션**에서 어느 엘리먼트가 언제 얼마나 shifted 됐는지 시각화
- **"지금 수정 반영 잘 됐나?"** 확인에 최적
- 단일 세션이라 실사용자 분포 반영 못 함

## 해석 매트릭스

| 상황 | 우선 지표 | 이유 |
|------|---------|------|
| 수정 직후 효과 검증 | Lighthouse + DevTools | 즉시, 반복 가능 |
| 실제 사용자 영향 파악 | CrUX 75p | RUM이라 가장 현실적 |
| SEO 순위 영향 | CrUX | Google 랭킹 알고리즘이 CrUX 사용 |
| 디버깅 | DevTools | 구체적 원인 지목 가능 |
| 배포 전 게이트 | Lighthouse | CI 통합 가능 |

## 함정

### 함정 1: "어제 수정했는데 CrUX는 그대로야"
**오해**: 수정이 안 됐다
**현실**: CrUX 28일 평균에 과거 27일 데이터가 남아 있음 → 4주 후 반영

### 함정 2: "Lighthouse CLS 0인데 CrUX 0.89"
**오해**: Lighthouse 버그
**현실**: Lighthouse는 fast connection + single load. CrUX는 slow connection + DPR 3+ 등 극한 조건 포함. 특정 조건에서만 나타나는 CLS가 있음 (예: Swiper pre-hydration on slow 3G).

### 함정 3: "Lighthouse score는 높은데 실사용자 체감은 나쁘다"
**원인**: Synthetic에는 없는 조건 — 3rd-party 스크립트 응답 지연, 광고 인젝션, 브라우저 확장 등

## 검증 순서 (권장)
1. **수정 후 즉시** — DevTools Performance + Lighthouse로 로컬 검증
2. **배포 후 5분** — Production URL에 Lighthouse 재측정 (bundle 해시 바뀐 것 확인)
3. **1~3일 후** — Google Search Console 색인 재크롤 확인
4. **4주 후** — CrUX 데이터 재확인

## 검증할 수 없는 것은?
- 브라우저 확장 DOM 주입 (지역/사용자마다 다름)
- 극단 조건 (iPhone 6, Chrome 90 이전 등)
- 실제 사용자 스크롤 / 인터랙션 패턴

이런 건 RUM (Datadog, Sentry, Cloudflare Web Analytics)으로만 잡힘.

## 교훈
"CrUX 수치가 나쁘다" = **"당신이 어제 한 수정은 아직 반영 전"**이 절반 이상의 경우.
수정 직후 재확인은 DevTools + Lighthouse로. CrUX는 느긋하게 4주 기다리기.
