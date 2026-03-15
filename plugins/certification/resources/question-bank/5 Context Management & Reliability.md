# Domain 5: Context Management & Reliability (15%)
## Comprehensive Question Bank

---

## Task Statement 5.1: Manage conversation context to preserve critical information across long interactions

### Q5.1.1 — Scenario: Customer Support Resolution Agent
**Difficulty: Easy | Knowledge: Progressive summarization risks**

Your support agent handles a long conversation where a customer discusses three issues: a $47.99 refund for order #8812, a billing charge of $12.50 on March 3rd, and a shipping address update. As the conversation grows, your system summarizes earlier turns to save context space. After summarization, the agent refers to "the refund" and "the billing issue" but can no longer recall the specific amounts, order number, or date. What caused this?

A) Progressive summarization condensed numerical values, percentages, dates, and customer-stated expectations into vague summaries — transactional facts were lost because the summarization didn't preserve specific data points.

B) The summarization algorithm has a character limit that truncated the specific values.

C) The model prioritized the most recent conversation turns and deprioritized older factual details.

D) The customer's information was stored in tool results that were excluded from summarization.

**Correct Answer: A**

**Explanation:** Progressive summarization naturally condenses details into abstractions — "$47.99 refund for order #8812" becomes "the refund." This is a known risk: numerical values, dates, order numbers, and specific amounts are exactly the details that matter most for resolution but are most likely to be lost in summarization. Option B describes a mechanical issue rather than the conceptual problem. Option C describes recency bias, which is a different effect. Option D is speculative about implementation details.

---

### Q5.1.2 — Scenario: Customer Support Resolution Agent
**Difficulty: Medium | Skill: Extracting transactional facts into persistent context**

Given the summarization risk in Q5.1.1, how should you preserve critical information across long support conversations?

A) Extract transactional facts (amounts, dates, order numbers, statuses) into a persistent "case facts" block that is included in every prompt outside the summarized conversation history — this structured data survives summarization intact.

B) Increase the context window size to avoid the need for summarization entirely.

C) Instruct the model to "remember all important numbers and dates" in the system prompt.

D) Store all facts in an external database and have the model query it when needed.

**Correct Answer: A**

**Explanation:** A persistent "case facts" block separates critical structured data from the conversation narrative. While conversation history gets summarized, the case facts block is preserved verbatim in every prompt. This ensures amounts, dates, and identifiers remain available regardless of how long the conversation runs. Option B is impractical for very long conversations. Option C is a prompt instruction that doesn't change how summarization works. Option D adds infrastructure and latency for data the model already has.

---

### Q5.1.3 — Scenario: Multi-Agent Research System
**Difficulty: Medium | Knowledge: Lost-in-the-middle effect**

Your research system aggregates findings from five subagents into a single input for the synthesis agent. The aggregated input is 15,000 tokens. Quality analysis shows the synthesis consistently incorporates findings from the first and last subagents but frequently omits or misrepresents findings from the middle three subagents. What phenomenon explains this?

A) The "lost in the middle" effect — models reliably process information at the beginning and end of long inputs but may omit or underweight findings from middle sections.

B) The middle subagents produced lower-quality findings that the synthesis agent correctly deprioritized.

C) The synthesis agent's context window is too small for 15,000 tokens, causing the middle to be truncated.

D) The aggregation order was determined by subagent completion time, and the middle agents happened to produce less relevant findings.

**Correct Answer: A**

**Explanation:** The "lost in the middle" effect is a well-documented phenomenon where models attend more reliably to the beginning and end of long inputs than to the middle. This is a positional effect, not a quality judgment — the middle content could be equally or more important. Option B assumes quality-based filtering without evidence. Option C describes truncation, which would produce a clean cutoff rather than selective omission. Option D attributes the pattern to coincidence.

---

### Q5.1.4 — Scenario: Multi-Agent Research System
**Difficulty: Medium | Skill: Mitigating position effects in aggregated inputs**

How should you restructure the aggregated input from Q5.1.3 to mitigate the lost-in-the-middle effect?

A) Place a key findings summary at the beginning of the aggregated input, and organize detailed results with explicit section headers — this ensures the most important points are in the high-attention beginning position, while headers help the model navigate the detailed sections.

B) Randomize the order of subagent findings each time to distribute the attention loss equally.

C) Limit each subagent's output to 2,000 tokens so the total stays below the attention degradation threshold.

D) Process each subagent's findings in a separate API call to the synthesis agent, accumulating results across turns.

**Correct Answer: A**

**Explanation:** Placing key findings summaries at the beginning leverages the high-attention position for the most critical information. Explicit section headers provide structural anchors that help the model navigate and attend to specific sections even in the middle. Option B distributes the problem instead of solving it. Option C arbitrarily limits subagent output quality. Option D turns a single synthesis into a multi-turn conversation that may lose coherence.

---

### Q5.1.5 — Scenario: Customer Support Resolution Agent
**Difficulty: Hard | Skill: Trimming verbose tool outputs**

Your agent calls `lookup_order` which returns a JSON object with 40+ fields (order ID, customer ID, billing address, shipping address, item details, warehouse codes, internal tracking IDs, fulfillment center, carrier contracts, etc.). For a return request, only 5 fields are relevant: order ID, item name, purchase date, return eligibility, and refund amount. Over a multi-issue conversation with several order lookups, these verbose tool results accumulate and consume significant context. What's the best approach?

A) Trim verbose tool outputs to only relevant fields before they accumulate in context — for example, a PostToolUse hook on `lookup_order` that extracts only return-relevant fields, reducing each lookup from 40+ fields to 5 fields and preventing context bloat across multiple lookups.

B) Summarize tool results after each call by asking the model to extract what's relevant.

C) Increase the context window to accommodate the verbose results.

