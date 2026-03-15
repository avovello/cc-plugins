---
name: domain-1-agentic-architecture
description: Training curriculum for Domain 1: Agentic Architecture & Orchestration (27% of exam). Use when /training targets this domain.
---

# Domain 1: Agentic Architecture & Orchestration (27%)

## Overview
- Weight: 27% of scored content
- Task statements: 1.1–1.7
- Primary scenarios: Customer Support Resolution Agent, Multi-Agent Research System, Developer Productivity with Claude
- Cross-domain connections: Tool Design & MCP Integration (tool selection within agents), Context Management & Reliability (context passing between agents, error propagation), Claude Code Configuration (session management, plan mode)

## Task Statement 1.1: Design and implement agentic loops for autonomous task execution

### Key Concepts

The agentic loop is the fundamental execution pattern for Claude-based agents. At its core, the loop sends a request to Claude, inspects the `stop_reason` in the response, and decides what to do next. When `stop_reason` is `"tool_use"`, the loop executes the requested tool, appends the tool result to the conversation history, and sends the updated history back to Claude for the next iteration. When `stop_reason` is `"end_turn"`, the loop terminates and returns Claude's final response to the user.

The critical principle here is model-driven decision-making. Claude decides which tool to call next based on the full conversation context — your code should not pre-determine tool sequences or parse Claude's natural language output to decide what happens next. The loop is a simple dispatcher: Claude requests, your code executes, results go back to Claude. This keeps the intelligence in the model and the reliability in the code.

Tool results must be appended to the existing conversation history, not sent in isolation. If you discard prior history and start a new conversation with just the tool result, Claude loses all context about what it was doing and why. The full conversation history is what enables Claude to reason coherently across multiple tool calls.

### Common Misconceptions

- **Parsing text for loop control**: Some candidates believe that checking whether Claude included text content alongside a tool call is a valid way to determine if the loop should end. In reality, Claude may include explanatory text alongside tool_use blocks — the only reliable termination signal is `stop_reason: "end_turn"`.
- **Pre-determined tool chains**: A common distractor is executing tool A, then immediately calling tool B with parsed results before sending anything back to Claude. This violates model-driven orchestration — you are making the decision about what tool to call next, not Claude.
- **Arbitrary iteration caps as primary control**: Setting `maxTurns: 50` and relying on that as the main stopping mechanism is an anti-pattern. The primary control should be `stop_reason`, with iteration caps as a safety net only.
- **Discarding conversation history**: Starting a fresh conversation with only the latest tool result destroys conversational coherence. Claude needs the full history to maintain its reasoning chain.

### Practice Focus

Pay attention to questions that present a tool_use response and ask "what should the loop do next?" The correct answer always involves: execute the tool, append the result to history, send the full history back. Watch for distractors that inject programmatic logic between Claude's request and the next API call. Also watch for questions about loop termination — the answer is always `stop_reason`, never text parsing or iteration counting.

## Task Statement 1.2: Orchestrate multi-agent systems with coordinator-subagent patterns

### Key Concepts

The hub-and-spoke architecture is the standard pattern for multi-agent systems built with the Claude Agent SDK. A coordinator agent sits at the center and manages all communication, error handling, and information routing between subagents. Subagents do not talk to each other directly — all information flows through the coordinator.

Subagents operate with isolated context. They do not automatically inherit the coordinator's conversation history or share memory between invocations. This is a fundamental architectural property: when you spawn a subagent, you must explicitly provide everything it needs to know in its prompt. The coordinator's job is task decomposition (breaking a complex query into subtasks), delegation (sending each subtask to the right subagent), and result aggregation (combining subagent outputs into a coherent response).

A key design skill is dynamic subagent selection. Rather than always routing every query through the full pipeline of subagents, a well-designed coordinator analyzes query requirements and invokes only the subagents needed. For simple queries, this might mean calling just one subagent; for complex research, it might mean invoking search, analysis, and synthesis agents in sequence.

Iterative refinement loops are important for quality: the coordinator evaluates synthesis output for gaps, re-delegates targeted queries to search and analysis subagents, and re-invokes synthesis until coverage is sufficient. This is preferable to a single pass through the pipeline.

