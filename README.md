# korvia-seo-skill v3

SEO Harness Team — Claude Code Skill for comprehensive SEO/SEM/GEO/CWV optimization.

**Proven on production**: www.korvia.com saw 21+ commits across 2 days deliver Lighthouse SEO 92→100, CLS desktop 0.89→≤0.1, BP 54→77 on homepage, image payload -185 KB, all with zero blockers across 5 consecutive 3-layer agent pipeline rounds.

## Overview

A 6-agent SEO expert team + proven orchestration patterns for large-scale site-wide sweeps.

### Agents
- **Intake/Orchestrator** — URL analysis and task delegation
- **Researcher** — Keyword, competitor, SERP analysis
- **SEM Strategist** — Google Ads blueprint, Smart Bidding
- **Content Writer** — E-E-A-T optimized page rewriting
- **Technical SEO** — Meta, Schema, CWV, robots, llms.txt
- **GEO Optimizer** — ChatGPT/Perplexity/Gemini citation
- **Quality Harness** — 24-item AI penalty avoidance gate

### Orchestration patterns (v2 addition)
- **3-layer agent pipeline** — Worker parallel → Audit independent → Human final
- **Batch splitting** — 40+ file sweeps via 5-7 file-disjoint parallel batches
- **Manager agent** — Per-page checklist audit producing ready-to-dispatch prompts

## Quick Start

```bash
# Clone
git clone https://github.com/henryeoh/korvia-seo-skill.git

# Install to your project
cp -r korvia-seo-skill/.claude/skills/seo-harness-team /path/to/project/.claude/skills/
cp -r korvia-seo-skill/.claude/agents /path/to/project/.claude/

# In Claude Code:
# /full-audit https://your-website.com
```

## Commands

| Command | Agent | Description |
|---------|-------|-------------|
| `/full-audit <URL>` | All | Complete SEO pipeline |
| `/seo-audit <URL>` | Technical SEO | Site health score |
| `/keyword-research <topic>` | Researcher | Keyword clusters |
| `/competitor-analysis <URL>` | Researcher | Competitor gaps |
| `/sem-strategy <URL>` | SEM Strategist | Google Ads blueprint |
| `/write-page <URL> <keyword>` | Content Writer | Page rewrite |
| `/meta-optimize <URL>` | Technical SEO | Meta tag A/B sets |
| `/schema-generate <URL>` | Technical SEO | JSON-LD Schema |
| `/geo-optimize <URL>` | GEO Optimizer | AI search optimization |
| `/llms-txt <URL>` | Technical SEO | llms.txt generation |
| `/robots-update <URL>` | Technical SEO | AI crawler config |
| `/harness-check` | Quality Harness | 24-item quality gate |

## Documentation (NEW in v2)

### 🤖 `docs/AEO_GEO_PLAYBOOK_2026.md`  · ⚠ LEGACY
AI 검색 시대 상위 전략 레퍼런스 (2026-05-29 lock-in). SEO/AEO/GEO 3-레이어 정의, Princeton 정량 근거(구체 %는 **SUPERSEDED — 인용 금지**, 방향만: 인용문·통계·출처=상위 레버, SSOT=`GEO_AEO §B/§Δ`), 플랫폼별 인용 성향(수치=3자 측정·방향성만), schema 토대(llms.txt=저ROI 강등), 측정 툴링, 한국/네이버 맥락, KORVIA 적용 3-Phase 플랜. 운영 체크리스트는 `geo-optimizer.md` 참조.
> ⚠ **SUPERSEDED (부분) — KEEP for 전략 why**: 정량 수치·llms.txt·스키마 생사는 폐지/정정됨 → SSOT는 `GEO_AEO_AI_ENGINES_2026 §B/§Δ` · `SCHEMA_JSONLD_CATALOG` · `ROBOTS_SITEMAP_RSS_TEMPLATES §A`.

