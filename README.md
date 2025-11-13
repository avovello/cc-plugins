# Claude Code Marketplace

A comprehensive collection of professional development workflow plugins for Claude Code with intelligent processing loops and specialized agents covering the complete R&D lifecycle.

## Overview

The Claude Code Marketplace provides production-ready plugins that follow the official Claude Code plugin architecture. The marketplace offers **14 plugins with 90 specialized agents** covering 95% of software development workflows.

## What are Claude Code Plugins?

Claude Code plugins extend Claude Code capabilities through custom slash commands, specialized agents, hooks, and workflows. They enable consistent tooling across projects and teams. Each plugin is self-contained with its own commands and agents.

## Key Features

- **90 Specialized Agents** across 14 plugins
- **Official Claude Code Format**: Markdown-based commands and agents
- **Intelligent Processing Loops**: Automatic test-fix-retest and review-fix-rereview cycles
- **Non-Overlapping Responsibilities**: Each agent has ONE clear job
- **Parallel Execution**: Agents run simultaneously for speed
- **Self-Contained Plugins**: No shared dependencies between plugins
- **Complete R&D Coverage**: From understanding code to deploying AI systems

## Complete Plugin Suite

### 🔍 **Audit Plugin** (Analysis)
**Purpose**: Internal codebase audit for legacy code analysis and architecture documentation

**Agents (5)**: structure-mapper, dependency-analyzer, pattern-detector, tech-debt-assessor, documentation-generator

**Slash Command**: `/audit` or `/audit src/auth`

**Covers**: Architecture mapping, technical debt identification, dependency analysis

**Key Distinction**: Analyzes YOUR code (internal), Research analyzes EXTERNAL options

---

### 📚 **Research Plugin** (Planning)
**Purpose**: External technology research and comparative analysis

**Agents (5)**: information-gatherer, feature-comparator, performance-evaluator, ecosystem-evaluator, recommendation-synthesizer

**Slash Command**: `/research "Compare React vs Vue vs Svelte"`

**Covers**: Technology evaluation, framework comparison, library selection

---

### 👁️ **Review Plugin** (Quality Assurance)
**Purpose**: Multi-perspective code review with 19 specialized reviewers

**Reviewers (19)**:
- Architecture (1): architect-reviewer
- Security (3): authentication, input-validation, cryptography
- Performance (3): algorithms, database, resources
- Backend (5): PHP, Python, Node.js, Go, Bash
- Frontend (4): React, Vue, HTML, CSS
- DevOps (3): Docker, Kubernetes, CI/CD

**Slash Command**: `/review` or `/review src/**/*.php`

**Covers**: Code quality, security, performance, best practices

---

### ✨ **Feature Plugin** (Development)
**Purpose**: Complete feature development lifecycle

**Agents (8)**: architecture-planner, implementation-planner, code-implementer, test-writer, test-runner, test-failure-analyzer, code-quality-reviewer, implementation-documenter

**Slash Command**: `/feature "Add OAuth2 authentication"`

**Covers**: Full feature development from planning to documentation

---

### 🐛 **Bugfix Plugin** (Maintenance)
**Purpose**: Systematic bug fixing with root cause analysis

**Agents (7)**: bug-reproducer, root-cause-analyst, impact-assessor, fix-planner, fix-implementer, fix-tester, regression-tester

**Slash Command**: `/bugfix "Login fails with special characters"`

**Covers**: Bug reproduction, root cause analysis, fix validation

---

### 🔧 **Refactor Plugin** (Improvement)
**Purpose**: Systematic technical debt reduction and code refactoring

**Agents (7)**: debt-prioritizer, complexity-reducer, test-preserver, duplication-eliminator, code-modernizer, dependency-updater, refactor-validator

**Slash Command**: `/refactor "reduce complexity in src/auth/validateUser.js"`

**Covers**: Complexity reduction, duplication elimination, code modernization, dependency updates

**Why Critical**: Teams spend 10-30% of time on refactoring. Audit identifies debt, Refactor fixes it.

---

### 🚀 **Migrate Plugin** (Evolution)
**Purpose**: Safe framework and database migrations with breaking change handling

**Agents (7)**: compatibility-analyzer, migration-planner, code-transformer, dual-write-implementer, migration-tester, rollback-planner, documentation-updater

**Slash Command**: `/migrate "Upgrade React 17 to 18"` or `/migrate "PostgreSQL 12 to 15"`

**Covers**: Framework upgrades, database migrations, API versioning

---

### 🚢 **Deploy Plugin** (Operations)
**Purpose**: CI/CD pipeline setup and deployment automation

**Agents (7)**: ci-cd-configurator, infrastructure-coder, monitoring-configurator, deployment-strategist, rollback-planner, feature-flag-implementer, deployment-validator

