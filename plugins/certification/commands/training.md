---
description: Interactive study of Claude Certified Architect – Foundations exam domains. Teaches concepts, tests understanding with mini-quizzes, and adapts to weak areas.
allowed-tools: Read, Glob, Grep, Bash
---

# Training Command

Interactive domain study for the Claude Certified Architect – Foundations exam.

## Usage

```
/training              # Recommended domain based on quiz history (or Domain 1 if no history)
/training 1            # Study Domain 1: Agentic Architecture & Orchestration
/training 2            # Study Domain 2: Tool Design & MCP Integration
/training 3            # Study Domain 3: Claude Code Configuration & Workflows
/training 4            # Study Domain 4: Prompt Engineering & Structured Output
/training 5            # Study Domain 5: Context Management & Reliability
/training 1.3          # Study specific task statement 1.3
```

## Workflow

### Step 1: Determine Study Target

If the user provides a domain number (1–5), use it directly. If they provide a task statement reference like `1.3`, focus exclusively on that statement within its parent domain.

If no argument is provided:

1. Check whether `~/.claude/certification/results.jsonl` exists
2. If it does: load the study-path skill (`${CLAUDE_PLUGIN_ROOT}/skills/study-path.md`), compute weak areas, and recommend the highest-priority domain
3. If it does not: default to Domain 1

Tell the user which domain or task statement you will cover and explain why you chose it.

### Step 2: Load Domain Skill

Read the matching domain skill to get authoritative content for teaching:

- Domain 1: `${CLAUDE_PLUGIN_ROOT}/skills/domain-1-agentic-architecture.md`
- Domain 2: `${CLAUDE_PLUGIN_ROOT}/skills/domain-2-tool-design-mcp.md`
- Domain 3: `${CLAUDE_PLUGIN_ROOT}/skills/domain-3-claude-code-config.md`
- Domain 4: `${CLAUDE_PLUGIN_ROOT}/skills/domain-4-prompt-engineering.md`
- Domain 5: `${CLAUDE_PLUGIN_ROOT}/skills/domain-5-context-management.md`

### Step 3: Teach Task Statements

Work through each task statement in the domain (or the single targeted one). For each:

1. **Explain** the key concepts using the skill content. Ground explanations in concrete, exam-relevant scenarios. Focus on WHY things work the way they do, not just WHAT they are.

2. **Surface misconceptions** — call out the common wrong assumptions and explain why they fail. Exam distractors are built from exactly these misunderstandings, so addressing them head-on is critical.

3. **Mini-quiz** — pull 1–2 Easy questions from the question bank that match this task statement.
   - Read the appropriate question bank file:
     - Domain 1: `${CLAUDE_PLUGIN_ROOT}/resources/question-bank/1-agentic-architecture-and-orchestration.md`
     - Domain 2: `${CLAUDE_PLUGIN_ROOT}/resources/question-bank/2-tool-design-and-mcp-integration.md`
     - Domain 3: `${CLAUDE_PLUGIN_ROOT}/resources/question-bank/3-claude-code-configuration-and-workflows.md`
     - Domain 4: `${CLAUDE_PLUGIN_ROOT}/resources/question-bank/4-prompt-engineering-and-structured-output.md`
     - Domain 5: `${CLAUDE_PLUGIN_ROOT}/resources/question-bank/5-context-management-and-reliability.md`
   - Find questions tagged to this task statement
   - Present the question and **wait for the user's answer**
   - If correct: confirm briefly and highlight the key insight
   - If wrong: explain thoroughly — why their choice was wrong, why the correct answer is right, and tie it back to the concept just taught

4. **Check-in** — after each task statement, ask whether the user wants to:
   - Continue to the next task statement
   - Go deeper on this one (present Medium or Hard questions)
   - Switch to a different domain
   - Stop studying and take a quiz

### Step 4: Session Summary

When the user finishes the domain or decides to stop, present a summary:

```
## Training Session Summary

**Domain studied:** [N] — [Name]
**Task statements covered:** [list]
**Mini-quiz results:** [correct] / [total]

### Weak spots to revisit
- [task statements where mini-quiz answers were wrong]

### Recommended next steps
- `/training [next domain]` — Continue studying
- `/quiz --domain [N]` — Test this domain
- `/quiz` — Take a full mock exam
```

Then append a training record so future sessions can adapt:

```bash
mkdir -p ~/.claude/certification && echo '{"date":"<ISO-8601>","domain":<N>,"task_statements":["x.y",...],"mini_quiz":{"total":<T>,"correct":<C>}}' >> ~/.claude/certification/training-progress.jsonl
```
