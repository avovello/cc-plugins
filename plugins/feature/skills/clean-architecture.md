# Clean Architecture Skill

## Purpose

Provides guidance on applying Clean Architecture principles for building systems that are independent of frameworks, databases, and UI.

## When to Load

Load this skill when:
- Designing new feature architecture
- Structuring application layers
- Defining boundaries between components
- Ensuring business logic independence

## The Dependency Rule

> **Source code dependencies must only point INWARD.**

Inner layers know nothing about outer layers. The business rules don't know about the database or the web framework.

```
┌─────────────────────────────────────────────────────────────┐
│                   Frameworks & Drivers                       │
│  ┌─────────────────────────────────────────────────────┐    │
│  │               Interface Adapters                     │    │
│  │  ┌─────────────────────────────────────────────┐    │    │
│  │  │              Use Cases                       │    │    │
│  │  │  ┌─────────────────────────────────────┐    │    │    │
│  │  │  │            Entities                  │    │    │    │
│  │  │  │         (Business Rules)             │    │    │    │
│  │  │  └─────────────────────────────────────┘    │    │    │
│  │  └─────────────────────────────────────────────┘    │    │
│  └─────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────┘

Dependencies point INWARD →→→
```

## The Four Layers

### Layer 1: Entities (Innermost)

**Purpose**: Enterprise-wide business rules and core domain objects.

**Characteristics**:
- Most general, highest-level rules
- Least likely to change
- NO dependencies on outer layers
- Pure business logic, no I/O

**Contents**:
```markdown
## Entities Layer

### Domain Objects
- User: id, email, role, permissions
- Order: id, items, total, status
- Payment: id, amount, method, status

### Business Rules
- User.canAccessResource(resource): boolean
- Order.calculateTotal(): Money
- Payment.isRefundable(): boolean

### Value Objects
- Money: amount, currency, operations
- Email: validation, formatting
- Address: validation, formatting
```

**Example**:
```typescript
// entities/Order.ts - NO external dependencies
class Order {
  constructor(
    public readonly id: OrderId,
    public readonly items: OrderItem[],
    public status: OrderStatus
  ) {}

  calculateTotal(): Money {
    return this.items.reduce(
      (sum, item) => sum.add(item.price.multiply(item.quantity)),
      Money.zero()
    );
  }

  canBeCancelled(): boolean {
    return this.status === OrderStatus.PENDING;
  }
}
```

---

### Layer 2: Use Cases

**Purpose**: Application-specific business rules that orchestrate data flow.

**Characteristics**:
- Define what the application DOES
- Orchestrate entities to achieve goals
- Input/Output boundaries defined here
- Independent of delivery mechanism

**Contents**:
```markdown
## Use Cases Layer

### Application Services
- CreateOrderUseCase: validates, creates order, returns result
- ProcessPaymentUseCase: coordinates payment flow
- CancelOrderUseCase: validates cancellation, updates status

### Input/Output Ports
- CreateOrderInput: { userId, items, shippingAddress }
- CreateOrderOutput: { orderId, total, estimatedDelivery }

### Boundaries (Interfaces)
- OrderRepository: save, find, update (interface only)
- PaymentGateway: charge, refund (interface only)
- NotificationSender: send (interface only)
```

**Example**:
```typescript
// usecases/CreateOrderUseCase.ts
class CreateOrderUseCase {
  constructor(
    private orderRepository: OrderRepository,  // Interface
    private inventoryService: InventoryService // Interface
  ) {}

  async execute(input: CreateOrderInput): Promise<CreateOrderOutput> {
    // Validate inventory
    await this.inventoryService.checkAvailability(input.items);

    // Create domain object
    const order = new Order(
      OrderId.generate(),
      input.items,
      OrderStatus.PENDING
    );

    // Persist via interface
    await this.orderRepository.save(order);

    return {
      orderId: order.id.value,
      total: order.calculateTotal().format(),
      estimatedDelivery: this.calculateDelivery(input.shippingAddress)
    };
  }
}
```

---

### Layer 3: Interface Adapters

**Purpose**: Convert data between use cases and external agents.

**Characteristics**:
- Controllers handle HTTP requests
- Presenters format responses
- Gateways implement repository interfaces
- Data format conversion happens here

**Contents**:
```markdown
## Interface Adapters Layer

### Controllers (Input)
- OrderController: HTTP → Use Case Input
- PaymentController: HTTP → Use Case Input
- WebhookController: External Events → Use Case Input

### Presenters (Output)
- OrderPresenter: Use Case Output → HTTP Response
- ErrorPresenter: Exceptions → Error Response

### Gateways (Data Access)
- PostgresOrderRepository implements OrderRepository
- StripePaymentGateway implements PaymentGateway
- SendGridNotificationSender implements NotificationSender

### Data Mappers
- OrderMapper: Entity ↔ Database Row
- PaymentMapper: Entity ↔ API Response
```

