# Domain 4: Prompt Engineering & Structured Output (20%)
## Comprehensive Question Bank

---

## Task Statement 4.1: Design prompts with explicit criteria to improve precision and reduce false positives

### Q4.1.1 — Scenario: Claude Code for Continuous Integration
**Difficulty: Easy | Knowledge: Explicit criteria vs vague instructions**

Your CI review pipeline flags too many false positives. A colleague suggests adding "be conservative — only report high-confidence findings" to the system prompt. Why is this likely ineffective?

A) General instructions like "be conservative" or "only report high-confidence findings" fail to improve precision because they don't define what constitutes a reportable issue — the model interprets "high confidence" differently across contexts. Specific categorical criteria (what to flag vs skip) are needed.

B) "Be conservative" works well in most cases but needs to be paired with a confidence threshold parameter.

C) The instruction is effective but needs to be placed at the beginning of the system prompt to ensure the model processes it first.

D) Conservative settings reduce both false positives and true positives equally, resulting in no net improvement.

**Correct Answer: A**

**Explanation:** Vague instructions like "be conservative" don't provide the model with actionable criteria for deciding what to report. "High confidence" is subjective — the model may be highly confident about style issues and uncertain about subtle bugs, the opposite of what's desired. Specific criteria (e.g., "flag potential null pointer exceptions, SQL injection risks, and type mismatches; skip local variable naming, import ordering, and bracket style") give the model clear decision boundaries. Option B suggests a parameter that doesn't exist. Option C is placement optimization for a fundamentally inadequate instruction. Option D oversimplifies the dynamics.

---

### Q4.1.2 — Scenario: Claude Code for Continuous Integration
**Difficulty: Medium | Knowledge: False positive impact on developer trust**

Your CI review pipeline reports findings in five categories: security, bugs, performance, style, and documentation. Analytics show 90%+ precision for security and bugs, but only 40% precision for style findings (60% false positive rate). Developers have started ignoring all review comments, including the accurate security and bug findings. What is the most effective immediate action?

A) Temporarily disable the high false-positive category (style) to restore developer trust in the remaining accurate categories, while separately improving the style prompts before re-enabling them.

B) Add disclaimers to style findings noting they may be false positives, while keeping all categories active.

C) Lower the overall review strictness to reduce false positives across all categories.

D) Require developers to explicitly mark each finding as accepted or rejected to train the system.

**Correct Answer: A**

**Explanation:** High false positive rates in one category undermine confidence in all categories — developers learn to ignore everything. Temporarily disabling the problematic category restores trust in the accurate ones (security, bugs) while buying time to improve the style prompts. Option B adds noise without solving the trust problem. Option C reduces true positives in accurate categories to address a problem in one category. Option D shifts burden to developers without fixing the root cause.

---

### Q4.1.3 — Scenario: Claude Code for Continuous Integration
**Difficulty: Hard | Skill: Writing specific review criteria**

Your CI pipeline reviews API endpoint code. You want to catch real issues while minimizing false positives. Which set of review criteria is most effective?

A) "Report: missing input validation on user-facing parameters, SQL queries constructed from string concatenation, API responses that leak internal error details or stack traces. Skip: local variable naming conventions, import ordering, logging format preferences, team-specific bracket style. For each finding, specify the file, line, and a concrete fix suggestion."

B) "Carefully review the code for any issues. Report anything that could potentially cause problems. Be thorough but avoid false positives."

C) "Focus on security and correctness. Use your best judgment about what to report. Rate your confidence for each finding on a scale of 1-10."

D) "Review the code against OWASP top 10 guidelines and common coding best practices. Report all deviations."

**Correct Answer: A**

**Explanation:** Option A defines explicit, actionable criteria: specific categories to report (with concrete examples like SQL string concatenation), specific categories to skip (with examples like import ordering), and required output format. This eliminates ambiguity about what constitutes a reportable finding. Option B uses vague language ("any issues", "potentially cause problems") that produces unpredictable results. Option C relies on subjective judgment and poorly calibrated self-reported confidence. Option D references a broad external standard without prioritizing or scoping what to check.

---

### Q4.1.4 — Scenario: Claude Code for Continuous Integration
**Difficulty: Medium | Skill: Defining explicit severity criteria**

Your CI pipeline assigns severity levels (critical, major, minor) to findings, but severity is inconsistent — the same pattern might be "critical" in one review and "minor" in another. How do you achieve consistent severity classification?

A) Define explicit severity criteria with concrete code examples for each level — for instance, "Critical: SQL injection, authentication bypass, data exposure. Example: `query = 'SELECT * FROM users WHERE id = ' + userId`. Major: Unhandled exceptions, race conditions, resource leaks. Minor: Missing null checks on optional fields, suboptimal algorithms for small datasets."

B) Add "Be consistent with severity ratings across all reviews" to the system prompt.

C) Use a post-processing classifier that normalizes severity ratings based on keyword matching.

D) Remove severity levels entirely and report all findings as equal priority.

**Correct Answer: A**

**Explanation:** Concrete code examples anchored to each severity level give the model unambiguous classification criteria. When the model encounters a similar pattern, it can reason by analogy to the examples. Option B provides a directive without criteria for achieving consistency. Option C adds a brittle external system. Option D removes useful prioritization information that helps developers triage.

---

### Q4.1.5 — Scenario: Structured Data Extraction
**Difficulty: Medium | Skill: Category-specific precision improvement**

Your extraction pipeline classifies document types into 8 categories. Seven categories achieve 95%+ accuracy, but "regulatory filing" misclassifies 30% of the time — confusing regulatory filings with annual reports. Rather than lowering the classification threshold globally, how do you target this specific weakness?