### 📘 `docs/WEB_PLAYBOOK.md`  · ⚠ LEGACY
Full 16-category SEO + Performance + a11y playbook distilled from 21+ production commits. Every category has concrete code patterns, gotchas discovered, and verification steps. Covers:
- robots/sitemap/llms.txt (incl. env `.trim()` bug)
- Schema.org entity graph (Organization + WebSite + Service + FAQPage + HowTo + Event + CollectionPage)
- CLS root causes (7 patterns)
- Hydration #418 (safety nets)
- Perf (GTM lazyOnload, browserslist modern, optimizeCss, unoptimized)
- Security headers (HSTS/COOP/CORP/CSP Report-Only)
- CrUX vs Lighthouse interpretation
- Next.js gotchas (styled-jsx client-only, Tailwind v4)
> ⚠ **SUPERSEDED (부분) — KEEP for Next.js 구현 패턴**: robots/sitemap/스키마 그래프(FAQPage/HowTo 포함) 사실은 SSOT로 이관 — FAQ/HowTo 리치결과는 2026 폐지(온페이지 파싱만), robots 목록은 `ROBOTS_SITEMAP_RSS_TEMPLATES §A`.

### 📋 `docs/NEW_PAGE_CHECKLIST.md`  · ⚠ LEGACY
17-point checklist for every new Next.js page. Use as a pre-commit gate. Split into:
- A. Metadata (6 items)
- B. Schema.org JSON-LD (3 items)
- C. Images (4 items)
- D. CLS defense (3 items)
- E. Accessibility (1 item)
- Plus: page type-specific additional checks + validation commands
> ⚠ **SUPERSEDED (부분) — KEEP for 프리커밋 체크리스트**: 발행 게이트 자체는 `docs/CONTENT_AUTORULES.md` 마스터 게이트([AUTO]) + `slop-judge`([JUDGE])가 우선(파생 런타임 카드 `docs/GATE_CARD.md` **생성됨(2026-07-02)**).

### 🎯 `docs/CLS_SEVEN_SOURCES.md`
7 most common CLS causes in Next.js + Tailwind v4:
1. Swiper pre-hydration stack (biggest — can cause CLS 0.85 alone)
2. Raw `<img>` without width/height
3. Intl API locale mismatch
4. Animated counter width growth
5. Animations using layout properties
6. Font swap without adjustFontFallback
7. Async client component materialization

### 🖼️ `docs/NEXT_IMAGE_SWIPER.md`
Swiper + next/image pattern: `<Image fill>` has quirks, but `<Image width={N} height={M}>` works fine. 250×250 avatars → 36×36 display = raw `<img>` wastes ~12KB/avatar. Migration to next/image saves ~70KB/page.

### 📊 `docs/CRUX_LIGHTHOUSE_INTERPRETATION.md`
When to trust which metric:
- CrUX (real users, 28-day rolling) — source of truth for SEO ranking but lag-heavy
- Lighthouse synthetic — immediate reflection, use for iterative fixing
- DevTools Performance — root-cause debugging
- Common confusion: "CrUX still bad after I fixed it" = 4-week propagation

### 🛡️ `docs/CSP_ALLOWLIST.md`
CSP writing without breaking 3rd parties:
- Microsoft Clarity: `scripts.clarity.ms` + `c.clarity.ms` + `y.clarity.ms` (wildcard `*.clarity.ms`)
- Google Ads: `googleadservices.com` + `googleads.g.doubleclick.net` + `google.com/ccm` + `td.doubleclick.net`
- Start Report-Only → 1-2 weeks monitoring → enforcing

### 🔄 `docs/3LAYER_AGENT_PIPELINE.md`
Proven orchestration for 10+ file bulk operations:
```
[Manager discovers] → [Workers parallel batches] → [Independent audit] → [Human final commit]
```
5 consecutive rounds on production delivered zero blockers, 100+ files modified.

## 🌏 Multi-Engine Indexing & Korean-Search KB (NEW 2026-07)

한국(**Naver·Daum 최우선**) + Google/Bing/Yahoo + AI 답변엔진(GEO/AEO)까지 **색인·노출 전 과정**을 다루는 **12편** 지식팩(2026-07 신설 — 아래 표가 정확한 목록. `docs/`의 v2 기존 8편과 레거시 `AEO_GEO_PLAYBOOK_2026`는 이 12편에 미포함). 각 문서 상단 `## 0. 자동 적용 규칙(Auto-apply)` 블록은 훅이 아니라 **작성/기술 에이전트의 read-path로 로드**된다 — 발행 시 `docs/CONTENT_AUTORULES.md`의 마스터 게이트(규칙 SSOT, §0)를 먼저 읽고, 페이지가 특정 엔진을 노릴 때만 해당 엔진 §0(~300토큰)을 추가로 연다. 전문(§1+)은 게이트 실패 시에만. 파생 런타임 카드 `docs/GATE_CARD.md`(각 §0 블록의 중복제거 합본, ≤~900토큰) **생성됨(2026-07-02)**. 실제 콘솔 기준: 네이버 서치어드바이저 `searchadvisor.naver.com/console/board`, 다음 웹마스터도구 `webmaster.daum.net/dashboard`.

