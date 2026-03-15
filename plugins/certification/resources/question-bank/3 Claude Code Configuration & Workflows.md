# Domain 3: Claude Code Configuration & Workflows (20%)
## Comprehensive Question Bank

---

## Task Statement 3.1: Configure CLAUDE.md files with appropriate hierarchy, scoping, and modular organization

### Q3.1.1 — Scenario: Code Generation with Claude Code
**Difficulty: Easy | Knowledge: CLAUDE.md configuration hierarchy**

Your team's project has a root-level `CLAUDE.md` with coding standards, and one developer has personal preferences (preferred variable naming, comment style) in `~/.claude/CLAUDE.md`. A new team member reports that Claude doesn't follow the personal preferences when working on the project. What's the most likely explanation?

A) The new team member hasn't created their own `~/.claude/CLAUDE.md` file — user-level settings are personal and not shared through version control, so each developer must create their own.

B) Project-level CLAUDE.md overrides user-level settings, so personal preferences are ignored.

C) The `~/.claude/CLAUDE.md` file must be registered in the project's `.mcp.json` to take effect.

D) User-level CLAUDE.md only works when no project-level CLAUDE.md exists.

**Correct Answer: A**

**Explanation:** User-level configuration in `~/.claude/CLAUDE.md` is local to each individual developer — it is not shared via version control. Each team member must create their own file with their personal preferences. Option B is incorrect — both levels apply; user-level settings complement project-level settings rather than being overridden. Option C confuses MCP server configuration with CLAUDE.md. Option D incorrectly suggests mutual exclusion between levels.

---

### Q3.1.2 — Scenario: Code Generation with Claude Code
**Difficulty: Medium | Knowledge: @import syntax for modularity**

Your monorepo has three packages: `api/`, `web/`, and `shared/`. Each has different coding standards. The root CLAUDE.md has grown to 800 lines covering all three packages. Developers report that Claude sometimes applies `web/` conventions when editing `api/` code. What is the most effective restructuring?

A) Create separate standards files for each package (e.g., `api/standards.md`, `web/standards.md`) and use `@import` in each package's CLAUDE.md to reference only the relevant standards file, keeping each package's context focused.

B) Split the root CLAUDE.md into three sections with clear headers ("## API Standards", "## Web Standards", "## Shared Standards") and rely on Claude to match the correct section to the file being edited.

C) Create three separate root CLAUDE.md files — one per package — and configure a build script to copy the correct one depending on which package is active.

D) Move all standards into the system prompt configuration instead of CLAUDE.md files.

**Correct Answer: A**

**Explanation:** The `@import` syntax allows each package's CLAUDE.md to reference only its relevant standards file, keeping context focused and preventing cross-contamination. When editing `api/` code, only API standards are loaded. Option B keeps everything in one file and relies on Claude to infer which section applies — this is what caused the original problem. Option C requires external tooling and doesn't support simultaneous work across packages. Option D moves configuration to an inappropriate location.

---

