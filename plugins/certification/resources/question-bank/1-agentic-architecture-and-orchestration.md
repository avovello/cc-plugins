# Domain 1: Agentic Architecture & Orchestration (27%)
## Comprehensive Question Bank

---

## Task Statement 1.1: Design and implement agentic loops for autonomous task execution

### Q1.1.1 — Scenario: Customer Support Resolution Agent
**Difficulty: Easy | Knowledge: Agentic loop lifecycle**

Your customer support agent processes a request and Claude responds with `stop_reason: "tool_use"` along with a `tool_use` block requesting `get_customer` with the customer's email. What should your agentic loop do next?

A) Execute the `get_customer` tool with the provided input, append the tool result to the conversation history, and send the updated conversation back to Claude for the next iteration.

B) Execute the `get_customer` tool, parse the result for a customer ID, and then immediately call `lookup_order` with that ID before sending anything back to Claude.

C) Check whether Claude also included text content in the response, and if so, present that text to the user and terminate the loop.

D) Execute the `get_customer` tool and start a new conversation with Claude containing only the tool result, discarding the prior conversation history.

**Correct Answer: A**

**Explanation:** The agentic loop lifecycle requires executing the requested tool, appending the result to conversation history, and sending the full updated conversation back to Claude so it can reason about the next action. Option B violates model-driven decision-making by pre-determining the next tool call programmatically. Option C is an anti-pattern — checking for assistant text content as a completion indicator is unreliable since Claude may include explanatory text alongside tool calls. Option D breaks conversational coherence by discarding history.

---

### Q1.1.2 — Scenario: Multi-Agent Research System
**Difficulty: Medium | Knowledge: stop_reason handling and anti-patterns**

Your research coordinator agent uses an agentic loop. A junior developer proposes the following termination logic:

```
if "RESEARCH COMPLETE" in assistant_response.text:
    return final_answer
elif iteration_count >= 10:
    return timeout_response
else:
    continue_loop()
```

Which two anti-patterns does this implementation exhibit?

A) Parsing natural language signals to determine loop termination, and setting arbitrary iteration caps as the primary stopping mechanism.

B) Checking for assistant text content as a completion indicator, and failing to append tool results to conversation history.

C) Using pre-configured decision trees instead of model-driven decisions, and not handling the "tool_use" stop_reason.

D) Setting arbitrary iteration caps as the primary stopping mechanism, and failing to distinguish between "tool_use" and "end_turn" stop reasons.

**Correct Answer: A**

**Explanation:** The code checks for a natural language string ("RESEARCH COMPLETE") to determine completion — this is unreliable because the model may phrase completion differently. It also uses an arbitrary iteration cap (10) as the primary stopping mechanism instead of relying on `stop_reason: "end_turn"`. The correct approach checks `stop_reason` values: continue when "tool_use", terminate when "end_turn". Option D is partially correct about iteration caps but wrong about the first anti-pattern — the code does not fail to distinguish stop reasons, it simply ignores them entirely in favor of text parsing.

---

### Q1.1.3 — Scenario: Developer Productivity with Claude
**Difficulty: Medium | Skill: Implementing agentic loop control flow**

You're building a developer productivity agent that explores codebases. Your agentic loop correctly checks `stop_reason` and executes tools. However, during testing you notice the agent enters an infinite loop — it keeps requesting the same `Read` tool call on the same file repeatedly. What is the most likely cause and appropriate fix?

A) The tool result is not being appended to the conversation history between iterations, so Claude doesn't know it already received the file contents and requests it again.

B) The `Read` tool is returning an error that Claude interprets as a missing file, causing it to retry. You should add retry limits on individual tool calls.

C) Claude's context window is full and it's losing track of earlier tool results. You should implement `/compact` between iterations.

D) The `stop_reason` is being misread — the loop should terminate after any `Read` operation since file reads are informational, not action-oriented.

**Correct Answer: A**

**Explanation:** The most common cause of infinite tool-call loops is failing to append tool results to the conversation context. Without seeing the result of its previous `Read` call, Claude has no evidence the file was read and logically requests it again. Option B describes a possible but less common issue and the fix (retry limits) is an anti-pattern when used as the primary mechanism. Option C is speculative — context window exhaustion would produce different symptoms. Option D fundamentally misunderstands the agentic loop: termination should be based on `stop_reason`, not tool type.

---

### Q1.1.4 — Scenario: Customer Support Resolution Agent
**Difficulty: Hard | Knowledge: Model-driven vs pre-configured decisions**

Your support agent handles returns, billing disputes, and account issues. A colleague proposes this architecture: a routing classifier first categorizes each request, then enables only the tools relevant to that category (e.g., returns get `lookup_order` + `process_refund`; billing gets `get_billing_history` + `apply_credit`). Multi-concern requests are split into separate pipelines. What is the primary disadvantage of this approach compared to giving Claude access to all relevant tools and letting it reason about which to use?

A) It replaces model-driven decision-making with pre-configured decision trees, preventing Claude from adapting when a request doesn't fit neatly into one category or when investigation reveals the initial categorization was wrong.

B) It increases latency because the routing classifier adds an extra API call before the agent begins processing each request.

C) It requires maintaining separate system prompts for each category, which increases configuration complexity and makes updates harder.

D) It reduces token efficiency because the classifier's output must be included in the context alongside the customer's original message.

**Correct Answer: A**

**Explanation:** The key distinction is between model-driven decision-making (Claude reasons about which tool to call based on evolving context) and pre-configured decision trees (tools are pre-selected based on static classification). The pre-configured approach breaks down when requests span categories, when initial classification is wrong, or when mid-conversation investigation reveals a different issue than expected. Options B, C, and D describe real but secondary concerns — the fundamental architectural problem is removing Claude's ability to adapt its tool usage as understanding develops.

---

### Q1.1.5 — Scenario: Structured Data Extraction
**Difficulty: Easy | Skill: Loop termination**

You're building an extraction pipeline where Claude processes a document by first calling `extract_metadata`, then `extract_line_items`, and finally returning structured results. After the second tool call returns results, Claude responds with `stop_reason: "end_turn"` and includes the final structured extraction in its text content. What should your loop do?

