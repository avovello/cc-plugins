---
name: domain-2-tool-design-mcp
description: Training curriculum for Domain 2: Tool Design & MCP Integration (18% of exam). Use when /training targets this domain.
---

# Domain 2: Tool Design & MCP Integration (18%)

## Overview
- Weight: 18% of scored content
- Task statements: 2.1–2.6
- Primary scenarios: Customer Support Resolution Agent, Multi-Agent Research System, Developer Productivity with Claude
- Cross-domain connections: Agentic Architecture (tool distribution across agents, tool_choice in orchestration), Claude Code Configuration (MCP server integration in .mcp.json), Context Management (error propagation from tools)

## Task Statement 2.1: Design effective tool interfaces with clear descriptions and boundaries

### Key Concepts

Tool descriptions are the primary mechanism LLMs use for tool selection. When Claude receives a set of tools, it reads their descriptions to decide which one to call. Minimal descriptions like "Retrieves customer information" versus "Retrieves order details" provide insufficient differentiation — the model cannot reliably distinguish when to use one versus the other, especially when both accept similar identifier formats.

Effective tool descriptions include: the tool's specific purpose, what input formats it accepts, example queries it handles, edge cases, and explicit boundaries explaining when to use it versus similar alternatives. For example, instead of "Retrieves order details," a better description is "Looks up order status, shipping info, and line items by order number (format: #NNNNN). Use this for order-specific queries. For customer account info (email, name, billing), use get_customer instead."

Ambiguous or overlapping tool descriptions cause misrouting. If two tools have near-identical descriptions (e.g., `analyze_content` and `analyze_document`), the model will inconsistently choose between them. The fix is either renaming tools to clarify their purpose (e.g., `extract_web_results` with a web-specific description) or splitting a generic tool into purpose-specific tools with defined input/output contracts.

System prompt wording also affects tool selection. Keyword-sensitive instructions can create unintended tool associations — for example, if the system prompt frequently mentions "customer analysis," the model may associate the word "analysis" with a tool called `analyze_content` even when `extract_web_results` is more appropriate.

### Common Misconceptions

- **Positional bias in tool ordering**: A frequent distractor claims that the order tools are listed in causes selection bias. While minor positional effects exist, tool descriptions are the dominant factor in selection reliability.
- **System prompt routing rules fix selection**: Adding routing rules to the system prompt ("for order queries, use lookup_order") is a partial fix but less robust than improving tool descriptions directly. Descriptions are the primary mechanism.
- **Consolidating tools solves ambiguity**: Merging two ambiguous tools into one generic tool trades one problem for another. The generic tool now must handle multiple concerns, reducing clarity. Splitting into well-described specific tools is usually better.
- **The model is "correctly" doing multi-step reasoning**: When the model calls get_customer before lookup_order for an order query, some distractors frame this as intentional identity verification. Unless the system explicitly requires verification, misrouting is the more likely explanation.

### Practice Focus

Questions present a scenario where the agent calls the wrong tool and ask for the root cause or fix. The root cause is almost always insufficient tool descriptions. The fix is improving descriptions with input formats, examples, and boundary explanations. Watch for answers that blame system prompt ordering or propose adding routing logic — the primary fix is always better descriptions.

## Task Statement 2.2: Implement structured error responses for MCP tools

### Key Concepts

The MCP `isError` flag communicates tool failures back to the agent. But the flag alone is insufficient — the agent needs structured error metadata to make appropriate recovery decisions. This includes `errorCategory` (transient, validation, permission, business), `isRetryable` boolean, and a human-readable description.

The critical distinction is between error types: transient errors (timeouts, service unavailability) are retryable; validation errors (invalid input format) require input correction; business errors (policy violations like "refund exceeds limit") are non-retryable and need alternative workflows; permission errors need escalation. Returning a generic "Operation failed" message prevents the agent from distinguishing between these — it cannot know whether to retry, fix the input, or escalate.