### Q3.1.3 — Scenario: Code Generation with Claude Code
**Difficulty: Medium | Skill: Splitting monolithic CLAUDE.md into rules/**

Your project's CLAUDE.md has grown to 600 lines covering testing conventions, API patterns, deployment rules, and code style. Developers find it hard to maintain and Claude occasionally misses instructions buried in the middle. What restructuring approach is most maintainable?

A) Split the monolithic CLAUDE.md into focused topic-specific files in `.claude/rules/` — for example, `testing.md`, `api-conventions.md`, `deployment.md`, and `code-style.md` — keeping each file focused on one concern.

B) Keep the single CLAUDE.md but reorganize it with numbered sections and a table of contents at the top.

C) Create multiple CLAUDE.md files in subdirectories, one for each concern area.

D) Move the less frequently needed instructions to a separate file that developers manually include with `@import` when needed.

**Correct Answer: A**

**Explanation:** The `.claude/rules/` directory is designed for exactly this purpose — organizing topic-specific rule files as an alternative to a monolithic CLAUDE.md. Each file covers one concern, making maintenance easier and reducing the chance of instructions being missed in a long document. Option B improves readability for humans but doesn't solve the long-context problem for Claude. Option C places files by concern rather than by directory structure, which doesn't align with how subdirectory CLAUDE.md files work. Option D requires manual inclusion, which defeats automatic loading.

---

### Q3.1.4 — Scenario: Code Generation with Claude Code
**Difficulty: Hard | Skill: Diagnosing configuration hierarchy issues**

A senior developer has Claude configured to always generate comprehensive JSDoc comments on every function. Junior developers on the team don't see this behavior — Claude generates minimal or no JSDoc comments for them. Where is the JSDoc instruction most likely configured, and how would you diagnose this?

A) The instruction is likely in the senior developer's `~/.claude/CLAUDE.md` (user-level), which is not shared via version control. Diagnose by having each developer run the `/memory` command to verify which memory files are loaded and compare across team members.

B) The instruction is in a subdirectory CLAUDE.md that only applies to the files the senior developer typically edits.

C) The instruction is in a `.claude/rules/` file with a path glob that matches only certain file patterns the senior developer works with.

D) The instruction is in the project CLAUDE.md but uses conditional syntax that checks the developer's username.

**Correct Answer: A**

**Explanation:** When one developer has behavior that others don't see, the most likely cause is user-level configuration (`~/.claude/CLAUDE.md`) which is personal and not shared. The `/memory` command reveals which memory files are loaded, enabling direct comparison between developers to confirm the diagnosis. Option B is possible but would affect anyone editing those files, not just one developer. Option C would similarly affect all developers editing matching files. Option D describes functionality that doesn't exist in CLAUDE.md.

---

### Q3.1.5 — Scenario: Code Generation with Claude Code
**Difficulty: Easy | Knowledge: Directory-level CLAUDE.md**

Your project has a `src/api/` directory with its own `CLAUDE.md` containing REST API conventions (HTTP method usage, response format standards, error handling patterns). When does Claude apply these conventions?

A) When editing files within the `src/api/` directory or its subdirectories — directory-level CLAUDE.md files apply to their directory scope.

B) Always, because all CLAUDE.md files in the project are loaded at startup regardless of what file is being edited.

C) Only when the developer explicitly opens the `src/api/CLAUDE.md` file in their editor.

D) Only when the developer navigates to the `src/api/` directory in their terminal before starting Claude Code.

**Correct Answer: A**

**Explanation:** Directory-level CLAUDE.md files apply when editing files within that directory and its subdirectories. This creates a natural scoping mechanism — API conventions load only when working on API files. Option B is incorrect — the hierarchy means lower-level files are scoped, not globally loaded. Options C and D describe manual activation that doesn't match how the configuration hierarchy works.

---

### Q3.1.6 — Scenario: Code Generation with Claude Code
**Difficulty: Medium | Skill: Using /memory for debugging**

After updating your project's `.claude/rules/testing.md` file with new test conventions, you notice Claude still follows the old conventions during your session. What is the most effective diagnostic step?

A) Run the `/memory` command to verify which memory files are currently loaded and whether the updated `testing.md` is among them, confirming whether the session has picked up the changes.

B) Restart Claude Code entirely and start a new session to force-reload all configuration.

C) Check the file for YAML frontmatter syntax errors that might prevent it from loading.

D) Delete and recreate the file to ensure the filesystem change is detected.

**Correct Answer: A**

**Explanation:** The `/memory` command shows which memory files are loaded in the current session, making it the correct diagnostic tool. It reveals whether the updated file was loaded, whether an older cached version is active, or whether the file isn't loading at all. Option B might fix the immediate problem but doesn't diagnose the root cause — if there's a configuration error, the problem will persist. Options C and D are possible next steps after diagnosis, not the first diagnostic step.

---

## Task Statement 3.2: Create and configure custom slash commands and skills

### Q3.2.1 — Scenario: Code Generation with Claude Code
**Difficulty: Easy | Knowledge: Project-scoped vs user-scoped commands**

You create a `/review` slash command for your team's standard code review checklist. Every developer should have it automatically when they clone the repo. Where should you place the command file?

A) In `.claude/commands/` in the project repository — project-scoped commands are version-controlled and automatically available to all developers.

B) In `~/.claude/commands/` on each developer's machine.

C) In the root `CLAUDE.md` file as a command definition block.

D) In `.claude/skills/` with a SKILL.md frontmatter file.

**Correct Answer: A**

**Explanation:** Project-scoped commands in `.claude/commands/` are committed to version control and available to every developer who clones or pulls the repository. Option B is user-scoped — each developer would need to manually create the file. Option C uses the wrong mechanism — CLAUDE.md is for instructions, not command definitions. Option D describes skills, which are different from commands.

---

### Q3.2.2 — Scenario: Code Generation with Claude Code
**Difficulty: Medium | Knowledge: context: fork for skill isolation**

You create a skill that performs deep codebase analysis, generating 50+ pages of verbose output about architecture, dependencies, and code patterns. When used in a regular session, this analysis output fills the context window and degrades the quality of subsequent interactions. How should you configure this skill?

A) Add `context: fork` to the skill's SKILL.md frontmatter so it runs in an isolated sub-agent context, preventing its verbose output from polluting the main conversation.

B) Add `max_tokens: 2000` to the skill's frontmatter to limit its output length.

C) Split the skill into 10 smaller skills, each analyzing a different aspect of the codebase.

D) Add a `/compact` call at the end of the skill to reduce context usage after completion.

**Correct Answer: A**

**Explanation:** `context: fork` runs the skill in an isolated sub-agent context. The verbose analysis executes in the fork, and only the final summary returns to the main conversation — preventing context pollution. Option B arbitrarily limits output quality. Option C fragments the workflow without addressing the context problem (each small skill still adds to the main context). Option D reduces context after pollution has already occurred, which may lose important earlier conversation content.

---

### Q3.2.3 — Scenario: Code Generation with Claude Code
**Difficulty: Medium | Skill: Configuring allowed-tools in skills**

You create a skill that generates documentation by reading source code and writing markdown files. During testing, you discover the skill occasionally executes Bash commands to run the code it's documenting, which causes unintended side effects. How should you constrain the skill?

A) Add `allowed-tools` to the skill's SKILL.md frontmatter, restricting tool access to only Read and Write operations — this prevents the skill from executing Bash commands or making other potentially destructive calls.

B) Add a note in the skill's instructions: "Do not execute any code. Only read source files and write documentation."

C) Run the skill in a Docker container to sandbox any unintended executions.

D) Remove the Bash tool from the project's global tool configuration.

**Correct Answer: A**

**Explanation:** `allowed-tools` in SKILL.md frontmatter restricts which tools are available during skill execution, providing deterministic constraints. Limiting to Read and Write prevents Bash execution entirely at the configuration level. Option B relies on prompt-based instructions that have a non-zero failure rate. Option C introduces external infrastructure for a problem solvable through configuration. Option D removes Bash globally, affecting all sessions and skills.

---

### Q3.2.4 — Scenario: Code Generation with Claude Code
**Difficulty: Medium | Knowledge: argument-hint frontmatter**

You create a `/migrate` skill that migrates database schemas. It requires a target version number as input (e.g., `/migrate v2.3`). Developers frequently invoke it without the version argument, causing it to migrate to the latest version unexpectedly. How do you improve the UX?

A) Add `argument-hint: "target version (e.g., v2.3)"` to the SKILL.md frontmatter so developers are prompted for the required parameter when they invoke the skill without arguments.

B) Add validation logic in the skill's instructions that checks for the version argument and asks the developer to re-run with one if missing.

C) Default to the latest version but add a confirmation prompt in the skill asking "Migrate to latest (v2.5)? (y/n)".

D) Create separate skills for each version: `/migrate-v2.1`, `/migrate-v2.2`, `/migrate-v2.3`.

**Correct Answer: A**

**Explanation:** `argument-hint` prompts developers for required parameters when they invoke the skill without arguments, improving discoverability and preventing accidental operations. Option B handles the missing argument but requires an extra round-trip. Option C defaults to a potentially dangerous operation. Option D creates maintenance overhead — a new skill for every version.

---

### Q3.2.5 — Scenario: Code Generation with Claude Code
**Difficulty: Hard | Skill: Choosing between skills and CLAUDE.md**

Your team has two types of coding guidance: (1) universal standards that should always apply (e.g., "use TypeScript strict mode", "all functions must have return types"), and (2) task-specific workflows that developers invoke on demand (e.g., "generate API endpoint with full test coverage", "refactor class to use composition pattern"). Where should each type of guidance live?

A) Universal standards in CLAUDE.md (always-loaded, applied automatically to every interaction), and task-specific workflows as skills in `.claude/skills/` (invoked on demand when the developer needs that specific workflow).

B) Both in CLAUDE.md — universal standards at the top and workflows described under headers.

C) Both as skills — universal standards as an auto-loading skill and workflows as invokable skills.

D) Universal standards in `.claude/rules/` and workflows in CLAUDE.md with instructions on how to invoke them.

**Correct Answer: A**

**Explanation:** This directly maps to the design intent of each mechanism. CLAUDE.md is always-loaded — perfect for universal standards that should apply to every interaction. Skills are on-demand — perfect for task-specific workflows that only apply when explicitly invoked. Option B bloats CLAUDE.md with task-specific content that wastes tokens in every interaction. Option C doesn't have an "auto-loading skill" mechanism for universal standards. Option D puts workflows in the wrong location — CLAUDE.md is for instructions, not invocable procedures.

---

### Q3.2.6 — Scenario: Code Generation with Claude Code
**Difficulty: Medium | Skill: Personal skill customization**

A team member wants a variant of the shared `/review` command that also checks for accessibility compliance — something only their front-end team cares about. They don't want to modify the shared command and affect other teams. What's the correct approach?

A) Create a personal variant in `~/.claude/commands/` (e.g., `review-a11y.md`) with the accessibility additions — personal commands don't affect teammates and can coexist with the shared project command.

B) Fork the shared `/review` command in `.claude/commands/` and add the accessibility checks, committing the change.

C) Add accessibility instructions to their `~/.claude/CLAUDE.md` so all reviews automatically include those checks.

D) Create a separate skill in `.claude/skills/` called `a11y-review` with the accessibility-specific checks.

**Correct Answer: A**

**Explanation:** Personal commands in `~/.claude/commands/` are user-scoped and don't affect other developers. This lets the front-end developer have their own variant without modifying the shared team command. Option B modifies the shared command, affecting all teams. Option C applies accessibility checks to all interactions, not just reviews. Option D creates a project-scoped skill visible to everyone when only one developer needs it.

---

## Task Statement 3.3: Apply path-specific rules for conditional convention loading

### Q3.3.1 — Scenario: Code Generation with Claude Code
**Difficulty: Easy | Knowledge: .claude/rules/ with YAML frontmatter path scoping**

Your project has Terraform infrastructure files in `terraform/` and Python application code in `src/`. You want Claude to follow specific Terraform naming conventions (e.g., resource naming patterns, variable formatting) only when editing Terraform files. How do you configure this?

A) Create a file in `.claude/rules/` with YAML frontmatter specifying `paths: ["terraform/**/*"]` so the Terraform rules load only when editing files matching that glob pattern.

