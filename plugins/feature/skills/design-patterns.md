# Design Patterns Skill

## Purpose

Provides guidance on applying ubiquitous design patterns for solving recurring software design problems.

## When to Load

Load this skill when:
- Designing new components or features
- Solving common architectural problems
- Identifying reusable solutions
- Reviewing code for pattern opportunities

## Pattern Categories

### Creational Patterns
Focus on object creation mechanisms, providing flexibility and efficiency.

### Structural Patterns
Define how classes and objects are composed to form larger structures.

### Behavioral Patterns
Focus on communication and interaction between objects.

---

## Creational Patterns

### Factory Method

**Problem**: Need to create objects without specifying exact class.

**Solution**: Define interface for creating objects; let subclasses decide which class to instantiate.

**When to Use**:
- Don't know exact types ahead of time
- Want to delegate instantiation to subclasses
- Need to centralize complex creation logic

```markdown
## Factory Method Pattern

### Structure
Creator (abstract)
  └── factoryMethod(): Product
  └── operation(): uses Product

ConcreteCreatorA
  └── factoryMethod(): returns ProductA

ConcreteCreatorB
  └── factoryMethod(): returns ProductB

### Example: Notification Factory
interface Notification:
  send(message)

NotificationFactory:
  create(type: string): Notification

EmailNotification implements Notification
SMSNotification implements Notification
PushNotification implements Notification

// Usage
factory.create('email').send('Hello')
factory.create('sms').send('Hello')
```

**Benefits**:
- Loose coupling between creator and products
- Single Responsibility: creation logic in one place
- Open/Closed: add new products without changing existing code

---

### Singleton

**Problem**: Ensure a class has only one instance with global access.

**Solution**: Make constructor private; provide static method returning same instance.

**When to Use**:
- Exactly one instance needed (config, connection pool)
- Global access point required
- Lazy initialization desired

```markdown
## Singleton Pattern

### Structure
Singleton:
  - static instance: Singleton
  - private constructor()
  + static getInstance(): Singleton

### Example: Database Connection
class DatabaseConnection:
  private static instance: DatabaseConnection
  private connection: Connection

  private constructor():
    this.connection = createConnection()

  static getInstance():
    if (!instance):
      instance = new DatabaseConnection()
    return instance

// Usage - always same instance
const db1 = DatabaseConnection.getInstance()
const db2 = DatabaseConnection.getInstance()
// db1 === db2
```

**Caution**:
- Can introduce global state
- Makes testing harder (use dependency injection instead when possible)
- Consider thread safety in concurrent environments

---

### Builder

**Problem**: Construct complex objects step by step.

**Solution**: Separate construction from representation; same process creates different representations.

**When to Use**:
- Object has many optional parameters
- Construction involves multiple steps
- Want to create different representations

```markdown
## Builder Pattern

### Example: Query Builder
QueryBuilder:
  select(fields): this
  from(table): this
  where(condition): this
  orderBy(field): this
  limit(n): this
  build(): Query

// Usage
const query = new QueryBuilder()
  .select(['id', 'name'])
  .from('users')
  .where('active = true')
  .orderBy('created_at')
  .limit(10)
  .build()
```

---

## Structural Patterns

### Adapter

**Problem**: Incompatible interfaces need to work together.

**Solution**: Wrap existing class with new interface that client expects.

**When to Use**:
- Integrate with legacy or third-party code
- Interface doesn't match what client needs
- Want to create reusable class that works with unrelated interfaces

```markdown
## Adapter Pattern

### Structure
Target (interface client expects)
Adaptee (existing class with incompatible interface)
Adapter implements Target:
  - adaptee: Adaptee
  + request(): adapts adaptee.specificRequest()

### Example: Payment Gateway Adapter
// What our system expects
interface PaymentProcessor:
  charge(amount: Money, card: CardInfo): PaymentResult

// Third-party SDK (incompatible)
class StripeSDK:
  createCharge(cents: number, token: string): StripeResponse

// Adapter
class StripeAdapter implements PaymentProcessor:
  constructor(private stripe: StripeSDK)

  charge(amount: Money, card: CardInfo): PaymentResult:
    const token = this.tokenize(card)
    const response = this.stripe.createCharge(amount.cents, token)
    return this.mapResponse(response)
```

