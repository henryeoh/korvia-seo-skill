---
last_verified: 2026-07
reverify_by: 2026-Q4
---

# DAUM / Kakao 검색 색인·노출 (Daum/Kakao Search Indexing & Exposure)

> Generated 2026-07-01 · Owner: Henry Oh (KORVIA) · ⚠ 콘솔/API 변경은 사용 전 재확인
>
> 대상: KORVIA 4개 사이트(Passome · Korvia · Kimchi Mobile · ACT Art Center).
> 이 문서는 `korvia-seo-skill/docs/`의 상위 SEO/AEO/GEO 문서를 보완하는 **Daum/Kakao 색인 차원 전용** 확장이다.
> 중복 금지 — 아래 문서와 상호보완이며, 겹치는 개념은 링크로 참조한다:
> - AI 답변엔진 전략(GEO/AEO): [`./AEO_GEO_PLAYBOOK_2026.md`](./AEO_GEO_PLAYBOOK_2026.md)
> - 사이트 위생·robots·sitemap·llms.txt·Next.js 함정: [`./WEB_PLAYBOOK.md`](./WEB_PLAYBOOK.md) (§1 SEO, §11 Next.js 함정)
> - 신규 페이지 체크리스트: [`./NEW_PAGE_CHECKLIST.md`](./NEW_PAGE_CHECKLIST.md)

---

## 0. 자동 적용 규칙 (Auto-apply)

작성자/에이전트가 기계적으로 따를 항목. 이유·근거는 아래 본문 참조.

1. **한국어 콘텐츠가 있는 KORVIA 사이트(Korvia · Kimchi · Passome · ACT)는 robots.txt(또는 Next.js `app/robots.ts`)에 `User-agent: Daumoa` + `Allow: /` + 절대 URL `Sitemap:` 라인을 포함한다.** 전역 `Disallow: /`나 광범위 봇 차단을 넣지 않는다. (일반 robots/sitemap 규칙은 [`./WEB_PLAYBOOK.md`](./WEB_PLAYBOOK.md) §1 참조 — 여기서는 Daumoa 허용만 추가)
2. **Cloudflare WAF/Bot Management가 Daumoa를 차단하지 않는지 신규 배포마다 확인한다.** 허용 기준은 **IP 대역이 아니라 UA 토큰(`Daumoa`/`daum`)** 으로 설정한다 — ⚠ 소유권 이전(카카오→AXZ→업스테이지)으로 IP 대역이 바뀔 수 있고 공식 CIDR은 미공표.
3. **새 사이트/도메인 최초 공개 시 `register.search.daum.net` 검색등록**으로 대표 도메인(쿼리스트링 제거한 메인 URL)을 1회 등록하고, **필수 3종(제목 · 설명-품목 · 디렉토리)** 을 채운다.
4. **`webmaster.daum.net`(Daum 웹마스터도구)에서 소유확인 후 `수집요청 → 수집 Seed URL 등록(사이트맵)`으로 사이트맵을 제출한다.**
5. **사이트맵은 파일당 URL 50,000개 · 50MB 이내**, 사이트맵 내 모든 URL 호스트를 제출 호스트와 동일하게(크로스도메인 금지) 유지한다. 초과 시 `sitemapindex` 사용.
6. **블로그/뉴스형 콘텐츠 페이지는 사이트맵 외에 RSS 또는 Atom 피드도 Seed URL로 함께 제출**한다(Kimchi · Passome 콘텐츠성 페이지).
7. **네이트(Nate)용 별도 등록을 하지 않는다** — 네이트는 다음 검색결과를 그대로 노출하므로 다음 등록으로 자동 커버(중복 작업 금지).
8. **다음을 Bing/구글 색인의 자동 파생으로 가정하지 않는다** — 다음은 자체 엔진이므로 위 등록을 반드시 별도로 수행한다.
9. **다음 전용 콘텐츠 최적화에 과투자하지 않는다.** 점유율 낮음(약 3%, 4위) → 등록 · 사이트맵 · robots 허용의 **"최소 위생(hygiene)" 수준만** 유지.
10. **트렌드/키워드 리서치를 '카카오데이터트렌드'에 의존하지 않는다** — ⚠ 이미 종료됨(2025-08-29). 트렌드는 네이버 데이터랩/구글 트렌드 사용.
11. **`register.search.daum.net` · `webmaster.daum.net`의 URL/정책/UA/IP 변동을 분기별로 재확인한다** — ⚠ 소유권 대개편(업스테이지 인수) 직후이므로 반기→분기로 단축.

---

## 1. 한 줄 결론 (실효성 판단)