A) Add explicit criteria distinguishing regulatory filings from annual reports — for example, "Regulatory filings are submissions to government agencies (SEC, FDA, EPA) with filing numbers and regulatory references. Annual reports are company-published documents with CEO letters, financial summaries, and shareholder information. When a document contains both regulatory data and financial summaries, classify based on the primary audience: government agency = regulatory filing, shareholders = annual report."

B) Remove "regulatory filing" as a category and merge it with "annual report."

C) Add a confidence threshold that sends low-confidence classifications to human review.

D) Train a separate binary classifier specifically for the regulatory vs annual report distinction.

**Correct Answer: A**

**Explanation:** Explicit criteria that define distinguishing features between confusable categories directly address the classification boundary. By specifying what makes each type unique and providing a tiebreaker rule for ambiguous cases, the model can make consistent decisions. Option B eliminates a useful distinction. Option C handles symptoms without improving the classification itself. Option D adds ML infrastructure when a prompt improvement would suffice.

---

## Task Statement 4.2: Apply few-shot prompting to improve output consistency and quality

### Q4.2.1 — Scenario: Claude Code for Continuous Integration
**Difficulty: Easy | Knowledge: Few-shot examples for format consistency**

Your CI pipeline produces review findings in inconsistent formats — sometimes a single sentence, sometimes a paragraph, sometimes with file references and sometimes without. Detailed format instructions in the prompt haven't resolved the inconsistency. What technique is most likely to achieve consistent formatting?

A) Provide 2-4 few-shot examples showing the exact desired output format — each example should include the file location, issue description, severity level, and a concrete fix suggestion — so the model can pattern-match the format consistently.

B) Define a JSON schema for the output format and validate it after each response.

C) Add stricter formatting instructions with a template: "Format: [FILE]:[LINE] - [SEVERITY] - [DESCRIPTION] - [FIX]."

D) Post-process the output to normalize it into a consistent format.

**Correct Answer: A**

**Explanation:** Few-shot examples are the most effective technique for achieving consistently formatted output when detailed instructions alone produce inconsistent results. By showing 2-4 complete examples, the model learns the exact format pattern and replicates it. Option B (JSON schema via tool_use) would also work but is more heavyweight when the goal is formatted text, not strict data structures. Option C provides a template without examples of it filled out, which is less effective. Option D handles the symptom rather than fixing generation quality.

---

### Q4.2.2 — Scenario: Customer Support Resolution Agent
**Difficulty: Medium | Knowledge: Few-shot for ambiguous scenario handling**

Your support agent inconsistently handles requests that could be either returns or exchanges. Sometimes it immediately initiates a return; other times it asks clarifying questions. You want it to always ask for clarification when the intent is ambiguous. What is the most effective approach?

A) Create 2-4 targeted few-shot examples for ambiguous scenarios, showing the reasoning for why clarification was chosen over immediate action — for instance, an example where a customer says "I want to send this back" and the agent responds by asking whether they want a refund or exchange, with reasoning that "send back" is ambiguous between return and exchange.

B) Add a rule: "When in doubt, always ask for clarification before proceeding."

C) Implement a routing classifier that detects ambiguous intent and forces the clarification path.

D) Require customers to select "return" or "exchange" from a menu before reaching the agent.

**Correct Answer: A**

**Explanation:** Few-shot examples with explicit reasoning for ambiguous cases are the most effective way to calibrate the model's judgment. By showing examples of ambiguous phrasing and demonstrating the desired behavior (ask for clarification) with reasoning (why it's ambiguous), the model can generalize to novel ambiguous phrasings. Option B is too vague — it doesn't define what "in doubt" means. Option C adds infrastructure when prompt engineering would suffice. Option D removes the agent's flexibility to handle natural language.

---

### Q4.2.3 — Scenario: Structured Data Extraction
**Difficulty: Medium | Knowledge: Few-shot reducing hallucination in extraction**

Your extraction pipeline processes invoices with varied formats. When invoices use informal measurements (e.g., "about 50 units", "roughly 2 dozen", "a few hundred"), the model sometimes converts these to exact numbers (50, 24, 300) without flagging the imprecision. How do few-shot examples help?

A) Provide few-shot examples demonstrating correct handling of informal measurements — showing that "about 50 units" should extract as `{quantity: 50, precision: "approximate"}` and "a few hundred" as `{quantity: null, precision: "vague", original_text: "a few hundred"}` — so the model learns to preserve imprecision indicators rather than fabricating exact values.

B) Add a validation rule that rejects any round numbers as potentially approximate.

C) Add a prompt instruction: "Do not convert vague quantities to exact numbers."

D) Remove the quantity field from the schema to avoid the problem entirely.

**Correct Answer: A**

**Explanation:** Few-shot examples demonstrating correct extraction from documents with varied formats (including informal measurements) are the recommended approach for reducing hallucination. The examples show the model exactly how to handle imprecision — using precision flags and preserving original text rather than fabricating exact values. Option B creates false positives for legitimately round numbers. Option C provides a rule without showing how to apply it. Option D eliminates useful data.

---

### Q4.2.4 — Scenario: Claude Code for Continuous Integration
**Difficulty: Hard | Knowledge: Few-shot enabling generalization**

Your code review pipeline flags `try/catch` blocks that swallow exceptions silently (no logging, no re-throw). You add a single few-shot example showing a bare `catch(e) {}` block flagged as problematic. However, the model now only flags bare catch blocks — it misses variations like `catch(e) { return null; }` and `catch(e) { /* TODO */ }`. How should you improve the few-shot examples?