**Example**:
```typescript
// adapters/controllers/OrderController.ts
class OrderController {
  constructor(private createOrderUseCase: CreateOrderUseCase) {}

  async create(req: HttpRequest): Promise<HttpResponse> {
    // Convert HTTP to Use Case Input
    const input: CreateOrderInput = {
      userId: req.user.id,
      items: req.body.items.map(this.mapItem),
      shippingAddress: req.body.shippingAddress
    };

    // Execute Use Case
    const output = await this.createOrderUseCase.execute(input);

    // Convert Use Case Output to HTTP
    return {
      status: 201,
      body: {
        orderId: output.orderId,
        total: output.total,
        estimatedDelivery: output.estimatedDelivery
      }
    };
  }
}
```

---

### Layer 4: Frameworks & Drivers (Outermost)

**Purpose**: External tools, frameworks, and infrastructure details.

**Characteristics**:
- Database drivers and ORMs
- Web frameworks (Express, FastAPI, etc.)
- External service SDKs
- Configuration and bootstrap code

**Contents**:
```markdown
## Frameworks & Drivers Layer

### Web Framework
- Express/FastAPI/Spring routes
- Middleware configuration
- Request/Response handling

### Database
- PostgreSQL/MySQL connection
- Prisma/TypeORM/SQLAlchemy
- Migration scripts

### External Services
- Stripe SDK
- AWS SDK
- SendGrid SDK

### Configuration
- Environment variables
- Dependency injection setup
- Application bootstrap
```

**Example**:
```typescript
// frameworks/web/routes.ts
import express from 'express';
import { OrderController } from '../../adapters/controllers/OrderController';
import { CreateOrderUseCase } from '../../usecases/CreateOrderUseCase';
import { PostgresOrderRepository } from '../../adapters/gateways/PostgresOrderRepository';

const router = express.Router();

// Dependency Injection at framework level
const orderRepository = new PostgresOrderRepository(database);
const createOrderUseCase = new CreateOrderUseCase(orderRepository);
const orderController = new OrderController(createOrderUseCase);

router.post('/orders', (req, res) => orderController.create(req, res));
```

---

## Directory Structure

```
src/
├── entities/                 # Layer 1: Business Rules
│   ├── Order.ts
│   ├── User.ts
│   ├── Payment.ts
│   └── value-objects/
│       ├── Money.ts
│       └── Email.ts
│
├── usecases/                 # Layer 2: Application Logic
│   ├── CreateOrderUseCase.ts
│   ├── ProcessPaymentUseCase.ts
│   ├── ports/               # Interfaces for outer layers
│   │   ├── OrderRepository.ts
│   │   └── PaymentGateway.ts
│   └── dto/                 # Input/Output definitions
│       ├── CreateOrderInput.ts
│       └── CreateOrderOutput.ts
│
├── adapters/                 # Layer 3: Interface Adapters
│   ├── controllers/
│   │   └── OrderController.ts
│   ├── presenters/
│   │   └── OrderPresenter.ts
│   └── gateways/
│       ├── PostgresOrderRepository.ts
│       └── StripePaymentGateway.ts
│
└── frameworks/               # Layer 4: External Details
    ├── web/
    │   ├── app.ts
    │   └── routes.ts
    ├── database/
    │   ├── connection.ts
    │   └── migrations/
    └── config/
        └── environment.ts
```

---

## Clean Architecture Checklist

```markdown
## Dependency Rule Compliance

### Entities (Layer 1)
- [ ] No imports from use cases, adapters, or frameworks
- [ ] Contains only pure business logic
- [ ] No I/O operations (database, HTTP, file system)

### Use Cases (Layer 2)
- [ ] Only imports from entities
- [ ] Defines interfaces for external dependencies
- [ ] No concrete implementations of I/O

### Adapters (Layer 3)
- [ ] Implements interfaces defined in use cases
- [ ] Converts between external formats and use case formats
- [ ] No business logic (only transformation)

### Frameworks (Layer 4)
- [ ] Only configures and wires components
- [ ] Contains all concrete infrastructure
- [ ] Business logic is completely absent

## Testability Check
- [ ] Entities testable with NO mocks
- [ ] Use cases testable with mock repositories
- [ ] Can swap database without changing use cases
- [ ] Can swap web framework without changing business logic
```

---

## Integration with code-architect

When designing features, apply Clean Architecture:

1. **Start with Entities**: Define core business rules first
2. **Design Use Cases**: Define what the application does
3. **Define Ports**: Create interfaces for external dependencies
4. **Plan Adapters**: Design how external world connects
5. **Defer Framework Decisions**: Choose frameworks last

## Best Practices

1. **Dependencies Flow Inward**: Never import from outer layers
2. **Business Logic is Framework-Free**: No Express, Django, etc. in entities
3. **Use Interfaces at Boundaries**: Adapters implement, use cases consume
4. **Test from Inside Out**: Entities first, then use cases
5. **Defer Decisions**: Database choice can wait until adapters layer