| 항목 | 값 | 시사점 |
|---|---|---|
| 다음 검색 백엔드 | **자체 엔진**(Bing/구글 제휴 아님) | 구글/Bing 색인이 다음 색인을 대신하지 못함 → **별도 등록 필요** |
| 다음 크롤러 | **Daumoa** (이미지: `Daumoa-image`) | robots.txt로 제어 가능, robots 준수 |
| 다음 점유율 | **국내 4위 소수 엔진** (Bing 근소 우위 3위 · 수치 SSOT=[MULTI_ENGINE §1.1](./MULTI_ENGINE_MASTER_2026.md)) | 단독 실효성 낮으나 **네이트가 다음 결과 그대로 노출** → 실효 청중은 점유율보다 큼 |
| 네이버 | 압도적 1위 (수치 SSOT=[MULTI_ENGINE §1.1](./MULTI_ENGINE_MASTER_2026.md)) | 한국인 청중의 주력은 네이버(별도 차원, 이 문서 범위 밖) |
| 소유·운영 | ⚠ **2025-12 카카오→AXZ 이관 → 2026-05 업스테이지가 AXZ 인수** | 콘솔/정책/크롤러 변동 리스크 상존 → **과투자 금지, 최소 위생만** |

**권장 스탠스:** 다음은 **"1회성 최소 위생 등록" 대상**. 웹마스터도구 등록 + 사이트맵 제출 + robots에서 Daumoa 허용, 그 이상 리소스 투입은 비권장. 한국어 콘텐츠가 있는 4개 사이트 모두 등록만 해두면 된다.

---

## 2. 다음 검색은 지금 무엇으로 구동되는가 (백엔드 + ⚠ 소유권 대변동)

### 2-A. 자체 엔진 (Bing 제휴 아님)

- 다음은 Bing/구글 백엔드를 쓰지 않는다. **자체 크롤러 `Daumoa`로 색인을 구축**한다. (Bing 제휴설은 근거 없음 — 오히려 Bing이 다음을 점유율에서 추월한 *경쟁자*.)
- 자체 엔진 사실은 소유권 이전 이후 **더 강화**됨 — 신소유주 업스테이지가 자체 LLM 'Solar'를 다음 검색과 결합하는 AI 포털 고도화를 추진 중.

### 2-B. ⚠ STALE 주의 — 카카오 소유 아님 (2026-07 현재)

이 부분은 지식 컷오프(2026-01) 이후 사건이라 **라이브로 재확인함(2026-07-01)**. 과거 자료의 "다음 = 카카오 자회사" 서술은 **STALE**이다.

