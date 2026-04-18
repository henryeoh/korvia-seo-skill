---
name: CSP 작성 시 3rd-party 서브도메인 fan-out 주의
description: Microsoft Clarity는 scripts.clarity.ms / c.clarity.ms / y.clarity.ms 로 분화. Google Ads는 googleadservices.com + googleads.g.doubleclick.net + google.com/ccm. 와일드카드 또는 전수 열거 필요.
type: feedback
originSessionId: 10654995-dff2-4b5c-a086-7c644c9a3bb1
---
Content-Security-Policy 작성 시 흔한 실수: "Clarity 쓰니까 clarity.ms 추가" 하면 Clarity 실제 트래픽의 절반만 허용됨.

## Microsoft Clarity 실제 사용 도메인
- `scripts.clarity.ms` — 스크립트 전송
- `c.clarity.ms` — 이벤트 beacon
- `y.clarity.ms` — 세션 녹화 beacon
- `www.clarity.ms` — 메인 도메인 (시작 로드)

**와일드카드**: `https://*.clarity.ms` 로 한 번에 커버

## Google Ads / Conversion Tracking
- `www.googleadservices.com` — 변환 픽셀
- `googleads.g.doubleclick.net` — viewthrough 트래킹
- `www.google.com/ccm/collect` — Consent Mode 비콘
- `td.doubleclick.net` — frame-src (iframe 내 변환)

전부 명시 필요 (와일드카드 부적절 — google.com 전체 허용은 위험)

## 감지 방법
1. Lighthouse mobile/desktop 측정 후 **"Issues panel"** 섹션
2. CSP Report-Only로 선배포 → 위반 리포트 수집
3. Chrome DevTools → Console 에서 "Content Security Policy" 경고 검색

## 예시 (Korvia CSP Report-Only)
```
script-src 'self' 'unsafe-inline' 'unsafe-eval'
  https://www.googletagmanager.com
  https://www.google-analytics.com
  https://www.google.com
  https://www.googleadservices.com
  https://googleads.g.doubleclick.net
  https://bat.bing.com
  https://*.clarity.ms
  https://c.bing.com;
```

## 교훈
"Clarity 추가했으니 CSP 되겠지" 오해 금지. 3rd-party는 **실제 트래픽 허용**까지 확인 (Report-Only → Issues Panel → 0 violation 확인 → enforcing 전환).

Report-Only에서 enforcing으로 전환 전에 필수:
1. 1~2주 모니터링 (위반 0)
2. `report-uri` 또는 `report-to` 엔드포인트 구성 (violation collection)
3. `'unsafe-inline'` + `'unsafe-eval'` 제거는 nonce 구현 후에만