For business rule violations, the error response should include `retryable: false` and a customer-friendly explanation so the agent can communicate appropriately rather than repeatedly attempting the same blocked action.

Error recovery follows a local-first principle: subagents should handle transient failures locally (retry with backoff), propagating to the coordinator only errors they cannot resolve. When propagating, they should include what was attempted and any partial results, enabling the coordinator to make informed decisions.

A subtle but important distinction: access failures (the database was unreachable) versus valid empty results (the query succeeded but found no matching records). These require different agent responses — retry/escalate for access failures, inform the user for empty results. If both return the same generic error, the agent cannot distinguish them.

### Common Misconceptions

- **Generic error messages are sufficient**: "Operation failed" tells the agent nothing about what to do next. Structured metadata with error category and retryability is essential.
- **All errors should be retried**: Retrying a business rule violation or a permission error wastes tokens and creates a frustrating loop. Only transient errors should be retried.
- **Errors should terminate the entire workflow**: A single subagent failure should not kill the whole multi-agent pipeline. The coordinator should work with partial results and note coverage gaps.
- **Empty results are errors**: A query that returns zero results is a successful query, not an error. The agent should inform the user that nothing was found, not retry the query.

### Practice Focus

Questions present an error scenario and ask how the tool should respond or how the agent should handle the error. Correct answers always involve structured error metadata with category classification and retryability. Watch for questions that ask about the difference between transient and business errors — the retryability distinction is the key.

## Task Statement 2.3: Distribute tools appropriately across agents and configure tool choice

### Key Concepts

Giving an agent access to too many tools degrades tool selection reliability. Research and practice show that agents with 4-5 focused tools significantly outperform agents with 18+ tools. Each additional tool increases decision complexity and the probability of misrouting. The principle is least privilege: each agent gets only the tools needed for its specific role.

Agents with tools outside their specialization tend to misuse them. A synthesis agent with access to web search tools may attempt its own searches instead of relying on findings from the search subagent. This wastes tokens and produces inconsistent results. Restrict each subagent's tool set to its role: search agents get search tools, analysis agents get analysis tools, synthesis agents get synthesis tools.

For high-frequency cross-role needs, provide scoped alternatives rather than full access. For example, instead of giving the synthesis agent a full web search tool, provide a lightweight `verify_fact` tool that checks specific claims against cached search results. Complex cases still route through the coordinator.

`tool_choice` configuration controls how Claude selects tools: `"auto"` lets Claude choose whether to call a tool or respond with text; `"any"` forces Claude to call a tool (guaranteeing structured output); forced selection (`{"type": "tool", "name": "extract_metadata"}`) ensures a specific tool is called first. Use forced selection when a specific tool must run before others (e.g., metadata extraction before enrichment), then switch to `"auto"` for subsequent turns.

### Common Misconceptions

- **More tools means more capable**: The opposite is true for reliability. More tools increase decision complexity and error rates.
- **tool_choice "auto" guarantees tool use**: "auto" allows the model to respond with text instead of calling a tool. Use "any" to guarantee a tool call, or forced selection for a specific tool.
- **Forced tool selection works for multi-step sequences**: You can only force one tool per turn. Multi-step forced sequences require multiple turns with different tool_choice settings.
- **All agents should have the same tools**: Each agent's tool set should match its role. A synthesis agent does not need search tools; a search agent does not need write tools.

### Practice Focus

Questions test your understanding of tool distribution (which agent gets which tools) and tool_choice configuration (auto vs. any vs. forced). Watch for scenarios where an agent misuses a tool outside its role — the fix is restricting that tool from the agent, not adding prompt instructions. For tool_choice questions, remember: "auto" = optional, "any" = must call some tool, forced = must call this specific tool.

## Task Statement 2.4: Integrate MCP servers into Claude Code and agent workflows

### Key Concepts

