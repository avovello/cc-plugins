# Domain 2: Tool Design & MCP Integration (18%)
## Comprehensive Question Bank

---

## Task Statement 2.1: Design effective tool interfaces with clear descriptions and boundaries

### Q2.1.1 — Scenario: Customer Support Resolution Agent
**Difficulty: Easy | Knowledge: Tool descriptions as primary selection mechanism**

Your agent has two tools: `get_customer` (description: "Retrieves customer information") and `lookup_order` (description: "Retrieves order details"). When users ask "what's the status of my order #12345?", the agent calls `get_customer` instead of `lookup_order` about 30% of the time. What is the root cause?

A) The tool descriptions are too minimal — LLMs use descriptions as their primary mechanism for tool selection, and these descriptions don't provide enough context to reliably differentiate when to use each tool.

B) The agent's system prompt doesn't include routing rules specifying which tool to use for order-related queries.

C) The `get_customer` tool is listed before `lookup_order` in the tool configuration, causing a positional bias.

D) The model is attempting to verify the customer's identity before looking up the order, which is actually correct behavior.

**Correct Answer: A**

**Explanation:** Tool descriptions are the primary mechanism LLMs use for tool selection. When descriptions are minimal and similar ("Retrieves customer information" vs "Retrieves order details"), the model lacks sufficient context to differentiate reliably. The fix is to expand descriptions with input formats, example queries, edge cases, and boundary explanations. Option B adds routing logic outside the tool system when the descriptions themselves should solve this. Option C overstates positional bias — it exists but isn't the dominant factor when descriptions are ambiguous. Option D would only explain the behavior if the agent consistently called `get_customer` first, not as a replacement.

---

### Q2.1.2 — Scenario: Multi-Agent Research System
**Difficulty: Medium | Knowledge: Ambiguous tool descriptions cause misrouting**

Your research system has two tools: `analyze_content` (description: "Analyzes content and extracts key information") and `analyze_document` (description: "Analyzes documents and extracts relevant data"). The coordinator frequently routes academic papers to `analyze_content` and web articles to `analyze_document`, even though `analyze_document` is designed for all file-based analysis and `analyze_content` is meant for web-scraped text. What is the most effective fix?

A) Rename the tools and rewrite descriptions to eliminate functional overlap — for example, rename `analyze_content` to `extract_web_results` with a description specifying it processes web-scraped text, and rename `analyze_document` to `parse_uploaded_files` specifying it handles PDFs, DOCs, and other file uploads.

B) Add a routing instruction to the coordinator's system prompt: "Use analyze_content for web results and analyze_document for uploaded files."

C) Merge both tools into a single `analyze` tool that auto-detects the input type and routes internally.

D) Add a `source_type` parameter to both tools that the coordinator must specify ("web" or "document"), forcing explicit routing.

**Correct Answer: A**

**Explanation:** When tool names and descriptions have near-identical wording ("analyzes content" vs "analyzes documents"), the model infers distinctions that don't match the developer's intent. Renaming tools and rewriting descriptions to clearly differentiate purpose, expected inputs, and boundaries directly solves the root cause. Option B patches with prompt instructions but doesn't fix the confusing descriptions — new agents or changed prompts will reintroduce the problem. Option C eliminates the selection problem but sacrifices the benefits of specialized tool logic. Option D adds complexity without fixing the descriptions that caused the confusion.

---

### Q2.1.3 — Scenario: Developer Productivity with Claude
**Difficulty: Hard | Knowledge: System prompt wording affecting tool selection**

Your developer productivity agent has a well-described `search_codebase` MCP tool and the built-in `Grep` tool. Despite `search_codebase` being more capable (it indexes the codebase and supports semantic search), the agent almost always uses `Grep` instead. The agent's system prompt includes the instruction: "When exploring code, use grep-like searches to find relevant files and functions." What is the most likely cause of the misrouting?

A) The system prompt instruction contains keyword-sensitive language ("grep-like") that creates an unintended association with the built-in `Grep` tool, overriding the MCP tool's description — the prompt should be reviewed and reworded to avoid naming specific tools when describing general search strategies.

B) Built-in tools always take priority over MCP tools in Claude's tool selection logic, regardless of descriptions.

C) The `search_codebase` tool's description is too long, causing the model to favor the simpler built-in `Grep` tool.

D) MCP tools are slower to invoke than built-in tools, and the model has learned to prefer faster options.

**Correct Answer: A**

**Explanation:** System prompt wording can create unintended tool associations. The phrase "grep-like" directly biases the model toward the built-in `Grep` tool, even when the MCP `search_codebase` tool is more appropriate. This is a documented consideration: prompts should be reviewed for keyword-sensitive instructions that override well-written tool descriptions. Option B is incorrect — there's no hard priority rule favoring built-in tools. Option C reverses the actual dynamic — more detailed descriptions generally improve selection. Option D attributes learning behavior that doesn't match how tool selection works.

---

### Q2.1.4 — Scenario: Structured Data Extraction
**Difficulty: Medium | Skill: Splitting generic tools into purpose-specific tools**

Your extraction pipeline uses a single tool called `analyze_document` that handles metadata extraction, content summarization, and claim verification. During testing, you find the agent frequently calls `analyze_document` to summarize when it should be extracting data points, and sometimes mixes extraction output with summary text. What redesign would most improve reliability?

A) Split `analyze_document` into three purpose-specific tools — `extract_data_points`, `summarize_content`, and `verify_claim_against_source` — each with defined input/output contracts specifying exactly what it accepts and returns.

B) Add a `mode` parameter to `analyze_document` (values: "extract", "summarize", "verify") and instruct the agent to always specify the mode.

C) Create a routing layer that inspects the agent's intent and internally selects the correct `analyze_document` mode.

D) Add detailed few-shot examples to the system prompt showing when to use each mode of `analyze_document`.

**Correct Answer: A**

**Explanation:** Splitting a generic tool into purpose-specific tools with defined input/output contracts gives the model clear, unambiguous choices. Each tool's description can precisely specify its purpose, inputs, and outputs without competing with other functionalities. Option B adds a parameter but the model still faces the same selection challenge — deciding which mode to use based on the same generic description. Option C adds infrastructure complexity when the tool design itself should solve the problem. Option D addresses a symptom with prompt engineering instead of fixing the architectural cause.

---

### Q2.1.5 — Scenario: Customer Support Resolution Agent
**Difficulty: Hard | Skill: Writing comprehensive tool descriptions**

