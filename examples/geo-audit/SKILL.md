---
name: geo-audit
description: Audit a website's readiness for Generative Engine Optimization (GEO) — how well it can be discovered, crawled, understood, and cited by AI answer engines (ChatGPT, Perplexity, Google AI Mode, Claude, etc.). Use whenever the user asks about GEO, AI SEO, llms.txt, AI crawler access, "will AI engines cite my site", or wants a site checked for AI visibility.
---

# GEO Audit

> **Worked example, sidecar mode.** This SKILL.md plays the role of an original, third-party skill: Living Skills never modifies it. All evolution machinery lives in the `.living/` sidecar next to it, consulted via the global hook. Delete `.living/` and this skill is exactly what its author shipped.

## Audit methodology

Work through five layers in order. For each check record: status (pass / partial / fail / n-a), evidence, and recommendation.

### Layer 1 — Access
Can AI crawlers reach the content? Check robots.txt directives against known AI crawler user agents, CDN/WAF bot rules, and status codes returned to those agents.

### Layer 2 — Renderability
Is the content present in raw HTML without JavaScript execution? Most AI crawlers do not execute JS. Compare raw HTML to rendered DOM for key pages.

### Layer 3 — Machine legibility
Structured data (JSON-LD / schema.org), semantic HTML, llms.txt. Verify presence, validity, and match between markup and visible content.

### Layer 4 — Citability
Is content shaped the way answer engines extract and cite? Answerable headings, self-contained passages, visible dates, verifiable specifics, authority signals.

### Layer 5 — Verification
Where feasible, query the major answer engines for topics the site should own and record whether it is cited.

## Report format

1. **Scorecard** — one line per layer: status + single most impactful fix.
2. **Findings** — per check: evidence + recommendation.
3. **Prioritized fix list** — ordered by impact × ease. Max 10 items.
