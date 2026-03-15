---
name: domain-3-claude-code-config
description: Training curriculum for Domain 3: Claude Code Configuration & Workflows (20% of exam). Use when /training targets this domain.
---

# Domain 3: Claude Code Configuration & Workflows (20%)

## Overview
- Weight: 20% of scored content
- Task statements: 3.1–3.6
- Primary scenarios: Code Generation with Claude Code, Developer Productivity with Claude, Claude Code for Continuous Integration
- Cross-domain connections: Tool Design & MCP Integration (MCP server configuration in .mcp.json), Prompt Engineering (CI prompt design, structured output for review), Context Management (context window usage in plan mode, session management)

## Task Statement 3.1: Configure CLAUDE.md files with appropriate hierarchy, scoping, and modular organization

### Key Concepts

CLAUDE.md files form a configuration hierarchy with three levels: user-level (`~/.claude/CLAUDE.md`), project-level (`.claude/CLAUDE.md` or root `CLAUDE.md`), and directory-level (subdirectory CLAUDE.md files). All levels that apply are loaded and combined — they complement each other rather than overriding. User-level settings are personal to each developer and not shared through version control; each team member must create their own.

The `@import` syntax enables modular organization by referencing external files. Instead of one massive CLAUDE.md, you can import specific standards files relevant to each package. For example, a frontend package's CLAUDE.md might import `@import ../standards/react-patterns.md` while a backend package imports `@import ../standards/api-conventions.md`. This keeps each CLAUDE.md focused and maintainable.

The `.claude/rules/` directory provides an alternative to a monolithic CLAUDE.md. Topic-specific rule files (e.g., `testing.md`, `api-conventions.md`, `deployment.md`) can be placed here and are automatically loaded. This is particularly useful for large projects where a single CLAUDE.md would become unwieldy.

The `/memory` command verifies which memory files are loaded in the current session, useful for diagnosing inconsistent behavior across sessions or team members.

### Common Misconceptions

- **Project-level CLAUDE.md overrides user-level**: Both levels apply simultaneously. User-level settings complement project-level settings rather than being overridden by them.
- **User-level CLAUDE.md is shared via version control**: It is not. `~/.claude/CLAUDE.md` is local to each developer. If a new team member does not see personal preferences, they need to create their own file.
- **CLAUDE.md must be registered in .mcp.json**: This confuses MCP server configuration with CLAUDE.md configuration. They are separate systems.
- **User-level and project-level are mutually exclusive**: Both can coexist and both are loaded. There is no mutual exclusion.
- **@import paths are resolved at runtime from the working directory**: Import paths are relative to the file containing the @import directive, not the current working directory.

### Practice Focus

Questions frequently test the hierarchy and scoping of CLAUDE.md files. The most common trap is believing that one level overrides another — they always complement. Watch for "new team member" scenarios — if the member does not have their own user-level file, they will not see personal preferences regardless of what other team members have configured. Also watch for questions about modular organization — @import and .claude/rules/ are both valid approaches.

## Task Statement 3.2: Create and configure custom slash commands and skills

### Key Concepts

Custom slash commands live in two locations: project-scoped (`.claude/commands/`) for team-wide availability via version control, and user-scoped (`~/.claude/commands/`) for personal commands. This mirrors the CLAUDE.md scoping pattern.

Skills in `.claude/skills/` with SKILL.md files support frontmatter configuration including `context: fork`, `allowed-tools`, and `argument-hint`. The `context: fork` option is particularly important — it runs the skill in an isolated sub-agent context, preventing skill outputs from polluting the main conversation. This is valuable for skills that produce verbose output (codebase analysis) or exploratory context (brainstorming alternatives).

The `allowed-tools` frontmatter restricts tool access during skill execution, enforcing least privilege. The `argument-hint` frontmatter prompts developers for required parameters when they invoke the skill without arguments.

Personal skill customization is done by creating personal variants in `~/.claude/skills/` with different names to avoid affecting teammates. This allows individual developers to customize skill behavior without modifying shared project skills.

The key decision between skills and CLAUDE.md: skills are on-demand (invoked when needed for specific tasks), while CLAUDE.md instructions are always loaded (universal standards that apply to every interaction).

### Common Misconceptions

- **Skills and commands are the same thing**: Commands are slash commands invoked by `/command-name`; skills are invocable capabilities with additional frontmatter options like `context: fork`.
- **context: fork shares state with the main session**: Forked contexts are isolated. The skill runs in a sub-agent and returns results to the main session, but its verbose intermediate outputs do not persist in the main context.
- **Personal skills override project skills of the same name**: To avoid conflicts, personal variants should use different names. Same-name collisions between scopes create unpredictable behavior.
- **All instructions belong in CLAUDE.md**: Task-specific instructions that are only relevant for certain workflows belong in skills, not in the always-loaded CLAUDE.md. Putting everything in CLAUDE.md wastes context tokens.