A) Terminate the loop and return Claude's response as the final extraction result.

B) Check whether all expected tools (`extract_metadata` and `extract_line_items`) were called before terminating, and re-prompt if any were skipped.

C) Send one more iteration to Claude asking it to confirm the extraction is complete before terminating.

D) Parse the text content for a valid JSON structure before terminating, and re-prompt if JSON is malformed.

**Correct Answer: A**

**Explanation:** When `stop_reason` is `"end_turn"`, Claude has determined it has completed its task. The agentic loop should trust this signal and terminate. Option B imposes a pre-configured sequence rather than trusting model-driven decisions. Option C adds an unnecessary round trip — the `end_turn` signal already indicates completion. Option D conflates loop termination logic with output validation; validation should happen after the loop terminates, not as a loop control mechanism.

---

## Task Statement 1.2: Orchestrate multi-agent systems with coordinator-subagent patterns

### Q1.2.1 — Scenario: Multi-Agent Research System
**Difficulty: Medium | Knowledge: Hub-and-spoke architecture**

Your multi-agent research system has a coordinator, a web search agent, a document analysis agent, and a synthesis agent. During testing, the web search agent directly passes its findings to the document analysis agent to cross-reference, bypassing the coordinator. This sometimes produces good results but occasionally causes cascading failures when the search results are malformed. What architectural principle does this violate, and why does it matter?

A) It violates hub-and-spoke architecture where all inter-subagent communication must route through the coordinator, which prevents observability into what data flows between agents and eliminates the coordinator's ability to handle errors consistently.

B) It violates subagent isolation because the web search agent should not know the document analysis agent exists — subagents should only communicate via shared memory.

C) It violates the principle of least privilege because the web search agent is accessing the document analysis agent's tools without authorization.

D) It violates context window management because the web search agent's full output consumes too many tokens in the document analysis agent's context.

**Correct Answer: A**

**Explanation:** Hub-and-spoke architecture requires all subagent communication to flow through the coordinator. This ensures observability (you can log and inspect all data flows), consistent error handling (the coordinator can catch malformed search results before they reach the document analysis agent), and controlled information flow. Option B incorrectly describes shared memory as the communication mechanism — subagents communicate through the coordinator via explicit context passing. Option C misapplies the principle of least privilege, which relates to tool access, not agent-to-agent communication. Option D describes a valid concern but not the architectural violation.

---

### Q1.2.2 — Scenario: Multi-Agent Research System
**Difficulty: Hard | Skill: Iterative refinement loops**

Your research system produces a report on "renewable energy policy trends." The coordinator delegates to search, analysis, and synthesis agents. The synthesis agent returns a coherent report, but it only covers solar and wind energy, missing geothermal, hydroelectric, and biomass. Logs show the coordinator accepted the synthesis output on the first pass. How should you modify the coordinator to prevent incomplete coverage?

A) Implement an iterative refinement loop where the coordinator evaluates the synthesis output for coverage gaps, re-delegates to the search and analysis agents with targeted queries for missing subtopics, and re-invokes synthesis until coverage is sufficient.

B) Add a separate validation agent that reviews synthesis output against a predefined taxonomy of renewable energy types and returns a pass/fail verdict.

C) Expand the web search agent's initial query set to always include all known subtypes of any topic, ensuring comprehensive initial coverage.

D) Instruct the synthesis agent to list any topics it suspects are missing at the end of its output, then have the coordinator check this list.

**Correct Answer: A**

**Explanation:** Iterative refinement loops are the recommended pattern for ensuring comprehensive coverage. The coordinator evaluates output quality, identifies gaps, and selectively re-delegates to fill them. This is adaptive and works across any topic. Option B adds infrastructure complexity when the coordinator can perform this evaluation itself with appropriate prompting. Option C assumes you can enumerate all subtypes in advance, which fails for novel or complex topics. Option D relies on the synthesis agent to identify its own blind spots — but it can only assess coverage of information it received, not information the upstream agents never found.

---

### Q1.2.3 — Scenario: Customer Support Resolution Agent
**Difficulty: Medium | Knowledge: Coordinator's role in task decomposition**

A customer writes: "I need to return order #4521 because the size is wrong, and also my last three billing statements have incorrect charges, and I'd like to update my shipping address." Your coordinator agent needs to handle this multi-concern request. What is the most effective decomposition strategy?

A) Decompose into three distinct items (return, billing dispute, address update), investigate each using shared context from the `get_customer` call, and synthesize a unified resolution addressing all three concerns.

B) Handle the concerns sequentially in the order mentioned, completing each one fully before moving to the next, presenting three separate responses to the customer.

C) Identify the most urgent concern (billing dispute because it involves money), handle that first, then ask the customer if they still need help with the other issues.

D) Route the entire message to a human agent because multi-concern requests exceed the complexity threshold for autonomous handling.

**Correct Answer: A**

**Explanation:** The recommended approach is to decompose multi-concern requests into distinct items, investigate each (potentially in parallel using shared context like the verified customer record), and synthesize a unified response. Option B handles them sequentially but presents three separate responses, which is poor customer experience. Option C prioritizes arbitrarily and risks the customer repeating themselves. Option D escalates unnecessarily — multi-concern requests aren't inherently beyond the agent's capability if each individual concern is within scope.

---

### Q1.2.4 — Scenario: Multi-Agent Research System
**Difficulty: Medium | Skill: Partitioning research scope**

Your coordinator assigns three search subagents to research "impact of remote work on productivity." Agent A searches for "remote work productivity studies," Agent B searches for "work from home productivity research," and Agent C searches for "telecommuting efficiency data." All three return largely overlapping results. How should you redesign the partitioning?

A) Assign distinct subtopics or source types to each agent — for example, Agent A covers academic studies, Agent B covers industry surveys and corporate reports, and Agent C covers government labor statistics.

B) Have each agent use a different search engine to ensure diverse sources even with similar queries.

C) Run all three agents with the same broad query and use the synthesis agent to deduplicate the results.

D) Reduce to a single search agent with a comprehensive query that covers all aspects, then split only if results are insufficient.

