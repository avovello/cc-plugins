# Investigation Plugin 🔍

Deep codebase exploration and architectural understanding for legacy code analysis, architecture documentation, and dependency mapping.

## Overview

The Investigation plugin provides systematic codebase analysis through 5 specialized agents that work in parallel to understand your code's structure, dependencies, patterns, and technical debt.

## Use Cases

- **Legacy Code Analysis**: Understand unfamiliar or inherited codebases
- **Architecture Documentation**: Generate comprehensive architecture docs
- **Dependency Audit**: Analyze dependencies and identify issues
- **Technical Debt Assessment**: Identify and prioritize technical debt
- **Onboarding**: Create guides for new team members

## Installation

```bash
# Install the plugin
claude-code plugin install investigation

# Or manually copy to your project
cp -r investigation .claude/plugins/
```

## Usage

### Basic Usage

```bash
# Investigate current directory
/investigate

# Investigate specific directory
/investigate src/auth

# Investigate entire codebase
/investigate .
```

### What It Does

1. **Maps Structure** (structure-mapper agent)
   - Directory organization
   - File counts and types
   - Naming conventions
   - Entry points

2. **Analyzes Dependencies** (dependency-analyzer agent)
   - External dependencies
   - Versions and updates
   - Security issues
   - Unused dependencies

3. **Detects Patterns** (pattern-detector agent)
   - Design patterns in use
   - Architectural patterns
   - Coding conventions
   - Testing patterns

4. **Assesses Technical Debt** (tech-debt-assessor agent)
   - TODO/FIXME comments
   - Code smells
   - Deprecated code
   - Missing error handling

5. **Generates Documentation** (documentation-generator agent)
   - ARCHITECTURE.md
   - COMPONENTS.md
   - DEPENDENCIES.md
   - TECHNICAL_DEBT.md
   - INVESTIGATION_REPORT.md

## Output

The investigation creates:

```
investigation-output/
├── ARCHITECTURE.md          # System architecture overview
├── COMPONENTS.md            # Component documentation
├── DEPENDENCIES.md          # Dependency analysis
├── TECHNICAL_DEBT.md        # Technical debt assessment
└── INVESTIGATION_REPORT.md  # Complete investigation summary
```

## Specialized Agents

### 1. Structure Mapper
Maps directory structure, file organization, and project type.

**Focuses on**: Physical organization, naming patterns, project configuration

### 2. Dependency Analyzer
Analyzes external dependencies, versions, and security.

**Focuses on**: package.json, requirements.txt, composer.json, go.mod

### 3. Pattern Detector
Identifies design patterns, architectural patterns, and conventions.

**Focuses on**: Code patterns, not code quality

### 4. Tech Debt Assessor
Identifies technical debt, code smells, and areas needing attention.

**Focuses on**: TODOs, deprecated code, missing error handling, code smells

### 5. Documentation Generator
Creates comprehensive documentation from all findings.

**Focuses on**: Synthesizing all agent outputs into useful docs

## Examples

### Example 1: New Team Member Onboarding

```bash
# Generate comprehensive onboarding documentation
/investigate

# New developer reads:
# - INVESTIGATION_REPORT.md (start here)
# - ARCHITECTURE.md (understand system design)
# - COMPONENTS.md (learn key components)
```

### Example 2: Technical Debt Audit

```bash
# Assess technical debt before major refactoring
/investigate

# Review TECHNICAL_DEBT.md for:
# - Critical issues requiring immediate attention
# - Priority matrix for refactoring
# - Estimated effort
```

### Example 3: Dependency Audit

```bash
# Audit dependencies for security and updates
/investigate

# Review DEPENDENCIES.md for:
# - Outdated dependencies
# - Security vulnerabilities
# - Unused dependencies
```

### Example 4: Architecture Documentation

```bash
# Document architecture for stakeholders
/investigate

# Use generated docs:
# - ARCHITECTURE.md (for architects)
# - COMPONENTS.md (for developers)
# - INVESTIGATION_REPORT.md (for managers)
```

## Configuration

No configuration required - works out of the box.

## Notes

- **Read-only**: Investigation never modifies code
- **Parallel execution**: All agents run simultaneously for speed
- **Language agnostic**: Works with Node.js, Python, PHP, Go, Java, and more
- **Output format**: All documentation in markdown

## FAQ

**Q: How long does investigation take?**
A: Typically 2-5 minutes for medium-sized codebases (< 50k lines)

**Q: Does it modify any code?**
A: No, investigation is completely read-only

**Q: What if my codebase is huge?**
A: You can investigate specific directories instead of the entire codebase

**Q: Can I customize the output?**
A: The agents produce standardized output, but you can customize by modifying agent files

## Version

1.0.0

## Author

Claude Code Marketplace Contributors
