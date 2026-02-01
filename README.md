# Agile Application Lifecycle Management  
## Microservice-basiertes E-Commerce-System (Catalog & Order)

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

Order Service ──REST──▶ Catalog Service ──▶ PostgreSQL  
▲  
│  
Web UI

---

## Services im Überblick

### Catalog-Service

**Verantwortung**  
Zentraler Produktkatalog für Bücher und Autoren. Stellt eine REST-API bereit, die vom Order-Service konsumiert wird.

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
- Spring Boot 3.5.x
- Spring Data JPA
- PostgreSQL
- Testcontainers
- Gatling (Lasttests)
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
- Spring Boot 3.5.x
- Thymeleaf
- Spring RestClient
- Resilience4j
- Playwright (E2E)
- JaCoCo

---

## Technologie-Stack (konsolidiert)

- Sprache: Java 21
- Framework: Spring Boot 3.5.x
- Architektur: Microservices (Polyrepo)
- Datenbank: PostgreSQL
- Frontend: Thymeleaf
- Build: Maven
- Container: Docker, Docker Compose
- Testing: JUnit 5, Mockito
- Integration Tests: Testcontainers
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

- Kapischan Sriganthan – Architektur & Entwicklung  
- Mladen Radovanovic – Entwicklung & Testing  
- Micaela Wieland – Entwicklung & Dokumentation  

---

## Links

- Catalog-Service: https://github.com/ksriganthan/catalog  
- Order-Service: https://github.com/mladen98/order  
- Docker Hub:
  - ksrigganthan/catalog
  - ksrigganthan/order
