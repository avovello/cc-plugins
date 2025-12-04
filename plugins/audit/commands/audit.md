---
description: Internal codebase audit for architecture understanding, technical debt assessment, and dependency analysis
---

# Audit Command

**Purpose**: Internal codebase audit for architecture understanding, technical debt assessment, and dependency analysis

## Workflow

### Step 1: Validate Target

- Check that target directory exists
- Determine if it's a full codebase or specific component
- Identify project type (Node.js, Python, PHP, Go, etc.)

### Step 2: Launch Parallel Auditors

Start 5 specialized agents in parallel:

1. **structure-mapper**: Map directory structure and organization
2. **dependency-analyzer**: Analyze external dependencies
3. **pattern-detector**: Identify code patterns and conventions
4. **tech-debt-assessor**: Assess technical debt and code smells
5. **documentation-generator**: (waits for others, runs last)

Each agent operates independently and returns their findings.

### Step 3: Consolidate Findings

Merge outputs from all agents:
- Structure map from structure-mapper
- Dependency analysis from dependency-analyzer
- Pattern inventory from pattern-detector
- Tech debt report from tech-debt-assessor

### Step 4: Generate Documentation

Invoke **documentation-generator** with consolidated findings to create:
- `ARCHITECTURE.md` - System architecture overview
- `COMPONENTS.md` - Component documentation
- `DEPENDENCIES.md` - Dependency analysis
- `TECHNICAL_DEBT.md` - Tech debt assessment
- `AUDIT_REPORT.md` - Complete audit summary

### Step 5: Present Findings

Create final summary report highlighting:
- Key architectural insights
- Critical technical debt items
- Recommended next steps
- Onboarding guide for new developers

## Output Format

The audit produces:

```
audit-output/
├── ARCHITECTURE.md
├── COMPONENTS.md
├── DEPENDENCIES.md
├── TECHNICAL_DEBT.md
└── AUDIT_REPORT.md
```

Each file contains structured, actionable information about YOUR codebase.

## Usage

```bash
/audit "target/directory"
```

Or for full codebase:

```bash
/audit
```

## Notes

- Audit is read-only (no code changes)
- Analyzes INTERNAL code (your existing codebase)
- Focus is on understanding what you have, not evaluating external options
- All outputs are in markdown format
- Findings are fact-based, not opinionated
