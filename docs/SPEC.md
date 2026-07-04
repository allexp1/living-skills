# Living Sidecar Specification v1.1

The contract a skill + sidecar must satisfy. v1.1 replaces v1.0's in-place model with the sidecar model: **the original skill is immutable.**

## 1. Layout

```
<skill>/
├── SKILL.md                 ← original, immutable
└── .living/
    ├── PROTOCOL.md          ← sidecar operating rules
    ├── KNOWLEDGE.md         ← mutable beliefs
    ├── sources.md           ← trusted sources (+ optional UPSTREAM)
    ├── CHANGELOG.md         ← append-only provenance log
    └── ORIGINAL.sha256      ← sha256 of SKILL.md at install time
```

| File | Mutability | Editor |
|---|---|---|
| `SKILL.md` | **Immutable** | Skill author only |
| `.living/PROTOCOL.md` | Stable | Human |
| `.living/sources.md` | Stable | Human |
| `.living/KNOWLEDGE.md` | Mutable | Refresh Protocol |
| `.living/CHANGELOG.md` | Append-only | Refresh Protocol |
| `.living/ORIGINAL.sha256` | Re-baselined | Human-approved only |

## 2. Invariants

- **K0 (do no harm):** the sidecar system never writes to the original SKILL.md or anything outside `.living/`. Integrity is hash-verified on every invocation; a mismatch is reported to the user (offer: re-baseline or re-review), never silently accepted.
- **K1 (constitutional limit):** within `.living/`, the Refresh Protocol writes only KNOWLEDGE.md and CHANGELOG.md.
- **K2:** a completed refresh always bumps `last_updated`, even with no belief changes.
- **K3 (provenance):** every ADDED/CHANGED/REMOVED changelog line carries a source URL. No citation, no change.
- **K4 (honest staleness):** if refresh is skipped, output states the knowledge age.
- **K5 (upstream consent):** if sources.md declares an UPSTREAM and a newer vendor version is detected, the system notifies and diffs — it never auto-applies. Adopting upstream is a human action.
- **K6 (advisory floor):** skills in legal, health, finance, or safety domains run in `advisory` mode. `override` is forbidden for them.

## 3. Modes

Declared in KNOWLEDGE.md frontmatter (`mode:`).

- **advisory** (default): KNOWLEDGE.md informs the work; on conflict with SKILL.md content, the conflict is surfaced to the user with date and source, and the user decides. The machine never silently substitutes its research for the author's judgment.
- **override** (opt-in, owner's skills only): on conflict, KNOWLEDGE.md wins as the newer fact; the override is noted in output.

## 4. KNOWLEDGE.md frontmatter

```yaml
---
last_updated: YYYY-MM-DD
review_interval_days: <int>
mode: advisory | override
spec: living-skills/1.1
---
```

Body: granular `##` topic sections, so refresh rewrites surgically.

## 5. sources.md entries

```markdown
## <Source name>
- URL / search topic: <...>
- Tier: PRIMARY | SECONDARY
- Check for: <one line>
```

PRIMARY (official docs/specs/changelogs/first-party announcements) → may update beliefs directly. SECONDARY (blogs/community/third-party) → logged UNCONFIRMED until primary-corroborated.

Optional, for vendor-published skills:

```markdown
## UPSTREAM
- Repo/URL: <vendor location>
- Installed version/commit: <id>
```

Truth hierarchy: **upstream skill version > PRIMARY sources > SECONDARY sources** — but per K5, acting on upstream requires human approval.

## 6. Changelog entries

```markdown
## YYYY-MM-DD
- ADDED/CHANGED/REMOVED: <belief> — <rationale> — <source URL>
- UNCONFIRMED: <claim> — awaiting primary confirmation — <source URL>
- No changes found
```

## 7. Invocation sequence (PROTOCOL.md must implement)

1. Integrity check (K0) → 2. Upstream check if declared (K5) → 3. Staleness gate → refresh if stale (K1–K3) → 4. Perform task using KNOWLEDGE.md per mode (K6) → 5. State knowledge age if refresh skipped (K4).

## 8. The global hook

Because SKILL.md is immutable, it cannot reference its sidecar. Installation therefore includes one consented paragraph in the user's global memory (CLAUDE.md or equivalent) instructing the agent to check any invoked skill's directory for `.living/` and follow its PROTOCOL.md. Without the hook, sidecars operate only on explicit request.

## 9. Eligibility & intervals

Attach sidecars only to knowledge-bearing skills (external time-sensitive facts). Purely procedural skills get none. Intervals: 7–14 days (AI platforms, GEO/SEO, young APIs), 14–30 (mature ecosystems), 30–60 (specs, regulation).

## 10. Uninstall

`rm -rf .living/` restores the skill to its exact shipped state — provably, via the hash. A correct implementation leaves no other trace.