You're rewriting the description for the `lookup_order` tool. Which description would most effectively prevent misrouting and misuse?

A) "Retrieves order details by order ID or tracking number. Input: order_id (string, e.g., '#12345') OR tracking_number (string, e.g., 'TRK-98765'). Returns: order status, items, shipping details, delivery dates, and return eligibility. Use this tool when the customer asks about an order's status, delivery, items, or return eligibility. Do NOT use for customer account information (use get_customer instead) or billing/payment issues (use get_billing_history instead)."

B) "Looks up order information. Accepts an order ID or tracking number and returns order details."

C) "Use this tool to get information about customer orders. It can look up orders by ID and returns all relevant data about the order."

D) "Order lookup tool. Input: order identifier. Output: order data. Supports order IDs and tracking numbers."

**Correct Answer: A**

**Explanation:** Option A follows all best practices for tool descriptions: it specifies input formats with examples, lists what the tool returns, explains when to use it, and explicitly defines boundaries by listing when NOT to use it and what alternatives to choose instead. Options B, C, and D are progressively less detailed — they lack input format examples, output specifications, use-case guidance, and boundary explanations, which are all factors that contribute to unreliable tool selection.

---

### Q2.1.6 — Scenario: Developer Productivity with Claude
**Difficulty: Medium | Skill: Enhancing MCP tool descriptions for adoption**

You've configured a `jira_search` MCP server for your development team. Despite being available, the agent consistently uses the built-in `Bash` tool to call the Jira API via curl instead. The `jira_search` tool description reads: "Searches Jira." What is the most effective first step?

A) Enhance the `jira_search` tool description to explain its capabilities and outputs in detail — for example, "Searches Jira issues by JQL query, project key, or natural language description. Returns issue key, summary, status, assignee, priority, and labels. Supports filtering by status, sprint, and date range. Use this instead of manual API calls for any Jira-related lookups."

B) Remove the `Bash` tool from the agent's available tools to force it to use `jira_search`.

C) Add a system prompt rule: "Always use the jira_search tool for Jira-related queries. Never use Bash to call Jira APIs."

D) Configure the agent with `tool_choice: {"type": "tool", "name": "jira_search"}` to force Jira tool usage.

**Correct Answer: A**

**Explanation:** When MCP tools have minimal descriptions, the agent doesn't understand their capabilities and defaults to general-purpose tools it knows well (like `Bash`). Enhancing the description to explain what the tool does, what it accepts, and what it returns gives the model the information needed to prefer it. Option B removes a general-purpose tool the agent needs for other tasks. Option C patches with prompt instructions but doesn't fix the description gap. Option D forces a specific tool regardless of context, which breaks when the query isn't Jira-related.

---

## Task Statement 2.2: Implement structured error responses for MCP tools

### Q2.2.1 — Scenario: Customer Support Resolution Agent
**Difficulty: Easy | Knowledge: MCP isError flag pattern**

Your `lookup_order` MCP tool encounters a database timeout. Currently it returns: `{"error": "Operation failed"}`. The agent tells the customer: "I wasn't able to find your order. It may not exist in our system." What change to the error response would prevent this misinterpretation?

A) Return a structured error with the `isError` flag set, including an `errorCategory` of "transient", `isRetryable: true`, and a description explaining the database is temporarily unavailable — this lets the agent distinguish a temporary failure from a missing order.

B) Return the error with an HTTP 500 status code so the agent knows it's a server error.

C) Return `{"error": "Database timeout. Please retry."}` as the tool result.

D) Implement automatic retry logic inside the MCP tool so the agent never sees the error.

**Correct Answer: A**

**Explanation:** The `isError` flag pattern with structured metadata (error category, retryability, description) gives the agent the information it needs to respond appropriately. A "transient" error with `isRetryable: true` tells the agent this is temporary and can be retried, not that the order doesn't exist. Option B uses HTTP semantics that don't map to MCP tool communication. Option C improves the message but without the `isError` flag and structured metadata, the agent may still misinterpret. Option D hides errors, which prevents the agent from informing the customer about delays.

---

### Q2.2.2 — Scenario: Customer Support Resolution Agent
**Difficulty: Medium | Knowledge: Error type distinctions**

Your `process_refund` tool receives a request to refund an order that's outside the 30-day return window. Currently it returns: `{"isError": true, "content": "Refund failed"}`. The agent retries the refund three times before giving up and telling the customer the system is experiencing issues. What is wrong with the error response?

A) The error lacks an `errorCategory` and `isRetryable` flag — it should return `errorCategory: "business"`, `isRetryable: false`, and a customer-friendly explanation that the order is outside the return window, so the agent knows not to retry and can explain the actual reason.

B) The error should not use the `isError` flag for business rule violations — only transient technical errors should set `isError`.

C) The error message should include the exact policy text about the 30-day window so the agent can cite it to the customer.

D) The tool should not return an error at all — it should process the refund and let the business rules be enforced elsewhere.

**Correct Answer: A**

**Explanation:** Without structured metadata distinguishing error types, the agent treats all errors the same — typically retrying. By including `errorCategory: "business"`, `isRetryable: false`, and a clear explanation, the agent knows this is a policy violation (not a transient failure), shouldn't retry, and can explain the actual reason to the customer. Option B is incorrect — `isError` is appropriate for any tool failure including business rule violations. Option C over-specifies — the agent needs enough context to explain, not the full policy text. Option D ignores the business requirement entirely.

---

### Q2.2.3 — Scenario: Multi-Agent Research System
**Difficulty: Hard | Skill: Local recovery before propagation**

Your web search subagent encounters a timeout from one of three search providers it queries. Currently, it immediately propagates the full error to the coordinator with the message "Search failed." The coordinator then terminates the entire research workflow. How should error handling be restructured?

A) The subagent should implement local recovery for transient failures — retry the timed-out provider, and if it still fails, proceed with results from the two successful providers. Only propagate to the coordinator errors that cannot be resolved locally, including partial results and what was attempted.

B) The subagent should retry the failed provider with exponential backoff up to 5 times, then propagate success with whatever results it has.

C) The coordinator should implement retry logic that re-invokes the entire web search subagent when it receives an error.

D) The subagent should silently drop the failed provider's results and return the remaining results as a complete success.

**Correct Answer: A**