D) Call `lookup_order` only once per conversation and cache the result for reuse.

**Correct Answer: A**

**Explanation:** Trimming tool outputs via PostToolUse hooks is a proactive, deterministic approach to context management. By reducing each lookup to its relevant fields before the results enter the conversation context, you prevent token accumulation from the start. Option B uses an extra model call for each tool result. Option C doesn't solve the underlying inefficiency. Option D limits the agent's ability to look up multiple orders in multi-issue conversations.

---

### Q5.1.6 — Scenario: Multi-Agent Research System
**Difficulty: Hard | Skill: Modifying upstream agents for downstream context budgets**

Your synthesis subagent has a limited context budget of 8,000 tokens. The web search subagent currently returns verbose content — full article excerpts, reasoning chains about source reliability, and metadata — totaling 12,000 tokens per research topic. How should you restructure the upstream agent's output?

A) Modify the web search subagent to return structured data — key facts, citations, and relevance scores — instead of verbose content and reasoning chains. This gives the synthesis agent the essential information in a compact format that fits within its context budget while preserving attribution.

B) Truncate the web search output to 8,000 tokens before passing it to synthesis.

C) Have the synthesis agent request only the parts it needs from the web search output.

D) Run the synthesis agent with a larger context window to accommodate the full output.

**Correct Answer: A**

**Explanation:** When downstream agents have limited context budgets, upstream agents should produce structured, concise output — separating essential information (key facts, citations, relevance) from verbose content (full excerpts, reasoning chains). This is more effective than truncation (Option B), which may cut important information. Option C requires the synthesis agent to know what's available before seeing it. Option D addresses the symptom rather than the inefficiency.

---

### Q5.1.7 — Scenario: Customer Support Resolution Agent
**Difficulty: Medium | Knowledge: Passing complete conversation history**

Your support agent uses the Claude API for multi-turn conversations. After 10 turns, the agent suddenly loses context about the customer's issue and asks them to re-explain their problem. A developer discovers the API integration was sending only the latest user message, not the full conversation history. Why is this a problem?

A) The Claude API requires the complete conversation history in each subsequent request to maintain conversational coherence — without prior turns, the model has no memory of the conversation and treats each request as new.

B) The API caches conversation history server-side, but a session timeout cleared the cache after 10 turns.

C) The model's context window filled up after 10 turns, causing it to forget earlier messages.

D) The conversation should be split into multiple sessions after 10 turns to prevent context degradation.

**Correct Answer: A**

**Explanation:** The Claude API is stateless — complete conversation history must be included in each request. Without prior turns, the model literally has no information about what was discussed previously. This is a fundamental integration requirement, not a timeout or context window issue. Option B incorrectly assumes server-side state. Option C confuses context window limits with missing history. Option D proposes splitting when the fix is simply including the history.

---

## Task Statement 5.2: Design effective escalation and ambiguity resolution patterns

### Q5.2.1 — Scenario: Customer Support Resolution Agent
**Difficulty: Easy | Knowledge: Appropriate escalation triggers**

Which of the following is NOT an appropriate trigger for escalating to a human agent?

A) The customer's issue is complex but falls clearly within the agent's documented capabilities and available tools.

B) The customer explicitly says "I want to speak to a human agent."

C) The company's return policy doesn't address the customer's specific situation (a policy gap).

D) The agent has attempted multiple approaches but cannot make meaningful progress on the issue.

**Correct Answer: A**

**Explanation:** Appropriate escalation triggers include: customer requests for a human (B), policy gaps where the policy is ambiguous or silent on the specific request (C), and inability to make meaningful progress (D). A complex issue that falls within the agent's capabilities (A) should be handled by the agent — complexity alone isn't a reason to escalate. The agent's value is precisely in handling complex-but-tractable cases autonomously.

---

### Q5.2.2 — Scenario: Customer Support Resolution Agent
**Difficulty: Medium | Knowledge: Honoring explicit customer requests**

A customer asks about a defective product and immediately says "Transfer me to a manager." The agent can clearly see the product is under warranty and the replacement process is straightforward. What should the agent do?

A) Honor the customer's request immediately and escalate to a human agent — when a customer explicitly requests a human, the agent should comply without first attempting to investigate or resolve the issue.

B) Explain that the issue is straightforward and offer to resolve it before escalating, only escalating if the customer insists.

C) Acknowledge the request but first gather basic information (order number, defect details) to include in the handoff summary.

D) Ask the customer why they want a manager to determine if escalation is truly necessary.

**Correct Answer: A**

**Explanation:** When a customer explicitly demands a human agent, the agent should honor this immediately. Attempting to investigate first (B), gathering information first (C), or questioning the request (D) all delay the customer's explicit wish and may increase frustration. The exam guide is clear: honor explicit customer requests for human agents immediately without first attempting investigation. Option C seems reasonable but the priority is compliance with the direct request.

---

### Q5.2.3 — Scenario: Customer Support Resolution Agent
**Difficulty: Medium | Skill: Acknowledging frustration while offering resolution**

A frustrated customer writes: "This is unbelievable! I've been charged twice for order #7744. I want this fixed RIGHT NOW or I'm calling my bank." The issue (a duplicate charge) is within the agent's capability to resolve. Should the agent escalate?

A) No — acknowledge the customer's frustration while offering resolution, since the duplicate charge issue is within the agent's capability. Escalate only if the customer explicitly reiterates their preference for a human after the agent offers to resolve it.

B) Yes — the customer's frustration level indicates the case needs human handling for de-escalation.

C) Yes — the mention of calling the bank represents a financial threat that triggers automatic escalation.

D) No — ignore the emotional language and proceed directly with the technical resolution without acknowledging the frustration.

**Correct Answer: A**