**Correct Answer: A**

**Explanation:** Effective scope partitioning assigns distinct subtopics or source types to each agent, minimizing duplication. Assigning by source type (academic, industry, government) ensures each agent explores a unique information space. Option B changes the tool but not the scope, so queries still overlap. Option C accepts duplication and shifts the deduplication burden to the synthesis agent, wasting tokens and processing time. Option D eliminates the parallelism benefit of multiple agents.

---

### Q1.2.5 — Scenario: Multi-Agent Research System
**Difficulty: Hard | Skill: Dynamic subagent selection**

Your coordinator agent has access to four subagents: web search, document analysis, synthesis, and data visualization. A user asks: "What is the capital of France?" The coordinator routes this through all four subagents sequentially: web search finds the answer, document analysis processes the search results, synthesis writes a report, and data visualization creates a chart. The result is correct but took 45 seconds. What coordinator design flaw does this reveal?

A) The coordinator always routes through the full pipeline instead of dynamically selecting which subagents to invoke based on query complexity — a simple factual question should skip unnecessary agents.

B) The coordinator should have used parallel execution instead of sequential execution to reduce the 45-second processing time.

C) The data visualization agent should not be part of the pipeline since geographic questions don't need charts.

D) The coordinator should maintain a cache of previously answered questions to avoid repeating the full pipeline for common queries.

**Correct Answer: A**

**Explanation:** The coordinator should analyze query requirements and dynamically select which subagents to invoke. A simple factual question requires at most a web search — routing through analysis, synthesis, and visualization is wasteful. Option B would reduce latency but doesn't address the fundamental issue of invoking unnecessary agents. Option C correctly identifies one unnecessary agent but misses that document analysis and synthesis are also unnecessary for this query. Option D adds caching infrastructure when the real fix is smarter delegation logic.

---

## Task Statement 1.3: Configure subagent invocation, context passing, and spawning

### Q1.3.1 — Scenario: Multi-Agent Research System
**Difficulty: Easy | Knowledge: Subagent context isolation**

Your synthesis subagent produces a report with missing information that the web search subagent clearly found earlier. The coordinator's logs confirm the web search returned comprehensive results. What is the most likely cause?

A) The coordinator did not explicitly include the web search findings in the synthesis subagent's prompt — subagents do not automatically inherit the coordinator's conversation history.

B) The synthesis subagent's context window was too small to hold the web search findings, causing them to be truncated.

C) The web search subagent's results were stored in shared memory that the synthesis subagent failed to access due to a race condition.

D) The synthesis subagent was configured with `allowedTools` that excluded access to the web search results.

**Correct Answer: A**

**Explanation:** Subagents operate with isolated context — they do not inherit the coordinator's conversation history automatically. The coordinator must explicitly include all relevant findings from prior agents in each subagent's prompt. Option B is possible but less likely when the cause is simply not passing the data. Option C describes shared memory, which is not how subagent communication works (it goes through the coordinator). Option D confuses tool access with context passing — `allowedTools` controls which tools the agent can call, not what information it can receive.

---

### Q1.3.2 — Scenario: Multi-Agent Research System
**Difficulty: Medium | Knowledge: Task tool and allowedTools**

You're setting up a coordinator agent that needs to delegate to specialized subagents. Your coordinator's `AgentDefinition` includes tools for web search and document analysis. During testing, the coordinator correctly decides to delegate to a search subagent but fails with an error when attempting to spawn it. What is the most likely configuration issue?

A) The coordinator's `allowedTools` does not include `"Task"` — the Task tool is the mechanism for spawning subagents and must be explicitly allowed.

B) The coordinator's system prompt does not mention the subagents by name, so it doesn't know they exist.

C) The subagent's `AgentDefinition` was not registered in the same configuration file as the coordinator.

D) The coordinator is attempting to spawn the subagent using a direct function call instead of the Task tool.

**Correct Answer: A**

**Explanation:** The Task tool is the mechanism for spawning subagents. If `allowedTools` doesn't include `"Task"`, the coordinator cannot invoke subagents regardless of how well its prompt describes the delegation strategy. Option B is incorrect — the coordinator doesn't need to know subagent names in its system prompt if the Task tool is available and subagent definitions are properly configured. Option C describes a concern that depends on implementation specifics, not a fundamental requirement. Option D describes an implementation approach that would only arise if the developer misunderstands the SDK.

---

### Q1.3.3 — Scenario: Multi-Agent Research System
**Difficulty: Hard | Skill: Preserving attribution in context passing**

Your web search subagent returns findings as plain text paragraphs:

```
"Remote work has been shown to increase productivity by 13% according to a Stanford study. A Microsoft report found that collaboration hours increased by 25% during remote work..."
```

When the synthesis subagent uses these findings, the final report attributes the 13% statistic to Microsoft and the collaboration finding to Stanford. What context passing improvement would prevent this?

A) Use structured data formats that separate content from metadata — each finding should include the claim, the source URL/document name, and page numbers, so the synthesis agent can maintain correct attribution.

B) Have the web search agent include inline citations in its text output (e.g., "[Stanford, 2023]") so the synthesis agent can copy them directly.

C) Instruct the synthesis agent to verify each claim's source by re-searching the web before including it in the final report.

D) Pass the raw search result URLs to the synthesis agent so it can fetch and verify each source independently.

**Correct Answer: A**

**Explanation:** Structured data formats that separate content from metadata (claims, sources, page numbers) enable downstream agents to preserve attribution accurately. When findings are passed as plain text, the synthesis agent must infer which source supports which claim, leading to misattribution. Option B is an improvement over plain text but inline citations in unstructured text are still fragile — they can be misassociated during synthesis. Option C adds latency and redundant API calls. Option D gives the synthesis agent raw data instead of processed findings, defeating the purpose of the search subagent.

---

### Q1.3.4 — Scenario: Multi-Agent Research System
**Difficulty: Medium | Skill: Parallel subagent spawning**

Your coordinator needs to delegate to three subagents: one for academic papers, one for news articles, and one for government reports. Currently, it spawns them sequentially across three separate turns, taking ~30 seconds total. How can you reduce this latency?