**Explanation:** Local error recovery within subagents is the recommended pattern. The subagent handles what it can (retrying transient failures) and proceeds with partial results when possible. When propagating to the coordinator, it includes structured context about what failed, what was attempted, and what partial results are available — enabling the coordinator to make an informed decision. Option B is rigid about retry count without adapting to the error type. Option C re-runs the entire subagent when only one provider failed. Option D silently suppresses the failure, which is an anti-pattern — the coordinator can't annotate coverage gaps if it doesn't know about the failure.

---

### Q2.2.4 — Scenario: Developer Productivity with Claude
**Difficulty: Medium | Knowledge: Access failures vs valid empty results**

Your `search_codebase` MCP tool searches for function definitions matching a query. When the codebase index is temporarily unavailable, the tool returns `{"results": [], "count": 0}`. When a query has no matches in an available index, it returns the identical response. Why is this a problem, and what should change?

A) The agent cannot distinguish between "the index is down" (an access failure needing a retry decision) and "no functions match your query" (a valid empty result). The tool should return an `isError` response for index unavailability and a success response with empty results for valid queries with no matches.

B) The tool should always return at least one result to avoid confusion, even if it's a "no match" placeholder object.

C) The agent should be instructed via the system prompt to always retry empty results once to rule out transient failures.

D) The tool should return a different HTTP status code for each case so the agent can differentiate.

**Correct Answer: A**

**Explanation:** This is a critical distinction in error design. Access failures (index unavailable) require retry decisions — the agent might wait and retry or inform the user. Valid empty results (no matches found) are successful outcomes that should prompt the agent to try different search terms or inform the user. When both return identical responses, the agent has no basis for appropriate action. Option B introduces fake results that corrupt the information pipeline. Option C wastes resources retrying valid empty results. Option D uses HTTP semantics that don't apply to MCP tool communication.

---

### Q2.2.5 — Scenario: Customer Support Resolution Agent
**Difficulty: Medium | Skill: Structured error metadata**

You're implementing error responses for your `get_customer` MCP tool. Match each scenario to its correct structured error response:

**Scenario X:** Customer provides an email that doesn't match any account.
**Scenario Y:** The customer database is undergoing maintenance.
**Scenario Z:** The agent passes a malformed email address (missing @ symbol).

Which error configuration is correct?

A) X: Success with empty result (valid query, no match). Y: `isError: true`, `errorCategory: "transient"`, `isRetryable: true`. Z: `isError: true`, `errorCategory: "validation"`, `isRetryable: false`.

B) X: `isError: true`, `errorCategory: "business"`, `isRetryable: false`. Y: `isError: true`, `errorCategory: "transient"`, `isRetryable: true`. Z: `isError: true`, `errorCategory: "validation"`, `isRetryable: true`.

C) All three: `isError: true` with descriptive messages, letting the agent determine retryability from the description text.

D) X: `isError: true`, `errorCategory: "permission"`. Y: `isError: true`, `errorCategory: "transient"`. Z: Success with a warning message in the result.

**Correct Answer: A**

**Explanation:** Scenario X is not an error — it's a valid query that found no match. Returning it as a success with empty results lets the agent ask the customer for alternative identifiers. Scenario Y is a transient infrastructure issue that will resolve, so `isRetryable: true` is correct. Scenario Z is a validation error (malformed input) that won't succeed on retry with the same input, so `isRetryable: false` directs the agent to fix the input. Option B incorrectly marks "no match" as a business error and marks validation errors as retryable. Option C uses unstructured error handling. Option D misclassifies "no match" as a permission issue.

---

### Q2.2.6 — Scenario: Multi-Agent Research System
**Difficulty: Hard | Skill: Error propagation with coverage annotations**

Your synthesis subagent receives findings from three sources: academic papers (complete), news articles (complete), and government reports (failed — the subagent that fetches these encountered a permissions error). How should the synthesis subagent handle this in its output?

A) Complete the synthesis using available findings, and annotate the output with coverage information indicating which topic areas are well-supported by academic and news sources versus which areas may have gaps due to the unavailable government data.

B) Return an error to the coordinator indicating synthesis cannot be completed without all three sources.

C) Synthesize all available findings and present the output as complete, noting in a footnote that government sources were unavailable.

D) Attempt to substitute government findings by having the web search subagent look for government report summaries on news sites.

**Correct Answer: A**

**Explanation:** The synthesis should proceed with available data while being transparent about gaps. Coverage annotations indicate which findings are well-supported versus which areas have gaps due to unavailable sources. This gives the coordinator (or end user) the information needed to assess the report's completeness and decide whether to pursue additional research. Option B unnecessarily blocks the entire workflow. Option C buries the caveat in a footnote rather than structuring it into the report's findings. Option D introduces a workaround that may produce unreliable second-hand summaries.

---

## Task Statement 2.3: Distribute tools appropriately across agents and configure tool choice

### Q2.3.1 — Scenario: Multi-Agent Research System
**Difficulty: Easy | Knowledge: Too many tools degrade selection reliability**

Your research coordinator agent has access to 18 tools: its own coordination tools plus all tools from every subagent (web search, document analysis, synthesis, visualization). During testing, the coordinator sometimes calls document analysis tools directly instead of delegating to the document analysis subagent. What is the most likely cause?

A) Giving an agent access to too many tools (18 instead of 4-5) degrades tool selection reliability by increasing decision complexity — the coordinator should only have tools relevant to its coordination role.

B) The coordinator's system prompt doesn't clearly state that it should delegate rather than act directly.

C) The document analysis tools have better descriptions than the Task tool used for delegation.

D) The coordinator is optimizing for speed by calling tools directly rather than spawning a subagent.

**Correct Answer: A**

**Explanation:** Tool selection reliability degrades when agents have access to too many tools. The coordinator needs only its coordination tools (Task for delegation, possibly a planning tool) — not the specialized tools belonging to subagents. When presented with 18 options, the model's decision-making becomes unreliable and it may directly invoke tools that should be used through delegation. Option B might contribute but isn't the root cause. Option C assumes description quality is the bottleneck when it's actually option overload. Option D attributes intentional optimization that isn't how LLM tool selection works.

---

### Q2.3.2 — Scenario: Multi-Agent Research System
**Difficulty: Medium | Knowledge: Cross-specialization tool misuse**

Your synthesis subagent has access to web search tools "in case it needs to verify facts." During testing, you notice the synthesis agent spends most of its time searching for new information rather than synthesizing the findings it received. What went wrong, and what's the fix?

A) Agents with tools outside their specialization tend to misuse them — the synthesis agent should have a scoped `verify_fact` tool for simple lookups only, while complex searches route through the coordinator to the dedicated search subagent.