**Benefits**:
- Single Responsibility: conversion logic separate from business logic
- Open/Closed: new adapters without changing client code

---

### Decorator

**Problem**: Add responsibilities to objects dynamically.

**Solution**: Wrap objects with decorator classes that add behavior.

**When to Use**:
- Add behavior without modifying existing code
- Behavior should be removable
- Subclassing would create explosion of classes

```markdown
## Decorator Pattern

### Structure
Component (interface)
ConcreteComponent implements Component
Decorator implements Component:
  - wrapped: Component
  + operation(): adds behavior + calls wrapped.operation()

### Example: Logging Decorator
interface HttpClient:
  request(url): Response

BasicHttpClient implements HttpClient:
  request(url): fetch(url)

LoggingHttpClient implements HttpClient:
  constructor(private wrapped: HttpClient)
  request(url):
    console.log(`Requesting: ${url}`)
    const response = this.wrapped.request(url)
    console.log(`Response: ${response.status}`)
    return response

CachingHttpClient implements HttpClient:
  constructor(private wrapped: HttpClient, private cache: Cache)
  request(url):
    if (this.cache.has(url)):
      return this.cache.get(url)
    const response = this.wrapped.request(url)
    this.cache.set(url, response)
    return response

// Usage - compose behaviors
const client = new LoggingHttpClient(
  new CachingHttpClient(
    new BasicHttpClient(),
    cache
  )
)
```

**Benefits**:
- More flexible than inheritance
- Behaviors can be combined at runtime
- Single Responsibility: each decorator does one thing

---

### Facade

**Problem**: Complex subsystem with many interfaces.

**Solution**: Provide simplified interface to complex subsystem.

**When to Use**:
- Subsystem is complex with many classes
- Want to layer subsystems
- Decouple clients from subsystem implementation

```markdown
## Facade Pattern

### Example: Order Processing Facade
// Complex subsystem classes
InventoryService, PaymentService, ShippingService,
NotificationService, TaxCalculator, FraudDetector

// Simplified Facade
class OrderFacade:
  placeOrder(order: OrderRequest): OrderResult:
    // Coordinates all subsystems
    this.fraudDetector.check(order)
    this.inventory.reserve(order.items)
    this.taxCalculator.calculate(order)
    this.paymentService.charge(order)
    this.shippingService.schedule(order)
    this.notificationService.sendConfirmation(order)
    return OrderResult.success(order.id)

// Client only knows Facade
orderFacade.placeOrder(order)
```

---

## Behavioral Patterns

### Observer

**Problem**: Object state changes should notify dependent objects.

**Solution**: Define one-to-many dependency; when subject changes, observers are notified.

**When to Use**:
- Changes in one object require changing others
- Don't know how many objects need to change
- Object should notify without knowing who listens

```markdown
## Observer Pattern

### Structure
Subject:
  - observers: Observer[]
  + attach(observer)
  + detach(observer)
  + notify(): calls update() on all observers

Observer (interface):
  + update(subject)

### Example: Event System
class EventEmitter:
  private listeners: Map<string, Function[]>

  on(event: string, callback: Function):
    this.listeners.get(event).push(callback)

  emit(event: string, data: any):
    this.listeners.get(event).forEach(cb => cb(data))

// Usage
orderService.on('order.created', (order) => {
  inventoryService.reserve(order.items)
})

orderService.on('order.created', (order) => {
  emailService.sendConfirmation(order)
})

orderService.on('order.created', (order) => {
  analyticsService.track('purchase', order)
})
```

**Benefits**:
- Loose coupling between subject and observers
- Open/Closed: add new observers without modifying subject
- Supports broadcast communication

---

### Strategy

**Problem**: Need to select algorithm at runtime.

**Solution**: Define family of algorithms, encapsulate each, make interchangeable.

