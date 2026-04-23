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
