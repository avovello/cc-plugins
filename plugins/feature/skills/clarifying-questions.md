# Clarifying Questions Skill

## Purpose

Provides structured approach to identifying and presenting ambiguities that must be resolved before implementation.

## When to Load

Load this skill when:
- Transitioning from exploration to design
- Requirements are unclear
- Technical decisions need user input

## Importance

> **CRITICAL**: This is one of the most important phases. DO NOT proceed with implementation until questions are answered.

Asking questions early prevents:
- Wasted implementation effort
- Incorrect assumptions
- Rework and frustration

## Question Categories

### 1. Edge Cases

**What could go wrong?**

```markdown
## Edge Case Questions

1. What happens if the payment gateway times out?
   - Retry automatically?
   - Return error immediately?
   - Queue for later retry?

2. What if the user's session expires mid-payment?
   - Preserve payment attempt?
   - Start over?
   - Resume after re-auth?

3. What if the order no longer exists when payment completes?
   - Refund automatically?
   - Create orphan record?
   - Alert admin?
```

### 2. Error Handling

**How should errors be communicated?**

```markdown
## Error Handling Questions

1. Should error messages be user-friendly or technical?
   - User: "Payment failed, please try again"
   - Technical: "Gateway returned ERR_TIMEOUT after 30s"

2. Should failed payments be logged?
   - What level? Info/Warning/Error?
   - Include card details? (PCI compliance)

3. Retry behavior on failure?
   - Automatic retry count?
   - Backoff strategy?
   - User notification on retry?
```

### 3. Performance Requirements

**How fast and how much?**

```markdown
## Performance Questions

1. Expected load?
   - Peak payments per minute?
   - Concurrent users?

2. Response time requirements?
   - Maximum acceptable latency?
   - p95/p99 targets?

3. Caching strategy?
   - What can be cached?
   - Cache TTL?
   - Invalidation strategy?
```

### 4. Security Considerations

**What needs protection?**

```markdown
## Security Questions

1. Authentication requirements?
   - Require logged-in user?
   - Allow guest checkout?

2. Authorization levels?
   - Who can view payment history?
   - Who can issue refunds?

3. Data sensitivity?
   - What needs encryption at rest?
   - What can be logged?
   - PCI compliance requirements?
```

### 5. Backward Compatibility

**What existing behavior must be preserved?**

```markdown
## Compatibility Questions

1. API versioning?
   - New endpoint or modify existing?
   - Version in URL or header?

2. Database migrations?
   - Backward compatible schema?
   - Rollback strategy?

3. Feature flags?
   - Gradual rollout needed?
   - Kill switch required?
```

### 6. Design Preferences

**Style and pattern choices**

```markdown
## Design Questions

1. API response format?
   - Follow existing `{ success, data, error }` pattern?
   - Different format?

2. Naming conventions?
   - PaymentService or PaymentsService?
   - createPayment or processPayment?

3. File organization?
   - Single file or split by responsibility?
   - Separate folder for feature?
```

## Question Prioritization

### Must Answer (Blocking)
These must be answered before proceeding:
- Core functionality decisions
- Security requirements
- Breaking change implications

### Should Clarify (Recommended)
Important but can make reasonable assumptions:
- Error message wording
- Logging verbosity
- Cache TTL values

### Nice to Know (Optional)
Can defer to implementation time:
- Minor naming preferences
- Code organization details
- Documentation format

## Output Format

```markdown
# Questions Requiring Answers

## Must Answer (Blocking)

### 1. Payment Timeout Handling
When the payment gateway doesn't respond within timeout:
- **Option A**: Return error immediately
- **Option B**: Retry up to 3 times with backoff
- **Option C**: Queue for background retry and return pending status

**Impact**: Determines service architecture and user experience

### 2. Refund Authorization
Who should be able to issue refunds?
- **Option A**: Only admins via admin panel
- **Option B**: Customer service via internal API
- **Option C**: Self-service for users within 24 hours

**Impact**: Determines authorization model and UI requirements

---

## Should Clarify (Recommended)

### 3. Error Message Style
What level of detail in error messages?
- **Option A**: User-friendly: "Payment failed, please try again"
- **Option B**: Detailed: "Payment failed: Card declined (insufficient funds)"

**Default Assumption**: Option A (user-friendly) if not specified

### 4. Logging Verbosity
What payment events should be logged?
- **Option A**: All events (create, update, complete, fail)
- **Option B**: Only failures and completions
- **Option C**: Minimal (only failures)

**Default Assumption**: Option A (all events) if not specified

---

## Nice to Know (Optional)

### 5. Endpoint Naming
Preference for API endpoint naming?
- **Option A**: `/api/v1/payments` (REST standard)
- **Option B**: `/api/v1/checkout/pay` (action-based)

**Default Assumption**: Option A if not specified

---

## ⏸️ WAITING FOR ANSWERS

Please respond to the **Must Answer** questions before we proceed to architecture design.

The **Should Clarify** questions will use default assumptions if not answered.
```

## Integration with Feature Plugin

This skill is used in Phase 3 of the feature-dev command:

```markdown
## Phase 3: Clarifying Questions

1. Review exploration findings
2. Identify all ambiguities using this skill
3. Present questions in priority order
4. PAUSE and wait for user answers
5. Only proceed to Phase 4 after blocking questions answered
```

## Best Practices

1. **Ask Early**: Questions before implementation, not during
2. **Prioritize Clearly**: Blocking vs nice-to-know
3. **Provide Options**: Give concrete choices when possible
4. **Explain Impact**: Why does this decision matter?
5. **Default Assumptions**: State what you'll assume if not answered
6. **Group Logically**: Related questions together
7. **Be Comprehensive**: Better to ask too many than miss critical ones
