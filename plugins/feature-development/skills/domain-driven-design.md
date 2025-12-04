# Domain-Driven Design (DDD) Skill

## Purpose

Provides guidance on applying Domain-Driven Design principles for modeling software to match business domains.

## When to Load

Load this skill when:
- Designing complex business domains
- Defining module and service boundaries
- Collaborating with domain experts
- Modeling core business logic

## DDD Overview

Domain-Driven Design focuses on:
1. **Collaboration** with domain experts
2. **Modeling** software to reflect business reality
3. **Strategic** patterns for system organization
4. **Tactical** patterns for object modeling

---

## Strategic Design

### Ubiquitous Language

**Definition**: A shared language between developers and domain experts used consistently throughout code, documentation, and conversation.

**Purpose**:
- Reduces miscommunication
- Code reflects business concepts
- Domain experts can read and validate code

**Application**:
```markdown
## Ubiquitous Language: E-Commerce Domain

### Terms and Definitions
| Term | Definition | NOT |
|------|------------|-----|
| Order | A customer's request to purchase items | "Purchase", "Transaction" |
| Cart | Temporary collection before checkout | "Basket", "Bag" |
| SKU | Stock Keeping Unit - unique product identifier | "Product ID", "Item Code" |
| Fulfillment | Process of preparing and shipping order | "Delivery", "Shipping" |

### Usage in Code
// GOOD - uses ubiquitous language
class Order {
  addLineItem(sku: SKU, quantity: number)
  calculateSubtotal(): Money
  submitForFulfillment()
}

// BAD - inconsistent terminology
class Purchase {
  addProduct(productId: string, qty: number)
  getTotal(): number
  ship()
}
```

**Building Ubiquitous Language**:
1. Collaborate with domain experts
2. Document terms and definitions
3. Reject synonyms - one term per concept
4. Refactor code when language evolves

---

### Bounded Contexts

**Definition**: Explicit boundary within which a domain model is defined and applicable.

**Purpose**:
- Terms have specific meaning within context
- Different contexts can have different models
- Isolates complexity to manageable boundaries

**Application**:
```markdown
## Bounded Contexts: E-Commerce System

### Sales Context
- Order: customer's purchase request
- Customer: person making purchase
- Product: item being sold with price

### Fulfillment Context
- Shipment: package being delivered
- Recipient: person receiving delivery
- Item: physical thing being shipped

### Inventory Context
- Stock: quantity of product available
- Location: warehouse position
- Product: physical item with dimensions

Note: "Product" means different things in each context!
```

**Context Mapping**:
```
┌─────────────────┐         ┌─────────────────┐
│  Sales Context  │ ──────► │Fulfillment Ctx  │
│                 │         │                 │
│ Order ──────────┼─────────┼─► Shipment      │
│ Customer ───────┼─────────┼─► Recipient     │
│ LineItem ───────┼─────────┼─► Item          │
└─────────────────┘         └─────────────────┘
                │
                ▼
       ┌─────────────────┐
       │Inventory Context│
       │                 │
       │ Stock           │
       │ Reservation     │
       └─────────────────┘
```

**Integration Patterns**:
- **Shared Kernel**: Shared subset of domain model
- **Customer/Supplier**: Upstream/downstream relationship
- **Anticorruption Layer**: Translation layer between contexts
- **Published Language**: Well-documented API for integration

---

## Tactical Design

### Entities

**Definition**: Objects with unique identity that persists over time.

**Characteristics**:
- Has unique identifier
- Identity matters, not just attributes
- Can change over time but remains same entity

```markdown
## Entity Example: User

class User:
  id: UserId           // Identity
  email: Email         // Can change
  name: string         // Can change
  createdAt: DateTime  // Immutable

  // Identity comparison
  equals(other: User): boolean:
    return this.id.equals(other.id)

  // Behavior methods
  changeEmail(newEmail: Email): void
  updateName(newName: string): void
```

**Entity Guidelines**:
- ID assigned at creation, never changes
- Equality based on ID, not attributes
- Encapsulate behavior with attributes
- Keep entities focused on identity + behavior

---

### Value Objects

**Definition**: Objects defined by their attributes, not identity.

**Characteristics**:
- Immutable
- Equality based on attribute values
- No identity
- Replaceable

```markdown
## Value Object Examples

### Money
class Money:
  readonly amount: number
  readonly currency: Currency

  // Immutable - returns new instance
  add(other: Money): Money:
    if (this.currency != other.currency):
      throw new CurrencyMismatch()
    return new Money(this.amount + other.amount, this.currency)

  // Value equality
  equals(other: Money): boolean:
    return this.amount == other.amount &&
           this.currency == other.currency

### Address
class Address:
  readonly street: string
  readonly city: string
  readonly postalCode: string
  readonly country: Country

  // Immutable, validated at construction
  constructor(street, city, postalCode, country):
    this.validate(postalCode, country)
    // ... assign values

### Email
class Email:
  readonly value: string

  constructor(value: string):
    if (!this.isValid(value)):
      throw new InvalidEmail(value)
    this.value = value.toLowerCase()
```

**Value Object Guidelines**:
- Always immutable
- Validate at construction
- Operations return new instances
- Use for domain concepts without identity

---

### Aggregates

**Definition**: Cluster of entities and value objects with defined boundary and root entity.

**Purpose**:
- Enforce invariants (business rules)
- Transaction boundary
- Consistency boundary

