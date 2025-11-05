# Quick Start Guide

Get started with Claude Code Marketplace in 5 minutes!

## What is Claude Code Marketplace?

A collection of 13 professional workflow plugins for Claude Code that extend functionality through custom slash commands and specialized agents. Covers the complete R&D lifecycle from code audit to AI integration.

## Prerequisites

Install Claude Code if you haven't already:

```bash
npm install -g @anthropic-ai/claude-code
```

## Installation

### Quick Install (Recommended)

```bash
# Navigate to your project
cd your-project

# Start Claude Code
claude

# Install a plugin using the /plugin command
/plugin install https://github.com/avovello/claude-code-marketplace.git#plugins/refactor
```

### Manual Install

```bash
# Clone the marketplace
git clone https://github.com/avovello/claude-code-marketplace.git

# Copy specific plugins to your project
cp -r claude-code-marketplace/plugins/audit .claude/plugins/
cp -r claude-code-marketplace/plugins/refactor .claude/plugins/

# Or copy all plugins
cp -r claude-code-marketplace/plugins/* .claude/plugins/
```

## How to Use Plugins

All plugins are invoked using **slash commands** within Claude Code:

```
/command-name [arguments]
```

## Quick Examples

### 1. Audit Your Codebase

```bash
# Start Claude Code in your project
claude

# Run audit command
/audit src/auth

# Output: Generates ARCHITECTURE.md, TECHNICAL_DEBT.md, DEPENDENCIES.md, etc.
```

### 2. Refactor Technical Debt

```bash
# After running audit, fix the technical debt it identified
/refactor "reduce complexity in UserService"

# Or eliminate code duplication
/refactor "eliminate duplicate validation logic"
```

### 3. Add a Feature

```bash
# Full feature development workflow
/feature "Add OAuth2 authentication with Google"

# Workflow: Plan → Implement → Test → Review → Document
```

### 4. Fix a Bug

```bash
# Systematic bug fixing with root cause analysis
/bugfix "Login fails when email contains special characters"

# Workflow: Reproduce → Analyze → Plan → Fix → Test → Verify
```

### 5. Review Code

```bash
# Multi-perspective code review
/review

# Or review specific files
/review src/**/*.ts

# Uses 19 specialized reviewers (architecture, security, performance, etc.)
```

### 6. Research Technologies

```bash
# Compare external technologies
/research "Compare React vs Vue vs Svelte for dashboard app"

# Output: Comparison matrix, recommendations, tradeoffs
```

### 7. Optimize Performance

```bash
# Data-driven performance optimization
/optimize "reduce API response time"

# Workflow: Profile → Identify Bottlenecks → Optimize → Benchmark → Validate
```

### 8. Migrate Frameworks

```bash
# Safe framework upgrades with breaking change handling
/migrate "Upgrade React 17 to 18"

# Workflow: Analyze Changes → Plan → Transform → Test → Cutover
```

### 9. Deploy with CI/CD

```bash
# Setup deployment automation
/deploy "Setup GitHub Actions CI/CD pipeline"

# Or deploy to environment
/deploy production
```

### 10. Harden Security

```bash
# Systematic security hardening
/harden src/auth

# Or compliance check
/harden "OWASP Top 10 compliance"
```

### 11. Generate Documentation

```bash
# API documentation
/document src/api

# Or onboarding guide
/document "Create developer onboarding guide"
```

### 12. Prototype Ideas

```bash
# Quick experimentation
/prototype "Test Redis vs Memcached performance"

# Workflow: Plan → Implement POC → Test → Analyze → Decide (Go/No-Go)
```

### 13. Integrate AI/ML

```bash
# Add LLM capabilities
/ai-integration "Add ChatGPT to customer support chat"

# Or setup RAG system
/ai-integration "Setup RAG system for documentation search"
```

## Available Plugins

