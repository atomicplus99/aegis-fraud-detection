# AEGIS – Fraud Detection Engine

AEGIS is a backend fraud detection engine designed to analyze financial transactions and determine whether they present a potential fraud risk.

The system focuses exclusively on fraud analysis and decision-making, isolating business rules and risk evaluation logic from frameworks, databases, and infrastructure concerns. AEGIS is intentionally designed as a decision engine rather than a transactional system.

Its primary goal is to demonstrate robust backend system design, clear architectural boundaries, and domain-focused modeling using technologies commonly adopted in financial platforms.

---

## Problem Statement

Financial systems must evaluate suspicious transactions in near real time while maintaining strict separation between business rules and technical implementation details.

In many real-world systems, fraud detection logic is tightly coupled to persistence layers, frameworks, or delivery mechanisms, making it difficult to test, evolve, audit, or reason about the core decision-making process.

This tight coupling often results in:
- Rigid rule implementations
- Low testability of fraud logic
- Poor auditability of decisions
- High cost of change when rules evolve

AEGIS is designed to address these challenges by treating fraud detection as a first-class domain problem.

---

## Project Scope

AEGIS is **not** a payment processing system and does not execute or authorize transactions.

Its sole responsibility is to:
- Receive transaction data
- Evaluate the data against multiple fraud detection rules
- Calculate a risk score
- Produce a fraud-related decision (e.g. allow, review, block)

AEGIS acts as a decision support engine that can be integrated into larger financial systems.

---

## System Overview

AEGIS is designed as a distributed system composed of multiple microservices, each with a single, well-defined responsibility.

At a high level, the system:
1. Receives transaction data through an API boundary
2. Delegates fraud evaluation to a dedicated fraud analysis core
3. Produces a deterministic, explainable fraud decision
4. Enables downstream systems to react to that decision (e.g. persistence, notification, alerting)

---

## Architecture

AEGIS follows a **microservices architecture**, with explicit boundaries between services and no shared databases.

Core services adopt **Hexagonal Architecture (Ports and Adapters)** to ensure that:
- Business rules are independent of frameworks
- The core domain can be tested in isolation
- Infrastructure concerns are replaceable without impacting the domain model

Key architectural principles include:
- Clear separation between domain logic and infrastructure
- Explicit service responsibilities
- Frameworks treated as implementation details
- Business logic expressed through domain models and use cases

---

## Core Services

### Fraud Analysis Service

The core decision-making service responsible for:
- Evaluating fraud detection rules
- Calculating risk scores
- Producing fraud decisions
- Explaining the reasons behind each decision

This service encapsulates the fraud domain and applies Hexagonal Architecture internally.

---

### Transaction History Service

Responsible for:
- Persisting transaction data
- Providing historical access to past transactions
- Supporting fraud analysis with contextual data

This service does not contain fraud detection logic.

---

### Notification Service

Responsible for:
- Emitting alerts and notifications based on fraud decisions
- Integrating with external communication channels

This service does not evaluate fraud rules or calculate risk.

---

### API Gateway

Acts as the system entry point, responsible for:
- Request validation
- Input normalization
- Orchestration between services

The gateway contains no business logic.

---

## Communication

Services communicate synchronously via REST APIs.

Contracts between services are explicitly defined using DTO-based request and response models. These contracts are treated as stable interfaces and evolve independently from internal service implementations.

---

## Fraud Detection Rules

AEGIS supports a rule-based fraud detection model where individual rules are:
- Explicit
- Composable
- Independently testable

The initial rule set includes:
- High transaction amount threshold
- High transaction frequency within a short time window
- Impossible travel detection (“Superman rule”)

The architecture allows new rules to be introduced without modifying existing ones.

---

## Technology Stack

- Java 17
- Spring Boot (Fraud Analysis Service)
- NestJS (API Gateway)
- Docker
- Docker Compose

---

## Project Status

AEGIS is under active development as a backend fraud detection system designed to showcase architectural decision-making, domain isolation, and scalable backend design principles.

The project prioritizes clarity of responsibilities, extensibility of business rules, and long-term maintainability over short-term implementation convenience.

While not currently deployed in a production environment, the system is designed following patterns and principles commonly used in real-world financial platforms and can evolve toward production usage without structural redesign.