### Common Misconceptions

- **Subagents inherit coordinator context**: A very common wrong answer assumes subagents can access the coordinator's conversation history or that context is shared automatically. It is not — context must be explicitly passed.
- **Direct subagent-to-subagent communication**: Distractors often propose having the search agent pass results directly to the analysis agent. All communication must route through the coordinator for observability and consistent error handling.
- **Always invoking all subagents**: Some answers suggest running the full pipeline for every query. A good coordinator dynamically selects which subagents to invoke based on query complexity.
- **Overly narrow task decomposition**: Splitting a broad research topic into too many narrow subtasks can lead to gaps where no subagent covers the connections between subtopics. The coordinator should assign subtopics that overlap slightly to ensure coverage.

### Practice Focus

Questions about multi-agent systems frequently test whether you understand context isolation. If an answer assumes subagents "see" what the coordinator sees, it is wrong. Also watch for questions about error handling — the coordinator must handle subagent failures, not propagate them silently or terminate the entire workflow.

## Task Statement 1.3: Configure subagent invocation, context passing, and spawning

### Key Concepts

The Task tool is the mechanism for spawning subagents. For a coordinator to invoke subagents, its `allowedTools` must include `"Task"`. The AgentDefinition configuration specifies each subagent type's description, system prompt, and tool restrictions.

Context passing is entirely explicit. When the coordinator spawns a subagent, it must include all relevant findings from prior agents directly in the subagent's prompt. For example, passing web search results and document analysis outputs to the synthesis subagent means serializing those results into the prompt text. Structured data formats should separate content from metadata (source URLs, document names, page numbers) to preserve attribution through the pipeline.

Parallel subagent spawning is achieved by emitting multiple Task tool calls in a single coordinator response, rather than across separate turns. This is both faster and more token-efficient.

Fork-based session management (`fork_session`) creates independent branches from a shared analysis baseline. This is useful for exploring divergent approaches — for example, comparing two refactoring strategies from the same codebase analysis.

Coordinator prompts should specify research goals and quality criteria rather than step-by-step procedural instructions. This enables subagent adaptability — the subagent can adjust its approach based on what it discovers rather than rigidly following a prescribed sequence.

### Common Misconceptions

- **Automatic context inheritance**: The most common distractor claims subagents "inherit" context from the coordinator or from previous subagents. They do not.
- **Sequential spawning when parallel is possible**: Some answers spawn subagents one at a time across separate turns when they could be spawned in parallel within a single turn.
- **Procedural coordinator prompts**: Providing step-by-step instructions to subagents reduces adaptability. Goal-oriented prompts ("find all references to X and assess their credibility") are preferred over procedural ones ("first search Google, then check Wikipedia, then...").
- **Confusing fork_session with subagent spawning**: Forking creates an independent branch of the current session; spawning creates a new agent with its own context. They serve different purposes.

### Practice Focus

Questions often present a scenario where a subagent needs information from a previous phase and ask how to provide it. The answer is always "include it in the prompt." Watch for fork_session questions — these test whether you understand that forking preserves the analysis baseline while allowing divergent exploration.

## Task Statement 1.4: Implement multi-step workflows with enforcement and handoff patterns

### Key Concepts

The critical distinction here is between programmatic enforcement and prompt-based guidance for workflow ordering. When deterministic compliance is required — such as verifying customer identity before processing a refund — prompt instructions alone have a non-zero failure rate. A programmatic prerequisite that blocks `process_refund` until `get_customer` has returned a verified customer ID provides a deterministic guarantee.

Prompt-based guidance is appropriate for soft preferences (e.g., "try to resolve billing issues before addressing shipping concerns"), but not for hard business rules where a failure has financial or safety consequences. The exam tests your ability to distinguish when each approach is appropriate.

Structured handoff protocols are essential for mid-process escalation. When an agent escalates to a human, the handoff summary must include customer details, root cause analysis, refund amounts, and recommended actions. The human agent typically lacks access to the full conversation transcript, so the handoff must be self-contained.

