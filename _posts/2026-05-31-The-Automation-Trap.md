# The Automation Trap Isn't Technical. It's a Selection Problem.

Most automation projects don't fail because the technology doesn't work. They fail because the team picked the wrong process.

I've built automation pipelines at a large healthcare system for the past few years — everything from Power Automate flows that fire on SharePoint triggers to multi-step AI agents that extract structured data from PDFs. The hardest thing I've learned isn't how to build them. It's how to decide what to build.

The selection problem is invisible until it isn't. You spend eight weeks engineering a solution, ship it, and then watch it save four hours a month. The math was never there. You just didn't run the math before you started.

---

## The Selection Trap

McKinsey's November 2025 research puts 57% of US work hours as technically automatable with today's AI agents and robotics. That number gets cited as evidence that the automation opportunity is massive. It is. But it also means that most of what you're looking at *can* be automated — which makes the selection problem worse, not better.

Most organizations have automated less than 15% of eligible processes. The gap isn't technical capability. It's the absence of a filter for deciding which 15% to build first.

Two selection errors keep appearing in every automation program I've been part of:

**The prestige automation.** A process that looks impressive to automate — multi-system, AI-powered, generates a good slide. It runs monthly. Even if it works perfectly, the payback period stretches past two years. The engineering investment was real. The value wasn't.

**The visible-but-small win.** A process that's painful enough that everyone complains about it, but it's only 5% of one person's week. You automate it, the team cheers, and the real bottleneck — the process that consumes 40% of the week — sits untouched because it looked harder to automate.

Both errors share the same root cause: the decision was made on gut feel, not economics.

---

## The Complexity-to-Value Matrix

Before I scope any automation now, I place the candidate process in a 2×2. The axes are implementation effort and business value.

![Priority matrix](/assets/images/priority_2x2_matrix.svg)

**Quick Wins first.** High-frequency, structured processes that live in a single system. Low effort to automate, high payback.

**Strategic Bets get staged rollouts.** High value, but the complexity is real — plan for it, don't wish it away.

**Fill-ins only if trivial.** They're not the priority.

**Traps don't get built.** Full stop. The prestige automation lives here.

---

## Scoring a Process

**Effort signals** — these push a process toward low effort:
- Structured, predictable inputs (not PDFs with variable layouts, not free-text emails)
- Single system, no cross-system joins required
- Existing API or low-code connector available
- No multi-step approval paths
- No regulatory audit requirement

**Value signals** — these push a process toward high value:
- High frequency: daily or weekly, not monthly
- High per-unit time cost: this is someone's two hours, not their fifteen minutes
- Errors are expensive: compliance risk, rework, or downstream impact
- It's a bottleneck: upstream work queues here

If a process scores low on both axes, it's a Fill-in at best and a Trap at worst. Don't let the technology's novelty substitute for the economics.

---

## The Payback Formula

Once a process passes the quadrant filter, run the numbers:

![Automation payback period formula](/assets/images/payback_period_formula_titled.svg)

**Cost side:** development hours × fully-loaded rate, tooling and licensing, QA and testing, change management, ongoing maintenance.

**Value side:** hours saved per week × wage, error reduction value, cycle time improvement.

Two adjustments most estimates skip:

**Ramp-up discount.** The first one to three months after go-live are not full savings. People are still learning the new process, there are edge cases to handle, and exceptions that weren't in the spec.

**Maintenance decay.** Automations erode. The process changes, the source system gets updated, the data format shifts. Budget 10–20% annual maintenance overhead against your savings figure.

A direct-labor calculation understates ROI by 30–50%. Error reduction and cycle time value are real — include them. But they're harder to quantify, so lead with the labor math and treat the rest as upside.

---

## The Enterprise Wrinkle: Risk as a Modifier

In a regulated environment, a third dimension can reclassify a process. Compliance exposure, audit requirements, and data residency constraints can push a Quick Win into Strategic Bet territory — not because the automation is technically harder, but because the governance work around it is.

Before finalizing a quadrant placement: ask whether the process touches protected health information, financial records, or a regulatory workflow. If yes, the effort estimate goes up. The value might too — compliance risk reduction is often the highest-value automation outcome in healthcare — but the naive effort estimate needs to be adjusted.

---

## When This Doesn't Apply

This filter works when there's enough process regularity to automate. It doesn't work for processes that are genuinely judgment-intensive, where exceptions are the rule rather than the edge case.

Don't use the matrix to justify automating something that actually requires human reasoning — the maintenance cost of a badly-scoped AI agent will swamp any savings in the first year. The question isn't "can we automate this?" It's "does this process have enough structure that an automation will be cheaper to maintain than the human doing it?"

---

McKinsey didn't tell you what to build first. This does.

---

*Rich Wellman is an AI Automation Specialist at the healthcare system where he works, building AI pipelines on Azure. He writes about what actually works at richwellman.com.*
