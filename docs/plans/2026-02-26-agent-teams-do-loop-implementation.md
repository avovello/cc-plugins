# Agent Teams Do-Loop Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Introduce agent teams into bugfix and feature-development plugins using a shared do-loop pattern.

**Architecture:** A shared plugin provides the reusable do-loop skill (iteration protocol for doer+critic teammate pairs). Bugfix and feature-development commands are rewritten to spawn teams instead of dispatching subagents for fix/implement/review phases. Investigation phases (reproducer, root-cause-analyst, Explore agents) stay as subagents.

**Tech Stack:** Claude Code Agent Teams (experimental), markdown commands/skills/agents with YAML frontmatter.

---

### Task 1: Create shared plugin skeleton

**Files:**
- Create: `plugins/shared/.claude-plugin/plugin.json`

**Step 1: Create directory structure**

Run: `mkdir -p plugins/shared/.claude-plugin plugins/shared/skills`

**Step 2: Write plugin.json**

Create `plugins/shared/.claude-plugin/plugin.json`:

```json
{
  "name": "shared",
  "description": "Reusable agent team infrastructure — do-loop iteration protocol for doer+critic teammate pairs",
  "version": "1.0.0",
  "author": {
    "name": "Claude Code Marketplace Contributors",
    "email": "noreply@anthropic.com"
  },
  "keywords": ["agent-teams", "do-loop", "shared", "infrastructure"]
}
```

**Step 3: Verify**

Run: `cat plugins/shared/.claude-plugin/plugin.json | python3 -m json.tool`
Expected: valid JSON output

**Step 4: Commit**

```bash
git add plugins/shared/.claude-plugin/plugin.json
git commit -m "feat(shared): create shared plugin skeleton"
```

---

### Task 2: Write the do-loop skill

**Files:**
- Create: `plugins/shared/skills/do-loop.md`

**Context:** This is the core reusable piece. It gets loaded into the main session (team lead) and guides any doer+critic loop. It does NOT define specific roles — commands do that. It defines the communication and iteration protocol.

**Step 1: Write the skill**

Create `plugins/shared/skills/do-loop.md` with YAML frontmatter:

```yaml
---
name: do-loop
description: Iteration protocol for agent team do-loops — guides how doer+critic teammate pairs communicate, iterate, and resolve disagreements
---
```

Body must cover these sections (use second person, addressing the team lead):

**1. Team Spawning Protocol**
- Describe each teammate with: role name, responsibility (one sentence), model (opus for doers, opus for critics), and full context to pass (requirements, analysis results, codebase conventions)
- Always spawn exactly 2 teammates per loop (one doer, one critic)
- Pass the do-loop protocol description to the critic teammate so it knows the rules

**2. Task Structure**
- Create one task per loop with clear acceptance criteria
- Task title format: `[verb] [component] — [acceptance criteria summary]`
- Include in task description: what "done" means, what files are in scope, what quality bar applies
- Doer claims the task, critic reviews when doer messages "ready for review"

**3. Communication Protocol**
- Critic messages doer with structured feedback: `file:line — issue description — suggested fix`
- Doer messages critic: "Addressed feedback, ready for re-review" with summary of changes
- No broadcast — only direct messages between the pair
- No meta-discussion about process — keep messages focused on the work

**4. Iteration Rules**
- Max 3 review rounds per loop
- Round 1: full review, all issues
- Round 2: verify fixes, catch new issues from fixes only
- Round 3: final check, nit-level only
- After round 3: if issues remain, escalate to user (team lead) with unresolved list

**5. Exit Conditions**
- Critic marks task complete when satisfied (no remaining issues above nit-level)
- If doer and critic deadlock (disagree on same issue for 2 consecutive rounds): escalate to user with both perspectives
- User can override: accept as-is, side with doer, or side with critic

**6. Composability**
- Commands can chain multiple do-loops sequentially
- Output of one loop becomes input context for the next
- Between loops: team lead (main session) summarizes results and spawns new teammate pair
- Previous teammates are done after their loop — don't reuse across loops

**Step 2: Verify frontmatter**

Run: `head -5 plugins/shared/skills/do-loop.md`
Expected: valid YAML frontmatter with `name: do-loop` and `description:`

**Step 3: Commit**

```bash
git add plugins/shared/skills/do-loop.md
git commit -m "feat(shared): add do-loop skill — iteration protocol for agent team pairs"
```

---

### Task 3: Rewrite bugfix command

**Files:**
- Modify: `plugins/bugfix/commands/bugfix.md`

**Context:** Hybrid workflow — subagents for investigation (reproducer, root-cause-analyst), then agent team for fix+review. The existing agents are unchanged. Reference the do-loop skill.

**Step 1: Rewrite the command**

Rewrite `plugins/bugfix/commands/bugfix.md` with this structure:

Frontmatter:
```yaml
---
description: Systematic bug fixing with reproduction, root cause analysis, and team-based fix+review
allowed-tools: Task, Bash
---
```

Body sections:

**Usage** — same as current (`/bugfix "description"`)