MCP servers have two scoping levels: project-level (`.mcp.json` in the project root) for shared team tooling, and user-level (`~/.claude.json`) for personal or experimental servers. Project-level configuration is committed to version control and available to all team members; user-level configuration is local to the individual developer.

Environment variable expansion in `.mcp.json` (e.g., `${GITHUB_TOKEN}`) enables credential management without committing secrets. Each developer sets the variable in their local environment, and the MCP configuration references it by name.

Tools from all configured MCP servers are discovered at connection time and available simultaneously. This means naming conflicts between servers can cause confusion — if two MCP servers expose tools with similar names or descriptions, tool selection reliability degrades (connecting back to Task Statement 2.1).

MCP resources are a distinct mechanism from tools. Resources expose content catalogs — issue summaries, documentation hierarchies, database schemas — that give agents visibility into available data without requiring exploratory tool calls. This is more token-efficient than having the agent call tools to discover what data exists.

When choosing between community MCP servers and custom implementations, prefer existing community servers for standard integrations (Jira, GitHub, Slack). Reserve custom servers for team-specific workflows that community servers do not cover.

### Common Misconceptions

- **MCP server configuration is global by default**: It is not. Project-level and user-level scopes are distinct. Configuring a server in `~/.claude.json` does not make it available to teammates.
- **MCP tools need manual registration in prompts**: Tools from configured MCP servers are automatically discovered at connection time. You do not need to list them in the system prompt.
- **All MCP integration requires custom servers**: Community MCP servers cover many standard integrations. Building custom servers for common services (Jira, GitHub) is unnecessary work.
- **Resources and tools are the same thing**: Resources are read-only content catalogs; tools are callable functions. Resources reduce the need for exploratory tool calls by providing upfront visibility.
- **Built-in tools are always preferred over MCP tools**: The agent may prefer built-in tools like Grep over more capable MCP tools simply because built-in tools have better-known descriptions. Enhancing MCP tool descriptions prevents this preference.

### Practice Focus

Questions test MCP scoping (project vs. user), credential management (environment variable expansion), and the distinction between tools and resources. Watch for questions where an agent uses Grep when a more capable MCP search tool is available — the fix is improving the MCP tool's description, not removing Grep.

## Task Statement 2.5: Select and apply built-in tools (Read, Write, Edit, Bash, Grep, Glob) effectively

### Key Concepts

The built-in tools have distinct purposes that are frequently confused: **Grep** searches file contents for patterns (function names, error messages, import statements); **Glob** finds files matching name or extension patterns (`**/*.test.tsx`). Grep answers "which files contain X?" while Glob answers "which files are named like Y?"

**Read** loads full file contents; **Write** overwrites a file entirely; **Edit** makes targeted modifications using unique text matching. When Edit fails because the target text is not unique in the file, the fallback is Read (to get the full content) followed by Write (to output the modified version).

The efficient codebase exploration pattern is incremental: start with Grep to find entry points (e.g., the function name from an error), use Read to examine the discovered files, follow imports to trace flows, and expand outward. Reading all files upfront is wasteful and fills the context window. Tracing function usage across wrapper modules requires first identifying all exported names (with Grep on the source file), then searching for each name across the codebase.

### Common Misconceptions

- **Grep for finding files by name**: Grep searches file contents, not file names. To find files by name pattern, use Glob.
- **Glob for searching file contents**: Glob matches file paths, not contents. To find which files contain a specific string, use Grep.
- **Reading all files before understanding the codebase**: This fills the context window with irrelevant content. Start narrow with Grep, then Read specific files of interest.
- **Edit always works**: Edit requires unique text matching. If the text appears multiple times in the file, Edit fails. The fallback is Read + Write.
- **Bash for everything**: While Bash can do file searches (find, grep), the built-in tools are more efficient and have better integration with the agent's context.

### Practice Focus

Questions present a task ("find all files that import module X," "find all test files," "update a function signature") and ask which tool to use. Content search = Grep. File name search = Glob. Targeted edit = Edit (with Read+Write fallback). Watch for questions about the incremental exploration pattern — the correct approach always starts narrow and expands, never reads everything upfront.

