---
name: domain-5-context-management
description: Training curriculum for Domain 5: Context Management & Reliability (15% of exam). Use when /training targets this domain.
---

# Domain 5: Context Management & Reliability (15%)

## Overview
- Weight: 15% of scored content
- Task statements: 5.1–5.6
- Primary scenarios: Customer Support Resolution Agent, Multi-Agent Research System, Structured Data Extraction
- Cross-domain connections: Agentic Architecture (subagent context isolation, coordinator error handling, session management), Tool Design (structured error responses, tool result verbosity), Prompt Engineering (confidence calibration, validation flows), Claude Code Configuration (context window management, /compact)

## Task Statement 5.1: Manage conversation context to preserve critical information across long interactions

### Key Concepts

Progressive summarization is a common technique for managing long conversations, but it carries a specific risk: condensing numerical values, percentages, dates, and customer-stated expectations into vague summaries. A statement like "$47.99 refund for order #8812" becomes "the refund" after summarization. For transactional interactions where specific data points are essential for resolution, this loss is catastrophic.

The mitigation is extracting transactional facts (amounts, dates, order numbers, statuses) into a persistent "case facts" block that is included in each prompt separately from summarized history. The case facts block is never summarized — it persists verbatim across all turns. For multi-issue sessions, extract and persist structured issue data (order IDs, amounts, statuses) into a separate context layer.

The "lost in the middle" effect means models reliably process information at the beginning and end of long inputs but may omit findings from middle sections. The mitigation is placing key findings summaries at the beginning of aggregated inputs and organizing detailed results with explicit section headers. Position matters for attention.

Tool results accumulate in context and consume tokens disproportionately to their relevance. An order lookup might return 40+ fields when only 5 are relevant for the current task. Trimming verbose tool outputs to only relevant fields before they accumulate in context is essential for long sessions.

Complete conversation history must be passed in subsequent API requests to maintain conversational coherence. If you omit turns, the model loses track of what has been discussed and may repeat itself or contradict prior statements.

### Common Misconceptions

- **Summarization preserves all important details**: It does not. Summarization systematically loses specific numerical values, dates, and identifiers — exactly the details that matter most in transactional conversations.
- **Character/token limits cause summarization loss**: The issue is not truncation but abstraction. Summarization converts specifics ("$47.99 refund for order #8812") into generalizations ("the refund") regardless of length limits.
- **Recency bias explains lost details**: While models do have some recency bias, the primary cause of lost details in summarized conversations is the summarization process itself, not recency effects.
- **Tool results should be kept in full**: Verbose tool results waste context tokens. Trimming to relevant fields is essential, especially when multiple tool calls accumulate over a long conversation.
- **The "lost in the middle" effect is only about document length**: It applies to any long input, including aggregated research findings, multi-file reviews, or combined tool results. Position-aware organization mitigates it regardless of content type.

### Practice Focus

Questions present a long conversation where specific details are lost and ask for the cause or fix. The cause is progressive summarization losing transactional facts. The fix is a persistent "case facts" block or structured issue data layer outside the summarized history. Watch for questions about tool result accumulation — the fix is trimming to relevant fields, not avoiding tool calls.

## Task Statement 5.2: Design effective escalation and ambiguity resolution patterns

### Key Concepts

Appropriate escalation triggers are: customer explicitly requests a human, policy exceptions or gaps (not covered by existing rules), and inability to make meaningful progress after investigation. Notably, "complex case" alone is not an escalation trigger — many complex cases are within the agent's capability.

A critical distinction exists between immediate escalation and resolution-first approaches. When a customer explicitly demands a human agent, the agent should honor this immediately without first attempting investigation. However, when a customer is frustrated but has not explicitly asked for a human, the agent should acknowledge the frustration while offering resolution — escalating only if the customer reiterates their preference after the offer.

Sentiment-based escalation (escalating when the customer sounds angry) and self-reported confidence scores (escalating when the model reports low confidence) are both unreliable proxies for actual case complexity. Angry customers may have simple issues; confident models may be wrong. Escalation should be based on concrete criteria, not subjective assessment.