A) Have the coordinator emit all three Task tool calls in a single response, enabling parallel subagent execution rather than sequential turn-by-turn spawning.

B) Combine all three research tasks into a single subagent prompt that searches all source types, eliminating the overhead of multiple spawns.

C) Pre-warm the subagents by spawning them at system startup, then route tasks to already-running instances.

D) Reduce each subagent's `max_tokens` to speed up their individual responses.

**Correct Answer: A**

**Explanation:** Spawning parallel subagents by emitting multiple Task tool calls in a single coordinator response enables concurrent execution. This is the architecturally correct way to reduce latency while maintaining separation of concerns. Option B sacrifices the benefits of specialization and scope partitioning. Option C describes a persistent-agent model that doesn't match how the Task tool works. Option D may reduce response length but doesn't meaningfully impact total processing time when the bottleneck is sequential execution.

---

### Q1.3.5 — Scenario: Developer Productivity with Claude
**Difficulty: Medium | Skill: Coordinator prompt design**

Your coordinator agent's prompt includes step-by-step procedural instructions:

```
"Step 1: Send the query to the search agent. Step 2: Wait for results. Step 3: Send results to the analysis agent. Step 4: Wait for analysis. Step 5: Send analysis to the synthesis agent."
```

A colleague suggests replacing this with goal-oriented instructions. What is the advantage of goal-oriented coordinator prompts?

A) Goal-oriented prompts specify research goals and quality criteria rather than step-by-step procedures, enabling subagent adaptability — the coordinator can skip unnecessary steps, reorder tasks, or add extra investigation when needed.

B) Goal-oriented prompts use fewer tokens, reducing costs and leaving more context space for subagent results.

C) Goal-oriented prompts prevent the coordinator from making errors because it doesn't need to track which step it's on.

D) Goal-oriented prompts are easier for developers to write and maintain than procedural instructions.

**Correct Answer: A**

**Explanation:** Goal-oriented prompts enable adaptability. When a coordinator is given rigid steps, it follows them even when they're unnecessary (simple queries that don't need analysis) or insufficient (complex queries that need extra investigation). Specifying goals and quality criteria lets the coordinator exercise judgment about which subagents to invoke and when. Options B, C, and D describe potential secondary benefits but miss the core architectural advantage of adaptability.

---

## Task Statement 1.4: Implement multi-step workflows with enforcement and handoff patterns

### Q1.4.1 — Scenario: Customer Support Resolution Agent
**Difficulty: Medium | Knowledge: Programmatic vs prompt-based enforcement**

Your agent's system prompt states: "Always verify the customer's identity using get_customer before performing any account operations." Despite this instruction, production logs show that in 8% of cases, the agent skips identity verification when customers provide detailed account information upfront. Which statement best explains why this happens?

A) Prompt-based instructions have a non-zero failure rate for enforcing workflow ordering — when deterministic compliance is required, programmatic enforcement through hooks or prerequisite gates is necessary.

B) The 8% failure rate indicates the system prompt needs stronger wording, such as "CRITICAL: You MUST always verify identity first. Never skip this step under any circumstances."

C) The model is correctly exercising judgment by skipping unnecessary verification when the customer has already provided sufficient identifying information.

D) The failures occur because the system prompt is too long and the verification instruction is being lost in the middle of the context.

**Correct Answer: A**

**Explanation:** This is a core concept: prompt instructions are probabilistic, not deterministic. Even well-written instructions have a non-zero failure rate. When the business requirement demands 100% compliance (identity verification before financial operations), programmatic enforcement is required. Option B assumes stronger wording will achieve deterministic compliance, which it cannot. Option C reframes a bug as a feature — the business process requires verification regardless of what information the customer provides. Option D is speculative and misidentifies the root cause.

---

### Q1.4.2 — Scenario: Customer Support Resolution Agent
**Difficulty: Hard | Skill: Structured handoff summaries**

Your agent determines it cannot resolve a customer's request (a policy exception for a product outside the return window) and needs to escalate to a human agent. The human agent will handle the case in a separate system without access to the AI conversation transcript. Which handoff approach is most effective?

A) Compile a structured handoff summary including: customer ID, verified account details, root cause analysis (product outside return window, customer requesting exception), refund amount, actions already taken, and a recommended action for the human agent.

B) Transfer the full conversation transcript to the human agent's queue so they have complete context about the interaction.

C) Send the customer's original message and the agent's last response to the human agent, letting them pick up from where the AI left off.

D) Flag the case in the escalation system with the category "policy exception" and the customer ID, letting the human agent investigate from scratch.

**Correct Answer: A**

**Explanation:** Structured handoff summaries provide human agents with everything they need to continue resolution efficiently: verified identity, root cause, what's been attempted, and recommended next steps. This is essential when the human agent lacks access to the conversation transcript. Option B assumes transcript access, which the scenario explicitly rules out. Option C provides insufficient context — the human agent doesn't know what was already investigated. Option D forces the human agent to repeat the entire investigation, wasting time and frustrating the customer.

---

### Q1.4.3 — Scenario: Customer Support Resolution Agent
**Difficulty: Medium | Skill: Programmatic prerequisites**

You need to implement a programmatic prerequisite ensuring `process_refund` can only be called after `get_customer` has returned a verified customer ID. Which implementation approach provides the strongest guarantee?

A) Implement a hook that intercepts `process_refund` calls and checks whether a verified customer ID exists in the session state; if not, the hook blocks the call and returns an error message guiding the agent to verify the customer first.

B) Add `process_refund` to a conditional tool list that is only included in the agent's available tools after `get_customer` has been successfully called.

C) Include the prerequisite as the first line of the `process_refund` tool description: "PREREQUISITE: get_customer must be called first. Do not call this tool without a verified customer ID."

D) Implement a validation layer in the `process_refund` MCP tool itself that checks for a valid customer ID parameter and returns an error if missing.

**Correct Answer: A**

**Explanation:** A hook-based interceptor provides deterministic enforcement at the SDK level — the refund call is physically blocked until the prerequisite is met. Option B (conditional tool availability) also provides programmatic enforcement but is more complex to implement and may confuse the model when tools appear/disappear. Option C is prompt-based enforcement, which has a non-zero failure rate. Option D validates the parameter but doesn't ensure the customer was actually verified — the agent could fabricate or guess a customer ID without calling `get_customer`.

