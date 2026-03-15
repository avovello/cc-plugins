---
name: domain-4-prompt-engineering
description: Training curriculum for Domain 4: Prompt Engineering & Structured Output (20% of exam). Use when /training targets this domain.
---

# Domain 4: Prompt Engineering & Structured Output (20%)

## Overview
- Weight: 20% of scored content
- Task statements: 4.1–4.6
- Primary scenarios: Claude Code for Continuous Integration, Structured Data Extraction, Developer Productivity with Claude
- Cross-domain connections: Agentic Architecture (multi-pass review decomposition, hook-based enforcement vs. prompt-based), Tool Design (tool_choice configuration for structured output, tool descriptions), Claude Code Configuration (CI prompt design, CLAUDE.md review criteria), Context Management (confidence calibration, human review workflows)

## Task Statement 4.1: Design prompts with explicit criteria to improve precision and reduce false positives

### Key Concepts

Vague instructions like "be conservative" or "only report high-confidence findings" are ineffective at improving precision because they do not define what constitutes a reportable issue. The model interprets "high confidence" differently across contexts, so the instruction has no consistent effect. The fix is explicit categorical criteria: define which categories of issues to report (bugs, security vulnerabilities, data loss risks) and which to skip (minor style preferences, local naming patterns, debatable design choices).

The relationship between false positive rates and developer trust is multiplicative, not additive. If one category generates many false positives, developers lose confidence in all categories — even accurate ones. This means a single high-false-positive category can undermine the entire review system. The tactical fix is temporarily disabling high-false-positive categories while improving their prompts, preserving trust in the accurate categories.

Explicit severity criteria with concrete code examples for each level achieve consistent classification. Instead of "flag severe issues," provide examples: "Critical: null pointer dereference in production path. High: unvalidated user input passed to SQL query. Medium: missing error handling on API call. Skip: variable could be const, comment is slightly outdated."

### Common Misconceptions

- **"Be conservative" is a valid precision improvement**: It is not. Without categorical criteria, the model has no consistent way to interpret "conservative." This is the single most tested misconception in this task statement.
- **Confidence thresholds fix false positives**: Adding a confidence parameter (e.g., "only report findings above 80% confidence") sounds precise but is equally vague — the model's self-reported confidence is not well-calibrated to actual accuracy.
- **Prompt placement affects precision**: Putting "be conservative" at the beginning versus the end of the prompt has minimal effect. The problem is the vagueness of the instruction, not its position.
- **Reducing sensitivity reduces false positives without cost**: Distractors that claim "conservative settings reduce both false positives and true positives equally" oversimplify — well-designed categorical criteria reduce false positives while preserving true positives.

### Practice Focus

Whenever a question mentions false positives in a review or CI pipeline, look for the answer that provides specific categorical criteria (what to flag, what to skip, with examples). Reject answers that use vague qualifiers ("conservative," "high-confidence," "important issues only") without defining those terms concretely.

## Task Statement 4.2: Apply few-shot prompting to improve output consistency and quality

### Key Concepts

Few-shot examples are the most effective technique for achieving consistently formatted, actionable output when detailed instructions alone produce inconsistent results. They work by demonstrating the desired behavior rather than describing it — the model generalizes from concrete examples rather than interpreting abstract instructions.

Few-shot examples are particularly powerful for ambiguous-case handling. When the correct action for a given input is not obvious (e.g., "should the agent call get_customer or lookup_order when the user provides both a name and an order number?"), examples that show the reasoning for why one action was chosen over plausible alternatives teach the model judgment that instructions alone cannot convey.

For extraction tasks, few-shot examples reduce hallucination by demonstrating correct handling of varied document structures: inline citations versus bibliographies, methodology sections versus embedded details, informal measurements versus standardized units. When the model sees how to handle these variations in examples, it generalizes to novel documents rather than fabricating values.

The format of few-shot examples matters. Each example should demonstrate the complete desired output format (location, issue, severity, suggested fix) so the model produces consistent structure across all findings. 2-4 targeted examples covering distinct ambiguous scenarios are more effective than many examples of obvious cases.