When tool results return multiple customer matches, the agent should request additional identifiers for disambiguation rather than selecting based on heuristics (most recent, closest name match). Heuristic selection risks acting on the wrong customer's account.

Policy gaps require escalation. If a customer requests something the policy is silent on (e.g., competitor price matching when the policy only addresses own-site adjustments), the agent should escalate rather than improvising a response based on related policies.

### Common Misconceptions

- **Complex cases should always be escalated**: The agent can handle many complex cases. Escalation triggers are explicit human requests, policy gaps, and inability to progress — not complexity alone.
- **Sentiment analysis is reliable for escalation**: Customer sentiment does not reliably correlate with case difficulty or the need for human intervention. A frustrated customer with a simple issue does not need escalation.
- **Self-reported confidence drives escalation**: The model's self-reported confidence is not well-calibrated. A model reporting "high confidence" may still be wrong; a model reporting "low confidence" may be handling the case correctly.
- **Offer to help before honoring explicit human requests**: When a customer explicitly says "let me talk to a human," investigating first is disrespectful and wastes time. Honor the request immediately.
- **Use heuristics for multiple matches**: Selecting the "most likely" customer from multiple matches risks acting on the wrong account. Always ask for additional identifiers.

### Practice Focus

Escalation questions test three main patterns: when to escalate (explicit request, policy gap, no progress), how to handle frustrated customers (acknowledge + offer, escalate only on reiteration), and how to handle ambiguity (ask for clarification, never use heuristics). Watch for distractors that propose sentiment analysis or confidence-based escalation — these are always wrong.

## Task Statement 5.3: Implement error propagation strategies across multi-agent systems

### Key Concepts

Structured error context — including failure type, attempted query, partial results, and alternative approaches — enables intelligent coordinator recovery decisions. A generic error status like "search unavailable" hides valuable context: Was it a timeout? A rate limit? A permanent failure? Did the search return partial results before failing? What alternatives exist?

The distinction between access failures (timeouts, service down) and valid empty results (the query succeeded but found no matching data) is critical. Access failures need retry decisions or alternative source routing. Empty results need to be communicated to the user or factored into the analysis. If both are represented the same way (e.g., empty results), the coordinator cannot make the right decision.

Two anti-patterns in error handling: silently suppressing errors (returning empty results as success, which hides data gaps from the final output) and terminating entire workflows on single failures (which throws away partial results from successful subagents). The correct approach is graceful degradation — work with what succeeded, annotate what failed, and let the coordinator decide how to proceed.

Subagents should implement local recovery for transient failures (retry with backoff, try alternative endpoints) and only propagate errors they cannot resolve. When propagating, they include what was attempted and any partial results, enabling the coordinator to make informed recovery decisions.

Synthesis output should include coverage annotations indicating which findings are well-supported versus which topic areas have gaps due to unavailable sources. This transparency lets consumers of the output understand its completeness.

### Common Misconceptions

- **Generic error messages are sufficient for coordinators**: "Search unavailable" tells the coordinator nothing about recovery options. Structured context (failure type, what was tried, partial results) enables intelligent decisions.
- **Empty results and access failures are the same**: They require completely different handling. Conflating them leads to either unnecessary retries (for genuine empty results) or silent data gaps (for access failures disguised as empty results).
- **One subagent failure should terminate the workflow**: Partial results from successful subagents are still valuable. The coordinator should synthesize what is available and annotate gaps.
- **All errors should propagate to the coordinator immediately**: Transient failures should be handled locally by the subagent (retry, alternative approach) before propagation. Only unrecoverable errors need coordinator attention.
- **Synthesis should hide data gaps**: Hiding gaps produces output that appears comprehensive but is actually incomplete. Coverage annotations are essential for trustworthy synthesis.

### Practice Focus

Questions present a multi-agent error scenario and ask how to handle it. Correct answers always involve structured error context, graceful degradation with partial results, and explicit gap annotation. Watch for answers that propose terminating on first failure or returning generic error messages — both are anti-patterns.

## Task Statement 5.4: Manage context effectively in large codebase exploration

### Key Concepts

