# korvia-seo-skill v2

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

### 🤖 `docs/AEO_GEO_PLAYBOOK_2026.md`
AI 검색 시대 상위 전략 레퍼런스 (2026-05-29 lock-in). SEO/AEO/GEO 3-레이어 정의, Princeton 정량 근거(+41% 인용문/+30% 통계/+30% 출처), 플랫폼별 인용 성향(ChatGPT Wikipedia 48% / Perplexity Reddit 47% / 중복 11%), schema+llms.txt 토대, 측정 툴링, 한국/네이버 맥락, KORVIA 적용 3-Phase 플랜. 운영 체크리스트는 `geo-optimizer.md` 참조.

### 📘 `docs/WEB_PLAYBOOK.md`
Full 16-category SEO + Performance + a11y playbook distilled from 21+ production commits. Every category has concrete code patterns, gotchas discovered, and verification steps. Covers:
- robots/sitemap/llms.txt (incl. env `.trim()` bug)
- Schema.org entity graph (Organization + WebSite + Service + FAQPage + HowTo + Event + CollectionPage)
- CLS root causes (7 patterns)
- Hydration #418 (safety nets)
- Perf (GTM lazyOnload, browserslist modern, optimizeCss, unoptimized)
- Security headers (HSTS/COOP/CORP/CSP Report-Only)
- CrUX vs Lighthouse interpretation
- Next.js gotchas (styled-jsx client-only, Tailwind v4)

### 📋 `docs/NEW_PAGE_CHECKLIST.md`
17-point checklist for every new Next.js page. Use as a pre-commit gate. Split into:
- A. Metadata (6 items)
- B. Schema.org JSON-LD (3 items)
- C. Images (4 items)
- D. CLS defense (3 items)
- E. Accessibility (1 item)
- Plus: page type-specific additional checks + validation commands

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

한국(**Naver·Daum 최우선**) + Google/Bing/Yahoo + AI 답변엔진(GEO/AEO)까지 **색인·노출 전 과정**을 다루는 12편 지식팩. 각 문서 상단 `## 0. 자동 적용 규칙(Auto-apply)` 블록 = 작성 에이전트/발행 게이트가 기계적으로 적용. 실제 콘솔 기준: 네이버 서치어드바이저 `searchadvisor.naver.com/console/board`, 다음 웹마스터도구 `webmaster.daum.net/dashboard`.

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

**SSOT 맵(중복 사실은 여기부터 고칠 것)**: 스키마 생사 = `SCHEMA_JSONLD_CATALOG` · IndexNow/AI봇 UA/robots = `ROBOTS_SITEMAP_RSS_TEMPLATES`(+ Bing 심화 `BING_YAHOO_INDEXNOW`) · CWV 임계 = `CRUX_LIGHTHOUSE_INTERPRETATION`/`CLS_SEVEN_SOURCES` · GEO 인용/llms.txt = `GEO_AEO_AI_ENGINES_2026` · 발행 게이트 = `CONTENT_AUTORULES`.

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
- llms.txt file generation for AI crawler discovery
- robots.txt AI crawler configuration (11 bots allowlisted, Bytespider blocked)
- Quick Answer Blocks for AI Overview targeting

### AI Penalty Avoidance
- 24-item Quality Harness Gate
- AI expression pattern detection and removal
- E-E-A-T signal injection verification
- Author attribution enforcement

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
```

## Changelog

### v2 (2026-04-18)
Production-proven additions from www.korvia.com 2-day sweep (21+ commits):
- Added 7 practical docs distilled from real debugging sessions
- Documented 6 specific gotchas that only surface in production (env trim, Swiper styled-jsx SSR, CF Cache Browser TTL override, CLS 0.89 culprit)
- Orchestration: 3-layer pipeline pattern formalized
- Playbook: 16 categories replacing earlier 3-agent scope
- Metrics interpretation: CrUX vs Lighthouse clear separation

### v1 (2026-04-15)
Initial 6-agent SEO team with 24-item quality gate.

## Requirements

- Claude Code >= 1.0
- Tools: WebFetch, WebSearch, Bash, Edit, Write, Read, Glob, Grep

## License

MIT