B) Create a `terraform/CLAUDE.md` file with the Terraform conventions.

C) Add the Terraform conventions to the root CLAUDE.md under a "## Terraform" header.

D) Create a skill specifically for Terraform work that includes the conventions in its SKILL.md.

**Correct Answer: A**

**Explanation:** `.claude/rules/` files with YAML frontmatter `paths` fields enable conditional rule activation based on glob patterns. When the developer edits any file matching `terraform/**/*`, the rules load automatically. Option B (directory CLAUDE.md) also works for this specific case since all Terraform files are in one directory, but path-specific rules are more flexible and the recommended approach. Option C loads rules globally, wasting tokens on non-Terraform files. Option D requires manual invocation.

---

### Q3.3.2 — Scenario: Code Generation with Claude Code
**Difficulty: Medium | Knowledge: Glob patterns for cross-directory conventions**

Your codebase has test files co-located with source code throughout the project (e.g., `src/auth/Login.test.tsx`, `src/api/users.test.ts`, `lib/utils.test.js`). You want all test files to follow the same testing conventions regardless of their directory location. Which approach is most effective?

A) Create a `.claude/rules/testing.md` file with YAML frontmatter `paths: ["**/*.test.tsx", "**/*.test.ts", "**/*.test.js"]` to apply testing conventions to all test files regardless of where they are in the codebase.

