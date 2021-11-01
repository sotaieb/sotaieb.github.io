---
title: "Architecture-Process and Practices"
categories:
  - Architecture
tags:
  - TDD
  - DDD
  - BDD
---

Let's explore different development process and practices.

## Test Driven Development (TDD)

Test Driven Development, or TDD, is a process of developing software where a test is written prior to writing code.

- Write a test, watch it fail.
- Write just enough code to pass the test.
- Improve the code withpout changing its behaviour.

## Behavioural Driven Development (BDD)

Behavior Driven Development, or BDD, is a process of creating an executable specification that fails because the feature doesn't exist, then writing the simplest code that can make the spec pass.
TDD is a development practice while BDD is a team methodology.
In BDD the automated specifications are created by users or testers

## Hexagonal Architecture (ports and adapters) (by Steve Freeman and Nat Pryce)

There are no layers.

The hexagonal architecture divides a system into several loosely-coupled interchangeable components.

Each component is connected to the others through "ports" (Interfaces, Abstract APi).
Ex. port for UI, database, notifications, etc
Adapters are the glue between components and the outside world.
There can be several adapters for one port.
Ex. adapter for Gui, commande line, etc.
We should separate adapters from each other, and every adapter should depend just on the port it implements.

## Software Architecture Goals

- Independent of Framework
- Testable.
- Independent of UI.
- Independent of Database.
- Independent of any external agency.

## Domain Driven Design (DDD) (by Eric Evans)

Domain Driven Design, or DDD, is an approach to development that focus on the core domain (real business values), the logic behind it, and forces collaboration between technical and nontechnical parties to improve the model.

The DDD emphasizes the domain (domain driven). The Clean Architecture emphasizes the Use Cases, the services (Use-case driven).

- Domain (entities)
- Domain Services (business logic that involves multiple entities)
- Application Services (commands or queries)
- Infrastructure
- Presentation

## Clean Architecture

Clean Architecture is a layered architecture.
Clean architecture puts the business logic and application model at the center of the application.

- Entities (entities, value objects, aggregates,business rules )
- Use Cases (application specific business rules)
- Interface Adapters (controllers, gateways, etc)
- Presentation

## Onion Architecture

There are layers, with the dependencies always pointing inwards. a layer can use any of the layers inside it.

The inner layer is Domain Model and the outer is infrastructure.

- Domain Model (domain objects that have business value)
- Domain (application service, business logic and rules)
- Application Services
- Infrastructure/Presentation
