# Investigation Plugin 🔍

Deep codebase exploration and architectural understanding for legacy code, architecture analysis, and dependency mapping.

## Use Cases

- **Legacy Code Analysis**: Understand unfamiliar codebases
- **Architecture Documentation**: Map out system architecture
- **Dependency Analysis**: Identify and visualize dependencies
- **Code Archaeology**: Trace feature evolution and design decisions
- **Technical Debt Assessment**: Identify areas needing refactoring

## Workflow

```
Explore → Analyze → Document → Present Findings
```

### Phase 1: Explore
- Map directory structure
- Identify key components and entry points
- Discover patterns and conventions
- Catalog dependencies

### Phase 2: Analyze
- Examine architecture and design patterns
- Analyze component relationships
- Identify technical debt
- Assess code quality

### Phase 3: Document
- Create architecture diagrams (as markdown)
- Document component responsibilities
- Map data flows
- Identify key abstractions

### Phase 4: Present Findings
- Generate comprehensive report
- Highlight important insights
- Provide recommendations
- Create onboarding guide

## Subagents

- **Explorer**: Maps codebase structure and identifies components
- **Analyzer**: Analyzes architecture, patterns, and relationships
- **Documenter**: Creates comprehensive documentation

## Configuration

```yaml
# .claude/investigation-config.yaml
investigation:
  depth: deep # surface | medium | deep
  focus:
    - architecture
    - dependencies
    - patterns
    - technical_debt
  output:
    - architecture_doc
    - component_map
    - dependency_graph
    - recommendations
```

## Usage

### Via Slash Command
```bash
/investigate "Analyze the authentication system"
```

### Via Workflow
```bash
claude-code workflow run investigation --target="src/auth"
```

## Output

The investigation produces:

1. **Architecture Overview** (`ARCHITECTURE.md`)
   - High-level system architecture
   - Component responsibilities
   - Key design patterns

2. **Component Map** (`COMPONENTS.md`)
   - Detailed component documentation
   - Dependencies between components
   - Data flows

3. **Dependency Graph** (`DEPENDENCIES.md`)
   - External dependencies
   - Internal dependencies
   - Coupling analysis

4. **Recommendations** (`RECOMMENDATIONS.md`)
   - Technical debt items
   - Refactoring suggestions
   - Improvement opportunities

## Examples

See `examples/investigation/` for detailed usage examples.
