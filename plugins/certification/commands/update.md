---
description: Check for updates to Claude documentation, release notes, and certification materials. Compares current question bank against latest docs to flag outdated content.
allowed-tools: Read, Glob, Grep, Bash, WebFetch
---

# Update Command

Fetch the latest Claude documentation and release notes, compare against the current question bank, and flag content that may need updating.

## Usage

/update                # Full update check
/update --docs         # Only check documentation changes
/update --exam         # Only check exam guide for updates

## Workflow

### Step 1: Fetch Latest Sources

Use WebFetch to retrieve content from each of these sources:

1. **Claude Code docs**: https://docs.anthropic.com/en/docs/claude-code
2. **Claude API docs**: https://docs.anthropic.com/en/api
3. **MCP specification**: https://modelcontextprotocol.io
4. **Anthropic changelog**: https://docs.anthropic.com/en/docs/changelog
5. **Skilljar courses**: https://anthropic.skilljar.com

For each source, extract key topics, features, and any recent changes. If a source is unreachable, note it in the report and continue with the remaining sources.

When the user passes `--docs`, fetch only sources 1-4. When the user passes `--exam`, fetch only source 5 and the exam guide reference.

### Step 2: Load Current Content

Read the exam guide reference:

- `plugins/certification/resources/exam-guide-reference.md`

Read all question bank files:

- Use Glob to find `plugins/certification/resources/question-bank/*.md`
- Read each file to build a complete picture of current coverage

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

| Question ID | Issue | Source |
|-------------|-------|--------|
| [id] | [description of what changed] | [source URL] |

### New Topics Not Yet Covered

| Topic | Source | Relevant Domain |
|-------|--------|-----------------|
| [topic] | [source URL] | [domain from exam guide] |

### Up-to-Date Areas

[List domains and topics confirmed to be current]

### Recommended Actions

- [Specific questions to update, with IDs]
- [New questions to write, with suggested domains]
- [Topics to add to training skills]
```

This command identifies potential updates but does not modify any files. Review the suggestions and apply changes manually.