| 문서 | 역할 |
|------|------|
| `docs/MULTI_ENGINE_MASTER_2026.md` | 허브 — 청중 매핑·크로스엔진 색인 파이프라인·결정 매트릭스·발행 런북 |
| `docs/CONTENT_AUTORULES.md` | ★ **발행 전 게이트** — 모든 기사/홈페이지 자동적용 체크리스트(엔진·GEO·한국·이중언어·YMYL) |
| `docs/NAVER_SEARCHADVISOR_PLAYBOOK.md` | 네이버 서치어드바이저 운영 + C-Rank/DIA/노출영역/AI 검색 |
| `docs/DAUM_KAKAO_SEARCH.md` | webmaster.daum.net 대시보드 + Daumoa + **별도 제출**(IndexNow 미커버) |
| `docs/GOOGLE_SEARCH_CONSOLE_INDEXING.md` | GSC 운영 + 색인 파이프라인 + Indexing API 범위·한도 |
| `docs/GOOGLE_SERP_STRUCTURED_AI_2026.md` | 리치결과 생사표 + E-E-A-T + AI Overviews/AI Mode |
| `docs/BING_YAHOO_INDEXNOW.md` | Bing WMT + IndexNow + Bing→ChatGPT/Copilot grounding |
| `docs/GEO_AEO_AI_ENGINES_2026.md` | 엔진별 인용 메커니즘 + llms.txt 현실 + AI봇 + 측정 |
| `docs/SCHEMA_JSONLD_CATALOG.md` | 복붙 JSON-LD 템플릿(타입별·엔진 지원·생사) |
| `docs/ROBOTS_SITEMAP_RSS_TEMPLATES.md` | robots(AI봇 정책)·sitemap·RSS·IndexNow 키 템플릿 |
| `docs/PROJECT_STACK_PLAYBOOK.md` | Next.js/WordPress 스택별 구현 레시피 |
| `docs/LOCAL_SEO_NAVER_PLACE_GBP.md` | 네이버 스마트플레이스 + Google Business Profile(로컬/지도) |

**SSOT 맵 (owner만 값 보유 · 나머지 문서는 링크만 · 중복 사실은 owner부터 고칠 것)**:

| 사실 도메인 | Owner (값 보유) |
|------------|-----------------|
| robots 템플릿 · AI봇 UA 목록 | `ROBOTS_SITEMAP_RSS_TEMPLATES §A` |
| IndexNow 참여 목록 | `BING_YAHOO_INDEXNOW` |
| 한국 검색 점유율 | `MULTI_ENGINE_MASTER_2026` (각주) |
| 네이버 플레이스/지도 랭킹 | `LOCAL_SEO_NAVER_PLACE_GBP §1-6` |
| GEO 정량 수치(Princeton 등) · llms.txt 현실 | `GEO_AEO_AI_ENGINES_2026 §B/§Δ` |
| 스키마 리치결과 생사 | `SCHEMA_JSONLD_CATALOG` |
| CWV 임계 | `CRUX_LIGHTHOUSE_INTERPRETATION` / `CLS_SEVEN_SOURCES` |
| 발행 게이트 | `CONTENT_AUTORULES` (규칙 SSOT · 마스터 게이트) — 파생 런타임 [AUTO] 카드 `docs/GATE_CARD.md` **생성됨(2026-07-02)** |

> ⚠ **알려진 부채**: 위 사실 일부가 여러 문서에 중복 기술됨 — FAQ 리치 API 일몰(2026-08)·AI봇 UA 변경 시 **SSOT 문서부터 고치고 나머지는 링크로** 유지.
>
> **검증 상태(2026-07-01)**: 34-에이전트 리서치 → 적대검증 → 집필 → 비평(A−) → P0/P1 정정 → adversarial 재검증(6/6 RESOLVED). 컷오프(2026-01) 이후 콘솔/API는 각 문서 ⚠ 플래그대로 **사용 전 재확인**.

## Key Features