### Common Misconceptions

- **More examples are always better**: 2-4 targeted examples of ambiguous cases are more effective than 10+ examples of obvious cases. Quality and diversity of examples matter more than quantity.
- **Few-shot examples only help with formatting**: They also teach judgment, disambiguation, and edge case handling. Format consistency is a bonus, not the primary benefit.
- **Detailed instructions can replace few-shot examples**: For complex judgment calls, instructions produce inconsistent interpretation. Examples produce consistent behavior because they demonstrate rather than describe.
- **Examples must cover every possible case**: Few-shot examples enable generalization. A few well-chosen examples covering distinct patterns allow the model to handle novel variations it has not seen.
- **Few-shot examples prevent all errors**: They significantly reduce errors on patterns similar to the examples but cannot guarantee perfect output on all inputs. They are probabilistic, not deterministic.

### Practice Focus

Questions present a consistency or quality problem and ask for the most effective fix. If the issue is inconsistent formatting, ambiguous case handling, or extraction errors on varied document structures, few-shot examples are the answer. Watch for distractors that propose more detailed instructions or confidence thresholds — these are less effective than examples for consistency problems.

## Task Statement 4.3: Enforce structured output using tool use and JSON schemas

### Key Concepts

Tool use (`tool_use`) with JSON schemas is the most reliable approach for guaranteed schema-compliant structured output. When you define a tool with a JSON schema as its input, Claude must produce output matching that schema — eliminating JSON syntax errors entirely. This is more reliable than asking Claude to "output JSON" in a prompt, which can produce malformed JSON.

The `tool_choice` settings control tool invocation behavior: `"auto"` lets the model choose whether to call a tool or respond with text (the model may skip the tool entirely); `"any"` forces the model to call a tool but lets it choose which one (useful when multiple extraction schemas exist for different document types); forced selection (`{"type": "tool", "name": "extract_metadata"}`) ensures a specific tool is called (useful for enforcing extraction order).

A critical distinction: strict JSON schemas eliminate syntax errors but do not prevent semantic errors. Line items that do not sum to their stated total, values placed in the wrong fields, or fabricated data all pass schema validation but are semantically wrong. Semantic validation requires additional logic beyond the schema.

Schema design considerations: use `required` versus optional fields intentionally. When source documents may not contain certain information, make those fields optional (nullable) to prevent the model from fabricating values to satisfy required field constraints. Use enum fields with an "other" option plus a detail string for extensible categories — this captures edge cases that predefined enums cannot anticipate. Add "unclear" as an enum value for genuinely ambiguous cases.

### Common Misconceptions

- **tool_choice "auto" guarantees structured output**: "auto" allows the model to return text instead of calling a tool. Only "any" or forced selection guarantees a tool call (and therefore schema-compliant output).
- **JSON schemas prevent all output errors**: Schemas prevent syntax errors. Semantic errors (wrong values, fabricated data, inconsistent totals) require separate validation.
- **All fields should be required**: Making fields required when the source data may not contain them forces the model to fabricate values. Optional fields with null defaults are safer.
- **Enums should cover all cases**: A fixed enum without an "other" option forces the model to pick the closest predefined value even when none fits. Always include escape hatches for edge cases.
- **Prompt instructions alone can enforce JSON format**: Prompt-based JSON output is unreliable — the model may produce malformed JSON, wrap it in markdown code blocks, or add explanatory text. Tool use with schemas eliminates these issues.

### Practice Focus

Questions test tool_choice settings (auto vs. any vs. forced), schema design (required vs. optional, enums with escape hatches), and the distinction between syntax and semantic validation. If the scenario needs guaranteed structured output, the answer involves tool_use with schemas, not prompt instructions. If the scenario has fabricated values, the answer involves making fields optional.

## Task Statement 4.4: Implement validation, retry, and feedback loops for extraction quality

### Key Concepts