B) The synthesis agent's system prompt should emphasize that synthesis is its primary task and searching should be a last resort.

C) The web search tools should be removed entirely from the synthesis agent, requiring all verification to go through the coordinator.

D) The synthesis agent should be given a time limit, with search tools disabled after the first 30 seconds.

**Correct Answer: A**

**Explanation:** The principle of scoped tool access applies here. Rather than giving the synthesis agent full web search capability (which leads to scope creep), provide a constrained `verify_fact` tool designed for the 85% case (simple lookups: dates, names, statistics). Complex verification routes through the coordinator. Option B relies on prompt guidance to constrain behavior, which is probabilistic. Option C eliminates all verification capability, which forces round-trips through the coordinator even for trivial checks. Option D introduces an arbitrary time-based constraint that doesn't address the underlying tool design issue.

---

### Q2.3.3 — Scenario: Structured Data Extraction
**Difficulty: Medium | Knowledge: tool_choice configuration options**

You're building an extraction pipeline that processes documents of unknown type. You have three extraction tools: `extract_invoice`, `extract_receipt`, and `extract_contract`. You need the model to always call one of these tools (never return conversational text), but you want it to choose the appropriate one based on the document content. Which `tool_choice` configuration achieves this?

A) `tool_choice: "any"` — this guarantees the model calls a tool (rather than returning text) while allowing it to select the most appropriate extraction tool based on the document.

B) `tool_choice: "auto"` — this lets the model decide whether and which tool to call based on the document.

C) `tool_choice: {"type": "tool", "name": "extract_invoice"}` — force the most common tool and handle mismatches in post-processing.

D) Don't set `tool_choice` and instead add "You must always call one of the extraction tools" to the system prompt.

**Correct Answer: A**

**Explanation:** `tool_choice: "any"` guarantees the model calls a tool but allows it to choose which one — exactly the behavior needed when the document type is unknown but structured output is required. `"auto"` (Option B) allows the model to return text instead of calling a tool, which defeats the guarantee. Forced selection (Option C) ignores document type. Prompt-based enforcement (Option D) is probabilistic and may fail.

---

### Q2.3.4 — Scenario: Structured Data Extraction
**Difficulty: Hard | Skill: Forced tool selection for sequencing**

Your extraction pipeline has two phases: first extract metadata (title, date, author), then enrich the metadata with additional context. You have `extract_metadata` and `enrich_context` tools. During testing, the model sometimes skips metadata extraction and jumps directly to enrichment, producing incomplete results. How should you enforce the correct sequence using `tool_choice`?

A) In the first API call, use `tool_choice: {"type": "tool", "name": "extract_metadata"}` to force metadata extraction. Then, in a follow-up API call with the extraction results in context, either use `tool_choice: "auto"` or `tool_choice: {"type": "tool", "name": "enrich_context"}` for the enrichment step.

B) Set `tool_choice: "any"` and rely on the tool descriptions to guide the model to call `extract_metadata` first.

C) Remove `enrich_context` from the available tools in the first call, add it back in the second call after metadata is extracted.

D) Use a single API call with `tool_choice: "auto"` and instructions in the system prompt to always extract metadata before enriching.

**Correct Answer: A**

**Explanation:** Forced tool selection (`tool_choice: {"type": "tool", "name": "..."}`) ensures a specific tool is called first. By structuring this across two API calls — forcing metadata extraction in the first, then processing enrichment in the second — you guarantee the correct sequence programmatically. Option B doesn't enforce ordering. Option C achieves ordering through tool availability manipulation, which is more complex and less explicit. Option D relies on prompt instructions, which are probabilistic.

---

### Q2.3.5 — Scenario: Developer Productivity with Claude
**Difficulty: Medium | Skill: Replacing generic tools with constrained alternatives**

Your document analysis subagent has access to a general `fetch_url` tool. During testing, it sometimes fetches non-document URLs (social media feeds, arbitrary websites) that pollute its context with irrelevant content. What is the best fix?

A) Replace `fetch_url` with a constrained `load_document` tool that validates URLs against an allowlist of document repositories and file extensions, rejecting non-document URLs.

B) Add a system prompt instruction: "Only use fetch_url for document URLs. Do not fetch social media or other non-document content."

C) Implement a PostToolUse hook that filters out non-document content from `fetch_url` results before the agent processes them.

D) Remove `fetch_url` entirely and require all documents to be pre-loaded into the agent's context by the coordinator.

**Correct Answer: A**

**Explanation:** Replacing a generic tool with a constrained alternative that validates inputs against defined boundaries prevents misuse at the tool level. The `load_document` tool's description and validation logic make it clear what it accepts, and it rejects inappropriate requests. Option B is prompt-based enforcement with no guarantee. Option C lets the fetch happen (consuming time and resources) before filtering. Option D removes a useful capability and shifts burden to the coordinator unnecessarily.

---

### Q2.3.6 — Scenario: Customer Support Resolution Agent
**Difficulty: Easy | Knowledge: tool_choice "auto" vs "any"**

Your support agent sometimes responds to customer questions with conversational text ("I'd be happy to help!") instead of calling `get_customer` to look up their account. You want to ensure the agent always calls a tool on the first turn rather than generating pleasantries. Which configuration change is most appropriate for the first API call?

A) Set `tool_choice: "any"` to guarantee the model calls a tool rather than returning conversational text.

B) Set `tool_choice: "auto"` and add "Always call a tool first" to the system prompt.

C) Set `tool_choice: {"type": "tool", "name": "get_customer"}` to force customer lookup on every first turn.

D) Remove all non-tool response options from the model configuration.

**Correct Answer: A**

**Explanation:** `tool_choice: "any"` guarantees a tool call while letting the model choose the most appropriate tool based on the customer's message. This prevents conversational-only responses while preserving the model's judgment about which tool fits. Option B still allows `"auto"` to return text. Option C is too rigid — not every first message requires a customer lookup (someone might ask about store hours). Option D describes a configuration that doesn't exist.

---

## Task Statement 2.4: Integrate MCP servers into Claude Code and agent workflows

### Q2.4.1 — Scenario: Code Generation with Claude Code
**Difficulty: Easy | Knowledge: MCP server scoping**

Your team has a shared Jira MCP server that all developers should use. One developer also has a personal experimental MCP server for testing a new code analysis tool. Where should each be configured?

