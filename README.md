# Microservice-basiertes E-Commerce-System (Catalog & Order)
## Modul: Agile Application Lifecycle Management (Deutsch)

Dieses Projekt implementiert ein **modulares Microservice-System** bestehend aus zwei klar getrennten Services:

- **Catalog-Service** – Produkt- und Suchverwaltung (REST API)
- **Order-Service** – Web-Frontend, Warenkorb und Bestelllogik

Das System wurde im Rahmen des Moduls **Agile Application Lifecycle Management** entwickelt und folgt bewährten Prinzipien moderner Softwarearchitektur: klare Verantwortlichkeiten, lose Kopplung, Testbarkeit und Containerisierung.

---

## Gesamtarchitektur

- **Architekturstil**: Microservices (Polyrepo)
- **Kommunikation**: REST (HTTP, JSON)
- **Deployment**: Docker & Docker Compose
- **Datenhaltung**: PostgreSQL (Catalog-Service)
- **Frontend**: Thymeleaf (Order-Service)

Order Service ──▶ REST──▶ Catalog Service ──▶ PostgreSQL (H2) ──▶ Web UI

---

## Services im Überblick

### Catalog-Service

**Verantwortung**  
Zentraler Produktkatalog für Bücher. Stellt eine REST-API bereit, die vom Order-Service konsumiert wird.

**Kernfunktionen**
- CRUD für Bücher und Autoren
- Many-to-Many-Beziehung (Book ↔ Author)
- Erweiterte Volltextsuche (ISBN, Titel, Beschreibung, Autor)
- Case-insensitive Suche mit UND-Verknüpfung
- Automatische Initialisierung von Beispieldaten
- Docker-fähig
- Umfassende Testabdeckung

**Technologie**
- Java 21
- Spring Boot 3.5.6
- Spring Data JPA
- PostgreSQL
- Testcontainers
- Gatling (Lasttests)
- PlayWright (E2E)
- JaCoCo (Coverage)

---

### Order-Service

**Verantwortung**  
Benutzeroberfläche und Warenkorblogik. Integriert sich über REST mit dem Catalog-Service.

**Kernfunktionen**
- Web-UI für Produktsuche
- Session-basierter Warenkorb
- Resiliente Kommunikation mit Catalog-Service
- Retry- & Fallback-Mechanismen
- End-to-End-Tests mit Playwright
- Docker-fähig

**Technologie**
- Java 21
- Spring Boot 3.5.6
- Thymeleaf
- Spring RestClient
- Resilience4j
- Playwright (E2E)
- JaCoCo

---

## Technologie-Stack (konsolidiert)

- Sprache: Java 21
- Framework: Spring Boot 3.5.6
- Architektur: Microservices (Polyrepo)
- Datenbank: PostgreSQL (H2)
- Frontend: Thymeleaf
- Build: Maven
- Container: Docker, Docker Compose
- Testing: JUnit 5, Mockito
- Integration Tests: Testcontainers (PostgreSQL)
- E2E Tests: Playwright
- Performance: Gatling
- Monitoring: Spring Boot Actuator

---

## Repository-Struktur

Catalog-Service:
catalog/
- controller – REST Controller
- service – Business Logic
- repository – JPA Repositories
- data – Entities (Book, Author)
- config – Initialisierung & Konfiguration
- Dockerfile

Order-Service:
order/
- controller – Web Controller
- service – Warenkorb & Integration
- model – Cart, Book, Author
- config – REST Client & Session
- templates – Thymeleaf UI
- Dockerfile

---

## Service-Kommunikation

Beispiel: Buchsuche  
GET /books/search?keyword=Fitzek

- Aufgerufen vom Order-Service
- Delegiert an Catalog-Service
- JSON-Antwort wird im UI dargestellt
- Mehrere Keywords werden logisch UND-verknüpft

---

## Resilience & Fehlerbehandlung

Der Order-Service ist fehlertolerant gegenüber Ausfällen des Catalog-Services:

- Retry (3 Versuche, 500 ms)
- Fallback bei Nichterreichbarkeit
- UI bleibt funktionsfähig
- Warenkorb bleibt erhalten

---

## Testing-Strategie

- Unit-Tests: isolierte Logiktests
- Integration-Tests: Service + DB (Testcontainers)
- End-to-End-Tests: Browser-basierte Tests (Playwright)
- Performance-Tests: Last- & Stabilitätstests (Gatling)

Coverage-Reports werden mit JaCoCo erzeugt.

---

## Monitoring & Observability

Beide Services nutzen Spring Boot Actuator:

- /actuator/health
- /actuator/metrics
- /actuator/info

Zusätzlich:
- Retry-Metriken (Resilience4j)
- Getrennte Service-Health-Checks

---

## Deployment

Lokale Entwicklung:
./mvnw spring-boot:run

Docker (Einzelservice):
docker build -t catalog .
docker run -p 8080:8080 catalog

Gesamtsystem:
docker-compose up -d

Zugriff:
- Catalog-Service: http://localhost:8080
- Order-Service: http://localhost:8081

