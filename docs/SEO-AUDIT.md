# docs.masker.dev — SEO + GEO Audit (May 2026)

This audit covers the homepage at `/` (currently `get-started/introduction.mdx`) and site‑wide technical SEO. It documents exact changes applied in this PR and the rationale.

## Summary of findings

| Area | Before | After |
| --- | --- | --- |
| Homepage `<title>` | "Welcome to Masker.dev" (generic, not keyword-rich) | "Masker.dev Documentation — Real-time PHI Redaction for Voice AI Agents" |
| Homepage meta description | "The privacy layer that makes voice AI safe for healthcare." | Keyword-rich 155‑char description targeting Vapi / Retell / HIPAA |
| Hero | Card-only landing, no quick-start code | BLUF + "One URL change for Vapi" code snippet above the fold |
| JSON-LD | None | `SoftwareApplication`, `TechArticle`, `HowTo`, `FAQPage` on relevant pages |
| `llms.txt` / `humans.txt` | Missing | Added at site root |
| `robots.txt` / `sitemap.xml` | Mintlify default only | Custom `robots.txt`, plus an explicit sitemap stub (Mintlify also serves one at `/sitemap.xml` automatically) |
| Canonical URLs | Implicit | Configured site-wide via `metatags` in `mint.json` |
| OG / Twitter cards | Inconsistent | Site-wide defaults via `metatags`, page-level overrides on pillar pages |
| Internal linking to `masker.dev` | Sporadic | "Back to masker.dev" in topbar + per-page "Related on masker.dev" footers |
| Topical authority | Strong on internals, weak on integrations | New `/integrations/vapi`, `/integrations/retell`, `/guides/hipaa-voice-agents` pillar content |
| FAQ blocks | None | FAQPage schema on Vapi, Retell, HIPAA guide |
| Last-updated dates / E-E-A-T | None | Per-page `date` + author attribution (Masker.dev Engineering, Navendra Jha) |
| Breadcrumbs | None | Mintlify auto-renders breadcrumbs from `mint.json` navigation; canonical groups updated to align |

## Exact homepage meta + schema changes

### Frontmatter (applied)

```yaml
---
title: "Masker.dev Documentation — Real-time PHI Redaction for Voice AI Agents"
sidebarTitle: "Welcome"
description: "Official docs for Masker: self-hosted HIPAA PHI masking, Vapi and Retell integrations, API reference, streaming voice examples, and HIPAA compliance guides."
"og:title": "Masker.dev Documentation — Real-time PHI Redaction for Voice AI"
"og:description": "Self-hosted HIPAA PHI masking for Vapi and Retell. One URL change. Per-session signed audit reports."
"og:type": "website"
"twitter:card": "summary_large_image"
keywords: "Masker Vapi integration, Masker Retell HIPAA, real-time PHI redaction, HIPAA voice agent, PHI masking self-hosted, voice AI HIPAA, healthcare voice agent"
---
```

### JSON-LD (applied as raw `<script type="application/ld+json">` blocks via Mintlify MDX)

- `SoftwareApplication` on the homepage describing Masker.
- `TechArticle` on the HIPAA guide (`/guides/hipaa-voice-agents`).
- `HowTo` on `/integrations/vapi` and `/integrations/retell`.
- `FAQPage` on integration tutorials and the HIPAA guide.

Each block lives at the bottom of the page inside an MDX `export const` to keep Mintlify happy and to keep visual layout untouched.

## Site-wide technical SEO

Implemented in `docs/public/mint.json`:

- `metadata.metatags` block sets default OG, Twitter, theme-color, and `format-detection`.
- `seo.indexHiddenPages = false` (Mintlify default) — confirmed.
- New navigation tabs: **Integrations** and **Guides** so the new pillar pages live in the sidebar.
- Topbar link "Back to masker.dev" added.
- `footerSocials` extended with a YouTube/X handle placeholder (kept commented).

Files added:

- `docs/public/llms.txt`
- `docs/public/humans.txt`
- `docs/public/robots.txt`
- `docs/public/sitemap.xml` (manual; Mintlify also serves `/sitemap.xml` — keep both, this one is canonical for the SEO team to submit).

## Content & on-page SEO checklist applied

- One H1 per page (Mintlify renders the `title` frontmatter as H1).
- Keyword-rich H2/H3 on every new page.
- "Last updated" timestamps in page bodies (Mintlify renders from frontmatter `date`).
- E-E-A-T author attribution: a small "Written by Masker.dev Engineering" block at the bottom of every pillar page, linking to the team page on `masker.dev`.
- Citations to HIPAA Privacy Rule (`45 CFR §164.514(b)(2)`) on compliance pages.
- Internal linking: every integration page links to `masker.dev/integrations` (placeholder) and the blog pillar.
- "Next steps" CardGroup at the bottom of every pillar page.

## Generative Engine Optimization (GEO) tactics applied

- **BLUF** (Bottom Line Up Front) — every pillar page starts with a 1–3 sentence answer to the most likely AI search query, before any setup or background.
- **Exhaustive, citable content** — Safe Harbor identifier table (already present), before/after masking diff blocks (added on Vapi page), latency benchmark table (HIPAA guide).
- **Structured data** — HowTo schema on integrations, FAQPage on guides.
- **Conversational headings** — H2s phrased as questions (e.g., "How do I make my Vapi agent HIPAA compliant?") to match conversational AI search.
- **Tokens, code diffs, and tables** — LLMs cite high-signal, structured content. Every pillar page has at least one table and one before/after code block.

## Verification checklist after deploy

- [ ] `curl -I https://docs.masker.dev/` returns canonical link header.
- [ ] `curl https://docs.masker.dev/llms.txt` returns the file.
- [ ] `curl https://docs.masker.dev/sitemap.xml` lists every page.
- [ ] Validate JSON-LD with [Schema.org validator](https://validator.schema.org/) on the homepage, `/integrations/vapi`, `/guides/hipaa-voice-agents`.
- [ ] Submit both `masker.dev` and `docs.masker.dev` to Google Search Console.
- [ ] GA4 goal: `View docs → Request beta` (custom event when CTA clicked).
