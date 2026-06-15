# From Five Prompts to One

So we got to one prompt eventually. But it took six different architectures to get there, and I wouldn't have believed it would work if I hadn't watched it happen.

The problem is banking documents. Wire transfer requests, patient cash sweeps, concentration reports. Treasury staff reconcile these against the system every day to confirm every dollar moved where it was supposed to. Each document has transactions. Each transaction has a from, a to, and an amount. The job is to extract all of that and produce rows the system can compare against.

The first thing I tried was the obvious thing. Paste the PDF text into a model, prompt it for structured JSON. That worked as a proof of concept. An LLM can read these documents and return structured output without a custom parser. You can't automate that form, but the signal was clear enough to keep going.

![Manual flow](/assets/images/manual_copilot_flow_v2.svg)

So we moved to custom Document Intelligence models. Trained per document type, one for wire transfers, one for sweep summaries. Fixed layouts worked fine. Treasury documents don't have fixed layouts. One sweep has twelve rows, the next has twenty-three. Wire requests have optional fields that only show up for inter-bank transfers. Every time a new document type came in, another labeling session, another training run, another testing cycle. The maintenance overhead piled up immediately.

![Doc Intelligence Pipeline](/assets/images/doc_intelligence_pipeline.svg)

Then I built a classifier that routed each document to its matching analyzer. Classify first, send it to the right handler. That's a reasonable pattern. The failure modes multiplied proportionally. Misclassify a document and it goes to the wrong analyzer. Send it to the right analyzer and it hits a layout variant it hasn't seen. Get all that right and a field mapping is still incomplete. Three layers meant three places things could go wrong. Debugging a failure meant asking: did the classifier route correctly? Did the right analyzer fire? Were the nulls expected? I was spending more time diagnosing the pipeline than improving the extraction.

![Content Understanding Pipeline](/assets/images/classifier_failure_points.svg)

I moved to an Azure Function handling extraction with a low-code agent as the delivery layer. The function called AI services and returned structured JSON. Different layer, same underlying problem. Custom parsing logic per document type, new code for every format variation, a new maintenance bottleneck in a different place.

![Copilot Automate Pipeline](/assets/images/copilot_automate_pipeline.svg)

Then I committed fully to LLM-based extraction. A classification prompt identified the document type. A Switch action routed to one of four type-specific extraction prompts, each with its own JSON schema. Five prompts. Four schemas. Four Switch branches. This was the closest I came before the thing that actually worked, and it's also where I finally understood what the real problem was.

GPT-4.1 is an instruction-following model. And most of what these documents required wasn't instruction-following. It was reasoning. Inferring that the single From account at the top of a sweep applies to every row in the table below it. Recognizing that FFC routing means the sub-account is the real beneficiary, not the intermediate bank. Deciding whether a Total row aggregates multiple destinations or just validates a single wire amount. On clean documents, GPT-4.1 did okay. On the complex ones, it left from_entity blank, treated cost breakdown line items as separate transfers, misread structural relationships. I was asking an instruction-following model to do structural inference. That's not a prompting problem. That's the wrong model.

![Classify Switch Pipeline](/assets/images/classify_switch_pipeline.svg)

So I collapsed the whole thing to one prompt.

One prompt. One schema. All document types. No classifier. No Switch. No branches.

![GPT5 prompt Pipeline](/assets/images/gpt5_single_prompt_pipeline_v2.svg)

The schema is just money movements. From, to, amount. Every document type produces the same shape. The prompt tells the model to extract every distinct money movement, infer shared values from context, treat FFC accounts as the real beneficiary, and flag the document if the amounts don't sum to the grand total.

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

The model is GPT-5 reasoning. It reads the document, builds up an understanding of what's happening in it, infers entity relationships, and maps everything to the schema without being told what type of document it's looking at. Document type is still in the output, but it's a byproduct of the reasoning rather than a prerequisite for routing. Clean documents process in under five seconds.

What I had before was five prompts, four schemas, a Switch with four branches, and a single point of misrouting with no recovery path. What I have now is one prompt and a validation flag. If the amounts don't sum to the grand total, the document goes to human review.

The part I keep thinking about: the classify-then-branch architecture wasn't more complex because it was better. It was more complex because the model underneath it wasn't up to the task. The pipeline was compensating. Put in a model that can actually reason through the problem and the pipeline almost disappears.

---

*Rich Wellman is a Solutions Architect at a major healthcare system, building AI automation on Azure. He writes about what actually works at [richwellman.com](https://richwellman.com).*