---

## Mitwirkende

- Kapischan Sriganthan
- Mladen Radovanovic
- Micaela Wieland

---

## Links

- Catalog-Service: https://github.com/ksriganthan/catalog  
- Order-Service: https://github.com/mladen98/order  
- Docker Hub: https://hub.docker.com/repositories/ksrigganthan
  - ksrigganthan/catalog
  - ksrigganthan/order
  - 


# Microservice-based E-Commerce System (Catalog & Order)
## Module: Agile Application Lifecycle Management (English)

This project implements a **modular microservice system** consisting of two clearly separated services:

- **Catalog-Service** – Product and search management (REST API)
- **Order-Service** – Web frontend, shopping cart and order logic

The system was developed as part of the **Agile Application Lifecycle Management** module and follows proven principles of modern software architecture: clear responsibilities, loose coupling, testability, and containerization.

---

## Overall Architecture

- **Architecture Style**: Microservices (Polyrepo)
- **Communication**: REST (HTTP, JSON)
- **Deployment**: Docker & Docker Compose
- **Data Storage**: PostgreSQL (Catalog-Service)
- **Frontend**: Thymeleaf (Order-Service)

Order Service ──▶ REST──▶ Catalog Service ──▶ PostgreSQL (H2) ──▶ Web UI

---

## Services Overview

### Catalog-Service

**Responsibility**  
Central product catalog for books. Provides a REST API consumed by the Order-Service.

**Core Functions**
- CRUD for books and authors
- Many-to-Many relationship (Book ↔ Author)
- Advanced full-text search (ISBN, title, description, author)
- Case-insensitive search with AND logic
- Automatic initialization of sample data
- Docker-ready
- Comprehensive test coverage

**Technology**
- Java 21
- Spring Boot 3.5.6
- Spring Data JPA
- PostgreSQL
- Testcontainers
- Gatling (Load tests)
- Playwright (E2E)
- JaCoCo (Coverage)

---

### Order-Service

**Responsibility**  
User interface and shopping cart logic. Integrates with the Catalog-Service via REST.

**Core Functions**
- Web UI for product search
- Session-based shopping cart
- Resilient communication with Catalog-Service
- Retry & fallback mechanisms
- End-to-end tests with Playwright
- Docker-ready

**Technology**
- Java 21
- Spring Boot 3.5.6
- Thymeleaf
- Spring RestClient
- Resilience4j
- Playwright (E2E)
- JaCoCo

---

## Technology Stack (consolidated)

- Language: Java 21
- Framework: Spring Boot 3.5.6
- Architecture: Microservices (Polyrepo)
- Database: PostgreSQL (H2)
- Frontend: Thymeleaf
- Build: Maven
- Container: Docker, Docker Compose
- Testing: JUnit 5, Mockito
- Integration Tests: Testcontainers (PostgreSQL)
- E2E Tests: Playwright
- Performance: Gatling
- Monitoring: Spring Boot Actuator

---

## Repository Structure

Catalog-Service:
catalog/
- controller – REST Controller
- service – Business Logic
- repository – JPA Repositories
- data – Entities (Book, Author)
- config – Initialization & Configuration
- Dockerfile

Order-Service:
order/
- controller – Web Controller
- service – Shopping Cart & Integration
- model – Cart, Book, Author
- config – REST Client & Session
- templates – Thymeleaf UI
- Dockerfile

---

## Service Communication

Example: Book Search  
GET /books/search?keyword=Fitzek

- Called by Order-Service
- Delegated to Catalog-Service
- JSON response is displayed in the UI
- Multiple keywords are logically AND-linked

---

## Resilience & Error Handling

The Order-Service is fault-tolerant towards Catalog-Service failures:

- Retry (3 attempts, 500 ms)
- Fallback when unreachable
- UI remains functional
- Shopping cart is preserved

---

## Testing Strategy

- Unit Tests: isolated logic tests
- Integration Tests: Service + DB (Testcontainers)
- End-to-End Tests: Browser-based tests (Playwright)
- Performance Tests: Load & stability tests (Gatling)

Coverage reports are generated with JaCoCo.

---

## Monitoring & Observability

Both services use Spring Boot Actuator:

- /actuator/health
- /actuator/metrics
- /actuator/info

Additionally:
- Retry metrics (Resilience4j)
- Separate service health checks

---

## Deployment

Local Development:
./mvnw spring-boot:run

Docker (Single Service):
docker build -t catalog .
docker run -p 8080:8080 catalog

Complete System:
docker-compose up -d

Access:
- Catalog-Service: http://localhost:8080
- Order-Service: http://localhost:8081

---

## Contributors

- Kapischan Sriganthan
- Mladen Radovanovic
- Micaela Wieland

---

## Links

- Catalog-Service: https://github.com/ksriganthan/catalog  
- Order-Service: https://github.com/mladen98/order  
- Docker Hub: https://hub.docker.com/repositories/ksrigganthan
  - ksrigganthan/catalog
  - ksrigganthan/order