A) Add 2-3 more examples covering variations — `catch(e) { return null; }` (swallowed with silent return), `catch(e) { /* TODO */ }` (swallowed with placeholder comment) — alongside a counter-example showing an acceptable pattern like `catch(e) { logger.error(e); throw; }`. This enables the model to generalize the principle (exception handling must include logging or re-throwing) rather than matching only the specific pattern shown.

B) Expand the single example's description to list all variations: "This includes catch blocks that return null, have TODO comments, return default values, or are empty."

C) Add regex patterns for all known catch-swallowing variations so the review doesn't rely on LLM judgment.

D) Increase the model's temperature to encourage broader pattern matching.

**Correct Answer: A**

**Explanation:** Few-shot examples enable generalization to novel patterns. By showing multiple variations of the problematic pattern alongside a counter-example of the correct pattern, the model learns the underlying principle rather than surface-matching the specific example. The counter-example is particularly important — it defines the boundary between acceptable and problematic. Option B tries to enumerate variations in prose, which is less effective than showing them. Option C replaces LLM judgment with brittle regex. Option D is not a meaningful lever for this problem.

---

### Q4.2.5 — Scenario: Structured Data Extraction
**Difficulty: Medium | Skill: Few-shot for varied document structures**

Your pipeline extracts citations from academic papers. Some papers use inline citations "[Smith 2020]", others use footnotes, and others use a bibliography section. The model handles inline citations well but frequently returns empty citation lists for footnote and bibliography formats. What's the most effective fix?

A) Add few-shot examples demonstrating correct extraction from each document structure — one showing inline citation extraction, one showing footnote extraction, and one showing bibliography section extraction — so the model learns to handle varied formats rather than only the most common one.

B) Pre-process documents to convert all citation formats to inline before extraction.

C) Create three separate extraction prompts, one for each citation format, and route documents to the correct prompt.

D) Add "Check footnotes and bibliography sections for citations, not just inline references" to the prompt.

**Correct Answer: A**

**Explanation:** Few-shot examples covering varied document structures are the recommended approach for handling format diversity. By showing extraction from each citation style, the model learns to recognize and process all formats. Option B adds brittle preprocessing that may corrupt document formatting. Option C requires accurate format detection before extraction, adding complexity. Option D instructs without demonstrating, which is less effective than examples for structural variety.

---

### Q4.2.6 — Scenario: Customer Support Resolution Agent
**Difficulty: Hard | Skill: Few-shot for reducing false positives while enabling generalization**

Your agent has a policy violation detector that flags potentially abusive language from customers. It currently has high false positives — flagging frustrated but non-abusive language like "this is ridiculous" and "I can't believe how bad this is." Adding a rule "only flag actual threats or slurs" helped but now misses passive-aggressive patterns that constitute harassment. How do you calibrate the boundary?

A) Provide few-shot examples distinguishing acceptable frustrated language from genuine abuse patterns — showing "this is ridiculous, I've been waiting an hour" as not flagged (frustrated but not abusive), "I'm going to make sure everyone knows how terrible you are" as not flagged (negative feedback, not a threat), and actual patterns that should be flagged — enabling the model to generalize the boundary between frustration and abuse.

B) Create an exhaustive list of flaggable phrases and match against it.

C) Use sentiment analysis scores as a proxy — flag messages below a threshold.

D) Remove the detector entirely since the boundary is too difficult to define precisely.

**Correct Answer: A**

**Explanation:** Few-shot examples are particularly effective for calibrating judgment boundaries in ambiguous scenarios. By showing examples on both sides of the boundary with reasoning, the model learns the principle rather than memorizing specific phrases. It can then generalize to novel expressions. Option B can't anticipate all variations. Option C treats sentiment as equivalent to abuse, which it isn't. Option D abandons a valuable safety feature.

---

## Task Statement 4.3: Enforce structured output using tool use and JSON schemas

### Q4.3.1 — Scenario: Structured Data Extraction
**Difficulty: Easy | Knowledge: Tool use as the most reliable structured output method**

You need Claude to extract invoice data in a strict JSON format. Which approach provides the strongest guarantee of schema-compliant output?

A) Define an extraction tool with a JSON schema as its input parameters and use `tool_use` — this guarantees syntactically valid JSON matching the schema, eliminating parsing errors.

B) Add "Respond only in JSON format" to the system prompt and parse the response.

C) Use `--output-format json` with the API call.

D) Define the schema in the system prompt and ask Claude to validate its own output before responding.

**Correct Answer: A**

**Explanation:** Tool use (`tool_use`) with JSON schemas is the most reliable approach for guaranteed schema-compliant structured output. The model's response is constrained to produce valid JSON matching the defined schema, eliminating syntax errors entirely. Option B relies on prompt compliance, which may produce extra text, markdown formatting, or malformed JSON. Option C is a CLI flag for Claude Code, not the API. Option D adds a self-validation step that is probabilistic.

---

### Q4.3.2 — Scenario: Structured Data Extraction
**Difficulty: Medium | Knowledge: Schema compliance doesn't prevent semantic errors**

Your extraction tool produces syntactically valid JSON matching the schema for every document. However, quality audits reveal issues: line item amounts don't sum to the stated total, customer names appear in the address field, and dates are in the wrong fields. Why does tool_use with a strict schema not prevent these errors?

A) Strict JSON schemas via tool use eliminate syntax errors but do not prevent semantic errors — values can be placed in wrong fields, calculations can be inconsistent, and logical relationships between fields can be violated. Semantic validation requires additional validation logic.

B) The schema definitions are too permissive — using stricter type constraints would prevent field confusion.

C) The model needs more context about what each field means — adding descriptions to schema fields would fix semantic errors.

D) These errors indicate the model is too small for the task — a larger model would understand field semantics better.

**Correct Answer: A**

