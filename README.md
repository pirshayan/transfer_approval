# Project Overview

This repository demonstrates my approach to programming and designing complex software systems through a practical example. The scenario involves managing **payment remittances** that are pre-registered in the system as payable and need to be sent to a bank. However, before payment, each remittance must be approved by **two authorized users**. Once approved, their status changes to **"ready to be sent to the bank"**. The system provides services to approve remittances in two ways: **independent remittances** and **remittances belonging to a specific group**. It is assumed that the user is already **authenticated**.

The goal of this project is to decouple **business logic** from **implementation details** by applying **best practices** and principles such as **Domain-Driven Design (DDD)**, **Hexagonal Architecture**, **Clean Architecture**, and other industry standards. The implementation uses **Quarkus** as the framework and **Oracle Express** as the database.

## Architecture and Design Principles

### Core Assumptions
- The project assumes that a predefined schema exists in the **persistence layer**, and the desired logic must be implemented based on this schema.
- An **abstraction layer** is created between the **domain layer** and the **infrastructure layer** to reconcile inherited structural differences with the desired structure using **mappers**. This ensures that **domain classes** have no dependencies on (or awareness of) the underlying layers.
- The **domain layer** is designed to remain unchanged even if a new framework is adopted.

### Domain Layer
- The **domain layer** consists solely of **domain classes**, designed with a **DDD mindset**, embedding **invariants** within their respective **aggregates** (e.g., remittance entities).
- Business logic is encapsulated within aggregates unless it spans multiple aggregates or requires data retrieval from the **persistence layer** (Oracle Express). In such cases, it is placed in **domain services**.
- Example: The approval process for remittances and their status updates are handled within the domain layer.

### Presentation Layer
- The **presentation layer** exposes services externally, supporting protocols like **gRPC** and **REST** (implemented via Quarkus). It provides flexibility to approve both independent remittances and grouped remittances without altering other layers.
- Services can be deployed behind an **API Gateway** as a **microservice**, with seamless integration using **Kafka** or **gRPC**.

### Application Layer
- **Use cases** reside in the **application layer**, responsible for executing specific scenarios (e.g., approving a remittance) using domain layer classes.
- The **Command Pattern** delivers use cases to the **controller layer**, while **presenters** provide the results of each use case execution, ensuring the **dependency rule** is correctly enforced.

### Aggregate Root Design
- **Aggregate root classes** in the domain layer use the **Builder Pattern**, offering:
  - **Thread-safe instantiation**.
  - Benefits of a **Functional Architecture**, improving readability and simplifying test class development.

## Testing Strategy

### Unit Tests
- Tests follow the **Classic School** approach, focusing on **unit behavior**.
- Each unit test covers all scenarios (success and failure) and is located in the **domain/core package**.
- **Domain service tests** avoid **repository mocking**, as no database access or view is exposed externally. Access to **Oracle Express** data is restricted to the provided services.
- For more on this approach, see *Unit Testing Principles, Patterns, and Practices* by Vladimir Khorikov.

### Test Coverage
- Tests are provided for the **application layer** and **controller layer**.
- In the **core/domain package**, unit tests are:
  - Independent of technical details.
  - Dependent only on the behavior being tested, making them **resistant to refactoring**.

### Test Limitations
- In the **Arrangement phase** of some tests, raw **SQL statements** are used, creating a dependency on the **Oracle Express** schema. This was a deliberate choice to keep the sample concise, avoiding additional classes/methods for Arrangement setup.

## Key Features
- **Framework Independence**: The domain layer has minimal dependency on Quarkus (only via Transactional Annotation).
- **Protocol Flexibility**: Supports gRPC and REST via Quarkus, extensible without changing other layers.
- **Microservice Compatibility**: Easily integrates with Kafka or gRPC behind an API Gateway.
- **Testability**: Architecture enables straightforward, maintainable tests.

## Dependencies

- **Quarkus**: Kubernetes-native Java framework.
  - **Quarkus gRPC**: For gRPC services.
  - **Quarkus RESTEasy**: For RESTful services.
  - **Quarkus JDBC**: For Oracle Express connectivity.
  - **Quarkus Hibernate ORM**: For ORM with Oracle Express.
- **Oracle Express**: Free Oracle Database edition for persistence.
- **Maven**: Build and dependency management (see `pom.xml`).
