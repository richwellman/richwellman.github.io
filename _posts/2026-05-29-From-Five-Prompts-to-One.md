# From Five Prompts to One: How a Reasoning Model Eliminated an Entire Pipeline Layer

We built six architectures to solve the same problem. The one that worked was the simplest.

The problem: Treasury staff at a major healthcare system receive banking documents
every day — patient cash sweeps, wire transfer requests, concentration reports,
standalone transfers. Each one a PDF. Each one needs to be reconciled against a
treasury management system export to confirm every dollar moved where it was
supposed to move.

Manual reconciliation is slow. A single Patient Cash Sweep can contain seventeen
line items totaling tens of millions of dollars. One transposed account number is
hours of debugging.

The solution, eventually, was a single prompt running on a reasoning model. What
we built before that teaches more than the solution itself.

---

## What We Tried Before It Worked

**Approach 1 — Just ask Copilot.** Paste the PDF text in, prompt for extraction.
This proved the concept: an LLM can read these documents and return structured data
without a custom parser. It also proved the obvious limitation: you can't automate
a paste-and-wait workflow.

**Approach 2 — Custom Document Intelligence models.** We trained a custom model on
each document type — wire transfer forms, sweep summaries, concentration reports.
It worked for fixed layouts. Treasury documents aren't fixed layouts. One sweep has
twelve rows; the next has twenty-three. Wire requests have optional fields that
appear only for inter-bank transfers. A new document type meant a new labeling
session, a new training run, a new testing cycle. The maintenance overhead
compounded immediately.

**Approach 3 — Classifier plus type-specific analyzers.** A smarter architecture:
classify the document first, route to the right analyzer. The architecture was sound.
The failure modes multiplied. Misclassification routes the document to the wrong
analyzer. The right analyzer hits a layout variant it hasn't seen. The extraction
succeeds but a field mapping is incomplete. Each layer adds diagnostic surface
without proportional accuracy. Debugging a failed extraction now means asking: did
the classifier route correctly? Did the right analyzer fire? Did the extraction
return nulls it shouldn't have? Complexity scaled faster than reliability.

**Approach 4 — Copilot Studio orchestrating an Azure Function.** We moved to
Copilot Studio as the delivery layer, with an Azure Function handling extraction.
The problem: the function became a maintenance bottleneck. Custom parsing logic per
document type. Every new format meant new code. Different language, same problem as
the custom models.

**Approach 5 — GPT-4.1 with classify-then-extract prompts.** We embraced LLM-based
extraction fully. A classification prompt identified the document type. A Power
Automate Switch routed to one of four extraction prompts, each with its own JSON
schema. This was the closest we got before the real solution — and it exposed the
actual problem clearly.

GPT-4.1 is an instruction-following model. The extraction prompts we needed weren't
instruction-following tasks. They were reasoning tasks:

- Infer that the single "From" account at the top of a sweep applies to every row
  in the table below it
- Understand that FFC routing means the sub-account is the real beneficiary, not
  the intermediate bank
- Distinguish between line items that describe cost breakdown versus line items that
  are separate transfers
- Decide whether a "Total" row aggregates destinations or justifies a single wire amount

GPT-4.1 handled clean documents acceptably. On complex ones it left fields blank,
treated cost breakdowns as separate transfers, or misread structural relationships.
The model was doing its best. We were asking it to do something it wasn't built for.

The architecture also created a maintenance problem: five prompts, four JSON schemas,
a Switch with four branches. Adding a new document type meant a new prompt, a new
schema, a new Switch case.

---

## What Actually Worked

We collapsed everything to one prompt.

One prompt. One schema. All document types. No classifier. No Switch. No branches.

The schema defines every money movement as a `from → to → amount` row:

```json
{
  "document_type": "string",
  "date": "string",
  "transactions": [
    {
      "from_entity": "string",
      "from_account": "string",
      "to_entity": "string",
      "to_account": "string",
      "amount": "number",
      "description": "string or null"
    }
  ],
  "grand_total": "number or null",
  "transaction_count": "number"
}
```

The prompt instructs the model to extract every distinct money movement, infer
shared values from context, use FFC accounts as the real beneficiary, and validate
that amounts sum to the grand total.

The model runs on GPT-5 reasoning. The pipeline is five steps:

```
Trigger → OCR → One Prompt → Parse JSON → Validate → Database
```

The reasoning model reads the document, builds an internal model of its structure,
infers entity relationships, and maps everything to the universal schema — without
being told what type of document it's processing. Document type is still output as
a field, but it's a byproduct of reasoning rather than a routing prerequisite.

The arithmetic validation catches extraction failures: if `grand_total` is present
and the amounts don't sum to it, a `validation_error` field routes the document to
human review. Clean documents get processed automatically.

---

## The Numbers

| Dimension | Classify-then-branch (GPT-4.1) | Single prompt (GPT-5 reasoning) |
|---|---|---|
| Prompts to maintain | 5 | 1 |
| Power Automate actions | ~15 | ~5 |
| JSON schemas | 4 | 1 |
| Adding a new document type | New prompt + Switch case + schema | Update one prompt |
| Failure modes | Misclassification, wrong branch, null fields | Extraction error |
| Calls per document | 2 | 1 |

---

## What This Actually Means

The lesson isn't specific to document extraction. It's about where complexity
belongs in an AI pipeline.

**Classification-and-branch architectures push complexity into orchestration.** The
prompt stays simple; the pipeline compensates. You pay for this with failure modes
at every routing decision, with maintenance overhead on every branch, with no
recovery path from a misclassification.

**A reasoning model moves that complexity into the model itself** — where it's
invisible, zero-maintenance, and doesn't require you to enumerate every document
type in advance. The model infers structure as part of generating output. That's
what it's for.

The broader principle: if you're building elaborate classification and branching
logic to compensate for a model that can't reason through the problem, you might
have the wrong model. Pipeline complexity is often a symptom of model mismatch.

Universal schemas are worth looking for. Every money movement is `from → to →
amount`. Every support ticket is `symptom → severity → owner`. Find the invariant
in your domain. Build the schema around that, not around the document types.

And built-in validation catches what prompts miss. Extraction will fail
occasionally. A deterministic check — arithmetic, referential integrity, required
field presence — provides a reliable safety net without requiring extraction to be
perfect.

The reasoning model didn't just solve the problem. It simplified the entire pipeline.

---

*Rich Wellman is a Solutions Architect at a major healthcare system, building AI
automation on Azure. He writes about what actually works at
[richwellman.com](https://richwellman.com).*