## Task Statement 2.6: Handle MCP elicitation requests for mid-task user input

### Key Concepts

MCP elicitation allows MCP servers to request structured input from the user mid-task via an interactive dialog. Instead of failing silently when information is missing, the server pauses, displays a dialog, and waits for the user's response before continuing.

There are two elicitation modes: **form fields** and **browser URL**. Form field mode displays structured input fields (text inputs, selections, yes/no confirmations) directly in the terminal — suitable for simple, self-contained input like confirming an action or providing a configuration value. Browser URL mode opens a URL in the user's browser — designed for OAuth flows, external service authorization, and any interaction requiring a web-based interface.

Choosing the right mode depends on the input type. Static credentials (API keys, tokens) should not use elicitation at all — they belong in environment variables referenced via `.mcp.json` expansion (e.g., `${API_KEY}`). OAuth and third-party authentication should use browser URL mode so the identity provider handles credentials securely. Simple confirmations and dynamic preferences should use form field mode for inline response.

In headless/CI environments (`-p` flag), elicitation dialogs cannot display interactively. The solution is **Elicitation hooks** — pre-configured hook responses that automatically satisfy elicitation requests without human interaction. This enables unattended operation while preserving the elicitation capability for interactive sessions.

In multi-agent workflows, elicitation creates mid-task interruptions. If the required input is predictable (e.g., which database partition to query), the coordinator should collect it upfront and pass it as a tool parameter — reserving elicitation for genuinely unpredictable needs like expired token re-authentication.

Security is a critical consideration. Elicitation requests originate from MCP servers, which are third-party code. A malicious server could use form field elicitation to phish for credentials. Teams should audit MCP servers, prefer OAuth (browser URL mode) over raw credential entry for sensitive authentication, and verify what a server is requesting before responding.

### Common Misconceptions

- **Elicitation replaces environment variables for credentials**: Static credentials should use environment variable expansion in `.mcp.json`. Elicitation is for dynamic, session-specific input — not configuration that can be set once.
- **Form field input is secure by default**: Form field values are sent directly to the requesting MCP server. There is no sandboxing or encryption layer — the server receives exactly what the user types.
- **Elicitation works the same in headless mode**: Without an interactive terminal, elicitation dialogs cannot display. Elicitation hooks are required for CI/CD and headless pipelines.
- **All user input should go through elicitation**: Predictable input should be collected upfront and passed as parameters. Elicitation is for genuinely dynamic needs that arise mid-task.
- **Elicitation is a Claude feature**: Elicitation requests come from MCP servers, not from Claude itself. The trust boundary is the server, not the AI model.

### Practice Focus

Questions test understanding of when to use each elicitation mode, the distinction between elicitation and environment variables for credentials, security implications of form field input, and handling elicitation in non-interactive environments. Watch for questions where elicitation is proposed for static configuration — the correct answer redirects to environment variables. For security questions, remember that MCP servers are third-party code and elicitation is a potential phishing vector.

## Recurring Themes

Several themes recur across this domain:

- **Descriptions drive behavior**: Tool descriptions are the primary mechanism for tool selection. Improving descriptions is almost always the first fix for misrouting problems. This applies to both custom MCP tools and built-in tool usage.
- **Structured metadata over generic messages**: Whether for errors, tool results, or context passing, structured data with explicit categories and metadata enables better agent decisions than generic strings.
- **Least privilege for tool access**: Each agent should have the minimum set of tools needed for its role. More tools does not mean more capable — it means more confused.
- **Scoping and isolation**: MCP servers have project vs. user scope. Subagents have isolated tool sets. These boundaries exist for reliability and should be respected, not worked around.
- **Incremental over exhaustive**: Whether exploring a codebase or discovering available tools, start narrow and expand based on findings rather than loading everything upfront.
