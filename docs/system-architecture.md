# AEGIS – System Architecture

This document defines the global architecture of the AEGIS fraud detection system.
It establishes service boundaries, responsibilities, and high-level data flow,
serving as the architectural foundation before any implementation begins.

---

## Architectural Goals

The architecture of AEGIS is designed to achieve the following goals:

- Strict separation of business logic from infrastructure concerns
- Clear service boundaries with single, well-defined responsibilities
- High testability of core fraud detection logic
- Extensibility of fraud rules without structural redesign
- Ability to evolve individual services independently

These goals guide all architectural decisions described in this document.

---

## System Overview

AEGIS is a distributed backend system focused exclusively on fraud risk evaluation.
It is designed as a set of independent microservices that collaborate to analyze
financial transactions and produce fraud-related decisions.

The system does not execute transactions. It evaluates transaction data and
returns a deterministic, explainable fraud assessment.

---

## Core Services

### 1. Fraud Analysis Service

**Responsibility**

The Fraud Analysis Service is the core of the system. It is responsible for:

- Evaluating incoming transactions against fraud detection rules
- Calculating a fraud risk score
- Producing a fraud decision (ALLOW, REVIEW, BLOCK)
- Providing explainable reasons for each decision

**Key Characteristics**

- Implements Hexagonal Architecture (Ports & Adapters)
- Contains all fraud-related business rules
- Is independent of delivery mechanisms (REST, messaging)
- Is independent of persistence and external services

**Out of Scope**

- Persisting full transaction history
- Sending notifications
- Handling client-facing HTTP concerns

---

### 2. Transaction History Service

**Responsibility**

The Transaction History Service is responsible for:

- Persisting transaction data
- Providing historical transaction context
- Supporting fraud analysis with past transaction information

**Key Characteristics**

- Owns transaction persistence
- Acts as a source of historical truth
- Exposes read-only access to transaction history for other services

**Out of Scope**

- Fraud rule evaluation
- Risk scoring
- Notification logic

---

### 3. Notification Service

**Responsibility**

The Notification Service is responsible for:

- Emitting notifications based on fraud decisions
- Integrating with external communication channels (email, SMS, webhooks)
- Managing notification retries and delivery concerns

**Key Characteristics**

- Stateless regarding fraud logic
- Reacts only to finalized fraud decisions
- Can evolve independently from fraud analysis logic

**Out of Scope**

- Fraud evaluation
- Transaction persistence
- Risk calculation

---

### 4. API Gateway

**Responsibility**

The API Gateway serves as the system entry point and is responsible for:

- Receiving external requests
- Validating and normalizing input data
- Routing requests to internal services
- Orchestrating synchronous service calls

**Key Characteristics**

- Contains no business logic
- Acts purely as an edge and orchestration layer
- Shields internal services from external consumers

**Out of Scope**

- Fraud decision-making
- Business rule implementation
- Data persistence

---

## Service Boundaries and Data Ownership

Each service owns its data and business logic:

- Fraud Analysis Service owns fraud rules and decision logic
- Transaction History Service owns transaction persistence
- Notification Service owns notification delivery state

No databases are shared between services.

Services communicate only through well-defined interfaces.

---

## Communication Model

AEGIS uses synchronous REST-based communication between services.

- Requests and responses are defined via explicit DTO contracts
- Services do not expose internal domain models
- Communication is designed to be deterministic and traceable

Asynchronous communication may be introduced in future iterations but is not part
of the current architectural scope.

---

## High-Level Data Flow

1. A transaction is received via the API Gateway
2. Transaction data is forwarded to the Fraud Analysis Service
3. Fraud Analysis Service requests historical data from Transaction History Service
4. Fraud rules are evaluated and a decision is produced
5. The decision is returned to the caller
6. Notification Service may be triggered based on the decision

---

## Architectural Constraints

The following constraints are intentionally enforced:

- No shared databases
- No business logic in the API Gateway
- No fraud rules outside the Fraud Analysis Service
- No direct coupling between infrastructure and domain logic

These constraints are non-negotiable and ensure long-term maintainability.

---

## Evolution Considerations

The architecture allows for:

- Adding new fraud rules without modifying existing ones
- Introducing new services without impacting the fraud core
- Changing communication mechanisms without rewriting business logic

The Fraud Analysis Service is designed to remain stable as the system evolves.

---

## Summary

This document defines the architectural foundation of AEGIS.

All implementation decisions must align with the service boundaries,
responsibilities, and constraints defined here. Deviations from this architecture
should be explicitly documented and justified.