A) The shared Jira server in project-level `.mcp.json` (committed to version control), and the personal experimental server in user-level `~/.claude.json` (not committed).

B) Both in `.mcp.json` so they're available to the agent simultaneously.

C) The Jira server in `~/.claude.json` and the experimental server in `.mcp.json`.

D) Both in `CLAUDE.md` as tool configuration directives.

**Correct Answer: A**

**Explanation:** MCP server scoping distinguishes shared team tools from personal tools. Project-level `.mcp.json` is version-controlled and available to all team members who clone the repo — appropriate for shared infrastructure like Jira. User-level `~/.claude.json` is local to the individual — appropriate for personal experiments that shouldn't affect teammates. Option B puts the experimental server in shared config, exposing it to all developers. Option C reverses the scoping. Option D uses the wrong configuration mechanism — `CLAUDE.md` is for instructions and context, not server configuration.

---

### Q2.4.2 — Scenario: Code Generation with Claude Code
**Difficulty: Medium | Knowledge: Environment variable expansion**

Your team's `.mcp.json` configures a GitHub MCP server that requires an authentication token. A new developer clones the repo and finds the MCP server isn't working. The configuration file contains:

```json
{
  "mcpServers": {
    "github": {
      "command": "github-mcp",
      "env": {
        "GITHUB_TOKEN": "${GITHUB_TOKEN}"
      }
    }
  }
}
```

What is the most likely cause?

A) The new developer hasn't set the `GITHUB_TOKEN` environment variable on their machine — the `${GITHUB_TOKEN}` syntax in `.mcp.json` uses environment variable expansion, so each developer must have the variable configured locally.

B) The `${GITHUB_TOKEN}` syntax is invalid in `.mcp.json` — the actual token value should be hardcoded.

C) The `.mcp.json` file shouldn't be committed to version control because it contains authentication configuration.

D) The MCP server requires the token to be in `~/.claude.json` instead of `.mcp.json`.

**Correct Answer: A**

**Explanation:** Environment variable expansion in `.mcp.json` (using `${VAR_NAME}` syntax) allows credential management without committing secrets to version control. Each developer must set the required environment variable on their machine. Option B recommends hardcoding tokens, which is a security anti-pattern. Option C is incorrect — `.mcp.json` should be committed; it's the environment variables containing actual secrets that shouldn't be. Option D confuses server configuration scoping with credential management.

---

### Q2.4.3 — Scenario: Developer Productivity with Claude
**Difficulty: Medium | Knowledge: MCP resources vs tools**

Your team's Jira MCP server provides only action-based tools (`create_issue`, `update_issue`, `search_issues`). When developers ask Claude to help with sprint planning, it makes dozens of `search_issues` calls to understand what's in the current sprint, consuming tokens and time. What MCP design pattern would reduce these exploratory calls?

A) Expose a content catalog as an MCP resource — for example, a "current sprint summary" resource that provides an overview of all issues, statuses, and assignees, giving the agent visibility into available data without requiring exploratory tool calls.

B) Add a `get_all_sprint_issues` tool that returns every issue in a single call.

C) Cache the results of `search_issues` so repeated calls return instantly.

D) Add a system prompt instruction telling the agent to limit itself to 3 Jira searches per task.

**Correct Answer: A**

**Explanation:** MCP resources expose content catalogs (issue summaries, documentation hierarchies, database schemas) that give agents visibility into available data without requiring exploratory tool calls. A sprint summary resource provides the overview the agent needs to plan its approach, reducing the need for multiple search calls. Option B returns potentially large amounts of data in a tool call when a resource is more appropriate for browsable reference data. Option C reduces latency but not the number of tool calls or token usage. Option D arbitrarily limits the agent's ability to gather information.

---

### Q2.4.4 — Scenario: Developer Productivity with Claude
**Difficulty: Medium | Skill: Choosing community vs custom MCP servers**

Your team needs Jira integration for their Claude Code workflow. A developer starts building a custom MCP server that wraps the Jira REST API. Another developer points out that community Jira MCP servers already exist. What guidance applies?

A) Choose existing community MCP servers over custom implementations for standard integrations like Jira — reserve custom server development for team-specific workflows that aren't covered by existing solutions.

B) Always build custom MCP servers to ensure full control over the API surface and security model.

C) Use the community server as a starting point but fork it immediately to customize for your team's needs.

D) Community servers are acceptable for development but should be replaced with custom implementations for production.

**Correct Answer: A**

**Explanation:** For standard integrations (Jira, GitHub, Slack), community MCP servers are battle-tested and maintained by the community. Building custom servers for standard functionality duplicates effort without adding value. Custom servers are appropriate when your team has unique workflow requirements not covered by existing solutions. Option B wastes development resources on solved problems. Option C adds maintenance burden for a fork that may diverge from upstream improvements. Option D assumes community servers aren't production-quality, which isn't inherently true.

---

### Q2.4.5 — Scenario: Code Generation with Claude Code
**Difficulty: Hard | Knowledge: Multi-server simultaneous access**

Your project's `.mcp.json` configures a GitHub server and a Jira server. Your personal `~/.claude.json` configures an experimental code analysis server. During a session, you ask Claude to "find the Jira ticket for the authentication bug, check the linked GitHub PR, and run a code quality analysis on the PR's changes." What should you expect regarding tool availability?

A) Tools from all three configured MCP servers (GitHub, Jira, and code analysis) are discovered at connection time and available simultaneously — Claude can use tools from any server in any order within the same session.

B) Only project-level servers are available. Personal servers must be explicitly activated during the session.

C) Claude will use tools from one server at a time, requiring you to specify which server to connect to for each part of the request.

D) Project-level and user-level servers cannot be used in the same session due to scope isolation.

**Correct Answer: A**

**Explanation:** Tools from all configured MCP servers (both project-level and user-level) are discovered at connection time and available simultaneously. Claude can seamlessly use tools from different servers within the same session without explicit switching. Option B incorrectly suggests personal servers need activation. Option C describes a sequential model that doesn't match how MCP works. Option D claims scope isolation prevents simultaneous use, which is incorrect — scoping determines who sees the configuration, not runtime availability.

---

## Task Statement 2.5: Select and apply built-in tools (Read, Write, Edit, Bash, Grep, Glob) effectively

### Q2.5.1 — Scenario: Developer Productivity with Claude
**Difficulty: Easy | Knowledge: Grep vs Glob distinction**

You need to find all TypeScript test files in a large codebase. Which built-in tool is most appropriate?

