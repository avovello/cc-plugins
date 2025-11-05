# Investigation Command

**Purpose**: Deep codebase exploration and architectural understanding

## Workflow

### Step 1: Validate Target

- Check that target directory exists
- Determine if it's a full codebase or specific component
- Identify project type (Node.js, Python, PHP, Go, etc.)

### Step 2: Launch Parallel Investigators

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
- `INVESTIGATION_REPORT.md` - Complete investigation summary

### Step 5: Present Findings

Create final summary report highlighting:
- Key architectural insights
- Critical technical debt items
- Recommended next steps
- Onboarding guide for new developers

## Output Format

The investigation produces:

```
investigation-output/
├── ARCHITECTURE.md
├── COMPONENTS.md
├── DEPENDENCIES.md
├── TECHNICAL_DEBT.md
└── INVESTIGATION_REPORT.md
```

Each file contains structured, actionable information about the codebase.

## Usage

```bash
/investigate "target/directory"
```

Or for full codebase:

```bash
/investigate
```

## Notes

- Investigation is read-only (no code changes)
- Focus is on understanding, not evaluation
- All outputs are in markdown format
- Findings are fact-based, not opinionated
