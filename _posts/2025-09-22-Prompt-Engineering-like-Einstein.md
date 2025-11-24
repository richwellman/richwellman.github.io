# Prompt Engineering: The art of asking better questions

> "If I had an hour to fix a problem I would spend 55 minutes on forming the question."
>
> — Often attributed to Albert Einstein (and variations to other luminaries)

Whether Einstein actually said this or not, the principle is profound. And nowhere is it more relevant than in prompt engineering.

## The Problem with Most Prompts

Most people treat AI like a search engine. They type in a quick question, hit enter, and hope for useful results.

"Write me a sales email."

"Analyze this data."

"Create a marketing plan."

The AI dutifully generates something. It's grammatically correct. It looks professional. And it's completely useless because the question was lazy.

Garbage in, garbage out—but with better grammar.

## Prompt Engineering is Problem Definition

The real work of prompt engineering isn't learning special syntax or magic words. It's the discipline of clearly defining what you actually want.

This is hard. It requires you to:

- Know your desired outcome
- Understand your constraints
- Define your criteria for success
- Provide relevant context
- Anticipate edge cases

In other words, it forces you to think clearly about the problem before demanding a solution.

That's why spending 55 minutes on the question isn't wasted time. It's the work.

## Bad Prompt vs. Good Prompt

**Bad Prompt:**
```
Write a sales email.
```

**Good Prompt:**
```
Write a sales follow-up email for a B2B SaaS prospect who attended our webinar
on AI automation but didn't respond to our first outreach.

Constraints:
- Keep it under 150 words
- Don't mention pricing (that's for the call)
- Reference specific value from the webinar content
- Include a soft ask for 15-minute discovery call

Tone: Professional but conversational, not salesy

Context: This prospect is a VP of Operations at a 200-person manufacturing
company. They're exploring AI but don't have internal expertise. Our
differentiation is practical implementation vs. theoretical consulting.
```

Which one will give you a better result?

The second prompt took 5 minutes to write. It will save you 30 minutes of editing generic AI output into something actually useful.

## The Five Questions Framework

Before you write a prompt, answer these five questions:

### 1. What is the specific output I need?
Not "a blog post" but "an 800-word blog post formatted for LinkedIn with 3 actionable takeaways and a CTA to book a consultation."

### 2. Who is the audience?
Not "people" but "CFOs at mid-market companies who are skeptical of AI hype and need to see ROI in months, not years."

### 3. What context does the AI need that I have in my head?
The AI doesn't know your industry, your company, your product, or your customer's pain points. You have to provide that.

### 4. What constraints or requirements apply?
Length limits, tone requirements, format specifications, things to avoid, compliance requirements.

### 5. How will I know if the output is good?
What criteria will you use to evaluate it? Define those upfront.

## Examples in Practice

### Example 1: Data Analysis

**Lazy prompt:**
```
Analyze this sales data.
```

**Better prompt:**
```
Analyze Q4 sales data to identify:
1. Which product lines underperformed vs. target
2. Whether the decline was due to volume or pricing
3. Geographic patterns in the decline
4. Correlation with sales rep tenure

Format findings as an executive summary with:
- 3 key insights (bullets, not paragraphs)
- Supporting data table
- 2 recommended actions

I need this for Thursday's board meeting, so focus on findings that
suggest concrete actions we can take in Q1.
```

### Example 2: Code Generation

**Lazy prompt:**
```
Write a Python script to process CSV files.
```

**Better prompt:**
```
Write a Python script that:
- Reads CSV files from /data/input/ directory
- Filters for rows where status = "complete"
- Calculates total revenue by product category
- Outputs summary to /data/output/summary.csv

Requirements:
- Handle missing values gracefully
- Log errors to /logs/processing.log
- Use pandas library
- Include error handling for file not found
- Add comments explaining each major step

Input CSV format: date, product_id, category, revenue, status
Output CSV format: category, total_revenue, row_count
```

### Example 3: Strategic Thinking

**Lazy prompt:**
```
Help me think through this business problem.
```

