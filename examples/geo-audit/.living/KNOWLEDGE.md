---
last_updated: 2026-07-04
review_interval_days: 14
spec: living-skills/1.0
---

# GEO Knowledge Base

> ⚠️ **Illustrative seed content.** These entries show the *shape* of a knowledge base, not verified current facts. Run the Refresh Protocol once before relying on this skill — that is exactly what the protocol is for.

## AI crawlers

Known crawler user agents to check in robots.txt and server logs (verify current list against primary docs on each refresh):

- OpenAI: `GPTBot` (training), `OAI-SearchBot` (search), `ChatGPT-User` (on-demand fetch)
- Anthropic: `ClaudeBot`, `Claude-User`
- Perplexity: `PerplexityBot`, `Perplexity-User`
- Google: `Google-Extended` (AI training opt-out control); AI Mode/Overviews draw on standard Googlebot indexing

Rule: blocking a search/fetch agent removes the site from that engine's answers; blocking only the training agent does not.

## Rendering behavior

Working assumption: most AI crawlers do not reliably execute JavaScript; content absent from raw HTML should be treated as invisible to them. Google's pipeline renders; treat everything else as HTML-only until a primary source says otherwise. Verify each refresh — this is among the fastest-moving facts in the field.

## Site conventions

- `llms.txt` (llmstxt.org proposal): curated markdown index for LLMs at site root. Adoption by engines remains inconsistent — treat as low-cost/uncertain-benefit; verify current engine support each refresh.
- Structured data: JSON-LD (schema.org) for Organization, Article/Product, FAQPage as applicable; markup must match visible content.
- Standard hygiene still applies: clean sitemaps, canonical tags, fast responses to bot traffic.

## Citation patterns

Current working model of what answer engines preferentially cite:

- Self-contained passages that answer a question in the first 1–3 sentences under a descriptive heading
- Pages with visible dates and recent updates
- Content with verifiable specifics (numbers, names, sources) over generic prose
- Sources already ranking in traditional search (retrieval frequently piggybacks on search indices)

## Thresholds & misc

- Bot response time: keep under ~1s TTFB for crawler user agents; some fetch-on-demand agents time out aggressively.
- Paywalled/gated content: invisible to most engines unless a specific bypass agreement exists.
