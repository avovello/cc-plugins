---
description: Fetch latest Claude documentation, compare against the question bank, and apply updates — editing outdated questions, adding new ones for uncovered topics, and fixing metadata.
allowed-tools: Read, Edit, Write, Glob, Grep, Bash, WebFetch
---

# Update Command

Fetch the latest Claude documentation and release notes, compare against the current question bank, and apply updates to keep questions accurate and coverage complete.

## Usage

/update                # Full update: analyze + apply changes
/update --docs         # Only check documentation changes
/update --exam         # Only check exam guide for updates
/update --report-only  # Analysis only, no file modifications (legacy behavior)

## Workflow

### Step 1: Fetch Latest Sources

Use WebFetch to retrieve content from each of these sources:

1. **Claude Code docs**: https://code.claude.com/docs
2. **Claude API docs**: https://platform.claude.com/docs/en/api/getting-started
3. **MCP specification**: https://modelcontextprotocol.io
4. **Claude Code changelog**: https://code.claude.com/docs/en/changelog.md
5. **API versioning**: https://platform.claude.com/docs/en/api/versioning
6. **Skilljar courses**: https://anthropic.skilljar.com

For each source, extract key topics, features, and any recent changes. If a source is unreachable, note it in the report and continue with the remaining sources.

When the user passes `--docs`, fetch only sources 1-5. When the user passes `--exam`, fetch only source 6 and the exam guide reference.

### Step 2: Load Current Content

Read the exam guide reference:

- `plugins/certification/resources/exam-guide-reference.md`

Read all question bank files:

- Use Glob to find `plugins/certification/resources/question-bank/*.md` (exclude `review-findings.md`)
- Read each file to build a complete picture of current coverage

Also read the existing review findings:

- `plugins/certification/resources/question-bank/review-findings.md`

### Step 3: Compare and Analyze

For each fetched source, identify:

1. **New features or concepts** not covered in the question bank
   - New CLI flags, new tool capabilities, new MCP features
   - New best practices or pattern changes

2. **Changed behavior** that contradicts existing questions
   - Deprecated features still tested
   - Changed defaults or configuration paths
   - Updated API behavior

3. **New exam guide versions** (if the exam guide URL is accessible)
   - New task statements
   - Changed domain weights
   - New scenarios

### Step 4: Generate Report

Present the findings in this format:

```
## Content Update Report

**Checked:** [date]
**Sources fetched:** [N] / [total]

### Potentially Outdated Questions

| Question ID | Issue | Source | Action |
|-------------|-------|--------|--------|
| [id] | [what changed] | [source URL] | Edit / Replace / Remove |

### New Topics Not Yet Covered

| Topic | Source | Relevant Domain | Task Statement |
|-------|--------|-----------------|----------------|
| [topic] | [source URL] | [domain] | [task] |

### Up-to-Date Areas

[List domains and topics confirmed to be current]

### Planned Changes

- [Specific edits, additions, removals with IDs and rationale]
```

### Step 5: Confirm Before Applying

If `--report-only` was passed, stop here. Otherwise:

Present the planned changes summary to the user and ask for confirmation before proceeding. Group changes by type:

- **Edits**: questions being updated with corrected information
- **Additions**: new questions being written
- **Removals**: questions being deleted
- **Replacements**: questions being rewritten entirely
- **Metadata fixes**: footer corrections, file renames

Wait for the user to confirm or adjust the plan.

### Step 6: Apply Changes

Use the `question-bank-update` skill to execute each planned change. The skill defines the exact format, numbering, and validation rules.

For each change:
1. Read the target question bank file
2. Apply the modification (edit, add, remove, or replace)
3. Recount and update the footer metadata

If files need renaming (legacy naming conventions), use `git mv` and update references in `plugins/certification/skills/scoring.md`.

### Step 7: Validate and Report

After all changes are applied:

1. Run the validation checklist from the `question-bank-update` skill against every modified file
2. Update `resources/question-bank/review-findings.md` — remove resolved issues, update counts, note any new issues
3. Present a summary of what was changed:

```
## Update Applied

**Questions edited:** [N]
**Questions added:** [N]
**Questions removed:** [N]
**Questions replaced:** [N]
**Metadata fixed:** [yes/no]
**Files renamed:** [list or "none"]

### Changes Detail

[List each change: question ID, what was done, why]

### Remaining Issues

[Any items from the report that were not addressed, with reasons]
```