### Practice Focus

Questions test the distinction between project-scoped and user-scoped commands/skills, the purpose of `context: fork`, and when to use skills versus CLAUDE.md. If the scenario involves verbose output that should not clutter the main session, `context: fork` is the answer. If the scenario involves team-wide availability, project-scoped location is the answer.

## Task Statement 3.3: Apply path-specific rules for conditional convention loading

### Key Concepts

The `.claude/rules/` directory supports files with YAML frontmatter containing `paths` fields with glob patterns for conditional rule activation. When a rule file specifies `paths: ["terraform/**/*"]`, those rules load only when editing files matching the pattern. This reduces irrelevant context and saves tokens — Terraform conventions do not need to be loaded when editing React components.

Glob-pattern rules have an advantage over directory-level CLAUDE.md files for conventions that span multiple directories. For example, test files may be spread throughout a codebase (`src/auth/*.test.tsx`, `src/api/*.test.tsx`, `lib/*.test.ts`). A path-specific rule with `paths: ["**/*.test.tsx", "**/*.test.ts"]` applies to all test files regardless of location, while a directory-level CLAUDE.md would need to be duplicated in every directory.

The choice between path-specific rules and subdirectory CLAUDE.md files depends on whether the convention is directory-specific (use CLAUDE.md in that directory) or file-type-specific across the codebase (use path-specific rules).

### Common Misconceptions

- **Path-specific rules are always active**: They are not. Rules with `paths` patterns only load when editing files that match the pattern. This is their primary advantage — conditional activation.
- **Directory-level CLAUDE.md is always better for subdirectory conventions**: When conventions apply to a file type that spans multiple directories (e.g., all test files), path-specific rules are more maintainable than duplicating CLAUDE.md files in every directory.
- **Path patterns use regex syntax**: Path patterns use glob syntax (e.g., `**/*.test.tsx`), not regex. The `**` matches any directory depth; `*` matches any characters within a path segment.
- **All rules should be path-specific**: Rules that apply universally (coding standards, commit conventions) should remain in the main CLAUDE.md or unpatterned rules files. Path-specific rules are for conditional conventions only.

### Practice Focus

Questions present a scenario where conventions should apply conditionally and ask for the best configuration approach. If the convention is tied to a file type across the codebase, path-specific rules in `.claude/rules/` are the answer. If tied to a specific directory, a subdirectory CLAUDE.md may suffice. Watch for questions about token efficiency — loading all rules unconditionally wastes context.

## Task Statement 3.4: Determine when to use plan mode vs direct execution

### Key Concepts

Plan mode is designed for complex tasks involving large-scale changes, multiple valid approaches, architectural decisions, and multi-file modifications. It enables safe codebase exploration and design before committing to changes, preventing costly rework. Examples: microservice restructuring, library migrations affecting 45+ files, choosing between integration approaches with different infrastructure requirements.

Direct execution is appropriate for simple, well-scoped changes with clear scope: single-file bug fixes with a clear stack trace, adding a date validation conditional, renaming a variable. Using plan mode for these simple tasks wastes time without adding value.

The Explore subagent isolates verbose discovery output during plan mode, returning summaries to preserve main conversation context. Without it, the context window fills with exploration results during multi-phase tasks, degrading later reasoning quality.

A powerful pattern combines plan mode for investigation with direct execution for implementation. First, use plan mode to understand the codebase and design the approach; then switch to direct execution for the planned changes. This provides the safety of exploration with the efficiency of direct action.

### Common Misconceptions

- **Plan mode for everything**: Using plan mode for a simple one-line bug fix is wasteful. Plan mode has overhead that is only justified for complex, multi-file changes.
- **Direct execution for architectural changes**: Jumping straight into implementing a microservice restructuring without planning leads to costly rework when the approach does not work.
- **Plan mode prevents all mistakes**: Plan mode enables better decisions through exploration, but it does not guarantee correctness. It reduces the risk of major architectural mistakes.
- **The Explore subagent is only for plan mode**: While commonly used with plan mode, the Explore subagent is useful anytime verbose exploration output would clutter the main context.

### Practice Focus

Questions describe a task and ask whether to use plan mode or direct execution. The deciding factors are: scope (single file vs. multi-file), complexity (well-understood vs. multiple valid approaches), and risk (low-impact change vs. architectural decision). Complex, multi-file, uncertain tasks get plan mode; simple, single-file, clear tasks get direct execution.

## Task Statement 3.5: Apply iterative refinement techniques for progressive improvement

### Key Concepts

When prose descriptions produce inconsistent results, concrete input/output examples are the most effective way to communicate expected transformations. Instead of explaining what a migration script should do in words, provide 2-3 examples of input data and the expected output. This eliminates ambiguity in interpretation.