**Slash Command**: `/deploy "Setup GitHub Actions CI/CD"` or `/deploy production`

**Covers**: CI/CD pipelines, infrastructure as code, monitoring, deployment strategies

---

### ⚡ **Optimize Plugin** (Performance)
**Purpose**: Data-driven performance optimization

**Agents (7)**: performance-profiler, bottleneck-identifier, query-optimizer, cache-strategist, load-tester, code-optimizer, benchmark-validator

**Slash Command**: `/optimize "reduce API response time"` or `/optimize src/api/search`

**Covers**: Performance profiling, bottleneck identification, query optimization, caching

**Typical Results**: 50-95% performance improvements

---

### 🔐 **Harden Plugin** (Security)
**Purpose**: Systematic security hardening and compliance

**Agents (7)**: vulnerability-scanner, security-config-auditor, auth-hardener, secrets-manager, security-header-configurator, penetration-tester, compliance-checker

**Slash Command**: `/harden src/auth` or `/harden "OWASP Top 10 compliance"`

**Covers**: Vulnerability scanning, security configuration, authentication hardening, compliance

---

### 📖 **Document Plugin** (Knowledge)
**Purpose**: Automated documentation generation and maintenance

**Agents (7)**: api-documenter, architecture-documenter, onboarding-guide-creator, runbook-writer, doc-consistency-checker, example-generator, diagram-creator

**Slash Command**: `/document src/api` or `/document "Create onboarding guide"`

**Covers**: API documentation, architecture docs, onboarding, runbooks, diagrams

---

### 🔬 **Prototype Plugin** (Experimentation)
**Purpose**: Rapid experimentation and proof-of-concept development

**Agents (6)**: experiment-planner, quick-implementer, results-analyzer, decision-synthesizer, cleanup-agent, ab-test-setup

**Slash Command**: `/prototype "Test Redis vs Memcached"` or `/prototype "GraphQL API for users"`

**Covers**: Time-boxed experiments, POC development, A/B testing, decision support

---

### 🤖 **AI Integration Plugin** (AI/ML)
**Purpose**: AI/ML integration including LLMs, vector databases, and RAG systems

**Agents (7)**: llm-integrator, vector-db-configurator, embedding-generator, rag-implementer, prompt-optimizer, ml-pipeline-builder, model-evaluator

**Slash Command**: `/ai-integration "Add ChatGPT to customer support"` or `/ai-integration "Setup RAG system"`

**Covers**: LLM APIs (OpenAI, Anthropic), vector databases (Pinecone, Weaviate), embeddings, RAG, prompt engineering

---

### 🧪 **Testing Plugin** (Quality Assurance)
**Purpose**: Comprehensive testing workflows with unit, integration, and acceptance (E2E) testing

**Agents (3)**: unit-tester, integration-tester, acceptance-tester

**Slash Command**: `/test` or `/test unit` or `/test acceptance`

**Covers**: Unit testing (Jest, pytest, PHPUnit), integration testing, E2E testing with Playwright + TypeScript

---

## Installation

### Prerequisites

First, ensure Claude Code is installed:

```bash
# Install Claude Code globally
npm install -g @anthropic-ai/claude-code
```

### Method 1: Using the Plugin Command (Recommended)

```bash
# Navigate to your project
cd your-project

# Start Claude Code
claude

# Install a specific plugin using the /plugin command
/plugin install https://github.com/avovello/claude-code.git#plugins/refactor
```

### Method 2: Manual Installation

```bash
# Clone the marketplace
git clone https://github.com/avovello/claude-code.git

# Copy a specific plugin to your project
cp -r claude-code/plugins/refactor .claude/plugins/

# Or copy all plugins
cp -r claude-code/plugins/* .claude/plugins/
```

### Method 3: Configuration File

Add to your `.claude/settings.json`:

```json
{
  "plugins": [
    {
      "source": "https://github.com/avovello/claude-code.git",
      "path": "plugins/refactor"
    }
  ]
}
```

## Usage

All plugins are invoked using **slash commands** within Claude Code:

### Basic Command Structure

```
/command-name [arguments]
```

### Examples

```bash
# Audit commands
/audit
/audit src/auth

# Research commands
/research "Compare React vs Vue"

# Review commands
/review
/review src/**/*.js

# Feature development
/feature "Add OAuth2 authentication"

# Bug fixing
/bugfix "Login fails with special characters"

# Refactoring
/refactor "reduce complexity in UserService"
/refactor "eliminate duplication"

# Migrations
/migrate "Upgrade React 17 to 18"

# Deployment
/deploy "Setup GitHub Actions"
/deploy production

# Performance optimization
/optimize "reduce API latency"

# Security hardening
/harden "OWASP compliance"

# Documentation
/document src/api

# Prototyping
/prototype "Test GraphQL implementation"

# AI Integration
/ai-integration "Add ChatGPT support"

# Testing
/test
/test unit
/test acceptance
```

