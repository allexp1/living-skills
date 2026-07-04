# Living Sidecar Protocol — geo-audit

> Read by the global Living Skills hook whenever the geo-audit skill is invoked. The original SKILL.md is never modified; this file governs the sidecar only.

## Mode

`advisory` — knowledge here never silently overrides SKILL.md. Conflicts are surfaced to the user.

## On every invocation of the parent skill

1. Verify integrity: hash `../SKILL.md` and compare to `ORIGINAL.sha256`. If it differs, tell the user the original skill changed since the sidecar was installed and offer to re-baseline (update the hash) or re-review the sidecar.
2. Read `KNOWLEDGE.md` frontmatter. If `last_updated` is older than `review_interval_days`, announce staleness and run the Refresh Protocol below before the main task (user may say "skip refresh"; then state the knowledge age in the output).
3. Use `KNOWLEDGE.md` as the source of current facts for the audit.
4. **Advisory conflict rule:** if `KNOWLEDGE.md` contradicts something stated inside SKILL.md, do NOT silently substitute. Tell the user: "The skill says X; as of <last_updated>, <source> indicates Y. How do you want to proceed?" and follow their choice.

## Refresh Protocol

1. Read `sources.md`. For each entry, web-search for changes/announcements since `last_updated`.
2. PRIMARY-source findings: apply to `KNOWLEDGE.md`. SECONDARY-source findings: log as UNCONFIRMED in `CHANGELOG.md` until a primary source corroborates.
3. Rewrite only affected sections of `KNOWLEDGE.md`; preserve section structure.
4. Bump `last_updated`.
5. Append every change (or "No changes found") to `CHANGELOG.md` with rationale and source URL.
6. Write only inside `.living/`. Never modify `../SKILL.md` or `sources.md`.
7. Summarize changes to the user in 2–3 lines before continuing.