**When to Use**:
- Many related classes differ only in behavior
- Need different variants of an algorithm
- Algorithm should be selected at runtime

```markdown
## Strategy Pattern

### Structure
Context:
  - strategy: Strategy
  + setStrategy(strategy)
  + executeStrategy()

Strategy (interface):
  + execute()

ConcreteStrategyA, ConcreteStrategyB implement Strategy

### Example: Pricing Strategy
interface PricingStrategy:
  calculatePrice(basePrice: Money, customer: Customer): Money

RegularPricing implements PricingStrategy:
  calculatePrice(basePrice, customer):
    return basePrice

PremiumDiscount implements PricingStrategy:
  calculatePrice(basePrice, customer):
    return basePrice.multiply(0.9)  // 10% off

LoyaltyPricing implements PricingStrategy:
  calculatePrice(basePrice, customer):
    const discount = customer.loyaltyPoints * 0.001
    return basePrice.multiply(1 - discount)

// Usage
class PricingService:
  constructor(private strategy: PricingStrategy)

  setStrategy(strategy: PricingStrategy):
    this.strategy = strategy

  calculatePrice(basePrice, customer):
    return this.strategy.calculatePrice(basePrice, customer)

// Runtime selection
if (customer.isPremium):
  pricingService.setStrategy(new PremiumDiscount())
else:
  pricingService.setStrategy(new RegularPricing())
```

**Benefits**:
- Algorithms interchangeable at runtime
- Isolates algorithm implementation
- Eliminates conditional statements

---

### Command

**Problem**: Parameterize objects with operations; queue or log requests.

**Solution**: Encapsulate request as object with execute method.

**When to Use**:
- Parameterize objects with actions
- Queue, schedule, or log operations
- Support undo/redo functionality

```markdown
## Command Pattern

### Structure
Command (interface):
  + execute()
  + undo()

ConcreteCommand:
  - receiver
  + execute(): calls receiver action
  + undo(): reverses action

Invoker:
  - command: Command
  + executeCommand()

### Example: Order Operations
interface OrderCommand:
  execute(): void
  undo(): void

CreateOrderCommand:
  constructor(private order: Order, private repo: OrderRepository)
  execute(): this.repo.save(this.order)
  undo(): this.repo.delete(this.order.id)

CancelOrderCommand:
  constructor(private orderId: string, private repo: OrderRepository)
  private previousStatus: OrderStatus
  execute():
    const order = this.repo.find(this.orderId)
    this.previousStatus = order.status
    order.status = CANCELLED
    this.repo.save(order)
  undo():
    const order = this.repo.find(this.orderId)
    order.status = this.previousStatus
    this.repo.save(order)

// Command history for undo
class CommandHistory:
  private history: Command[] = []

  execute(command: Command):
    command.execute()
    this.history.push(command)

  undo():
    const command = this.history.pop()
    command?.undo()
```

---

## Pattern Selection Guide

```markdown
## When to Use Which Pattern

| Problem | Pattern |
|---------|---------|
| Create objects without specifying class | Factory Method |
| Single instance with global access | Singleton |
| Complex object construction | Builder |
| Incompatible interfaces | Adapter |
| Add behavior dynamically | Decorator |
| Simplify complex subsystem | Facade |
| Notify dependents of changes | Observer |
| Select algorithm at runtime | Strategy |
| Encapsulate operations | Command |
```

---

## Integration with code-architect

When designing features, identify pattern opportunities:

1. **Analyze Requirements**: What problems need solving?
2. **Match to Patterns**: Which patterns address those problems?
3. **Evaluate Trade-offs**: Is the pattern worth the complexity?
4. **Document Decisions**: Explain why pattern was chosen

## Best Practices

1. **Don't Over-Pattern**: Use patterns when they solve real problems
2. **Know the Trade-offs**: Patterns add complexity
3. **Follow Conventions**: Use standard pattern names
4. **Combine Patterns**: Patterns often work together
5. **Refactor to Patterns**: Let patterns emerge from code