---

### Q1.4.4 — Scenario: Claude Code for Continuous Integration
**Difficulty: Easy | Knowledge: Handoff summary compilation**

Your CI pipeline agent detects a security vulnerability it cannot auto-fix (it requires a design decision about authentication strategy). It needs to hand off to a human developer. What should the handoff include?

A) A structured summary containing: the vulnerability found, the file and line number, why auto-fix isn't possible (requires design decision between two authentication strategies), and the two candidate approaches with trade-offs.

B) The full agent conversation log so the developer can see every step of the analysis.

C) A one-line message: "Security vulnerability found in auth module — please investigate."

D) The raw output of the security scanning tool the agent used, without additional context.

**Correct Answer: A**

**Explanation:** Effective handoff summaries compile the essential context a human needs to continue the work: what was found, where, why automated resolution isn't possible, and actionable next steps. This saves the developer from repeating the agent's investigation. Option B overwhelms the developer with irrelevant intermediate steps. Option C provides too little context to act on efficiently. Option D provides raw data without the agent's analysis of why it matters or what the options are.

---

### Q1.4.5 — Scenario: Claude Code for Continuous Integration
**Difficulty: Medium | Skill: Parallel investigation of multi-concern requests**

Your CI pipeline receives a PR that modifies both the payment processing module and the user notification system. The pipeline needs to check for security issues, API contract changes, and test coverage gaps. How should you structure the multi-step workflow?

A) Run parallel investigation passes — one per concern (security, API contracts, test coverage) — with each pass examining only the files relevant to its concern, then merge findings into a consolidated report with programmatic deduplication of overlapping issues.

B) Run a single comprehensive pass that examines all files for all three concerns simultaneously, relying on the agent to organize its findings by category.

C) Run sequential passes — security first, then API contracts, then test coverage — with each pass receiving the full PR diff.

D) Assign the payment module to one agent and the notification module to another, each checking all three concerns for their assigned module.

**Correct Answer: A**

**Explanation:** Parallel per-concern passes ensure each investigation type gets focused attention without dilution from competing objectives. Programmatic deduplication handles cases where the same code triggers multiple concern types. Option B suffers from attention dilution — a single pass examining three concerns across two modules produces inconsistent depth. Option C is unnecessarily sequential when the concerns are independent. Option D splits by module rather than concern, which means each agent must be expert in all three domains rather than focused on one.

---

## Task Statement 1.5: Apply Agent SDK hooks for tool call interception and data normalization

### Q1.5.1 — Scenario: Customer Support Resolution Agent
**Difficulty: Medium | Knowledge: PostToolUse hooks for data normalization**

Your agent calls three MCP tools that return dates in different formats: `get_customer` returns Unix timestamps (e.g., `1709251200`), `lookup_order` returns ISO 8601 strings (e.g., `"2024-03-01T00:00:00Z"`), and `get_billing_history` returns human-readable strings (e.g., `"March 1, 2024"`). The agent occasionally misinterprets dates when comparing across tools. What is the most reliable fix?

A) Implement PostToolUse hooks that normalize all date formats from these tools into a consistent format (e.g., ISO 8601) before the agent processes the results.

B) Add instructions to the system prompt explaining all three date formats and how to convert between them.

C) Standardize the date format at the MCP tool level so all tools return the same format.

D) Include few-shot examples in the system prompt showing correct date comparisons across different formats.

**Correct Answer: A**

**Explanation:** PostToolUse hooks intercept tool results and transform them before the model processes them, providing deterministic normalization. This eliminates the model's need to interpret heterogeneous formats. Option C (standardizing at the tool level) would be ideal but may not be feasible if the tools wrap third-party APIs you don't control. Options B and D rely on the model to perform conversions correctly, which is probabilistic — numerical date conversions are especially error-prone for LLMs.

---

### Q1.5.2 — Scenario: Customer Support Resolution Agent
**Difficulty: Hard | Skill: Tool call interception for policy enforcement**

Your company policy limits automated refunds to $500. Above that threshold, a human agent must approve. You need to enforce this rule. Your current system prompt states: "Do not process refunds above $500. Escalate these to a human agent." Logs show 3% of refunds above $500 are still being processed automatically. How should you fix this?

A) Implement a tool call interception hook that inspects the `amount` parameter of `process_refund` calls; if the amount exceeds $500, the hook blocks the call and returns a message directing the agent to use the `escalate_to_human` tool instead.

B) Strengthen the system prompt with uppercase emphasis: "CRITICAL RULE: NEVER process refunds above $500. This is a hard business rule with zero exceptions."

C) Add a validation check inside the `process_refund` MCP tool that rejects amounts over $500 and returns an error.

D) Implement a PostToolUse hook that checks the result of `process_refund` and reverses any refund above $500 after it's been processed.

**Correct Answer: A**

**Explanation:** Tool call interception hooks block policy-violating actions before they execute, providing deterministic enforcement. The hook catches the call, checks the amount, and redirects to escalation — the refund never processes. Option B is prompt-based enforcement, which already failed at 3%. Option C adds backend validation (which is good defense-in-depth) but returns an error rather than redirecting to the correct workflow — the agent may retry or fail ungracefully. Option D catches violations after execution, meaning the refund has already been processed and must be reversed, which is operationally risky.

---

### Q1.5.3 — Scenario: Structured Data Extraction
**Difficulty: Medium | Knowledge: Hooks vs prompt-based enforcement**

You're building an extraction pipeline where certain document types (medical records, legal contracts) must always be flagged for human review, regardless of extraction confidence. A developer proposes adding this rule to the system prompt. Under what condition is this approach acceptable?

A) It is never acceptable for this requirement — when business rules require guaranteed compliance, hooks provide deterministic enforcement that prompt instructions cannot match.

B) It is acceptable if the system prompt instruction is reinforced with few-shot examples showing the flagging behavior for each document type.