B) Create a `CLAUDE.md` in each directory that contains test files, copying the testing conventions into each one.

C) Put testing conventions in the root CLAUDE.md so they're always loaded.

D) Create a `tests/` directory and require all test files to be moved there, then use a directory-level CLAUDE.md.

**Correct Answer: A**

**Explanation:** Glob patterns in `.claude/rules/` path-specific rules can match files by type regardless of directory location. `**/*.test.tsx` matches any test file at any depth. This is the advantage over directory-level CLAUDE.md files — conventions that span many directories only need one rule file. Option B requires maintaining duplicate files across many directories. Option C loads testing rules for all files, wasting context on non-test files. Option D restructures the project to fit the tooling, which is backward.

---

### Q3.3.3 — Scenario: Code Generation with Claude Code
**Difficulty: Medium | Skill: Choosing path rules vs subdirectory CLAUDE.md**

Your project has three types of convention needs: (1) REST API response format rules that apply only to files in `src/api/`, (2) React component patterns that apply to all `.tsx` files across the codebase, and (3) SQL query style rules that apply to all `.sql` files. Which configuration approach handles all three correctly?

A) Use a directory-level CLAUDE.md for `src/api/` (since all API files are in one directory), and `.claude/rules/` files with glob patterns for React (`paths: ["**/*.tsx"]`) and SQL (`paths: ["**/*.sql"]`) conventions (since these file types span multiple directories).

B) Use `.claude/rules/` files with glob patterns for all three — `paths: ["src/api/**/*"]`, `paths: ["**/*.tsx"]`, and `paths: ["**/*.sql"]`.

C) Put all three sets of conventions in the root CLAUDE.md and let Claude determine which apply based on the file being edited.

D) Create separate skills for each convention set and instruct developers to invoke the relevant skill before editing.

**Correct Answer: B**

**Explanation:** While Option A is functionally valid, Option B is more consistent and maintainable — using the same mechanism (`.claude/rules/` with glob patterns) for all three convention types keeps the configuration approach uniform. The glob pattern `src/api/**/*` achieves the same scoping as a directory CLAUDE.md but lives alongside the other rules in `.claude/rules/`. Option C loads everything globally. Option D requires manual invocation.

---

### Q3.3.4 — Scenario: Code Generation with Claude Code
**Difficulty: Hard | Skill: Path-scoped rules reducing token usage**

Your project has 12 different convention files totaling 3,000 tokens. On average, a developer edits files that match only 2-3 of these rule files. A colleague argues that loading all rules is fine because 3,000 tokens is small compared to the context window. Why might path-scoped conditional loading still be valuable?

A) Path-scoped rules load only when editing matching files, reducing irrelevant context — even at 3,000 tokens, irrelevant rules add noise that can confuse the model by presenting conflicting or inapplicable guidance, not just consume tokens.

B) Path-scoped loading is only valuable when the total rules exceed 10,000 tokens. At 3,000 tokens, loading everything is acceptable.

C) Path-scoped loading primarily benefits CI/CD pipelines where context windows are smaller.

D) Path-scoped loading is mainly about developer experience — it prevents developers from seeing irrelevant rules in the `/memory` output.

**Correct Answer: A**

**Explanation:** The benefit of path-scoped loading isn't purely about token count — it's about context relevance. Even 3,000 tokens of rules can cause issues if they contain conflicting guidance. For example, API response format rules might conflict with data model conventions, or React component patterns might mislead the model when editing a backend file. Reducing irrelevant context improves response quality regardless of token count. Option B sets an arbitrary threshold. Option C limits the benefit to one use case. Option D focuses on developer UX rather than model behavior.

---

## Task Statement 3.4: Determine when to use plan mode vs direct execution

### Q3.4.1 — Scenario: Code Generation with Claude Code
**Difficulty: Easy | Knowledge: When plan mode is appropriate**

A developer needs to restructure a monolithic Express.js application into microservices. This involves decisions about service boundaries, API contracts, shared library extraction, and changes to 45+ files. Which execution mode should they use?

A) Plan mode — this task involves large-scale changes, multiple valid approaches, and architectural decisions that require codebase exploration and design before committing to changes.

B) Direct execution — let Claude start making changes and discover the right approach incrementally.

C) Direct execution with a comprehensive upfront specification detailing exactly how each service should be structured.

D) Plan mode for the first 5 files, then switch to direct execution for the remaining 40.

**Correct Answer: A**

**Explanation:** Plan mode is designed for complex tasks involving large-scale changes, multiple valid approaches, and architectural decisions. A monolith-to-microservices restructuring requires exploring the codebase, understanding dependencies, evaluating approaches, and designing before implementing. Option B risks costly rework when dependencies are discovered late. Option C assumes the developer already knows the right structure. Option D arbitrarily splits the process.

---

### Q3.4.2 — Scenario: Code Generation with Claude Code
**Difficulty: Easy | Knowledge: When direct execution is appropriate**

