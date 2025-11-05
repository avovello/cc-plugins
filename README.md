# Claude Code Marketplace 🚀

**Claude Nexus** - A sophisticated marketplace of professional development workflows for Claude Code.

## Overview

The Claude Code Marketplace provides enterprise-grade plugins that implement complete development lifecycle workflows with intelligent processing loops, automated reviews, and comprehensive quality gates.

## Philosophy

Traditional development workflows are linear. Claude Nexus introduces **cyclic quality loops**:
- **Planning** → **Review** → **Implementation** → **Testing** → **Bug Analysis** → **Fix** → **Re-test** → **Code Review** → **Review Fixes** → **Documentation** → **Deploy**

Each plugin orchestrates specialized subagents, hooks, and skills to ensure quality at every step.

## Plugins

### 🔍 Investigation Plugin
Deep codebase exploration and architectural understanding.
- **Use Case**: Understanding legacy code, architecture analysis, dependency mapping
- **Workflow**: Explore → Analyze → Document → Present Findings
- **Subagents**: `explorer`, `analyzer`, `documenter`

### 📚 Research Plugin
Technology research, API exploration, and best practice discovery.
- **Use Case**: Evaluating technologies, researching patterns, API investigation
- **Workflow**: Research → Compare → Evaluate → Recommend
- **Subagents**: `researcher`, `evaluator`, `comparator`

### 👁️ Review Plugin
Multi-perspective code review with automated quality checks.
- **Use Case**: PR reviews, architecture reviews, security audits
- **Workflow**: Analyze → Review → Validate → Report → Fix Review → Re-validate
- **Subagents**: `code-reviewer`, `security-reviewer`, `performance-reviewer`

### ✨ Feature Plugin
Complete feature development with quality loops.
- **Use Case**: New feature development from concept to deployment
- **Workflow**:
  - **Plan** → Planning Review → **Implement**
  - → **Test** → (Bug Analysis → Fix → Re-test)*
  - → **Review** → (Review Fixes → Re-review)*
  - → **Document** → **Deploy**
- **Subagents**: `planner`, `implementer`, `tester`, `bug-analyzer`, `reviewer`, `documenter`

### 🐛 Bugfix Plugin
Intelligent bug fixing with comprehensive testing loops.
- **Use Case**: Bug fixes with root cause analysis and verification
- **Workflow**:
  - **Analyze Bug** → **Identify Root Cause** → **Plan Fix**
  - → **Implement Fix** → **Test**
  - → (Bug Still Exists? → Re-analyze → Re-fix → Re-test)*
  - → **Regression Test** → **Review** → **Deploy**
- **Subagents**: `bug-analyzer`, `root-cause-detective`, `fixer`, `tester`, `regression-tester`

## Architecture

```
claude-code-marketplace/
├── plugins/                    # Plugin implementations
│   ├── investigation/
│   ├── research/
│   ├── review/
│   ├── feature/
│   └── bugfix/
├── subagents/                  # Reusable subagent definitions
├── skills/                     # Reusable skill implementations
├── hooks/                      # Event-driven automation hooks
├── workflows/                  # Workflow orchestration definitions
├── examples/                   # Usage examples
└── docs/                       # Comprehensive documentation
```

## Key Concepts

### Subagents
Specialized AI agents optimized for specific tasks (e.g., `bug-analyzer`, `security-reviewer`)

### Skills
Reusable capabilities that can be invoked (e.g., `run-tests`, `analyze-performance`)

### Hooks
Event-driven automation that triggers on specific events (e.g., `pre-commit`, `post-test`, `on-failure`)

### Workflows
Orchestrated sequences of subagents, skills, and hooks that implement processing loops

### Processing Loops
Intelligent cycles that repeat until quality criteria are met:
- **Testing Loop**: Test → Analyze Failures → Fix → Re-test
- **Review Loop**: Review → Apply Feedback → Re-review
- **Quality Gate Loop**: Check Quality → Fix Issues → Re-check

## Installation

```bash
# Clone the marketplace
git clone https://github.com/avovello/claude-code-marketplace.git

# Link desired plugins to your Claude Code workspace
cd your-project
ln -s /path/to/claude-code-marketplace/plugins/feature .claude/workflows/feature
```

## Usage

See `examples/` for detailed usage examples of each plugin.

## Plugin Approach Options

We provide **three implementation approaches** for maximum flexibility:

### Approach 1: Monolithic Workflows (Recommended for simplicity)
Single workflow file that orchestrates everything with state management.
- ✅ Simple to understand and maintain
- ✅ Easy state sharing between steps
- ❌ Less modular, harder to reuse components

### Approach 2: Modular Composition (Recommended for reusability)
Separate subagents, skills, and workflows composed via configuration.
- ✅ Highly modular and reusable
- ✅ Easy to mix and match components
- ❌ More complex setup

### Approach 3: Hybrid (Recommended for production)
Core workflows with pluggable subagents and hooks for customization.
- ✅ Balance of simplicity and flexibility
- ✅ Easy to extend and customize
- ✅ Production-ready

See `docs/approaches.md` for detailed comparison and recommendations.

## Contributing

Contributions welcome! Please see `CONTRIBUTING.md` for guidelines.

## License

MIT License - see LICENSE file for details.

---

Built with ❤️ for the Claude Code community