Context degradation in extended sessions is a real phenomenon: as the context window fills, the model starts giving inconsistent answers and referencing "typical patterns" rather than specific classes discovered earlier. The findings from early exploration get pushed out of effective attention range by subsequent verbose output.

Scratchpad files address this by persisting key findings to the filesystem. The agent writes important discoveries (class hierarchies, dependency maps, key file locations) to a scratchpad file, then references the scratchpad for subsequent questions. This persists information across context boundaries and counteracts degradation.

Subagent delegation isolates verbose exploration output from the main agent's context. Instead of the main agent running 50 Grep calls and reading 30 files (which fills its context), it spawns a subagent to investigate a specific question. The subagent does the verbose exploration and returns a structured summary to the main agent, which preserves its high-level coordination context.

Structured state persistence enables crash recovery: each agent exports its current state to a known file location, and the coordinator loads a manifest on resume. This allows multi-hour exploration sessions to survive interruptions without losing all progress.

The `/compact` command reduces context usage during extended sessions by summarizing the current conversation. Use it when context fills with verbose discovery output and reasoning quality starts to degrade.

### Common Misconceptions

- **Large context windows eliminate degradation**: Even with very large context windows, the model's effective attention degrades as context fills. Quantity of context does not guarantee quality of attention.
- **The model remembers everything in context**: The model processes all of context but does not attend equally to all parts. Early findings get diluted by subsequent verbose output.
- **Scratchpad files are redundant with context**: The scratchpad persists information on disk, making it available even after context compaction or session resumption. It serves a different purpose than in-context information.
- **Subagent delegation is always slower**: While spawning a subagent has overhead, it preserves the main agent's context budget for coordination. The net effect is often better quality, not worse speed.
- **/compact should be used preemptively**: /compact is most valuable when context is actually filling with verbose output. Using it too early discards useful context unnecessarily.

### Practice Focus

Questions present an extended exploration scenario where the agent's quality degrades over time and ask for the fix. Scratchpad files for persisting key findings, subagent delegation for isolating verbose exploration, and /compact for context recovery are the three main tools. Watch for questions about crash recovery — structured state exports with coordinator manifests is the pattern.

## Task Statement 5.5: Design human review workflows and confidence calibration

### Key Concepts

Aggregate accuracy metrics (e.g., "97% overall accuracy") can mask poor performance on specific document types or fields. A system that excels on standard invoices but fails on handwritten receipts might report high aggregate accuracy while producing unreliable results for an important document category. Performance must be validated by document type and field segment before reducing human review.

Stratified random sampling of high-confidence extractions provides ongoing error rate measurement and detects novel error patterns. Even when the system reports high confidence, a random sample should be routed to human review to verify that confidence is calibrated. This catches systematic biases that confidence scores alone cannot detect.

Field-level confidence scores, calibrated using labeled validation sets, enable targeted review routing. Instead of reviewing entire extractions, reviewers focus on fields where the model reports low confidence or where source documents contain ambiguous or contradictory information. This prioritizes limited reviewer capacity on the highest-risk fields.

The path to reducing human review is: validate accuracy by document type and field, confirm consistent performance across all segments, then gradually reduce review rates while maintaining stratified sampling as an ongoing monitoring mechanism.

### Common Misconceptions

- **High aggregate accuracy means the system is reliable**: Aggregate metrics hide per-category performance. A system can be 97% accurate overall while failing on 30% of a specific document type.
- **Model confidence equals actual accuracy**: Self-reported confidence requires calibration. Without calibration against labeled data, confidence scores are unreliable for routing decisions.
- **Human review can be eliminated once accuracy is high**: Stratified sampling should continue indefinitely as a monitoring mechanism. Novel document patterns can introduce new error types at any time.
- **Review entire extractions or nothing**: Field-level confidence enables targeted review of specific uncertain fields rather than full-extraction review, making much better use of limited reviewer time.
- **Performance on training data predicts production performance**: Production documents may include types, formats, or quality levels not represented in training/validation data. Ongoing monitoring is essential.

### Practice Focus

