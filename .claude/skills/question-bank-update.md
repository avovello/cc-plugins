---
name: question-bank-update
description: Procedures for physically modifying question bank files — adding, editing, removing, and replacing questions while maintaining format, numbering, and metadata consistency.
---

# Question Bank Update Skill

This skill governs how question bank files are modified. Every change to a question bank file MUST follow these rules.

## Question Bank Location

All files live under `plugins/certification/resources/question-bank/`:

| Domain | File |
|--------|------|
| 1 | `1-agentic-architecture-and-orchestration.md` |
| 2 | `2-tool-design-and-mcp-integration.md` |
| 3 | `3-claude-code-configuration-and-workflows.md` |
| 4 | `4-prompt-engineering-and-structured-output.md` |
| 5 | `5-context-management-and-reliability.md` |

If a file exists under a legacy name (spaces, title case, ampersands), rename it to the canonical hyphenated-lowercase form and update any references in `skills/scoring.md`.

## Question Format

Every question MUST match this exact structure:

```markdown
### Q{domain}.{task}.{number} — Scenario: {scenario name}
**Difficulty: {Easy|Medium|Hard} | {Knowledge|Skill}: {topic tag}**

{Question text — one or more paragraphs, may include code blocks}

A) {option text}

B) {option text}

C) {option text}

D) {option text}

**Correct Answer: {A|B|C|D}**

**Explanation:** {Explanation text that addresses why the correct answer is right AND why each distractor is wrong}
```

Cross-domain questions use `Q{domain}.X.{number}` and a modified tag line:
```markdown
**Difficulty: Hard | Domains: {N} ({name}) + {M} ({name})**
```

Questions are separated by `---` on its own line.

## ID Numbering Rules

- IDs follow `Q{domain}.{task}.{number}` where number is sequential within the task
- When **adding** a question to task T, use the next available number: if Q1.4.3 is the last, the new one is Q1.4.4
- When **removing** a question, do NOT renumber remaining questions (IDs are stable references used in results.jsonl)
- When **replacing** a question (same ID, different content), keep the original ID
- Cross-domain questions use `Q{domain}.X.{number}` and are placed at the end of the file before the footer

## Scenarios Reference

| # | Name | Primary Domains |
|---|------|----------------|
| 1 | Customer Support Resolution Agent | 1, 2, 5 |
| 2 | Code Generation with Claude Code | 3, 5 |
| 3 | Multi-Agent Research System | 1, 2, 5 |
| 4 | Developer Productivity with Claude | 2, 3, 1 |
| 5 | Claude Code for CI/CD | 3, 4 |
| 6 | Structured Data Extraction | 4, 5 |

Every new question MUST use one of these six scenario names exactly as written.

## Distractor Archetypes

Each question should use 3 distractors from these archetypes. Vary the mix — do not over-rely on any single archetype:

- **Over-engineered**: adds unnecessary complexity (classifiers, ML infra, extra abstraction layers, premature optimization, custom middleware)
- **Solves the wrong problem**: correct technique applied to the wrong root cause
- **Probabilistic when deterministic is needed**: relies on prompt-based guidance where programmatic enforcement exists
- **Feature/flag that doesn't exist**: references plausible-sounding but non-existent API features

## Update Operations

### 1. Edit Existing Question

When a question contains outdated information:

1. Read the question bank file
2. Locate the question by ID (e.g., Q2.3.4)
3. Edit ONLY the parts that need changing — preserve the ID, scenario, and structure
4. If the correct answer changes, update the explanation to address all distractors relative to the new answer
5. Update the footer metadata if difficulty changed

### 2. Add New Question

When a topic is not covered:

1. Determine the correct domain and task statement
2. Determine the next available number within that task
3. Choose a scenario appropriate for the domain (see Scenarios Reference)
4. Choose difficulty based on current distribution — prefer Easy if under-represented
5. Write the question following the exact format above
6. Insert it after the last question for that task statement (before the next `## Task Statement` header or before cross-domain questions)
7. Update the footer metadata

### 3. Remove Question

When a question is deprecated or invalid:

1. Remove the entire question block (from `### Q` through the `---` separator)
2. Do NOT renumber subsequent questions
3. Update the footer metadata

### 4. Replace Question

When a question needs to be rewritten entirely (e.g., near-duplicate):

1. Keep the same question ID
2. Write entirely new content following the format
3. Update the footer metadata if difficulty changed

### 5. Fix Metadata Footer

The last 2 lines of each file MUST be:

```markdown
*Total: {N} questions covering all {T} task statements + {C} cross-domain questions*
*Distribution: {E} Easy, {M} Medium, {H} Hard*
```

After ANY modification, recount:
- Total questions (count all `### Q` headers)
- Cross-domain questions (count all `### Q{d}.X.` headers)
- Easy/Medium/Hard (count each `**Difficulty: {level}` occurrence)

### 6. Rename Files

When standardizing filenames:
1. Use `git mv` to preserve history
2. Update `plugins/certification/skills/scoring.md` file path references
3. Update any other skills or commands that reference the old filename

## Validation Checklist

After every update, verify:

- [ ] Every question has all required fields (ID, scenario, difficulty, tag, 4 options, answer, explanation)
- [ ] No duplicate question IDs within the file
- [ ] Question IDs are sequential within each task (no gaps from additions — gaps from removals are OK)
- [ ] Footer metadata matches actual counts
- [ ] Explanation addresses why the correct answer is right AND why each distractor is wrong
- [ ] Scenario name matches one of the 6 canonical names exactly
- [ ] Difficulty tag is exactly `Easy`, `Medium`, or `Hard`
- [ ] Knowledge/Skill tag line uses `|` separator correctly

## Applying /update Report Findings

When the `/update` command produces a report, process each section:

### "Potentially Outdated Questions" table
For each row: edit the question (Operation 1) to reflect current documentation. If the change invalidates the question entirely, replace it (Operation 4).

### "New Topics Not Yet Covered" table
For each row: add a new question (Operation 2) in the relevant domain. Prefer under-represented task statements and scenarios (check review-findings.md for current gaps).

### "Recommended Actions"
Execute each action item using the appropriate operation above.

After applying all changes, update `resources/question-bank/review-findings.md` to reflect the current state — remove resolved issues, update counts, add any new issues discovered during the update.
