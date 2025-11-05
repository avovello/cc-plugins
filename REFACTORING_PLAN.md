# Repository Refactoring Plan

## Current Problem
The repository was incorrectly implemented as a Python package instead of following the official Claude Code plugin architecture. Claude Code plugins are NOT Python code - they are **self-contained directories with markdown files** that define commands and subagents.

## Understanding Claude Code Plugin Architecture

### Official Plugin Structure
```
plugin-name/
├── .claude-plugin/
│   └── plugin.json          # Manifest with name, description, version, author
├── commands/
│   └── command-name.md      # Slash command implementation (markdown!)
├── agents/                   # Subagents directory
│   ├── subagent-1.md        # Specialized agent definition
│   ├── subagent-2.md
│   └── subagent-n.md
├── skills/                   # Optional: reusable skills
│   └── skill-name/
│       └── SKILL.md
└── README.md                # Plugin documentation
```

### Key Insights

1. **Commands are Markdown files**, NOT code
   - They contain instructions for Claude to follow
   - They specify tools, workflow steps, output format
   - They can launch parallel subagents
   - Example: `/review` command is defined in `commands/review.md`

2. **Subagents are Markdown files**
   - Define specialized AI agents
   - Describe their expertise and when to invoke them
   - Can have frontmatter with metadata
   - Claude delegates work to these specialized agents

3. **Self-Contained Plugins**
   - Each plugin has everything it needs inside its directory
   - No shared code or dependencies between plugins
   - Can be installed independently

4. **Configuration is Declarative**
   - No hardcoded limits - specify in the command markdown
   - Example: "Launch 5 parallel reviewers" is written in the command
   - Numbers, options, behavior all defined in markdown

## Target Repository Structure

```
claude-code-marketplace/
├── README.md                           # Marketplace overview
├── QUICKSTART.md                       # How to install and use plugins
├── CONTRIBUTING.md                     # How to contribute
├── LICENSE
│
├── plugins/
│   │
│   ├── investigation/                  # 🔍 Investigation Plugin
│   │   ├── .claude-plugin/
│   │   │   └── plugin.json
│   │   ├── commands/
│   │   │   └── investigate.md         # /investigate command
│   │   ├── agents/
│   │   │   ├── explorer.md            # Codebase exploration
│   │   │   ├── analyzer.md            # Architecture analysis
│   │   │   └── documenter.md          # Documentation creation
│   │   └── README.md
│   │
│   ├── research/                       # 📚 Research Plugin
│   │   ├── .claude-plugin/
│   │   │   └── plugin.json
│   │   ├── commands/
│   │   │   └── research.md            # /research command
│   │   ├── agents/
│   │   │   ├── researcher.md          # Information gathering
│   │   │   ├── evaluator.md           # Technology evaluation
│   │   │   └── comparator.md          # Comparative analysis
│   │   └── README.md
│   │
│   ├── review/                         # 👁️ Review Plugin
│   │   ├── .claude-plugin/
│   │   │   └── plugin.json
│   │   ├── commands/
│   │   │   └── review.md              # /review command
│   │   ├── agents/
│   │   │   ├── architect-reviewer.md  # Architecture review
│   │   │   ├── security-reviewer.md   # Security audit
│   │   │   ├── backend-reviewer.md    # Backend (PHP, Python, Node, etc.)
│   │   │   ├── frontend-reviewer.md   # Frontend (React, Vue, etc.)
│   │   │   ├── devops-reviewer.md     # DevOps (Docker, K8s, CI/CD)
│   │   │   └── performance-reviewer.md # Performance analysis
│   │   └── README.md
│   │
│   ├── feature/                        # ✨ Feature Plugin
│   │   ├── .claude-plugin/
│   │   │   └── plugin.json
│   │   ├── commands/
│   │   │   └── feature.md             # /feature command
│   │   ├── agents/
│   │   │   ├── planner.md             # Feature planning
│   │   │   ├── implementer.md         # Implementation
│   │   │   ├── tester.md              # Testing
│   │   │   ├── bug-analyzer.md        # Bug analysis
│   │   │   ├── reviewer.md            # Code review
│   │   │   └── documenter.md          # Documentation
│   │   └── README.md
│   │
│   └── bugfix/                         # 🐛 Bugfix Plugin
│       ├── .claude-plugin/
│       │   └── plugin.json
│       ├── commands/
│       │   └── bugfix.md              # /bugfix command
│       ├── agents/
│       │   ├── reproducer.md          # Bug reproduction
│       │   ├── root-cause-detective.md # Root cause analysis
│       │   ├── fixer.md               # Bug fixing
│       │   ├── tester.md              # Testing
│       │   └── regression-tester.md   # Regression testing
│       └── README.md
│
├── examples/                           # Usage examples
│   ├── USAGE_GUIDE.md
│   └── sample-workflows.md
│
└── docs/
    ├── plugin-development.md           # How to create plugins
    ├── command-patterns.md             # Command writing patterns
    └── subagent-patterns.md            # Subagent design patterns
```