**Explanation:** This is a key distinction: tool_use with schemas guarantees structural compliance (valid JSON, correct field names, correct types) but not semantic correctness (right values in right fields, mathematical consistency, logical relationships). Semantic validation — checking that line items sum correctly, that fields contain contextually appropriate values — requires separate validation logic. Option B may help marginally but can't prevent all semantic errors. Option C could improve results but isn't a guarantee. Option D misidentifies the root cause.

---

### Q4.3.3 — Scenario: Structured Data Extraction
**Difficulty: Medium | Skill: Designing schemas with optional/nullable fields**

Your extraction tool processes documents where some fields may not be present in every document. Currently, all fields are marked `required` in the schema. When a document doesn't contain a phone number, the model fabricates one to satisfy the required constraint. How should you redesign the schema?

A) Design fields as optional (nullable) when the source document may not contain the information — this signals to the model that returning `null` is acceptable, preventing it from fabricating values to satisfy required field constraints.

B) Add a prompt instruction: "Return empty strings for missing fields."

C) Keep all fields required but add a `confidence` score alongside each field.

D) Remove optional fields from the schema entirely so the model only extracts what's present.

**Correct Answer: A**

**Explanation:** Making fields nullable when information may be absent is a key schema design principle. Required fields create pressure for the model to produce a value, which leads to hallucination when the information doesn't exist. Nullable fields explicitly permit `null`, signaling that absence is an acceptable response. Option B uses empty strings, which are semantically ambiguous (empty vs missing). Option C adds confidence but doesn't prevent fabrication. Option D loses valuable data when the information is present in most documents.

---

### Q4.3.4 — Scenario: Structured Data Extraction
**Difficulty: Medium | Skill: Enum with "other" + detail pattern**

Your extraction tool categorizes documents into types: invoice, receipt, purchase_order, and contract. Occasionally it encounters a document type not in the enum — such as a packing slip or a delivery confirmation — and misclassifies it as the closest match. How should you extend the schema?

A) Add an `"other"` enum value paired with a `detail_string` field — for example, `{type: "other", type_detail: "packing slip"}`. This provides extensible categorization without requiring schema updates for every new document type.

B) Expand the enum to include all possible document types you can anticipate.

C) Remove the enum constraint and allow free-text document type classification.

D) Add a "confidence" field and have the model report low confidence when the type doesn't match an enum value.

**Correct Answer: A**

**Explanation:** The `"other"` + detail string pattern provides extensible categorization. Known types get clean enum classification; unknown types get `"other"` with a descriptive detail field that can be analyzed for future enum additions. Option B requires constant schema updates and will always miss edge cases. Option C loses the standardization benefit of enums. Option D doesn't solve the misclassification — the model still has to pick an incorrect enum value.

---

### Q4.3.5 — Scenario: Structured Data Extraction
**Difficulty: Hard | Skill: Format normalization alongside strict schemas**

Your extraction pipeline processes invoices where dollar amounts appear in various formats: "$1,234.56", "1234.56 USD", "US$1,234.56", "$1.234,56" (European format). Your schema defines amount fields as numbers. The model sometimes extracts the wrong value (interpreting European "1.234,56" as 1.23456 instead of 1234.56). How do you handle this?

A) Include format normalization rules in the prompt alongside the strict output schema — for example, "Dollar amounts may appear as '$1,234.56', '1234.56 USD', or '1.234,56' (European format with period as thousands separator). Always normalize to a plain number: 1234.56. When in doubt about the format, use the document's country of origin or currency symbol to determine the convention."

B) Change the schema to accept amounts as strings and normalize them in post-processing.

C) Add few-shot examples showing each format variant and the expected numeric output.

D) Use a regex pre-processor to normalize all amount formats before extraction.

**Correct Answer: A**

**Explanation:** Format normalization rules in the prompt complement the strict output schema. The prompt explains how to interpret different formats, and the schema ensures the output is a clean number. This combination handles the full pipeline. Option B pushes complexity to post-processing and loses the benefit of schema-enforced types. Option C (few-shot examples) would also help and could be used in combination, but without explicit normalization rules the model may not generalize to unseen format variants. Option D risks corrupting non-amount numbers in the document.

---

### Q4.3.6 — Scenario: Structured Data Extraction
**Difficulty: Medium | Skill: Handling ambiguity with "unclear" enum values**

Your extraction tool categorizes the urgency of support tickets. Most tickets clearly fall into "low", "medium", or "high" urgency. However, some tickets have ambiguous urgency signals — for example, "my internet is slow but it's not a big deal, although I do work from home." The model forces these into one of the three categories inconsistently. How should you improve the schema?

A) Add an `"unclear"` enum value for genuinely ambiguous cases — this lets the model signal ambiguity instead of forcing a potentially incorrect classification, and downstream routing can apply default handling for unclear urgency.

B) Default all ambiguous cases to "medium" as a compromise.

C) Add a `confidence` field and filter out low-confidence classifications.

D) Add detailed classification criteria for every possible ambiguity pattern.

**Correct Answer: A**

**Explanation:** Adding an `"unclear"` enum value for ambiguous cases is a schema design best practice. It acknowledges that not every input maps cleanly to a defined category, letting the model express genuine uncertainty instead of forcing a potentially wrong answer. Downstream systems can apply default handling for unclear cases. Option B creates a catch-all that obscures the ambiguity. Option C adds noise without resolving the classification. Option D tries to eliminate ambiguity through exhaustive rules, which is impractical.

---

## Task Statement 4.4: Implement validation, retry, and feedback loops for extraction quality

### Q4.4.1 — Scenario: Structured Data Extraction
**Difficulty: Easy | Knowledge: Retry-with-error-feedback pattern**

