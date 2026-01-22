# Open Data Interchange Framework (OpenDIF)
**_Secure Data Exchange Platform_**


[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://www.apache.org/licenses/LICENSE-2.0)
![CI](https://img.shields.io/github/actions/workflow/status/OpenDIF/opendif-core/integration-tests.yml)
![Security Scan](https://img.shields.io/github/actions/workflow/status/OpenDIF/opendif-core/security.yml)
![Last Commit](https://img.shields.io/github/last-commit/OpenDIF/opendif-core)

**OpenDIF** is an open-source platform that transforms how organizations exchange data. Instead of dealing with multiple disconnected data sources, OpenDIF acts as an intelligent intermediary that provides a single, unified point of access. Whether you're a large company, university, or government agency, this framework simplifies complex data requests by automatically coordinating between data providers, handling authorization, managing consent, and aggregating results into a single response.

<p align="center">
  •   <a href="#why-opendif">Why OpenDIF?</a> •
  <a href="#key-features">Key Features</a> •
  <a href="#getting-started">Getting Started</a> •
  <a href="#architecture">Architecture</a> •
  <a href="#contributing">Contributing</a> •
  <a href="#license">License</a> •
</p>

## Why OpenDIF?

OpenDIF is designed to solve the complex challenge of secure, compliant data exchange across organizations. Traditional approaches require building custom integrations for each data source, managing multiple authentication systems, and handling consent workflows manually. OpenDIF eliminates this complexity by providing a unified platform that handles all these concerns automatically.

The platform ensures **data sovereignty**—each provider maintains full control over their own data and systems, while OpenDIF handles the orchestration, security, and compliance. Every request is traced with a unique trace ID, enabling complete audit trails for compliance with regulations like GDPR. Policy checks happen at the field level, ensuring fine-grained access control that protects sensitive information.

OpenDIF is built to scale, with each service running independently and communicating through well-defined APIs. Services can be deployed together or separately, allowing organizations to start small and grow as needed. The optional audit service and observability stack can be added when required, without disrupting core functionality.

## Key Features

OpenDIF offers powerful capabilities that set it apart from traditional data exchange solutions:

| Feature | Status |
|---------|--------|
| **Unified Data Access** – Single GraphQL endpoint to access data from multiple providers | Completed |
| **Field-Level Authorization** – Attribute-based access control (ABAC) with granular permissions | Completed |
| **Consent Management** – Complete consent workflow for data owners with citizen-facing portal | Completed |
| **Distributed Tracing** – Full audit trail with trace ID correlation across all services | Completed |
| **GraphQL Federation** – Intelligent query splitting and parallel data fetching | Completed |
| **Multi-Provider Support** – Connect multiple data providers with different schemas | Completed |
| **Optional Audit Logging** – Comprehensive audit service for compliance tracking | Completed |
| **Optional Observability** – Metrics collection and visualization (Prometheus, Grafana) | Completed |
| **Identity Provider Integration** – Integration with IdP systems | In Progress |
| **Graceful Degradation** – Services function normally even if optional components are unavailable | Completed |
| **Docker Ready** – Containerized services with Docker Compose orchestration | Completed |
| **Microservices Architecture** – Independent, scalable services with well-defined APIs | Completed |

## Getting Started

### Quick Start

1. For detailed setup instructions, prerequisites, and deployment guides, please refer to [opendif-core](https://github.com/OpenDIF/opendif-core).
2. To see OpenDIF in action, check out [opendif-farajaland](https://github.com/OpenDIF/opendif-farajaland), which demonstrates a complete deployment for a fictional country. 

### Minimum Requirements

- **Go 1.21+** (for backend services)
- **Node.js 18+** (for frontend portals)
- **PostgreSQL 13+** (for data persistence)
- **Docker and Docker Compose** (for containerized deployment)

### Software Installation

Ensure you have the following installed:

- [Git](https://git-scm.com/downloads)
- [Go](https://golang.org/dl/) (version 1.21 or higher)
- [Node.js](https://nodejs.org/) (version 18 or higher)
- [Docker Engine](https://docs.docker.com/engine/install/)
- [Docker Compose](https://docs.docker.com/compose/install/)



## Architecture

### System Overview

OpenDIF follows a microservices architecture with clear separation of concerns. The platform acts as an intermediary between external entities (Consumers, Providers, Application Users) and provides secure, policy-driven data exchange with comprehensive consent management.

<img width="1251" height="1291" alt="OpenDIF_Final drawio" src="https://github.com/user-attachments/assets/ef708d5b-1a24-49bf-a56b-6ded64babb65" />


### External Entities

**External Actors:**
- **Application Users** - End users who interact with consumer applications and manage their consent through the Consent Portal
- **User IdP** - External Identity Provider that authenticates Application Users for consumer applications
- **Consumers** - Organizations running applications that request data through OpenDIF
  - **Consumer Applications** - Applications (e.g., Application 1, Application 2) that send data requests to OpenDIF
- **Providers** - Organizations offering data sources
  - **Data Sources** - External data systems (e.g., Data Source 1, Data Source 2) that provide data to OpenDIF
- **OpenDIF Admin** - Administrative users who manage the platform through the Admin Portal
- **OpenDIF Members** - Provider and Consumer organization representatives who manage their configurations through the Member Portal

### OpenDIF System Components

#### Portals (React/TypeScript)

- **Consent Portal** - Citizen-facing interface for Application Users to manage their data consent preferences
- **Admin Portal** - Administrative dashboard for OpenDIF Admins to manage platform-wide settings and configurations
- **Member Portal** - Interface for OpenDIF Members (Providers and Consumers) to manage their data sources, applications, and configurations

#### Identity Provider

- **OpenDIF IdP** - Identity Provider that authenticates and manages identities within the OpenDIF system, handling authentication for portals and internal services

#### Gateways

- **API Gateway (Ingress)** - Primary entry point for external applications and portals into the OpenDIF system
  - Routes requests from Consumer Applications, Consent Portal, Admin Portal, and Member Portal
  - Forwards requests to internal services: OpenDIF IdP, Orchestration Engine, Configuration Manager, Consent Engine, Policy Decision Point, and Audit Service
- **Egress Gateway** - Exit point for OpenDIF to communicate with external data sources
  - Receives requests from Secure Token Service
  - Routes requests to Provider Data Sources
- **API Gateway (Egress for Auxiliary Services)** - Gateway for auxiliary services to communicate externally (for monitoring and management tools)

#### Backend Services (Go)
- **Orchestration Engine** (Port 8080) - Data exchange workflow orchestration, GraphQL API, query federation and parallel data fetching. Coordinates with Secure Token
- **Secure Token Service** - Part of Orchestration Engine. Manages secure tokens for external communication with data providers and handles JWT token generation and validation for egress requests
- **Policy Decision Point** (Port 8082) - Field-level policy enforcement. Manages policy rules in the Policy Store
- **Consent Engine** (Port 8081) - Consent management and workflow coordination. Validates consent before data access and manages consent records in the Consent Store
- **Configuration Manager** (`portal-backend/`, Port 3000) - Backend API for Admin Portal and Member Portal. Handles schema submissions, application processing, and member management

#### Data Stores
- **Data Source & Applications Configuration** - Stores configuration details for data sources and applications (managed by Configuration Manager/Portal Backend)
- **Consent Store** - Stores user consent records (managed by Consent Engine)
- **Policy Store** - Stores policy rules and metadata (managed by Policy Decision Point)

#### Auxiliary Services

- **Observability Stack** (`observability/`) - Metrics collection and visualization (Prometheus, Grafana)
- **Audit Service** (`audit-service/`) - Audit logging and event tracking (services function normally without it)
  
### Data Flow

1. **Data Consumer Request**: Consumer Application sends GraphQL query to Orchestration Engine via API Gateway (Ingress)
2. **Authentication**: Request is authenticated through OpenDIF IdP
3. **Authorization Check**: Orchestration Engine queries Policy Decision Point for field-level permissions
4. **Consent Verification**: If consent is required, Orchestration Engine coordinates with Consent Engine to verify user consent
5. **Configuration Lookup**: Orchestration Engine queries Configuration Manager for data source and application configurations
6. **Token Generation**: Secure Token Service generates secure tokens for external provider communication
7. **Data Federation**: Orchestration Engine splits query, and Secure Token Service routes requests through Egress Gateway to multiple Provider Data Sources in parallel
8. **Response Aggregation**: Results are merged and returned as unified GraphQL response through API Gateway (Ingress)
9. **Audit Logging**: All service interactions are logged to Audit Service for compliance tracking

### Open Source Components

OpenDIF is built using open-source technologies:

- **Go** - Backend services and APIs
- **GraphQL** - Unified query language for data access
- **React/TypeScript** - Frontend portals
- **Open Policy Agent (OPA)** - Policy evaluation engine
- **PostgreSQL** - Data persistence
- **Docker** - Containerization and orchestration
- **Prometheus & Grafana** - Metrics and monitoring (optional)
- **OpenTelemetry** - Vendor-agnostic observability (optional)

## Contributing

Thank you for wanting to contribute to OpenDIF Core! We welcome contributions from the community. Please see our [Contributing Guidelines](https://github.com/OpenDIF/opendif-core/tree/main/docs/contributing) for details on:

- Development setup
- Code style and standards
- Pull request process
- Reporting issues

## Documentation

For detailed documentation, see the `docs/` directory. Service-specific documentation is available in each service's README:

- [Orchestration Engine](https://github.com/OpenDIF/opendif-core/tree/main/exchange/orchestration-engine/README.md)
- [Policy Decision Point](https://github.com/OpenDIF/opendif-core/tree/main/exchange/policy-decision-point/README.md)
- [Consent Engine](https://github.com/OpenDIF/opendif-core/tree/main/exchange/consent-engine/README.md)
- [Portal Backend (Configuration Manager)](https://github.com/OpenDIF/opendif-core/tree/main/portal-backend/README.md)
- [Audit Service](https://github.com/OpenDIF/opendif-core/tree/main/audit-service/README.md)
- [Observability Stack](https://github.com/OpenDIF/opendif-core/tree/main/observability/README.md)