C) It is acceptable if the flagging rule is placed at the very beginning and very end of the system prompt to mitigate lost-in-the-middle effects.

D) It is acceptable only during prototyping; production deployment should migrate to hook-based enforcement.

**Correct Answer: D**

**Explanation:** The distinction between hooks and prompts is about risk tolerance. Prompt-based enforcement is reasonable during prototyping and iteration because it's fast to implement and modify. However, when the business rule requires guaranteed compliance (medical and legal documents must always get human review), production systems should use hooks for deterministic enforcement. Option A is too absolute — prompts are fine for non-critical experimentation. Options B and C attempt to improve prompt reliability but cannot achieve the deterministic guarantee required.

---

### Q1.5.4 — Scenario: Customer Support Resolution Agent
**Difficulty: Easy | Knowledge: Hook pattern types**

Which hook pattern would you use to convert a numeric order status code (e.g., `status: 3`) returned by `lookup_order` into a human-readable label (e.g., `status: "shipped"`) before the agent processes it?

A) A PostToolUse hook that intercepts the `lookup_order` result and transforms status codes into readable labels.

B) A PreToolUse hook that modifies the `lookup_order` request to ask for readable labels instead of numeric codes.

C) A system prompt instruction telling the agent that status code 3 means "shipped."

D) A custom MCP resource that provides a status code mapping table the agent can reference.

**Correct Answer: A**

**Explanation:** PostToolUse hooks intercept tool *results* for transformation before the model processes them — exactly the right pattern for converting output formats. Option B intercepts outgoing calls, not incoming results, and can't change what the backend returns. Option C relies on the model to perform the conversion, which is probabilistic. Option D provides reference information but still requires the model to look it up and apply it correctly.

---

## Task Statement 1.6: Design task decomposition strategies for complex workflows

### Q1.6.1 — Scenario: Claude Code for Continuous Integration
**Difficulty: Medium | Knowledge: Sequential pipeline vs dynamic decomposition**

Your CI pipeline needs to review a pull request that modifies 8 files. Two approaches are proposed:

**Approach 1:** Analyze each file individually for local issues (style, bugs, security), then run a cross-file integration pass examining data flow between modified files.

**Approach 2:** Send the agent the list of modified files and instruct it to "investigate the PR and report all issues," letting it adaptively decide which files to examine and in what order.

Which approach is better suited for this specific workflow, and why?

A) Approach 1, because code review is a predictable multi-aspect task where prompt chaining into focused per-file passes plus an integration pass avoids attention dilution and ensures consistent depth across all files.

B) Approach 2, because adaptive investigation allows the agent to prioritize high-risk files and skip low-risk ones, reducing unnecessary analysis.

C) Approach 1, because sequential processing of 8 files is more token-efficient than letting the agent decide its own exploration path.

D) Approach 2, because the agent may discover cross-file issues during individual file review that a rigid pipeline would miss.

**Correct Answer: A**

**Explanation:** Code review is a predictable, structured task — you know you need local analysis of each file plus cross-file integration review. Prompt chaining into focused passes ensures every file gets consistent attention and avoids the attention dilution that causes the "detailed feedback for some files, superficial for others" problem. Option B's adaptive approach is better suited for open-ended investigation tasks, not structured reviews. Option C mentions token efficiency, which isn't the primary benefit. Option D misunderstands the pipeline — the integration pass specifically addresses cross-file issues.

---

### Q1.6.2 — Scenario: Developer Productivity with Claude
**Difficulty: Hard | Skill: Adaptive decomposition for open-ended tasks**

A developer asks: "Add comprehensive tests to this legacy codebase." The codebase has 200+ files with no existing test suite. Which task decomposition strategy is most appropriate?

A) Dynamic adaptive decomposition: first map the codebase structure, identify high-impact areas (critical business logic, frequently modified files), create a prioritized test plan, then iteratively implement tests — adapting the plan as dependencies and complexities are discovered.

B) Prompt chaining: systematically generate tests for each file in alphabetical order, reviewing the generated tests at the end.

C) Delegate to subagents: assign one subagent per directory to generate tests in parallel, then merge all test files.

D) Single-pass execution: provide the entire codebase and instruct the agent to generate a complete test suite in one response.

**Correct Answer: A**

**Explanation:** "Add comprehensive tests to a legacy codebase" is an open-ended task where the optimal approach depends on what you discover as you go — which modules are most critical, which have complex dependencies, which are testable without major refactoring. Adaptive decomposition allows the plan to evolve as understanding deepens. Option B applies a rigid sequence inappropriate for varying complexity. Option C parallelizes without first understanding priorities or dependencies. Option D exceeds any reasonable context window and produces low-quality output.

---

### Q1.6.3 — Scenario: Claude Code for Continuous Integration
**Difficulty: Medium | Knowledge: Prompt chaining patterns**

Your team uses Claude Code to review PRs. Currently, a single review prompt examines all changed files and produces mixed results — some files get thorough analysis while others are barely mentioned. You decide to implement prompt chaining. Which decomposition is most effective?

A) Split into per-file local analysis passes (style, bugs, security for each file individually), then run a separate cross-file integration pass examining how changes in one file affect behavior in others.

B) Split by review category: first pass checks all files for style issues, second pass checks all files for bugs, third pass checks all files for security issues.

C) Split by file importance: first pass reviews the most critical files in detail, second pass quickly scans remaining files.

D) Split by reviewer expertise: one pass simulates a senior developer's review, another simulates a security engineer's review.

**Correct Answer: A**

**Explanation:** Per-file local analysis ensures each file gets consistent, focused attention, eliminating the attention dilution that caused the original problem. The separate cross-file integration pass catches data flow issues, API contract changes, and other inter-file dependencies that per-file passes miss. Option B splits by concern type but still processes all files simultaneously in each pass, not solving the attention dilution. Option C introduces subjective prioritization and may miss issues in "less critical" files. Option D doesn't address the structural problem.

---

### Q1.6.4 — Scenario: Claude Code for Continuous Integration
**Difficulty: Easy | Knowledge: When to use prompt chaining vs single pass**

Your CI pipeline runs a linting check on changed files. Each file is checked against the same set of style rules with no cross-file dependencies. Should you use prompt chaining (one pass per file) or a single pass for all files?