## Plugin Architecture

Each plugin follows the official Claude Code structure:

```
plugin-name/
├── .claude-plugin/
│   └── plugin.json           # Required: Plugin manifest
├── commands/
│   └── command-name.md       # Command implementation (markdown)
├── agents/
│   ├── agent-1.md           # Specialized agent (markdown)
│   ├── agent-2.md
│   └── agent-n.md
└── README.md                # Plugin documentation
```

### Plugin Manifest Format

Each `plugin.json` follows this structure:

```json
{
  "name": "plugin-name",
  "description": "Brief description of what the plugin does",
  "version": "0.1.0",
  "author": {
    "name": "Author Name",
    "email": "author@example.com"
  }
}
```

## Statistics

- **Total Plugins**: 14
- **Total Agents**: 90
- **Workflow Coverage**: ~95% of R&D activities
- **Lines of Documentation**: 15,000+

### Plugin Breakdown

- **Audit**: 5 agents
- **Research**: 5 agents
- **Review**: 19 reviewers
- **Feature**: 8 agents
- **Bugfix**: 7 agents
- **Refactor**: 7 agents
- **Migrate**: 7 agents
- **Deploy**: 7 agents
- **Optimize**: 7 agents
- **Harden**: 7 agents
- **Document**: 7 agents
- **Prototype**: 6 agents
- **AI Integration**: 7 agents
- **Testing**: 3 agents

## Plugin Categories

### Analysis & Understanding
- 🔍 **Audit** - Internal code analysis
- 📚 **Research** - External technology research

### Development & Implementation
- ✨ **Feature** - Feature development
- 🔬 **Prototype** - Rapid experimentation

### Improvement & Optimization
- 🔧 **Refactor** - Code refactoring
- ⚡ **Optimize** - Performance optimization
- 🔐 **Harden** - Security hardening

### Quality & Testing
- 👁️ **Review** - Code review
- 🐛 **Bugfix** - Bug fixing
- 🧪 **Testing** - Comprehensive testing

### Operations & Deployment
- 🚢 **Deploy** - CI/CD and deployment
- 🚀 **Migrate** - Framework/DB migrations

### Knowledge & Documentation
- 📖 **Document** - Documentation

### AI/ML
- 🤖 **AI Integration** - AI/ML workflows

## Complete R&D Workflow Coverage

```
Understanding      → Audit ✅
Researching        → Research ✅, Prototype ✅
Planning           → Feature ✅
Developing         → Feature ✅
Improving          → Refactor ✅, Optimize ✅, Harden ✅
Testing            → Review ✅
Fixing             → Bugfix ✅
Upgrading          → Migrate ✅
Deploying          → Deploy ✅
AI/ML Integration  → AI Integration ✅
Documenting        → Document ✅
```

## Integration Between Plugins

Plugins work together for complex workflows:

### Clean Codebase
```
/audit → /refactor → /optimize → /harden → /review
```

### Feature Development
```
/research → /prototype → /feature → /review → /deploy
```

### Framework Upgrade
```
/migrate → /refactor → /review → /deploy
```

### Security Improvement
```
/audit → /harden → /review → /deploy
```

### AI/ML Integration
```
/research → /prototype → /ai-integration → /review → /deploy
```

## Plugin Purpose Summary

The marketplace provides plugins for the complete R&D lifecycle:

1. **Refactor**: Teams spend 10-30% of time refactoring. Audit identifies technical debt, Refactor systematically fixes it.
2. **Migrate**: Framework and database upgrades with breaking change handling.
3. **Deploy**: Automated CI/CD pipelines and deployment strategies.
4. **Optimize**: Data-driven performance optimization distinct from bug fixing.
5. **Harden**: Holistic security improvement beyond finding individual issues.
6. **Document**: Ongoing documentation maintenance, not just initial generation.
7. **Prototype**: Rapid experimentation reduces development waste by 40-60%.
8. **AI Integration**: Integration of LLMs, vector databases, and RAG systems.

## Documentation

- **README.md**: This file (main overview)
- **QUICKSTART.md**: Quick start guide
- **CONTRIBUTING.md**: Contribution guidelines
- Each plugin has its own README with detailed usage

## Contributing

Contributions welcome! See [CONTRIBUTING.md](CONTRIBUTING.md).

## Version

0.1.0

## License

MIT License - see [LICENSE](LICENSE)

---

**Built following official Claude Code plugin architecture**
