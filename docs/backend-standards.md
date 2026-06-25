---
description: Backend development standards, best practices, and conventions for .NET applications with event-driven microservices architecture, including Domain-Driven Design, SOLID principles, architecture patterns, API design, and testing practices
globs: ["src/**/*.cs", "**/*.csproj", "**/*.sln", "**/appsettings*.json"]
alwaysApply: true
---

# Backend Project Standards and Best Practices

## Table of Contents

- [Overview](#overview)
- [Technology Stack](#technology-stack)
  - [Core Technologies](#core-technologies)
  - [Database & ORM](#database--orm)
  - [Messaging & Events](#messaging--events)
  - [Testing Framework](#testing-framework)
  - [Development Tools](#development-tools)
- [Architecture Overview](#architecture-overview)
  - [Event-Driven Microservices](#event-driven-microservices)
  - [Domain-Driven Design (DDD)](#domain-driven-design-ddd)
  - [Layered Architecture](#layered-architecture)
  - [Project Structure](#project-structure)
- [Domain-Driven Design Principles](#domain-driven-design-principles)
  - [Entities](#entities)
  - [Value Objects](#value-objects)
  - [Aggregates](#aggregates)
  - [Repositories](#repositories)
  - [Domain Services](#domain-services)
  - [Domain Events](#domain-events)
  - [Additional Recommendations](#additional-recommendations)
- [SOLID and DRY Principles](#solid-and-dry-principles)
  - [Single Responsibility Principle (SRP)](#single-responsibility-principle-srp)
  - [Open/Closed Principle (OCP)](#openclosed-principle-ocp)
  - [Liskov Substitution Principle (LSP)](#liskov-substitution-principle-lsp)
  - [Interface Segregation Principle (ISP)](#interface-segregation-principle-isp)
  - [Dependency Inversion Principle (DIP)](#dependency-inversion-principle-dip)
  - [DRY (Don't Repeat Yourself)](#dry-dont-repeat-yourself)
- [Coding Standards](#coding-standards)
  - [Language and Naming Conventions](#language-and-naming-conventions)
  - [C# Usage](#c-usage)
  - [Error Handling](#error-handling)
  - [Validation Patterns](#validation-patterns)
  - [Logging Standards](#logging-standards)
- [API Design Standards](#api-design-standards)
  - [REST Endpoints](#rest-endpoints)
  - [Request/Response Patterns](#requestresponse-patterns)
  - [Error Response Format](#error-response-format)
  - [CORS Configuration](#cors-configuration)
- [Database Patterns](#database-patterns)
  - [Entity Framework Core](#entity-framework-core)
  - [Migrations](#migrations)
  - [Repository Pattern](#repository-pattern)
- [Event-Driven Patterns](#event-driven-patterns)
  - [Event Publishing](#event-publishing)
  - [Event Consumption](#event-consumption)
  - [Event Sourcing](#event-sourcing)
  - [CQRS Pattern](#cqrs-pattern)
- [Testing Standards](#testing-standards)
  - [Unit Testing](#unit-testing)
  - [Integration Testing](#integration-testing)
  - [Test Coverage Requirements](#test-coverage-requirements)
  - [Mocking Standards](#mocking-standards)
- [Performance Best Practices](#performance-best-practices)
  - [Database Query Optimization](#database-query-optimization)
  - [Async/Await Patterns](#asyncawait-patterns)
  - [Caching Strategies](#caching-strategies)
- [Security Best Practices](#security-best-practices)
  - [Input Validation](#input-validation)
  - [Configuration Management](#configuration-management)
  - [Dependency Injection](#dependency-injection)
- [Development Workflow](#development-workflow)
  - [Git Workflow](#git-workflow)
  - [Development Scripts](#development-scripts)
  - [Code Quality](#code-quality)
- [Containerization & Deployment](#containerization--deployment)
  - [Docker Configuration](#docker-configuration)
  - [Kubernetes Deployment](#kubernetes-deployment)

---

## Overview

This document outlines the best practices, conventions, and standards for backend applications built with .NET, PostgreSQL, and event-driven microservices architecture. The backend follows Domain-Driven Design (DDD) principles and implements a layered architecture with asynchronous event-driven communication to ensure code consistency, maintainability, and scalability.

## Technology Stack

### Core Technologies
- **.NET SDK**: Version 6.0 or higher (8.0 recommended)
- **C#**: Latest language version with nullable reference types enabled
- **ASP.NET Core**: Web API framework for RESTful services
- **Dependency Injection**: Built-in .NET DI container

### Database & ORM
- **PostgreSQL**: Version 12 or higher for relational data storage
- **Entity Framework Core**: Modern ORM with code-first migrations
- **Npgsql**: PostgreSQL provider for Entity Framework Core
- **Dapper** (optional): For performance-critical queries

### Messaging & Events
- **Message Broker**: RabbitMQ, Azure Service Bus, Apache Kafka, or similar
- **MassTransit** or **NServiceBus**: Message bus abstraction
- **Event Store** (optional): For event sourcing patterns
- **Redis**: For caching and pub/sub scenarios

### Testing Framework
- **xUnit**: Primary testing framework
- **NUnit** or **MSTest**: Alternative testing frameworks
- **Moq**: Mocking framework for unit tests
- **FluentAssertions**: Assertion library for readable tests
- **TestContainers**: For integration testing with real dependencies
- **Coverage Threshold**: 90% for branches, functions, lines, and statements

### Development Tools
- **Visual Studio** or **Visual Studio Code**: Primary IDEs
- **Rider**: Alternative IDE
- **dotnet CLI**: Command-line tools for building, testing, and running
- **StyleCop** or **EditorConfig**: Code style enforcement
- **SonarAnalyzer**: Static code analysis

## Architecture Overview

### Event-Driven Microservices

The system follows an event-driven microservices architecture where services communicate asynchronously through events and messages.

**Key Characteristics:**
- **Service Autonomy**: Each microservice owns its data and business logic
- **Asynchronous Communication**: Services communicate via message brokers
- **Event-Driven**: State changes trigger domain events consumed by interested services
- **Eventual Consistency**: Data consistency achieved through event propagation
- **Resilience**: Services continue operating even when other services are unavailable

**Communication Patterns:**
- **Event Notification**: Services publish events when domain state changes
- **Event-Carried State Transfer**: Events contain sufficient data for consumers
- **Command Messages**: Direct service-to-service commands for orchestration
- **Request-Response**: Synchronous patterns when immediate response needed

### Domain-Driven Design (DDD)

Domain-Driven Design is a methodology that focuses on modeling software according to business logic and domain knowledge. By centering development on a deep understanding of the domain, DDD facilitates the creation of complex systems.

**Benefits:**
- **Improved Communication**: Promotes a common language (Ubiquitous Language) between developers and domain experts
- **Clear Domain Models**: Helps build models that accurately reflect business rules and processes
- **High Maintainability**: By dividing the system into bounded contexts and subdomains, it facilitates maintenance and software evolution
- **Event-Driven Integration**: Domain events enable loose coupling between bounded contexts

### Layered Architecture

Each microservice follows a layered DDD architecture:

**Presentation Layer** (`Presentation/` or `API/`)
- Controllers handle HTTP requests/responses
- API models for request/response DTOs
- Middleware for cross-cutting concerns

**Application Layer** (`Application/`)
- Application services orchestrate business workflows
- Command and Query handlers (CQRS pattern)
- DTOs for data transfer between layers
- Validators for input validation
- Event handlers for processing domain events

**Domain Layer** (`Domain/`)
- Entities and Value Objects define core business concepts
- Aggregates enforce business invariants
- Domain Services contain business logic spanning multiple entities
- Repository interfaces define data access contracts
- Domain Events represent significant business occurrences
- Pure business logic without external dependencies

**Infrastructure Layer** (`Infrastructure/`)
- Entity Framework DbContext implementations
- Repository implementations
- Message bus integrations (RabbitMQ, Kafka, etc.)
- External service integrations
- Logging, caching, and other technical concerns

### Project Structure

```
src/
├── ServiceName.API/                    # API/Presentation layer
│   ├── Controllers/                    # HTTP request handlers
│   ├── Middleware/                     # Custom middleware
│   ├── Program.cs                      # Application entry point
│   └── appsettings.json               # Configuration
├── ServiceName.Application/            # Application layer
│   ├── Commands/                       # Command handlers (CQRS)
│   ├── Queries/                        # Query handlers (CQRS)
│   ├── DTOs/                          # Data transfer objects
│   ├── Services/                       # Application services
│   ├── Validators/                     # Input validators
│   ├── EventHandlers/                  # Domain event handlers
│   └── Interfaces/                     # Application interfaces
├── ServiceName.Domain/                 # Domain layer
│   ├── Entities/                       # Domain entities
│   ├── ValueObjects/                   # Value objects
│   ├── Aggregates/                     # Aggregate roots
│   ├── Events/                         # Domain events
│   ├── Services/                       # Domain services
│   ├── Repositories/                   # Repository interfaces
│   └── Exceptions/                     # Domain exceptions
├── ServiceName.Infrastructure/         # Infrastructure layer
│   ├── Data/                          
│   │   ├── DbContext.cs               # EF Core context
│   │   ├── Configurations/            # Entity configurations
│   │   └── Migrations/                # Database migrations
│   ├── Repositories/                   # Repository implementations
│   ├── MessageBus/                     # Message broker integration
│   ├── ExternalServices/               # Third-party integrations
│   └── Logging/                        # Logging configuration
├── ServiceName.Tests.Unit/             # Unit tests
│   ├── Domain/                         # Domain layer tests
│   ├── Application/                    # Application layer tests
│   └── API/                           # Controller tests
└── ServiceName.Tests.Integration/      # Integration tests
    ├── API/                           # API integration tests
    └── Infrastructure/                 # Infrastructure tests
```

## Domain-Driven Design Principles

### Entities

Entities are objects with a distinct identity that persists over time.

**Before:**
```csharp
// Previously, entity data might have been handled as a simple POCO without behavior
public class Entity
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string Email { get; set; }
}
```

**After:**
```csharp
public class Entity
{
    public int Id { get; private set; }
    public string FirstName { get; private set; }
    public string LastName { get; private set; }
    public string Email { get; private set; }
    
    // Constructor enforces valid state
    public Entity(string firstName, string lastName, string email)
    {
        if (string.IsNullOrWhiteSpace(firstName))
            throw new ArgumentException("First name cannot be empty", nameof(firstName));
            
        FirstName = firstName;
        LastName = lastName;
        SetEmail(email);
    }
    
    // Methods encapsulate business logic
    public void SetEmail(string email)
    {
        if (!email.Contains("@"))
            throw new ArgumentException("Invalid email format", nameof(email));
            
        Email = email;
    }
    
    // For EF Core
    private Entity() { }
}
```

**Explanation**: `Entity` is an entity because it has a unique identifier (`Id`) that distinguishes it from other entities. It encapsulates business logic and enforces invariants.

**Best Practice**: Entities should:
- Have private setters to enforce encapsulation
- Validate state through methods rather than allowing arbitrary property changes
- Include a private parameterless constructor for ORM compatibility

### Value Objects

Value Objects describe aspects of the domain without conceptual identity. They are defined by their attributes rather than an identifier.

**Before:**
```csharp
// Handling address as primitive properties
public class Entity
{
    public string Street { get; set; }
    public string City { get; set; }
    public string PostalCode { get; set; }
    public string Country { get; set; }
}
```

**After:**
```csharp
public class Address : ValueObject
{
    public string Street { get; private set; }
    public string City { get; private set; }
    public string PostalCode { get; private set; }
    public string Country { get; private set; }
    
    public Address(string street, string city, string postalCode, string country)
    {
        Street = street ?? throw new ArgumentNullException(nameof(street));
        City = city ?? throw new ArgumentNullException(nameof(city));
        PostalCode = postalCode ?? throw new ArgumentNullException(nameof(postalCode));
        Country = country ?? throw new ArgumentNullException(nameof(country));
    }
    
    protected override IEnumerable<object> GetEqualityComponents()
    {
        yield return Street;
        yield return City;
        yield return PostalCode;
        yield return Country;
    }
    
    // Private constructor for EF Core
    private Address() { }
}

// Base class for value objects
public abstract class ValueObject
{
    protected abstract IEnumerable<object> GetEqualityComponents();
    
    public override bool Equals(object obj)
    {
        if (obj == null || obj.GetType() != GetType())
            return false;
            
        var other = (ValueObject)obj;
        return GetEqualityComponents().SequenceEqual(other.GetEqualityComponents());
    }
    
    public override int GetHashCode()
    {
        return GetEqualityComponents()
            .Select(x => x?.GetHashCode() ?? 0)
            .Aggregate((x, y) => x ^ y);
    }
}
```

**Explanation**: `Address` is a Value Object because it's defined by its attributes, not an identity. Two addresses with the same values are considered equal.

**Recommendation**: Value Objects should be immutable and implement value-based equality.

### Aggregates

Aggregates are clusters of objects that must be treated as a unit. They have a root entity that enforces invariants and consistency boundaries.

**Before:**
```csharp
// Entity and related data handled separately
public class Order
{
    public int Id { get; set; }
    public decimal Total { get; set; }
}

public class OrderItem
{
    public int Id { get; set; }
    public int OrderId { get; set; }
    public decimal Price { get; set; }
}
```

**After:**
```csharp
public class Order // Aggregate Root
{
    private readonly List<OrderItem> _items = new();
    
    public int Id { get; private set; }
    public IReadOnlyCollection<OrderItem> Items => _items.AsReadOnly();
    public decimal Total { get; private set; }
    
    public Order()
    {
        // EF Core constructor
    }
    
    public void AddItem(string productName, decimal price, int quantity)
    {
        if (price <= 0)
            throw new ArgumentException("Price must be positive", nameof(price));
            
        var item = new OrderItem(productName, price, quantity);
        _items.Add(item);
        RecalculateTotal();
    }
    
    public void RemoveItem(int itemId)
    {
        var item = _items.FirstOrDefault(i => i.Id == itemId);
        if (item == null)
            throw new InvalidOperationException("Item not found");
            
        _items.Remove(item);
        RecalculateTotal();
    }
    
    private void RecalculateTotal()
    {
        Total = _items.Sum(i => i.Price * i.Quantity);
    }
}

public class OrderItem // Part of Order aggregate
{
    public int Id { get; private set; }
    public string ProductName { get; private set; }
    public decimal Price { get; private set; }
    public int Quantity { get; private set; }
    
    internal OrderItem(string productName, decimal price, int quantity)
    {
        ProductName = productName;
        Price = price;
        Quantity = quantity;
    }
    
    private OrderItem() { } // EF Core
}
```

**Explanation**: `Order` acts as an aggregate root that contains order items. All operations that modify items go through the `Order` aggregate root to maintain consistency.

**Recommendation**: 
- Aggregates should maintain transactional consistency boundaries
- Only aggregate roots should be accessed directly by repositories
- Changes to entities within an aggregate should go through the root

### Repositories

Repositories provide interfaces for accessing aggregates and entities, encapsulating data access logic.

**Before:**
```csharp
// Direct database access without abstraction
public class EntityService
{
    private readonly DbContext _context;
    
    public Entity GetById(int id)
    {
        return _context.Entities.Find(id);
    }
}
```

**After:**
```csharp
// Domain layer - Repository interface
public interface IEntityRepository
{
    Task<Entity> GetByIdAsync(int id);
    Task<IEnumerable<Entity>> GetAllAsync();
    Task<Entity> AddAsync(Entity entity);
    Task UpdateAsync(Entity entity);
    Task DeleteAsync(int id);
}

// Infrastructure layer - Repository implementation
public class EntityRepository : IEntityRepository
{
    private readonly ApplicationDbContext _context;
    
    public EntityRepository(ApplicationDbContext context)
    {
        _context = context ?? throw new ArgumentNullException(nameof(context));
    }
    
    public async Task<Entity> GetByIdAsync(int id)
    {
        return await _context.Entities
            .Include(e => e.RelatedItems)
            .FirstOrDefaultAsync(e => e.Id == id);
    }
    
    public async Task<IEnumerable<Entity>> GetAllAsync()
    {
        return await _context.Entities
            .Include(e => e.RelatedItems)
            .ToListAsync();
    }
    
    public async Task<Entity> AddAsync(Entity entity)
    {
        await _context.Entities.AddAsync(entity);
        await _context.SaveChangesAsync();
        return entity;
    }
    
    public async Task UpdateAsync(Entity entity)
    {
        _context.Entities.Update(entity);
        await _context.SaveChangesAsync();
    }
    
    public async Task DeleteAsync(int id)
    {
        var entity = await GetByIdAsync(id);
        if (entity != null)
        {
            _context.Entities.Remove(entity);
            await _context.SaveChangesAsync();
        }
    }
}
```

**Explanation**: `EntityRepository` provides a clear interface for accessing entity data, encapsulating EF Core logic.

**Recommendation**: 
- Define repository interfaces in the Domain layer
- Implement repositories in the Infrastructure layer
- Use dependency injection to provide repository implementations
- Repositories should work with aggregate roots, not individual entities

### Domain Services

Domain Services contain business logic that doesn't naturally belong to an entity or value object.

**Before:**
```csharp
// Loose functions to handle business logic
public static class BusinessLogic
{
    public static int CalculateAge(DateTime birthDate)
    {
        var today = DateTime.Today;
        var age = today.Year - birthDate.Year;
        if (birthDate.Date > today.AddYears(-age)) age--;
        return age;
    }
}
```

**After:**
```csharp
public interface IEntityDomainService
{
    int CalculateAge(Entity entity);
    bool CanPromote(Entity entity);
}

public class EntityDomainService : IEntityDomainService
{
    public int CalculateAge(Entity entity)
    {
        var today = DateTime.Today;
        var age = today.Year - entity.BirthDate.Year;
        if (entity.BirthDate.Date > today.AddYears(-age)) age--;
        return age;
    }
    
    public bool CanPromote(Entity entity)
    {
        var age = CalculateAge(entity);
        return age >= 18 && entity.Status == EntityStatus.Active;
    }
}
```

**Explanation**: `EntityDomainService` encapsulates business logic that involves entity operations but doesn't belong to a single entity.

### Domain Events

Domain events represent something significant that happened in the domain. They enable loose coupling between aggregates and bounded contexts.

**Event Definition:**
```csharp
public abstract class DomainEvent
{
    public DateTime OccurredOn { get; protected set; }
    
    protected DomainEvent()
    {
        OccurredOn = DateTime.UtcNow;
    }
}

public class EntityCreatedEvent : DomainEvent
{
    public int EntityId { get; }
    public string FirstName { get; }
    public string LastName { get; }
    public string Email { get; }
    
    public EntityCreatedEvent(int entityId, string firstName, string lastName, string email)
    {
        EntityId = entityId;
        FirstName = firstName;
        LastName = lastName;
        Email = email;
    }
}
```

**Event Publishing in Aggregate:**
```csharp
public abstract class AggregateRoot
{
    private readonly List<DomainEvent> _domainEvents = new();
    
    public IReadOnlyCollection<DomainEvent> DomainEvents => _domainEvents.AsReadOnly();
    
    protected void AddDomainEvent(DomainEvent domainEvent)
    {
        _domainEvents.Add(domainEvent);
    }
    
    public void ClearDomainEvents()
    {
        _domainEvents.Clear();
    }
}

public class Entity : AggregateRoot
{
    public int Id { get; private set; }
    public string FirstName { get; private set; }
    public string LastName { get; private set; }
    public string Email { get; private set; }
    
    public static Entity Create(string firstName, string lastName, string email)
    {
        var entity = new Entity(firstName, lastName, email);
        entity.AddDomainEvent(new EntityCreatedEvent(
            entity.Id, 
            entity.FirstName, 
            entity.LastName, 
            entity.Email));
        return entity;
    }
    
    private Entity(string firstName, string lastName, string email)
    {
        FirstName = firstName;
        LastName = lastName;
        Email = email;
    }
    
    private Entity() { }
}
```

**Event Publishing in DbContext:**
```csharp
public class ApplicationDbContext : DbContext
{
    private readonly IMediator _mediator;
    
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options, IMediator mediator)
        : base(options)
    {
        _mediator = mediator;
    }
    
    public override async Task<int> SaveChangesAsync(CancellationToken cancellationToken = default)
    {
        // Dispatch domain events before saving
        var domainEvents = ChangeTracker.Entries<AggregateRoot>()
            .SelectMany(x => x.Entity.DomainEvents)
            .ToList();
        
        var result = await base.SaveChangesAsync(cancellationToken);
        
        // Publish events after successful save
        foreach (var domainEvent in domainEvents)
        {
            await _mediator.Publish(domainEvent, cancellationToken);
        }
        
        // Clear events
        ChangeTracker.Entries<AggregateRoot>()
            .ToList()
            .ForEach(e => e.Entity.ClearDomainEvents());
        
        return result;
    }
}
```

**Event Handler:**
```csharp
public class EntityCreatedEventHandler : INotificationHandler<EntityCreatedEvent>
{
    private readonly ILogger<EntityCreatedEventHandler> _logger;
    private readonly IMessageBus _messageBus;
    
    public EntityCreatedEventHandler(
        ILogger<EntityCreatedEventHandler> logger,
        IMessageBus messageBus)
    {
        _logger = logger;
        _messageBus = messageBus;
    }
    
    public async Task Handle(EntityCreatedEvent notification, CancellationToken cancellationToken)
    {
        _logger.LogInformation("Entity created: {EntityId}", notification.EntityId);
        
        // Publish integration event to message bus for other microservices
        await _messageBus.PublishAsync(new EntityCreatedIntegrationEvent
        {
            EntityId = notification.EntityId,
            Email = notification.Email,
            OccurredOn = notification.OccurredOn
        }, cancellationToken);
    }
}
```

### Additional Recommendations

**Use of Factories**

Factories are useful in DDD to encapsulate the logic of creating complex objects, ensuring that all created objects comply with domain rules from the moment of creation.

**Example:**
```csharp
public interface IEntityFactory
{
    Entity CreateEntity(string firstName, string lastName, string email);
}

public class EntityFactory : IEntityFactory
{
    public Entity CreateEntity(string firstName, string lastName, string email)
    {
        // Complex creation logic, validation, and business rules
        if (string.IsNullOrWhiteSpace(email) || !email.Contains("@"))
            throw new ArgumentException("Invalid email", nameof(email));
        
        return Entity.Create(firstName, lastName, email);
    }
}
```

**Recommendation**: Implement factories for the creation of entities and aggregates, especially those that are complex and require specific initial configuration that complies with business rules.

**Improvement in Relationship Modeling**

Relationships between entities and aggregates must be clear and consistent with business rules.

**Recommendation**: Review and possibly redesign relationships between entities to ensure they accurately reflect domain needs and rules. Use navigation properties carefully and consider whether relationships should be managed within aggregate boundaries.

**Domain Events Integration**

Domain events are an important part of DDD and enable loose coupling between bounded contexts in a microservices architecture.

**Recommendation**: 
- Implement a domain event system using MediatR or similar libraries
- Publish domain events from aggregates when significant state changes occur
- Convert domain events to integration events for cross-service communication
- Handle events asynchronously to avoid blocking operations

**Bounded Contexts**

In a microservices architecture, each service represents a bounded context with its own domain model.

**Recommendation**:
- Clearly define bounded context boundaries
- Avoid sharing domain models between services
- Use integration events for cross-context communication
- Maintain separate databases per service when possible

## SOLID and DRY Principles

### SOLID Principles

SOLID principles are five object-oriented design principles that help create more understandable, flexible, and maintainable systems.

#### Single Responsibility Principle (SRP)

Each class should have a single responsibility or reason to change.

**Before:**
```csharp
// A class that handles multiple responsibilities
public class EntityManager
{
    public void ProcessEntity(Entity entity)
    {
        // Validation
        if (!entity.Email.Contains("@"))
            throw new Exception("Invalid email");
        
        // Database access
        using var context = new ApplicationDbContext();
        context.Entities.Add(entity);
        context.SaveChanges();
        
        // Logging
        Console.WriteLine("Entity saved");
        
        // Email notification
        SendEmail(entity.Email, "Welcome!");
    }
}
```

**After:**
```csharp
public class Entity
{
    public void ValidateEmail()
    {
        if (!Email.Contains("@"))
            throw new ArgumentException("Invalid email", nameof(Email));
    }
}

public class EntityRepository : IEntityRepository
{
    private readonly ApplicationDbContext _context;
    
    public EntityRepository(ApplicationDbContext context)
    {
        _context = context;
    }
    
    public async Task<Entity> AddAsync(Entity entity)
    {
        entity.ValidateEmail();
        await _context.Entities.AddAsync(entity);
        await _context.SaveChangesAsync();
        return entity;
    }
}

public class EntityNotificationService : IEntityNotificationService
{
    private readonly IEmailService _emailService;
    
    public EntityNotificationService(IEmailService emailService)
    {
        _emailService = emailService;
    }
    
    public async Task SendWelcomeEmail(Entity entity)
    {
        await _emailService.SendAsync(entity.Email, "Welcome!");
    }
}
```

**Explanation**: Each class now has a single responsibility: validation, persistence, or notifications.

#### Open/Closed Principle (OCP)

Software entities should be open for extension but closed for modification.

**Before:**
```csharp
public class PriceCalculator
{
    public decimal Calculate(Product product, string customerType)
    {
        if (customerType == "Regular")
            return product.Price;
        else if (customerType == "Premium")
            return product.Price * 0.9m;
        else if (customerType == "VIP")
            return product.Price * 0.8m;
        
        return product.Price;
    }
}
```

**After:**
```csharp
public interface IPricingStrategy
{
    decimal Calculate(decimal basePrice);
}

public class RegularPricingStrategy : IPricingStrategy
{
    public decimal Calculate(decimal basePrice) => basePrice;
}

public class PremiumPricingStrategy : IPricingStrategy
{
    public decimal Calculate(decimal basePrice) => basePrice * 0.9m;
}

public class VIPPricingStrategy : IPricingStrategy
{
    public decimal Calculate(decimal basePrice) => basePrice * 0.8m;
}

public class PriceCalculator
{
    private readonly IPricingStrategy _strategy;
    
    public PriceCalculator(IPricingStrategy strategy)
    {
        _strategy = strategy;
    }
    
    public decimal Calculate(Product product)
    {
        return _strategy.Calculate(product.Price);
    }
}
```

**Explanation**: New pricing strategies can be added without modifying existing code.

#### Liskov Substitution Principle (LSP)

Objects of a derived class should be replaceable with objects of the base class without altering the program's functionality.

**Best Practice**: Ensure derived classes can be used wherever their base class is expected. Use abstract classes and interfaces appropriately.

#### Interface Segregation Principle (ISP)

Many specific interfaces are better than a single general interface.

**Before:**
```csharp
public interface IEntityOperations
{
    void Save();
    void Validate();
    void SendNotification();
    void GenerateReport();
}
```

**After:**
```csharp
public interface ISaveable
{
    void Save();
}

public interface IValidatable
{
    void Validate();
}

public interface INotifiable
{
    void SendNotification();
}

public class Entity : ISaveable, IValidatable
{
    public void Save() { /* implementation */ }
    public void Validate() { /* implementation */ }
}
```

**Explanation**: Interfaces are segregated into smaller, focused contracts.

#### Dependency Inversion Principle (DIP)

High-level modules should not depend on low-level modules; both should depend on abstractions.

**Before:**
```csharp
public class EntityService
{
    private readonly EntityRepository _repository = new EntityRepository();
    
    public Entity GetEntity(int id)
    {
        return _repository.GetById(id);
    }
}
```

**After:**
```csharp
public class EntityService
{
    private readonly IEntityRepository _repository;
    
    public EntityService(IEntityRepository repository)
    {
        _repository = repository ?? throw new ArgumentNullException(nameof(repository));
    }
    
    public async Task<Entity> GetEntityAsync(int id)
    {
        return await _repository.GetByIdAsync(id);
    }
}

// Dependency Injection registration in Program.cs
builder.Services.AddScoped<IEntityRepository, EntityRepository>();
builder.Services.AddScoped<EntityService>();
```

**Explanation**: `EntityService` depends on an abstraction (`IEntityRepository`), making it testable and flexible.

### DRY (Don't Repeat Yourself)

The DRY principle focuses on reducing duplication in code.

**Before:**
```csharp
public async Task SaveEntityAsync(Entity entity)
{
    if (!entity.Email.Contains("@"))
        throw new ArgumentException("Invalid email");
    await _context.SaveChangesAsync();
}

public async Task UpdateEntityAsync(Entity entity)
{
    if (!entity.Email.Contains("@"))
        throw new ArgumentException("Invalid email");
    await _context.SaveChangesAsync();
}
```

**After:**
```csharp
public class Entity
{
    public void ValidateEmail()
    {
        if (!Email.Contains("@"))
            throw new ArgumentException("Invalid email", nameof(Email));
    }
}

public async Task SaveEntityAsync(Entity entity)
{
    entity.ValidateEmail();
    await _context.SaveChangesAsync();
}

public async Task UpdateEntityAsync(Entity entity)
{
    entity.ValidateEmail();
    await _context.SaveChangesAsync();
}
```

**Explanation**: Email validation is centralized in a single method, eliminating duplication.

## Coding Standards

### Language and Naming Conventions

- **Variable Naming**: Use camelCase for local variables and parameters (e.g., `entityId`, `firstName`)
- **Property Naming**: Use PascalCase for properties (e.g., `EntityId`, `FirstName`)
- **Class Naming**: Use PascalCase for classes and interfaces (e.g., `Entity`, `IEntityRepository`)
- **Interface Naming**: Prefix interfaces with `I` (e.g., `IEntityService`, `IRepository<T>`)
- **Constants Naming**: Use PascalCase for constants (e.g., `MaxEntitiesPerPage`)
- **Method Naming**: Use PascalCase for methods (e.g., `GetEntityById`, `SaveAsync`)
- **Private Fields**: Use camelCase with underscore prefix `_fieldName` (e.g., `_context`, `_logger`)
- **File Naming**: Use PascalCase matching the main class name (e.g., `EntityRepository.cs`, `IEntityService.cs`)
- **Async Methods**: Suffix async methods with `Async` (e.g., `GetEntityAsync`, `SaveChangesAsync`)

**Examples:**

```csharp
// Good: All in English with proper conventions
public class EntityRepository : IEntityRepository
{
    private readonly ApplicationDbContext _context;
    private readonly ILogger<EntityRepository> _logger;
    private const int MaxRetries = 3;
    
    public EntityRepository(
        ApplicationDbContext context, 
        ILogger<EntityRepository> logger)
    {
        _context = context ?? throw new ArgumentNullException(nameof(context));
        _logger = logger ?? throw new ArgumentNullException(nameof(logger));
    }
    
    public async Task<Entity?> GetByIdAsync(int entityId, CancellationToken cancellationToken = default)
    {
        _logger.LogInformation("Finding entity by ID: {EntityId}", entityId);
        
        return await _context.Entities
            .FirstOrDefaultAsync(e => e.Id == entityId, cancellationToken);
    }
}

// Avoid: Non-English names or incorrect conventions
public class RepositorioEntidad // Wrong: Non-English name
{
    private ApplicationDbContext context; // Wrong: Missing underscore prefix
    
    public Entity BuscarPorId(int idEntidad) // Wrong: Non-English, not async
    {
        return context.Entities.Find(idEntidad);
    }
}
```

**Error Messages and Logs:**

```csharp
// Good: English error messages
throw new NotFoundException($"Entity not found with ID: {entityId}");
_logger.LogError("Failed to create entity: {Error}", exception.Message);

// Avoid: Non-English messages
throw new NotFoundException($"Entidad no encontrada con ID: {entityId}");
_logger.LogError("Error al crear entidad: {Error}", exception.Message);
```

### C# Usage

- **Nullable Reference Types**: Enable nullable reference types in project file
- **Explicit Types**: Use explicit types for complex types, `var` for obvious assignments
- **Async/Await**: Use async/await for all I/O operations
- **Pattern Matching**: Use modern C# pattern matching features
- **Records**: Use records for immutable DTOs and value objects
- **Target Framework**: Use latest LTS or STS .NET version

```csharp
// Good: Modern C# features
public record EntityDto(int Id, string FirstName, string LastName, string Email);

public async Task<Entity?> GetEntityAsync(int id)
{
    var entity = await _repository.GetByIdAsync(id);
    
    return entity switch
    {
        null => throw new NotFoundException($"Entity {id} not found"),
        { IsDeleted: true } => throw new InvalidOperationException("Entity is deleted"),
        _ => entity
    };
}

// Avoid: Legacy patterns
public class EntityDto
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    // ... etc
}
```

**Project Configuration (csproj):**
```xml
<PropertyGroup>
    <TargetFramework>net8.0</TargetFramework>
    <Nullable>enable</Nullable>
    <ImplicitUsings>enable</ImplicitUsings>
    <TreatWarningsAsErrors>true</TreatWarningsAsErrors>
</PropertyGroup>
```

### Error Handling

- **Custom Exception Classes**: Create domain-specific exception classes
- **Exception Middleware**: Use global exception middleware for consistent error responses
- **Logging**: Log exceptions with appropriate context
- **Avoid Generic Exceptions**: Use specific exception types

```csharp
// Custom domain exceptions
public class NotFoundException : Exception
{
    public NotFoundException(string message) : base(message) { }
    
    public NotFoundException(string message, Exception innerException) 
        : base(message, innerException) { }
}

public class DomainValidationException : Exception
{
    public DomainValidationException(string message) : base(message) { }
}

// Exception middleware
public class ExceptionHandlingMiddleware
{
    private readonly RequestDelegate _next;
    private readonly ILogger<ExceptionHandlingMiddleware> _logger;
    
    public ExceptionHandlingMiddleware(
        RequestDelegate next, 
        ILogger<ExceptionHandlingMiddleware> logger)
    {
        _next = next;
        _logger = logger;
    }
    
    public async Task InvokeAsync(HttpContext context)
    {
        try
        {
            await _next(context);
        }
        catch (NotFoundException ex)
        {
            _logger.LogWarning(ex, "Resource not found");
            await HandleExceptionAsync(context, ex, StatusCodes.Status404NotFound);
        }
        catch (DomainValidationException ex)
        {
            _logger.LogWarning(ex, "Validation error");
            await HandleExceptionAsync(context, ex, StatusCodes.Status400BadRequest);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Unhandled exception");
            await HandleExceptionAsync(context, ex, StatusCodes.Status500InternalServerError);
        }
    }
    
    private static async Task HandleExceptionAsync(HttpContext context, Exception exception, int statusCode)
    {
        context.Response.ContentType = "application/json";
        context.Response.StatusCode = statusCode;
        
        var response = new
        {
            success = false,
            error = new
            {
                message = exception.Message,
                code = exception.GetType().Name
            }
        };
        
        await context.Response.WriteAsJsonAsync(response);
    }
}
```

### Validation Patterns

- **FluentValidation**: Use FluentValidation for complex validation logic
- **Data Annotations**: Use for simple property-level validation
- **Validate Early**: Validate at API boundary before processing

```csharp
// FluentValidation validator
public class CreateEntityCommandValidator : AbstractValidator<CreateEntityCommand>
{
    public CreateEntityCommandValidator()
    {
        RuleFor(x => x.FirstName)
            .NotEmpty().WithMessage("First name is required")
            .MaximumLength(100).WithMessage("First name cannot exceed 100 characters");
        
        RuleFor(x => x.Email)
            .NotEmpty().WithMessage("Email is required")
            .EmailAddress().WithMessage("Invalid email format");
    }
}

// Usage in controller
[ApiController]
[Route("api/[controller]")]
public class EntitiesController : ControllerBase
{
    private readonly IMediator _mediator;
    
    public EntitiesController(IMediator mediator)
    {
        _mediator = mediator;
    }
    
    [HttpPost]
    public async Task<ActionResult<EntityDto>> CreateEntity(
        [FromBody] CreateEntityCommand command,
        CancellationToken cancellationToken)
    {
        var result = await _mediator.Send(command, cancellationToken);
        return CreatedAtAction(nameof(GetEntity), new { id = result.Id }, result);
    }
}
```

### Logging Standards

- **Use ILogger**: Use ASP.NET Core's built-in logging abstraction
- **Structured Logging**: Use structured logging with named parameters
- **Log Levels**: Use appropriate log levels (Trace, Debug, Information, Warning, Error, Critical)
- **Sensitive Data**: Never log sensitive data (passwords, tokens, PII)

```csharp
public class EntityService
{
    private readonly IEntityRepository _repository;
    private readonly ILogger<EntityService> _logger;
    
    public EntityService(
        IEntityRepository repository, 
        ILogger<EntityService> logger)
    {
        _repository = repository;
        _logger = logger;
    }
    
    public async Task<Entity> CreateEntityAsync(CreateEntityDto dto)
    {
        _logger.LogInformation(
            "Creating entity with email: {Email}", 
            dto.Email); // OK to log email in this context
        
        try
        {
            var entity = Entity.Create(dto.FirstName, dto.LastName, dto.Email);
            var result = await _repository.AddAsync(entity);
            
            _logger.LogInformation(
                "Entity created successfully: {EntityId}", 
                result.Id);
            
            return result;
        }
        catch (Exception ex)
        {
            _logger.LogError(
                ex, 
                "Failed to create entity for email: {Email}", 
                dto.Email);
            throw;
        }
    }
}
```

## API Design Standards

### REST Endpoints

- **RESTful Naming**: Use RESTful conventions for endpoint naming
- **HTTP Methods**: Use appropriate HTTP methods (GET, POST, PUT, DELETE, PATCH)
- **Resource-Based URLs**: URLs should represent resources, not actions
- **Versioning**: Use API versioning (e.g., `/api/v1/entities`)

```csharp
[ApiController]
[Route("api/v1/[controller]")]
public class EntitiesController : ControllerBase
{
    private readonly IMediator _mediator;
    
    public EntitiesController(IMediator mediator)
    {
        _mediator = mediator;
    }
    
    // GET api/v1/entities
    [HttpGet]
    public async Task<ActionResult<IEnumerable<EntityDto>>> GetEntities(
        [FromQuery] GetEntitiesQuery query,
        CancellationToken cancellationToken)
    {
        var result = await _mediator.Send(query, cancellationToken);
        return Ok(result);
    }
    
    // GET api/v1/entities/{id}
    [HttpGet("{id}")]
    public async Task<ActionResult<EntityDto>> GetEntity(
        int id,
        CancellationToken cancellationToken)
    {
        var result = await _mediator.Send(new GetEntityQuery(id), cancellationToken);
        return Ok(result);
    }
    
    // POST api/v1/entities
    [HttpPost]
    public async Task<ActionResult<EntityDto>> CreateEntity(
        [FromBody] CreateEntityCommand command,
        CancellationToken cancellationToken)
    {
        var result = await _mediator.Send(command, cancellationToken);
        return CreatedAtAction(nameof(GetEntity), new { id = result.Id }, result);
    }
    
    // PUT api/v1/entities/{id}
    [HttpPut("{id}")]
    public async Task<ActionResult<EntityDto>> UpdateEntity(
        int id,
        [FromBody] UpdateEntityCommand command,
        CancellationToken cancellationToken)
    {
        if (id != command.Id)
            return BadRequest("ID mismatch");
        
        var result = await _mediator.Send(command, cancellationToken);
        return Ok(result);
    }
    
    // DELETE api/v1/entities/{id}
    [HttpDelete("{id}")]
    public async Task<IActionResult> DeleteEntity(
        int id,
        CancellationToken cancellationToken)
    {
        await _mediator.Send(new DeleteEntityCommand(id), cancellationToken);
        return NoContent();
    }
}
```

### Request/Response Patterns

- **JSON Format**: Use JSON for request and response bodies
- **DTOs**: Use Data Transfer Objects for API models
- **Consistent Structure**: Maintain consistent response structure across all endpoints
- **Status Codes**: Use appropriate HTTP status codes
- **CancellationToken**: Accept CancellationToken in async methods

```csharp
// Response DTOs
public record EntityDto(
    int Id,
    string FirstName,
    string LastName,
    string Email,
    DateTime CreatedAt);

public record ApiResponse<T>(
    bool Success,
    T Data,
    string? Message = null);

public record ErrorResponse(
    bool Success,
    ErrorDetail Error);

public record ErrorDetail(
    string Message,
    string Code,
    Dictionary<string, string[]>? ValidationErrors = null);

// Controller response pattern
[HttpGet("{id}")]
public async Task<ActionResult<ApiResponse<EntityDto>>> GetEntity(
    int id,
    CancellationToken cancellationToken)
{
    var entity = await _mediator.Send(new GetEntityQuery(id), cancellationToken);
    
    return Ok(new ApiResponse<EntityDto>(
        Success: true,
        Data: entity,
        Message: "Entity retrieved successfully"));
}
```

### Error Response Format

- **Consistent Format**: All errors should follow the same response structure
- **Error Codes**: Use meaningful error codes for different error types
- **Validation Errors**: Include detailed validation errors when applicable

```csharp
// Error response examples
// 400 Bad Request
{
    "success": false,
    "error": {
        "message": "Validation failed",
        "code": "VALIDATION_ERROR",
        "validationErrors": {
            "FirstName": ["First name is required"],
            "Email": ["Invalid email format"]
        }
    }
}

// 404 Not Found
{
    "success": false,
    "error": {
        "message": "Entity not found with ID: 123",
        "code": "NOT_FOUND"
    }
}

// 500 Internal Server Error
{
    "success": false,
    "error": {
        "message": "An unexpected error occurred",
        "code": "INTERNAL_SERVER_ERROR"
    }
}
```

### CORS Configuration

- **Enable CORS**: Configure CORS to allow frontend origins
- **Secure Configuration**: Only allow specific origins in production
- **Credentials**: Configure credentials handling appropriately

```csharp
// Program.cs
var builder = WebApplication.CreateBuilder(args);

builder.Services.AddCors(options =>
{
    options.AddPolicy("AllowFrontend", policy =>
    {
        policy.WithOrigins(builder.Configuration["FrontendUrl"] ?? "http://localhost:3000")
            .AllowAnyHeader()
            .AllowAnyMethod()
            .AllowCredentials();
    });
});

var app = builder.Build();

app.UseCors("AllowFrontend");
```

## Database Patterns

### Entity Framework Core

- **DbContext**: Central class for database operations
- **Entity Configurations**: Use Fluent API for entity configurations
- **Connection Strings**: Store in appsettings.json with environment-specific overrides
- **Code-First Approach**: Define entities in code and generate database schema

**DbContext Implementation:**
```csharp
public class ApplicationDbContext : DbContext
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }
    
    public DbSet<Entity> Entities => Set<Entity>();
    public DbSet<OrderAggregate> Orders => Set<OrderAggregate>();
    
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);
        
        // Apply all entity configurations from assembly
        modelBuilder.ApplyConfigurationsFromAssembly(typeof(ApplicationDbContext).Assembly);
        
        // Configure schema
        modelBuilder.HasDefaultSchema("public");
    }
}
```

**Entity Configuration:**
```csharp
public class EntityConfiguration : IEntityTypeConfiguration<Entity>
{
    public void Configure(EntityTypeBuilder<Entity> builder)
    {
        builder.ToTable("entities");
        
        builder.HasKey(e => e.Id);
        
        builder.Property(e => e.Id)
            .HasColumnName("id")
            .ValueGeneratedOnAdd();
        
        builder.Property(e => e.FirstName)
            .HasColumnName("first_name")
            .HasMaxLength(100)
            .IsRequired();
        
        builder.Property(e => e.LastName)
            .HasColumnName("last_name")
            .HasMaxLength(100)
            .IsRequired();
        
        builder.Property(e => e.Email)
            .HasColumnName("email")
            .HasMaxLength(255)
            .IsRequired();
        
        builder.HasIndex(e => e.Email)
            .IsUnique();
        
        // Value object configuration
        builder.OwnsOne(e => e.Address, address =>
        {
            address.Property(a => a.Street).HasColumnName("address_street");
            address.Property(a => a.City).HasColumnName("address_city");
            address.Property(a => a.PostalCode).HasColumnName("address_postal_code");
            address.Property(a => a.Country).HasColumnName("address_country");
        });
        
        // Collection configuration
        builder.HasMany(e => e.Orders)
            .WithOne()
            .HasForeignKey("entity_id")
            .OnDelete(DeleteBehavior.Cascade);
    }
}
```

**Connection String Configuration:**
```json
// appsettings.json
{
  "ConnectionStrings": {
    "DefaultConnection": "Host=localhost;Port=5432;Database=mydb;Username=postgres;Password=password"
  }
}

// appsettings.Development.json
{
  "ConnectionStrings": {
    "DefaultConnection": "Host=localhost;Port=5432;Database=mydb_dev;Username=dev_user;Password=dev_password"
  }
}
```

**DbContext Registration:**
```csharp
// Program.cs
builder.Services.AddDbContext<ApplicationDbContext>(options =>
{
    options.UseNpgsql(
        builder.Configuration.GetConnectionString("DefaultConnection"),
        npgsqlOptions =>
        {
            npgsqlOptions.MigrationsAssembly(typeof(ApplicationDbContext).Assembly.FullName);
            npgsqlOptions.EnableRetryOnFailure(maxRetryCount: 3);
        });
    
    if (builder.Environment.IsDevelopment())
    {
        options.EnableSensitiveDataLogging();
        options.EnableDetailedErrors();
    }
});
```

### Migrations

- **Version Control**: All database changes must be version-controlled through migrations
- **Migration Naming**: Use descriptive names for migrations
- **Review Migrations**: Review migration files before applying
- **Commands**: Use dotnet ef CLI for migration management

```powershell
# Create a new migration
dotnet ef migrations add AddEntityTable --project Infrastructure --startup-project API

# Apply migrations
dotnet ef database update --project Infrastructure --startup-project API

# Rollback to specific migration
dotnet ef database update PreviousMigrationName --project Infrastructure --startup-project API

# Remove last migration (if not applied)
dotnet ef migrations remove --project Infrastructure --startup-project API

# Generate SQL script
dotnet ef migrations script --project Infrastructure --startup-project API --output migration.sql

# Generate idempotent script
dotnet ef migrations script --project Infrastructure --startup-project API --idempotent --output migration_idempotent.sql
```

**Migration Example:**
```csharp
public partial class AddEntityTable : Migration
{
    protected override void Up(MigrationBuilder migrationBuilder)
    {
        migrationBuilder.CreateTable(
            name: "entities",
            schema: "public",
            columns: table => new
            {
                id = table.Column<int>(type: "integer", nullable: false)
                    .Annotation("Npgsql:ValueGenerationStrategy", NpgsqlValueGenerationStrategy.IdentityByDefaultColumn),
                first_name = table.Column<string>(type: "character varying(100)", maxLength: 100, nullable: false),
                last_name = table.Column<string>(type: "character varying(100)", maxLength: 100, nullable: false),
                email = table.Column<string>(type: "character varying(255)", maxLength: 255, nullable: false),
                created_at = table.Column<DateTime>(type: "timestamp with time zone", nullable: false, defaultValueSql: "now()")
            },
            constraints: table =>
            {
                table.PrimaryKey("PK_entities", x => x.id);
            });
        
        migrationBuilder.CreateIndex(
            name: "IX_entities_email",
            schema: "public",
            table: "entities",
            column: "email",
            unique: true);
    }
    
    protected override void Down(MigrationBuilder migrationBuilder)
    {
        migrationBuilder.DropTable(
            name: "entities",
            schema: "public");
    }
}
```

### Repository Pattern

- **Repository Interfaces**: Define repository interfaces in the domain layer
- **EF Core Implementation**: Implement repositories using EF Core in the infrastructure layer
- **Dependency Injection**: Inject DbContext into repositories
- **Aggregate Roots Only**: Repositories should work with aggregate roots

```csharp
// Domain layer - Repository interface
public interface IEntityRepository
{
    Task<Entity?> GetByIdAsync(int id, CancellationToken cancellationToken = default);
    Task<IEnumerable<Entity>> GetAllAsync(CancellationToken cancellationToken = default);
    Task<Entity> AddAsync(Entity entity, CancellationToken cancellationToken = default);
    Task UpdateAsync(Entity entity, CancellationToken cancellationToken = default);
    Task DeleteAsync(int id, CancellationToken cancellationToken = default);
    Task<bool> ExistsAsync(int id, CancellationToken cancellationToken = default);
}

// Infrastructure layer - Repository implementation
public class EntityRepository : IEntityRepository
{
    private readonly ApplicationDbContext _context;
    private readonly ILogger<EntityRepository> _logger;
    
    public EntityRepository(
        ApplicationDbContext context,
        ILogger<EntityRepository> logger)
    {
        _context = context ?? throw new ArgumentNullException(nameof(context));
        _logger = logger ?? throw new ArgumentNullException(nameof(logger));
    }
    
    public async Task<Entity?> GetByIdAsync(int id, CancellationToken cancellationToken = default)
    {
        return await _context.Entities
            .Include(e => e.Orders)
            .FirstOrDefaultAsync(e => e.Id == id, cancellationToken);
    }
    
    public async Task<IEnumerable<Entity>> GetAllAsync(CancellationToken cancellationToken = default)
    {
        return await _context.Entities
            .Include(e => e.Orders)
            .AsNoTracking()
            .ToListAsync(cancellationToken);
    }
    
    public async Task<Entity> AddAsync(Entity entity, CancellationToken cancellationToken = default)
    {
        await _context.Entities.AddAsync(entity, cancellationToken);
        await _context.SaveChangesAsync(cancellationToken);
        return entity;
    }
    
    public async Task UpdateAsync(Entity entity, CancellationToken cancellationToken = default)
    {
        _context.Entities.Update(entity);
        await _context.SaveChangesAsync(cancellationToken);
    }
    
    public async Task DeleteAsync(int id, CancellationToken cancellationToken = default)
    {
        var entity = await GetByIdAsync(id, cancellationToken);
        if (entity != null)
        {
            _context.Entities.Remove(entity);
            await _context.SaveChangesAsync(cancellationToken);
        }
    }
    
    public async Task<bool> ExistsAsync(int id, CancellationToken cancellationToken = default)
    {
        return await _context.Entities
            .AnyAsync(e => e.Id == id, cancellationToken);
    }
}

// DI Registration in Program.cs
builder.Services.AddScoped<IEntityRepository, EntityRepository>();
```

## Event-Driven Patterns

### Event Publishing

Services publish integration events to the message broker when significant domain changes occur that other services need to know about.

**Integration Event Definition:**
```csharp
public abstract class IntegrationEvent
{
    public Guid EventId { get; }
    public DateTime OccurredOn { get; }
    
    protected IntegrationEvent()
    {
        EventId = Guid.NewGuid();
        OccurredOn = DateTime.UtcNow;
    }
}

public class EntityCreatedIntegrationEvent : IntegrationEvent
{
    public int EntityId { get; }
    public string Email { get; }
    public string FirstName { get; }
    public string LastName { get; }
    
    public EntityCreatedIntegrationEvent(int entityId, string email, string firstName, string lastName)
    {
        EntityId = entityId;
        Email = email;
        FirstName = firstName;
        LastName = lastName;
    }
}
```

**Message Bus Interface:**
```csharp
public interface IMessageBus
{
    Task PublishAsync<TEvent>(TEvent @event, CancellationToken cancellationToken = default) 
        where TEvent : IntegrationEvent;
}
```

**RabbitMQ Implementation with MassTransit:**
```csharp
public class RabbitMqMessageBus : IMessageBus
{
    private readonly IPublishEndpoint _publishEndpoint;
    private readonly ILogger<RabbitMqMessageBus> _logger;
    
    public RabbitMqMessageBus(
        IPublishEndpoint publishEndpoint,
        ILogger<RabbitMqMessageBus> logger)
    {
        _publishEndpoint = publishEndpoint;
        _logger = logger;
    }
    
    public async Task PublishAsync<TEvent>(TEvent @event, CancellationToken cancellationToken = default) 
        where TEvent : IntegrationEvent
    {
        try
        {
            _logger.LogInformation(
                "Publishing event {EventType} with ID {EventId}",
                typeof(TEvent).Name,
                @event.EventId);
            
            await _publishEndpoint.Publish(@event, cancellationToken);
            
            _logger.LogInformation(
                "Successfully published event {EventType} with ID {EventId}",
                typeof(TEvent).Name,
                @event.EventId);
        }
        catch (Exception ex)
        {
            _logger.LogError(
                ex,
                "Failed to publish event {EventType} with ID {EventId}",
                typeof(TEvent).Name,
                @event.EventId);
            throw;
        }
    }
}
```

**MassTransit Configuration:**
```csharp
// Program.cs
builder.Services.AddMassTransit(x =>
{
    // Register consumers
    x.AddConsumersFromNamespaceContaining<EntityCreatedConsumer>();
    
    x.UsingRabbitMq((context, cfg) =>
    {
        cfg.Host(builder.Configuration["MessageBroker:Host"], h =>
        {
            h.Username(builder.Configuration["MessageBroker:Username"]);
            h.Password(builder.Configuration["MessageBroker:Password"]);
        });
        
        // Configure retry policy
        cfg.UseMessageRetry(r => r.Interval(3, TimeSpan.FromSeconds(5)));
        
        // Configure endpoints
        cfg.ConfigureEndpoints(context);
    });
});

builder.Services.AddScoped<IMessageBus, RabbitMqMessageBus>();
```

### Event Consumption

Services consume events from other services to react to changes in the system.

**Event Consumer:**
```csharp
public class EntityCreatedConsumer : IConsumer<EntityCreatedIntegrationEvent>
{
    private readonly ILogger<EntityCreatedConsumer> _logger;
    private readonly IEmailService _emailService;
    
    public EntityCreatedConsumer(
        ILogger<EntityCreatedConsumer> logger,
        IEmailService emailService)
    {
        _logger = logger;
        _emailService = emailService;
    }
    
    public async Task Consume(ConsumeContext<EntityCreatedIntegrationEvent> context)
    {
        var @event = context.Message;
        
        _logger.LogInformation(
            "Consuming EntityCreatedIntegrationEvent for Entity {EntityId}",
            @event.EntityId);
        
        try
        {
            // Process the event
            await _emailService.SendWelcomeEmailAsync(
                @event.Email,
                @event.FirstName,
                context.CancellationToken);
            
            _logger.LogInformation(
                "Successfully processed EntityCreatedIntegrationEvent for Entity {EntityId}",
                @event.EntityId);
        }
        catch (Exception ex)
        {
            _logger.LogError(
                ex,
                "Failed to process EntityCreatedIntegrationEvent for Entity {EntityId}",
                @event.EntityId);
            
            // Throw to trigger retry policy
            throw;
        }
    }
}
```

**Consumer Configuration:**
```csharp
public class EntityCreatedConsumerDefinition : ConsumerDefinition<EntityCreatedConsumer>
{
    protected override void ConfigureConsumer(
        IReceiveEndpointConfigurator endpointConfigurator,
        IConsumerConfigurator<EntityCreatedConsumer> consumerConfigurator)
    {
        // Configure concurrency
        endpointConfigurator.ConcurrentMessageLimit = 10;
        
        // Configure retry policy
        endpointConfigurator.UseMessageRetry(r => 
        {
            r.Interval(3, TimeSpan.FromSeconds(5));
            r.Ignore<ArgumentException>();
        });
        
        // Configure circuit breaker
        endpointConfigurator.UseCircuitBreaker(cb =>
        {
            cb.TrackingPeriod = TimeSpan.FromMinutes(1);
            cb.TripThreshold = 15;
            cb.ActiveThreshold = 10;
            cb.ResetInterval = TimeSpan.FromMinutes(5);
        });
    }
}
```

### Event Sourcing

Event sourcing stores the state of entities as a sequence of events rather than just the current state.

**Event Store Pattern (Optional):**
```csharp
public interface IEventStore
{
    Task SaveEventsAsync<TAggregateId>(
        TAggregateId aggregateId,
        IEnumerable<DomainEvent> events,
        int expectedVersion,
        CancellationToken cancellationToken = default);
    
    Task<IEnumerable<DomainEvent>> GetEventsAsync<TAggregateId>(
        TAggregateId aggregateId,
        CancellationToken cancellationToken = default);
}

public class EventStoreRepository : IEventStore
{
    private readonly ApplicationDbContext _context;
    
    public EventStoreRepository(ApplicationDbContext context)
    {
        _context = context;
    }
    
    public async Task SaveEventsAsync<TAggregateId>(
        TAggregateId aggregateId,
        IEnumerable<DomainEvent> events,
        int expectedVersion,
        CancellationToken cancellationToken = default)
    {
        var eventDescriptors = events.Select((e, i) => new EventDescriptor
        {
            AggregateId = aggregateId.ToString(),
            EventType = e.GetType().Name,
            EventData = JsonSerializer.Serialize(e),
            Version = expectedVersion + i + 1,
            OccurredOn = e.OccurredOn
        });
        
        await _context.EventStore.AddRangeAsync(eventDescriptors, cancellationToken);
        await _context.SaveChangesAsync(cancellationToken);
    }
    
    public async Task<IEnumerable<DomainEvent>> GetEventsAsync<TAggregateId>(
        TAggregateId aggregateId,
        CancellationToken cancellationToken = default)
    {
        var events = await _context.EventStore
            .Where(e => e.AggregateId == aggregateId.ToString())
            .OrderBy(e => e.Version)
            .ToListAsync(cancellationToken);
        
        return events.Select(e => 
            JsonSerializer.Deserialize<DomainEvent>(e.EventData))
            .Where(e => e != null)
            .Cast<DomainEvent>();
    }
}
```

### CQRS Pattern

Command Query Responsibility Segregation separates read and write operations for better scalability and performance.

**Command Example:**
```csharp
public record CreateEntityCommand(
    string FirstName,
    string LastName,
    string Email) : IRequest<EntityDto>;

public class CreateEntityCommandHandler : IRequestHandler<CreateEntityCommand, EntityDto>
{
    private readonly IEntityRepository _repository;
    private readonly IMessageBus _messageBus;
    private readonly ILogger<CreateEntityCommandHandler> _logger;
    
    public CreateEntityCommandHandler(
        IEntityRepository repository,
        IMessageBus messageBus,
        ILogger<CreateEntityCommandHandler> logger)
    {
        _repository = repository;
        _messageBus = messageBus;
        _logger = logger;
    }
    
    public async Task<EntityDto> Handle(
        CreateEntityCommand request,
        CancellationToken cancellationToken)
    {
        _logger.LogInformation("Creating new entity with email: {Email}", request.Email);
        
        // Create entity
        var entity = Entity.Create(request.FirstName, request.LastName, request.Email);
        
        // Save to database
        await _repository.AddAsync(entity, cancellationToken);
        
        // Publish integration event
        await _messageBus.PublishAsync(
            new EntityCreatedIntegrationEvent(
                entity.Id,
                entity.Email,
                entity.FirstName,
                entity.LastName),
            cancellationToken);
        
        _logger.LogInformation("Entity created with ID: {EntityId}", entity.Id);
        
        return new EntityDto(
            entity.Id,
            entity.FirstName,
            entity.LastName,
            entity.Email,
            DateTime.UtcNow);
    }
}
```

**Query Example:**
```csharp
public record GetEntityQuery(int Id) : IRequest<EntityDto>;

public class GetEntityQueryHandler : IRequestHandler<GetEntityQuery, EntityDto>
{
    private readonly ApplicationDbContext _context;
    private readonly ILogger<GetEntityQueryHandler> _logger;
    
    public GetEntityQueryHandler(
        ApplicationDbContext context,
        ILogger<GetEntityQueryHandler> logger)
    {
        _context = context;
        _logger = logger;
    }
    
    public async Task<EntityDto> Handle(
        GetEntityQuery request,
        CancellationToken cancellationToken)
    {
        _logger.LogInformation("Querying entity with ID: {EntityId}", request.Id);
        
        // Query optimized for reads (AsNoTracking)
        var entity = await _context.Entities
            .AsNoTracking()
            .Where(e => e.Id == request.Id)
            .Select(e => new EntityDto(
                e.Id,
                e.FirstName,
                e.LastName,
                e.Email,
                e.CreatedAt))
            .FirstOrDefaultAsync(cancellationToken);
        
        if (entity == null)
        {
            throw new NotFoundException($"Entity with ID {request.Id} not found");
        }
        
        return entity;
    }
}
```

**MediatR Configuration:**
```csharp
// Program.cs
builder.Services.AddMediatR(cfg =>
{
    cfg.RegisterServicesFromAssembly(typeof(CreateEntityCommandHandler).Assembly);
});

// Add validation pipeline behavior
builder.Services.AddTransient(typeof(IPipelineBehavior<,>), typeof(ValidationBehavior<,>));

// Validation behavior
public class ValidationBehavior<TRequest, TResponse> : IPipelineBehavior<TRequest, TResponse>
    where TRequest : IRequest<TResponse>
{
    private readonly IEnumerable<IValidator<TRequest>> _validators;
    
    public ValidationBehavior(IEnumerable<IValidator<TRequest>> validators)
    {
        _validators = validators;
    }
    
    public async Task<TResponse> Handle(
        TRequest request,
        RequestHandlerDelegate<TResponse> next,
        CancellationToken cancellationToken)
    {
        if (_validators.Any())
        {
            var context = new ValidationContext<TRequest>(request);
            
            var validationResults = await Task.WhenAll(
                _validators.Select(v => v.ValidateAsync(context, cancellationToken)));
            
            var failures = validationResults
                .SelectMany(r => r.Errors)
                .Where(f => f != null)
                .ToList();
            
            if (failures.Any())
            {
                throw new ValidationException(failures);
            }
        }
        
        return await next();
    }
}
```

## Testing Standards

The project has strict requirements for code quality and maintainability. These are the unit testing standards and best practices that must be applied.

### Test File Structure
- Use descriptive test file names: `[ComponentName]Tests.cs`
- Organize tests in separate test projects (e.g., `ServiceName.Tests.Unit`, `ServiceName.Tests.Integration`)
- Use xUnit as the primary testing framework
- Maintain 90% coverage threshold for branches, functions, lines, and statements

### Test Organization Pattern

Template:
```csharp
public class EntityServiceTests
{
    private readonly Mock<IEntityRepository> _repositoryMock;
    private readonly Mock<ILogger<EntityService>> _loggerMock;
    private readonly EntityService _sut; // System Under Test
    
    public EntityServiceTests()
    {
        _repositoryMock = new Mock<IEntityRepository>();
        _loggerMock = new Mock<ILogger<EntityService>>();
        _sut = new EntityService(_repositoryMock.Object, _loggerMock.Object);
    }
    
    [Fact]
    public async Task GetEntityAsync_WithValidId_ReturnsEntity()
    {
        // Arrange
        var entityId = 1;
        var expectedEntity = new Entity("John", "Doe", "john@example.com") { Id = entityId };
        _repositoryMock
            .Setup(r => r.GetByIdAsync(entityId, It.IsAny<CancellationToken>()))
            .ReturnsAsync(expectedEntity);
        
        // Act
        var result = await _sut.GetEntityAsync(entityId);
        
        // Assert
        result.Should().NotBeNull();
        result.Should().BeEquivalentTo(expectedEntity);
        _repositoryMock.Verify(
            r => r.GetByIdAsync(entityId, It.IsAny<CancellationToken>()),
            Times.Once);
    }
    
    [Fact]
    public async Task GetEntityAsync_WithInvalidId_ThrowsNotFoundException()
    {
        // Arrange
        var entityId = 999;
        _repositoryMock
            .Setup(r => r.GetByIdAsync(entityId, It.IsAny<CancellationToken>()))
            .ReturnsAsync((Entity?)null);
        
        // Act & Assert
        await Assert.ThrowsAsync<NotFoundException>(
            () => _sut.GetEntityAsync(entityId));
    }
}
```

Real example with theory and inline data:
```csharp
public class EntityValidationTests
{
    [Theory]
    [InlineData("")]
    [InlineData(" ")]
    [InlineData(null)]
    public void ValidateEmail_WithInvalidEmail_ThrowsArgumentException(string invalidEmail)
    {
        // Arrange
        var entity = new Entity("John", "Doe", "valid@example.com");
        
        // Act & Assert
        Assert.Throws<ArgumentException>(() => entity.SetEmail(invalidEmail));
    }
    
    [Theory]
    [InlineData("test@example.com")]
    [InlineData("user.name+tag@domain.co.uk")]
    public void ValidateEmail_WithValidEmail_DoesNotThrow(string validEmail)
    {
        // Arrange
        var entity = new Entity("John", "Doe", "old@example.com");
        
        // Act
        var exception = Record.Exception(() => entity.SetEmail(validEmail));
        
        // Assert
        exception.Should().BeNull();
        entity.Email.Should().Be(validEmail);
    }
}
```

### Test Case Naming Convention
- Use descriptive, behavior-driven naming: `[MethodName]_[Scenario]_[ExpectedResult]`
- Use PascalCase for test method names
- Group related test cases in the same test class
- Use `[Fact]` for single test cases and `[Theory]` for parameterized tests

### Test Structure (AAA Pattern)

Always follow the Arrange-Act-Assert pattern:
```csharp
[Fact]
public async Task CreateEntityAsync_WithValidData_ReturnsCreatedEntity()
{
    // Arrange - Set up test data and mocks
    var command = new CreateEntityCommand("John", "Doe", "john@example.com");
    var expectedEntity = new Entity(command.FirstName, command.LastName, command.Email);
    
    _repositoryMock
        .Setup(r => r.AddAsync(It.IsAny<Entity>(), It.IsAny<CancellationToken>()))
        .ReturnsAsync(expectedEntity);
    
    // Act - Execute the method under test
    var result = await _sut.CreateEntityAsync(command);
    
    // Assert - Verify the expected behavior
    result.Should().NotBeNull();
    result.Email.Should().Be(command.Email);
    _repositoryMock.Verify(
        r => r.AddAsync(It.IsAny<Entity>(), It.IsAny<CancellationToken>()),
        Times.Once);
}
```

Assertion pattern with FluentAssertions:
```csharp
// Value assertions
result.Should().NotBeNull();
result.Id.Should().BePositive();
result.Email.Should().Be("john@example.com");
result.CreatedAt.Should().BeCloseTo(DateTime.UtcNow, TimeSpan.FromSeconds(1));

// Collection assertions
entities.Should().HaveCount(5);
entities.Should().Contain(e => e.Email == "john@example.com");
entities.Should().BeInAscendingOrder(e => e.CreatedAt);

// Exception assertions
await action.Should().ThrowAsync<NotFoundException>()
    .WithMessage("Entity not found*");

// Mock verification
_repositoryMock.Verify(
    r => r.GetByIdAsync(1, It.IsAny<CancellationToken>()),
    Times.Once);
```

### Mocking Standards

- Mock all external dependencies (repositories, services, external APIs)
- Mock repository layers in service tests
- Mock service layers in controller tests
- Use Moq for creating mocks
- Create reusable mock setups for common scenarios
- Clear or reset mocks when necessary

```csharp
public class EntityServiceTests : IDisposable
{
    private readonly Mock<IEntityRepository> _repositoryMock;
    private readonly Mock<IMessageBus> _messageBusMock;
    private readonly Mock<ILogger<EntityService>> _loggerMock;
    private readonly EntityService _sut;
    
    public EntityServiceTests()
    {
        _repositoryMock = new Mock<IEntityRepository>();
        _messageBusMock = new Mock<IMessageBus>();
        _loggerMock = new Mock<ILogger<EntityService>>();
        
        _sut = new EntityService(
            _repositoryMock.Object,
            _messageBusMock.Object,
            _loggerMock.Object);
    }
    
    public void Dispose()
    {
        // Cleanup if necessary
    }
}
```

### Test Coverage Requirements

- **Comprehensive test coverage**: Include these test categories for each method:
  1. **Happy Path Tests**: Valid inputs producing expected outputs
  2. **Error Handling Tests**: Invalid inputs, missing data, database errors
  3. **Edge Cases**: Boundary values, null inputs, empty collections
  4. **Validation Tests**: Input validation, business rule enforcement
  5. **Integration Points**: External service calls, database operations

- **Threshold**: 90% for branches, functions, lines, and statements
- **Coverage Reports**: Generate coverage reports with `dotnet test --collect:"XPlat Code Coverage"`
- **Coverage Tools**: Use tools like Coverlet for code coverage and ReportGenerator for HTML reports

```powershell
# Run tests with coverage
dotnet test --collect:"XPlat Code Coverage"

# Generate HTML report
reportgenerator `
  -reports:"**/coverage.cobertura.xml" `
  -targetdir:"coverage-report" `
  -reporttypes:Html
```

### Error Testing

- Test both expected domain exceptions and unexpected errors
- Verify error messages are descriptive and helpful
- Test error propagation through service layers
- Ensure proper HTTP status codes in controller tests

```csharp
[Fact]
public async Task GetEntityAsync_WhenEntityNotFound_ThrowsNotFoundException()
{
    // Arrange
    _repositoryMock
        .Setup(r => r.GetByIdAsync(It.IsAny<int>(), It.IsAny<CancellationToken>()))
        .ReturnsAsync((Entity?)null);
    
    // Act & Assert
    await Assert.ThrowsAsync<NotFoundException>(
        () => _sut.GetEntityAsync(999));
    
    // Verify logging occurred
    _loggerMock.Verify(
        x => x.Log(
            LogLevel.Warning,
            It.IsAny<EventId>(),
            It.Is<It.IsAnyType>((v, t) => true),
            It.IsAny<Exception>(),
            It.Is<Func<It.IsAnyType, Exception?, string>>((v, t) => true)),
        Times.Once);
}
```

### Controller Testing Specifics

- Mock the mediator (when using MediatR/CQRS)
- Test HTTP request/response handling
- Verify status codes and response structure
- Test model validation

```csharp
public class EntitiesControllerTests
{
    private readonly Mock<IMediator> _mediatorMock;
    private readonly EntitiesController _controller;
    
    public EntitiesControllerTests()
    {
        _mediatorMock = new Mock<IMediator>();
        _controller = new EntitiesController(_mediatorMock.Object);
    }
    
    [Fact]
    public async Task CreateEntity_WithValidCommand_ReturnsCreatedAtAction()
    {
        // Arrange
        var command = new CreateEntityCommand("John", "Doe", "john@example.com");
        var expectedDto = new EntityDto(1, "John", "Doe", "john@example.com", DateTime.UtcNow);
        
        _mediatorMock
            .Setup(m => m.Send(command, It.IsAny<CancellationToken>()))
            .ReturnsAsync(expectedDto);
        
        // Act
        var result = await _controller.CreateEntity(command, CancellationToken.None);
        
        // Assert
        var createdResult = result.Result.Should().BeOfType<CreatedAtActionResult>().Subject;
        createdResult.StatusCode.Should().Be(201);
        createdResult.Value.Should().BeEquivalentTo(expectedDto);
    }
}
```

### Service Testing Specifics

- Mock domain repositories and external services
- Test business logic in isolation
- Verify data transformation and validation
- Test error handling and edge cases
- Test event publishing

```csharp
[Fact]
public async Task CreateEntityAsync_WhenSuccessful_PublishesIntegrationEvent()
{
    // Arrange
    var command = new CreateEntityCommand("John", "Doe", "john@example.com");
    var entity = new Entity(command.FirstName, command.LastName, command.Email) { Id = 1 };
    
    _repositoryMock
        .Setup(r => r.AddAsync(It.IsAny<Entity>(), It.IsAny<CancellationToken>()))
        .ReturnsAsync(entity);
    
    // Act
    await _sut.CreateEntityAsync(command);
    
    // Assert
    _messageBusMock.Verify(
        m => m.PublishAsync(
            It.Is<EntityCreatedIntegrationEvent>(e => 
                e.EntityId == entity.Id && 
                e.Email == entity.Email),
            It.IsAny<CancellationToken>()),
        Times.Once);
}
```

### Database Testing

- Use in-memory database for unit tests (with caution)
- Use TestContainers for integration tests with real PostgreSQL
- Test both successful and failed database operations
- Verify correct queries and parameters
- Test transaction handling

```csharp
// Integration test with TestContainers
public class EntityRepositoryIntegrationTests : IAsyncLifetime
{
    private PostgreSqlContainer _container;
    private ApplicationDbContext _context;
    private EntityRepository _repository;
    
    public async Task InitializeAsync()
    {
        _container = new PostgreSqlBuilder()
            .WithDatabase("testdb")
            .WithUsername("postgres")
            .WithPassword("postgres")
            .Build();
        
        await _container.StartAsync();
        
        var options = new DbContextOptionsBuilder<ApplicationDbContext>()
            .UseNpgsql(_container.GetConnectionString())
            .Options;
        
        _context = new ApplicationDbContext(options);
        await _context.Database.MigrateAsync();
        
        _repository = new EntityRepository(_context, Mock.Of<ILogger<EntityRepository>>());
    }
    
    public async Task DisposeAsync()
    {
        await _context.DisposeAsync();
        await _container.DisposeAsync();
    }
    
    [Fact]
    public async Task AddAsync_WithValidEntity_PersistsToDatabase()
    {
        // Arrange
        var entity = new Entity("John", "Doe", "john@example.com");
        
        // Act
        var result = await _repository.AddAsync(entity);
        
        // Assert
        result.Id.Should().BeGreaterThan(0);
        var savedEntity = await _context.Entities.FindAsync(result.Id);
        savedEntity.Should().NotBeNull();
        savedEntity!.Email.Should().Be("john@example.com");
    }
}
```

### Async Testing

- Always use async/await for asynchronous operations
- Use `Task` return type for async test methods
- Test cancellation token handling
- Test timeout scenarios where applicable

```csharp
[Fact]
public async Task GetEntityAsync_WithCancellation_ThrowsOperationCanceledException()
{
    // Arrange
    var cts = new CancellationTokenSource();
    cts.Cancel();
    
    _repositoryMock
        .Setup(r => r.GetByIdAsync(It.IsAny<int>(), It.IsAny<CancellationToken>()))
        .ThrowsAsync(new OperationCanceledException());
    
    // Act & Assert
    await Assert.ThrowsAsync<OperationCanceledException>(
        () => _sut.GetEntityAsync(1, cts.Token));
}
```

### Database Testing
- Mock Prisma client and all database operations
- Test both successful and failed database operations
- Verify correct database queries and parameters
- Test transaction handling and rollback scenarios

### Async Testing
- Always use `async/await` for asynchronous operations
- Use `Promise.allSettled()` for testing concurrent operations
- Properly handle promise rejections in tests
- Test timeout scenarios where applicable

### Test Data Management

- Use builder pattern or factory methods for creating test data
- Keep test data consistent and realistic
- Avoid hardcoded values in multiple places
- Use meaningful test data that reflects real-world scenarios

```csharp
// Test data builder pattern
public class EntityBuilder
{
    private string _firstName = "John";
    private string _lastName = "Doe";
    private string _email = "john.doe@example.com";
    
    public EntityBuilder WithFirstName(string firstName)
    {
        _firstName = firstName;
        return this;
    }
    
    public EntityBuilder WithEmail(string email)
    {
        _email = email;
        return this;
    }
    
    public Entity Build()
    {
        return new Entity(_firstName, _lastName, _email);
    }
}

// Usage in tests
var entity = new EntityBuilder()
    .WithFirstName("Jane")
    .WithEmail("jane@example.com")
    .Build();
```

### Integration Testing

- **Controller Testing**: Test HTTP request/response handling with WebApplicationFactory
- **Database Testing**: Test repository implementations with real database using TestContainers
- **End-to-End Flow**: Test complete request flows including middleware

### Integration with Development Workflow

- Run tests before every commit
- Ensure all tests pass before merging
- Use test-driven development when appropriate
- Update tests when modifying existing functionality
- Configure continuous integration to run tests automatically

### Common Anti-Patterns to Avoid

- Don't test implementation details, test behavior
- Don't create overly complex test setups
- Don't ignore failing tests or skip error scenarios
- Don't use real external services in unit tests
- Don't create tests that depend on execution order
- Don't write tests that are too tightly coupled to implementation

## Performance Best Practices

### Database Query Optimization

- **Select Specific Fields**: Only select fields that are needed using projections
- **Use Indexes**: Ensure proper database indexes for frequently queried fields
- **Avoid N+1 Queries**: Use EF Core's `Include()` and `ThenInclude()` to fetch related data efficiently
- **AsNoTracking**: Use `AsNoTracking()` for read-only queries to improve performance

```csharp
// Good: Fetch related data efficiently with AsNoTracking
var entities = await _context.Entities
    .AsNoTracking()
    .Include(e => e.Orders)
        .ThenInclude(o => o.Items)
    .Where(e => e.IsActive)
    .ToListAsync(cancellationToken);

// Avoid: N+1 queries
var entities = await _context.Entities.ToListAsync();
foreach (var entity in entities)
{
    var orders = await _context.Orders
        .Where(o => o.EntityId == entity.Id)
        .ToListAsync(); // N+1 query problem
}

// Good: Projection for specific fields
var entityDtos = await _context.Entities
    .AsNoTracking()
    .Select(e => new EntityDto(
        e.Id,
        e.FirstName,
        e.LastName,
        e.Email,
        e.CreatedAt))
    .ToListAsync(cancellationToken);
```

**Database Indexing:**
```csharp
// In entity configuration
public class EntityConfiguration : IEntityTypeConfiguration<Entity>
{
    public void Configure(EntityTypeBuilder<Entity> builder)
    {
        // Single column index
        builder.HasIndex(e => e.Email)
            .IsUnique();
        
        // Composite index
        builder.HasIndex(e => new { e.LastName, e.FirstName });
        
        // Filtered index
        builder.HasIndex(e => e.CreatedAt)
            .HasFilter("is_active = true");
    }
}
```

### Async/Await Patterns

- **Always Use Async/Await**: Use async/await for all I/O operations
- **ConfigureAwait**: Use `ConfigureAwait(false)` in library code to avoid context capture
- **Avoid Blocking**: Never use `.Result` or `.Wait()` on async operations
- **Parallel Operations**: Use `Task.WhenAll()` for parallel independent operations

```csharp
// Good: Async all the way
public async Task<EntityDto> GetEntityAsync(int id, CancellationToken cancellationToken)
{
    var entity = await _repository.GetByIdAsync(id, cancellationToken);
    return MapToDto(entity);
}

// Good: Parallel operations for independent tasks
public async Task<(IEnumerable<Entity>, int)> GetEntitiesWithCountAsync(
    CancellationToken cancellationToken)
{
    var entitiesTask = _repository.GetAllAsync(cancellationToken);
    var countTask = _repository.GetCountAsync(cancellationToken);
    
    await Task.WhenAll(entitiesTask, countTask);
    
    return (entitiesTask.Result, countTask.Result);
}

// Avoid: Blocking async code
public Entity GetEntity(int id)
{
    return _repository.GetByIdAsync(id).Result; // BAD: Deadlock risk
}

// Avoid: Unnecessary async
public async Task<int> CalculateSum(int a, int b)
{
    return await Task.FromResult(a + b); // BAD: No I/O operation
}

// Good: Synchronous when no I/O
public int CalculateSum(int a, int b)
{
    return a + b;
}
```

### Caching Strategies

- **Response Caching**: Cache HTTP responses for frequently accessed data
- **Distributed Caching**: Use Redis for distributed caching across microservices
- **In-Memory Caching**: Use IMemoryCache for single-instance caching

```csharp
// Distributed cache with Redis
public class CachedEntityRepository : IEntityRepository
{
    private readonly IEntityRepository _repository;
    private readonly IDistributedCache _cache;
    private readonly ILogger<CachedEntityRepository> _logger;
    private static readonly TimeSpan CacheDuration = TimeSpan.FromMinutes(10);
    
    public CachedEntityRepository(
        IEntityRepository repository,
        IDistributedCache cache,
        ILogger<CachedEntityRepository> logger)
    {
        _repository = repository;
        _cache = cache;
        _logger = logger;
    }
    
    public async Task<Entity?> GetByIdAsync(int id, CancellationToken cancellationToken = default)
    {
        var cacheKey = $"entity:{id}";
        
        // Try get from cache
        var cachedData = await _cache.GetStringAsync(cacheKey, cancellationToken);
        if (cachedData != null)
        {
            _logger.LogDebug("Cache hit for entity {EntityId}", id);
            return JsonSerializer.Deserialize<Entity>(cachedData);
        }
        
        // Get from database
        _logger.LogDebug("Cache miss for entity {EntityId}", id);
        var entity = await _repository.GetByIdAsync(id, cancellationToken);
        
        if (entity != null)
        {
            // Store in cache
            var serialized = JsonSerializer.Serialize(entity);
            await _cache.SetStringAsync(
                cacheKey,
                serialized,
                new DistributedCacheEntryOptions
                {
                    AbsoluteExpirationRelativeToNow = CacheDuration
                },
                cancellationToken);
        }
        
        return entity;
    }
}

// Redis configuration in Program.cs
builder.Services.AddStackExchangeRedisCache(options =>
{
    options.Configuration = builder.Configuration["Redis:ConnectionString"];
    options.InstanceName = "EntityService:";
});

// Decorator pattern registration
builder.Services.AddScoped<IEntityRepository, EntityRepository>();
builder.Services.Decorate<IEntityRepository, CachedEntityRepository>();
```

## Security Best Practices

### Input Validation

- **Validate All Inputs**: Validate all user inputs before processing
- **Use FluentValidation**: Centralize validation logic
- **Sanitize Data**: Prevent injection attacks
- **Type Safety**: Leverage C#'s type system

```csharp
public class CreateEntityCommandValidator : AbstractValidator<CreateEntityCommand>
{
    public CreateEntityCommandValidator()
    {
        RuleFor(x => x.FirstName)
            .NotEmpty().WithMessage("First name is required")
            .MaximumLength(100).WithMessage("First name cannot exceed 100 characters")
            .Matches("^[a-zA-Z ]*$").WithMessage("First name can only contain letters and spaces");
        
        RuleFor(x => x.Email)
            .NotEmpty().WithMessage("Email is required")
            .EmailAddress().WithMessage("Invalid email format")
            .MaximumLength(255);
        
        RuleFor(x => x.Password)
            .NotEmpty()
            .MinimumLength(8)
            .Matches(@"[A-Z]").WithMessage("Password must contain at least one uppercase letter")
            .Matches(@"[a-z]").WithMessage("Password must contain at least one lowercase letter")
            .Matches(@"[0-9]").WithMessage("Password must contain at least one number")
            .Matches(@"[\W]").WithMessage("Password must contain at least one special character");
    }
}
```

### Configuration Management

- **Never Commit Secrets**: Never commit sensitive data to version control
- **Use User Secrets**: Use user secrets for development
- **Environment Variables**: Use environment variables for production
- **Azure Key Vault**: Use Azure Key Vault or similar for production secrets

```csharp
// User Secrets for development (not committed to source control)
// Run: dotnet user-secrets init
// Run: dotnet user-secrets set "ConnectionStrings:DefaultConnection" "Host=localhost;..."

// Configuration setup in Program.cs
var builder = WebApplication.CreateBuilder(args);

if (builder.Environment.IsDevelopment())
{
    builder.Configuration.AddUserSecrets<Program>();
}

// Azure Key Vault for production
if (builder.Environment.IsProduction())
{
    var keyVaultEndpoint = builder.Configuration["KeyVault:Endpoint"];
    if (!string.IsNullOrEmpty(keyVaultEndpoint))
    {
        builder.Configuration.AddAzureKeyVault(
            new Uri(keyVaultEndpoint),
            new DefaultAzureCredential());
    }
}

// Validate required configuration at startup
var requiredSettings = new[]
{
    "ConnectionStrings:DefaultConnection",
    "MessageBroker:Host"
};

foreach (var setting in requiredSettings)
{
    if (string.IsNullOrEmpty(builder.Configuration[setting]))
    {
        throw new InvalidOperationException(
            $"Required configuration setting '{setting}' is missing");
    }
}
```

### Dependency Injection

- **Use Built-in DI**: Use ASP.NET Core's built-in dependency injection
- **Register by Interface**: Always register services by their interface
- **Lifetime Management**: Choose appropriate service lifetimes (Transient, Scoped, Singleton)
- **Avoid Service Locator**: Don't use service locator anti-pattern

```csharp
// Program.cs - Service registration
// Transient: Created each time they're requested
builder.Services.AddTransient<IEmailService, EmailService>();

// Scoped: Created once per request
builder.Services.AddScoped<IEntityRepository, EntityRepository>();
builder.Services.AddScoped<EntityService>();

// Singleton: Created once for the application lifetime
builder.Services.AddSingleton<IMessageBus, RabbitMqMessageBus>();

// DbContext (Scoped by default)
builder.Services.AddDbContext<ApplicationDbContext>(options =>
    options.UseNpgsql(builder.Configuration.GetConnectionString("DefaultConnection")));

// Generic repositories
builder.Services.AddScoped(typeof(IRepository<>), typeof(Repository<>));
```

## Development Workflow

### Git Workflow

- **Feature Branches**: Develop features in separate branches with descriptive names
- **Branch Naming**: Use conventional naming (e.g., `feature/entity-management`, `bugfix/email-validation`)
- **Descriptive Commits**: Write descriptive commit messages in English following conventional commits
- **Pull Requests**: Code review before merging to main branch
- **Small Changes**: Keep branches small and focused on single features

```powershell
# Create feature branch
git checkout -b feature/add-entity-service

# Commit with descriptive message
git add .
git commit -m "feat: add entity service with CRUD operations"

# Push and create pull request
git push -u origin feature/add-entity-service
```

### Development Scripts

```powershell
# Restore dependencies
dotnet restore

# Build solution
dotnet build

# Run application
dotnet run --project src/ServiceName.API

# Run tests
dotnet test

# Run tests with coverage
dotnet test --collect:"XPlat Code Coverage"

# Create migration
dotnet ef migrations add MigrationName --project src/ServiceName.Infrastructure --startup-project src/ServiceName.API

# Apply migrations
dotnet ef database update --project src/ServiceName.Infrastructure --startup-project src/ServiceName.API

# Format code
dotnet format

# Clean solution
dotnet clean
```

### Code Quality

- **Static Analysis**: Run code analysis before commits
- **Code Formatting**: Use EditorConfig and dotnet format
- **All Tests Passing**: Ensure all tests pass before deployment
- **Code Review**: Review code for adherence to standards

```xml
<!-- .editorconfig -->
root = true

[*.cs]
indent_style = space
indent_size = 4
end_of_line = crlf
charset = utf-8
trim_trailing_whitespace = true
insert_final_newline = true

# Naming conventions
dotnet_naming_rule.interfaces_should_be_pascal_case_prefixed_with_i.severity = warning
dotnet_naming_rule.interfaces_should_be_pascal_case_prefixed_with_i.symbols = interface
dotnet_naming_rule.interfaces_should_be_pascal_case_prefixed_with_i.style = begins_with_i

# Code style rules
csharp_prefer_braces = true:warning
csharp_prefer_simple_using_statement = true:suggestion
csharp_style_namespace_declarations = file_scoped:warning
```

## Containerization & Deployment

### Docker Configuration

- **Multi-Stage Builds**: Use multi-stage builds for smaller images
- **Non-Root User**: Run containers as non-root user
- **Health Checks**: Implement health check endpoints

```dockerfile
# Dockerfile
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src

# Copy csproj files and restore dependencies
COPY ["src/ServiceName.API/ServiceName.API.csproj", "src/ServiceName.API/"]
COPY ["src/ServiceName.Application/ServiceName.Application.csproj", "src/ServiceName.Application/"]
COPY ["src/ServiceName.Domain/ServiceName.Domain.csproj", "src/ServiceName.Domain/"]
COPY ["src/ServiceName.Infrastructure/ServiceName.Infrastructure.csproj", "src/ServiceName.Infrastructure/"]
RUN dotnet restore "src/ServiceName.API/ServiceName.API.csproj"

# Copy everything else and build
COPY . .
WORKDIR "/src/src/ServiceName.API"
RUN dotnet build "ServiceName.API.csproj" -c Release -o /app/build

# Publish
FROM build AS publish
RUN dotnet publish "ServiceName.API.csproj" -c Release -o /app/publish /p:UseAppHost=false

# Final stage
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS final
WORKDIR /app
EXPOSE 80
EXPOSE 443

# Create non-root user
RUN adduser --disabled-password --gecos '' appuser && chown -R appuser /app
USER appuser

COPY --from=publish /app/publish .

HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD curl --fail http://localhost:80/health || exit 1

ENTRYPOINT ["dotnet", "ServiceName.API.dll"]
```

```yaml
# docker-compose.yml
version: '3.8'

services:
  api:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "5000:80"
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ConnectionStrings__DefaultConnection=Host=postgres;Port=5432;Database=mydb;Username=postgres;Password=postgres
      - MessageBroker__Host=rabbitmq
    depends_on:
      - postgres
      - rabbitmq
    networks:
      - backend

  postgres:
    image: postgres:15
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
      POSTGRES_DB: mydb
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - backend

  rabbitmq:
    image: rabbitmq:3-management
    ports:
      - "5672:5672"
      - "15672:15672"
    environment:
      RABBITMQ_DEFAULT_USER: guest
      RABBITMQ_DEFAULT_PASS: guest
    networks:
      - backend

volumes:
  postgres_data:

networks:
  backend:
```

### Kubernetes Deployment

- **Deployment Manifests**: Define Kubernetes deployments
- **ConfigMaps and Secrets**: Use for configuration
- **Health Checks**: Configure liveness and readiness probes
- **Resource Limits**: Set CPU and memory limits

```yaml
# deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: entity-service
  labels:
    app: entity-service
spec:
  replicas: 3
  selector:
    matchLabels:
      app: entity-service
  template:
    metadata:
      labels:
        app: entity-service
    spec:
      containers:
      - name: api
        image: entity-service:latest
        ports:
        - containerPort: 80
        env:
        - name: ASPNETCORE_ENVIRONMENT
          value: "Production"
        - name: ConnectionStrings__DefaultConnection
          valueFrom:
            secretKeyRef:
              name: db-secrets
              key: connection-string
        resources:
          requests:
            memory: "256Mi"
            cpu: "250m"
          limits:
            memory: "512Mi"
            cpu: "500m"
        livenessProbe:
          httpGet:
            path: /health
            port: 80
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /health/ready
            port: 80
          initialDelaySeconds: 5
          periodSeconds: 5
---
apiVersion: v1
kind: Service
metadata:
  name: entity-service
spec:
  selector:
    app: entity-service
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
  type: LoadBalancer
```

This document serves as the foundation for maintaining code quality and consistency across .NET backend applications with event-driven microservices architecture. All team members should follow these practices to ensure a maintainable, scalable, and testable codebase.
