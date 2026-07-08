# You're Going to Build a Star Schema Eventually. Build It First.

So the first version of this report was wrong. Not wrong in the sense that it crashed or threw errors. It produced numbers. They were just about thirteen times too big.

Let me back up.

The system compares Active Directory accounts against HR and an identity vault every week, flags anything that doesn't match, and puts it on someone's queue to fix. Twenty-five different mismatch patterns. An enabled account for someone HR says was terminated. A vault entry still active for someone who's gone. The Power BI report shows how many accounts are in each scenario this week and whether that number is going up or down.

Simple ask. The data underneath is where it got interesting.

The first design did what feels natural when you're close to the SQL. One wide flat table: every account joined to every source, all the columns side by side. Then twenty-five SELECT statements stacked into a UNION ALL, one branch per scenario. I dropped and recreated it every run with SELECT INTO.

It worked, in the sense that it produced rows. But the moment I pointed Power BI at it, everything started fighting me.

The number in the KPI card for a scenario with 400 accounts was 5,200. Thirteen weeks of history sitting in the same table, no date dimension to tell Power BI to only look at this week, so COUNTROWS just added everything up. All of it. Every snapshot at once.

You can patch that. I tried. You add a hard-coded filter to the measure, then do it again for the next measure, then again for the one after. And every time someone adds a visual and forgets the filter, the wrong number shows up in a big card.

At some point I stopped patching and started asking why I was patching. Power BI was telling me something the whole time. It wanted a date dimension. It wanted a defined grain. It was asking for a star.

So I built one.

The whole thing comes down to one sentence you have to write before you write any SQL: what is one row of the fact table? Here, one row is one account, in one scenario, on one weekly snapshot. That's it. Once you write that down, everything else falls out of it.

The fact table holds only keys. Which snapshot, which scenario, which account. COUNTROWS on that table gives you the account count, because the grain is one account per row. The thing you want is the thing the table naturally produces.

Around it go the dimensions. `dim_date` with a flag marking which dates are snapshot dates. That's the table whose absence caused the thirteen-times bug. It is not optional. `dim_scenario` with the scenario name and severity. `dim_account` so the detail page can show who, not just how many.

Once the shape was right, the measures I'd been fighting became almost nothing. Count this week: filter to the latest snapshot date, COUNTROWS. Week over week: compare latest snapshot to the previous one. Eight-week sparkline: drop `dim_date` on the matrix axis and Power BI does it. Drillthrough to the account list: set the scenario as the drillthrough field and the filter travels the relationships for free.

None of that required anything clever. It just required the model to be the shape Power BI was assuming the whole time.

I made one exception. The relationship between the fact and the account dimension is bidirectional, so picking a scenario also filters the account list down to the people in that scenario. Single-direction won't carry that filter. So I bent the rule in one named place, left everything else single-direction, and documented it. One deliberate exception is fine. A model full of them is a different kind of adventure.

One other thing, specific to Fabric: store GUID natural keys as `VARCHAR(36)`, not the native `uniqueidentifier` type. Fabric stores `uniqueidentifier` as binary in the underlying Delta files and it breaks Direct Lake readability. Small thing, would have cost me a migration later.

Looking back at the first version now, it wasn't a different approach from the star schema. It was the first quarter of a star schema, stopped early, with the rest of the work pushed into the report layer where every fix costs more than it would have in the model. All the patching, all the REMOVEFILTERS, all the hard-coded date filters were me building dimensions in DAX after the fact.

Build the boring star first. Power BI does exactly what it promised when you do.

---

*Rich Wellman is a Solutions Architect at a large enterprise, building data and AI systems on Azure and Microsoft Fabric. He writes about what actually works at [richwellman.com](https://richwellman.com).*
