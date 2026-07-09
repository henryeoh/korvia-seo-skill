---
last_verified: 2026-07
reverify_by: 2026-Q4
---

# STALE REGISTER — 사실 부패 감시 대장 (freshness/staleness 폐루프의 register 축)

> Generated 2026-07-02 · Owner: Henry Oh (KORVIA) · ⚠ 이 문서는 **"언제·무엇을 다시 확인해야 하나"**만 담는다. 사실 값 자체는 각 SSOT 문서가 보유하며 여기서는 재기입하지 않고 링크만 건다(register 자신이 stale 복제본이 되지 않도록).

---

## 0. 역할과 사용법 (read-path)

`korvia-seo-skill/docs/`의 freshness/staleness 폐루프는 두 축으로 구성된다:

1. **문서 단위 타이머** = 각 문서 front-matter의 `last_verified` / `reverify_by`.
2. **개별 사실 단위 타이머** = 이 대장(register). 부패 속도가 문서 평균보다 빠른 개별 사실(콘솔·API·봇 UA·점유율·리치결과 일몰 등)을 사실 단위로 추적한다.

**에이전트 read-path**: `content-writer`·`quality-harness`·`geo-optimizer`는 YMYL(비자/취업/통신/금융) 콘텐츠 발행 전, 관련 사실의 `재확인 기한/트리거`가 지났는지 이 대장에서 확인한다. (훅으로 자동 적용되는 것이 아니라, 에이전트가 읽어서 판단하는 read-path다.)

**예약 에이전트(배선 대기 — 게이트)**: 분기 1회 예약 에이전트(schedule/cron)가 `재확인 기한`을 지난 문서·행을 나열하고 재확인 태스크를 연다. 현 결정은 **지식베이스만 정리, 실제 배선은 나중**이므로 cron·캘린더 생성은 §2의 승인 게이트 대상이다.

**기재 규칙**: STEP1(2026-07-02) 공식 재확인을 통과한 사실만 "STEP1 확인" 열에 판정+출처로 기재한다. 재확인 범위 밖·보도·3자 출처는 그대로 ⚠ 라벨을 유지하고, 값은 SSOT 문서에서만 읽는다.

**판정 범례**
- ✅ = STEP1(2026-07-02) 공식 재확인 통과(CONFIRMED)
- ◐ = STEP1 PARTIAL(부분 확인 — finding 문장 그대로 인용, 미확인 부분 명기)
- ⚠ = STEP1 미검증(재확인 대상 아님 / 보도 기준 / 3자 출처) — SSOT 문서의 값·라벨을 그대로 사용

---

## 1. 감시 대장 (초기 행)

> 감시 주기 근거: `MULTI_ENGINE_MASTER_2026.md` §3.2(감시 주기 반기→분기) · rule 3(Naver 소유확인 1년 → 연장 태스크). 기본 재확인 기한 = **2026-Q4**(front-matter와 동기), 일몰 예고가 있는 행만 별도 기한.

