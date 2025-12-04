---
description: Documentation maintenance with automated API docs, architecture diagrams, and developer guides
---

# Document Command

**Purpose**: Documentation maintenance through automated API documentation, architecture diagrams, and comprehensive developer guides

## Overview

The document command ensures documentation stays synchronized with code. Research shows that **outdated documentation costs teams 20-30% of development time** in confusion and miscommunication.

**Key Distinction**: Document maintains existing documentation, while Audit generates initial documentation.

## Workflow

### Step 1: Analyze Code
- Scan API endpoints
- Analyze architecture
- Identify undocumented features
- Find outdated documentation

### Step 2: Generate Documentation
Run specialized agents:
- **api-documenter**: API endpoint documentation
- **architecture-documenter**: System architecture docs
- **onboarding-guide-creator**: New developer guides
- **runbook-writer**: Operational procedures

### Step 3: Add Examples
- Code examples for APIs
- Usage patterns
- Common scenarios
- Integration examples

### Step 4: Create Diagrams
- Architecture diagrams
- Sequence diagrams
- Data flow diagrams
- Entity relationship diagrams

### Step 5: Validate Consistency
- Check doc accuracy
- Verify examples work
- Ensure completeness

## Usage Examples

```bash
/document "update API documentation for /api/users endpoints"
/document "create architecture documentation"
/document "generate onboarding guide for new developers"
/document "create runbook for deployment process"
```

## Output

```
documentation/
├── API.md                     # API documentation
├── ARCHITECTURE.md            # System architecture
├── ONBOARDING.md             # Developer onboarding
├── RUNBOOK.md                # Operational procedures
├── examples/                 # Code examples
└── diagrams/                 # Architecture diagrams
```

**Priority**: P3 - MEDIUM
