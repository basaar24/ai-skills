# Development Guide

This guide provides step-by-step instructions for setting up the development environment and running tests for the application. It uses Windows commands and examples with .NET, PostgreSQL, and event-driven microservices architecture.

## 🚀 Setup Instructions

### Prerequisites

Ensure you have the following installed:
- **.NET SDK** (version 6.0 or higher recommended)
- **PostgreSQL** (version 12 or higher)
- **Docker Desktop** for Windows (if using containerized services)
- **Git** for Windows
- **Visual Studio** or **Visual Studio Code** (recommended IDE)
- **Message broker** (e.g., RabbitMQ, Azure Service Bus, or Kafka) for event-driven architecture

### 1. Clone the Repository

```powershell
git clone <your-repository-url>
cd <your-project-directory>
```

### 2. Environment Configuration

Create environment configuration files for each microservice:

**Service Configuration** (`appsettings.Development.json`):
```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Host=localhost;Port=5432;Database=<your-db-name>;Username=<your-db-user>;Password=<your-db-password>"
  },
  "MessageBroker": {
    "Host": "localhost",
    "Port": "<broker-port>",
    "Username": "<broker-user>",
    "Password": "<broker-password>",
    "VirtualHost": "/"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  }
}
```

### 3. Database Setup

Start PostgreSQL service (example with Docker on Windows):

```powershell
# Start PostgreSQL container
docker run -d --name postgres-dev `
  -e POSTGRES_USER=<your-db-user> `
  -e POSTGRES_PASSWORD=<your-db-password> `
  -e POSTGRES_DB=<your-db-name> `
  -p 5432:5432 `
  postgres:latest

# Verify the database is running
docker ps
```

Database configuration:
- **Host**: `localhost`
- **Port**: `5432`
- **Database**: `<your-db-name>`
- **Username**: `<your-db-user>`
- **Password**: `<your-db-password>`

### 4. Message Broker Setup

Start your message broker service (example with RabbitMQ):

```powershell
# Start RabbitMQ container
docker run -d --name rabbitmq-dev `
  -p 5672:5672 `
  -p 15672:15672 `
  -e RABBITMQ_DEFAULT_USER=<broker-user> `
  -e RABBITMQ_DEFAULT_PASS=<broker-password> `
  rabbitmq:management

# Access management UI at http://localhost:15672
```

### 5. Service Setup

For each microservice in your solution:

```powershell
# Navigate to service directory
cd src\<ServiceName>

# Restore dependencies
dotnet restore

# Apply database migrations
dotnet ef database update

# (Optional) Seed the database with sample data
dotnet run --seed

# Start the development server
dotnet run
```

The service API will be available at the configured port (typically `http://localhost:5000` or `https://localhost:5001`)

### 6. Client Application Setup

If your solution includes a client application:

```powershell
# Navigate to client directory
cd src\<ClientApp>

# Restore dependencies
dotnet restore

# Start the development server
dotnet run
```

### 7. Testing Suite Setup

```powershell
# From the solution root or test project directory

# Restore test dependencies
dotnet restore

# Run all tests
dotnet test
```

## 🧪 Testing

### Unit Testing

```powershell
# Run all unit tests
dotnet test --filter Category=Unit

# Run tests with coverage
dotnet test --collect:"XPlat Code Coverage"

# Run tests with detailed output
dotnet test --logger "console;verbosity=detailed"
```

### Integration Testing

```powershell
# Run integration tests (may require running infrastructure)
dotnet test --filter Category=Integration

# Run with environment setup
docker-compose -f docker-compose.test.yml up -d
dotnet test --filter Category=Integration
docker-compose -f docker-compose.test.yml down
```

### End-to-End Testing

```powershell
# Run E2E tests
dotnet test --filter Category=E2E

# Run with specific configuration
dotnet test --filter Category=E2E --settings test.runsettings
```

## 📝 Additional Commands

### Database Management

```powershell
# Create a new migration
dotnet ef migrations add <MigrationName> --project <ProjectPath>

# Apply migrations
dotnet ef database update --project <ProjectPath>

# Rollback to specific migration
dotnet ef database update <PreviousMigrationName> --project <ProjectPath>

# Remove last migration (if not applied)
dotnet ef migrations remove --project <ProjectPath>

# Generate SQL script from migrations
dotnet ef migrations script --project <ProjectPath> --output migration.sql

# Reset database
dotnet ef database drop --force --project <ProjectPath>
dotnet ef database update --project <ProjectPath>
```

### Build for Production

```powershell
# Build solution in Release mode
dotnet build --configuration Release

# Publish service for deployment
dotnet publish --configuration Release --output ./publish

# Create Docker image for service
docker build -t <service-name>:<version> .
```

### Code Quality

```powershell
# Format code
dotnet format

# Run static analysis (if configured)
dotnet build /p:RunAnalyzers=true /p:TreatWarningsAsErrors=true

# Run security scan (requires additional tools)
dotnet list package --vulnerable --include-transitive
```

## 🏗️ Microservices Architecture

### Running Multiple Services

```powershell
# Start all services with Docker Compose
docker-compose up -d

# View logs from all services
docker-compose logs -f

# Stop all services
docker-compose down

# Rebuild and restart services
docker-compose up -d --build
```

### Event-Driven Communication

Services communicate via message broker. Key patterns:

- **Event Publishing**: Services publish domain events when state changes occur
- **Event Subscription**: Services subscribe to events they need to react to
- **Command Messages**: Direct service-to-service commands for orchestration
- **Query Messages**: Request-response patterns for data retrieval

## 🐛 Troubleshooting

### Common Issues

**Database connection errors:**
- Verify PostgreSQL is running: `docker ps` or check Windows Services
- Test connection: `psql -h localhost -U <user> -d <database>`
- Check connection string in `appsettings.Development.json`
- Ensure firewall allows port 5432

**Message broker connection errors:**
- Verify broker is running: `docker ps`
- Check broker credentials in configuration
- Verify network connectivity to broker host
- Check broker logs: `docker logs rabbitmq-dev`

**Port already in use:**
- Check process using port: `netstat -ano | findstr :<port>`
- Stop process: `taskkill /PID <process-id> /F`
- Or change port in `launchSettings.json` or configuration

**Migration errors:**
- Check migration files for syntax errors
- Verify database connection
- Reset database: `dotnet ef database drop --force && dotnet ef database update`
- Check Entity Framework Core version compatibility

**Service discovery issues:**
- Verify all dependent services are running
- Check service registration configuration
- Review network/DNS configuration
- Validate service URLs in configuration

## 📚 Additional Resources

- [Project Documentation](./README.md)
- [Backend Standards](./backend-standards.md)
- [Frontend Standards](./frontend-standards.md)
- [API Specification](./api-spec.yml)
- [Data Model](./data-model.md)
- [Architecture Decision Records](./architecture/)
- [Event Catalog](./events.md)