```markdown
## Aggregate Example: Order

Order (Aggregate Root)
├── OrderId (Entity identity)
├── CustomerId (Reference to another aggregate)
├── OrderStatus (Value Object)
├── ShippingAddress (Value Object)
└── LineItems[] (Entity collection)
    ├── LineItemId
    ├── ProductId (Reference)
    ├── Quantity
    └── UnitPrice

### Aggregate Rules
class Order:  // Aggregate Root
  private items: LineItem[]

  // All access through root
  addItem(productId: ProductId, quantity: number, price: Money):
    // Enforce invariants
    if (this.status != DRAFT):
      throw new OrderNotModifiable()
    if (quantity <= 0):
      throw new InvalidQuantity()

    const item = new LineItem(productId, quantity, price)
    this.items.push(item)

  // Never expose internal entities directly
  getItems(): ReadonlyArray<LineItem>:
    return [...this.items]  // Return copy

  // Aggregate enforces all invariants
  submit():
    if (this.items.length == 0):
      throw new EmptyOrder()
    if (!this.shippingAddress):
      throw new MissingShippingAddress()
    this.status = SUBMITTED
```

**Aggregate Design Rules**:
1. Reference other aggregates by ID only
2. One transaction per aggregate
3. Keep aggregates small
4. Root controls all access
5. Enforce all invariants within aggregate

---

### Domain Services

**Definition**: Operations that don't naturally belong to any entity or value object.

**When to Use**:
- Operation involves multiple aggregates
- Significant domain logic doesn't fit in entities
- Stateless operation

```markdown
## Domain Service Example

// Doesn't belong to Order or Inventory
class OrderFulfillmentService:
  constructor(
    private inventoryRepository: InventoryRepository,
    private orderRepository: OrderRepository
  )

  fulfillOrder(orderId: OrderId): FulfillmentResult:
    const order = this.orderRepository.find(orderId)

    // Check inventory for all items
    for (item in order.items):
      const stock = this.inventoryRepository.findByProduct(item.productId)
      if (!stock.canFulfill(item.quantity)):
        return FulfillmentResult.insufficientStock(item.productId)

    // Reserve inventory
    for (item in order.items):
      const stock = this.inventoryRepository.findByProduct(item.productId)
      stock.reserve(item.quantity)
      this.inventoryRepository.save(stock)

    order.markAsFulfilling()
    this.orderRepository.save(order)

    return FulfillmentResult.success()
```

---

### Repositories

**Definition**: Abstraction for retrieving and persisting aggregates.

**Purpose**:
- Hide data access details
- Provide collection-like interface
- One repository per aggregate root

```markdown
## Repository Example

interface OrderRepository:
  find(id: OrderId): Order | null
  findByCustomer(customerId: CustomerId): Order[]
  save(order: Order): void
  delete(id: OrderId): void

// Implementation in infrastructure layer
class PostgresOrderRepository implements OrderRepository:
  find(id: OrderId): Order | null:
    const row = this.db.query('SELECT * FROM orders WHERE id = ?', id)
    if (!row) return null
    return this.hydrate(row)

  save(order: Order): void:
    // Handle insert or update
    // Persist aggregate root and all entities within
```

**Repository Guidelines**:
- One per aggregate root
- Return fully hydrated aggregates
- Interface in domain layer
- Implementation in infrastructure layer

---

### Domain Events

**Definition**: Record of something significant that happened in the domain.

**Purpose**:
- Communicate between aggregates
- Trigger side effects
- Audit trail

```markdown
## Domain Event Example

// Event definition
class OrderSubmitted:
  readonly orderId: OrderId
  readonly customerId: CustomerId
  readonly total: Money
  readonly occurredAt: DateTime

// Raising events
class Order:
  private events: DomainEvent[] = []

  submit():
    // ... validation
    this.status = SUBMITTED
    this.events.push(new OrderSubmitted(
      this.id,
      this.customerId,
      this.calculateTotal(),
      DateTime.now()
    ))

  pullEvents(): DomainEvent[]:
    const events = [...this.events]
    this.events = []
    return events

// Handling events (in application layer)
class OrderSubmittedHandler:
  handle(event: OrderSubmitted):
    this.emailService.sendConfirmation(event.customerId, event.orderId)
    this.inventoryService.reserveStock(event.orderId)
    this.analyticsService.trackPurchase(event)
```

---

## DDD Checklist

```markdown
## Strategic Design
- [ ] Ubiquitous language documented
- [ ] Bounded contexts identified
- [ ] Context map created
- [ ] Integration patterns defined

## Tactical Design
- [ ] Entities have unique identity
- [ ] Value objects are immutable
- [ ] Aggregates enforce invariants
- [ ] Repository per aggregate root
- [ ] Domain services for cross-aggregate logic
- [ ] Domain events for side effects

## Code Quality
- [ ] Code uses ubiquitous language
- [ ] No business logic in infrastructure
- [ ] Aggregates are transaction boundaries
- [ ] References between aggregates by ID only
```

---

## Integration with code-architect

When designing features, apply DDD:

1. **Identify Bounded Context**: Where does this feature belong?
2. **Define Ubiquitous Language**: What terms do domain experts use?
3. **Model Aggregates**: What are the consistency boundaries?
4. **Design Repositories**: How to persist aggregates?
5. **Plan Domain Events**: What events should be raised?

## Best Practices

1. **Collaborate with Experts**: DDD is about shared understanding
2. **Start with Language**: Get terminology right first
3. **Keep Aggregates Small**: Large aggregates cause problems
4. **Eventual Consistency**: Between aggregates is OK
5. **Let Model Evolve**: Refactor as understanding deepens