Your extraction pipeline extracts an invoice total of $1,500 but the sum of line items equals $1,350. The first extraction attempt has failed semantic validation. What is the correct retry approach?

A) Send a follow-up request including the original document, the failed extraction, and the specific validation error ("Line item total $1,350 does not match stated total $1,500 — please re-examine the document for missing line items or calculation errors") to guide the model toward correction.

B) Simply re-run the same extraction prompt without changes, hoping for a different result.

C) Automatically correct the total to $1,350 in post-processing since the line items are more likely to be accurate.

D) Flag the document as unprocessable and route it to human review immediately.

**Correct Answer: A**

**Explanation:** Retry-with-error-feedback appends specific validation errors to the prompt, giving the model targeted guidance about what went wrong. This enables self-correction — the model may discover a missing line item, a discount, or a tax calculation it initially missed. Option B retries without new information, likely producing the same error. Option C makes an assumption about which value is correct without evidence. Option D gives up prematurely when a retry could resolve the discrepancy.

---

### Q4.4.2 — Scenario: Structured Data Extraction
**Difficulty: Medium | Knowledge: When retries are ineffective**

Your extraction pipeline processes lease agreements and attempts to extract the landlord's phone number. The document doesn't contain any phone number — it only lists a mailing address. The extraction returns `null` for the phone field. Your validation rule flags this as a failure and triggers a retry. After three retries, the model fabricates a plausible phone number. What went wrong?

A) Retries are ineffective when the required information is simply absent from the source document. The validation logic should distinguish between "extraction failed" (format/structural errors worth retrying) and "information not present" (the model correctly returned null). Retrying forces the model to fabricate a value.

B) The phone field should be marked as required in the schema to force the model to search harder.

C) The retry prompt should be more forceful: "The phone number MUST be in the document. Look more carefully."

D) Three retries is too many — a single retry would have been sufficient to avoid fabrication.

**Correct Answer: A**

**Explanation:** This illustrates a critical limit of retries: they're ineffective when the information doesn't exist in the source. Retrying tells the model "your answer was wrong, try again," which pressures it to produce something — leading to fabrication. Validation logic must distinguish between extractable-but-missed information (worth retrying) and absent information (where null is correct). Option B makes fabrication more likely by requiring the field. Option C increases fabrication pressure. Option D misidentifies the count as the problem when any retry was inappropriate here.

---

### Q4.4.3 — Scenario: Claude Code for Continuous Integration
**Difficulty: Medium | Skill: Tracking dismissal patterns with detected_pattern**

Your CI review pipeline generates findings that developers can dismiss with a reason. You want to systematically improve the pipeline by analyzing why findings are dismissed. What structural addition to the findings output enables this analysis?

A) Add a `detected_pattern` field to each finding that records which code construct triggered it (e.g., "try-catch-empty-body", "string-concatenation-in-query", "unused-import"). When developers dismiss findings, you can aggregate by `detected_pattern` to identify which patterns have high dismissal rates and improve or disable those specific detections.

B) Add a `confidence_score` field and correlate dismissals with low-confidence findings.

C) Log the full code context around each finding so dismissed findings can be manually reviewed.

D) Track dismissal rates per developer to identify who dismisses the most findings.

**Correct Answer: A**

**Explanation:** `detected_pattern` fields create a structured feedback loop. By recording which code pattern triggered each finding, you can aggregate dismissal data by pattern rather than by individual finding. If "unused-import" has a 90% dismissal rate, you know that specific detection needs improvement or removal. Option B correlates with confidence, which is poorly calibrated. Option C provides manual review capability but not systematic analysis. Option D measures developer behavior rather than pipeline accuracy.

---

### Q4.4.4 — Scenario: Structured Data Extraction
**Difficulty: Hard | Skill: Self-correction validation flows**

Your extraction pipeline processes financial documents. An invoice states "Total: $1,500" but the line items sum to $1,350. You want the extraction to detect and flag this discrepancy rather than silently choosing one value. How should you design the extraction schema?

A) Include both `stated_total` and `calculated_total` fields in the schema, plus a `conflict_detected` boolean. The model extracts the document's stated total, independently sums the line items, and flags when they disagree — letting downstream systems or human reviewers decide how to reconcile.

B) Include only a `total` field and validate it against the line item sum in post-processing.

C) Include a `total` field with a `source` enum ("stated" or "calculated") to indicate which value was used.

D) Include a `total` field and a `notes` free-text field where the model can describe any discrepancies.

**Correct Answer: A**

**Explanation:** Extracting both values plus a conflict flag is the recommended self-correction pattern. It preserves all information from the document (the stated total and the calculated sum), explicitly signals discrepancies, and defers reconciliation to systems or humans with broader context. Option B catches the discrepancy in post-processing but doesn't preserve the model's calculation. Option C forces a choice between values. Option D uses unstructured notes that are hard to process systematically.

---

### Q4.4.5 — Scenario: Structured Data Extraction
**Difficulty: Medium | Skill: Identifying retryable vs non-retryable errors**

Your pipeline processes a batch of 100 documents. Validation catches errors in 15 documents. Categorize which are worth retrying:

**Error Type X:** The model extracted dates in "MM/DD/YYYY" format instead of the required "YYYY-MM-DD" format.
**Error Type Y:** The model returned empty arrays for the "signatories" field — the documents are cover pages that don't contain signatory information.
**Error Type Z:** The model extracted a table as a single string instead of an array of row objects.

Which errors are retryable?

A) X and Z are retryable (format mismatches and structural output errors that can be corrected with feedback). Y is not retryable (the information is absent from the source documents, so retrying will either return empty again or fabricate data).

