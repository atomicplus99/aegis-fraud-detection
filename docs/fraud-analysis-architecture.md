# Fraud Analysis Service – Internal Architecture

This document defines the internal architecture of the Fraud Analysis Service,
the core decision-making component of the AEGIS fraud detection system.

The service is designed following Hexagonal Architecture (Ports & Adapters),
ensuring strict separation between business logic and technical concerns.

---

## Architectural Intent

The Fraud Analysis Service exists to evaluate financial transactions and produce
deterministic, explainable fraud decisions.

Its architecture is intentionally designed to:
- Isolate fraud rules from frameworks and infrastructure
- Allow independent evolution of business logic
- Support complex rule composition and scoring strategies
- Enable high testability without requiring external systems

---

## Architectural Style

The service follows **Hexagonal Architecture**, organized into three primary areas:

- Domain
- Application
- Infrastructure

Dependencies always point inward:
Infrastructure → Application → Domain

The domain layer has no dependencies on external frameworks or technologies.

---

## Layer Responsibilities

### Domain Layer

The Domain layer contains the pure fraud detection model and rules.

**Responsibilities**
- Define core domain concepts (Transaction, FraudDecision, RiskScore)
- Define fraud rule abstractions
- Encapsulate business invariants
- Express fraud logic independently of use cases

**Key Characteristics**
- No framework dependencies
- No persistence concerns
- No knowledge of REST, databases, or messaging

**Key Domain Concepts**
- Transaction
- FraudRule
- FraudDecision
- RiskScore
- RuleResult

---

### Application Layer

The Application layer orchestrates fraud evaluation use cases.

**Responsibilities**
- Coordinate fraud rule execution
- Aggregate rule results into a final decision
- Manage interaction with external systems via ports
- Enforce application-level workflows

**Key Characteristics**
- Depends only on domain abstractions
- Defines use cases as application services
- Does not implement fraud rules directly

**Core Use Case**
- EvaluateTransactionUseCase

---

### Infrastructure Layer

The Infrastructure layer contains technical implementations required by the
application layer.

**Responsibilities**
- Implement input adapters (REST, messaging)
- Implement output adapters (database access, external services)
- Wire dependencies together

**Key Characteristics**
- Contains all framework-specific code
- Can be replaced without affecting domain logic
- Implements ports defined in the application layer

---

## Ports and Adapters

### Input Ports

Input ports define how the application is invoked.

Example:
- EvaluateTransactionUseCase

These ports represent application-level intentions, not technical protocols.

---

### Output Ports

Output ports define required external interactions.

Examples:
- TransactionHistoryPort
- RiskScoringPort
- NotificationTriggerPort

The application layer depends on these abstractions, not their implementations.

---

## Fraud Rule Execution Model

Fraud rules are modeled as independent domain components implementing a common
FraudRule interface.

Rules:
- Are stateless
- Are composable
- Can be executed in sequence or in parallel
- Produce explicit RuleResult outputs

Rule execution follows a controlled evaluation flow managed by the application
layer.

---

## Decision Flow

1. A transaction enters the system via an input adapter
2. The application layer invokes the evaluation use case
3. Fraud rules are executed against the transaction
4. Rule results are aggregated
5. A risk score is calculated
6. A fraud decision is produced with explicit reasons

This flow is deterministic and auditable.

---

## Concurrency Considerations

The Fraud Analysis Service is designed to support concurrent transaction
evaluation.

Key considerations include:
- Stateless fraud rule execution
- External state accessed only through ports
- Idempotent evaluation where required

Concurrency control is delegated to infrastructure and persistence layers,
keeping the domain logic free from synchronization concerns.

---

## Architectural Constraints

The following constraints are enforced:

- Fraud rules must not depend on infrastructure
- No framework annotations in domain code
- No business logic in adapters
- Rule composition must be explicit and traceable

Violating these constraints compromises the integrity of the architecture.

---

## Evolution Strategy

The architecture allows for:
- Adding new fraud rules without modifying existing ones
- Changing rule evaluation strategies without rewriting domain logic
- Introducing new adapters without impacting core logic

The Fraud Analysis Service is intended to remain stable as complexity grows.

---

## Summary

This document defines the internal architecture of the Fraud Analysis Service.

All implementation decisions must conform to the layering, dependencies, and
constraints described here. Any deviation must be explicitly justified and
documented.