**Explanation:** The customer is frustrated but hasn't requested a human — they want the problem fixed. The agent should acknowledge the frustration ("I understand how frustrating a duplicate charge is") while offering to resolve the specific issue. Escalation is only appropriate if the customer explicitly asks for a human after the agent offers resolution. Option B uses sentiment as a proxy for case complexity, which is unreliable. Option C overinterprets "calling my bank" as an escalation trigger. Option D ignores the emotional component, which may further frustrate the customer.

---

### Q5.2.4 — Scenario: Customer Support Resolution Agent
**Difficulty: Hard | Skill: Escalating on policy gaps**

A customer asks: "Your competitor is selling the same product for $50 less. Can you match their price?" Your company's pricing policy addresses only own-site price adjustments (matching your own lower advertised prices), not competitor price matching. How should the agent handle this?

A) Escalate to a human agent — the policy is ambiguous or silent on competitor price matching, so the agent cannot determine whether this request can be granted. The escalation summary should include the customer's request, the competitor's price, and the relevant policy gap.

B) Decline the request because the policy doesn't explicitly allow competitor price matching.

C) Offer to match the price since good customer service means saying yes whenever possible.

D) Ask the customer to provide a link to the competitor's listing and then make a judgment call.

**Correct Answer: A**

**Explanation:** When policy is ambiguous or silent on a customer's specific request, the agent should escalate rather than making a unilateral decision. Competitor price matching isn't addressed by the policy — it's neither explicitly allowed nor forbidden. A human agent with policy authority should make this determination. Option B interprets silence as denial, which may lose a customer. Option C makes unauthorized concessions. Option D prolongs the interaction when the agent lacks authority to act regardless of evidence.

---

### Q5.2.5 — Scenario: Customer Support Resolution Agent
**Difficulty: Medium | Skill: Handling multiple customer matches**

A customer provides the name "John Smith" and asks about their order status. The `get_customer` tool returns three accounts matching "John Smith." What should the agent do?

A) Ask the customer for additional identifying information (email address, phone number, or order number) to narrow down to the correct account — selecting based on heuristics (most recent activity, matching address) risks acting on the wrong account.

B) Select the account with the most recent activity, as it's most likely the current customer.

C) Show the customer all three accounts and ask them to confirm which one is theirs.

D) Use the customer's IP address or session data to match to the correct account.

**Correct Answer: A**

**Explanation:** Multiple customer matches require clarification through additional identifiers, not heuristic selection. Picking the "most likely" account (B) risks acting on the wrong customer's account — potentially exposing private information or making unauthorized changes. Option C may expose other customers' information. Option D uses technical data that may not be available or reliable (shared IPs, VPNs).

---

### Q5.2.6 — Scenario: Customer Support Resolution Agent
**Difficulty: Hard | Knowledge: Why sentiment and self-confidence are unreliable escalation proxies**

A team proposes two escalation enhancements: (1) automatically escalate when customer sentiment drops below a threshold, and (2) have the agent self-report a confidence score and escalate when confidence is below 6/10. Why are both approaches unreliable?

A) Sentiment doesn't correlate with case complexity — a frustrated customer may have a simple issue, while a calm customer may have a complex edge case. Self-reported confidence is poorly calibrated — the agent may be incorrectly confident on hard cases and artificially uncertain on routine ones. Neither metric reliably identifies when escalation is actually needed.

B) Both approaches are actually reliable and should be implemented as additional escalation signals alongside existing criteria.

C) Sentiment analysis is unreliable because it can't detect sarcasm, but confidence scoring is generally accurate.

D) Both approaches add latency — sentiment analysis and confidence calculation slow down each response.

**Correct Answer: A**

**Explanation:** Both are unreliable proxies for the actual decision: whether the agent can resolve the issue. Sentiment measures emotion, not complexity — the easiest refund case might come from the angriest customer. Self-reported confidence is poorly calibrated because the model doesn't have reliable insight into when it's likely to be wrong. The exam guide explicitly calls out both as unreliable. Option B contradicts the documented guidance. Option C incorrectly validates confidence scoring. Option D focuses on a secondary concern.

---

## Task Statement 5.3: Implement error propagation strategies across multi-agent systems

### Q5.3.1 — Scenario: Multi-Agent Research System
**Difficulty: Easy | Knowledge: Structured error context enables recovery**

Your web search subagent encounters a timeout. It returns to the coordinator: `{"status": "error", "message": "Search unavailable"}`. The coordinator decides to skip web search entirely and proceed with only document analysis. Later review shows the timeout was transient and a retry would have succeeded. What error reporting improvement would have enabled better recovery?

A) Return structured error context including the failure type ("transient/timeout"), the attempted query, any partial results, and potential alternatives — this gives the coordinator the information to decide between retrying, trying a modified query, or proceeding with partial data.

B) Implement automatic retry logic inside the subagent so the coordinator never sees transient errors.

C) Return a more descriptive error message: "Search timed out after 30 seconds. The search provider may be temporarily overloaded."

D) Have the coordinator always retry any subagent that returns an error, regardless of error type.

**Correct Answer: A**

**Explanation:** Structured error context enables intelligent coordinator recovery. Knowing the failure type (transient), what was attempted (specific query), and what alternatives exist lets the coordinator make an informed decision — retry the same query, try a different search provider, or proceed with caveats. The generic "search unavailable" (the original response) hides all this context. Option B handles retries locally but the coordinator never learns about the transient issue. Option C is more descriptive but still unstructured. Option D wastes resources retrying non-retryable errors.

---

### Q5.3.2 — Scenario: Multi-Agent Research System
**Difficulty: Medium | Knowledge: Silent error suppression as anti-pattern**

Your document analysis subagent encounters a permission error when trying to access a restricted database. Rather than reporting the error, it returns an empty result set marked as a successful query with no matches: `{"results": [], "status": "success"}`. Why is this an anti-pattern?