A developer has a clear bug report with a stack trace pointing to a null check missing in `UserService.validateEmail()` on line 47. The fix is adding a single conditional check. Which execution mode should they use?

A) Direct execution — this is a well-understood, well-scoped change (single-file bug fix with a clear stack trace) that doesn't require exploration or planning.

B) Plan mode — all bug fixes benefit from exploring the codebase to understand the broader context before making changes.

C) Plan mode — the developer should verify the fix doesn't break other tests before implementing it.

D) Direct execution, but only after manually reading the file to confirm the fix is straightforward.

**Correct Answer: A**

**Explanation:** Direct execution is appropriate for simple, well-scoped changes with clear intent. A single null check addition in a known location with a clear stack trace requires no exploration or architectural decisions. Option B over-engineers a trivial fix. Option C conflates planning with testing — tests should run after implementation regardless of mode. Option D adds an unnecessary manual step for a clear fix.

---

### Q3.4.3 — Scenario: Code Generation with Claude Code
**Difficulty: Medium | Knowledge: Explore subagent for verbose discovery**

During a plan mode investigation of a complex library migration affecting 30+ files, the exploration phase generates extensive output — reading dozens of files, tracing import chains, cataloging all usage sites. This verbose discovery output is consuming most of the context window, leaving insufficient room for the actual planning and implementation phases. What approach addresses this?

A) Use the Explore subagent to isolate verbose discovery output — it runs exploration in an isolated context and returns summaries to the main agent, preserving context space for planning and implementation.

B) Use `/compact` frequently during exploration to free up context space.

C) Split the exploration into multiple separate sessions, manually summarizing findings between sessions.

D) Limit exploration to reading file headers and comments, skipping full file contents.

**Correct Answer: A**

**Explanation:** The Explore subagent specifically addresses context exhaustion during verbose discovery. It runs exploration in an isolated context, processes all the detailed file contents and dependency tracing, and returns a condensed summary to the main agent. This preserves the main context for planning and implementation. Option B discards context that may contain valuable earlier analysis. Option C breaks the workflow and relies on manual summarization. Option D provides incomplete information that leads to poor plans.

---

### Q3.4.4 — Scenario: Code Generation with Claude Code
**Difficulty: Medium | Skill: Combining plan mode and direct execution**

You need to migrate from REST to GraphQL for your project's API layer. You've never used GraphQL before and aren't sure about the best schema design approach. After planning, the migration strategy is clear and the first 5 endpoints have straightforward conversion paths. What execution approach fits this workflow?

A) Start in plan mode for investigation — explore the codebase, evaluate GraphQL schema approaches, and design the migration strategy. Then switch to direct execution for implementing the planned approach on the well-understood endpoints.

B) Stay in plan mode for the entire migration to maintain a coherent strategy throughout.

C) Start with direct execution on a simple endpoint to learn GraphQL patterns, then use plan mode for the complex ones.

D) Use plan mode to generate a complete implementation plan for all endpoints, then execute the entire plan in one direct execution session.

**Correct Answer: A**

**Explanation:** This combines plan mode and direct execution according to their strengths. Plan mode handles the investigation and design phase (exploring approaches, evaluating tradeoffs in an unfamiliar domain). Once the approach is established and specific implementations are well-understood, direct execution handles the straightforward implementation efficiently. Option B wastes planning overhead on clear implementations. Option C risks learning bad patterns from trial-and-error. Option D generates a plan too large for one execution session to handle coherently.

---

### Q3.4.5 — Scenario: Developer Productivity with Claude
**Difficulty: Hard | Skill: Selecting mode based on complexity signals**

For each task below, identify the most appropriate execution mode:

**Task X:** Add input validation to a form component — the validation rules are specified in the Jira ticket with exact regex patterns.
**Task Y:** Investigate why the checkout flow has a 5% failure rate — logs show intermittent errors across three services.
**Task Z:** Upgrade React from v17 to v18 — known breaking changes in lifecycle methods affect an unknown number of components.

Which assignment is correct?

A) X: Direct execution (well-scoped, clear specification). Y: Plan mode (open-ended investigation, multiple services, unknown root cause). Z: Plan mode (large-scale change, unknown scope of impact, architectural implications of lifecycle changes).

B) X: Direct execution. Y: Direct execution (just trace the logs). Z: Plan mode.

C) X: Plan mode (should verify validation doesn't conflict with existing logic). Y: Plan mode. Z: Direct execution (just follow the official migration guide).

D) All three: Plan mode (it's always safer to plan first).

**Correct Answer: A**

**Explanation:** The key signal is complexity and uncertainty. Task X has clear scope and specifications — direct execution. Task Y is open-ended investigation across multiple services with unknown root cause — needs exploration and hypothesis testing via plan mode. Task Z has known breaking changes but unknown scope — needs codebase exploration to assess impact before implementing. Option B oversimplifies Task Y. Option C over-engineers Task X and under-engineers Task Z. Option D ignores that plan mode adds overhead to tasks that don't need it.

---

## Task Statement 3.5: Apply iterative refinement techniques for progressive improvement

### Q3.5.1 — Scenario: Code Generation with Claude Code
**Difficulty: Medium | Knowledge: Concrete examples over prose descriptions**

You ask Claude to "format all dates consistently" in a data processing script. Claude converts some dates to ISO format, others to Unix timestamps, and leaves some unchanged. What's the most effective way to clarify your requirement?

A) Provide 2-3 concrete input/output examples showing the exact transformation expected — for instance, `"March 15, 2024" → "2024-03-15"`, `"03/15/24" → "2024-03-15"`, `"1710460800" → "2024-03-15"` — so the model can infer the consistent output pattern.