Questions test the distinction between aggregate and segmented accuracy, the need for calibration of confidence scores, and the design of ongoing monitoring through stratified sampling. If a scenario describes reducing human review based on aggregate metrics alone, the answer involves segment-level validation. If a scenario describes trusting model confidence without calibration, the answer involves labeled validation sets.

## Task Statement 5.6: Preserve information provenance and handle uncertainty in multi-source synthesis

### Key Concepts

Source attribution is lost during summarization steps when findings are compressed without preserving claim-source mappings. If a search subagent returns "Market size is $4.2B (Source: Gartner 2024)" and the summarization step produces "The market is large," both the specific figure and the source are lost. Claim-source mappings must be preserved through the entire pipeline.

Structured claim-source mappings require subagents to output findings as structured data: source URL, document name, relevant excerpt, publication date. Downstream agents must preserve these mappings through synthesis — every claim in the final report should be traceable to its source.

Conflicting statistics from credible sources should be annotated with source attribution rather than arbitrarily selecting one value. If Gartner says $4.2B and IDC says $3.8B, the synthesis should present both with attribution and note the discrepancy, not silently pick one. The coordinator or downstream consumer can then decide how to reconcile.

Temporal data is a specific source of apparent contradictions. A 2022 report and a 2024 report may cite different market sizes — this is not a contradiction but a temporal difference. Requiring publication/collection dates in structured outputs prevents temporal differences from being misinterpreted.

Different content types deserve different rendering in synthesis: financial data as tables, news events as prose, technical findings as structured lists. Converting everything to a uniform format (all prose or all bullets) loses the structure that makes each content type readable.

### Common Misconceptions

- **Summarization preserves attribution automatically**: It does not. Summarization systematically strips source references unless explicitly instructed to preserve claim-source mappings.
- **Conflicting sources mean one is wrong**: Credible sources may disagree due to different methodologies, time periods, or scopes. The correct handling is to present both with attribution, not to select one.
- **Temporal differences are contradictions**: Different dates explain different values. Without publication dates, a synthesis agent may flag a non-contradiction as an error or silently pick the "more recent" value.
- **Uniform formatting is professional**: Converting financial data to prose or technical findings to narrative paragraphs loses information-dense structure. Each content type should be rendered in its most appropriate format.
- **The synthesis agent should reconcile all conflicts**: Conflict reconciliation is a judgment call that may require domain expertise. The synthesis agent should annotate conflicts and let the coordinator or end user decide.

### Practice Focus

Questions about provenance test whether you understand that claim-source mappings must be explicitly preserved through the pipeline. If a synthesis report lacks citations, the fix is requiring subagents to output structured claim-source data and requiring downstream agents to preserve it. Conflict questions test whether you present both values with attribution versus selecting one. Temporal questions test whether you require dates to distinguish temporal differences from true contradictions.

## Recurring Themes

Several themes recur across this domain:

- **Explicit preservation over implicit trust**: Context does not preserve itself. Transactional facts need dedicated persistent storage. Source attribution needs structured claim-source mappings. Error context needs structured metadata. Relying on implicit preservation (summarization, generic error messages, unsourced claims) consistently fails.
- **Segmented analysis over aggregate metrics**: Aggregate accuracy, aggregate confidence, and aggregate error rates all hide important per-category performance differences. The exam consistently tests whether you analyze performance by segment (document type, field, error category) rather than relying on overall numbers.
- **Graceful degradation over binary success/failure**: Multi-agent systems should work with partial results and annotate gaps, not terminate on first failure. Extraction systems should flag uncertainty rather than fabricate or block. Escalation should be based on concrete criteria, not subjective assessment.
- **Position-aware context design**: The "lost in the middle" effect, context degradation in extended sessions, and progressive summarization losses all relate to how information is positioned and structured within context. Placing key information at the start, using explicit section headers, and persisting critical facts outside summarized history are all position-aware strategies.
- **Ongoing monitoring as a permanent practice**: Stratified sampling, coverage annotations, and confidence calibration are not one-time activities. They are ongoing mechanisms that catch novel error patterns and maintain system reliability as inputs change over time.
