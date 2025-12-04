# SOLID Principles Skill

## Purpose

Provides guidance on applying SOLID principles for designing maintainable, flexible, and robust software architecture.

## When to Load

Load this skill when:
- Designing new components or modules
- Refactoring existing code structure
- Reviewing architecture decisions
- Defining interfaces and abstractions

## The Five SOLID Principles

### S - Single Responsibility Principle (SRP)

**Definition**: A component should have only one reason to change.

**At Architecture Level**:
- Each module/service handles ONE business domain
- Changes from different stakeholders should affect different components
- If a change request could come from multiple business domains, the component is violating SRP

**Application**:
```markdown
## SRP Analysis

### Before (Violates SRP)
UserService handles:
- User authentication ← Security team changes
- User profile management ← Product team changes
- User notifications ← Marketing team changes

### After (Follows SRP)
- AuthenticationService: User login/logout/tokens
- ProfileService: User profile CRUD
- NotificationService: User notifications

Each service has ONE reason to change.
```

**Questions to Ask**:
- Who requests changes to this component?
- If multiple stakeholders, should it be split?
- Does this component have a single, cohesive purpose?

---

### O - Open/Closed Principle (OCP)

**Definition**: Software entities should be open for extension but closed for modification.

**At Architecture Level**:
- Add new functionality WITHOUT changing existing code
- Use interfaces and abstractions for extension points
- Plugin architectures exemplify OCP

**Application**:
```markdown
## OCP Analysis

### Before (Violates OCP)
PaymentService.processPayment(type):
  if type == 'credit': processCreditCard()
  if type == 'paypal': processPayPal()
  if type == 'crypto': processCrypto()  // Must modify to add

### After (Follows OCP)
interface PaymentProcessor:
  process(payment)

CreditCardProcessor implements PaymentProcessor
PayPalProcessor implements PaymentProcessor
CryptoProcessor implements PaymentProcessor  // Just add new class

PaymentService uses PaymentProcessor interface
```

**Extension Points to Design**:
- Strategy interfaces for algorithms
- Plugin systems for new capabilities
- Event handlers for new behaviors
- Factory methods for new types

---

### L - Liskov Substitution Principle (LSP)

**Definition**: Objects of a supertype should be replaceable with objects of subtypes without affecting correctness.

**At Architecture Level**:
- Services implementing common interfaces must be fully substitutable
- Don't strengthen preconditions or weaken postconditions in subtypes
- Subtypes must honor the contract of their supertypes

**Application**:
```markdown
## LSP Analysis

### Before (Violates LSP)
interface Bird:
  fly()

Penguin implements Bird:
  fly(): throw "Penguins can't fly"  // Violates LSP

### After (Follows LSP)
interface Bird:
  move()

FlyingBird extends Bird:
  fly()

Penguin implements Bird:
  move(): walk()

Eagle implements FlyingBird:
  move(): fly()
  fly(): soar()
```

**LSP Checklist**:
- [ ] Subtype doesn't throw unexpected exceptions
- [ ] Subtype doesn't have stricter preconditions
- [ ] Subtype honors all postconditions of supertype
- [ ] Subtype maintains invariants of supertype

---

### I - Interface Segregation Principle (ISP)

**Definition**: Clients should not be forced to depend on interfaces they don't use.

**At Architecture Level**:
- Many small, specific interfaces > one large interface
- Prevents unintended dependencies and side effects
- Clients only see methods they actually need

**Application**:
```markdown
## ISP Analysis

### Before (Violates ISP)
interface UserRepository:
  create(user)
  read(id)
  update(user)
  delete(id)
  generateReport()      // Not all clients need this
  sendNotification()    // Not all clients need this
  exportToCSV()         // Not all clients need this

### After (Follows ISP)
interface UserReader:
  read(id)

interface UserWriter:
  create(user)
  update(user)
  delete(id)

interface UserReporter:
  generateReport()
  exportToCSV()

interface UserNotifier:
  sendNotification()

// Clients depend only on what they need
ReadOnlyUserService uses UserReader
AdminUserService uses UserReader, UserWriter
ReportingService uses UserReporter
```

**Signs of ISP Violation**:
- Empty or stub method implementations
- "Not supported" exceptions
- Clients importing unused methods
- Fat interfaces with unrelated methods

---

### D - Dependency Inversion Principle (DIP)

**Definition**: High-level modules should not depend on low-level modules; both should depend on abstractions.

**At Architecture Level**:
- Business logic doesn't know about database implementation
- Core domain is independent of frameworks
- Dependencies point INWARD toward abstractions

**Application**:
```markdown
## DIP Analysis

### Before (Violates DIP)
OrderService (high-level):
  constructor():
    this.db = new MySQLDatabase()      // Concrete dependency
    this.email = new SendGridMailer()  // Concrete dependency

### After (Follows DIP)
interface DataStore:
  save(entity)
  find(id)

interface Mailer:
  send(message)

OrderService (high-level):
  constructor(dataStore: DataStore, mailer: Mailer):
    this.dataStore = dataStore  // Abstraction
    this.mailer = mailer        // Abstraction

// Low-level modules implement abstractions
MySQLDatabase implements DataStore
SendGridMailer implements Mailer
```

**DIP in Practice**:
- Define interfaces in the high-level module
- Low-level modules implement those interfaces
- Use dependency injection to wire components
- Core business logic has ZERO framework imports

---

## SOLID Checklist for Architecture Design

```markdown
## SOLID Compliance Check

### Single Responsibility
- [ ] Each component has ONE business reason to change
- [ ] Components are named after their single purpose
- [ ] No "Utility" or "Helper" classes doing multiple things

### Open/Closed
- [ ] New features can be added via new classes/modules
- [ ] Existing code doesn't need modification for extensions
- [ ] Extension points are clearly defined (interfaces, plugins)

### Liskov Substitution
- [ ] All implementations of an interface are substitutable
- [ ] No subtype throws "not implemented" exceptions
- [ ] Contracts are fully honored by all implementations

### Interface Segregation
- [ ] Interfaces are small and focused
- [ ] Clients don't depend on methods they don't use
- [ ] No "fat" interfaces with unrelated methods

### Dependency Inversion
- [ ] High-level modules depend on abstractions
- [ ] Low-level modules implement abstractions
- [ ] Business logic is framework-independent
- [ ] Dependencies are injected, not instantiated
```

## Integration with code-architect

When designing architecture, apply SOLID as follows:

1. **Component Design**: Use SRP to define component boundaries
2. **Extension Points**: Apply OCP to identify where flexibility is needed
3. **Interface Design**: Use ISP to create focused contracts
4. **Dependency Management**: Apply DIP to decouple layers
5. **Substitutability**: Verify LSP for all interface implementations

## Best Practices

1. **Start with SRP**: Get component boundaries right first
2. **Design for Extension**: Identify likely change points early
3. **Keep Interfaces Small**: Easier to implement and maintain
4. **Inject Dependencies**: Never `new` concrete classes in business logic
5. **Test Substitutability**: Unit tests reveal LSP violations