### 2026 SEO Standards
- E-E-A-T (Experience, Expertise, Authoritativeness, Trustworthiness)
- Core Web Vitals with INP (LCP, INP, CLS equal weight)
- Semantic keyword clustering over single-keyword optimization

### GEO (Generative Engine Optimization)
- ChatGPT, Perplexity, Gemini, Claude citation optimization
- llms.txt generation — ⚠ **저ROI · 선택**: Google은 미사용을 명시. 필수 아님, 실험으로만 취급 (SSOT `GEO_AEO_AI_ENGINES_2026`)
- robots.txt AI 크롤러 설정 — ⚠ 봇 목록/수치의 SSOT = `ROBOTS_SITEMAP_RSS_TEMPLATES §A` (여기 "11 bots"는 참고치, §A 실목록 우선). 인용봇(OAI-SearchBot/PerplexityBot/Claude-SearchBot) 허용 · 학습봇(GPTBot/ClaudeBot)은 별도 사업 토글 · Bytespider는 WAF 차단
- Quick Answer Blocks for AI Overview targeting

### AI Penalty Avoidance
- Quality Harness gate — v3에서 `CONTENT_AUTORULES` 마스터 게이트(규칙 SSOT)의 [AUTO] 실행기로 재정의(구 24항목 리스트 대체; 파생 런타임 카드 `docs/GATE_CARD.md` **생성됨(2026-07-02)**). 최종 무결성 판정은 `slop-judge` [JUDGE](author≠judge)
- AI expression pattern detection and removal
- E-E-A-T signal injection verification
- Author attribution enforcement

### Production Guardrails Learned From Korvia 2026
- Single verification gate: `lint + seo audit + build`
- Canonical coverage as a non-negotiable release condition
- Reciprocal `hreflang` only for real bilingual page pairs
- `308` consolidation + `410 Gone` pruning for index hygiene
- `Last Verified` + `FAQPage` + contextual CTA for evergreen guides
- Hub-and-spoke internal linking based on the user journey, not menu depth

### Google Ads (SEM)
- Signal-based automation architecture
- Smart Bidding + Broad Match + RSA strategy
- Phased bidding roadmap
- Negative keyword management

### CLS + Performance (v2 addition)
- 7-pattern CLS detector with fix code
- Swiper + next/image correct pattern
- GTM lazyOnload migration from @next/third-parties
- Cloudflare R2 Cache Rule (Edge + Browser TTL override)
- Image delivery optimization (remove `unoptimized` on CDN)

### Security (v2 addition)
- HSTS + COOP + CORP headers
- CSP Report-Only → enforcing migration path
- 3rd-party allowlist patterns (Clarity/GA/Bing UET/Ads)

## File Structure

```
.claude/
  skills/
    seo-harness-team/
      SKILL.md
      resources/
        quality-gate-checklist.md
        llms-txt-template.md
        robots-txt-ai-patch.txt
        schema-templates/
          article.json
          faqpage.json
          organization.json
          localbusiness.json
  agents/
    seo-researcher.md
    sem-strategist.md
    content-writer.md
    technical-seo.md
    geo-optimizer.md
    quality-harness.md
docs/                            # NEW in v2
  WEB_PLAYBOOK.md                # 16 categories
  NEW_PAGE_CHECKLIST.md          # 17-point checklist
  CLS_SEVEN_SOURCES.md           # CLS detection + fixes
  NEXT_IMAGE_SWIPER.md           # Swiper pattern
  CRUX_LIGHTHOUSE_INTERPRETATION.md  # Metric decoding
  CSP_ALLOWLIST.md               # 3rd-party fan-out
  3LAYER_AGENT_PIPELINE.md       # Bulk orchestration
  AEO_GEO_PLAYBOOK_2026.md       # ⚠ LEGACY (전략 why)
  # + 2026-07 멀티엔진 KB 12편(위 'Multi-Engine' 표) + 파생 카드 GATE_CARD.md·STALE_REGISTER.md(생성됨 2026-07-02)
```

## Changelog