A) Use prompt chaining with one pass per file — even though the rules are identical, per-file passes ensure consistent depth of analysis and prevent attention dilution where some files get thorough feedback while others are barely mentioned.

B) Use a single pass for all files since the rules are identical and there are no cross-file dependencies, making this a simple task that doesn't benefit from decomposition.

C) Use prompt chaining with one pass per rule category (e.g., naming conventions, formatting, complexity) across all files.

D) Use a single pass but limit the number of files to 5 per pass to avoid context overflow.

**Correct Answer: A**

**Explanation:** Even for uniform tasks, per-file passes prevent attention dilution — the well-documented problem where a single pass gives detailed feedback for early files and superficial feedback for later ones. Each file gets focused, consistent analysis. Option B underestimates attention dilution effects when processing multiple files. Option C splits by rule type but still processes all files simultaneously in each pass. Option D is an arbitrary limit that doesn't address the core attention dilution issue.

---

### Q1.6.5 — Scenario: Claude Code for Continuous Integration
**Difficulty: Medium | Skill: Adaptive investigation plans**

A CI pipeline detects that a PR introduces a new dependency. The pipeline needs to assess the dependency's security posture, license compatibility, and impact on build size. The assessment difficulty varies — license checks are straightforward, but security posture requires investigating the dependency's vulnerability history, maintainer activity, and transitive dependencies. How should you decompose this task?

A) Start with the straightforward checks (license compatibility, build size impact) as sequential prompt-chained steps, then use adaptive decomposition for the security assessment — letting the agent investigate vulnerability databases, maintainer history, and transitive dependencies iteratively based on what it discovers.

B) Run all three checks in a single pass since they all concern the same dependency.

C) Run all three checks as identical prompt-chained steps with the same depth of analysis for each.

D) Skip the security assessment and flag the dependency for manual human review, only automating the license and build size checks.

**Correct Answer: A**

**Explanation:** This combines prompt chaining for predictable tasks (license, build size) with adaptive decomposition for the open-ended security investigation. The security assessment's depth depends on what the agent finds — a well-maintained dependency with no CVEs needs less investigation than one with a complex vulnerability history. Option B suffers from attention dilution across dissimilar tasks. Option C applies uniform depth when the tasks have different complexity profiles. Option D unnecessarily limits automation — the agent can provide a useful initial security assessment even if humans make the final call.

---

## Task Statement 1.7: Manage session state, resumption, and forking

### Q1.7.1 — Scenario: Developer Productivity with Claude
**Difficulty: Easy | Knowledge: Named session resumption**

You're exploring a large codebase over multiple days. Yesterday you spent 2 hours mapping the authentication module. Today you want to continue that exploration. Which approach preserves your prior context?

A) Use `--resume auth-exploration` to continue the named session from yesterday, picking up where you left off with the codebase exploration context intact.

B) Start a new session and paste a summary of yesterday's findings into the first message.

C) Use `fork_session` to create a branch of yesterday's session for today's work.

D) Open Claude Code and it will automatically resume your most recent session.

**Correct Answer: A**

**Explanation:** Named session resumption (`--resume <session-name>`) is designed for exactly this purpose — continuing a specific prior conversation across work sessions. Option B discards the detailed context from yesterday and relies on a manual summary. Option C (forking) is for creating parallel exploration branches from a shared baseline, not for simply continuing work. Option D incorrectly assumes automatic session resumption.

---

### Q1.7.2 — Scenario: Developer Productivity with Claude
**Difficulty: Medium | Knowledge: fork_session use cases**

You've spent 30 minutes exploring a codebase and have identified that a performance issue could be solved either by implementing caching or by optimizing the database queries. You want to explore both approaches to compare them before committing to one. Which session management approach is most appropriate?

A) Use `fork_session` to create two independent branches from the current shared analysis baseline, exploring the caching approach in one fork and the query optimization approach in the other.

B) Continue in the current session, first fully exploring the caching approach, then starting a new exploration of the query optimization approach.

C) Use `--resume` to create two separate named sessions, copying the current context into each.

D) Start two completely new sessions and re-explain the codebase context in each one.

**Correct Answer: A**

**Explanation:** `fork_session` is specifically designed for creating independent branches from a shared analysis baseline to explore divergent approaches. Both forks retain the 30 minutes of codebase exploration context, and each can independently explore a different solution. Option B explores sequentially, which means the caching exploration's context may influence or bias the query optimization analysis. Option C misuses `--resume`, which continues an existing session rather than forking it. Option D wastes time re-establishing context that's already been built.

---

### Q1.7.3 — Scenario: Developer Productivity with Claude
**Difficulty: Hard | Skill: Choosing resumption vs fresh session**

You used Claude Code yesterday to analyze a codebase's authentication module and generate a list of recommended refactorings. Overnight, another developer merged a major PR that restructured the auth module's database layer — roughly 40% of the files you analyzed have changed. You need to continue the refactoring work today. Which approach is most reliable?

A) Start a new session with a structured summary of yesterday's key findings and the refactoring plan, noting which areas are affected by the overnight changes — this avoids stale tool results while preserving your analytical conclusions.

B) Use `--resume` to continue yesterday's session, then inform the agent about the overnight changes and ask it to re-analyze the affected files.

C) Use `--resume` to continue yesterday's session and proceed with the refactoring plan as-is, since the architectural-level findings are likely still valid.

D) Use `fork_session` to create a branch from yesterday's session for re-analysis of the changed files.

**Correct Answer: A**

**Explanation:** When prior tool results are significantly stale (40% of analyzed files changed), starting fresh with injected summaries is more reliable than resuming with stale context. The structured summary preserves analytical conclusions and the refactoring plan while avoiding the risk of the model reasoning from outdated file contents. Option B attempts to patch the session but the model may still reference stale tool results from yesterday's exploration. Option C ignores significant changes that invalidate specific findings. Option D creates a fork but both branches still contain stale tool results.

---

### Q1.7.4 — Scenario: Developer Productivity with Claude
**Difficulty: Medium | Skill: Informing resumed sessions about changes**