B) Rewrite the instruction with more detail: "Convert all date formats to ISO 8601 format (YYYY-MM-DD), including Unix timestamps, American date formats, and written-out dates."

C) Add a reference to the ISO 8601 specification in the prompt.

D) Let Claude choose a format and then manually correct the output, repeating until it converges.

**Correct Answer: A**

**Explanation:** Concrete input/output examples are the most effective technique when prose descriptions produce inconsistent results. The examples unambiguously show the expected transformation for each input variant, leaving no room for misinterpretation. Option B is more detailed but still requires the model to interpret "ISO 8601" and handle edge cases it might not anticipate. Option C assumes the model can derive implementation from a specification reference. Option D is inefficient trial-and-error.

---

### Q3.5.2 — Scenario: Code Generation with Claude Code
**Difficulty: Medium | Knowledge: Test-driven iteration**

You're building a CSV parser. Rather than describing all edge cases in prose, you want Claude to progressively improve its implementation. What's the most effective iterative approach?

A) Write a test suite first covering expected behavior (standard CSV parsing), edge cases (quoted fields, escaped commas, newlines within quotes), and performance requirements (10,000 rows in <1 second). Then share test failures with Claude iteratively to guide progressive improvement.

B) Describe all edge cases in the initial prompt and ask Claude to handle them all at once.

C) Start with the basic implementation, then manually test each edge case and report failures one at a time.

D) Provide a CSV specification document and ask Claude to implement a fully compliant parser.

**Correct Answer: A**

**Explanation:** Test-driven iteration is the recommended pattern: write comprehensive tests first, then iterate by sharing test failures. This gives Claude concrete, unambiguous feedback about what's wrong and what's expected, enabling progressive improvement. Option B tries to handle everything upfront, which often leads to missed edge cases. Option C is manual and slow. Option D relies on the model to derive implementation from a specification, which is less reliable than test-driven refinement.

---

### Q3.5.3 — Scenario: Developer Productivity with Claude
**Difficulty: Medium | Knowledge: The interview pattern**

You need Claude to implement a caching layer for your application but you're not sure about the best approach — you haven't considered cache invalidation strategies, TTL policies, or failure modes. What technique helps surface these considerations before implementation?

A) Use the interview pattern: have Claude ask questions about your requirements to surface design considerations you may not have anticipated (cache invalidation strategy, TTL policies, failure behavior, memory limits) before implementing the solution.

B) Describe your basic needs and let Claude choose the best caching approach based on its training knowledge.

C) Research caching strategies independently, then provide Claude with a complete specification to implement.

D) Ask Claude to implement the simplest possible caching solution, then iteratively add features as needs arise.

**Correct Answer: A**

**Explanation:** The interview pattern leverages Claude's knowledge to surface considerations the developer may not have anticipated. By asking questions before implementing, it can identify important design decisions (invalidation strategy, TTL, failure modes) that affect architecture. This prevents costly rework from discovering these issues mid-implementation. Option B gives the model no basis for choosing appropriately. Option C puts the full research burden on the developer when Claude can help. Option D may require architectural rework when later features conflict with initial simple choices.

---

### Q3.5.4 — Scenario: Code Generation with Claude Code
**Difficulty: Hard | Skill: Sequential vs parallel issue resolution**

Claude generates a database migration script that has three issues: (1) a null handling bug that causes data loss on rows with empty fields, (2) incorrect column type mapping that truncates decimal values, and (3) missing index creation that will cause slow queries. Issues 1 and 2 interact — fixing the null handling changes which rows are affected by the type mapping. Issue 3 is independent. How should you communicate these to Claude?

A) Provide issues 1 and 2 together in a single detailed message (since their fixes interact), including specific test cases with example input and expected output. After those are resolved, address issue 3 separately.

B) Provide all three issues in a single message so Claude has the complete picture.

C) Fix them one at a time in sequence: first issue 1, then issue 2, then issue 3.

D) Let Claude re-analyze the entire script from scratch rather than pointing out specific issues.

**Correct Answer: A**

**Explanation:** When fixes interact, they should be addressed together in a single message so Claude can reason about how they affect each other. Issues 1 and 2 interact (null handling affects type mapping), so combining them prevents contradictory fixes. Issue 3 is independent and can be addressed separately without risk of interference. Option B includes an unrelated issue that may distract from the interacting pair. Option C risks fixing issue 1 in a way that makes issue 2 harder. Option D discards the developer's diagnosis.

---

### Q3.5.5 — Scenario: Structured Data Extraction
**Difficulty: Medium | Skill: Providing specific test cases for edge cases**

Your extraction pipeline handles address fields inconsistently. Sometimes it extracts "123 Main St, Suite 200" as one field; other times it splits it into separate street and suite fields. Prose instructions ("extract the full address including suite number") haven't resolved the inconsistency. What's the best fix?

A) Provide specific test cases with example input documents and expected output — for instance, showing that "123 Main St, Suite 200, New York, NY 10001" should always extract as `{street: "123 Main St, Suite 200", city: "New York", state: "NY", zip: "10001"}`.

B) Add a schema constraint requiring the street field to always include suite information.

C) Post-process the extraction to merge separate street and suite fields programmatically.

D) Add "IMPORTANT: Never split the suite number from the street address" to the system prompt in capital letters.

**Correct Answer: A**