Test-driven iteration writes test suites first (covering expected behavior, edge cases, and performance requirements), then iterates by sharing test failures to guide progressive improvement. Each iteration shares the specific failing tests and their output, giving Claude targeted information about what needs to change.

The interview pattern has Claude ask clarifying questions before implementing, surfacing considerations the developer may not have anticipated. This is particularly valuable in unfamiliar domains where the developer may not know the right questions to ask (e.g., cache invalidation strategies, failure modes in distributed systems).

The batching decision: provide all issues in a single message when they interact with each other (fixing one affects another), and fix them sequentially when they are independent. Interacting problems need holistic reasoning; independent problems benefit from focused attention.

### Common Misconceptions

- **More detailed prose always produces better results**: Past a certain point, longer prose descriptions introduce ambiguity rather than reducing it. Concrete examples are more effective than verbose explanations.
- **Test-driven iteration requires perfect tests upfront**: Tests can be refined alongside the implementation. The key is having some tests to provide objective feedback on each iteration.
- **The interview pattern slows development**: For simple tasks, it does add overhead. But for complex tasks in unfamiliar domains, it prevents costly misunderstandings and rework.
- **Always fix everything at once**: Providing 10 unrelated issues in a single message dilutes attention. Independent issues should be addressed sequentially.

### Practice Focus

Questions present a scenario where Claude produces inconsistent or incorrect output and ask for the best improvement strategy. If the issue is ambiguous requirements, examples are the answer. If the issue is missing edge cases, test-driven iteration is the answer. If the issue is incomplete domain understanding, the interview pattern is the answer.

## Task Statement 3.6: Integrate Claude Code into CI/CD pipelines

### Key Concepts

The `-p` (or `--print`) flag runs Claude Code in non-interactive mode, essential for automated pipelines where no human is available to provide interactive input. Without it, CI runs hang waiting for input.

`--output-format json` combined with `--json-schema` enforces structured output in CI contexts, producing machine-parseable findings that can be automatically posted as inline PR comments. This is how you turn Claude Code's natural language output into structured data that CI tooling can process.

CLAUDE.md serves as the mechanism for providing project context to CI-invoked Claude Code: testing standards, fixture conventions, review criteria, and what constitutes a valuable finding. Without this context, CI-invoked Claude Code produces generic, low-value output.

Session context isolation is critical for review quality: the same Claude session that generated code is less effective at reviewing its own changes because it retains reasoning context from generation, making it less likely to question its own decisions. An independent review instance (without prior reasoning context) catches more issues.

When re-running reviews after new commits, include prior review findings in context and instruct Claude to report only new or still-unaddressed issues. This prevents duplicate comments that frustrate developers.

### Common Misconceptions

- **Self-review is equivalent to independent review**: A model reviewing its own output in the same session has confirmation bias from retained reasoning context. Independent instances without that context are more effective.
- **Extended thinking fixes self-review limitations**: Extended thinking helps with complex reasoning but does not overcome the fundamental self-review bias of retained generation context.
- **CI runs can use interactive mode**: Without the `-p` flag, CI runs hang waiting for input that never comes. Non-interactive mode is required.
- **JSON output eliminates all quality issues**: `--output-format json` with `--json-schema` eliminates format/syntax issues but does not prevent semantic problems (irrelevant findings, false positives). Prompt quality still matters.
- **Prior review context is unnecessary on re-runs**: Without prior findings in context, the re-run produces duplicate comments on already-reviewed code. Including prior findings enables incremental review.

### Practice Focus

Questions about CI integration test three main areas: the `-p` flag for non-interactive execution, structured output for machine-parseable findings, and self-review limitations. If a scenario mentions review quality problems with the code generator reviewing its own code, the answer involves independent review instances. If the scenario mentions duplicate CI comments, the answer involves providing prior findings in context.

## Recurring Themes

Several themes recur across this domain:

- **Scoping and sharing**: CLAUDE.md, commands, skills, and MCP servers all have project-level (shared, version-controlled) and user-level (personal, local) scopes. Understanding which scope to use and how they combine is foundational.
- **Context efficiency**: Path-specific rules, context: fork, the Explore subagent, and plan mode all address the same underlying challenge — keeping irrelevant content out of the context window. Token efficiency directly affects reasoning quality.
- **Right tool for the job**: Plan mode vs. direct execution, skills vs. CLAUDE.md, path-specific rules vs. directory CLAUDE.md — the exam tests judgment about which mechanism is appropriate for each situation.
- **CI as a distinct context**: CI runs differ from interactive use in several ways: non-interactive mode required, structured output needed, self-review limitations, incremental review across runs. These differences recur across multiple task statements.
- **Examples over prose**: Concrete input/output examples consistently outperform verbose prose descriptions for communicating expected behavior, both in CLAUDE.md configuration and in iterative refinement.