| # | 감시 대상 | SSOT 문서 (값 보유·링크만) | STEP1 확인 (2026-07-02) | 재확인 기한 / 트리거 |
|---|---|---|---|---|
| S1 | FAQ 구조화데이터 리치결과·관련 지원 일몰(예고 2026-08) | [`SCHEMA_JSONLD_CATALOG.md`](./SCHEMA_JSONLD_CATALOG.md) · [`GOOGLE_SERP_STRUCTURED_AI_2026.md`](./GOOGLE_SERP_STRUCTURED_AI_2026.md) | ⚠ STEP1 V 범위 밖 — 재확인 안 됨(SSOT 문서 값 사용) | **2026-08 이전** — 일몰 시점에 SCHEMA/SERP의 FAQ 항목 재검증 |
| S2 | Naver 통합검색 "AI탭" 정식화 | [`NAVER_SEARCHADVISOR_PLAYBOOK.md`](./NAVER_SEARCHADVISOR_PLAYBOOK.md) | ⚠ 보도 기준(한국어 언론 2026-04-28) — 공식 미확정, AI 브리핑 콘솔 데이터 없음 | Naver 공식 발표 / 콘솔 지표 제공 시 → 2026-Q4 점검 |
| S3 | Daum·카카오 검색 공정위 관련 이슈(발표 2026-06-16) | [`DAUM_KAKAO_SEARCH.md`](./DAUM_KAKAO_SEARCH.md) | ⚠ 보도 기준(2026-06-16) — 공식 확정 아님("인수 완료" 등 확정 표기 금지) | 관측 트리거 / 공식 확정 시 → 2026-Q4 점검 |
| S4 | GSC 생성형 AI performance 리포트 확장 | [`GOOGLE_SEARCH_CONSOLE_INDEXING.md`](./GOOGLE_SEARCH_CONSOLE_INDEXING.md) | ✅ V1 CONFIRMED — 2026-06-03 출시, impressions-only(clicks/CTR/쿼리 분해 없음), 옵트아웃은 일반 순위 무영향, 영국 우선 단계 롤아웃 ([blog](https://developers.google.com/search/blog/2026/06/gen-ai-performance-reports)) | 전체 대상 확대 · 지표(clicks/CTR/query) 추가 여부 → 2026-Q4 |
| S5 | Grok(xAI) 크롤러 user-agent | [`GEO_AEO_AI_ENGINES_2026.md`](./GEO_AEO_AI_ENGINES_2026.md) | ⚠ STEP1 미검증 — xAI 공식 봇 문서로 UA 미확인, ⚠ 라벨 유지 | xAI 공식 봇/robots 문서 확인 시 → 2026-Q4 |
| S6 | Google Business Profile 엽서(우편) 인증 | [`LOCAL_SEO_NAVER_PLACE_GBP.md`](./LOCAL_SEO_NAVER_PLACE_GBP.md) | ⚠ 3자 출처("~2026-01 대다수 종료·영상 인증 기본") — 공식 미확정 | Google 공식 verification 문서 재확인 → 2026-Q4 |
| S7 | IndexNow 참여 검색엔진 목록 | [`BING_YAHOO_INDEXNOW.md`](./BING_YAHOO_INDEXNOW.md) **(SSOT)** | ✅ V8 CONFIRMED — Bing·Naver·Seznam.cz·Yandex·Yep·Amazon 참여, Google·Daum 미참여, 제출 URL은 참여사 전체 자동 공유 ([faq](https://www.indexnow.org/faq)) | 참여사 변동(Google/Daum 합류 등) → 2026-Q4 |
| S8 | GA4 기본 채널 'AI Assistants' | [`GEO_AEO_AI_ENGINES_2026.md`](./GEO_AEO_AI_ENGINES_2026.md) §E | ✅ V5 CONFIRMED — 2026-05-13 추가, ChatGPT·Gemini·Deepseek·Copilot·Grok referrer 자동분류, Google AIO/AI Mode는 Organic으로 제외, 설정 불필요 ([support](https://support.google.com/analytics/answer/9756891)) | 기본 채널 정의 변경 시 → 2026-Q4 |
| S9 | Cloudflare AI Crawl Control 기능 | [`GEO_AEO_AI_ENGINES_2026.md`](./GEO_AEO_AI_ENGINES_2026.md) §E · robots 연동 [`ROBOTS_SITEMAP_RSS_TEMPLATES.md`](./ROBOTS_SITEMAP_RSS_TEMPLATES.md) | ◐ V11 PARTIAL — 전 플랜(무료 포함) 제공·회사별/크롤러별 분류·CSV 내보내기는 확인; **"20+개 크롤러" 수치는 공식 문서에 명시 없어 미확인** ([docs](https://developers.cloudflare.com/ai-crawl-control/features/analyze-ai-traffic/)) | 기능·플랜·분류 변경 시 → 2026-Q4 |

---

## 2. 배선 상태 (게이트 — Henry 승인 필요)

이 대장은 폐루프의 **register 축(=사실 대장 자체)**만 완성한 상태다. 실행 축(자동 재확인)은 다음 배선을 요하며, 현 결정("지식베이스만, 배선 나중")에 따라 **승인 게이트**로 남긴다:

- [ ] **분기 예약 에이전트(schedule/cron)**: 이 대장 + 각 문서 front-matter의 `reverify_by`를 지난 항목을 나열하고 재확인 태스크를 여는 예약 에이전트. (훅 아님 — 예약 실행 + read-path.)
- [ ] **Naver 소유확인 만료 캘린더 이벤트**: 현재는 문서(rule 3)에 "1년 후 연장 태스크 등록"이라고 **서술만** 되어 있고 실제 캘린더 이벤트는 없다. 실제 이벤트 생성 필요.
- [ ] **read-path 배선(STEP3)**: `quality-harness` / `content-writer` / `geo-optimizer` 발행 전 이 대장 참조를 명문화.

> 이 대장을 추가한 이유: 첫 빌드가 이미 부패한 P0 사실 2건(Daum≠Bing 백엔드, IndexNow 미참여)을 실었다. 사람이 발행 시점에 "재확인"을 기억하는 데 의존하면 YMYL(비자·통신) 콘텐츠에 stale 사실이 새어든다. 문서 front-matter(문서 단위) + 이 대장(사실 단위) + 예약 에이전트(실행)가 3중으로 이를 막는다.