## What Needs to Change

### 1. Remove Incorrect Files
```
DELETE:
- setup.py
- pyproject.toml
- src/ (entire directory with Python code)
- All Python implementation files
```

### 2. Restructure Each Plugin

Each plugin needs:

#### A. Plugin Manifest (`.claude-plugin/plugin.json`)
```json
{
  "name": "plugin-name",
  "description": "Brief description",
  "version": "1.0.0",
  "author": {
    "name": "Claude Code Marketplace Contributors",
    "email": "noreply@anthropic.com"
  }
}
```

#### B. Command File (`commands/command-name.md`)
Markdown file with:
- Purpose and description
- Tools allowed (optional table)
- Workflow steps (numbered instructions)
- How to invoke subagents
- Output format specifications
- Processing loops defined in the steps

Example structure:
```markdown
# Command Name

Purpose: What this command does

## Workflow

1. **Step 1: Validation**
   - Check prerequisites
   - Validate inputs

2. **Step 2: Gather Context**
   - Read relevant files
   - Understand current state

3. **Step 3: Parallel Analysis**
   Launch 5 specialized agents in parallel:
   - Agent 1: Focus on X
   - Agent 2: Focus on Y
   - Agent 3: Focus on Z
   - Agent 4: Focus on A
   - Agent 5: Focus on B

4. **Step 4: Consolidate Results**
   - Merge agent outputs
   - Filter by confidence (keep only 80+ scores)

5. **Step 5: Execute Action**
   - Implement changes
   - Test results

6. **Step 6: Documentation**
   - Update documentation
   - Create summary

## Processing Loops

If tests fail in Step 5:
- Analyze failures
- Fix issues
- Return to Step 5 (max 3 iterations)

If review fails in Step 5:
- Apply feedback
- Return to review (max 2 iterations)

## Output Format

[Define expected output format]
```

#### C. Subagent Files (`agents/*.md`)
Markdown files defining specialized agents:

```markdown
# Agent Name

## Purpose
What this agent specializes in and when to invoke it.

## Expertise
- Area 1
- Area 2
- Area 3

## Approach
How this agent analyzes and what it looks for.

## Output Format
What this agent returns.
```

### 3. Plugin-Specific Changes

#### Investigation Plugin
- Command: `/investigate`
- Agents: explorer, analyzer, documenter
- Focus: Deep codebase understanding

#### Research Plugin
- Command: `/research`
- Agents: researcher, evaluator, comparator
- Focus: Technology comparison and best practices

#### Review Plugin (Most Complex)
- Command: `/review`
- Agents:
  - **architect-reviewer.md**: Reviews architecture, design patterns, SOLID principles
  - **security-reviewer.md**: Security vulnerabilities, authentication, input validation
  - **backend-reviewer.md**: Backend code (PHP, Python, Node.js, Go, Bash scripts, etc.)
  - **frontend-reviewer.md**: Frontend code (React, Vue, Angular, vanilla JS, CSS)
  - **devops-reviewer.md**: Docker, Kubernetes, CI/CD, deployment configs, infrastructure
  - **performance-reviewer.md**: Performance issues, optimization opportunities
- Focus: Multi-perspective comprehensive review

#### Feature Plugin
- Command: `/feature`
- Agents: planner, implementer, tester, bug-analyzer, reviewer, documenter
- Focus: Complete feature development lifecycle with loops

#### Bugfix Plugin
- Command: `/bugfix`
- Agents: reproducer, root-cause-detective, fixer, tester, regression-tester
- Focus: Systematic bug fixing with root cause analysis

## Implementation Steps

### Phase 1: Clean Up (Remove Wrong Files)
1. Remove all Python files (setup.py, pyproject.toml, src/)
2. Keep only markdown documentation

### Phase 2: Create Plugin Structure
For each of the 5 plugins:
1. Create directory structure
2. Create plugin.json manifest
3. Create command markdown file
4. Create all subagent markdown files
5. Update plugin README.md

### Phase 3: Documentation Update
1. Update main README to explain new structure
2. Update QUICKSTART with installation instructions
3. Create plugin development guide
4. Create examples

### Phase 4: Test & Validate
1. Validate plugin.json files
2. Verify command markdown format
3. Test installation process
4. Verify all links work

### Phase 5: Commit & Push
1. Commit refactored structure
2. Push to GitHub
3. Create updated documentation

## Key Principles

