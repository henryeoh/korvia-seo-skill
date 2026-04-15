# korvia-seo-skill

SEO Harness Team — Claude Code Skill for comprehensive SEO/SEM/GEO optimization.

## Overview

A 6-agent SEO expert team that takes a URL and automatically runs:
- Business analysis and audience profiling
- Keyword research and competitor gap analysis
- Google Ads campaign blueprint design
- E-E-A-T optimized content rewriting
- Technical SEO audit (meta tags, Schema, Core Web Vitals)
- Generative Engine Optimization (AI search citation)
- 24-item quality gate with AI penalty avoidance

## Architecture

```
[URL Input]
    |
    v
INTAKE AGENT (Orchestrator)
    |
    +-- RESEARCHER AGENT        /keyword-research, /competitor-analysis
    +-- SEM STRATEGIST AGENT    /sem-strategy
    +-- CONTENT WRITER AGENT    /write-page
    +-- TECHNICAL SEO AGENT     /seo-audit, /meta-optimize, /schema-generate
    +-- GEO OPTIMIZER AGENT     /geo-optimize
    +-- QUALITY HARNESS AGENT   /harness-check (24-item gate)
```

## Quick Start

```bash
# Clone the skill repo
git clone https://github.com/henryeoh/korvia-seo-skill.git

# Copy skill files to your project's .claude directory
cp -r korvia-seo-skill/.claude/skills/seo-harness-team /path/to/project/.claude/skills/
cp -r korvia-seo-skill/.claude/agents /path/to/project/.claude/

# In Claude Code, run:
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

## Key Features

### 2026 SEO Standards
- E-E-A-T (Experience, Expertise, Authoritativeness, Trustworthiness)
- Core Web Vitals with INP (LCP, INP, CLS equal weight)
- Semantic keyword clustering over single-keyword optimization

### GEO (Generative Engine Optimization)
- ChatGPT, Perplexity, Gemini, Claude citation optimization
- llms.txt file generation for AI crawler discovery
- robots.txt AI crawler configuration
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

## File Structure

```
.claude/
  skills/
    seo-harness-team/
      SKILL.md                  <- Main skill file
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
```

## Requirements

- Claude Code >= 1.0
- Tools: WebFetch, WebSearch, Bash (optional)

## License

MIT
