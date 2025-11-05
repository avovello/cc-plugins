# Document Plugin

Documentation maintenance through automated API documentation, architecture diagrams, and comprehensive developer guides.

## Overview

The Document plugin ensures documentation stays synchronized with code. Research shows that **outdated documentation costs teams 20-30% of development time** in confusion and miscommunication.

**Key Distinction**:
- **Document** maintains existing documentation (this plugin)
- **Audit** generates initial documentation

## Use Cases

- **API Documentation**: Keep API docs synchronized with endpoints
- **Architecture Docs**: Document system design and components
- **Onboarding Guides**: Help new developers get started quickly
- **Runbooks**: Operational procedures for production
- **Code Examples**: Working examples for documentation
- **Diagrams**: Visual architecture and flow diagrams

## Usage

```bash
/document "update API documentation for /api/users endpoints"
/document "create architecture documentation"
/document "generate onboarding guide for new developers"
/document "create runbook for deployment process"
```

## Specialized Agents (7)

### 1. **api-documenter**
Generates API documentation from code with schemas and examples

### 2. **architecture-documenter**
Creates system architecture docs with component diagrams

### 3. **onboarding-guide-creator**
Builds comprehensive onboarding guides for new developers

### 4. **runbook-writer**
Creates operational runbooks for deployment and troubleshooting

### 5. **doc-consistency-checker**
Validates documentation accuracy against code

### 6. **example-generator**
Generates working code examples for documentation

### 7. **diagram-creator**
Creates architecture, sequence, and data flow diagrams

## Output

```
documentation/
├── API.md                    # API documentation
├── ARCHITECTURE.md           # System architecture
├── ONBOARDING.md            # Developer onboarding
├── RUNBOOK.md               # Operational procedures
├── examples/                # Code examples
└── diagrams/                # Architecture diagrams
```

**Priority**: P3 - MEDIUM
**Why**: Good documentation accelerates development and reduces confusion
