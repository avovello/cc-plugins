# Claude Code Marketplace

A collection of professional development workflow plugins for Claude Code with intelligent processing loops and specialized subagents.

## Overview

The Claude Code Marketplace provides production-ready plugins that follow the official Claude Code plugin architecture. Each plugin contains specialized subagents with precise, non-overlapping responsibilities.

## Key Features

- **44 Specialized Subagents** across 5 plugins
- **Official Claude Code Format**: Markdown-based commands and agents
- **Intelligent Processing Loops**: Automatic test-fix-retest and review-fix-rereview cycles
- **Non-Overlapping Responsibilities**: Each agent has ONE clear job
- **Parallel Execution**: Agents run simultaneously for speed
- **Self-Contained Plugins**: No shared dependencies between plugins

## Plugins

### 🔍 Audit Plugin
**Purpose**: Internal codebase audit for legacy code analysis and architecture documentation

**Agents (5)**: structure-mapper, dependency-analyzer, pattern-detector, tech-debt-assessor, documentation-generator

**Usage**: `/audit` or `/audit src/auth`

**Output**: ARCHITECTURE.md, COMPONENTS.md, DEPENDENCIES.md, TECHNICAL_DEBT.md, AUDIT_REPORT.md

**Key Distinction**: Audit analyzes YOUR code (internal), while Research analyzes EXTERNAL options (technologies, libraries)

---

### 📚 Research Plugin
**Purpose**: Technology research and comparative analysis

**Agents (5)**: information-gatherer, feature-comparator, performance-evaluator, ecosystem-evaluator, recommendation-synthesizer

**Usage**: `/research "Compare React vs Vue vs Svelte"`

**Output**: RESEARCH_REPORT.md, REFERENCES.md

---

### 👁️ Review Plugin
**Purpose**: Multi-perspective code review with 19 specialized reviewers

**Reviewers (19)**:
- Architecture (1): architect-reviewer
- Security (3): authentication, input-validation, cryptography
- Performance (3): algorithms, database, resources
- Backend (5): PHP, Python, Node.js, Go, Bash
- Frontend (4): React, Vue, HTML, CSS
- DevOps (3): Docker, Kubernetes, CI/CD

**Usage**: `/review` or `/review src/**/*.php`

**Processing**: Quality gates → Fix loop (max 2 iterations) → Report

---

### ✨ Feature Plugin
**Purpose**: Complete feature development lifecycle

**Agents (8)**: architecture-planner, implementation-planner, code-implementer, test-writer, test-runner, test-failure-analyzer, code-quality-reviewer, implementation-documenter

**Usage**: `/feature "Add OAuth2 authentication"`

**Loops**: Testing Loop (max 3), Review Loop (max 2)

---

### 🐛 Bugfix Plugin
**Purpose**: Systematic bug fixing with root cause analysis

**Agents (7)**: bug-reproducer, root-cause-analyst, impact-assessor, fix-planner, fix-implementer, fix-tester, regression-tester

**Usage**: `/bugfix "Login fails with special characters"`

**Loop**: Fix → Test → Re-analyze → Re-fix (max 3 iterations)

## Installation

```bash
# Clone the marketplace
git clone https://github.com/avovello/claude-code-marketplace.git

# Install a specific plugin
cp -r claude-code-marketplace/plugins/review .claude/plugins/
```

## Usage Examples

### Audit
```bash
/audit                         # Full codebase
/audit src/auth               # Specific directory
```

### Research
```bash
/research "Compare state management: Redux vs Zustand vs MobX"
```

### Review
```bash
/review                        # Review current changes
/review src/**/*.php          # Review specific files
```

### Feature Development
```bash
/feature "Add real-time notifications with WebSockets"
```

### Bug Fixing
```bash
/bugfix "File upload fails for files > 5MB"
```

## Architecture

Each plugin follows official Claude Code structure:

```
plugin-name/
├── .claude-plugin/
│   └── plugin.json           # Plugin manifest
├── commands/
│   └── command-name.md       # Command implementation (markdown)
├── agents/
│   ├── agent-1.md           # Specialized agent (markdown)
│   ├── agent-2.md
│   └── agent-n.md
└── README.md
```

## Statistics

- **Total Plugins**: 5
- **Total Subagents**: 44
- **Audit**: 5 agents
- **Research**: 5 agents
- **Review**: 19 reviewers (most complex)
- **Feature**: 8 agents
- **Bugfix**: 7 agents

## Documentation

- **README.md**: This file
- **QUICKSTART.md**: Quick start guide
- **CONTRIBUTING.md**: Contribution guidelines
- **REFACTORING_PLAN_V3_FINAL.md**: Architecture decisions
- Each plugin has its own README with detailed usage

## Contributing

Contributions welcome! See [CONTRIBUTING.md](CONTRIBUTING.md).

## Version

1.0.0

## License

MIT License - see [LICENSE](LICENSE)

---

**Built following official Claude Code plugin architecture**
