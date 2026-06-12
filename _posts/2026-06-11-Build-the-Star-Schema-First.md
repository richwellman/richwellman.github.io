# You're Going to Build a Star Schema Eventually. Build It First.

Power BI is built on one assumption about your data: that it lives in a star schema. A central fact table surrounded by dimension tables, related one-to-many, single direction. Every feature that makes Power BI feel powerful — relationships, DAX, time intelligence, the matrix sparkline, drillthrough — assumes that shape underneath.

You can ignore that assumption. I did, on the first version of an identity reconciliation system I built. The report still got made. It just made me build the star schema anyway — slowly, painfully, in reverse, one broken measure at a time.

That's the whole lesson, so I'll say it plainly: you are going to build a star schema. The only question is whether you build it at the beginning, or build it after you've tried everything else.

I tried everything else.

---

## What the system does

Every week, the system compares each Active Directory account against two other sources of truth, an HR system and an identity vault, and flags accounts whose state disagrees. An enabled AD account for a worker HR says was terminated. An account the vault still thinks is active for someone who's gone. There are about twenty-five of these mismatch patterns, each one a "scenario" that lands on someone's remediation queue.

The output is a Power BI report: how many accounts are in each scenario this week, whether that number is going up or down, and the list of who's in it. Simple ask. The data underneath is where the adventure happened.

---

## The first engine: one big table and a wall of UNION ALL

The original design did what feels natural when you're close to the SQL. It built one wide, flattened table — every account joined to every source, all the columns side by side, by dropping the table and re-creating it with `SELECT INTO` on every run. Then it classified accounts by stacking twenty-five `SELECT` statements into a single `UNION ALL`, one branch per scenario, each branch hard-coding its own logic.

It worked, in the sense that it produced rows. But it had no notion of *time*, no shared dimensions, and no stable grain. It was a report-shaped answer pretending to be a data model. And the moment I pointed Power BI at it, the model started arguing with me.

---

## The symptom that gives it away: the numbers are too big

Here is the single most common Power BI bug, and it is almost never a Power BI bug:

> The numbers look about N times too big.

In my case, a scenario that had 400 accounts this week was showing 5,200. Multiply by thirteen weeks of history sitting in the same table and you get exactly the wrong answer, confidently rendered in a big KPI card.

The cause is structural. `COUNTROWS` over a fact table counts *every row in filter context*. If there's no date dimension to pin the visual to a single week, the filter context is "all of history," and the count sums every weekly snapshot at once. The flattened table had no date dimension, because a flattened table has no dimensions at all. So every card needed a hand-built filter to claw its way back to "just this week," and every author who forgot one got a number that was off by a factor of however many weeks had accumulated.

You can patch this. You can keep patching it forever. Or you can notice that the model is telling you something: *it wants a date dimension and a defined grain.* It wants a star.

The cross-filter problems told the same story. A slicer on one column wouldn't filter a table built from another, because the relationships needed to carry that filter only exist when you've split the wide table into a fact and its dimensions.

---

## The redesign: model the grain, then the dimensions

So I built the star — on purpose this time. The whole thing turns on one sentence you have to get right before you write any SQL: **what is one row of the fact table?**

Here, one row is *one account, in one scenario, on one weekly snapshot.* That's the grain. Write it down, because every other decision falls out of it. The fact table, I'll call it `fact_account_snapshot`, holds only the keys: which snapshot date, which scenario, which account. No measures stored in it at all. Data Warehouse folks call it a "factless fact" table, which I found confusing at first. You count it with `COUNTROWS`, and because the grain is one row per account per scenario per week, a row count *is* the account count. The thing you want is the thing the model naturally produces.

Around it go conformed dimensions, each answering one "by what?":

- `dim_date` — a real calendar, with a flag marking which dates are snapshot dates. This is the table whose absence caused the "too big" bug. It is not optional. It is never optional.
- `dim_scenario` — one row per scenario, with its name, severity, and remediation notes.
- `dim_account` — the account's attributes, so the detail page can show *who*, not just *how many*.

Relationships run one-to-many from each dimension into the fact, single direction. And here's the payoff: the measures I'd been fighting to write became almost trivial once the shape was right.

- **Count this week**: pin to the latest snapshot date via `dim_date`, then `COUNTROWS`. No more accidental thirteen-week sums.
- **Week-over-week change**: compare the latest snapshot to the *previous* snapshot. (Deliberately "previous snapshot," not "seven days ago", if a weekly load is late, the math still holds.)
- **An eight-week sparkline per scenario**: drop the date dimension on the axis of a matrix and let Power BI's built-in sparkline do it. That only works because the date lives in its own dimension.
- **Drillthrough to the account list**: set the scenario as the drillthrough field; the filter travels the relationships for free.

None of these required heroic DAX. They required the model to match the assumption Power BI was making the entire time.

---

## The one place I broke my own rule (on purpose)

Purist star schemas use single-direction relationships everywhere. I made exactly one exception: the relationship between the fact and the account dimension is bidirectional, so that picking a scenario on the page also filters the account list to the people in that scenario. The filter has to travel "backward" up that one relationship, and single-direction won't carry it.

That's the right way to bend the rule: knowingly, in one named place, because the other relationships stay single-direction so there's no ambiguous filter path. Bidirectional-everywhere is how you get a model that returns different numbers depending on which visual you click. One deliberate exception, documented, is fine. A model full of them is a different kind of adventure.

*(One platform-specific aside, since this ran on Microsoft Fabric: store any GUID natural keys as `VARCHAR(36)`, not the native `uniqueidentifier` type. Fabric stores `uniqueidentifier` as binary in the underlying Delta files, which breaks Direct Lake readability. Small decision, saves a future migration. That's a post of its own.)*

---

## What I'd tell myself at the start

The flattened-table version wasn't a different solution from the star schema. It was the *first quarter* of the star schema, stopped early, with the remaining work deferred into the report layer where it costs ten times as much. Every "just add a filter," every bidirectional toggle, every measure that needed `REMOVEFILTERS` to undo a problem the model created, all of it was me building dimensions and a grain by hand, in DAX, after the fact.

If you report out of a warehouse, the takeaways are short:

- **You will build a star schema.** Decide whether it's deliberate or accidental.
- **Write the fact grain as one sentence before you write SQL.** "One row per ___ per ___ per ___." Everything follows from it.
- **A date dimension is not optional.** The "numbers too big" bug is a date dimension asking to exist.
- **Put the complexity in the model's shape, not in clever measures.** When a DAX formula gets baroque, suspect the model first.

It's the same lesson I keep relearning in different domains: complexity has a right place to live. In document pipelines it belongs in the model, not the orchestration. In BI it belongs in the schema, not the report. Build the boring star up front, and Power BI does exactly what it promised. Skip it, and you'll build it anyway — just sideways, under deadline, one too-big number at a time.

---

*Rich Wellman is a Solutions Architect at a major healthcare system, building data and AI systems on Azure and Microsoft Fabric. He writes about what actually works at [richwellman.com](https://richwellman.com).*