Multi-concern decomposition is another key skill: when a customer raises three issues in one message, the agent should decompose them into distinct items, investigate each (potentially in parallel using shared context), and synthesize a unified resolution.

### Common Misconceptions

- **Prompt instructions are sufficient for critical ordering**: The most tested misconception. Candidates who choose "add stronger instructions to the system prompt" for identity verification before refunds are wrong — programmatic enforcement is required for deterministic compliance.
- **Few-shot examples guarantee compliance**: Few-shot examples improve probabilistic compliance but do not guarantee it. For business-critical sequences, hooks or programmatic gates are needed.
- **Tool routing classifiers solve ordering problems**: A routing classifier controls which tools are available, not the order in which they are called. It addresses a different problem.
- **Minimal handoff summaries**: Answers that include only "escalate to human" without structured context (customer ID, root cause, recommended action) miss the point of handoff protocols.

### Practice Focus

This task statement is heavily tested. Always ask yourself: "Is this a hard business rule or a soft preference?" If it is a hard rule (identity verification, refund limits, compliance checks), the answer involves programmatic enforcement. If it is a soft preference, prompt guidance may suffice. Handoff questions test whether you include comprehensive structured context.

## Task Statement 1.5: Apply Agent SDK hooks for tool call interception and data normalization

### Key Concepts

Hooks are Agent SDK mechanisms that intercept tool calls and tool results at specific points in the execution lifecycle. PostToolUse hooks intercept tool results before the model processes them, enabling data transformation and normalization. For example, different MCP tools may return dates as Unix timestamps, ISO 8601 strings, or localized formats — a PostToolUse hook can normalize these to a consistent format before Claude sees them.

Tool call interception hooks inspect outgoing tool calls before execution. This enables policy enforcement: for example, blocking refunds exceeding $500 and redirecting to human escalation. The key advantage of hooks over prompt instructions is deterministic guarantees. A hook that blocks a policy-violating action will always block it; a prompt instruction will work most of the time but has a non-zero failure rate.

The decision framework is: use hooks when business rules require guaranteed compliance (financial limits, identity verification, data access controls). Use prompt-based enforcement when the rule is a preference or guideline where occasional deviation is acceptable.

### Common Misconceptions

- **Hooks and prompt instructions are interchangeable**: They are not. Hooks provide deterministic guarantees; prompts provide probabilistic compliance. The exam tests whether you know when each is appropriate.
- **PostToolUse hooks modify the tool call**: PostToolUse hooks intercept the result, not the call. To intercept the outgoing call, you need a different hook point.
- **Normalization should happen in the system prompt**: Asking Claude to "interpret all dates as ISO 8601" is less reliable than normalizing the data before Claude sees it. Hooks handle data transformation; prompts handle reasoning.
- **Hooks are only for blocking**: Hooks serve both blocking (policy enforcement) and transformation (data normalization) purposes.

### Practice Focus

Questions typically present a scenario where something goes wrong (wrong date format causes confusion, refund exceeds limit) and ask for the best fix. If the issue involves data format inconsistency, the answer is a PostToolUse normalization hook. If the issue involves policy violations, the answer is a tool call interception hook. If the issue involves reasoning quality, the answer might be prompt improvement.

## Task Statement 1.6: Design task decomposition strategies for complex workflows

### Key Concepts

Two primary decomposition patterns exist: fixed sequential pipelines (prompt chaining) and dynamic adaptive decomposition. Prompt chaining breaks a task into sequential steps where each step's output feeds the next — for example, analyzing each file individually, then running a cross-file integration pass. This works well for predictable, structured tasks like multi-aspect code reviews.

Dynamic adaptive decomposition generates subtasks based on what is discovered at each step. This is appropriate for open-ended investigation tasks where the next step depends on findings from the current step — for example, "add comprehensive tests to a legacy codebase" requires first mapping structure, identifying high-impact areas, then creating a prioritized plan that adapts as dependencies are discovered.

