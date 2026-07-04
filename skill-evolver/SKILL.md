---
name: skill-evolver
description: Give existing skills a self-updating knowledge sidecar so their domain facts stay current — WITHOUT modifying the original skill. Use whenever the user wants a skill to "learn", "evolve", "stay up to date", "self-update", "not go stale", or asks to make a skill "dynamic", add a knowledge base or refresh mechanism to a skill, or batch-upgrade a skills directory. Works on a single skill or a whole folder, including third-party/vendor skills.
---

# Skill Evolver

Attaches a **living sidecar** to a skill: a small `.living/` folder holding dated domain knowledge, trusted sources, a refresh protocol, and a changelog. The original SKILL.md is never modified — not one byte. Delete the sidecar and the skill is exactly what its author shipped.

```
target-skill/
├── SKILL.md                ← NEVER TOUCHED (hash-verified)
└── .living/
    ├── PROTOCOL.md         ← sidecar rules: staleness gate, refresh, conflict handling
    ├── KNOWLEDGE.md        ← current domain facts, dated frontmatter
    ├── sources.md          ← trusted sources, PRIMARY/SECONDARY tiers
    ├── CHANGELOG.md        ← append-only provenance log
    └── ORIGINAL.sha256     ← integrity hash of SKILL.md at install time
```

**Prime directive (Invariant K0):** never modify the original skill. All writes go inside `.living/`, and within it, refresh may only write KNOWLEDGE.md and CHANGELOG.md.

## Modes

- **advisory** (default; MANDATORY for legal / health / finance / safety skills): KNOWLEDGE.md informs the work, but if it contradicts something stated in SKILL.md, the conflict is surfaced to the user — never silently overridden. The author's judgment stands until a human says otherwise.
- **override** (opt-in, user's own skills only): where KNOWLEDGE.md and SKILL.md conflict, KNOWLEDGE.md wins as the newer fact, noting the override in output.

Ask the user before choosing override. When in doubt — or whenever the skill's domain could affect legal, medical, financial, or safety decisions — advisory. Not negotiable for those domains.

## Workflow

### Step 0: Global hook (first run only)
Because the original SKILL.md can't reference its sidecar, a one-time global hook is needed. Ask the user for permission to append this to their global CLAUDE.md (or equivalent memory file):

> **Living Skills hook:** Before using any skill, check its directory for a `.living/` folder. If present, read `.living/PROTOCOL.md` and follow it: verify the original's hash, check knowledge staleness, refresh if stale, and apply its conflict-handling mode.

If the user declines, sidecars still work but only when explicitly invoked ("consult the sidecar").

### Step 1: Scope & eligibility
Enumerate target skill(s). For each, classify:
- **Knowledge-bearing** (time-sensitive external facts: platform behavior, specs, rules, thresholds, pricing) → attach sidecar.
- **Purely procedural** (conventions, workflows, no external ground truth) → skip, say why.
- **Already has `.living/`** → skip, or offer to tune interval/sources.
- **High-stakes domain** (legal/health/finance/safety) → sidecar in advisory mode only; state this explicitly.

### Step 2: Build KNOWLEDGE.md
Read SKILL.md (read-only). Identify its time-sensitive claims and write a knowledge base covering the same topics with current understanding, organized in granular `##` sections. Frontmatter:

```yaml
---
last_updated: <today YYYY-MM-DD>
review_interval_days: <7-14 fast fields / 30-60 slow fields>
mode: advisory | override
spec: living-skills/1.1
---
```

Offer to run a first refresh immediately so the sidecar starts current rather than inheriting the skill's staleness.

### Step 3: Build sources.md
Trusted sources for the skill's domain, each with URL/search topic, tier (PRIMARY = official docs/specs/changelogs, apply directly; SECONDARY = blogs/community, log as UNCONFIRMED until primary-corroborated), and one line on what to check. If the skill is vendor-published, add an UPSTREAM entry:

```markdown
## UPSTREAM
- Repo/URL: <vendor skill location>
- Installed version/commit: <as found>
```

Tell the user sources.md deserves one manual review — it's the epistemic foundation.

### Step 4: Write PROTOCOL.md
The sidecar's operating rules (see examples/geo-audit/.living/PROTOCOL.md in the Living Skills repo for the template):
1. Integrity check: hash ../SKILL.md vs ORIGINAL.sha256; on mismatch, inform user and offer re-baseline or re-review. Also, if an UPSTREAM entry exists, check the vendor source for a newer skill version — if found, notify the user and offer a diff; NEVER auto-apply upstream changes (Invariant K5).
2. Staleness gate: if last_updated older than review_interval_days → refresh first (user may skip; then state knowledge age in output).
3. Use KNOWLEDGE.md for current facts.
4. Conflict handling per mode (advisory: surface to user; override: prefer KNOWLEDGE.md, note it).
5. Refresh Protocol: search sources since last_updated → tier semantics → rewrite affected KNOWLEDGE.md sections → bump date → log everything with citations → write only inside .living/ → summarize changes in 2–3 lines.

### Step 5: Seal & report
- Write ORIGINAL.sha256 (sha256 of SKILL.md).
- Verify SKILL.md is byte-identical to before (hash it before and after the whole operation).
- Report per skill: mode chosen, review interval, sources listed, skips and reasons. Remind: sources.md wants a human skim; refresh is pull-based unless a cron heartbeat is added (see Living Skills docs/AUTOMATION.md).

## Batch mode
Process one skill at a time. End with a table: skill | sidecar attached / skipped (reason) | mode | interval | # sources.
