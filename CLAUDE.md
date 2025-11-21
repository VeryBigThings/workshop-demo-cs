# eShopOnWeb - Workshop Demo Application

This is a .NET 9 e-commerce reference application by NimblePros/Microsoft, used for Claude Code workshop training.

## Application Overview

**Domain:** E-commerce platform (online store)
**Architecture:** Monolithic with Clean Architecture + Domain-Driven Design
**Purpose:** Educational reference application demonstrating .NET best practices

## Technology Stack

- **Framework:** ASP.NET Core 9.0
- **Language:** C# 12
- **Frontend:** Razor Pages + Blazor Admin
- **ORM:** Entity Framework Core
- **Database:** SQL Server 2022
- **Testing:** xUnit
- **Build Tool:** .NET CLI

## Database Schema

**Main Entities:**
- **CatalogItem** (Products) - Items for sale with pricing, images
- **Basket** - Shopping cart with items
- **Order** - Completed purchases with order items
- **CatalogBrand** - Product brands
- **CatalogType** - Product categories

**Databases:**
- **Microsoft.eShopOnWeb.CatalogDb** - Products, orders, baskets
- **Microsoft.eShopOnWeb.Identity** - User authentication

## Setup Instructions

### Prerequisites

1. **.NET 9 SDK** - Required for building and running
2. **Docker Desktop** - For SQL Server container
3. **Git** - For version control

### Quick Start

**1. Start SQL Server:**
```bash
docker run -e "ACCEPT_EULA=Y" \
  -e "MSSQL_SA_PASSWORD=YourStrong@Passw0rd" \
  -p 1433:1433 --name workshop-db-cs -d \
  mcr.microsoft.com/mssql/server:2022-latest
```

**Wait 30-60 seconds** for SQL Server to fully initialize (especially on ARM Macs).

**2. Build the application:**
```bash
dotnet build eShopOnWeb.sln
```

**3. Trust HTTPS certificate** (first time only):
```bash
dotnet dev-certs https --trust
```

**4. Run database migrations** (automatic on first run):

The application will create and seed databases automatically on first startup.

**5. Start the application:**
```bash
cd src/Web
dotnet run
```

Application will be available at: **https://localhost:5001**

### Verify Installation

**Check database:**
```bash
docker exec -it workshop-db-cs /opt/mssql-tools18/bin/sqlcmd \
  -S localhost -U SA -P 'YourStrong@Passw0rd' -C \
  -Q "SELECT name FROM sys.databases WHERE name LIKE '%eShop%'"
```

Expected output:
- Microsoft.eShopOnWeb.CatalogDb
- Microsoft.eShopOnWeb.Identity

**Access application:**
- Web UI: https://localhost:5001
- Blazor Admin: https://localhost:5001/admin
- API (Swagger): https://localhost:5001/swagger (if configured)

**Demo Users:**
- Admin: admin@microsoft.com / Pass@word1
- Demo User: demouser@microsoft.com / Pass@word1

## Development Commands

### Building & Running

```bash
# Build entire solution
dotnet build eShopOnWeb.sln

# Build specific project
dotnet build src/Web/Web.csproj

# Run web application
cd src/Web && dotnet run

# Run Blazor admin
cd src/BlazorAdmin && dotnet run

# Run PublicApi
cd src/PublicApi && dotnet run
```

### Database Management

```bash
# Check SQL Server status
docker ps | grep workshop-db-cs

# View SQL Server logs
docker logs workshop-db-cs

# Restart SQL Server
docker restart workshop-db-cs

# Connect to database
docker exec -it workshop-db-cs /opt/mssql-tools18/bin/sqlcmd \
  -S localhost -U SA -P 'YourStrong@Passw0rd' -C
```

**Reset databases** (drops and recreates):
```bash
# Stop application first
# Then delete database files or use SQL commands
docker exec -it workshop-db-cs /opt/mssql-tools18/bin/sqlcmd \
  -S localhost -U SA -P 'YourStrong@Passw0rd' -C \
  -Q "DROP DATABASE [Microsoft.eShopOnWeb.CatalogDb]; DROP DATABASE [Microsoft.eShopOnWeb.Identity]"

# Restart application - it will recreate and seed
cd src/Web && dotnet run
```

### Testing

```bash
# Run all tests
dotnet test

# Run tests with coverage
dotnet test /p:CollectCoverage=true

# Run tests for specific project
dotnet test tests/UnitTests/UnitTests.csproj

# Run integration tests
dotnet test tests/IntegrationTests/IntegrationTests.csproj

# Run with detailed output
dotnet test --logger "console;verbosity=detailed"
```

### Code Quality

```bash
# Format code
dotnet format

# Build in Release mode
dotnet build -c Release

# Clean build artifacts
dotnet clean
```

## Project Structure

```
src/
├── Web/                    # Main Razor Pages frontend
│   ├── Pages/             # Razor Pages
│   ├── ViewModels/        # Page models
│   └── Services/          # Application services
├── BlazorAdmin/           # Blazor admin panel
├── PublicApi/             # REST API
├── ApplicationCore/       # Domain layer
│   ├── Entities/         # Domain entities
│   ├── Interfaces/       # Repository interfaces
│   ├── Services/         # Domain services
│   └── Specifications/   # Query specifications
├── Infrastructure/        # Data access layer
│   ├── Data/             # EF Core DbContext
│   │   ├── CatalogContext.cs
│   │   ├── AppIdentityDbContext.cs
│   │   └── Config/       # Entity configurations
│   ├── Identity/         # ASP.NET Identity
│   └── Services/         # Infrastructure services
└── BlazorShared/         # Shared Blazor components
```

