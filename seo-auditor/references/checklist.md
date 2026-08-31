# SEO Audit Checklist & Scoring Rubric

Use this alongside SKILL.md. Each category is scored 0–20; sum for an overall score out of 100.

## 1. Technical (weight: 20)

| Check | Pass | Fail | Warning |
|---|---|---|---|
| `<title>` present, unique per page | Present & unique | Missing or duplicated across pages | Present but generic/templated with no per-page variation |
| Meta description present | 120–160 chars, unique | Missing or duplicated | Present but too short/long or truncated |
| Canonical tag | Present, self-referencing or correctly pointed | Missing on indexable page | Present but pointing to wrong/inconsistent URL |
| Robots meta / `X-Robots-Tag` | Absent (indexable) or intentionally set and consistent with intent | Accidentally `noindex` on a page meant to rank | Inconsistent between code and live output (hybrid mode) |
| `robots.txt` | Present, valid, doesn't block important paths | Blocks pages that should be indexed, or 404s | Overly permissive/blocks nothing when it should scope crawl budget |
| `sitemap.xml` | Present, valid XML, referenced in robots.txt | Missing entirely on a multi-page site | Present but stale/missing new pages |
| Structured data (JSON-LD) | Valid, relevant schema type for content | Invalid JSON or wrong schema type | Present but incomplete (missing recommended fields) |
| URL structure | Clean, descriptive, lowercase, hyphenated | Contains session IDs, excessive params, or non-descriptive IDs | Overly long or inconsistent casing |
| HTTPS | Enforced site-wide with redirect from HTTP | Mixed content or HTTP accessible without redirect | HTTPS present but redirect chain is long (3xx hops) |

## 2. Content (weight: 20)

| Check | Pass | Fail | Warning |
|---|---|---|---|
| Title tag quality | Descriptive, includes primary topic, ~50–60 chars | Missing/keyword-stuffed/duplicated | Too generic or slightly over/under length |
| Heading hierarchy | Single H1, logical H2/H3 nesting | No H1 or multiple competing H1s, skipped levels | H1 present but doesn't reflect page topic |
| Keyword usage | Natural placement in title/H1/intro/body | Stuffing or complete absence of topical terms | Present but not in high-value positions (title/H1) |
| Content depth | Substantive, answers likely search intent | Thin content (e.g. a few sentences) with no clear value | Adequate but thinner than competing/similar pages on the site |
| Duplicate content | Each page's core content is unique | Near-identical content across multiple pages/URLs | Boilerplate dominates page vs. unique content |

## 3. Performance signals (weight: 20 — see limitations note in SKILL.md)

| Check | Pass | Fail | Warning |
|---|---|---|---|
| Image sizing | `width`/`height` set, modern format (webp/avif) | Large unoptimized images with no dimensions | Correct format but no explicit dimensions (CLS risk) |
| Lazy loading | Below-fold images/iframes use `loading="lazy"` | No lazy-loading on a long, image-heavy page | Partial — applied inconsistently |
| Render-blocking resources | Minimal blocking CSS/JS in `<head>` | Large synchronous scripts/stylesheets blocking render | Some blocking resources but deferred/async where it matters most |
| DOM size | Reasonable node count for page complexity | Excessively deep/wide DOM (thousands of nodes) | Larger than ideal but not extreme |
| Font loading | `font-display: swap` or equivalent, preloaded critical fonts | No font-display strategy, causing FOIT | Preloaded but no swap strategy |
| Real Core Web Vitals (LCP/INP/CLS) | N/A — mark **Not verifiable from available data**; recommend Lighthouse/PSI | — | — |

## 4. Links (weight: 20)

| Check | Pass | Fail | Warning |
|---|---|---|---|
| Internal linking | Relevant pages interlinked, reasonable link count | Orphan pages with no inbound internal links | Sparse internal linking |
| Anchor text | Descriptive, topical | Generic ("click here") on important links | Mixed — some descriptive, some generic |
| Broken links | All checked links resolve | Internal links returning 404s | Some external links unverifiable/timeout |
| Outbound link hygiene | `rel="nofollow"`/`sponsored`/`ugc"` used appropriately | Paid/sponsored links without proper `rel` | Inconsistent application across similar link types |

## 5. Mobile/Accessibility (weight: 20)

| Check | Pass | Fail | Warning |
|---|---|---|---|
| Viewport meta tag | `width=device-width, initial-scale=1` present | Missing entirely | Present but restricts zoom (`user-scalable=no`) |
| Image alt text | Descriptive alt on content images, empty alt on decorative | Missing alt on meaningful images | Alt present but non-descriptive (e.g. filename) |
| Semantic HTML | Proper landmarks (`nav`, `main`, `header`, `footer`), form labels tied to inputs | Div-soup with no semantic structure | Partial semantic use |
| Heading/DOM order matches visual order | Consistent | Visually reordered via CSS in a way that breaks reading order | Minor mismatches |
| Tap targets / color contrast | N/A — mark **Not verifiable from available data** without rendering; recommend a Lighthouse accessibility pass | — | — |

## Scoring

- Per category: start at 20, subtract 4 per **Fail**, subtract 2 per **Warning**, floor at 0. Items marked **Not verifiable** are excluded from the denominator (don't penalize for what can't be checked) but must still be listed in the report.
- Overall score = sum of the five category scores (0–100).
- Suggested grade bands: 90–100 Excellent · 75–89 Good · 60–74 Needs work · Below 60 Significant issues.

## Impact / Effort labeling (for the prioritized list)

- **Impact**: High = likely blocking indexing/ranking entirely (noindex accident, missing title, broken canonical) or affects every page (template-level). Medium = meaningfully suboptimal but not blocking (weak meta description, thin content). Low = polish-level (minor anchor text wording).
- **Effort**: Quick fix = single attribute/tag/config change. Moderate = content rewrite or component-level change. Involved = architectural (e.g. restructuring routing, adding a sitemap generator, introducing lazy-loading infra).
- Sort the prioritized list by **High impact + Quick/Moderate effort first**, then remaining High impact, then Medium, then Low.