**Better prompt:**
```
I need to decide whether to build our AI evaluation capability
in-house or partner with an existing vendor.

Context:
- We have 2 engineers with ML experience
- Current projects need eval framework within 2 months
- Budget: $50K for tools/services this quarter
- Long-term: expect 5-10 AI projects per year

Help me think through:
1. Build vs. buy tradeoffs specific to our situation
2. What capabilities we'd need in-house either way
3. Risks I might be overlooking
4. What decision criteria I should use

Challenge my assumptions. I'm biased toward building because
I like having control, but that might not be the right call here.
```

## The ROI of Better Prompts

**Time spent on prompt:** 5 minutes
**Time saved editing mediocre output:** 30 minutes
**Quality improvement:** Significant

But the real value isn't time savings. It's forcing yourself to think clearly about what you actually need.

Often, when you sit down to write a detailed prompt, you realize:
- You don't actually know what good looks like
- You haven't defined your constraints
- You're asking the wrong question entirely
- You need to gather more information first

That's valuable. That's 55 minutes well spent.

## What Good Prompt Engineering Looks Like

Good prompt engineers don't memorize special techniques. They:

1. **Define the problem clearly** before asking for solutions
2. **Provide context** the AI can't infer
3. **Specify constraints** and success criteria
4. **Iterate** on prompts based on output quality
5. **Build libraries** of proven prompt patterns
6. **Test edge cases** to find where prompts break

It's less "prompt hacking" and more "clear thinking."

## Common Prompt Engineering Mistakes

### Mistake 1: Assuming the AI knows your context
The AI doesn't know your business, your industry, your customers, or your goals. You have to tell it.

### Mistake 2: Being vague about what "good" means
If you can't describe what good output looks like, the AI can't produce it.

### Mistake 3: Not specifying format
"Write a report" could mean anything from 2 paragraphs to 50 pages. Be specific.

### Mistake 4: Asking for multiple unrelated things
One prompt, one clear output. If you need multiple things, use multiple prompts.

### Mistake 5: Not iterating
First prompts rarely nail it. Refine based on what you get back.

## Advanced: Chain of Thought and Structured Prompts

Once you master basic prompt clarity, you can use advanced techniques:

**Chain of Thought:**
```
Before answering, think through:
1. What are the key constraints?
2. What assumptions am I making?
3. What could go wrong?
Then provide your recommendation.
```

**Few-Shot Learning:**
```
Here are 3 examples of good outputs:
[Example 1]
[Example 2]
[Example 3]

Now create one for this new scenario: [details]
```

**Role-Based Prompting:**
```
You are a CFO evaluating AI project ROI. Analyze this proposal with
a focus on financial risk, capital requirements, and payback period.
```

But these are techniques, not fundamentals. The fundamental is: spend time on the question.

## The Meta-Lesson

Prompt engineering teaches a valuable skill that applies beyond AI: the ability to clearly articulate what you want.

How many meetings meander because nobody defined the desired outcome?

How many projects fail because requirements were vague?

How many emails get ignored because the ask wasn't clear?

Prompt engineering is just clear thinking in written form.

And in an age where AI will do increasingly sophisticated tasks, your ability to clearly define the problem becomes your competitive advantage.

Anyone can type "write me a thing."

The value is in knowing exactly what thing you need and why.

That's the 55 minutes. That's the work.

---

## Practical Exercise

Take something you'd normally ask AI to do. Before you write the prompt, spend 5 minutes answering:

1. What specific output do I need?
2. Who is this for?
3. What context is missing?
4. What makes output "good"?
5. What constraints apply?

Then write the prompt.

Compare the quality of output to what you'd get from "just ask it something quick."

The difference is the ROI of thinking before prompting.

---

**Need help implementing AI that actually delivers value?** I help companies build AI systems with proper evaluation frameworks and business metric alignment—which starts with asking the right questions. [Let's talk](mailto:your-email@example.com) about making your AI projects work.

---

*About the author: [Your name] helps companies implement AI automation that delivers measurable business value, starting with clearly defining what "value" means for their specific context.*