**Phase 1: Investigation (subagents)** — sequential, same as current:
1. Dispatch `bugfix:reproducer` with bug description → reproduction report
2. Dispatch `bugfix:root-cause-analyst` with reproduction report → root cause + fix proposal
3. Present findings to user. If user confirms, proceed. If not, stop.

**Phase 2: Fix do-loop (agent team)**

Load the `do-loop` skill for iteration protocol. Spawn 2 teammates:

| Teammate | Role | Model | Context |
|----------|------|-------|---------|
| fixer | Implement the fix per the root cause analysis. Minimal changes, match existing code style. | opus | Reproduction report + root cause analysis + fix proposal + relevant file paths |
| reviewer | Review the fix for correctness, edge cases, regressions, and code style. | opus | Same context + do-loop iteration protocol |

Iterate per do-loop protocol (max 3 rounds).

**Phase 3: Regression test do-loop (agent team)**

Spawn a fresh doer+critic pair per do-loop composability rules. Load `regression-test` skill.

| Teammate | Role | Model | Context |
|----------|------|-------|---------|
| fixer | Write regression test for the fixed bug. Cover exact scenario + edge cases + related patterns. | opus | Fix changes + regression-test skill guidance |
| reviewer | Review test quality: naming, coverage, assertions, placement. | opus | Do-loop protocol |

One mini-loop (1-2 rounds).

**Phase 4: Verify and summarize**

Main session (lead) runs tests to verify. Summarizes all changes. User decides whether to commit.

**Agents table** — reproducer, root-cause-analyst (subagents, pre-team)

**Skills table** — do-loop (iteration protocol), regression-test (test guidance)

**Step 2: Verify frontmatter**

Run: `head -5 plugins/bugfix/commands/bugfix.md`
Expected: valid YAML with `allowed-tools: Task, Bash`

**Step 3: Commit**

```bash
git add plugins/bugfix/commands/bugfix.md
git commit -m "feat(bugfix): rewrite command for team-based fix+review do-loops"
```

---

### Task 4: Rewrite feature-development command

**Files:**
- Modify: `plugins/feature-development/commands/feature.md`

**Context:** Phases 1-3 stay mostly the same (discovery, exploration via Explore subagents, clarifying questions). Phases 4-6 become three sequential do-loops. The code-architect agent stays as a reference for the architect teammate's role. The testing-loop and review-loop skills provide domain context for test and review teammates.

**Step 1: Rewrite the command**

Rewrite `plugins/feature-development/commands/feature.md` with this structure:

Frontmatter:
```yaml
---
description: Systematic feature development with codebase exploration, architecture design, and team-based implementation loops
allowed-tools: Task, Bash
---
```

Body sections:

**Usage** — same as current

**Phase 1: Discovery** — same as current (command-level, gather requirements, confirm)

**Phase 2: Codebase Exploration** — same as current (parallel Explore subagents)

**Phase 3: Clarifying Questions** — same as current (load `clarifying-questions` skill, hard stop)

**Phase 4: Architecture do-loop (agent team)**

Load do-loop skill. Spawn 2 teammates:

| Teammate | Role | Model | Context |
|----------|------|-------|---------|
| architect | Design architecture applying SOLID, Clean Architecture, Design Patterns, DDD principles. Produce an implementation blueprint with specific files, interfaces, data flow. Follow existing codebase patterns exactly. | opus | Requirements + exploration results + solid-principles, clean-architecture, design-patterns, domain-driven-design skills |
| arch-reviewer | Review architecture for feasibility, principle compliance, gaps, over-engineering. Challenge assumptions. | opus | Same context + do-loop protocol |

Iterate per do-loop protocol.

**PAUSE**: Present approved blueprint to user. Do NOT proceed until user explicitly approves.

**Phase 5: Implementation do-loop (agent team)**

Spawn 2 teammates:

| Teammate | Role | Model | Context |
|----------|------|-------|---------|
| implementer | Write code per approved blueprint. Follow codebase conventions from Phase 2. Implement in dependency order: data models → services → APIs → UI. | opus | Approved blueprint + codebase conventions + exploration results |
| code-reviewer | Review implementation for bugs, security, architecture violations, edge cases. Reference blueprint as source of truth. Load review-loop skill for quality criteria. | opus | Blueprint + do-loop protocol + review-loop skill |

Iterate per do-loop protocol.

**Phase 6: Test do-loop (agent team)**

Spawn 2 teammates:

| Teammate | Role | Model | Context |
|----------|------|-------|---------|
| test-writer | Write unit, integration, and E2E tests for the implementation. Follow existing test patterns. Load testing-loop skill for failure analysis. Target 80%+ coverage. | opus | Implementation changes + blueprint + testing-loop skill |
| test-reviewer | Review test quality: coverage, assertions, edge cases, naming, mocking strategy. | opus | Do-loop protocol |

Iterate per do-loop protocol.

**Phase 7: Summary**

Main session summarizes: components built, key decisions, files changed, next steps.

**Skills table**: do-loop, clarifying-questions, solid-principles, clean-architecture, design-patterns, domain-driven-design, testing-loop, review-loop

**Step 2: Verify frontmatter**

Run: `head -5 plugins/feature-development/commands/feature.md`
Expected: valid YAML with `allowed-tools: Task, Bash`