A) Silently suppressing errors by returning empty results as success prevents any recovery — the coordinator can't retry, can't try alternatives, can't annotate the final report with coverage gaps, and may incorrectly conclude that no relevant data exists in the database.

B) Empty results waste the coordinator's context space with useless data.

C) The coordinator will proceed faster without the error handling overhead.

D) The synthesis agent will produce better output if it doesn't have to handle error annotations.

**Correct Answer: A**

**Explanation:** Silent error suppression is explicitly called out as an anti-pattern. It converts a recoverable situation (permission error → try different credentials, request access, note the gap) into an invisible one. The coordinator makes decisions based on false information ("no data exists") rather than accurate information ("data may exist but we couldn't access it"). Options B, C, and D incorrectly frame error suppression as having minor or even positive consequences.

---

### Q5.3.3 — Scenario: Multi-Agent Research System
**Difficulty: Medium | Skill: Distinguishing access failures from valid empty results**

Your search subagent queries three databases. Database A returns 5 results, Database B returns 0 results (no matches for the query), and Database C returns an error (connection refused). How should each be reported back to the coordinator?

A) Database A: success with 5 results. Database B: success with 0 results (valid empty — the query was executed but found no matches). Database C: error with structured context including failure type ("transient/connection"), the query that was attempted, and `isRetryable: true`. This lets the coordinator distinguish "no data exists" from "we couldn't check."

B) Database A: success with 5 results. Database B and C: both reported as errors since they returned no useful data.

C) Database A: success with 5 results. Database B: error ("no results found"). Database C: error ("connection refused").

D) All three: success with results. Database B returns an empty array, Database C returns a "service unavailable" placeholder result.

**Correct Answer: A**

**Explanation:** The critical distinction is between valid empty results (the query succeeded but found nothing) and access failures (the query couldn't execute). Database B's empty result is informative — it confirms no matching data exists. Database C's failure is different — data may exist but is inaccessible. The coordinator needs this distinction to decide whether to retry Database C, note the gap in the report, or try an alternative source. Option B incorrectly equates "no results" with "error." Option C reports valid empty results as errors. Option D suppresses the access failure.

---

### Q5.3.4 — Scenario: Multi-Agent Research System
**Difficulty: Hard | Skill: Workflow-terminating errors vs recoverable errors**

Your research coordinator delegates to four subagents. Subagent A completes successfully. Subagent B fails with a transient timeout. Subagent C fails with a permanent permission error. Subagent D completes successfully. A developer proposes terminating the entire workflow when any subagent fails. Why is this an anti-pattern, and what's the better approach?

A) Terminating the entire workflow on single failures is an anti-pattern because recoverable and partial-result paths exist. The better approach: retry Subagent B (transient failure), note Subagent C's gap (permanent failure — retrying won't help), proceed with results from A and D, and produce a final report annotated with coverage gaps from B (if retry fails) and C.

B) Terminate on the permanent error (C) but retry the transient error (B), then proceed if B succeeds.

C) Proceed only with successful agents (A and D) and silently omit findings from B and C.

D) Retry all failed agents once, terminate if any still fail.

**Correct Answer: A**

**Explanation:** Different failure types require different responses. Transient failures (B) should be retried. Permanent failures (C) should be noted as coverage gaps — retrying won't resolve a permission error. The workflow should proceed with available results (A, D, and potentially B after retry) and annotate the output with coverage limitations. Option B terminates on a permanent error when proceeding with a gap annotation is more useful. Option C silently suppresses errors. Option D retries non-retryable errors and terminates unnecessarily.

---

### Q5.3.5 — Scenario: Claude Code for Continuous Integration
**Difficulty: Easy | Knowledge: Access failures vs valid empty results**

Your CI pipeline agent queries a code coverage tool via MCP and receives an empty result set. The agent reports: "No test coverage data exists for this module." A developer points out the module has 85% test coverage. What most likely went wrong?

A) The agent conflated an access failure or misconfigured query with a valid empty result — an empty response from a tool could mean "no data found" or "query failed silently," and the agent should distinguish between these before reporting conclusions.

B) The MCP tool returned stale cached data that didn't include the latest coverage results.

C) The agent's context window was full, causing it to drop the tool result.

D) The code coverage tool only reports coverage below a threshold, so 85% coverage was intentionally excluded.

**Correct Answer: A**

**Explanation:** A critical error propagation concept is distinguishing access failures from valid empty results. An empty response might mean the query was malformed, permissions were insufficient, or the tool endpoint was misconfigured — not necessarily that no data exists. The agent should check for error indicators, validate the query, or attempt a diagnostic query before concluding the data doesn't exist. Option B is possible but speculative. Option C describes a different failure mode. Option D invents a behavior not described in the scenario.

---

## Task Statement 5.4: Manage context effectively in large codebase exploration

### Q5.4.1 — Scenario: Developer Productivity with Claude
**Difficulty: Easy | Knowledge: Context degradation in extended sessions**

After a 45-minute exploration session analyzing a large codebase, your agent starts giving inconsistent answers — referencing "typical patterns" instead of specific classes it discovered earlier, and contradicting findings it made 30 minutes ago. What is happening?

A) Context degradation in extended sessions — as the conversation grows, the model starts losing track of specific earlier findings, falling back on general knowledge rather than session-specific discoveries.

B) The model's temperature increased during the session, causing more random outputs.

C) The codebase files changed during the session, invalidating earlier findings.

D) The model reached a time limit and switched to a lower-quality mode.

**Correct Answer: A**

**Explanation:** Context degradation is a known phenomenon in extended sessions. As conversation length grows, earlier specific discoveries become harder for the model to access reliably, and it substitutes general knowledge ("typical patterns") for specific findings ("the AuthService class on line 47 uses a singleton pattern"). Options B, C, and D describe mechanisms that don't exist or are speculative.