A critical skill is splitting large code reviews into per-file local analysis passes plus a separate cross-file integration pass. This avoids attention dilution — when reviewing many files at once, the model may miss issues in the middle (the "lost in the middle" effect). Per-file passes ensure thorough analysis of each file, while the integration pass catches cross-file issues like data flow problems.

### Common Misconceptions

- **One-pass reviews for large codebases**: Reviewing 20+ files in a single pass leads to attention dilution. The correct approach is per-file passes plus an integration pass.
- **Always using dynamic decomposition**: Fixed pipelines are more predictable and appropriate for structured tasks. Dynamic decomposition adds complexity that is only justified for open-ended tasks.
- **Prompt chaining for open-ended investigation**: When the task is exploratory and the next step depends on current findings, a fixed pipeline cannot adapt. Dynamic decomposition is needed.
- **Skipping the integration pass**: Per-file analysis catches local issues but misses cross-file problems (e.g., a function in file A returns a new format that file B does not handle). The integration pass is essential.

### Practice Focus

Questions present a specific task and ask which decomposition approach is appropriate. Fixed pipelines for structured, predictable tasks; dynamic decomposition for open-ended, exploratory tasks. Watch for questions about code review specifically — the per-file + integration pass pattern is a favorite exam topic.

## Task Statement 1.7: Manage session state, resumption, and forking

### Key Concepts

Named session resumption using `--resume <session-name>` continues a specific prior conversation. This is useful for multi-session investigations where you want to pick up where you left off. However, if the codebase has changed since the last session, the resumed session contains stale tool results — you must inform the agent about specific file changes for targeted re-analysis.

`fork_session` creates independent branches from a shared analysis baseline. This is valuable for exploring divergent approaches: for example, comparing two testing strategies or refactoring approaches from a shared codebase analysis. Each fork proceeds independently without affecting the other.

A key judgment call is choosing between session resumption and starting fresh. When prior context is mostly valid (minor changes, continuation of the same task), resumption is efficient. When prior tool results are stale (significant code changes, refactoring since last session), starting a new session with a structured summary of prior findings is more reliable than resuming with outdated information.

When resuming after code modifications, the best practice is to inform the session about specific file changes rather than requiring full re-exploration. This targeted approach is both faster and preserves the valid portions of prior context.

### Common Misconceptions

- **Resumed sessions automatically detect code changes**: They do not. If you resume after modifying files, the session still contains old tool results. You must explicitly inform the agent about changes.
- **fork_session shares state between branches**: Forks are independent after the fork point. Changes in one branch do not appear in the other.
- **Always resume instead of starting fresh**: When tool results are significantly stale, resumption introduces more confusion than it saves time. A fresh session with injected summaries is often better.
- **Using --resume without session names**: Named sessions are important for managing multiple investigation threads. Unnamed resumption picks up the most recent session, which may not be the one you want.

### Practice Focus

Questions test the tradeoff between resumption and fresh starts. If the scenario mentions significant code changes since the last session, the answer likely involves starting fresh with a structured summary. If the scenario mentions minor changes, targeted re-analysis after resumption is appropriate. Fork questions test whether you understand that forks create independent exploration branches.

## Recurring Themes

Several themes recur across multiple task statements in this domain:

- **Model-driven vs. programmatic control**: The exam consistently tests whether you know when to let Claude decide (tool selection, investigation direction) versus when to enforce programmatically (business rules, workflow ordering, data normalization). The rule of thumb: let Claude drive reasoning and decisions; enforce deterministic rules in code.
- **Explicit context passing**: Subagents, resumed sessions, and multi-step workflows all require explicit context management. Nothing is automatic — you must deliberately pass, preserve, and structure information.
- **Deterministic vs. probabilistic compliance**: Prompt instructions are probabilistic; hooks and programmatic gates are deterministic. Critical business rules demand deterministic enforcement.
- **Decomposition granularity**: Both over-decomposition (too many tiny subtasks with gaps between them) and under-decomposition (one massive pass that dilutes attention) are anti-patterns. The right granularity depends on the task structure.
- **Hub-and-spoke communication**: All multi-agent communication routes through the coordinator. Direct agent-to-agent communication is never the correct answer.
