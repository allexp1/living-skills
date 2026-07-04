# Automation: from pull to push

By default an evolved skill refreshes lazily — on invocation, when stale. That's zero-infrastructure, but the first use after a quiet stretch pays a research tax, and unused skills learn nothing. A scheduled heartbeat fixes both.

## Cron + Claude Code (Linux / macOS)

Refresh one skill weekly:

```bash
# crontab -e
0 6 * * 1 cd ~/.claude/skills && claude -p "Invoke the geo-audit skill's Refresh Protocol only. Do not run an audit. Report what changed." >> ~/logs/skill-refresh.log 2>&1
```

Refresh every evolved skill (they're identifiable by KNOWLEDGE.md):

```bash
#!/usr/bin/env bash
# refresh-skills.sh
set -euo pipefail
SKILLS_DIR="${1:-$HOME/.claude/skills}"
for k in "$SKILLS_DIR"/*/KNOWLEDGE.md; do
  skill_dir="$(dirname "$k")"
  name="$(basename "$skill_dir")"
  echo "=== Refreshing: $name ($(date -Iseconds)) ==="
  claude -p "Read $skill_dir/SKILL.md and run its Refresh Protocol only — no main task. Summarize changes." \
    --allowedTools "WebSearch,WebFetch,Read,Write,Edit"
done
```

```bash
# weekly, Monday 06:00
0 6 * * 1 ~/bin/refresh-skills.sh >> ~/logs/skill-refresh.log 2>&1
```

## launchd (macOS, e.g. a dedicated Mac mini)

`~/Library/LaunchAgents/com.user.skill-refresh.plist`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN"
 "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0"><dict>
  <key>Label</key><string>com.user.skill-refresh</string>
  <key>ProgramArguments</key>
  <array><string>/Users/you/bin/refresh-skills.sh</string></array>
  <key>StartCalendarInterval</key>
  <dict><key>Weekday</key><integer>1</integer><key>Hour</key><integer>6</integer></dict>
  <key>StandardOutPath</key><string>/Users/you/logs/skill-refresh.log</string>
  <key>StandardErrorPath</key><string>/Users/you/logs/skill-refresh.err</string>
</dict></plist>
```

```bash
launchctl load ~/Library/LaunchAgents/com.user.skill-refresh.plist
```

## GitHub Actions (if your skills live in a repo)

```yaml
# .github/workflows/refresh-skills.yml
name: Refresh skill knowledge
on:
  schedule: [{ cron: "0 6 * * 1" }]
  workflow_dispatch:
jobs:
  refresh:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm install -g @anthropic-ai/claude-code
      - run: ./refresh-skills.sh ./skills
        env: { ANTHROPIC_API_KEY: "${{ secrets.ANTHROPIC_API_KEY }}" }
      - run: |
          git config user.name "skill-refresh-bot"
          git config user.email "bot@users.noreply.github.com"
          git add -A
          git diff --staged --quiet || git commit -m "chore: knowledge refresh $(date -I)"
          git push
```

This variant has a pleasant side effect: every refresh becomes a commit, so `git log -- skills/*/KNOWLEDGE.md` is a versioned history of your domain, and a PR-based variant (commit to a branch, open a PR) turns the human audit step into a code review.

## Operational notes

- **Stagger schedules** if you have many skills — don't fire 15 research sessions at 06:00 sharp.
- **Review cadence:** skim the changelogs monthly. That's the entire human cost of the system, and it's the step that catches bad ingestions.
- **Spec reminder:** whatever runs the refresh, it may only write KNOWLEDGE.md and CHANGELOG.md. If a refresh commit ever touches SKILL.md or sources.md, treat it as a bug.