**Explanation:** Concrete input/output examples clarify ambiguous extraction rules that prose descriptions fail to resolve. By showing the exact expected output for representative inputs, the model learns the boundary between street and city parsing. Option B constrains the schema but doesn't show the model how to populate it correctly. Option C handles the symptom in post-processing. Option D relies on emphasis rather than examples, which is less effective for ambiguous parsing decisions.

---

## Task Statement 3.6: Integrate Claude Code into CI/CD pipelines

### Q3.6.1 — Scenario: Claude Code for Continuous Integration
**Difficulty: Easy | Knowledge: -p flag for non-interactive mode**

Your CI/CD pipeline script includes: `claude "Review this PR for security issues"`. The job hangs indefinitely. What's wrong?

A) The `-p` flag is missing — without it, Claude Code waits for interactive input. The correct command is `claude -p "Review this PR for security issues"`.

B) The command needs the `--ci` flag to enable CI mode.

C) The `CLAUDE_HEADLESS=true` environment variable must be set.

D) The command should use `claude --batch` for automated execution.

**Correct Answer: A**

**Explanation:** The `-p` (or `--print`) flag is the documented way to run Claude Code in non-interactive mode. It processes the prompt, outputs results to stdout, and exits without waiting for user input. Options B, C, and D reference features that don't exist — `--ci`, `CLAUDE_HEADLESS`, and `--batch` are not Claude Code CLI options.

---

### Q3.6.2 — Scenario: Claude Code for Continuous Integration
**Difficulty: Medium | Knowledge: Structured output for CI**

Your CI pipeline needs to post Claude's review findings as inline comments on specific lines of a pull request. Currently, Claude returns prose paragraphs that are difficult to parse. How should you get machine-parseable output?

A) Use `--output-format json` with `--json-schema` to produce structured findings that include file paths, line numbers, severity, and descriptions — this output can be parsed and posted as inline PR comments automatically.

B) Add instructions to the prompt asking Claude to format its response as JSON.

C) Parse Claude's prose output with regex to extract file names and line numbers.

D) Post Claude's full prose response as a single PR comment and let developers read through it.

**Correct Answer: A**

**Explanation:** `--output-format json` with `--json-schema` are CLI flags that enforce structured output in CI contexts. By defining a JSON schema that includes file paths, line numbers, severity, and descriptions, the pipeline gets machine-parseable data that can be mapped to inline PR comments. Option B uses prompt-based enforcement, which may produce malformed JSON. Option C is fragile regex parsing of unstructured text. Option D is functional but not actionable — inline comments are far more useful for developers.

---

### Q3.6.3 — Scenario: Claude Code for Continuous Integration
**Difficulty: Medium | Skill: Avoiding duplicate review comments**

Your CI pipeline runs Claude Code reviews on every new commit to a PR. After 3 commits, the PR has accumulated duplicate comments — Claude flags the same issues that were already flagged on previous commits and haven't been addressed yet. How do you prevent this?

A) Include prior review findings in context when re-running reviews after new commits, and instruct Claude to report only new issues or issues that remain unaddressed but haven't been previously flagged.

B) Clear all previous review comments before each new review run.

C) Only run reviews on the final commit before merge, not on intermediate commits.

D) Deduplicate comments in post-processing by comparing new findings against previously posted comments.

**Correct Answer: A**

**Explanation:** Providing prior review findings in context lets Claude know what's already been reported. Instructions to focus on new or unaddressed issues prevent duplicate comments while still catching new problems introduced in the latest commit. Option B removes valuable historical feedback. Option C delays feedback, reducing its value to developers. Option D is fragile — textual comparison of findings may miss near-duplicates or misclassify distinct issues.

---

### Q3.6.4 — Scenario: Claude Code for Continuous Integration
**Difficulty: Medium | Knowledge: Session context isolation for review quality**

Your pipeline uses Claude Code to generate code and then immediately reviews its own output in the same session. A colleague suggests splitting generation and review into separate sessions. Why might this improve review quality?

A) The same session retains reasoning context from generation, making the model less likely to question its own decisions — an independent review session without the generator's reasoning context is more effective at catching issues.

B) Separate sessions allow different model versions for generation and review.

C) The generation session's tool results consume context space that reduces review quality.

D) CI pipelines require separate sessions for billing purposes.

**Correct Answer: A**

**Explanation:** Session context isolation is important for review effectiveness. When the same session generates and reviews code, the model retains its reasoning from the generation phase — it's anchored to its own decisions and less likely to identify problems. A separate review session starts fresh, giving the reviewer an independent perspective. Option B is an incidental benefit, not the primary reason. Option C may contribute but isn't the fundamental issue. Option D is not a real constraint.

---

### Q3.6.5 — Scenario: Claude Code for Continuous Integration
**Difficulty: Hard | Skill: CLAUDE.md for CI test generation quality**

Your CI pipeline uses Claude Code to generate test cases for new code. The generated tests are often low-value (testing trivial getters/setters, duplicating existing test scenarios, using hardcoded values instead of available fixtures). How do you improve test generation quality?

A) Document testing standards, valuable test criteria (e.g., "test business logic and edge cases, not trivial accessors"), available fixtures and test utilities, and existing test patterns in CLAUDE.md — this provides project context that improves test generation quality and reduces low-value output.

B) Add a `--test-mode` flag to Claude Code that activates test-specific generation heuristics.

C) Post-process generated tests to filter out trivial ones using a test complexity analyzer.