You resume a session where you previously analyzed five files in a module. Since your last session, `auth_handler.py` was modified to add a new validation function. How should you inform the resumed session about this change?

A) Tell the agent specifically which file changed and what was added, so it can perform targeted re-analysis of `auth_handler.py` and its dependents without re-exploring the entire module.

B) Ask the agent to re-read all five files to ensure its understanding is current.

C) Don't inform the agent — it will detect the changes automatically when it next reads the file.

D) Provide a diff of the changes so the agent can process exactly what changed.

**Correct Answer: A**

**Explanation:** When resuming sessions after code modifications, informing the agent about specific file changes enables targeted re-analysis. The agent can focus on `auth_handler.py` and any files that depend on it rather than re-exploring the entire module. Option B is wasteful — re-reading all five files when only one changed. Option C is risky — the agent may not read the file again if it believes it already has current information. Option D (providing a diff) shows what changed syntactically but doesn't explain the purpose — the agent needs to understand why the validation function was added (e.g., new compliance requirement) to assess downstream impact on dependent files.

---

### Q1.7.5 — Scenario: Claude Code for Continuous Integration
**Difficulty: Medium | Knowledge: Fork vs fresh session for review workflows**

Your CI pipeline uses Claude Code to generate code and then review it. Currently, both steps happen in the same session. A colleague proposes using `fork_session` after generation to create a separate branch for the review step, arguing it provides isolation. Why is `fork_session` less effective than starting a completely fresh review session for this use case?

A) `fork_session` creates a branch that inherits the full conversation history including the generation reasoning — the review fork still has the generator's context, reducing review independence. A fresh session with only the generated code and review criteria provides true isolation.

B) `fork_session` is slower than starting a fresh session because it must copy the full conversation state.

C) `fork_session` is designed for exploring alternative implementations, not for review workflows.

D) `fork_session` doesn't preserve tool results from the parent session, so the reviewer can't see the generated code.

**Correct Answer: A**

**Explanation:** The key distinction is that `fork_session` inherits the parent's full conversation history, including all reasoning context from the generation phase. This means the forked review session is still anchored to the generation logic, undermining review independence. A fresh session that receives only the generated code and explicit review criteria provides genuine isolation — the reviewer evaluates the code without the generator's reasoning influencing its judgment. Option B focuses on performance, which is not the issue. Option C is too narrow — fork_session has many uses, but isolation from prior reasoning isn't one of them. Option D is incorrect — forked sessions do inherit tool results.

---

## Cross-Domain Questions (Domain 1 intersecting with other domains)

### Q1.X.1 — Scenario: Customer Support Resolution Agent
**Difficulty: Hard | Domains: 1 (Architecture) + 2 (Tool Design) + 5 (Reliability)**

Your support agent uses four MCP tools: `get_customer`, `lookup_order`, `process_refund`, and `escalate_to_human`. Production monitoring reveals three issues simultaneously:

1. The agent occasionally calls `process_refund` without first calling `get_customer`
2. When `lookup_order` returns a timeout error, the agent tells the customer "your order doesn't exist"
3. Refunds over $500 are sometimes processed without human approval

Which combination of changes addresses all three issues most effectively?

A) A programmatic prerequisite hook blocking `process_refund` until `get_customer` succeeds; structured error responses from `lookup_order` distinguishing timeouts from "not found"; and a tool call interception hook blocking refunds over $500 and redirecting to `escalate_to_human`.

B) Enhanced system prompt instructions covering all three rules with emphasis markers and few-shot examples for each scenario.

C) A PreToolUse hook that validates all tool calls against a dependency graph; a PostToolUse hook that retries all failed tool calls automatically; and a global refund limit in the `process_refund` tool backend.

D) A routing classifier that pre-determines the correct tool sequence for each request type; automatic retry logic for all tool timeouts; and removal of `process_refund` from the agent's tools entirely, requiring all refunds to go through humans.

**Correct Answer: A**

**Explanation:** Each issue requires a specific, targeted fix. Issue 1 (ordering) needs programmatic prerequisite enforcement since prompt-based approaches already failed. Issue 2 (misinterpreted errors) needs structured error responses that distinguish transient failures (timeouts) from valid results (order not found). Issue 3 (policy violation) needs tool call interception to block the action before it executes. Option B uses prompt-based enforcement for all three, which provides no deterministic guarantees. Option C's automatic retry of all failures doesn't solve the misinterpretation problem and the backend limit doesn't redirect to the correct workflow. Option D over-constrains the system by removing autonomous refund capability entirely.

---

### Q1.X.2 — Scenario: Multi-Agent Research System
**Difficulty: Hard | Domains: 1 (Architecture) + 5 (Context Management)**

Your coordinator delegates to a search subagent that returns 15 pages of raw search results. The document analysis subagent receives these results but produces shallow analysis, missing key details from the middle of the search results. The synthesis subagent then produces a report with gaps in coverage. Two problems are interacting: verbose context and position effects. What is the most effective combined fix?

A) Have the search subagent return structured, condensed findings (key facts, citations, relevance scores) instead of raw results, and have the coordinator place key findings summaries at the beginning of the input when passing context to downstream agents.

B) Increase the document analysis subagent's `max_tokens` to ensure it can process all 15 pages thoroughly.

C) Split the search results into three batches and run three parallel document analysis subagents, one per batch.

D) Add instructions to the document analysis subagent's prompt to "pay equal attention to all parts of the input."

**Correct Answer: A**

**Explanation:** This addresses both problems. Structured condensed findings reduce verbose context so only relevant information is passed (solving the token budget issue). Placing summaries at the beginning mitigates the "lost in the middle" effect where models process information at the start and end more reliably than the middle. Option B increases output capacity but doesn't help the model attend to middle content. Option C reduces per-agent input size but adds coordination complexity without addressing the position effect within each batch. Option D is a prompt instruction that doesn't reliably mitigate a known cognitive limitation of language models.

---

*End of Domain 1 Question Bank*
*Total: 36 questions covering all 7 task statements + 2 cross-domain questions*
*Distribution: 7 Easy, 19 Medium, 10 Hard*