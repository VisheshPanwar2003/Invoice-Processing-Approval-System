# Invoice Processing & Approval System

A production-oriented invoice processing and approval system designed to streamline the workflow between suppliers and buyers.

The system manages the complete invoice lifecycle — from supplier submission and purchase-order matching to approval, dispute handling, and due-date calculation.

A key business rule is that the payment due date is **calculated and frozen when the invoice is approved**, preventing approval delays from shifting the payment timeline.

## Overview

Small suppliers often face cash-flow problems because invoices can take 60–90 days to get paid while their own supplier obligations may be due much earlier.

This project addresses the software side of that problem by providing a controlled workflow:

**Submit Invoice → Match Purchase Order → Approve / Dispute → Lock Payment Terms → Track Due Date**

Once an invoice is approved, its payment terms and due date become immutable.

## Key Features

- Supplier invoice submission
- Purchase order matching with configurable tolerance
- Invoice approval and dispute workflow
- Automatic payment due-date calculation
- Immutable payment terms after approval
- SLA-based deemed approval
- Invoice withdrawal for eligible invoices
- Purchase order capacity reservation and consumption
- Domain-driven invoice lifecycle
- Asynchronous invoice-approved event publishing
- Background processing for automated workflows
- Structured application architecture
- Architecture tests enforcing module boundaries
- Automated unit and integration testing
- Azure-based deployment infrastructure
- Application observability with logging and telemetry

## Invoice Lifecycle

```text
Submitted
    │
    ├── Match Purchase Order
    │
    ├── Approve ──────────────┐
    │                         │
    └── Dispute               │
                              ▼
                         Approved
                              │
                              ▼
                    Payment Terms Locked
                              │
                              ▼
                         Due Date Set
```

Invoices that remain pending beyond the configured SLA can be automatically deemed approved.

## Architecture

The application follows a **modular monolith** architecture with clear separation between bounded contexts and application layers.

```text
                    ┌─────────────────────┐
                    │      Web API        │
                    │   Composition Root  │
                    └──────────┬──────────┘
                               │
              ┌────────────────┴────────────────┐
              │                                 │
      ┌───────▼────────┐              ┌─────────▼────────┐
      │   Procurement  │              │     Invoicing     │
      │     Module     │              │      Module       │
      └───────┬────────┘              └─────────┬────────┘
              │                                 │
       Domain / Application /           Domain / Application /
          Infrastructure                  Infrastructure
```

Each module follows:

```text
Domain
   ↓
Application
   ↓
Infrastructure
```

The domain layer contains business rules, while the application layer contains use cases and interfaces. Infrastructure provides implementations for persistence and external dependencies.

The Web project acts as the composition root and wires the application together using dependency injection.

## Technology Stack

### Backend

- C#
- .NET 10
- ASP.NET Core
- Entity Framework Core
- SQL
- REST API

### Architecture & Design

- Modular Monolith
- Domain-Driven Design
- Clean Architecture principles
- Bounded Contexts
- Dependency Inversion
- Domain Events
- Repository Pattern
- Ports & Adapters

### Infrastructure

- Microsoft Azure
- Azure App Service
- Azure SQL
- Azure Service Bus
- Application Insights
- Log Analytics
- OpenTelemetry
- Bicep

### Testing

- xUnit
- NetArchTest
- Unit Testing
- Architecture Testing
- Integration Testing

## Project Structure

```text
capstone/
│
├── src/
│   ├── SharedKernel/
│   │   └── Capstone.SharedKernel/
│   │
│   └── Modules/
│       ├── Procurement/
│       │   ├── Capstone.Procurement.Domain/
│       │   ├── Capstone.Procurement.Application/
│       │   └── Capstone.Procurement.Infrastructure/
│       │
│       └── Invoicing/
│           ├── Capstone.Invoicing.Domain/
│           ├── Capstone.Invoicing.Application/
│           └── Capstone.Invoicing.Infrastructure/
│
├── host/
│   ├── Capstone.Web/
│   └── Capstone.Worker/
│
├── tests/
│   ├── Capstone.Invoicing.Domain.Tests/
│   └── Capstone.ArchitectureTests/
│
├── infra/
├── observability/
├── adr/
├── DESIGN.md
└── Capstone.slnx
```

## Getting Started

### Prerequisites

- .NET 10 SDK
- SQL database
- Azure account if deploying the infrastructure

### Clone the Repository

```bash
git clone <repository-url>
cd capstone
```

### Restore Dependencies

```bash
dotnet restore Capstone.slnx
```

### Build

```bash
dotnet build Capstone.slnx
```

### Run Tests

```bash
dotnet test Capstone.slnx
```

### Run the Application

```bash
dotnet run --project host/Capstone.Web
```

## Testing

The project includes tests covering:

- Invoice domain invariants
- Invoice lifecycle transitions
- Business rules
- Purchase-order capacity behavior
- Architecture and dependency boundaries
- Application workflows

Architecture tests ensure that modules maintain their intended dependency boundaries and prevent unwanted coupling between Procurement and Invoicing.

## Infrastructure & Observability

The project includes infrastructure-as-code for deploying the application to Azure.

Infrastructure components include:

- Azure App Service
- Azure SQL
- Azure Service Bus
- Application Insights
- Log Analytics
- Virtual Network integration
- Private database connectivity
- OpenTelemetry-based observability

Bicep templates are included in the `infra/` directory.

## Current Limitations

The following capabilities are intentionally outside the current scope:

- No frontend UI
- No real payment processing
- No financing or credit assessment
- Identity mapping between authenticated users and supplier/buyer accounts is not fully implemented
- Payment terms currently use an in-memory lookup
- Disputed invoices do not currently have automatic expiry
- No downstream consumer currently processes the published `InvoiceApproved` event
- Matching tolerance currently uses a global default

## Future Improvements

Potential future extensions include:

- Supplier and buyer web interfaces
- Full authentication and authorization
- Real payment processing integration
- Dedicated Payment Terms bounded context
- Automated dispute resolution
- Configurable matching rules per buyer-supplier relationship
- Financing integration
- Event consumers for downstream financing workflows
- Production-grade persistence and external service integrations

## License

This project is intended as a software engineering portfolio project demonstrating production-oriented backend architecture, domain modeling, testing, asynchronous processing, and cloud infrastructure.