| 시점 | 사건 | 출처 |
|---|---|---|
| 2023-05 | 콘텐츠CIC 출범(다음메일·카페·검색·뉴스·쇼핑) | — |
| 2025-05-22 | 카카오 이사회, 콘텐츠CIC 완전 분사 의결('다음준비신설법인', 당시 카카오 100% 자회사) | [kakaocorp.com](https://www.kakaocorp.com/page/detail/11565) |
| 2025-06 | 신설법인 사명 **'AXZ'** 로 변경 | — |
| **2025-12-01** | **다음 서비스의 법적 제공주체가 카카오 → AXZ로 이관**(회원 시스템 개편 착수) | [v.daum.net 2025-11-24](https://v.daum.net/v/20251124161348710) |
| **2026-05** | **AI 기업 '업스테이지(Upstage)'가 AXZ 인수 완료** → 다음은 업스테이지 소유 | [fnnews.com](https://www.fnnews.com/news/202605070819041556) |
| 2026-03 | (참고) 다음, '실시간 이슈 검색어' 6년 만에 부활 + 4색 로고 복원, 대대적 개편 | [v.daum.net 2026-03-04](https://v.daum.net/v/20260304061102362) |

> ⚠ **결론:** 2026-07 현재 다음은 카카오 자회사가 아니라 **업스테이지 소유**이며, Solar LLM 기반 AI 포털로 재편 중이다. 콘솔(`register`/`webmaster.daum.net`)은 현재도 작동하나(2026-07-01 라이브 확인) **소유·플랫폼 대개편 한가운데**이므로 URL/정책/UA/IP 변동 감시가 이전보다 더 필요. → Auto-apply #11(분기별 재확인).

### 2-C. ⚠ 생성형 AI 검색 — '미도입' 전제 무효화, 재편 진행 중

과거 자료의 "다음은 자체 생성형 AI 검색을 아직 전면 도입하지 않았다"는 서술은 **STALE/진행형 변수**다. ⚠ **보도 기준(1차 공식 미확인):** 2026-06-16 **업스테이지 미디어데이**에서 다음의 **'AI 에이전트 포털' 전환**(뉴스·검색·카페·티스토리·메일 접점을 Solar LLM과 결합해 의도 해석·정리형으로 개편) 로드맵이 발표됐다고 **다수 언론이 보도**했다(업스테이지의 AXZ 인수·Solar 결합 배경: [fnnews.com 2026-05](https://www.fnnews.com/news/202605070819041556)). 소유·플랫폼 재편은 **진행 중**이며, 다음 검색 결과에 AI 답변이 실제로 도입됐는지는 아직 미확정이다.

> **관측형 승격 트리거:** **다음 통합검색 결과(SERP)에 AI 답변/요약(브리핑) 블록이 실제로 관측되면 즉시** 이 문서의 "한국인 청중 트랙"을 **AEO/GEO 트랙**([`./AEO_GEO_PLAYBOOK_2026.md`](./AEO_GEO_PLAYBOOK_2026.md))으로 **승격 재평가**한다(이때 §6의 'ROI 음' 전제도 조건부 재검토). 측정 불가능한 '분기별 재확인'(§10)보다 이 관측 신호를 우선한다.

### 2-D. 네이트(Nate) = 다음 결과 그대로

- 네이트는 2014-01-14 자체 검색을 종료하고 다음 검색을 채택. 이후 네이트 검색결과·실시간 순위가 다음과 동일.
- ✅ **다음에 등록하면 네이트에도 자동 노출**된다(별도 등록 불필요). → Auto-apply #7.

### 2-E. 점유율 (SSOT=MULTI_ENGINE §1.1 — ⚠ 구체 소수점 단정 금지)

- **한국 검색 점유율 수치의 SSOT는 [`./MULTI_ENGINE_MASTER_2026.md`](./MULTI_ENGINE_MASTER_2026.md) §1.1이다**(값 재기입 금지 — 여기서는 링크만 두고, 다음 차원 판단에 필요한 방향성만 요약).
- **방향성(요약):** 다음은 국내 **4위** 소수 엔진(수년째 하락), Bing이 다음을 근소하게 앞서 3위, 네이버가 압도적 1위 — **다음 단독 트래픽 기대치는 낮다**(→ §6 ROI). Bing↔다음 순위 역전 **방향성은 확실**하나 **특정 소수점(예: "Bing 3.12%")은 단정하지 말 것**(출처별 편차 큼 → 최신·정확 수치는 위 SSOT 링크에서 확인).

---

## 3. 다음 색인 확보 경로 — 두 개의 도구 (헷갈리지 말 것)

다음은 도구가 **두 개**이고 목적이 다르다.

### 3-A. 검색등록 (`register.search.daum.net`) — 수동 디렉토리 등록

- **URL:** `https://register.search.daum.net/` (신규신청 `https://register.search.daum.net/index.daum`)
- **성격:** 사이트·블로그·웹문서를 **무료로 수동 등록**해 다음 검색에 노출. 카탈로그/디렉토리 방식.
- **절차:**
  1. `검색등록` → `등록하기` → `신규등록`
  2. `사이트 검색` 선택 → 대표 도메인 URL 입력(**쿼리스트링 제거, 메인 도메인만**) → `확인`
  3. 동의 3종 체크: 개인정보수집 이용 동의 / 개인정보 취급위탁 동의 / 서비스이용 동의 → `확인`
  4. `공통정보` · `신청자 정보` 입력 → `확인`
  5. 노출정보 입력: **제목(필수)**, 모바일 URL, 설명, **설명-품목(필수)**, 설명-지역, 카카오톡 채널 URL, **디렉토리(필수)**
- **심사:** 절차 간단, **5일 이내 심사 후 검색결과 반영**. 결과는 신청 이메일로 통보. 가이드 위반 시 편집/거부 가능.
- **용도:** 브랜드·회사 사이트를 다음(그리고 네이트)에 "존재 등록"시키는 가장 빠른 길. 4개 사이트 각각 1회 신청.

### 3-B. Daum 웹마스터도구 Beta (`webmaster.daum.net`) — 색인·사이트맵·통계

- **URL:** `https://webmaster.daum.net/`
- **성격:** 사이트 소유확인 후 **사이트맵/Seed URL 제출, 수집요청, 검색최적화·품질·수집/색인 통계**를 제공하는 SEO 콘솔. 구글 서치콘솔 / 네이버 서치어드바이저의 다음판. **Beta 상태**(2026-07-01 라이브 확인, 종료/마이그레이션 공지 없음).
- **소유확인 = 사이트 URL + PIN코드(영문+숫자 8~12자)** — 2026-07-01 `webmaster.daum.net/login` 라이브 실측:
  - PIN이 없으면 로그인 화면의 **`PIN코드 발급받기`**(→ `webmaster.daum.net/join`)로 발급 → 발급 안내 FAQ [38321](https://cs.daum.net/faq/service/15/category/4118/detail/38321). 발급 PIN을 사이트에 게시(메타/파일)한 뒤, 로그인 화면에 **사이트 URL + PIN 입력 → `인증하기`**.
  - ⚠ 운영사 표기 **© AXZ Corp.**(로그인 푸터, 2026-07-01) — 소유권 이전(§2-B) 실측 확인. 로그인은 **사이트별 PIN** 방식(계정 SSO 아님).
- **공식 제공 기능 5종** — Daum 고객센터 FAQ [38325](https://cs.daum.net/faq/service/15/category/4118/detail/38325)(무료):
  1. **Daum 검색최적화(SEO) 정보** — 검색엔진 최적화 여부 분석·제공
  2. **정보분석** — `수집정보`(크롤러가 수집한 정보)·`색인정보`(색인된 정보)·`검색정보`(검색결과 노출·클릭)를 **기간별 통계 차트**
  3. **문서분석** — title·내용·날짜가 잘못 나오면 **올바른 구조로 개선 가이드**
  4. **수집요청** — Seed URL 제출(아래)
  5. **수집 가능 여부 확인** — Daumoa가 해당 사이트를 수집 가능한 상태인지 검증(robots/응답 등)
- ⚠ **웹마스터도구는 색인의 '게이트'가 아니다** — 공식 명시: *"수집 가능한 웹사이트는 웹마스터도구 등록 여부와 무관하게 Daum 검색 기준에 따라 노출 가능"*. 도구는 진단·최적화·수집 가속 **보조**일 뿐, 등록 자체가 색인 조건은 아님.
- **실측 콘솔 네비게이션** — 2026-07-02 `webmaster.daum.net` 로그인 대조(korvia.com 등록됨: 응답코드 200·수집상태 가능). 상단 메뉴 **6개**:
  - `요약`(`/dashboard`) — 응답코드·서버연결·수집상태 + **수집정보·색인정보·검색정보** 3카드(일별 최근10일·주별 최근10주 평균 라인차트)
  - `검색최적화`(`/tool/seo`) — SEO 진단
  - `정보분석` — `수집`(`/stats/crawl`)·`색인`(`/stats/index`)·`검색`(`/stats/search`) 통계
  - `문서분석`(`/tool/analysis`) — title·내용·날짜 교정 가이드
  - `수집요청`(`/tool/collect`) — 제출 폼 3종(아래)
  - `로봇룰`(`/tool/robots`) — **현재 robots.txt를 불러와 Daumoa 수집 가능 여부를 테스트**(`결과보기`). ← critique가 '존재 불명확'하다던 robots 검증 도구 = **실재 확인**.
- **사이트맵/Seed URL 제출:** `수집요청`(`/tool/collect`) 페이지의 **입력 폼 3종**(각 URL 입력 + `수집요청` 버튼) — 2026-07-02 실측:
  1. **문서등록** — 단일 문서 URL 1건 수집 요청(즉시성 필요한 개별 페이지)
  2. **수집 Seed URL 등록(RSS 피드·Atom 피드·리스트 페이지)** — 피드/목록 URL(리스트 페이지는 연결된 URL을 **1단계까지** 수집)
  3. **수집 Seed URL 등록(사이트맵)** — 사이트맵 URL 입력(예: `https://도메인/sitemap.xml`)
  - **Seed URL로 허용되는 형식:** 사이트맵(XML), **RSS 피드**, **Atom 피드**, 목록(list) 페이지
  - **사이트맵 제한:** 파일당 **URL 최대 50,000개**, 크기 **최대 50MB**. 사이트맵 내 모든 URL의 **호스트 = 최초 수집요청 URL 호스트**(크로스도메인 불가)
  - 효과: 로봇이 자동 발견 못 한 페이지도 수집 → 검색 누락 최소화
- **수집 소요:** 로봇 상태·문서 수에 따라 **수개월** 걸릴 수 있음(즉시 아님). register(5일 심사)와 대조.
- **Seed URL 형식 상세**(FAQ [38543](https://cs.daum.net/faq/service/15/category/4118/detail/38543), 라이브 확인): **RSS/Atom 피드**(XML), **리스트 페이지**(리스트로 연결된 URL을 **1단계까지** 수집, **첫 페이지만 등록 가능**, 신규글 자동 감지), **사이트맵**(sitemaps.org 표준, gzip 허용). 등록 여부와 무관하게 노출은 Daum 기준을 따름.

### 3-C. 웹문서 색인 제한 12기준 (콘텐츠 품질 게이트) — 라이브 실측

> Daum 고객센터 FAQ [28967](https://cs.daum.net/faq/service/15/category/4118/detail/28967)(2026-07-01 대조) — "웹문서 검색결과에 나오게 하려면" 갖춰야 할 조건: ①robots에서 Daumoa 수집 허용 → ②응답 상태 정상(4XX/5XX·연결실패 없어야) → ③아래 12개 제한기준에 안 걸릴 것. 등록·사이트맵 제출을 해도 아래에 걸리면 색인 제외될 수 있다.
>
> ⚠ **제출 경로 단일화(P1-19):** FAQ 28967 원문은 위 조건 충족 후 마지막 단계로 **고객센터 '문의하기'로 등록 요청**을 두지만, 이는 **웹마스터도구(`webmaster.daum.net`) 이전의 구(舊) 절차**로 보인다. **현행 1차 제출 경로 = §3-B의 webmaster `수집요청`(Seed URL/사이트맵) 제출로 단일화**한다(신규 사이트는 이 경로만 실행). 고객센터 '문의하기'는 **사이트맵 제출 후에도 색인 누락이 지속될 때의 예외적 수동 에스컬레이션**으로만 사용한다(§3-B가 명시하듯 "등록 여부와 무관하게 노출은 Daum 기준을 따름" — 티켓 자체가 색인 조건은 아님).

1. 청소년 유해  2. 과도한 광고  3. 저작권 위배  4. 개인정보 노출  5. 다른 웹문서와 중복  6. **등록 후 내용 변질/삭제**(가격·판매기간 변동, **구인공고처럼 특정 기간 후 삭제**)  7. **분석 어려움(비표준 HTML·과도한 JavaScript)**  8. 여러 호스트에 동일 내용  9. 정보 임의 숨김(폰트 크기 0·바탕색 유사 텍스트·hidden 날짜)  10. 기계 생성(스크랩·번역기·키워드 반복)  11. 빈약한 문서(정보 부족·복붙·개발 중)  12. 기타 Daum 검색 규정 위반.

**⚠ KORVIA 직결 리스크:**
- **#6(기간만료 삭제)** → **Korvia 채용공고·EPIK 모집**처럼 마감 후 사라지는 페이지는 Daum 색인에 불리 → 상시 정보성 페이지(가이드/FAQ)를 별도로 두고 그쪽을 색인 대상으로.
- **#7(과도한 JS)** → **Next.js SPA(Passome·Korvia·ACT)** 는 클라이언트 렌더 과다 시 Daumoa가 본문을 못 읽을 수 있음 → 핵심 콘텐츠를 **SSR/SSG로 HTML에 실어** 내보낼 것(빈 컨테이너만 오면 색인 불가). → [`./PROJECT_STACK_PLAYBOOK.md`](./PROJECT_STACK_PLAYBOOK.md).
- **#5·#8(중복/멀티호스트)** → `www`↔non-`www`, http↔https, 언어 경로 중복은 **canonical**로 대표 URL 통일.

### 3-A vs 3-B 요약

| | 검색등록(`register`) | 웹마스터도구(`webmaster`) |
|---|---|---|
| 목적 | 수동 디렉토리 노출 | 크롤 색인·사이트맵·통계 |
| 반영속도 | 심사 5일 | 수집 수개월 |
| 사이트맵 | ✗ | ✓ (50k URL / 50MB) |
| 통계 | ✗ | ✓ |
| 권장 용도 | 브랜드 즉시노출 | 크롤 기반 지속색인 |

> **KORVIA 권장:** 둘 다 등록. `register`로 빠른 노출, `webmaster`로 사이트맵 제출해 전체 페이지 색인.

---

## 4. 크롤러 제어 — Daumoa & robots.txt

> 일반 robots.txt/AI-크롤러 정책은 [`./WEB_PLAYBOOK.md`](./WEB_PLAYBOOK.md) §1.1 참조. 여기서는 **Daumoa 전용 델타**만 다룬다.

### 4-A. User-agent 문자열

| 크롤러 | User-agent (관측/문서 변형) |
|---|---|
| 웹문서 | `Mozilla/5.0 (compatible; Daum/4.1; +http://cs.daum.net/faq/15/4118.html?faqId=28966)` |
| 웹문서(구) | `Mozilla/5.0 (compatible; MSIE or Firefox mutant; not on Windows server;) Daumoa/4.0` |
| 이미지 | `Daumoa-image/1.0` |
| robots 토큰(별칭) | `daumoa, damoa, daum, daumos, duamoa, duam, duamos` |

- ⚠ **주의:** 로그상 실제 토큰이 `Daumoa`가 아니라 그냥 `Daum`으로 찍히는 경우도 관측됨. robots 규칙은 **`Daumoa`** 로 지정하는 것이 표준이며, 봇은 `daumoa/daum` 계열 토큰을 인식.
- ⚠ **IP 출처(UNVERIFIABLE):** 한국 IP, (구)카카오 데이터센터 대역으로 관측되나 **공식 CIDR 목록 미공표**. 소유권이 AXZ→업스테이지로 이동해 대역이 바뀔 수 있으므로 **IP 하드코딩 금지, UA 토큰 기준 허용**. → Auto-apply #2.

### 4-B. robots.txt 설정

허용(권장 — 기본):
```
User-agent: Daumoa
Allow: /

Sitemap: https://www.example.com/sitemap.xml
```

전면 차단(원치 않을 때만):
```
User-agent: Daumoa
Disallow: /
```

- robots.txt는 **사이트 루트**(`https://도메인/robots.txt`)에 위치. Daumoa는 robots.txt를 **정상 준수**.
- **Next.js(App Router) `app/robots.ts` 스니펫** — 기존 AI-크롤러 룰 배열에 Daumoa 항목만 추가:
```ts
// app/robots.ts — 기존 rules[] 에 아래 객체 하나 추가
import type { MetadataRoute } from 'next'

export default function robots(): MetadataRoute.Robots {
  return {
    rules: [
      // ... 기존 GPTBot/ClaudeBot/PerplexityBot 등 AI 크롤러 룰 (WEB_PLAYBOOK §1.1) ...
      { userAgent: 'Daumoa', allow: '/', disallow: ['/api/', '/_next/', '/admin/'] },
    ],
    sitemap: 'https://www.example.com/sitemap.xml', // 반드시 절대 URL 단일 문자열 (.trim() 주의)
  }
}
```
> ⚠ 환경변수로 sitemap URL을 조립하면 **항상 `.trim()`** — trailing newline이 `Sitemap: https://...\n` 로 깨지는 실제 버그가 있었다(상세: [`./WEB_PLAYBOOK.md`](./WEB_PLAYBOOK.md) §1.1 / §11.4).

- **WordPress(Kimchi)** — SEO 플러그인(예: Yoast/Rank Math) 또는 서버 robots에서 Daumoa를 명시 허용하고 sitemap 라인 포함. 플러그인이 XML 사이트맵을 생성하면 그 URL을 `webmaster.daum.net`에 제출.

### 4-C. Cloudflare 뒤 사이트 (Korvia · Passome · ACT) 유의

- 전역 `Disallow: /`나 과도한 봇 차단(**Cloudflare WAF / Bot Management 룰 포함**)으로 Daumoa가 막히지 않도록 신규 배포마다 점검.
- ⚠ 허용 규칙은 **카카오 IP 대역이 아니라 UA 토큰(`Daumoa`/`daum`) 기준**으로 — 소유권 이전으로 IP가 바뀔 수 있고 CIDR은 미공표(§4-A).

---

## 5. 카카오 검색 노출 채널

- **노출 채널:** 다음 통합검색(웹문서 · 사이트 · 블로그 · 이미지) + **네이트 검색**(동일 결과). 카카오톡 채널 URL을 검색등록에 넣을 수 있어 브랜드 채널 연계 가능(카카오톡 `#검색` 노출 고려).
- ⚠ **관련 서비스 종료:** '카카오데이터트렌드'(다음 검색 트렌드 조회)는 **2025-08-29 종료됨**(과거형 — 과거 자료의 "종료 예정"은 STALE). 트렌드/키워드 리서치는 이 도구에 의존하지 말고 **네이버 데이터랩 / 구글 트렌드** 사용. → Auto-apply #10.

---

## 6. 실효성(ROI) 냉정 평가 — 점유율 대비 노력

| 관점 | 판단 |
|---|---|
| 순수 점유율 | 약 3%(4위). Bing보다 낮음. **단독 트래픽 기대치는 낮다.** |
| 실효 도달 | 네이트가 다음 결과를 그대로 노출 → **체감 청중은 점유율보다 큼**(무료 보너스). |
| 초기 비용 | 사이트당 **1회성 등록 30~60분**(register + webmaster + robots 한 줄). 유지비 거의 0. |
| 한계 비용 | 다음 **전용** 콘텐츠 최적화의 추가 유입은 미미 → 여기서 멈춰야 ROI 양(+). |
| 리스크 프리미엄 | ⚠ 소유권 대개편(업스테이지)·AI 재편 중 → 지금 대규모 투자하면 정책 변동에 매몰될 위험. |

> **판정:** **"등록·사이트맵·robots 허용"의 최소 위생까지만 = ROI 양(+).** 그 이상(다음 전용 콘텐츠·랭킹 튜닝)은 **ROI 음(−), 하지 말 것.** 네이버(별도 차원)와 구글/AEO/GEO에 자원을 배분하라. → Auto-apply #9.

---

## 7. KORVIA 4개 사이트 실행 체크리스트

| 단계 | 조치 | 사이트별 메모 |
|---|---|---|
| 1 | robots에 `User-agent: Daumoa / Allow: /` + `Sitemap:` 절대 URL | Next.js 3개=`app/robots.ts`(§4-B); Kimchi=WP SEO 플러그인/서버 robots |
| 2 | Cloudflare 봇/WAF가 Daumoa(UA 기준) 차단 안 하는지 확인 | Korvia · Passome · ACT(CF 뒤) 특히 |
| 3 | `webmaster.daum.net` 소유확인(PIN 또는 메타/파일) | 4개 각각 |
| 4 | 웹마스터도구 → `수집요청 → Seed URL(사이트맵)` 제출 | 사이트맵 50k URL/50MB, 동일 호스트 준수 |
| 5 | `register.search.daum.net` 검색등록(대표 URL + 제목/품목/디렉토리 필수) | 즉시 노출용, 5일 심사 |
| 6 | 이중언어: **한국어 대표 URL 우선 등록** | 다음/네이트 청중은 한국인(§8) |
| 7 | (선택) 블로그/뉴스형은 RSS/Atom도 Seed URL 제출 | Kimchi · Passome 콘텐츠성 페이지 |

### 프로젝트별 적용 노트

- **Passome (`www.passome.com`, Next.js/Vercel):** `app/robots.ts`에 Daumoa 룰 추가 + `sitemap.ts`의 절대 URL 사용. AI/AX 컨설팅 콘텐츠 페이지가 늘면 RSS Seed URL 병행. 한국어 대표 URL 우선.
- **Korvia (`www.korvia.com`, Next.js):** 이미 AI-크롤러 허용 정책 운영 중([`./WEB_PLAYBOOK.md`](./WEB_PLAYBOOK.md) §12). **YMYL(비자/취업)** — 한국어 EPIK/비자 페이지가 있으면 한국어 URL을 register에 등록해 다음/네이트에서도 잡히게. Cloudflare 뒤이므로 §4-C 필수 점검.
- **Kimchi Mobile (`www.kimchimobile.com`, WordPress):** WP SEO 플러그인 XML 사이트맵 URL을 webmaster에 제출. **YMYL(통신/금융, eSIM)** — 외국인 대상이지만 한국어 랜딩이 있으면 다음 등록으로 국내 검색 커버. 콘텐츠성(블로그/FAQ)은 RSS Seed URL 병행.
- **ACT Art Center (`actartcenter.com`, Next.js):** 미술 교육/센터 = **로컬 검색 성격 강함** → register '설명-지역'·디렉토리를 정확히 채워 지역 노출 극대화(다음/네이트에서 지역 브랜드 검색 대응). Cloudflare 뒤면 §4-C 점검.

---

## 8. 이중언어 · 외국인 대상 · YMYL 특이사항

- **다음/네이트 청중 ≈ 한국인.** 외국인(영어) 청중은 Google이 지배적이므로 **다음 등록은 한국어 대표 URL을 우선**으로 한다(영어 URL은 Google/AEO 트랙에서 처리 — [`./AEO_GEO_PLAYBOOK_2026.md`](./AEO_GEO_PLAYBOOK_2026.md)).
- **한/영 이중언어 사이트:** register 검색등록의 대표 URL은 한국어 페이지(또는 `/ko`)로. hreflang·canonical은 기존 규칙([`./WEB_PLAYBOOK.md`](./WEB_PLAYBOOK.md) §3.1, [`./NEW_PAGE_CHECKLIST.md`](./NEW_PAGE_CHECKLIST.md) A6)을 그대로 따르고 다음 전용 변형은 만들지 않는다.
- **YMYL(비자/취업=Korvia, 통신/금융=Kimchi):** 다음도 색인은 하되, **신뢰 신호(정확성·출처·전문가 인용)의 본진은 여전히 구글/AEO/GEO 트랙**이다. 다음 랭킹을 위해 YMYL 콘텐츠를 별도 튜닝하지 말 것(ROI 음, §6). 정확성 규칙은 상위 문서 준수.
- **AI 답변엔진 교차:** ⚠ 업스테이지 Solar 결합으로 다음이 AI 답변형이 되면, 다음도 GEO 대상이 될 수 있음(§2-C). 재편 완료 시 [`./AEO_GEO_PLAYBOOK_2026.md`](./AEO_GEO_PLAYBOOK_2026.md) §3(플랫폼별 인용 성향)에 '다음/Solar' 행 추가 검토.

---

## 9. 기존 스킬 문서와의 관계 (중복 금지 맵)

| 주제 | 이 문서(Daum/Kakao) | 상위/일반 문서 |
|---|---|---|
| robots.txt 일반·AI 크롤러 | Daumoa UA·허용 델타만(§4) | [`./WEB_PLAYBOOK.md`](./WEB_PLAYBOOK.md) §1.1, §12.1 |
| sitemap 일반·priority·prune | Daum 제출 경로·50k/50MB 한도만(§3-B) | [`./WEB_PLAYBOOK.md`](./WEB_PLAYBOOK.md) §1.2 |
| llms.txt / schema | (다루지 않음) | [`./WEB_PLAYBOOK.md`](./WEB_PLAYBOOK.md) §1.3·§2, [`./AEO_GEO_PLAYBOOK_2026.md`](./AEO_GEO_PLAYBOOK_2026.md) §4 |
| AI 답변엔진 전략(GEO/AEO) | 교차 가능성 플래그만(§2-C, §8) | [`./AEO_GEO_PLAYBOOK_2026.md`](./AEO_GEO_PLAYBOOK_2026.md) 전체 |
| 신규 페이지 배포 체크 | "다음 사이트맵 자동 반영 확인" 링크만 | [`./NEW_PAGE_CHECKLIST.md`](./NEW_PAGE_CHECKLIST.md) |

> **분류:** Daum/Kakao 색인은 **네이버(별도 차원)와 함께 "한국인 청중" 트랙**. 구글/Bing/IndexNow/AEO/GEO는 "외국인 청중 + AI 답변엔진" 트랙. 신규 페이지 배포 시 [`./NEW_PAGE_CHECKLIST.md`](./NEW_PAGE_CHECKLIST.md)에 "다음 웹마스터도구 사이트맵 반영 확인"을 항목으로 추가 권장(신규 URL은 사이트맵을 통해 Daumoa가 재수집).

---

## 10. ⚠ 재검토 트리거 (감시 목록)

- **[STALE 해소 감시]** 업스테이지의 AXZ(다음) 인수·Solar 기반 AI 검색 재편 진행 → 6~12개월 내 다음 색인·랭킹·AI 노출 로직 변경 가능. **분기별 재확인.**
- **[콘솔 존속]** `register.search.daum.net` / `webmaster.daum.net` URL·기능·통합 여부 — 소유권 이전 직후이므로 분기 단위 점검.
- **[크롤러 UA/IP]** Daumoa UA 토큰·IP 대역 변경 가능성 — robots·CF 룰을 IP가 아닌 UA 기준으로 유지(§4).
- **[점유율]** Bing vs 다음 순위·수치는 출처 편차 큼 → 단정 금지(§2-E).

---

## Sources

라이브 재확인일: **2026-07-01**. `(official)` = 공식 1차 소스.

- (official) Daum 검색등록 — https://register.search.daum.net/
- (official) Daum 웹마스터도구 (로그인) — https://webmaster.daum.net/
- (official) 카카오, Daum 웹마스터 도구 Beta 오픈 — https://www.kakaocorp.com/page/detail/8807
- (official) 웹문서 검색 도움말 (Daumoa UA·사이트맵 50k/50MB·Seed URL, Daum 고객센터) — https://cs.daum.net/faq/15/4118.html
- (official) 검색 등록 도움말 (Daum 고객센터) — https://cs.daum.net/faq/15/9334.html
- (official) 네이트 검색 = 다음 결과 (Daum 고객센터) — https://cs.daum.net/faq/261/15308.html
- (official) 카카오 콘텐츠CIC 분사 의결(2025-05-22) — https://www.kakaocorp.com/page/detail/11565
- 다음, 12/1부터 법적 제공주체 카카오→AXZ 이관 (이데일리/다음뉴스, 2025-11-24) — https://v.daum.net/v/20251124161348710
- ⚠ 업스테이지, AXZ(다음) 인수·Solar 결합 AI 포털 (파이낸셜뉴스, 2026-05) — https://www.fnnews.com/news/202605070819041556
- (참고) 다음 실시간 검색 6년 만에 부활·4색 로고 복원 (이데일리/다음뉴스, 2026-03-04) — https://v.daum.net/v/20260304061102362
- 다음 웹마스터도구 사이트맵/Seed URL 등록 실무 가이드 (큐샵 고객센터, 2026) — https://help.qshop.ai/customer_support/guide/setting/manage/seo/daum
- 다음 웹마스터 도구 소유확인(PIN)·수집요청 절차 (CreatorLink) — https://supportcenter.creatorlink.net/creatorlink/marketing_tool/webmaster_tools/daum_webmaster_tool
- Daumoa user-agent / IP (Hall) — https://usehall.com/agents/daum-bot
- Daumoa UA 목록 (udger.com) — https://udger.com/resources/ua-list/bot-detail?bot=Daumoa
- ⚠ 카카오데이터트렌드 종료(2025-08-29) — https://www.1000001.net/2025/07/2025-8-29.html
- 한국 검색엔진 점유율 2025(다음 4위·Bing 3위 역전, ⚠ 소수점 편차) — https://seo.tbwakorea.com/blog/searchengine-ranking
- (배경) 다음 하락·AI 논의 (Korea Herald) — https://www.koreaherald.com/article/3456625