Retry-with-error-feedback appends specific validation errors to the prompt on retry, guiding the model toward correction. Instead of simply retrying with the same prompt (which often produces the same error), the follow-up request includes the original document, the failed extraction, and specific validation errors. This gives the model targeted information about what went wrong.

The critical limitation of retries: they are ineffective when the required information is simply absent from the source document. If a document does not contain a publication date, retrying the extraction will not produce one — the model will either fabricate a date or return null. Retries are effective for format mismatches (the date was in an unexpected format), structural errors (the model looked in the wrong section), or calculation errors (line items did not sum correctly).

Feedback loop design for review systems includes a `detected_pattern` field in structured findings that records which code construct triggered each finding. When developers dismiss a finding, the pattern is logged, enabling systematic analysis of what the model flags incorrectly. Over time, this data drives prompt improvements for specific false positive patterns.

Self-correction validation flows compare extracted values against internal consistency checks: extracting "calculated_total" alongside "stated_total" to flag discrepancies, adding "conflict_detected" booleans when source data is internally inconsistent. These validations catch semantic errors that schema validation cannot.

### Common Misconceptions

- **Retry with the same prompt fixes extraction errors**: Without error feedback, the model tends to produce the same error. The retry must include what specifically went wrong.
- **Retries work for missing information**: If the information is not in the document, no amount of retrying will extract it. Retries fix format/structural/calculation errors, not absence of data.
- **Schema validation catches all errors**: Schema validation catches syntax errors. Semantic errors (wrong values, inconsistent totals) require separate validation logic.
- **Dismissed findings are just noise**: Dismissed findings contain valuable signal about false positive patterns. Tracking `detected_pattern` enables systematic prompt improvement.
- **Validation should block output entirely**: A better pattern flags discrepancies (with conflict_detected booleans) rather than blocking output. Flagged extractions can be routed to human review.

### Practice Focus

Questions present an extraction error and ask for the best fix. If the error is a format or structural issue, retry with error feedback is the answer. If the information is absent from the source, the answer involves handling the absence gracefully (optional fields, "not found" values) rather than retrying. Watch for questions about feedback loops in review systems — the `detected_pattern` field for systematic analysis is a key concept.

## Task Statement 4.5: Design efficient batch processing strategies

### Key Concepts

The Message Batches API offers 50% cost savings with a processing window of up to 24 hours and no guaranteed latency SLA. This makes it appropriate for non-blocking, latency-tolerant workloads: overnight reports, weekly audits, nightly test generation, bulk document extraction. It is inappropriate for blocking workflows like pre-merge CI checks that need results in minutes.

The batch API does not support multi-turn tool calling within a single request. Each batch request is a single-turn API call — you cannot execute tools mid-request and return results. This means agentic workflows (which require multi-turn tool use) cannot run as batch requests. Pre-process documents and construct complete prompts before batching.

`custom_id` fields correlate batch request/response pairs, enabling you to match results back to their source documents and handle partial failures (resubmitting only failed documents).

Batch submission frequency must account for the processing window. If your SLA requires results within 30 hours and batch processing takes up to 24 hours, you must submit batches with at least 6 hours of lead time. A 4-hour submission window provides margin.

Before batch-processing large volumes, refine prompts on a small sample set. First-pass success rates directly affect cost — fixing prompt issues on 50 documents is far cheaper than fixing them on 5,000.

### Common Misconceptions

- **Batch API for pre-merge checks**: Pre-merge checks are blocking and latency-sensitive. The batch API has no latency guarantee and can take up to 24 hours. Use the synchronous API for pre-merge.
- **Batch supports multi-turn tool use**: It does not. Each batch request is a single-turn call. Agentic workflows requiring tool execution cannot be batched.
- **Submit everything at once and iterate**: Prompt refinement on a sample set before bulk submission is far more cost-effective. Submitting 5,000 documents with a flawed prompt wastes significant budget.
- **Failed batches require full resubmission**: Use `custom_id` to identify which specific documents failed and resubmit only those, with appropriate modifications (e.g., chunking oversized documents).
- **Batch processing is always cheaper**: The 50% cost savings only applies when latency is acceptable. If you need results in minutes, you must use synchronous API at full price.