B) All three are retryable — the model should try harder on each.

C) Only X is retryable — Z requires a schema change and Y requires different documents.

D) None are retryable — all should be routed to human review.

**Correct Answer: A**

**Explanation:** The distinction between retryable and non-retryable errors is critical for efficient pipeline operation. Format mismatches (X: date format) and structural errors (Z: table as string vs array) are correctable — the model has the information but output it incorrectly, so retry-with-feedback can guide correction. Absent information (Y: no signatories on cover pages) is non-retryable — the information doesn't exist, and retrying pressures fabrication. Option B wastes resources on non-retryable errors. Option C underestimates Z's retryability. Option D is unnecessarily conservative.

---

## Task Statement 4.5: Design efficient batch processing strategies

### Q4.5.1 — Scenario: Structured Data Extraction
**Difficulty: Easy | Knowledge: Message Batches API basics**

Your team processes 10,000 invoices weekly. Currently this costs $2,000 using the synchronous API. Your manager asks about the Message Batches API. Which statement about it is accurate?

A) The Message Batches API offers 50% cost savings (reducing the weekly cost to ~$1,000) with a processing window of up to 24 hours — it's designed for latency-tolerant, non-blocking workloads.

B) The Message Batches API offers 25% cost savings with guaranteed same-hour processing.

C) The Message Batches API offers 50% cost savings with guaranteed 1-hour processing.

D) The Message Batches API offers 75% cost savings but requires a minimum batch size of 10,000 requests.

**Correct Answer: A**

**Explanation:** The Message Batches API provides 50% cost savings with an up-to-24-hour processing window and no guaranteed latency SLA. This makes it ideal for overnight or weekly batch jobs where latency isn't critical. Options B, C, and D state incorrect savings percentages, processing guarantees, or minimum requirements.

---

### Q4.5.2 — Scenario: Claude Code for Continuous Integration
**Difficulty: Medium | Knowledge: Batch API appropriateness for workflow types**

Your CI/CD system has two automated Claude workflows: (1) a pre-merge security check that blocks merging until complete, and (2) a weekly codebase quality audit that runs overnight Saturday and is reviewed Monday morning. Your team wants to reduce costs. Which workflows should use the Batch API?

A) Only the weekly quality audit — it's a non-blocking, latency-tolerant workload suitable for batch processing. The pre-merge check must remain synchronous because developers are blocked waiting for results.

B) Both workflows — use batch for the weekly audit and batch with status polling for the pre-merge check.

C) Neither — batch processing doesn't support the tool calling needed for code analysis.

D) Only the pre-merge check — batch processing's faster throughput helps unblock developers sooner.

**Correct Answer: A**

**Explanation:** The Batch API's up-to-24-hour processing window with no latency SLA makes it unsuitable for blocking workflows where developers wait for results. The weekly audit runs overnight and is reviewed Monday — a perfect fit for batch processing at 50% savings. Option B uses batch for a blocking workflow, which risks developers waiting hours to merge. Option C is incorrect — the batch API's limitation is on multi-turn tool calling, not all tool calling. Option D reverses the suitability — batch processing doesn't guarantee faster throughput.

---

### Q4.5.3 — Scenario: Structured Data Extraction
**Difficulty: Hard | Skill: Calculating batch submission frequency**

Your SLA guarantees document processing within 30 hours of submission. You use the Message Batches API with its up-to-24-hour processing window. Documents arrive continuously throughout the day. What is the maximum interval between batch submissions to guarantee the SLA?

A) Submit batches every 6 hours — this ensures the worst-case scenario (a document arriving just after a batch closes) faces at most 6 hours of waiting plus 24 hours of processing = 30 hours total, meeting the SLA.

B) Submit batches every 24 hours — one batch per day covers the 30-hour SLA with 6 hours of margin.

C) Submit batches every 12 hours — this provides sufficient margin within the 30-hour SLA.

D) Submit in real-time as documents arrive to minimize latency.

**Correct Answer: A**

**Explanation:** Working backward from the SLA: 30-hour SLA minus 24-hour maximum processing time = 6 hours maximum wait time. A document arriving just after a batch submission must wait at most 6 hours for the next batch, then up to 24 hours for processing, totaling 30 hours. Option B (24-hour intervals) means a worst-case wait of 24 + 24 = 48 hours. Option C (12-hour intervals) means 12 + 24 = 36 hours, exceeding the SLA. Option D isn't batch processing and doesn't capture cost savings.

---

### Q4.5.4 — Scenario: Structured Data Extraction
**Difficulty: Medium | Skill: Handling batch failures with custom_id**

Your batch submission of 500 documents completes, but 23 documents failed: 15 because they exceeded context limits and 8 because of transient API errors. How should you handle resubmission?

A) Resubmit only the 23 failed documents (identified by their `custom_id` values) with appropriate modifications — chunk the 15 oversized documents into smaller sections, and resubmit the 8 transient failures without modification. Don't reprocess the 477 successful documents.

B) Resubmit the entire batch of 500 documents to ensure consistency.

C) Manually review all 23 failed documents and process them through the synchronous API.

D) Discard the 23 failed documents and report a 95.4% success rate.

**Correct Answer: A**

**Explanation:** `custom_id` fields correlate batch requests with responses, enabling targeted resubmission of only failed documents. The two failure types require different handling: oversized documents need chunking (structural fix), while transient failures can be resubmitted as-is. Option B wastes resources reprocessing 477 successful documents. Option C bypasses the cost savings of batch processing. Option D accepts data loss unnecessarily.

---

### Q4.5.5 — Scenario: Structured Data Extraction
**Difficulty: Medium | Skill: Prompt refinement before batch processing**