A) Glob, because it finds files matching naming patterns (e.g., `**/*.test.tsx`) — it's for file path pattern matching, not content searching.

B) Grep, because it can search for files containing the word "test" in their names.

C) Bash with `find . -name "*.test.tsx"` for maximum flexibility.

D) Read on the root directory to list all files, then filter programmatically.

**Correct Answer: A**

**Explanation:** Glob is specifically designed for file path pattern matching — finding files by name or extension pattern. `**/*.test.tsx` matches all TypeScript test files regardless of directory depth. Grep (Option B) searches file *contents* for patterns, not file names. While Bash `find` (Option C) would work, Glob is the purpose-built tool for this exact use case. Option D is extremely inefficient for file discovery.

---

### Q2.5.2 — Scenario: Developer Productivity with Claude
**Difficulty: Easy | Knowledge: Grep for content search**

A developer asks: "Find all files where the `processRefund` function is called." Which built-in tool should the agent use?

A) Grep, because it searches file contents for patterns — in this case, searching across the codebase for occurrences of "processRefund" in source code.

B) Glob, using a pattern like `**/*processRefund*` to find files named after the function.

C) Read on every file in the codebase and search for the function name in the output.

D) Bash with `cat * | grep processRefund` to search all files simultaneously.

**Correct Answer: A**

**Explanation:** Grep is the correct tool for searching file contents across a codebase. It efficiently finds all files containing the string "processRefund", showing where the function is called. Glob (Option B) matches file names, not file contents. Read (Option C) would require loading every file individually, which is extremely inefficient. Option D uses Bash for a task the built-in Grep tool handles directly.

---

### Q2.5.3 — Scenario: Developer Productivity with Claude
**Difficulty: Medium | Skill: Edit vs Read+Write fallback**

Your agent needs to modify a specific function in a file. It attempts to use the Edit tool to replace the function body, but Edit fails because the text anchor isn't unique — it appears in three places in the file. You've already tried expanding the text anchor with more surrounding context, but the file has a highly repetitive structure (auto-generated code with similar function signatures) making unique anchors impractical. What is the correct fallback approach?

A) Use Read to load the full file contents, make the modification programmatically (identifying the correct location by surrounding context), then use Write to save the complete modified file.

B) Retry Edit with a longer text anchor that includes more surrounding context until it's unique.

C) Use Bash with `sed` to perform the replacement using line numbers.

D) Split the file into sections, Edit each section independently, then concatenate them back.

**Correct Answer: A**

**Explanation:** When Edit fails due to non-unique text matches and expanding the anchor is impractical (as in auto-generated code with repetitive structure), the documented fallback is Read + Write. Read loads the full file, allowing the agent to identify the exact location using complete programmatic context (e.g., line numbers, surrounding logic), then Write saves the modified version. Option B has already been tried — the stem specifies that unique anchors are impractical due to the repetitive structure. Option C uses external tooling when built-in tools suffice and is fragile if line numbers shift. Option D is unnecessarily complex and error-prone.

---

### Q2.5.4 — Scenario: Developer Productivity with Claude
**Difficulty: Hard | Skill: Incremental codebase understanding**

A developer asks the agent to understand how the authentication flow works in an unfamiliar codebase with 500+ files. Which approach most effectively builds understanding incrementally?

A) Start with Grep to find entry points (e.g., searching for "login", "authenticate", "auth middleware"), then use Read to follow imports and trace the flow from entry points through the call chain, building understanding progressively.

B) Use Glob to find all files in the `auth/` directory, then Read all of them to build a complete picture before answering.

C) Use Bash to run a static analysis tool that generates a call graph for the entire codebase.

D) Use Read on the project's README and main entry point file, then answer based on high-level documentation.

**Correct Answer: A**

**Explanation:** Incremental understanding starts broad (Grep to find relevant entry points) then goes deep (Read to trace specific flows). This is efficient because it only loads files relevant to the authentication flow rather than reading everything upfront. Option B reads an entire directory, which may include irrelevant files and miss auth logic in other directories. Option C uses external tooling when built-in tools are sufficient and available. Option D relies on documentation that may be outdated or incomplete.

---

### Q2.5.5 — Scenario: Developer Productivity with Claude
**Difficulty: Medium | Skill: Tracing function usage across wrappers**

You ask the agent to find all usages of a utility function `formatDate` that's exported from `utils/date.ts`. The agent uses Grep to search for "formatDate" and finds 15 direct usages. However, you know `formatDate` is also re-exported through `utils/index.ts` as part of a barrel export, and some files import it from there. How should the agent proceed to find all usages?

A) First identify all exported names by reading `utils/index.ts` to see how `formatDate` is re-exported, then search for each import path pattern (both `from 'utils/date'` and `from 'utils'` or `from 'utils/index'`) across the codebase.

B) Search for "formatDate" in all files — if it's imported from any path, the function name still appears in the import statement.

C) Read `utils/index.ts` and if `formatDate` is re-exported, replace the barrel export with direct exports to simplify future searches.

D) Use Bash to run a TypeScript compiler analysis that resolves all import chains automatically.

**Correct Answer: A**

**Explanation:** When functions are re-exported through barrel files (index.ts), they can be imported via different paths. The agent must trace the export chain — read the barrel file to understand re-export patterns, then search for each possible import path. Option B sounds reasonable but only works if the function name appears literally in every import — destructured re-exports or renamed exports would be missed. Option C modifies production code to aid search, which is inappropriate. Option D uses external tooling unnecessarily.

---

### Q2.5.6 — Scenario: Developer Productivity with Claude
**Difficulty: Medium | Skill: Selecting the right tool combination**

A developer asks: "Update all test files to use the new `renderWithProviders` helper instead of the old `render` call from `@testing-library/react`." Which sequence of built-in tools is most appropriate?

A) Glob to find all test files (`**/*.test.tsx`), then Grep within those files to find ones using `render` from `@testing-library/react`, then Edit each matching file to replace the import and function call — falling back to Read+Write if Edit fails on any file.

B) Grep for `render` across the entire codebase, then Edit every file that contains it.

C) Bash with a `sed` command to find-and-replace across all `.test.tsx` files.

D) Read every test file, check for the old pattern, and Write the updated version back.

**Correct Answer: A**

**Explanation:** This combines tools by their strengths: Glob narrows to test files by pattern, Grep identifies which test files use the specific import, and Edit makes targeted modifications with Read+Write as a fallback. Option B searches too broadly (non-test files may also import `render`) and doesn't verify the import source. Option C risks unintended replacements without checking context. Option D is inefficient — reading every test file when Grep can quickly identify the relevant subset.

