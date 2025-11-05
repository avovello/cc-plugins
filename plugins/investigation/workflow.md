# Investigation Workflow

This workflow performs deep codebase investigation and architectural analysis.

## Configuration

```yaml
max_exploration_depth: 5
focus_areas:
  - architecture
  - dependencies
  - patterns
  - technical_debt
output_format: markdown
```

## Workflow Execution

When this workflow is triggered, you will execute the following phases:

---

### Phase 1: Exploration

**Objective**: Map the codebase structure and identify key components.

**Steps**:

1. **Map Directory Structure**
   - Use Glob to list all directories and files
   - Identify project type (Node.js, Python, Java, etc.)
   - Find configuration files

2. **Identify Entry Points**
   - Find main files (index.js, main.py, etc.)
   - Locate test directories
   - Identify build configurations

3. **Discover Dependencies**
   - Read package.json / requirements.txt / pom.xml
   - Identify external libraries
   - Note version constraints

4. **Catalog Code Organization**
   - Identify naming conventions
   - Find common patterns
   - Note directory structure conventions

**Output**: Exploration report with:
- Directory structure
- Project type and configuration
- Entry points
- Dependencies list
- Code organization patterns

---

### Phase 2: Analysis

**Objective**: Analyze architecture, patterns, and relationships.

**Steps**:

1. **Architecture Analysis**
   - Identify architectural patterns (MVC, microservices, layered, etc.)
   - Map component responsibilities
   - Identify core abstractions

2. **Component Relationship Mapping**
   - Analyze imports and dependencies
   - Identify tightly coupled components
   - Map data flows

3. **Pattern Recognition**
   - Identify design patterns in use
   - Note coding conventions
   - Find common anti-patterns

4. **Technical Debt Assessment**
   - Identify code smells
   - Find deprecated patterns
   - Note TODO/FIXME comments
   - Assess test coverage

**Output**: Analysis report with:
- Architecture overview
- Component relationship diagram (text-based)
- Design patterns identified
- Technical debt items
- Quality assessment

---

### Phase 3: Documentation

**Objective**: Create comprehensive documentation of findings.

**Steps**:

1. **Create Architecture Documentation**
   - High-level system overview
   - Component descriptions
   - Architecture diagrams (as markdown/ASCII)

2. **Document Components**
   - Purpose of each major component
   - Key classes/functions
   - Dependencies

3. **Create Dependency Documentation**
   - External dependencies
   - Internal dependencies
   - Dependency graph (text-based)

4. **Document Patterns and Conventions**
   - Naming conventions
   - Code organization patterns
   - Best practices in use

**Output**: Documentation files:
- `ARCHITECTURE.md`
- `COMPONENTS.md`
- `DEPENDENCIES.md`
- `PATTERNS.md`

---

### Phase 4: Present Findings

**Objective**: Generate comprehensive report with insights and recommendations.

**Steps**:

1. **Compile Investigation Report**
   - Executive summary
   - Detailed findings
   - Visual representations (ASCII/markdown)

2. **Generate Recommendations**
   - Refactoring opportunities
   - Technical debt priorities
   - Architecture improvements
   - Testing gaps

3. **Create Onboarding Guide**
   - Where to start for new developers
   - Key files to understand
   - Common workflows
   - Development setup

**Output**: Final deliverables:
- `INVESTIGATION_REPORT.md` - Complete investigation findings
- `RECOMMENDATIONS.md` - Actionable recommendations
- `ONBOARDING.md` - Developer onboarding guide

---

## Invocation

This workflow expects the following input:

```yaml
investigation_target: "path/to/investigate" # Directory or component to investigate
investigation_type: "full | architecture | dependencies | patterns"
output_directory: "./investigation-output"
```

## Available Tools

- **Task (Explore subagent)**: For thorough codebase exploration
- **Glob**: For finding files and patterns
- **Grep**: For searching code
- **Read**: For reading files
- **Write**: For creating documentation
- **TodoWrite**: For tracking investigation progress

## Example Usage

```bash
# Full codebase investigation
claude-code workflow investigation --target="." --type=full

# Architecture-focused investigation
claude-code workflow investigation --target="src/" --type=architecture

# Dependency analysis
claude-code workflow investigation --target="." --type=dependencies
```

## Hooks

### Pre-Investigation Hook
- Validate target exists
- Check for existing documentation
- Estimate investigation scope

### Post-Investigation Hook
- Validate all documentation created
- Generate summary report
- Create TODO items for follow-up

### On-Complexity-High Hook
- Break down investigation into smaller parts
- Prioritize most critical components
- Create investigation plan

---

## Notes for Implementation

When implementing this workflow:

1. **Start Broad, Go Deep**: Begin with high-level structure, then dive into details
2. **Use TodoWrite**: Track exploration progress through components
3. **Parallel Exploration**: When possible, explore independent components in parallel
4. **Visual Representation**: Use ASCII diagrams, tree structures, and markdown tables
5. **Actionable Insights**: Focus on insights that lead to concrete actions

## Success Criteria

Investigation is complete when:
- [ ] All major components are documented
- [ ] Architecture is clearly described
- [ ] Dependencies are mapped
- [ ] Technical debt is identified
- [ ] Recommendations are actionable
- [ ] Onboarding guide is created
