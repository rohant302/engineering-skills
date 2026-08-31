---
name: seo-auditor
description: Run a comprehensive SEO audit covering technical SEO, on-page content, performance signals, internal/external links, and mobile/accessibility basics. Works on a live URL (or list of URLs/sitemap) via web fetching, OR on local source files (Next.js/React pages, HTML, metadata config, robots.txt, sitemap files) without needing the internet, OR both combined. Produces a full findings report plus a prioritized, impact-ranked fix list. Use this whenever the user asks to "audit SEO", "check SEO", "review this page/site for SEO", "why isn't this ranking", "SEO checklist", "improve search rankings", or shares a URL/codebase and asks for an SEO review — even if they don't use the word "audit" explicitly.
---

# SEO Auditor

Run a structured, honest SEO audit across five categories — Technical, Content, Performance, Links, and Mobile/Accessibility — and produce both a full report and a prioritized action list.

## Step 1: Determine mode and scope

**Mode** (what you're auditing):
- **Live mode** — user gives one or more URLs. Fetch them with `web_fetch`. Also try fetching `/robots.txt` and `/sitemap.xml` at the site root — don't guess their contents.
- **Local mode** — user gives/points to a local codebase (e.g. a Next.js `app/` or `pages/` dir, static HTML, a metadata config file). Read the relevant files directly instead of fetching.
- **Hybrid** — both are available; cross-check what the code *intends* to output against what the live page *actually* serves (e.g. a `metadata` export in Next.js vs. the rendered `<head>`), and flag any mismatch as its own finding.

**Scope**:
- **Single page** — default when one URL or one route/file is given.
- **Multi-page/site** — when given a list of URLs, a sitemap, or a whole routes directory. Audit each page individually against the full checklist, then roll up into a site-level summary that highlights issues repeated across pages (these are usually template-level bugs, not one-off content problems) separately from page-specific issues.

If scope/mode is ambiguous (e.g. just a domain with no path), ask once, briefly, rather than guessing.

## Step 2: Gather data

- Live: fetch the page's rendered HTML via `web_fetch`. Note that `web_fetch` returns what's fetchable server-side — client-side-rendered content that only appears after JS execution may not be visible this way; if the page appears mostly empty/app-shell, say so explicitly rather than scoring it as missing content.
- Local: read the actual template/page files, metadata config (e.g. `generateMetadata`, `next-seo` config, `<Head>` usage), `public/robots.txt`, sitemap generation code, and any structured-data (JSON-LD) source.
- For multi-page audits, gather all pages before scoring, so cross-page checks (duplicate titles/descriptions, duplicate content, orphan pages) are possible.

## Step 3: Run the checklist

Work through each category using the detailed check items in `references/checklist.md`. For every check item, record one of: **Pass**, **Fail**, **Warning** (present but suboptimal), or **Not verifiable from available data** (be explicit about this last one — don't guess).

The five categories:
1. **Technical** — meta tags, canonical, robots directives, sitemap, structured data, indexability, URL structure, HTTPS.
2. **Content** — title/meta description quality and length, heading hierarchy, keyword usage and placement, content depth, duplicate content across pages.
3. **Performance signals** — proxies Claude *can* actually check from source/HTML (render-blocking resources, unoptimized/oversized images, missing lazy-loading, excessive DOM size, font-loading strategy) — see the "Performance limitations" note below before scoring this category.
4. **Links** — internal linking structure, descriptive vs. generic anchor text, orphan pages (in multi-page audits), broken internal links if verifiable, external link hygiene (`rel` attributes on outbound/sponsored/UGC links).
5. **Mobile/Accessibility** — viewport meta tag, image `alt` text, semantic HTML/landmark usage, heading order, form label association. Flag (don't fake-score) anything needing actual rendering, like visual tap-target sizing or color contrast.

### Performance limitations — be honest

Claude cannot render pages or measure real Core Web Vitals (LCP, INP, CLS) from fetched HTML alone. Score only what's genuinely inferable from source (e.g. an unoptimized multi-MB `<img>` with no `width`/`height`/`loading` attribute is a legitimate finding). For anything requiring real field/lab data, mark it **Not verifiable from available data** and recommend the user run Lighthouse or the PageSpeed Insights API for authoritative numbers — never fabricate a score or metric.

## Step 4: Score and prioritize

Use the scoring rubric in `references/checklist.md` to compute a per-category score and an overall score out of 100. For every **Fail** or **Warning**, assign an impact (High/Medium/Low) and rough effort (Quick fix/Moderate/Involved) per the guidance in that reference file — this drives the prioritized list in Step 5.

## Step 5: Deliver two outputs

1. **Prioritized summary (in chat, first)** — a short ranked list of the highest-impact, lowest-effort fixes first, then everything else grouped by impact. This is what the user reads first; keep it scannable.
2. **Full report (as a file)** — the complete category-by-category findings, scores, and every check item's status. This is long-form reference content the user will want to save/share, so write it as an actual Markdown file to the outputs directory (per standard file-creation practice) rather than dumping the whole thing into chat. Use clear per-category headers, a summary table of scores at the top, and the prioritized fix list repeated at the end for convenience.

For multi-page audits, the file should include a site-level rollup (recurring/template issues) before the per-page detail sections.

## Guardrails

- Never fabricate data you can't actually observe — a "Not verifiable" status is always better than an invented score or number.
- Don't recommend keyword stuffing, cloaking, doorway pages, hidden text, link schemes, or any other search-engine-guideline-violating tactic, even if it might work short-term.
- Distinguish clearly between what's broken (Fail), what's suboptimal (Warning), and what's just unknown from available data — conflating these erodes trust in the whole report.
- For local-mode audits, don't assume a build/deploy step already ran — check source/config, not a live rendered version, unless the user also gave you the live URL.

## Reference

See `references/checklist.md` for the full per-category check-item list, what "Pass" vs "Fail" vs "Warning" means for each item, the scoring rubric/weights, and the impact/effort labeling guide used to build the prioritized list.