---

## Task Statement 2.6: Handle MCP elicitation requests for mid-task user input

### Q2.6.1 — Scenario: Developer Productivity with Claude
**Difficulty: Easy | Knowledge: MCP elicitation modes (form fields vs browser URL)**

Your team uses an MCP server that integrates with a third-party CI/CD platform requiring OAuth authentication. During a deployment task, the MCP server needs the developer to authorize access. Which MCP elicitation mode is appropriate and why?

A) Browser URL mode — the server opens the OAuth provider's authorization URL in the developer's browser, allowing them to complete the login and consent flow, then confirm back in the CLI.

B) Form field mode — the server displays username and password fields in the terminal so the developer can type their CI/CD credentials directly.

C) No elicitation needed — the MCP server should store OAuth tokens permanently in `.mcp.json` so authorization happens once and never again.

D) The MCP server should fail gracefully with an error message telling the developer to authenticate manually before retrying.

**Correct Answer: A**

**Explanation:** OAuth flows require browser-based authorization where the identity provider handles login, consent, and token exchange. Browser URL elicitation is designed for exactly this — the server requests that Claude Code open a URL, the user completes the flow in their browser, and confirms. Option B exposes credentials in plaintext in the terminal, which is insecure and not how OAuth works. Option C stores tokens in version-controlled config, which is a security risk — credentials belong in environment variables or secure storage. Option D provides a poor experience when elicitation can handle it interactively.

---

### Q2.6.2 — Scenario: Customer Support Resolution Agent
**Difficulty: Medium | Knowledge: When MCP servers should use elicitation vs alternatives**

Your customer support agent uses an MCP server to access the ticketing system. The server needs an API key to authenticate. Currently, developers must manually set the `TICKET_API_KEY` environment variable before starting Claude Code. A teammate proposes using MCP elicitation to prompt for the API key via a form field dialog when it's missing. What is the best approach?

A) Use environment variable expansion in `.mcp.json` (e.g., `${TICKET_API_KEY}`) for the API key, and only use elicitation as a fallback when the variable is unset — elicitation should handle dynamic or session-specific input, not static credentials that can be configured once.

B) Always use form field elicitation to prompt for the API key at the start of every session — this ensures the key is never stored on disk and maximizes security.

C) Store the API key directly in `.mcp.json` since it's a project-level configuration that all team members need.

D) Use browser URL elicitation to redirect the developer to the ticketing system's settings page where they can copy their API key.

**Correct Answer: A**

**Explanation:** Environment variable expansion is the standard mechanism for credential management in MCP configurations — it avoids committing secrets and works across all team members. Elicitation is best suited for dynamic input needed mid-task (authentication flows, user approvals, conditional configuration), not static credentials that can be set once. Option B creates unnecessary friction every session. Option C commits secrets to version control. Option D adds unnecessary steps for a value the developer already has.

---

### Q2.6.3 — Scenario: Developer Productivity with Claude
**Difficulty: Medium | Knowledge: Elicitation in CI/CD and headless environments**

Your team runs Claude Code in headless mode (`-p` flag) as part of a CI/CD pipeline. An MCP server used in this pipeline has elicitation requests for confirming destructive database migrations. In headless mode, there is no interactive terminal. How should you handle this?

A) Configure an Elicitation hook to auto-respond to the MCP server's elicitation requests in CI/CD — the hook provides pre-configured responses without requiring interactive input, enabling unattended operation.

B) Switch the CI/CD pipeline from headless mode to interactive mode so elicitation dialogs can be displayed.

C) Remove the elicitation capability from the MCP server since it doesn't work in headless mode.

D) Add a `--skip-elicitation` flag to the Claude Code command to suppress all elicitation requests.

**Correct Answer: A**

**Explanation:** Elicitation hooks are the mechanism for handling elicitation requests in non-interactive environments. They allow you to define pre-configured responses that automatically satisfy the server's input requests without human interaction — essential for CI/CD pipelines. Option B defeats the purpose of headless mode and is impractical in CI. Option C removes a useful safety feature from all environments. Option D is not a real flag, and suppressing confirmations for destructive operations is dangerous.

---

### Q2.6.4 — Scenario: Multi-Agent Research System
**Difficulty: Medium | Knowledge: Elicitation vs tool parameters for user input**

Your multi-agent research system has a coordinator that dispatches search subagents to different MCP-backed data sources. One data source requires the user to select which database partition to search (partitions change monthly). A developer proposes adding MCP elicitation to the data source server so it can ask the user which partition to query mid-task. What is the problem with this approach?

A) Elicitation requests surface to the end user, interrupting the autonomous flow of the multi-agent system — the coordinator should collect the partition preference upfront via its own prompt and pass it as a tool parameter to the subagent, keeping the workflow non-interactive.

B) There is no problem — elicitation is the correct mechanism for collecting user preferences at any point in a workflow.

C) The problem is that subagents cannot trigger elicitation — only the coordinator agent can display dialogs.

D) Elicitation only supports form fields and cannot present a selection list of partition names.

**Correct Answer: A**

**Explanation:** In multi-agent systems, mid-task user interruptions break the autonomous workflow that makes them valuable. If the coordinator already interacts with the user, it should collect the partition preference during the initial task setup and pass it as a parameter to the subagent. Elicitation is appropriate when the server encounters an unexpected need for input (e.g., authentication challenge), not for predictable configuration that can be gathered upfront. Option B ignores workflow design principles. Option C is technically incorrect — elicitation is a server-side request mechanism. Option D is incorrect — elicitation supports structured form fields including selections.

---

### Q2.6.5 — Scenario: Code Generation with Claude Code
**Difficulty: Hard | Knowledge: Security considerations for MCP elicitation**

A new MCP server your team installed for database management displays an elicitation dialog asking for the production database password via a form field. A developer enters it without questioning. What security concern does this raise, and what is the correct mitigation?

A) MCP elicitation dialogs come from MCP servers, not from Claude itself — a malicious or poorly designed server could use elicitation to phish for credentials. The mitigation is to verify what the server is requesting and why, prefer OAuth/token-based flows (browser URL mode) over raw credential entry, and audit MCP servers before installation.

B) There is no concern — elicitation dialogs are sandboxed and encrypted, so credentials entered in form fields cannot be accessed by the MCP server.