## Workshop Usage Notes

### Common Workshop Tasks

**Explore codebase:**
```bash
# View solution structure
dotnet sln list

# View project dependencies
dotnet list reference

# Find specific code patterns
grep -r "IRepository" src/ApplicationCore/
```

**Test coverage analysis:**
```bash
dotnet test /p:CollectCoverage=true /p:CoverageReportsDirectory=./coverage
```

**Database debugging:**
```bash
# Query catalog items
docker exec -it workshop-db-cs /opt/mssql-tools18/bin/sqlcmd \
  -S localhost -U SA -P 'YourStrong@Passw0rd' -C \
  -d "Microsoft.eShopOnWeb.CatalogDb" \
  -Q "SELECT * FROM CatalogItems"
```

### Workshop Scenarios

This application is used to demonstrate:

1. **Test Coverage Improvement** - Add tests for Basket service
2. **N+1 Query Detection** - Find and fix EF Core query inefficiencies
3. **New Feature Implementation** - Add product reviews/ratings
4. **Database Performance** - Optimize slow queries with indexes
5. **Clean Architecture** - Add new features following CA patterns
6. **Code Review** - Review PR changes for quality and security

## Architecture Patterns

**Clean Architecture Layers:**
1. **ApplicationCore** (Domain) - Business logic, entities, interfaces
2. **Infrastructure** - Data access, external services
3. **Web/BlazorAdmin/PublicApi** - Presentation layers

**Key Patterns:**
- Repository Pattern (IRepository, IAsyncRepository)
- Specification Pattern (for complex queries)
- Dependency Injection (ASP.NET Core DI)
- Domain-Driven Design (Aggregates, Value Objects)

## Configuration

### Connection Strings

Located in `src/Web/appsettings.Development.json`:

```json
{
  "ConnectionStrings": {
    "CatalogConnection": "Server=localhost,1433;Database=Microsoft.eShopOnWeb.CatalogDb;User Id=sa;Password=YourStrong@Passw0rd;TrustServerCertificate=true",
    "IdentityConnection": "Server=localhost,1433;Database=Microsoft.eShopOnWeb.Identity;User Id=sa;Password=YourStrong@Passw0rd;TrustServerCertificate=true"
  }
}
```

### Logging

Configured for detailed debugging:
- Console logging
- Debug output
- EF Core query logging (shows SQL)

## Troubleshooting

### Database Connection Issues

```bash
# Check SQL Server is running
docker ps | grep workshop-db-cs

# Check logs for errors
docker logs workshop-db-cs

# Restart container
docker restart workshop-db-cs

# Wait for SQL Server to be ready (ARM Macs: 30-60s)
sleep 30
```

### Port Conflicts

```bash
# Check if port 5001 is in use
lsof -i :5001

# Kill process using port
kill -9 <PID>
```

### Build Issues

```bash
# Clean solution
dotnet clean

# Restore packages
dotnet restore

# Rebuild
dotnet build --no-incremental
```

### Database Migration Issues

```bash
# Drop databases manually
docker exec -it workshop-db-cs /opt/mssql-tools18/bin/sqlcmd \
  -S localhost -U SA -P 'YourStrong@Passw0rd' -C \
  -Q "DROP DATABASE [Microsoft.eShopOnWeb.CatalogDb]; DROP DATABASE [Microsoft.eShopOnWeb.Identity]"

# Restart app to recreate
cd src/Web && dotnet run
```

## Known Issues

### ARM Mac Performance
- SQL Server runs via x86 emulation (slow but works)
- Container startup takes 30-60 seconds
- Native ARM alternative: Azure SQL Edge (but has stability issues)

### Seed Data Retry
- Infrastructure includes retry logic for seed operations
- Handles SQL Server slow startup gracefully

## Resources

**Official Documentation:**
- eShopOnWeb Guide: (in repository)
- .NET 9 Docs: https://learn.microsoft.com/dotnet/core
- EF Core: https://learn.microsoft.com/ef/core
- ASP.NET Core: https://learn.microsoft.com/aspnet/core

**Repositories:**
- VBT Fork: https://github.com/VeryBigThings/eShopOnWeb
- Upstream: https://github.com/NimblePros/eShopOnWeb

## Workshop Integration

This application mirrors the TypeScript demo app (Conduit) for City Furniture workshops:

| Aspect | eShopOnWeb (C#) | Conduit (TypeScript) |
|--------|-----------------|---------------------|
| Domain | E-commerce | Blogging platform |
| Framework | ASP.NET Core | Express.js |
| ORM | Entity Framework Core | Prisma |
| Database | SQL Server | PostgreSQL |
| Tests | xUnit | Jest |
| Language | C# | TypeScript |
| Container | workshop-db-cs | workshop-db-ts |

Both demonstrate Clean Architecture, have comprehensive tests, use relational databases, and provide realistic workshop scenarios.