---

### Q5.4.2 — Scenario: Developer Productivity with Claude
**Difficulty: Medium | Skill: Scratchpad files for persistent findings**

To counteract context degradation during a multi-hour codebase exploration, a developer creates a scratchpad file where the agent records key findings after each exploration phase. How does this help?

A) The scratchpad file persists key findings across context boundaries — when the agent needs to reference earlier discoveries, it reads the scratchpad file rather than relying on its degrading memory of the conversation, ensuring consistent and accurate recall of specific findings.

B) The scratchpad file reduces the conversation length by replacing verbose tool outputs with summary references.

C) The scratchpad file serves as documentation for other developers, not as a context management mechanism.

D) The scratchpad file triggers a context window reset, giving the agent a fresh starting point.

**Correct Answer: A**

**Explanation:** Scratchpad files persist findings outside the conversation context. As the conversation degrades, the agent can Read the scratchpad to recover specific details (class names, file paths, dependency relationships) that might be lost in the long conversation. This acts as an external memory that counteracts context degradation. Option B describes a secondary benefit. Option C is an incidental benefit, not the purpose. Option D describes behavior that doesn't occur.

---

### Q5.4.3 — Scenario: Developer Productivity with Claude
**Difficulty: Medium | Skill: Subagent delegation for exploration isolation**

You need to understand how a codebase's payment processing system works. The investigation requires reading 20+ files, tracing function calls across modules, and mapping database relationships. If you do this in the main conversation, the verbose output will fill the context window. What approach preserves the main agent's context?

A) Spawn a subagent to investigate the payment system specifically — the subagent reads files and traces dependencies in its own isolated context, then returns a structured summary to the main agent with key findings, architecture overview, and important file references.

B) Use `/compact` after each file read to keep the context manageable.

C) Read only the first 50 lines of each file to reduce context consumption.

D) Split the exploration across 4 separate sessions, manually carrying context between them.

**Correct Answer: A**

**Explanation:** Subagent delegation isolates verbose exploration output. The subagent performs all the detailed file reading and dependency tracing in its own context, then returns only a structured summary to the main agent. The main agent's context remains clean for high-level coordination and subsequent tasks. Option B discards context that may be needed. Option C produces incomplete understanding. Option D fragments the workflow and loses continuity.

---

### Q5.4.4 — Scenario: Developer Productivity with Claude
**Difficulty: Hard | Skill: Structured state persistence for crash recovery**

Your multi-agent codebase analysis runs for 2 hours with a coordinator and three subagents. Midway through, the coordinator crashes. Without recovery mechanisms, all progress is lost and the analysis must restart from scratch. How should you design crash recovery?

A) Design structured state persistence where each agent exports its current state (findings, progress, pending tasks) to a known file location. The coordinator maintains a manifest tracking each agent's state files. On recovery, the coordinator loads the manifest, reads each agent's exported state, and injects the recovered context into agent prompts to resume from the last checkpoint.

B) Log all tool calls and responses to a file, then replay them on recovery.

C) Save the full conversation history to a file and use `--resume` on recovery.

D) Run the coordinator with automatic checkpointing that saves state every 5 minutes.

**Correct Answer: A**

**Explanation:** Structured state persistence using manifests provides organized crash recovery. Each agent's state is independently recoverable, and the coordinator's manifest tracks the overall progress. On resume, the coordinator knows exactly what was completed, what was in progress, and what needs to restart. Option B creates an enormous replay log that may not represent the current state after partial completions. Option C only recovers the coordinator's conversation, not subagent state. Option D describes a mechanism without specifying what state is saved or how it's recovered.

---

### Q5.4.5 — Scenario: Developer Productivity with Claude
**Difficulty: Medium | Skill: Using /compact for context management**

During an extended exploration session, your context is filling with verbose discovery output — file contents, grep results, and dependency traces. You're only 60% through the exploration. What is the appropriate use of `/compact`?

A) Use `/compact` to reduce context usage by compressing verbose discovery output, freeing space for the remaining 40% of exploration — this trades detailed history for continued capacity while preserving the key findings summarized in the compaction.

B) Never use `/compact` during exploration because it loses important details.

C) Use `/compact` after every tool call to keep context at minimum size.

D) Use `/compact` only at the very end of exploration before starting implementation.

**Correct Answer: A**

**Explanation:** `/compact` is appropriate during extended exploration sessions when context fills with verbose discovery output. It compresses the conversation while preserving key findings, freeing space for continued work. Option B is too conservative — sometimes compaction is necessary to continue. Option C is excessive — frequent compaction loses too much detail. Option D is too late — if context is full at 60%, you can't wait until the end.

---

### Q5.4.6 — Scenario: Developer Productivity with Claude
**Difficulty: Medium | Skill: Summarizing before spawning next phase**

Your codebase exploration has two phases: Phase 1 maps the overall architecture, Phase 2 investigates specific subsystems. After Phase 1 produces 30 findings about module structure, dependencies, and entry points, you need to spawn Phase 2 subagents. How should you transition?

A) Summarize Phase 1's key findings into a structured brief before spawning Phase 2 subagents, injecting this summary into each subagent's initial context — this gives subagents the architectural overview they need without the verbose exploration history.

B) Pass the full Phase 1 conversation to each Phase 2 subagent for complete context.

C) Let Phase 2 subagents start fresh without Phase 1 context, since they're investigating specific subsystems.

D) Have each Phase 2 subagent re-discover the architectural context it needs by re-reading the same files.

**Correct Answer: A**