### Practice Focus

Questions test whether you can match workloads to API approaches. Blocking, latency-sensitive workloads (pre-merge checks, real-time extraction) require synchronous API. Non-blocking, overnight/weekly workloads suit batch API. Watch for questions about batch limitations — no multi-turn tool calling and no latency SLA are the key constraints.

## Task Statement 4.6: Design multi-instance and multi-pass review architectures

### Key Concepts

Self-review has a fundamental limitation: the model retains reasoning context from generation, making it less likely to question its own decisions. Even with "please carefully review your output" instructions or extended thinking, the model's retained context from generating the code biases its review toward confirming its own choices. This is not a prompt engineering problem — it is an architectural limitation of same-session review.

Independent review instances — separate Claude sessions without the generator's reasoning context — are significantly more effective at catching subtle issues. The reviewer starts fresh, examines only the code, and does not have access to the rationale that influenced the code's design. This architectural separation is more impactful than any prompt improvement to self-review.

Multi-pass review splits large reviews into per-file local analysis passes plus cross-file integration passes. Per-file passes catch local issues (bugs, style violations, missing validation) without attention dilution. The cross-file integration pass catches issues that only manifest across file boundaries (data flow mismatches, inconsistent API contracts, missing error propagation). This decomposition avoids the "lost in the middle" problem where the model misses issues in the middle of large reviews.

Verification passes where the model self-reports confidence alongside each finding enable calibrated review routing — high-confidence findings go straight to developers, low-confidence findings get additional review.

### Common Misconceptions

- **Extended thinking fixes self-review bias**: Extended thinking helps with complex reasoning but does not overcome the fundamental bias of reviewing your own output in the same context.
- **Stronger review instructions fix self-review**: "Please review carefully and be critical" does not overcome retained reasoning context. Architectural separation (independent instances) is required.
- **Single-pass review works for large changes**: Reviewing 20+ files in a single pass leads to attention dilution and missed findings, especially in the middle of the review. Multi-pass is essential for large changes.
- **Confidence scores are well-calibrated by default**: Self-reported confidence requires calibration using labeled validation sets. Uncalibrated confidence scores are unreliable for routing decisions.
- **Multi-pass doubles the cost without benefit**: The per-file + integration pattern catches more issues at a cost increase well below 2x (because per-file passes are smaller and cheaper). The quality improvement justifies the cost.

### Practice Focus

Self-review limitation questions appear frequently. If a scenario describes a code generator reviewing its own output and missing issues, the answer is always independent review instances — not better prompts, not extended thinking. For multi-pass questions, remember the decomposition: per-file local passes for local issues, cross-file integration pass for data flow and consistency issues.

## Recurring Themes

Several themes recur across this domain:

- **Specificity over generality**: Explicit criteria outperform vague instructions. Concrete examples outperform abstract descriptions. Targeted few-shot examples outperform lengthy prose. The exam consistently rewards specific, actionable approaches.
- **Architectural solutions over prompt solutions**: Self-review bias, attention dilution, and format reliability are all better addressed architecturally (independent instances, multi-pass decomposition, tool_use schemas) than through prompt engineering alone.
- **Validation is multi-layered**: Schema validation catches syntax errors. Semantic validation catches value errors. Feedback loops catch systematic false positive patterns. No single layer catches everything.
- **Cost-latency tradeoff**: Batch API saves cost but sacrifices latency. Synchronous API provides speed but at full cost. Multi-pass review improves quality but increases cost. Every decision involves tradeoffs that the exam expects you to reason about explicitly.
- **Graceful handling of absence and ambiguity**: Optional schema fields, "unclear" enum values, "other" + detail patterns, conflict_detected booleans — the exam rewards designs that handle edge cases explicitly rather than forcing the model into a predetermined category.
