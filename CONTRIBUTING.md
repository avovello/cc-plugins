# Contributing to Claude Code Marketplace

Thank you for your interest in contributing! This document provides guidelines for contributing to the Claude Code Marketplace.

## Table of Contents

1. [Getting Started](#getting-started)
2. [How to Contribute](#how-to-contribute)
3. [Plugin Development](#plugin-development)
4. [Subagent Development](#subagent-development)
5. [Hook Development](#hook-development)
6. [Documentation](#documentation)
7. [Testing](#testing)
8. [Code Review Process](#code-review-process)
9. [Community Guidelines](#community-guidelines)

## Getting Started

1. **Fork the repository**
   ```bash
   # Fork on GitHub, then clone your fork
   git clone https://github.com/YOUR_USERNAME/claude-code-marketplace.git
   cd claude-code-marketplace
   ```

2. **Create a branch**
   ```bash
   git checkout -b feature/your-feature-name
   # or
   git checkout -b fix/bug-description
   ```

3. **Make your changes**

4. **Test your changes**
   - Test plugins with real Claude Code instances
   - Verify documentation is accurate
   - Check examples work as expected

5. **Submit a pull request**

## How to Contribute

### Areas for Contribution

- 🔌 **New Plugins**: Create new workflow plugins
- 🤖 **Subagents**: Develop specialized subagents
- 🪝 **Hooks**: Create useful hook templates
- 📚 **Documentation**: Improve docs and examples
- 🐛 **Bug Fixes**: Fix issues in existing plugins
- ✨ **Enhancements**: Improve existing features
- 🎨 **Examples**: Add usage examples

### What We're Looking For

**High Priority**:
- Real-world plugin examples
- Production-tested workflows
- Performance optimizations
- Better error handling
- More comprehensive examples

**Welcome Additions**:
- Language-specific plugins (Python, Java, Go, etc.)
- Framework-specific plugins (React, Django, Spring, etc.)
- Domain-specific plugins (ML, Data Science, DevOps, etc.)
- Integration plugins (CI/CD, monitoring, etc.)

## Plugin Development

### Plugin Structure

```
plugins/your-plugin/
├── README.md              # Plugin documentation
├── workflow.md           # Workflow implementation
├── subagents/            # Plugin-specific subagents (optional)
│   └── specialized.md
└── hooks/                # Plugin-specific hooks (optional)
    └── custom-hook.yaml
```

### Plugin Requirements

1. **README.md** must include:
   - Clear description of what the plugin does
   - Use cases
   - Workflow diagram
   - Configuration options
   - Usage examples
   - Success criteria

2. **workflow.md** must include:
   - Clear step-by-step workflow
   - Input parameters
   - State management
   - Error handling
   - Output format

3. **Quality Standards**:
   - Clear, actionable steps
   - Comprehensive error handling
   - Progress tracking with TodoWrite
   - User communication at each phase
   - Documented exit conditions

### Plugin Template

Use this template for new plugins:

```markdown
# Your Plugin Name

Brief description.

## Use Cases
- Use case 1
- Use case 2

## Workflow
[Diagram or description]

## Configuration
[Configuration options]

## Usage
[How to use]

## Examples
[Real examples]
```

## Subagent Development

### Subagent Structure

Subagents are markdown files that define specialized AI agents:

```markdown
# Subagent Name

Description of the subagent's purpose.

## Your Responsibilities
1. Responsibility 1
2. Responsibility 2

## Execution Steps
Step-by-step process

## Output Format
Expected output structure

## Available Tools
Tools this subagent can use
```

### Subagent Best Practices

1. **Single Responsibility**: Each subagent should have one clear purpose
2. **Clear Instructions**: Provide step-by-step execution guidance
3. **Defined Outputs**: Specify exact output format
4. **Tool Usage**: Document which tools to use
5. **Error Handling**: Include error handling guidance

## Hook Development

### Hook Structure

Hooks are YAML files that define event-driven automation:

```yaml
name: hook-name
description: What this hook does

triggers:
  - event: event_name
    condition: optional_condition

actions:
  - name: action_name
    type: action_type
    # ... action configuration

config:
  # ... hook configuration
```

### Hook Best Practices

1. **Focused Purpose**: One hook = one concern
2. **Clear Triggers**: Define exactly when hook runs
3. **Safe Actions**: Ensure actions are safe to run automatically
4. **Configurable**: Allow users to customize behavior
5. **Well Documented**: Explain what the hook does and why

## Documentation

### Documentation Standards

1. **Clear Writing**: Use simple, clear language
2. **Complete Examples**: All examples should be runnable
3. **Up-to-Date**: Keep docs in sync with code
4. **User-Focused**: Write for users, not developers
5. **Visual Aids**: Use diagrams and tables when helpful

### Documentation Checklist

- [ ] README updated if needed
- [ ] Examples added/updated
- [ ] Configuration documented
- [ ] Usage guide updated
- [ ] Approaches document updated if adding new approach

## Testing

### Manual Testing

Before submitting:

1. **Test the Happy Path**
   - Run through the main workflow
   - Verify it works as expected
   - Check all outputs are created

2. **Test Error Cases**
   - Try invalid inputs
   - Test failure scenarios
   - Verify error messages are helpful

3. **Test Edge Cases**
   - Empty inputs
   - Very large inputs
   - Unusual but valid inputs

4. **Test Integrations**
   - Test with different project types
   - Verify hooks work correctly
   - Check subagent interactions

### Testing Checklist

- [ ] Plugin works end-to-end
- [ ] Error handling works
- [ ] Documentation examples work
- [ ] Configuration options work
- [ ] Hooks trigger correctly

## Code Review Process

### What Reviewers Look For

1. **Functionality**
   - Does it work as described?
   - Are there bugs or issues?

2. **Documentation**
   - Is it well documented?
   - Are examples clear and complete?

3. **Code Quality**
   - Is it well structured?
   - Is it maintainable?

4. **User Experience**
   - Is it easy to use?
   - Are error messages helpful?

### Review Timeline

- Initial review: Within 1 week
- Follow-up reviews: Within 3 days
- Merge: When approved by 1+ maintainers

## Community Guidelines

### Code of Conduct

1. **Be Respectful**: Treat everyone with respect
2. **Be Constructive**: Provide helpful feedback
3. **Be Patient**: Everyone is learning
4. **Be Inclusive**: Welcome all contributors

### Communication

- **Issues**: For bugs and feature requests
- **Discussions**: For questions and ideas
- **Pull Requests**: For code contributions

### Getting Help

If you need help:
1. Check existing documentation
2. Search existing issues
3. Ask in Discussions
4. Create a new issue

## Recognition

Contributors will be:
- Listed in CONTRIBUTORS.md
- Credited in release notes
- Acknowledged in plugin READMEs for their contributions

## Plugin Acceptance Criteria

For a plugin to be accepted:

1. **Quality**
   - ✅ Well documented
   - ✅ Clear use cases
   - ✅ Working examples
   - ✅ Error handling

2. **Utility**
   - ✅ Solves real problems
   - ✅ Useful to multiple users
   - ✅ Not duplicating existing plugins

3. **Maintainability**
   - ✅ Clear code structure
   - ✅ Follows marketplace conventions
   - ✅ Sustainable to maintain

## Development Workflow

```bash
# 1. Create feature branch
git checkout -b feature/my-plugin

# 2. Make changes
# ... develop your plugin

# 3. Test thoroughly
# ... test with real Claude Code

# 4. Commit with clear messages
git add .
git commit -m "Add [plugin-name] plugin for [use-case]

- Implements workflow for [feature]
- Includes comprehensive examples
- Adds documentation"

# 5. Push to your fork
git push origin feature/my-plugin

# 6. Create pull request on GitHub
```

## Questions?

If you have questions:
- Open a Discussion
- Check existing documentation
- Ask in your pull request

Thank you for contributing to Claude Code Marketplace! 🚀