**Explanation:** Summarizing findings between phases creates a clean, focused context transfer. Phase 2 subagents get the architectural overview (module boundaries, dependencies, entry points) without the verbose file reads and grep outputs from Phase 1. Option B dumps verbose history into subagent context. Option C deprives subagents of critical context about how their subsystem fits into the broader architecture. Option D wastes time and tokens re-discovering already-known information.

---

## Task Statement 5.5: Design human review workflows and confidence calibration

### Q5.5.1 — Scenario: Structured Data Extraction
**Difficulty: Medium | Knowledge: Aggregate accuracy masking segment-level problems**

Your extraction pipeline reports 97% overall accuracy across 10,000 documents. Management considers reducing human review. However, when accuracy is broken down by document type, invoices show 99%, receipts show 98%, but handwritten forms show only 78%. Why is the aggregate metric misleading?

A) Aggregate accuracy metrics can mask poor performance on specific document types or fields — the 97% overall is dominated by the high-volume invoice and receipt categories, hiding the 78% failure rate on handwritten forms. Decisions about reducing human review must be based on per-segment accuracy.

B) The 97% overall accuracy is sufficient for most business purposes regardless of segment breakdown.

C) The handwritten forms category is too small to be statistically significant.

D) Accuracy should be measured per field rather than per document type.

**Correct Answer: A**

**Explanation:** Aggregate metrics are dominated by high-volume categories. If 80% of documents are invoices (99% accuracy) and only 5% are handwritten forms (78% accuracy), the aggregate looks great while a significant segment fails. Reducing human review based on the aggregate would cause 22% of handwritten forms to have unreviewed errors. Option B ignores the segment-level failure. Option C is speculative about sample size. Option D is a valid additional analysis but doesn't address the document-type masking problem.

---

### Q5.5.2 — Scenario: Structured Data Extraction
**Difficulty: Medium | Skill: Stratified random sampling**

You've implemented automated extraction with human review for low-confidence results. Over time, the team wants to reduce human review further by trusting high-confidence extractions. How do you safely validate this change?

A) Implement stratified random sampling of high-confidence extractions — randomly select a percentage of extractions the system classified as high-confidence and route them to human review anyway. This measures the actual error rate in the "trusted" tier and detects novel error patterns that the confidence model might miss.

B) Gradually reduce the human review percentage from 100% to 0% over six months, monitoring error rates.

C) Compare the model's confidence scores against a labeled validation set once, then trust the calibration going forward.

D) Trust high-confidence extractions immediately since the confidence model was trained on representative data.

**Correct Answer: A**

**Explanation:** Stratified random sampling provides ongoing quality measurement of the automated tier. Even after initial validation, document types, formats, and content evolve — sampling detects novel error patterns that emerge over time. Option B reduces review without measurement. Option C validates once but doesn't account for distribution shifts. Option D assumes static reliability without verification.

---

### Q5.5.3 — Scenario: Structured Data Extraction
**Difficulty: Hard | Skill: Field-level confidence calibration**

Your extraction tool outputs document-level confidence scores. A quality audit finds that within high-confidence documents, certain fields (e.g., "payment terms") have a 15% error rate while others (e.g., "invoice number") have a 0.5% error rate. How should you improve the review routing?

A) Have the model output field-level confidence scores instead of (or in addition to) document-level scores. Calibrate field-level thresholds using a labeled validation set — for example, "payment terms" might require human review whenever confidence is below 0.9, while "invoice number" can be trusted at 0.7. This routes limited reviewer capacity to the fields most likely to contain errors.

B) Send all documents to human review if any field's error rate exceeds 10%.

C) Add more few-shot examples specifically for "payment terms" extraction to improve accuracy.

D) Remove "payment terms" from automated extraction and always extract it manually.

**Correct Answer: A**

**Explanation:** Field-level confidence scores enable targeted review routing. Instead of reviewing entire documents (expensive) or trusting entire documents (risky), reviewers focus on specific fields that are most likely to contain errors. Calibrating thresholds against labeled data ensures the scores meaningfully predict error likelihood. Option B over-reviews all documents for one field's weakness. Option C may help but doesn't address the routing problem. Option D removes the field from automation entirely, which is disproportionate.

---

### Q5.5.4 — Scenario: Structured Data Extraction
**Difficulty: Medium | Skill: Analyzing accuracy by document type and field**

Before automating high-confidence extractions, you need to verify consistent performance. You check overall accuracy (96%), accuracy by document type (invoices 98%, contracts 94%, receipts 97%), and accuracy by field (totals 99%, dates 97%, names 95%, payment terms 82%). Which segment would you investigate further before proceeding?

A) Payment terms at 82% accuracy — this specific field significantly underperforms the aggregate and other fields. Before reducing human review, you need to understand why payment terms fail (inconsistent formatting, missing data, ambiguous language) and either improve extraction for this field or maintain human review specifically for it.

B) Contracts at 94% — the lowest document-type accuracy warrants investigation.

C) Names at 95% — any field below 97% needs improvement before automation.

D) No specific investigation is needed — 96% overall accuracy meets the quality threshold.

**Correct Answer: A**

**Explanation:** Payment terms at 82% is a clear outlier that would cause significant errors if human review were reduced. The field-level analysis reveals a problem hidden by the 96% aggregate. Investigating why this field fails (and either improving it or maintaining review for it) is essential before trusting the automated tier. Option B identifies a moderate concern but 94% by document type is reasonable. Option C sets an arbitrary threshold. Option D ignores the segment-level weakness.

---

### Q5.5.5 — Scenario: Claude Code for Continuous Integration
**Difficulty: Easy | Knowledge: Calibrating review thresholds with labeled data**

Your CI pipeline uses Claude to flag potential bugs in PRs. You want to set a confidence threshold above which findings are reported without human review. How should you determine this threshold?

A) Run the pipeline on a labeled validation set of PRs with known bugs and known clean code, measure precision and recall at different thresholds, and choose the threshold that meets your team's acceptable false positive rate.

