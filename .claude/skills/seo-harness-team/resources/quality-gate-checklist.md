# Quality Gate Checklist — Quick Reference

Use this checklist for rapid quality verification of any SEO content.

## SEO Quality (8 items)

- [ ] **Title Tag**: 50-60 chars, Primary KW front-loaded
- [ ] **Meta Description**: 150-160 chars, includes CTA
- [ ] **H1**: Exactly one, Primary KW naturally included
- [ ] **H2-H6**: Logical hierarchy, no skipped levels
- [ ] **Primary KW placement**: title + first paragraph + last paragraph
- [ ] **Semantic LSI keywords**: naturally distributed throughout
- [ ] **Internal links**: 2+ naturally placed
- [ ] **Image alt text**: Descriptive, keyword-relevant

## E-E-A-T Check (4 items)

- [ ] **Experience**: First-person signals, real cases, specific numbers
- [ ] **Expertise**: Industry terminology, accurate data points
- [ ] **Author Attribution**: Author name, bio, Schema markup
- [ ] **Source Citation**: 1+ external authority links per major claim

## AI Humanization (5 items)

- [ ] **AI patterns removed**: No cliche AI phrases
- [ ] **Clear position**: No fence-sitting without resolution
- [ ] **Unique insight**: 1+ points not in competitor content
- [ ] **Intent match**: Directly answers user's actual question
- [ ] **Natural rhythm**: Varied sentence lengths

## Technical SEO (3 items)

- [ ] **Schema Markup**: Valid JSON-LD
- [ ] **Core Web Vitals**: No LCP/INP/CLS regressions
- [ ] **Mobile friendly**: Responsive, readable

## Production Guardrails (5 items)

- [ ] **Canonical coverage**: Every scoped public page has a canonical
- [ ] **Truthful hreflang**: Reciprocal alternates only where real language pairs exist
- [ ] **Index hygiene**: Duplicate or obsolete URLs are routed via `308` or `410` and removed from sitemap scope
- [ ] **Verification gate**: Project has a repeatable lint + SEO audit + build command
- [ ] **External dependency clarity**: Final report distinguishes local pass status from Google recrawl lag

## GEO (4 items)

- [ ] **Quick Answer Block**: 40-80 words at page top
- [ ] **FAQ section**: Schema or structured Q&A
- [ ] **llms.txt**: Updated for new/changed pages
- [ ] **robots.txt**: AI crawlers allowed

---

**Scoring**: 29/29 = PASS | 25-28 = Minor fixes | 21-24 = Significant revision | <21 = Reject