D) Provide the entire test suite in context so Claude can see the testing patterns.

**Correct Answer: A**

**Explanation:** CLAUDE.md is the mechanism for providing project context to CI-invoked Claude Code. Documenting testing standards (what makes a valuable test), available fixtures (avoiding hardcoded values), and existing test patterns (preventing duplication) directly addresses each quality issue. Option B references a flag that doesn't exist. Option C handles symptoms in post-processing rather than improving generation quality. Option D may help with patterns but consumes massive context without the targeted guidance of explicit testing standards.

---

### Q3.6.6 — Scenario: Claude Code for Continuous Integration
**Difficulty: Medium | Skill: Providing existing tests to avoid duplication**

Your test generation pipeline sometimes produces test cases that duplicate scenarios already covered by your existing test suite. What context should you provide to prevent this?

A) Include existing test files in the context when running test generation, so Claude can see which scenarios are already covered and focus on generating tests for uncovered behavior.

B) Add a CLAUDE.md instruction: "Do not generate tests that duplicate existing test scenarios."

C) Run the generated tests alongside existing tests and remove any that test the same assertions.

D) Generate tests only for files that have zero existing test coverage.

**Correct Answer: A**

**Explanation:** Providing existing test files in context gives Claude direct visibility into what's already tested, enabling it to identify gaps and generate complementary test cases. Option B is a prompt instruction without the information needed to comply — Claude can't avoid duplication if it can't see existing tests. Option C handles duplication after generation, wasting compute. Option D is overly restrictive — even files with tests may have coverage gaps.

---

## Cross-Domain Questions (Domain 3 intersecting with other domains)

### Q3.X.1 — Scenario: Code Generation with Claude Code
**Difficulty: Hard | Domains: 3 (Claude Code Config) + 2 (Tool Design) + 5 (Context Management)**

Your team's Claude Code setup has these issues: (1) the Jira MCP server is configured in one developer's `~/.claude.json`, so other team members can't use Jira integration; (2) CLAUDE.md is 900 lines and developers report Claude misses instructions in the middle; (3) a new MCP code analysis tool has a one-word description ("Analyzes") and Claude never uses it, defaulting to Grep. Which combination of changes addresses all three?

A) Move the Jira MCP server to project-level `.mcp.json` with `${JIRA_TOKEN}` environment variable expansion. Split the 900-line CLAUDE.md into focused files in `.claude/rules/` with path-specific glob patterns. Enhance the code analysis tool description to detail its capabilities, inputs, and when to use it versus Grep.

B) Share the `~/.claude.json` file via the project repository. Add "IMPORTANT" markers to the 900-line CLAUDE.md. Add a system prompt instruction to prefer MCP tools over built-in tools.

C) Create a setup script that copies the Jira config to each developer's `~/.claude.json`. Restructure CLAUDE.md with numbered sections. Add `tool_choice` forcing the code analysis tool.

D) Document the Jira setup in the project README. Compress the CLAUDE.md into shorter instructions. Remove Grep so Claude uses the MCP tool.

**Correct Answer: A**

**Explanation:** Each fix targets the specific root cause. The Jira server belongs in project-level config (shared via version control) with environment variable expansion for credentials. The long CLAUDE.md should be split into topic-specific rule files with path-scoping to reduce irrelevant context. The MCP tool needs an enhanced description to compete with the well-understood built-in Grep. Option B shares personal config files (security risk), relies on emphasis markers, and uses prompt instructions. Option C uses scripts instead of proper scoping, and forces a tool regardless of context. Option D relies on documentation and removes useful tools.

---

### Q3.X.2 — Scenario: Claude Code for Continuous Integration
**Difficulty: Hard | Domains: 3 (Claude Code Config) + 4 (Prompt Engineering)**

Your CI review pipeline produces these issues: (1) false positives from flagging acceptable local patterns as problems, (2) inconsistent severity ratings for similar issues across different PRs, and (3) missed security vulnerabilities while flagging minor style issues. What combination of configuration and prompt engineering improvements addresses all three?

A) Add few-shot examples to CLAUDE.md showing acceptable local patterns to reduce false positives. Define explicit severity criteria with concrete code examples for each level in `.claude/rules/review-criteria.md`. Create review instructions that specify which issue categories to prioritize (security, correctness) versus skip (minor style) rather than relying on general "be conservative" guidance.

B) Train a custom classifier to filter out false positives post-review. Add a severity field to the JSON schema output. Use a security-focused model variant for the security pass.

C) Add "CRITICAL: Reduce false positives. Be consistent. Focus on security." to CLAUDE.md. Use `--output-format json` to standardize output format.

D) Disable categories that produce false positives. Force all severity ratings to "medium" for consistency. Run a separate security-only review pass with different tools.

**Correct Answer: A**

**Explanation:** Each issue maps to a specific prompt engineering and configuration technique. False positives need few-shot examples showing what acceptable patterns look like (so Claude can generalize). Severity inconsistency needs explicit criteria with concrete code examples (not vague levels). Missed security issues need categorical prioritization (what to focus on and what to skip) rather than general guidance like "be conservative." Option B adds infrastructure when configuration improvements haven't been tried. Option C uses vague instructions that don't improve precision. Option D disables capabilities and loses nuance.

---

*End of Domain 3 Question Bank*
*Total: 34 questions covering all 6 task statements + 2 cross-domain questions*
*Distribution: 7 Easy, 19 Medium, 8 Hard*