### v3 (2026-07)
Multi-engine indexing KB 교정 웨이브 + 스킬 배선(read-path). SSOT=`reports/2026-07-01-multi-engine-seo-kb/` + `reports/2026-07-02-seo-skill-critique-and-site-replan/`.
- **멀티엔진 KB 교정 웨이브** — 34-에이전트 리서치 산출물(12편 지식팩)에 adversarial 재검증으로 도출한 P0/P1 정정을 반영: Daum≠Bing 백엔드(자체 Daumoa)·**Daum IndexNow 미참여**(webmaster.daum.net 별도 제출)·FAQPage/HowTo 리치결과 2026 폐지(온페이지 파싱 타깃만)·llms.txt 저ROI·robots 하우스 기본=학습봇 허용 + Bytespider만 WAF 차단.
- **발행 게이트 = `CONTENT_AUTORULES` 마스터 게이트(규칙 SSOT)** — 승인 주체 아님: [AUTO] 실행 = Quality Harness · [JUDGE] 판정 = `slop-judge`(author≠judge). 파생 런타임 카드 `docs/GATE_CARD.md`(각 문서 §0 블록의 중복제거 합본 = 티어-1 발행 카드, ≤~900토큰) **생성됨(2026-07-02)**.
- **read-path 배선** — 46,700단어 KB를 훅이 아닌 **에이전트 read-path**로 실행면에 연결(정책상 UserPromptSubmit 슬롯은 mindvault→telegram 점유). content-writer/technical-seo가 `CONTENT_AUTORULES` 마스터 게이트(→ 파생 시 GATE_CARD) → 엔진별 §0 → (게이트 실패 시)전문 순으로 티어드 로드. 새 명령 `/gate`·`/multi-engine-submit`(SKILL.md).
- **측정 3계층** — GSC 생성형AI 리포트 + Bing AI Performance + Naver 사이트진단 + 서버로그 AI봇 히트를 규칙→측정→개선 루프로(owner + 데이터스토어). 정적 KB에서 폐루프 제품으로의 전환 첫 단계.
- **레거시 3문서 = KEEP + SUPERSEDED(부분) 배지** — `WEB_PLAYBOOK`(Next.js 구현부)·`NEW_PAGE_CHECKLIST`(프리커밋 게이트)·`AEO_GEO_PLAYBOOK_2026`(전략 why)는 고유가치로 보존하되, robots/스키마/llms.txt/GEO 수치 등 중복 사실은 SSOT 문서가 우선(각 문서 상단 배지 참조).

### v2 (2026-04-18)
Production-proven additions from www.korvia.com 2-day sweep (21+ commits):
- Added 7 practical docs distilled from real debugging sessions
- Documented 6 specific gotchas that only surface in production (env trim, Swiper styled-jsx SSR, CF Cache Browser TTL override, CLS 0.89 culprit)
- Orchestration: 3-layer pipeline pattern formalized
- Playbook: 16 categories replacing earlier 3-agent scope
- Metrics interpretation: CrUX vs Lighthouse clear separation

### v1 (2026-04-15)
Initial 6-agent SEO team with 24-item quality gate.

## Korvia 2026 Operational Learnings

These patterns were validated on the live `www.korvia.com` rollout and are now
recommended defaults for this skill:

1. **Use a single release gate**
   - A site is not “SEO clean” unless lint, metadata guardrails, and production build all pass together.
   - Recommended pattern: `npm run verify -> lint + seo audit + build`.

2. **Treat canonicals as release blockers**
   - Every public indexable page should emit a canonical URL.
   - Preview, QA, or intentionally noindex routes should be excluded from failure scope instead of polluting the audit.

3. **Do not fake `hreflang`**
   - Only emit reciprocal `en/ko/x-default` when the counterpart page truly exists and matches intent.
   - If a Korean page does not exist, prefer `en + x-default` rather than weak translation mapping.

4. **Favor index hygiene over page-count vanity**
   - Consolidate duplicate legacy URLs with `308` redirects.
   - Remove thin or obsolete operational pages with `410 Gone`.
   - Exclude retired URLs from sitemap output at the same time.

5. **Upgrade evergreen content with trust and next-step signals**
   - Add `Last Verified` to sensitive procedural guides.
   - Add `FAQPage` JSON-LD where the questions are truly represented in the UI.
   - Add contextual CTAs and related-guide links based on the user’s real next step.

6. **Build topical authority as a workflow**
   - Connect pages by user timeline: arrival -> immigration -> ARC -> banking -> mobile.
   - This performs better than isolated “related links” blocks because both users and search engines read the sequence as intent-consistent.

## Requirements

- Claude Code >= 1.0
- Tools: WebFetch, WebSearch, Bash, Edit, Write, Read, Glob, Grep

## License

MIT