1. **Markdown, Not Code**: Everything is markdown instructions, not Python/code
2. **Self-Contained**: Each plugin has all its dependencies
3. **Declarative Configuration**: Specify behavior in markdown, not hardcoded
4. **Parallel Subagents**: Define how many to launch in the command
5. **Processing Loops**: Define retry logic in the workflow steps
6. **Claude Interprets**: Claude reads these markdown files and executes them

## Example: Review Plugin Details

### `/review` Command Flow

```markdown
# Code Review Command

## Purpose
Comprehensive multi-perspective code review with specialized reviewers.

## Workflow

1. **Identify Changes**
   - Use git diff to find changed files
   - Categorize by type (backend, frontend, config, etc.)

2. **Launch Parallel Reviewers**
   Start 6 specialized reviewers in parallel:
   - **Architect Reviewer**: Review architectural decisions
   - **Security Reviewer**: Check for security vulnerabilities
   - **Backend Reviewer**: Review backend code (PHP, Python, Node.js, Go, Bash)
   - **Frontend Reviewer**: Review frontend code (React, Vue, JS, CSS)
   - **DevOps Reviewer**: Review Docker, K8s, CI/CD configs
   - **Performance Reviewer**: Identify performance issues

   Each reviewer should:
   - Focus only on their domain
   - Assign confidence score (0-100) to each issue
   - Provide specific file:line citations

3. **Consolidate Feedback**
   - Merge all reviewer outputs
   - Filter issues with confidence < 80
   - Remove duplicates
   - Categorize by severity (critical, high, medium, low)

4. **Check Approval Criteria**
   - Critical issues: 0 allowed
   - High issues: 2 or fewer allowed
   - If criteria not met, proceed to step 5

5. **Apply Fixes** (Loop: max 2 iterations)
   - Address critical and high-priority issues
   - Re-run affected reviewers
   - If issues remain and iterations < 2, repeat from step 5
   - If iterations >= 2, report remaining issues and ask for guidance

6. **Generate Report**
   - Create structured review report
   - List all issues by severity
   - Include reviewer citations
   - Provide summary statistics

## Output Format
[Review report template]
```

### Architect Reviewer Agent

```markdown
# Architect Reviewer

## Purpose
Review architectural decisions, design patterns, and code organization for maintainability and scalability.

## Expertise
- SOLID principles
- Design patterns (Factory, Strategy, Observer, etc.)
- Separation of concerns
- Dependency management
- Code organization
- API design
- Database schema design

## Review Focus

When reviewing code, analyze:

1. **Architecture Patterns**
   - Is the architecture appropriate for the problem?
   - Are design patterns used correctly?
   - Is there over-engineering or under-engineering?

2. **Code Organization**
   - Clear separation of concerns?
   - Proper layering (presentation, business logic, data)?
   - Modular and cohesive?

3. **Dependencies**
   - Proper dependency injection?
   - Loose coupling?
   - Circular dependencies?

4. **Extensibility**
   - Easy to add new features?
   - Open for extension, closed for modification?

5. **Technical Debt**
   - Shortcuts that will cause problems?
   - Areas needing refactoring?

## Confidence Scoring

- **90-100**: Clear architectural anti-pattern or violation
- **80-89**: Likely design issue that should be addressed
- **70-79**: Questionable pattern, could be improved
- **Below 70**: Opinion or style preference (don't report)

## Output Format

For each issue found:
- Description (brief, 1-2 sentences)
- Reason (which principle/pattern is violated)
- File and line number with GitHub permalink
- Confidence score (80-100)
- Suggested fix (optional, brief)
```

## Success Criteria

After refactoring:
- ✅ No Python code (only markdown)
- ✅ Each plugin is self-contained
- ✅ Each plugin has plugin.json manifest
- ✅ Commands are markdown files with clear workflows
- ✅ Subagents are markdown files with clear expertise
- ✅ Review plugin has 6 specialized reviewers
- ✅ Processing loops defined in command workflows
- ✅ All numbers/configs in markdown (not hardcoded)
- ✅ Follows official Claude Code plugin architecture
- ✅ Can be installed with `/plugin install` command

## Timeline

- Phase 1 (Clean Up): 15 minutes
- Phase 2 (Create Structure): 2 hours
- Phase 3 (Documentation): 30 minutes
- Phase 4 (Test): 15 minutes
- Phase 5 (Commit): 15 minutes

**Total**: ~3 hours

## Next Steps

1. Get approval on this plan
2. Execute Phase 1 (clean up wrong files)
3. Start Phase 2 (restructure plugins)
4. Continue through phases 3-5

---

**Ready to proceed?** This plan completely restructures the repository to match the official Claude Code plugin architecture, with markdown-based commands and subagents instead of Python code.