You have 5,000 documents to process in a batch. Before submitting the full batch, what preparatory step would maximize first-pass success rates?

A) Run prompt refinement on a representative sample set (50-100 documents across different types and edge cases) to identify and fix extraction issues before batch-processing the full 5,000, reducing iterative resubmission costs.

B) Submit the full 5,000 immediately to identify all failure patterns at once.

C) Process 10 documents synchronously to verify the prompt works, then batch the rest.

D) Run the batch with a simpler, faster model first, then reprocess failures with the full model.

**Correct Answer: A**

**Explanation:** Prompt refinement on a representative sample (covering different document types, formats, and edge cases) identifies and fixes extraction issues before committing to the full batch. This maximizes first-pass success rates and minimizes the costly cycle of batch submission → failure analysis → prompt fixing → resubmission. Option B discovers problems at full cost. Option C's sample of 10 is too small to be representative. Option D adds complexity and doesn't improve the prompt.

---

### Q4.5.6 — Scenario: Structured Data Extraction
**Difficulty: Medium | Knowledge: Batch API multi-turn limitation**

Your extraction pipeline normally uses a multi-turn flow: first call extracts data, the response is validated, and if validation fails, a follow-up call includes the errors for self-correction. Can you implement this same flow in a batch request?

A) No — the Batch API does not support multi-turn tool calling within a single request. You cannot execute tools mid-request and return results. For batch processing, you must design single-turn extraction prompts and handle validation retries as separate batch submissions.

B) Yes — the Batch API supports the same multi-turn flows as the synchronous API.

C) Yes, but only if you use the `tool_choice: "any"` option to force tool execution.

D) No, but you can simulate it by including all possible validation errors and corrections in the initial prompt.

**Correct Answer: A**

**Explanation:** The Batch API does not support multi-turn tool calling within a single request — it processes each request as a single turn. This means validation-retry loops must be restructured: the initial extraction runs as a batch, failures are identified from the results, and retries are submitted as a separate batch with error feedback included in each prompt. Option B is factually incorrect. Option C confuses tool_choice configuration with multi-turn capability. Option D describes an impractical approach.

---

## Task Statement 4.6: Design multi-instance and multi-pass review architectures

### Q4.6.1 — Scenario: Claude Code for Continuous Integration
**Difficulty: Easy | Knowledge: Self-review limitations**

Your pipeline generates code and then asks the same Claude instance to review it in the same conversation. The review rarely finds issues, even when an independent human reviewer identifies clear problems. Why?

A) The model retains reasoning context from generation, making it less likely to question its own decisions — it's anchored to the logic it used to generate the code, reducing its ability to spot flaws in that same session.

B) The model has a built-in bias toward approving its own work.

C) Self-review uses the same temperature as generation, producing identical reasoning paths.

D) The review prompt needs to be more specific about what types of issues to look for.

**Correct Answer: A**

**Explanation:** Self-review in the same session suffers from context anchoring. The model retains its generation reasoning — why it made each decision, what tradeoffs it considered — and this context makes it less likely to question those decisions. An independent review instance without this context approaches the code fresh. Option B overstates the issue as bias rather than context anchoring. Option C misattributes the cause to temperature. Option D may help marginally but doesn't address the fundamental context anchoring problem.

---

### Q4.6.2 — Scenario: Claude Code for Continuous Integration
**Difficulty: Medium | Skill: Independent review instances**

You want to add an automated code review step after Claude Code generates code. How should you structure it to maximize review effectiveness?

A) Use a second, independent Claude instance to review the generated code — this instance receives only the code and review criteria, without the generation conversation or reasoning context, enabling it to evaluate the code with fresh perspective.

B) Add a review step at the end of the generation session with "Now review the code you just wrote for bugs and security issues."

C) Use extended thinking mode in the generation session to have the model think more carefully about potential issues during generation.

D) Run the generation session twice and compare the outputs, flagging differences as potential issues.

**Correct Answer: A**

**Explanation:** An independent review instance without prior reasoning context is more effective at catching subtle issues than self-review. By providing only the code and review criteria (not the generation conversation), the reviewer approaches the code without anchoring to the generator's decisions. Option B is self-review in the same session — the least effective approach. Option C improves generation quality but doesn't replace independent review. Option D detects non-determinism, not necessarily bugs.

---

### Q4.6.3 — Scenario: Claude Code for Continuous Integration
**Difficulty: Hard | Skill: Multi-pass review for large PRs**

A pull request modifies 14 files across three modules: authentication (5 files), payment processing (6 files), and shared utilities (3 files). A single-pass review of all 14 files produces shallow, inconsistent feedback. How should you structure a multi-pass review?

A) Run three focused local analysis passes — one per module — examining each module's files for internal correctness (type safety, error handling, business logic). Then run a separate cross-module integration pass examining how authentication changes interact with payment processing, how shared utility changes affect both consumers, and whether API contracts between modules remain consistent.

B) Run 14 individual file reviews (one per file) and aggregate the findings.

C) Run three passes by review type: first all files for security, then all files for correctness, then all files for style.

D) Run a single pass with a larger context window model to give all 14 files adequate attention.

**Correct Answer: A**

**Explanation:** Multi-pass review splits by natural boundaries: module-level local passes ensure depth within each concern area, and a cross-module integration pass catches interactions between modules. This addresses attention dilution while specifically checking the integration points that single-module reviews miss. Option B reviews files in isolation without module context or cross-file analysis. Option C still processes all files simultaneously per pass, not solving the attention problem. Option D misunderstands that attention quality doesn't scale linearly with context window size.

---

### Q4.6.4 — Scenario: Claude Code for Continuous Integration
**Difficulty: Medium | Skill: Confidence-based review routing**