B) Start with a threshold of 80% confidence and adjust it up or down based on developer complaints about false positives.

C) Use the highest possible threshold (99%) to minimize false positives, accepting that some real bugs will be missed.

D) Don't use a threshold — report all findings and let developers filter them manually.

**Correct Answer: A**

**Explanation:** Calibrating thresholds against labeled ground truth is the principled approach — you measure actual precision and recall at various thresholds and choose based on your team's tolerance for false positives vs missed bugs. Option B uses ad hoc adjustment without systematic measurement. Option C optimizes for one metric (precision) without understanding the trade-off with recall. Option D pushes the filtering burden to developers, defeating the purpose of automated triage.

---

## Task Statement 5.6: Preserve information provenance and handle uncertainty in multi-source synthesis

### Q5.6.1 — Scenario: Multi-Agent Research System
**Difficulty: Medium | Knowledge: Source attribution loss during summarization**

Your research pipeline has three stages: search → analysis → synthesis. The search agent returns findings with source URLs. The analysis agent summarizes the findings but drops the URLs. The synthesis agent produces a report with unsourced claims. What structural change prevents attribution loss?

A) Require subagents to output structured claim-source mappings — each finding should include the claim, evidence excerpt, and source URL/document name. Downstream agents must preserve these mappings through synthesis, ensuring every claim in the final report traces back to its source.

B) Have the synthesis agent re-search for sources to verify its claims.

C) Include source URLs in the system prompt so all agents have access to them.

D) Add a post-processing step that uses text matching to re-associate claims with likely sources.

**Correct Answer: A**

**Explanation:** Structured claim-source mappings create a provenance chain that survives each pipeline stage. When the analysis agent's output includes `{claim: "...", source: "URL", evidence: "..."}`, the synthesis agent has the information needed to maintain attribution. Option B adds redundant work. Option C doesn't ensure mappings are preserved through processing. Option D is fragile and error-prone.

---

### Q5.6.2 — Scenario: Multi-Agent Research System
**Difficulty: Hard | Knowledge: Handling conflicting statistics from credible sources**

Your research pipeline finds two conflicting statistics: Source A (World Bank, 2024) reports global renewable energy adoption at 32%, while Source B (IEA, 2023) reports it at 28%. Both are credible institutions. How should the synthesis agent handle this?

A) Annotate the conflict with source attribution: "Global renewable energy adoption ranges from 28% (IEA, 2023) to 32% (World Bank, 2024). The discrepancy may reflect different measurement methodologies or the one-year gap between reports." This preserves both values with attribution rather than arbitrarily selecting one.

B) Use the more recent figure (32%, World Bank 2024) since it's newer.

C) Average the two figures and report 30%.

D) Omit the statistic entirely since conflicting sources make it unreliable.

**Correct Answer: A**

**Explanation:** When credible sources conflict, the synthesis should preserve both values with source attribution and note possible reasons for the discrepancy (methodology differences, temporal gaps). Arbitrarily selecting one (B) or averaging (C) obscures the uncertainty. Omitting (D) loses valuable information. The report should distinguish well-established findings from contested ones.

---

### Q5.6.3 — Scenario: Multi-Agent Research System
**Difficulty: Medium | Skill: Requiring temporal metadata**

Your synthesis agent combines findings from sources published between 2019 and 2024. The final report states "remote work adoption is 58%" without noting that this figure comes from a 2021 mid-pandemic survey. A reader assumes this reflects current (2024) conditions. What upstream change prevents this?

A) Require subagents to include publication or data collection dates in their structured outputs — when the synthesis agent sees `{claim: "remote work adoption is 58%", date: "2021", context: "mid-pandemic survey"}`, it can correctly temporal-context the finding and note that post-pandemic figures may differ.

B) Filter out any source older than 2 years before synthesis.

C) Add "Always note the date of each finding" to the synthesis agent's prompt.

D) Have the synthesis agent verify each statistic against the most recent available data.

**Correct Answer: A**

**Explanation:** Requiring temporal metadata in structured outputs enables correct interpretation of findings. Publication dates, data collection periods, and contextual notes (e.g., "mid-pandemic") let the synthesis agent present findings with appropriate temporal framing. Option B discards potentially valuable historical context. Option C is a prompt instruction without the structural data to support it. Option D adds significant overhead and may not find updated figures.

---

### Q5.6.4 — Scenario: Multi-Agent Research System
**Difficulty: Medium | Skill: Structuring reports to distinguish well-established from contested findings**

Your synthesis agent produces a report on "AI regulation trends." Some findings are well-established (the EU AI Act's existence and key provisions), while others are contested (the economic impact of AI regulation on innovation). The report presents everything with equal confidence. How should the output be structured?

A) Structure the report with explicit sections distinguishing well-established findings from contested ones — for example, "Established: The EU AI Act classifies AI systems into risk tiers with graduated requirements..." versus "Contested: The economic impact of AI regulation on innovation shows conflicting evidence. Source A argues... while Source B contends..." This preserves the original source characterizations.

B) Assign confidence scores to each section and let the reader interpret accordingly.

C) Only include well-established findings and omit contested ones for reliability.

D) Present all findings as equally established, noting sources for each.

**Correct Answer: A**

**Explanation:** Explicit structural separation between well-established and contested findings provides the reader with appropriate context for each claim. Well-established findings can be stated directly; contested findings should present the competing perspectives with source attribution. Option B uses numerical scores that may not be well-calibrated. Option C loses valuable information about ongoing debates. Option D misrepresents the certainty level of contested findings.

---

### Q5.6.5 — Scenario: Multi-Agent Research System
**Difficulty: Hard | Skill: Rendering different content types appropriately in synthesis**