**Step 3: Commit**

```bash
git add plugins/feature-development/commands/feature.md
git commit -m "feat(feature-dev): rewrite command for team-based architecture/implementation/test do-loops"
```

---

### Task 5: Update bugfix plugin metadata and README

**Files:**
- Modify: `plugins/bugfix/.claude-plugin/plugin.json`
- Modify: `plugins/bugfix/README.md`

**Step 1: Update plugin.json**

Change version to `4.0.0` and description to: "Systematic bug fixing with reproduction, root cause analysis, and team-based fix+review loops"

**Step 2: Rewrite README.md**

Update to reflect:
- Team-based workflow (not subagent pipeline)
- Hybrid approach: subagents for investigation, teams for fix+review
- 2 agents (reproducer, root-cause-analyst) — used in pre-team phase
- 2 skills referenced (do-loop from shared plugin, regression-test)
- Workflow diagram: Investigate (subagents) → Fix+Review (team loop) → Regression Test (team loop) → Verify
- Design decisions: why hybrid, why teams for fix-review, dependency on shared plugin
- Requires: `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`
- Version 4.0.0

**Step 3: Commit**

```bash
git add plugins/bugfix/.claude-plugin/plugin.json plugins/bugfix/README.md
git commit -m "docs(bugfix): update metadata and README for team-based workflow v4.0.0"
```

---

### Task 6: Update feature-development plugin metadata and README

**Files:**
- Modify: `plugins/feature-development/.claude-plugin/plugin.json`
- Modify: `plugins/feature-development/README.md`

**Step 1: Update plugin.json**

Change version to `3.0.0` and description to reflect team-based architecture/implementation/test loops.

**Step 2: Rewrite README.md**

Update to reflect:
- Team-based workflow with 3 sequential do-loops
- Phases 1-3 unchanged (discovery, exploration, clarifying questions)
- Phase 4: architecture do-loop (architect + arch-reviewer)
- Phase 5: implementation do-loop (implementer + code-reviewer)
- Phase 6: test do-loop (test-writer + test-reviewer)
- 1 agent (code-architect) — role reference for architect teammate
- 7 skills (existing) + do-loop from shared plugin
- Dependency on shared plugin
- Requires: `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`
- Version 3.0.0

**Step 3: Commit**

```bash
git add plugins/feature-development/.claude-plugin/plugin.json plugins/feature-development/README.md
git commit -m "docs(feature-dev): update metadata and README for team-based workflow v3.0.0"
```

---

### Task 7: Update root sync files

**Files:**
- Modify: `.claude-plugin/marketplace.json`
- Modify: `CLAUDE.md`
- Modify: `README.md`
- Modify: `QUICKSTART.md`

**Step 1: Update marketplace.json**

- Add `shared` plugin entry (version 1.0.0, category "infrastructure")
- Update `bugfix` entry (version 4.0.0, new description)
- Update `feature-development` entry (version 3.0.0, new description)
- Bump root version to `3.0.0`

**Step 2: Update CLAUDE.md**

- Change counts: 5 plugins, 13 agents, 8 commands, 12 skills
- Update project structure tree: add `shared/` with description
- Update bugfix line: `bugfix/ # 2 agents: reproducer, root-cause-analyst + 1 skill + team do-loops`
- Add note about agent teams being experimental and requiring env var

**Step 3: Update README.md**

- Add shared plugin section (brief — infrastructure plugin, 1 skill)
- Update bugfix section: hybrid subagent+team workflow, do-loop skill reference
- Update feature-dev section: 3 sequential do-loops
- Update plugin count: 5 plugins, 13 agents, 12 skills
- Add "Prerequisites" note about `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`
- Bump version to 3.0.0

**Step 4: Update QUICKSTART.md**

- Update plugin count and descriptions
- Add shared plugin install command
- Update bugfix workflow description
- Update feature-dev workflow description
- Add prerequisites section for agent teams env var
- Update plugin table (5 rows now)

**Step 5: Verify counts are consistent**

Run: `grep -r "13 agents\|12 skills\|5 plugins" CLAUDE.md README.md QUICKSTART.md`
Expected: consistent counts across all three files

**Step 6: Commit**

```bash
git add .claude-plugin/marketplace.json CLAUDE.md README.md QUICKSTART.md
git commit -m "docs: update all sync files for agent teams v3.0.0 — 5 plugins, 12 skills"
```

---

### Task 8: Final verification

**Step 1: Verify plugin structure**

Run: `find plugins/ -name "plugin.json" | sort`
Expected: 5 plugin.json files (bugfix, feature-development, qa, review, shared)

**Step 2: Verify no broken references**

Run: `grep -r "fix-implementer" plugins/`
Expected: no matches (deleted agent should have no references)

**Step 3: Verify all skill references exist**

Run: `ls plugins/shared/skills/do-loop.md plugins/bugfix/skills/regression-test.md`
Expected: both files exist

**Step 4: Review git log**

Run: `git log --oneline -8`
Expected: 7 commits from this implementation plan

**Step 5: Commit (if any fixups needed)**

Only if previous steps revealed issues.