Your pipeline generates review findings but you have limited human reviewer capacity. You want to use Claude's self-reported confidence to route findings efficiently. How should you use confidence scores?

A) Run a verification pass where the model self-reports confidence alongside each finding, then route low-confidence findings to human review while auto-applying high-confidence findings — but calibrate the confidence thresholds using a labeled validation set to ensure self-reported scores correlate with actual accuracy.

B) Trust the model's confidence scores directly — auto-apply anything above 0.8 confidence and send the rest to humans.

C) Ignore confidence scores entirely since model self-assessment is unreliable.

D) Use confidence scores only for display purposes, routing all findings to human review.

**Correct Answer: A**

**Explanation:** Self-reported confidence can be useful for routing if it's calibrated against actual performance. Running a verification pass that produces confidence scores, then validating those scores against labeled ground truth, lets you set meaningful thresholds. The key is calibration — uncalibrated confidence scores (Option B) are unreliable. Option C discards a potentially useful signal. Option D wastes limited human reviewer capacity on clearly correct findings.

---

### Q4.6.5 — Scenario: Claude Code for Continuous Integration
**Difficulty: Easy | Knowledge: When multi-pass review is needed vs single-pass**

Your CI pipeline reviews PRs. For small PRs (1-3 files, under 200 lines changed), a single review pass produces thorough, consistent feedback. A colleague proposes switching all reviews to multi-pass (separate security, logic, and style passes) for consistency. What is the best response?

A) Keep single-pass for small PRs and reserve multi-pass for larger PRs — small PRs don't suffer from attention dilution, so multi-pass adds latency and cost without improving review quality.

B) Switch everything to multi-pass for consistency across all PR sizes.

C) Use multi-pass only for PRs flagged as security-sensitive, regardless of size.

D) Always use single-pass and increase the model's context window to handle larger PRs.

**Correct Answer: A**

**Explanation:** Multi-pass review architectures solve attention dilution, which is primarily a problem with larger, more complex PRs. For small PRs where the model can attend to all content effectively in a single pass, adding multiple passes increases latency and API costs without meaningful quality improvement. Option B imposes unnecessary overhead on simple cases. Option C uses security sensitivity rather than complexity as the criterion, but attention dilution is driven by PR size, not topic. Option D ignores that context window size doesn't solve attention dilution.

---

## Cross-Domain Questions (Domain 4 intersecting with other domains)

### Q4.X.1 — Scenario: Structured Data Extraction
**Difficulty: Hard | Domains: 4 (Prompt Engineering) + 5 (Context Management)**

Your extraction pipeline processes 50-page financial reports. You have a tool_use schema for structured extraction. Two problems interact: (1) the model consistently misses data from pages 20-35 (the middle of the document), and (2) when you add few-shot examples to improve extraction quality, the combined prompt + document + examples approach the context limit. How do you address both?

A) Split extraction into sections (pages 1-20, 21-40, 41-50) with each section processed independently using the tool_use schema. Place a key findings summary from prior sections at the beginning of each subsequent section's prompt. Trim few-shot examples to 2 focused examples rather than 4, prioritizing examples that demonstrate the extraction patterns most relevant to financial data.

B) Use a larger context window model to accommodate both the full document and all few-shot examples.

C) Remove few-shot examples to save context space, relying solely on the schema definition and prompt instructions.

D) Process the full document in a single pass but add "Pay special attention to pages 20-35" to the prompt.

**Correct Answer: A**

**Explanation:** This addresses both problems: sectioned processing mitigates the "lost in the middle" effect by ensuring each section is close to the beginning or end of its input. Carrying key findings summaries forward maintains coherence across sections. Trimming to 2 focused few-shot examples preserves extraction quality while saving context space. Option B may not be available and doesn't solve the attention quality issue. Option C sacrifices extraction quality. Option D is a prompt instruction that doesn't reliably fix a cognitive limitation.

---

### Q4.X.2 — Scenario: Claude Code for Continuous Integration
**Difficulty: Hard | Domains: 4 (Prompt Engineering) + 3 (Claude Code Config) + 1 (Architecture)**

Your CI pipeline needs three capabilities: (1) per-file code review with few-shot examples for consistent findings, (2) cross-file integration analysis that catches data flow issues, and (3) automated test generation for new code using project fixtures. Currently all three run in a single Claude Code invocation that produces inconsistent quality. How should you restructure?

A) Split into three independent passes, each with focused configuration: a review pass with few-shot examples and explicit severity criteria in a review-specific CLAUDE.md section, an integration pass that receives summaries of per-file findings plus the cross-file dependency context, and a test generation pass with existing test files in context and testing standards documented in CLAUDE.md. Each pass uses `-p` for non-interactive execution with `--output-format json` for structured output.

B) Run all three in sequence within a single session so each pass benefits from the context of the previous pass.

C) Create three separate CLAUDE.md files (one per pass) and switch between them using session management.

D) Use a coordinator agent that delegates each pass to a subagent, running all three in parallel.

**Correct Answer: A**

**Explanation:** Three independent passes ensure each task gets focused attention without competing for context space. Each pass has its own configuration (few-shot examples for review, dependency context for integration, test files for generation) and produces structured output. Running them independently also avoids the self-review problem — the test generation pass doesn't share context with the code generation that produced the code being tested. Option B creates context competition and anchoring effects. Option C misuses session management for configuration switching. Option D adds orchestration complexity without benefit for a CI pipeline that can simply run three sequential commands.

---

*End of Domain 4 Question Bank*
*Total: 35 questions covering all 6 task statements + 2 cross-domain questions*
*Distribution: 7 Easy, 19 Medium, 9 Hard*