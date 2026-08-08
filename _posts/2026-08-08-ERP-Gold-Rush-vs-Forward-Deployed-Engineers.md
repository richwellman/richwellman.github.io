# What the ERP Gold Rush Can Teach the Forward Deployed Engineer Era

Every generation gets its own version of the same job: sit inside a business, figure out what people actually do all day, and translate that into something a system can act on. In the late '90s and early 2000s it was the SAP and PeopleSoft implementation consultant. Today it's the forward deployed engineer (FDE) — a role Palantir popularized and that OpenAI, Anthropic, Scale, and a wave of AI startups have since adopted as a core go-to-market motion.

Different technology, same starting move: send someone smart into the business to do discovery, because the off-the-shelf system doesn't know how this specific company actually operates.

## The Same Discovery Motion, Two Different Endpoints

ERP consultants ran business process reengineering ahead of configuration. They interviewed stakeholders, mapped "as-is" workflows, and force-fit them into the vendor's "best practice" templates — customizing only where the business refused to bend. The output was a fit-gap analysis and, eventually, a configured system that a client's own IT staff could run without the consultant in the room.

FDEs do the same discovery — sit inside the workflow, learn what the business actually does — but the target isn't a static configured system. It's a model or agent that has to keep adapting as the business, the data, and the underlying model itself change. Documentation isn't really the deliverable; it's scaffolding for prompts, tools, and evals. And critically, FDEs often don't try to make themselves unnecessary the way ERP consultants nominally did — the ongoing tuning loop is part of the pitch.

The deeper difference: ERP consultants translated process into rigid, deterministic rules. FDEs translate process into judgment a model can approximate probabilistically. That gap never fully closes — it just gets narrower. Which is probably why FDE is turning into a standing role rather than a project phase that winds down.

## Six Lessons From the First Era

**1. The implementation is never really "done."** ERP shops that budgeted for a fixed go-live date got burned — change requests and process drift kept arriving for years after cutover. Treating an AI deployment as a project with an end date instead of an operating model invites the same failure, just on a faster clock given how quickly models and tools churn.

**2. Customization debt compounds.** Every time a business insisted the system bend to its own way of working, it forked from the vendor's baseline and made every future upgrade harder. The AI-era version is over-fitting prompts and tools to one team's idiosyncratic process — great until the underlying model changes and the brittle scaffolding breaks.

**3. Process fluency beats software fluency.** SAP certifications were plentiful; people who could sit with a warehouse supervisor and extract what actually happened on the floor — not what the SOP claimed — were scarce and valuable. Same dynamic now: domain and process knowledge is the bottleneck for FDEs, not model trivia.

**4. Big-bang cutovers are where projects die.** Phased rollouts with parallel-run periods survived. "Flip the switch Monday" implementations produced the famous failure stories — Hershey's, FoxMeyer's bankruptcy. Staged rollout with a real fallback path is the same lesson for putting agents into production workflows.

**5. Governance gets neglected until it's a mess.** Nobody owned "why does this business rule exist," so ERPs accumulated years of undocumented custom code nobody could safely touch. The AI equivalent is prompt and tool sprawl with no versioning or ownership — same failure mode, compressed timeline.

**6. Vendor incentives and customer incentives diverge over time.** Consultancies made more money the longer an implementation dragged on. Worth watching for the same pattern in FDE engagements, where a vendor's growth metric is deployment breadth and depth — not necessarily the fastest path to customer self-sufficiency.

## The Throughline

The first era's failures were rarely about the technology being incapable. They were about organizations underestimating how much of "digital transformation" is actually organizational change management wearing a technology costume. That lesson didn't expire — it just got a new job title.

---

*Rich Wellman is a Solutions Architect at a large enterprise, building AI automation on Azure. He writes about what actually works at [richwellman.com](https://richwellman.com).*
