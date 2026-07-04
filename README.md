# Living Skills

**Knowledge rots. Methodology endures. And other people's skills are not yours to rewrite.**

A framework for keeping AI agent skills current **without ever modifying them** — plus a meta-skill (`skill-evolver`) that attaches a self-updating knowledge sidecar to any existing skill.

## The problem

Every skill fuses two materials with wildly different decay rates: **methodology** (how to work — stable for years) and **knowledge** (what's currently true — stale in weeks). A GEO skill written a month ago confidently applies last month's crawler behavior. Static skills don't fail loudly; they fail by applying yesterday's world with today's authority.

The obvious fix — rewrite the skill to separate the layers — has a problem of its own: many skills aren't yours to rewrite. Vendor skills, legal skills, health skills: authored judgment that a machine shouldn't reorganize, however carefully.

## The solution: the sidecar

Never touch the original. Attach an updated brain beside it.

```
your-skill/
├── SKILL.md                ← IMMUTABLE — hash-verified on every use
└── .living/
    ├── PROTOCOL.md         ← sidecar rules: staleness gate, refresh, conflicts
    ├── KNOWLEDGE.md        ← current facts, dated (machine-refreshed)
    ├── sources.md          ← trusted sources, trust-tiered (human-curated)
    ├── CHANGELOG.md        ← every belief change, with citations
    └── ORIGINAL.sha256     ← proof the original was never touched
```

On invocation: verify the original's hash → check knowledge age → refresh from trusted sources if stale → do the task with fresh facts. When fresh knowledge **contradicts** the skill's built-in claims, default behavior is **advisory**: surface the conflict — *"the skill says X; as of <date>, <source> says Y — how do you want to proceed?"* — and let the human decide. Silent override is opt-in for your own skills and forbidden for legal/health/finance/safety domains.

Uninstall = delete `.living/`. The skill is provably exactly what its author shipped.

Full reasoning: [PHILOSOPHY.md](PHILOSOPHY.md) · Formal contract: [docs/SPEC.md](docs/SPEC.md) (invariants K0–K6)

## Quickstart

1. Copy `skill-evolver/` into your skills directory.
2. Say: `make my geo-audit skill self-updating` (or `evolve every skill in ~/.claude/skills`).
3. Approve the one-time global hook (a paragraph in your CLAUDE.md that tells the agent to check invoked skills for a `.living/` sidecar — needed because the immutable original can't reference it).
4. Skim the generated `sources.md` once — it's the epistemic foundation and the one file worth human review.
5. Optional: add a heartbeat so refreshes happen on schedule instead of on demand — [docs/AUTOMATION.md](docs/AUTOMATION.md).

See [examples/geo-audit](examples/geo-audit) for a complete worked example: a pristine "vendor" SKILL.md plus a full sidecar, including a changelog showing a rejected secondary-source claim.

## Principles

1. **Do no harm (K0)** — the original skill is immutable, hash-verified, and restorable by deleting one folder.
2. **Constitutional limit (K1)** — the machine updates its beliefs (KNOWLEDGE.md), never its instructions or its epistemics.
3. **Provenance or it didn't happen (K3)** — every belief change carries a source URL.
4. **Trust tiers** — primary sources update beliefs; secondary sources only nominate.
5. **Advisory floor (K6)** — fresh research advises; authored judgment stands until a human rules. Mandatory for high-stakes domains.
6. **Upstream consent (K5)** — vendor skill updates are detected and diffed, never auto-applied.
7. **Honest staleness (K4)** — skipped refresh means the output states its knowledge age.
8. **Selective evolution** — procedural skills get no sidecar; a heartbeat belongs only where there's a pulse to track.

## License

MIT