C) The concern is that production credentials should never be used in development. The fix is to use a separate development database.

D) The concern is that form field values are logged in Claude Code's conversation history. The fix is to use `--no-log` mode when entering credentials.

**Correct Answer: A**

**Explanation:** MCP servers are third-party code — elicitation requests originate from the server, not from Claude's trusted infrastructure. A server requesting raw passwords via form fields could be exfiltrating credentials. The correct approach for sensitive authentication is browser URL elicitation with proper OAuth flows, where the identity provider (not the MCP server) handles credentials. Teams should audit MCP servers and prefer token-based auth. Option B is incorrect — form field values are sent directly to the MCP server. Option C identifies a secondary concern but misses the primary phishing risk. Option D cites a non-existent flag and misidentifies the risk.

---

### Q2.6.6 — Scenario: Customer Support Resolution Agent
**Difficulty: Hard | Knowledge: Designing MCP servers with appropriate elicitation**

You're building a custom MCP server for your support team's internal tools. The server needs to handle three cases: (1) initial setup requiring an API key, (2) OAuth re-authentication when tokens expire mid-session, and (3) confirmation before escalating a ticket to a manager. How should you design the elicitation strategy?

A) Use environment variable expansion for the API key (no elicitation needed — it's static config). Use browser URL elicitation for OAuth re-authentication (tokens expire unpredictably, and the OAuth flow requires browser interaction). Use form field elicitation for escalation confirmation (a simple yes/no approval that the user can answer directly in the terminal).

B) Use form field elicitation for all three cases — display the API key input, OAuth credentials, and escalation confirmation as form dialogs for consistency.

C) Use browser URL elicitation for all three cases — redirect to a web UI for API key entry, OAuth flow, and escalation approval.

D) Use elicitation for the OAuth flow only. Store the API key in `.mcp.json` and skip escalation confirmation since the agent should handle that autonomously.

**Correct Answer: A**

**Explanation:** Each case has different characteristics requiring different mechanisms. Static credentials belong in environment variables, not elicitation — they're set once and reused. OAuth re-authentication is inherently browser-based (the identity provider must handle the flow). Escalation confirmation is a simple approval best handled inline via form fields. Option B puts static credentials in elicitation (unnecessary friction) and handles OAuth insecurely. Option C over-uses browser redirects for simple operations. Option D commits the API key to version-controlled config (security risk) and removes a human approval gate for escalation.

---

## Cross-Domain Questions (Domain 2 intersecting with other domains)

### Q2.X.1 — Scenario: Customer Support Resolution Agent
**Difficulty: Hard | Domains: 2 (Tool Design) + 1 (Architecture) + 5 (Reliability)**

Your customer support system has evolved to include 12 MCP tools. Monitoring reveals three problems: (1) the agent misroutes `check_warranty` and `check_return_eligibility` 25% of the time because both descriptions mention "check if the item qualifies"; (2) the `get_shipping_status` tool returns different date formats depending on the carrier; (3) when `check_inventory` times out, the agent tells customers the item is out of stock. Rank the fixes by impact and explain the correct approach.

A) First, rewrite tool descriptions for `check_warranty` and `check_return_eligibility` to clearly differentiate their purpose, inputs, and boundaries (fixing 25% misrouting). Second, add a PostToolUse hook to normalize date formats from `get_shipping_status`. Third, implement structured error responses for `check_inventory` distinguishing timeouts from empty results. Each fix targets a specific layer: description quality, data normalization, and error handling.

B) Consolidate all 12 tools into 5 broader tools to reduce selection complexity, add comprehensive system prompt instructions for all edge cases, and implement automatic retry for all tool failures.

C) Implement a routing classifier that pre-selects tools before each turn, standardize all tool output formats at the MCP server level, and add global error handling that catches all failures and returns user-friendly messages.

D) Add few-shot examples covering all tool selection scenarios, post-process all tool results through a normalization layer, and implement a generic retry mechanism with exponential backoff for all tools.

**Correct Answer: A**

**Explanation:** Each problem has a specific root cause requiring a targeted fix. Tool misrouting stems from ambiguous descriptions (fix with description rewriting). Date format inconsistency needs deterministic normalization (fix with PostToolUse hooks). Error misinterpretation needs structured error responses (fix with isError metadata). Option B over-consolidates tools and relies on prompt instructions. Option C introduces unnecessary infrastructure. Option D uses few-shot examples for a problem better solved by descriptions and doesn't address the specific error types.

---

### Q2.X.2 — Scenario: Developer Productivity with Claude
**Difficulty: Hard | Domains: 2 (Tool Design) + 3 (Claude Code Configuration)**

Your team configures a shared `database-schema` MCP server in `.mcp.json` that exposes tools for querying schema information. Despite proper configuration, developers report that Claude rarely uses the MCP tools, instead using Bash to run SQL queries directly or Grep to search for schema definitions in migration files. Investigation shows the MCP tool descriptions read: "db_tables: Gets tables" and "db_columns: Gets columns." What multi-layered fix addresses this?

A) First, enhance MCP tool descriptions to explain capabilities in detail (e.g., "db_tables: Returns all database tables with their row counts, relationships, and descriptions. Use this instead of SQL queries or migration file searches for understanding database structure"). Second, expose a "database overview" MCP resource providing the full schema summary so the agent doesn't need to make exploratory calls. Third, review the project CLAUDE.md for any instructions that might bias toward Bash or Grep for database tasks.

B) Add `tool_choice: {"type": "tool", "name": "db_tables"}` to force the MCP tool whenever database questions arise.

C) Remove Bash and Grep from the agent's available tools so it has no alternative for database exploration.

D) Add a CLAUDE.md instruction: "Always use the database-schema MCP server for schema questions. Never use Bash for SQL queries."

**Correct Answer: A**

**Explanation:** This addresses three interconnected issues. Minimal descriptions ("Gets tables") don't convey enough value for the agent to prefer MCP tools over familiar built-in alternatives. Enhanced descriptions explain what the tools offer beyond raw SQL. An MCP resource provides schema overview without exploratory calls. Reviewing CLAUDE.md ensures no conflicting instructions bias tool selection. Option B forces a tool regardless of context. Option C removes general-purpose tools needed for non-database tasks. Option D relies on prompt-based enforcement without fixing the underlying description quality.

---

*End of Domain 2 Question Bank*
*Total: 37 questions covering all 6 task statements + 2 cross-domain questions*
*Distribution: 8 Easy, 18 Medium, 11 Hard*