| Plugin | Command | Purpose |
|--------|---------|---------|
| 🔍 **Audit** | `/audit` | Internal code analysis |
| 📚 **Research** | `/research` | External technology research |
| 👁️ **Review** | `/review` | Multi-perspective code review (19 reviewers) |
| ✨ **Feature** | `/feature` | Complete feature development |
| 🐛 **Bugfix** | `/bugfix` | Systematic bug fixing |
| 🔧 **Refactor** | `/refactor` | Technical debt reduction |
| 🚀 **Migrate** | `/migrate` | Framework/DB migrations |
| 🚢 **Deploy** | `/deploy` | CI/CD and deployment |
| ⚡ **Optimize** | `/optimize` | Performance optimization |
| 🔐 **Harden** | `/harden` | Security hardening |
| 📖 **Document** | `/document` | Documentation generation |
| 🔬 **Prototype** | `/prototype` | Rapid experimentation |
| 🤖 **AI Integration** | `/ai-integration` | AI/ML integration |

## Common Workflows

### Clean Up Legacy Code

```bash
# 1. Identify issues
/audit

# 2. Fix technical debt
/refactor "address high-priority items from audit"

# 3. Optimize performance
/optimize "improve slow endpoints"

# 4. Harden security
/harden "fix security vulnerabilities"

# 5. Final review
/review
```

### Develop New Feature

```bash
# 1. Research options
/research "Best authentication library for Node.js"

# 2. Quick prototype
/prototype "Test Passport.js integration"

# 3. Full implementation
/feature "Add authentication with Passport.js"

# 4. Code review
/review

# 5. Deploy
/deploy staging
```

### Framework Upgrade

```bash
# 1. Plan migration
/migrate "Upgrade Django 3.2 to 4.2"

# 2. Refactor updated code
/refactor "modernize to Django 4.2 patterns"

# 3. Review changes
/review

# 4. Deploy gradually
/deploy "production with feature flags"
```

### Security Hardening

```bash
# 1. Audit current state
/audit src/

# 2. Systematic hardening
/harden "OWASP Top 10 compliance"

# 3. Security-focused review
/review

# 4. Deploy updates
/deploy production
```

### AI Integration Project

```bash
# 1. Research AI options
/research "Compare OpenAI vs Anthropic vs local LLMs"

# 2. Prototype solution
/prototype "Test ChatGPT API integration"

# 3. Implement AI features
/ai-integration "Add ChatGPT-powered search"

# 4. Review and optimize
/review
/optimize "reduce AI API latency"

# 5. Deploy
/deploy production
```

## Tips

1. **Start Small**: Try one plugin at a time to understand the workflow
2. **Use Tab Completion**: Type `/` and press Tab to see available commands
3. **Combine Plugins**: Plugins work together for complex workflows
4. **Check Output**: Most plugins generate markdown files with results
5. **Iterate**: Use processing loops - plugins will test, fix, and re-test automatically

## Plugin Outputs

Most plugins generate markdown documentation:

```
audit-output/
├── ARCHITECTURE.md
├── TECHNICAL_DEBT.md
├── DEPENDENCIES.md
└── AUDIT_REPORT.md

refactor-output/
├── REFACTORING_PLAN.md
├── IMPROVEMENTS.md
└── COMMIT_MESSAGE.md

deploy-output/
├── DEPLOYMENT_PLAN.md
├── ci-cd-config.yml
└── infrastructure.tf
```

## Configuration

Customize plugin behavior in `.claude/settings.json`:

```json
{
  "plugins": [
    {
      "source": "https://github.com/avovello/claude-code-marketplace.git",
      "path": "plugins/refactor"
    }
  ]
}
```

## Next Steps

1. **Install a Plugin**: Start with `/audit` to understand your codebase
2. **Read Plugin READMEs**: Each plugin has detailed documentation
3. **Try Workflows**: Combine plugins for complete workflows
4. **Customize**: Adjust plugin behavior in settings
5. **Contribute**: Share your own plugins!

## Documentation

- **README.md**: Complete overview of all 13 plugins
- **Plugin READMEs**: Detailed docs in each `plugins/*/README.md`
- **Command Files**: See `plugins/*/commands/*.md` for workflows
- **Agent Files**: See `plugins/*/agents/*.md` for agent specifications

## Support

- **Repository**: https://github.com/avovello/claude-code-marketplace
- **Issues**: Report problems on GitHub Issues
- **Contributing**: See CONTRIBUTING.md

---

**Ready to go!** Start with `/audit` to analyze your codebase. 🚀