Your synthesis agent receives three types of findings: financial data (quarterly revenue figures), news developments (recent policy announcements), and technical analysis (architectural comparison of AI systems). Currently, the synthesis agent converts everything into prose paragraphs. Financial data loses precision when narrated, and technical comparisons are hard to follow in paragraph form. How should synthesis output be structured?

A) Render different content types in their most appropriate format — financial data as tables (preserving precision and enabling comparison), news as prose (natural narrative flow), and technical findings as structured lists or comparison matrices. The report should mix formats rather than forcing everything into a uniform style.

B) Use a uniform prose format for consistency across the report.

C) Convert all findings to bullet points for easy scanning.

D) Let the synthesis agent choose the format for each section based on its judgment.

**Correct Answer: A**

**Explanation:** Different content types have different optimal representations. Financial data is most useful in tables where numbers can be compared precisely. News is best as narrative prose. Technical comparisons benefit from structured lists or matrices. Forcing everything into prose (B) or bullets (C) compromises readability for at least some content types. Option D sounds reasonable but lacks the explicit guidance needed for consistent output quality.

---

### Q5.6.6 — Scenario: Multi-Agent Research System
**Difficulty: Medium | Skill: Completing analysis with conflicting values annotated**

Your document analysis subagent processes a financial report where the executive summary states annual revenue of "$4.2 billion" but a table on page 15 shows "$4.18 billion." Should the analysis agent resolve this discrepancy before passing results to synthesis?

A) Complete the analysis with both values included and explicitly annotated — report `{stated_revenue: "$4.2B", source: "executive summary", table_revenue: "$4.18B", source: "page 15 financial table", conflict: true}`. Let the coordinator decide how to reconcile before passing to synthesis.

B) Use the table value ($4.18B) since tabular data is more precise than summary text.

C) Use the executive summary value ($4.2B) since it's the primary stated figure.

D) Average the two values and report $4.19B.

**Correct Answer: A**

**Explanation:** The analysis agent should preserve conflicting values with source annotations rather than making reconciliation decisions. The discrepancy may reflect rounding (the summary rounded $4.18B to $4.2B), or it could indicate an actual error. The coordinator (or human reviewer) has broader context to decide — the analysis agent's job is to report what the document contains faithfully, not interpret discrepancies. Options B and C make unilateral selection decisions. Option D fabricates a value that appears in neither source.

---

## Cross-Domain Questions (Domain 5 intersecting with other domains)

### Q5.X.1 — Scenario: Customer Support Resolution Agent
**Difficulty: Hard | Domains: 5 (Context Management) + 1 (Architecture) + 2 (Tool Design)**

Your support agent handles a complex multi-issue conversation. After 15 turns, three interacting problems emerge: (1) the agent confuses details between the customer's two open orders because progressive summarization condensed the specifics; (2) `lookup_order` returns 40 fields per query, and three lookups have consumed 25% of the remaining context; (3) when the agent encounters a timeout from `get_billing_history`, it tells the customer their billing history is empty. How should you address all three?

A) Extract structured case facts (order IDs, amounts, dates, statuses) into a persistent block included outside summarized history. Add a PostToolUse hook to trim `lookup_order` results to only the 5-6 fields relevant to the current issue type. Implement structured error responses from `get_billing_history` distinguishing timeouts from empty results. Each fix targets a specific root cause: data loss from summarization, context bloat from verbose tools, and error misinterpretation.

B) Increase the context window to accommodate all three issues without changes.

C) Limit conversations to 10 turns and require a new session for additional issues.

D) Add comprehensive system prompt instructions addressing all three behaviors.

**Correct Answer: A**

**Explanation:** Three distinct problems require three targeted fixes that span context management (persistent case facts), tool design (trimming verbose results), and error handling (structured error responses). Option B treats context as unlimited. Option C degrades customer experience. Option D uses prompt-based fixes for problems that need structural solutions.

---

### Q5.X.2 — Scenario: Multi-Agent Research System
**Difficulty: Hard | Domains: 5 (Context Management) + 1 (Architecture) + 4 (Prompt Engineering)**

Your research pipeline processes a 50-page report through search, analysis, and synthesis agents. Three quality problems appear: (1) the synthesis agent drops source citations because the analysis agent's summaries don't preserve claim-source mappings; (2) findings from pages 20-35 are consistently underrepresented in the final output; (3) the synthesis agent's report treats a 2019 pre-pandemic statistic and a 2024 post-pandemic statistic as contradictory rather than temporally different. What combination of fixes addresses all three?

A) Require the analysis agent to output structured claim-source mappings (claim, source URL, evidence excerpt) to preserve provenance through synthesis. Split the 50-page document into overlapping sections with key findings summaries at the beginning of each to mitigate the lost-in-the-middle effect. Require subagents to include publication/data collection dates in structured outputs so the synthesis agent can correctly contextualize temporal differences.

B) Use a larger context model for synthesis to handle the full 50-page document. Add "always cite sources" and "check dates" to the synthesis prompt.

C) Summarize the document before analysis to reduce size. Add citations in post-processing. Flag all conflicting statistics for human review.

D) Process the document chronologically and have each agent note the date range it covered.

**Correct Answer: A**

**Explanation:** Each fix targets a specific failure: structured claim-source mappings prevent citation loss, sectioned processing with summaries mitigates positional attention effects, and temporal metadata enables correct interpretation of time-varying statistics. Option B addresses size but not structure, and uses prompt instructions for problems requiring structural solutions. Option C loses information through pre-summarization and defers problems to post-processing. Option D imposes a structure that doesn't match the document's organization.

---

*End of Domain 5 Question Bank*
*Total: 37 questions covering all 6 task statements + 2 cross-domain questions*
*Distribution: 6 Easy, 20 Medium, 11 Hard*