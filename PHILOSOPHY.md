# The Living Skills Philosophy

## I. The half-life of a skill

In 1962, Fritz Machlup coined "the half-life of knowledge" — the time it takes for half of what you know in a field to become obsolete. In fast-moving technical domains that half-life is now measured in months, sometimes weeks. Generative Engine Optimization barely existed two years ago; its "best practices" turn over with every crawler update and every new answer engine.

A skill file is frozen knowledge. The moment you write it, its half-life clock starts ticking. This creates a cruel asymmetry: the skills you most want to encode — the ones about fast-moving fields — are exactly the ones that decay fastest. The skill that most needs to exist is the one that goes stale first.

Static skills don't fail loudly. They fail by confidently applying yesterday's world with today's authority. A stale skill is worse than no skill, because no skill at least prompts a fresh look.

## II. The two-layer insight

Look closely at any knowledge-bearing skill and you find two materials with wildly different decay rates fused together:

**Methodology** — how to audit a site, how to structure a report, what "good" looks like, in what order to check things. This is craft. It changes when *you* learn something, on a timescale of years.

**Knowledge** — what the current rules are, how platforms behave this month, which spec version is live, what the thresholds are. This is state. It changes when *the world* changes, on a timescale of weeks.

Fusing them in one file means the whole skill inherits the decay rate of its fastest-rotting part. Separating them means the methodology stays pristine while the knowledge layer gets a maintenance mechanism sized to its own velocity.

This is old wisdom in new clothes. Code has always separated logic from configuration. Journalism separates the newsroom's standards from today's facts. Science separates the method from the current best theory. Living Skills applies the same cut to agent skills: **SKILL.md is the method; KNOWLEDGE.md is the current best theory.**

## III. The constitutional limit

The obvious next question: if a skill can rewrite its knowledge, why not let it rewrite its methodology too? Full self-improvement!

Because that system has a known failure mode, and it isn't dramatic — it's mundane. Each self-edit is made by a model reading its own previous self-edit. Small misreadings compound. Sections get "simplified" away. A persuasive blog post convinces one refresh cycle to weaken the verification step, and every future cycle inherits the weakness. Within a handful of iterations the skill is a photocopy of a photocopy.

So Living Skills draws a constitutional line:

> **The machine may update its beliefs. It may never update its instructions or its epistemics.**

Concretely: the Refresh Protocol may write to KNOWLEDGE.md and CHANGELOG.md, and to nothing else — ever. SKILL.md (how to think) and sources.md (whom to trust) are human-curated. The human amends the constitution; the machine amends the almanac.

This is what makes the system *converge* instead of *drift*. Beliefs anchored to primary sources are self-correcting — if a refresh ingests something wrong, the next refresh against the same authoritative sources can fix it. Instructions have no external anchor; an error there is permanent and self-propagating. You only allow autonomous mutation in the layer that has a ground truth to snap back to.

## IV. Do no harm: the sidecar and the advisory principle

The first design that occurs to everyone — including us — is surgery: cut the stale facts out of the skill, move them to a knowledge file, stitch in a refresh mechanism. It works, and for your own skills it's fine. But the moment the skill belongs to someone else, surgery is the wrong metaphor and the wrong ethics.

A skill is authored judgment. A legal-review skill, a health-guidance skill, a vendor's audited workflow — someone decided every sentence in it, and possibly carries responsibility for it. A machine that reorganizes that file, however carefully, now co-owns whatever goes wrong, and silently. So Living Skills' final form inverts the design: **the original skill is immutable.** All evolution machinery lives beside it in a `.living/` sidecar — an updated brain on the side — and the original is hash-verified on every use to prove the promise is kept. Uninstall is `rm -rf .living/`; what remains is byte-identical to what the author shipped.

Immutability forces a second question with a better answer than the obvious one: when the sidecar's fresh knowledge contradicts the skill's built-in claims, who wins? The obvious answer — newest fact wins — is correct for your own notes and dangerous everywhere else. The web research that feeds the sidecar is probabilistic; the skill's text is deliberate. So the default is **advisory**: the conflict is surfaced, dated, and sourced — *"the skill says X; as of <date>, <source> indicates Y — how do you want to proceed?"* — and a human resolves it. For legal, health, finance, and safety domains, advisory isn't the default, it's the floor: override mode is forbidden there outright.

This reframes what the system is. Not a machine that corrects skills, but a machine that keeps skills honest — a conscience that knows the date, attached to a body it is not allowed to alter.

## V. Provenance, or the epistemics of a self-updating system

A system that changes its own beliefs needs an answer to "why do you believe that?" Living Skills gives three:

**Trust tiers.** Every source in sources.md is PRIMARY (official docs, specs, changelogs, platform announcements) or SECONDARY (blogs, community posts, agency content). Primary findings update beliefs directly. Secondary findings only *nominate* — they enter the changelog as "unconfirmed, monitoring" until a primary source corroborates. This single rule filters most of the confident junk that dominates fast-moving fields, where the ratio of content *about* the field to authoritative statements *from* the field is catastrophically bad.

**Citations on every change.** A belief that arrives without a URL doesn't get in. This isn't bureaucracy; it's what makes the next property possible.

**The changelog as audit surface.** CHANGELOG.md is append-only and human-readable: date, what changed, why, source. It exists so a human can skim it monthly and catch the one bad ingestion before it matters. The system is autonomous in operation, supervised in retrospect — which is the right trade for a tool, as opposed to the wrong trade of being supervised in operation (defeats the point) or autonomous in retrospect (unauditable).

**Honest staleness** completes the picture: if the user skips a refresh, the skill must state the age of its knowledge in its output. A skill is allowed to be out of date; it is not allowed to hide it.

## VI. Pull, push, and the heartbeat

By default an evolved skill refreshes when invoked and stale — pull. This is free and requires no infrastructure, but it means the first use after a quiet period pays a research tax, and a skill nobody invokes learns nothing.

For skills you rely on, add a heartbeat: a scheduled job that invokes the refresh on a cadence (see docs/AUTOMATION.md). Now knowledge is always warm, refreshes happen off the critical path, and the changelog accumulates even while you're on vacation — a diary of how the field moved, written by the skill itself. Six months of a GEO skill's changelog is, incidentally, a decent history of GEO.

Review intervals should match domain velocity, not a global default: 7–14 days for AI-platform-adjacent fields, 30–60 for mature specs and slow regulation. The interval is itself a belief about the world's speed — and it lives in KNOWLEDGE.md's frontmatter, where a human can tune it.

## VII. Selective evolution

Not everything should live. A skill that encodes *your* conventions — file layouts, naming schemes, personal workflows — has no external world to track. Bolting a refresh protocol onto it adds latency, wasted searches, and a changelog full of "no changes found." The eligibility check in skill-evolver exists precisely to say no: procedural skills stay static, and that's correct. A heartbeat belongs only where there's a pulse to track.

## VIII. Where this goes

The pattern generalizes beyond skills. Anything an agent treats as ground truth — prompts, playbooks, runbooks, CLAUDE.md files — has the same two-layer structure and the same decay problem, and admits the same cure: separate method from state, give state a dated heartbeat and trusted sources, log every change with provenance, and forbid the loop from touching its own instructions.

The endgame isn't skills that improve themselves. It's something more modest and more trustworthy: **skills whose originals are provably untouched, whose facts are always current, and whose every changed belief is traced to a source a human chose to trust — with a human deciding whenever fresh facts and authored judgment collide.**

Knowledge rots. Methodology endures. Build accordingly.
