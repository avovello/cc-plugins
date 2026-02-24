# Quick Start Guide

Get started with CC Plugins in 5 minutes.

## What is CC Plugins?

A collection of 4 workflow plugins for Claude Code that extend functionality through custom slash commands and 13 specialized agents. Covers feature development, code review, bug fixing, and quality assurance.

## Prerequisites

Install Claude Code:

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

# Install a plugin
/plugin install https://github.com/avovello/cc-plugins.git#plugins/review
/plugin install https://github.com/avovello/cc-plugins.git#plugins/bugfix
/plugin install https://github.com/avovello/cc-plugins.git#plugins/qa
/plugin install https://github.com/avovello/cc-plugins.git#plugins/feature-development
```

### Manual Install

```bash
# Clone the repo
git clone https://github.com/avovello/cc-plugins.git

# Copy specific plugins to your project
cp -r cc-plugins/plugins/review .claude/plugins/
cp -r cc-plugins/plugins/bugfix .claude/plugins/

# Or copy all plugins
cp -r cc-plugins/plugins/* .claude/plugins/
```

## How to Use Plugins

All plugins are invoked using **slash commands** within Claude Code:

```
/command-name [arguments]
```

## Quick Examples

### 1. Review Code Changes

```bash
/review                        # Review uncommitted changes (default)
/review --commit abc123        # Review a specific commit
/review --commit HEAD~3..HEAD  # Review a commit range
/review --pr 123               # Review a pull request
```

Launches an investigator to map changes, then 4 specialized reviewers (security, architecture, performance, bugs) analyze in parallel. Reports only high-confidence findings.

### 2. Fix a Bug

```bash
/bugfix "Login fails when email contains special characters"
```

Workflow: Reproduce → Trace → Root Cause → Impact Assessment → Fix Plan → Implement → Verify

### 3. Add a Feature

```bash
/feature "Add OAuth2 authentication with Google"
```

Workflow: Discovery → Exploration → Clarifying Questions → Architecture Design → Implementation → QA-DEV-REVIEW Loops → Summary

### 4. Run Tests

```bash
/qa              # Run all tests
/unit            # Unit tests only
/integration     # Integration tests only
/e2e             # End-to-end browser tests
/write-tests     # Generate test code
```

## Available Plugins

| Plugin | Command | Agents | Purpose |
|--------|---------|--------|---------|
| **Review** | `/review` | 7 | Two-phase code review: investigate then review in parallel |
| **Bugfix** | `/bugfix` | 2 | Root cause analysis + fix implementation |
| **Feature** | `/feature` | 1 | Architecture-driven feature development |
| **QA** | `/qa`, `/unit`, `/e2e` | 3 | Test writing, execution, browser testing |

## Common Workflows

### Pre-Commit Review

```bash
# 1. Write your code changes
# 2. Review before committing
/review

# 3. Fix any issues found
# 4. Review again
/review
```

### Bug Fix Workflow

```bash
# 1. Investigate and fix the bug
/bugfix "Description of the issue"

# 2. Review the fix
/review

# 3. Run tests
/qa
```

### Feature Development

```bash
# 1. Develop the feature
/feature "Add user authentication"

# 2. Review the implementation
/review

# 3. Run tests
/qa
```

## Tips

1. **Start with /review** — it's the fastest way to catch issues before committing
2. **Combine plugins** — use `/bugfix` then `/review` then `/qa` for a complete fix workflow
3. **Check plugin READMEs** — each plugin has detailed documentation in `plugins/*/README.md`

## Documentation

- **README.md** — Complete overview of all plugins
- **Plugin READMEs** — Detailed docs in each `plugins/*/README.md`
- **Command Files** — See `plugins/*/commands/*.md` for workflows
- **Agent Files** — See `plugins/*/agents/*.md` for agent specifications
- **CLAUDE.md** — Project conventions and architecture decisions

## Support

- **Repository**: https://github.com/avovello/cc-plugins
- **Issues**: Report problems on GitHub Issues
- **Contributing**: See CONTRIBUTING.md
