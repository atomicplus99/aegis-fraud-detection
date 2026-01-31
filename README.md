# AEGIS – Fraud Detection Engine

AEGIS is a backend fraud detection engine designed to analyze financial
transactions and determine whether they are potentially fraudulent.

This project focuses on demonstrating backend architecture concepts
such as Hexagonal Architecture and Microservices using Java and NestJS.

Problem Statement

Financial systems must detect suspicious transactions in near real time
while keeping business rules isolated from frameworks, databases, and
infrastructure concerns.

In many systems, fraud detection logic is tightly coupled to technical
layers, making it difficult to test, evolve, or reason about the core
business rules.

Project Scope

AEGIS is NOT a payment system and does not execute transactions.

Its sole responsibility is to analyze transaction data and return a
fraud risk assessment based on predefined business rules.

The system receives transaction data, evaluates multiple fraud rules,
calculates a risk score, and determines whether the transaction should
be considered suspicious.

AEGIS is designed as a set of microservices, where each service has a
single, well-defined responsibility.

Architecture
- Microservices architecture
- Hexagonal Architecture (Ports & Adapters) in core services
- Clear separation between business logic and infrastructure

Communication
- Synchronous communication via REST
- DTO-based contracts between services

Frad Rules MVP

The initial version of AEGIS includes the following fraud detection rules:

- High transaction amount threshold
- High transaction frequency within a short time window
- (Optional) Impossible travel ("Superman rule")

Tech Stack

- Java 17
- Spring Boot (core fraud service)
- NestJS (API Gateway)
- Docker & Docker Compose

Project Status

This project is under active development and is intended as a
learning-focused backend system.

The main goal is to demonstrate architecture, design decisions, and
clean separation of concerns rather than production readiness.
