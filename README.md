# Agile Application Lifecycle Management

Ein modulares Microservice-System bestehend aus zwei zusammenhängenden Services: **Catalog** und **Order**.

---

## Catalog Service

Eine Spring Boot-basierte REST-API zur Verwaltung eines Buchkatalogs. Der Service bildet das Kernstück der Produktverwaltung und stellt Buchinformationen für andere Services bereit.

### Überblick

Der Catalog-Service ist eine Java-basierte Webanwendung, die eine vollständige CRUD-Funktionalität für Bücher und Autoren bereitstellt. Als Teil einer Microservice-Architektur arbeitet er eng mit dem **Order-Service** zusammen, der auf die Catalog-API zugreift, um Warenkorb- und Bestellfunktionalitäten zu realisieren.

### Verwandte Projekte

- **Order-Service**: Verwaltet Warenkörbe und Bestellungen, greift auf die Catalog-API zu

---

## Technologie-Stack

| Kategorie | Technologie | Version |
|-----------|-------------|---------|
| Runtime | Java | 21 |
| Framework | Spring Boot | 3.5.6 |
| Persistierung | Spring Data JPA | - |
| Datenbank | PostgreSQL | Latest |
| Template Engine | Thymeleaf | - |
| Build-Tool | Maven | 3.9+ |
| Containerisierung | Docker | - |
| Testing | JUnit 5 & Mockito | - |
| Integration Tests | Testcontainers | 1.19.7 |
| Performance Tests | Gatling | - |
| Code Coverage | JaCoCo | - |

---

## Funktionsumfang

### Kernfunktionen

- **REST API** zum Durchsuchen und Verwalten von Büchern
- **Autoren- und Buchverwaltung** mit bidirektionaler Many-to-Many-Beziehung
- **Automatische Dateninitialiserung** beim ersten Start mit Beispieldaten
- **Erweiterte Suchfunktion**:
  - Mehrere Keywords mit UND-Verknüpfung
  - Case-insensitive Volltextsuche
  - Suche über ISBN, Titel, Beschreibung und Autorennamen
- **Docker-Support** für einfaches Deployment
- **Umfassende Test-Suite**:
  - Unit-Tests
  - Integrationstests mit Testcontainers
  - Lasttests mit Gatling
- **Integration mit Order-Service** für erweiterte E-Commerce-Funktionen

---

## Voraussetzungen

Stellen Sie sicher, dass folgende Software installiert ist:

- **Java Development Kit (JDK)**: Version 21 oder höher
- **Apache Maven**: Version 3.9 oder höher
- **PostgreSQL**: Für den Produktivbetrieb
- **Docker** (optional): Für containerisierte Ausführung

---

## Installation und Start

### Lokale Entwicklung mit Maven

```bash
# Repository klonen
git clone https://github.com/ksriganthan/catalog.git
cd catalog

# Anwendung bauen
./mvnw clean package

# Anwendung starten
./mvnw spring-boot:run
```

Die Anwendung ist nach dem Start unter `http://localhost:8080` erreichbar.

### Docker-Deployment

```bash
# Docker Image erstellen
docker build -t catalog:latest .

# Container starten
docker run -p 8080:8080 catalog:latest
```

### Docker Compose (mit PostgreSQL)

```bash
# Services starten
docker-compose up -d

# Services stoppen
docker-compose down
```

---

## API-Dokumentation

### Endpunkte

#### Bücher durchsuchen

**Endpoint**: `GET /books/search`

**Beschreibung**: Sucht nach Büchern anhand von Keywords. Die Suche erfolgt case-insensitive über ISBN, Titel, Beschreibung und Autorennamen. Mehrere Keywords werden mit UND verknüpft.

**Query-Parameter**:
- `keyword` (optional, mehrfach): Suchbegriff(e)

**Beispiel-Request**:

```bash
# Suche nach einem Autor
curl "http://localhost:8080/books/search?keyword=Fitzek"

# Suche mit mehreren Keywords (UND-Verknüpfung)
curl "http://localhost:8080/books/search?keyword=Fitzek&keyword=Mimik"

# Alle Bücher abrufen
curl "http://localhost:8080/books/search"
```

**Beispiel-Response**:

```json
[
  {
    "isbn": "9783426519486",
    "title": "Mimik",
    "description": "Frau leidet an Gedächtnisverlust und versucht den Mord an Paul zu verhindern",
    "authors": [
      {
        "authorId": 3,
        "name": "Sebastian",
        "surname": "Fitzek"
      }
    ]
  }
]
```

**Response-Codes**:
- `200 OK`: Erfolgreiche Anfrage
- `400 Bad Request`: Ungültige Parameter
- `500 Internal Server Error`: Serverfehler

---

## Projektstruktur

```
catalog/
├── src/
│   ├── main/
│   │   ├── java/org/example/catalog/
│   │   │   ├── CatalogApplication.java          # Spring Boot Hauptklasse
│   │   │   ├── config/
│   │   │   │   └── DataInitializer.java         # Beispieldaten-Initialisierung
│   │   │   ├── controller/
│   │   │   │   └── BookRestController.java      # REST API Controller
│   │   │   ├── data/
│   │   │   │   ├── Book.java                    # Buch-Entität
│   │   │   │   └── Author.java                  # Autoren-Entität
│   │   │   ├── repository/
│   │   │   │   ├── BookRepository.java          # Buch-Repository
│   │   │   │   └── AuthorRepository.java        # Autoren-Repository
│   │   │   └── service/
│   │   │       └── BookService.java             # Business Logic
│   │   └── resources/
│   │       ├── application.properties           # Konfiguration
│   │       └── templates/                       # Thymeleaf Templates
│   └── test/
│       └── java/org/example/catalog/
│           ├── controller/                       # Controller Tests
│           ├── service/                          # Service Tests
│           └── repository/                       # Repository Tests
├── pom.xml                                       # Maven Konfiguration
├── Dockerfile                                    # Docker Image Definition
└── README.md                                     # Projektdokumentation
```

---

## Datenmodell

### Entity-Relationship-Diagram

```
Book (N) ←→ (M) Author
```

### Book-Entität

| Feld | Typ | Beschreibung | Constraints |
|------|-----|--------------|-------------|
| isbn | String | Internationale Standardbuchnummer | Primary Key, NOT NULL |
| title | String | Buchtitel | NOT NULL |
| description | String | Beschreibung des Buchinhalts | - |
| authors | Set<Author> | Autoren des Buches | Many-to-Many |

### Author-Entität

| Feld | Typ | Beschreibung | Constraints |
|------|-----|--------------|-------------|
| authorId | Long | Eindeutige ID | Primary Key, Auto-generated |
| name | String | Vorname des Autors | NOT NULL |
| surname | String | Nachname des Autors | NOT NULL |
| books | Set<Book> | Bücher des Autors | Many-to-Many |

---

## Architektur

### Microservice-Architektur (Polyrepo-Ansatz)

Das Projekt folgt einer **Polyrepo-Architektur** mit klarer Trennung der Verantwortlichkeiten:

#### Catalog-Service (dieses Repository)
- **Verantwortlichkeit**: Verwaltung des Buchkatalogs
- **Funktionen**:
  - CRUD-Operationen für Bücher und Autoren
  - Suchfunktionalität
  - Datenpersistierung

#### Order-Service (separates Repository)
- **Verantwortlichkeit**: Bestellabwicklung
- **Funktionen**:
  - Warenkorbverwaltung
  - Bestellungserstellung und -verarbeitung
  - Integration mit Catalog-Service über REST API

### Vorteile dieser Architektur

- **Unabhängige Skalierung**: Services können separat skaliert werden
- **Klare Verantwortlichkeiten**: Jeder Service hat einen definierten Aufgabenbereich
- **Technologische Flexibilität**: Services können unterschiedliche Technologien nutzen
- **Isolierte Deployments**: Services können unabhängig voneinander deployed werden

---

## Testing

### Tests ausführen

```bash
# Alle Tests ausführen
./mvnw test

# Mit Code Coverage Report
./mvnw test jacoco:report

# Nur Unit-Tests
./mvnw test -Dtest=*Test

# Nur Integrationstests
./mvnw test -Dtest=*IT

# Lasttests mit Gatling
./mvnw gatling:test
```

### Test-Coverage-Report

Nach Ausführung der Tests mit JaCoCo finden Sie den Coverage-Report unter:

```
target/site/jacoco/index.html
```

### Test-Strategie

| Test-Typ | Framework | Zweck |
|----------|-----------|-------|
| Unit-Tests | JUnit 5 & Mockito | Isolierte Komponententests |
| Integrationstests | Testcontainers | End-to-End-Tests mit echter Datenbank |
| Lasttests | Gatling | Performance- und Belastungstests |

---

## Konfiguration

### Datenbankverbindung

Die Standardkonfiguration verwendet PostgreSQL. Konfigurationsparameter in `src/main/resources/application.properties`:

```properties
spring.datasource.url=jdbc:postgresql://localhost:5432/catalog
spring.datasource.username=your_username
spring.datasource.password=your_password
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

### Testumgebung

Für Tests wird automatisch Testcontainers mit PostgreSQL verwendet. Keine manuelle Konfiguration erforderlich.

### Umgebungsvariablen

| Variable | Beschreibung | Standard |
|----------|--------------|----------|
| `DB_HOST` | PostgreSQL Host | localhost |
| `DB_PORT` | PostgreSQL Port | 5432 |
| `DB_NAME` | Datenbankname | catalog |
| `DB_USER` | Datenbankbenutzer | postgres |
| `DB_PASSWORD` | Datenbankpasswort | - |

---

## Monitoring und Observability

### Spring Boot Actuator

Die Anwendung nutzt Spring Boot Actuator für Monitoring-Endpoints:

- `/actuator/health` - Gesundheitsstatus der Anwendung
- `/actuator/info` - Informationen über die Anwendung
- `/actuator/metrics` - Metriken und Performance-Daten

### Aktivierung

Fügen Sie in `application.properties` hinzu:

```properties
management.endpoints.web.exposure.include=health,info,metrics
management.endpoint.health.show-details=always
```

---

## Deployment

### Production-Build

```bash
# Optimiertes Production-Build erstellen
./mvnw clean package -DskipTests

# JAR-Datei ausführen
java -jar target/catalog-0.0.1-SNAPSHOT.jar
```

### Docker Production-Image

```bash
# Multi-stage Build für optimiertes Image
docker build -t catalog:1.0.0 .

# Mit spezifischen Environment Variables
docker run -p 8080:8080 \
  -e DB_HOST=postgres \
  -e DB_PORT=5432 \
  -e DB_NAME=catalog \
  -e DB_USER=admin \
  -e DB_PASSWORD=secret \
  catalog:1.0.0
```

---

## Beitragende

- **Kapischan Sriganthan** - Entwicklung & Architektur
- **Mladen Radovanovic** - Entwicklung & Testing
- **Micaela Wieland** - Entwicklung & Dokumentation

---

## Lizenz

Dieses Projekt hat keine spezifische Lizenz und wird zu Bildungszwecken bereitgestellt.



# Order Service

Eine Spring Boot-basierte Webanwendung zur Verwaltung von Warenkörben und Bestellungen. Der Service bildet die Benutzeroberfläche des E-Commerce-Systems und integriert sich mit dem **Catalog-Service** zur Produktverwaltung.

---

## Überblick

Der Order-Service ist eine Java-basierte Webanwendung mit Thymeleaf-Frontend, die Warenkorbfunktionalitäten für einen Online-Buchhandel bereitstellt. Als Teil einer Microservice-Architektur arbeitet er eng mit dem **Catalog-Service** zusammen und nutzt dessen REST-API für die Produktsuche und -anzeige.

### Verwandte Projekte

- **Catalog-Service**: Stellt die REST-API für Bücher und Autoren bereit
- **Repository**: https://github.com/ksriganthan/catalog

---

## Technologie-Stack

| Kategorie | Technologie | Version |
|-----------|-------------|---------|
| Runtime | Java | 21 |
| Framework | Spring Boot | 3.5.7 |
| Template Engine | Thymeleaf | - |
| REST Client | Spring RestClient | - |
| Resilience | Resilience4j | 2.2.0 |
| Build-Tool | Maven | 3.9+ |
| Containerisierung | Docker | - |
| Testing | JUnit 5 | - |
| E2E Testing | Playwright | 1.46.0 |
| Code Coverage | JaCoCo | 0.8.14 |
| Monitoring | Spring Boot Actuator | - |

---

## Funktionsumfang

### Kernfunktionen

- **Web-UI** für Buchsuche und Warenkorb
- **Warenkorbverwaltung** mit Session-basierter Persistierung
- **Integration mit Catalog-Service** über REST-API
- **Resilience-Pattern**:
  - Automatische Retry-Mechanismen bei Catalog-Ausfällen
  - Fallback-Strategien bei Nichtverfügbarkeit
  - Circuit-Breaker-Pattern
- **Erweiterte Suchfunktion**:
  - Delegiert an Catalog-Service
  - Echtzeitsuche über Thymeleaf-UI
- **Session-Management**:
  - Cookie-basiertes Session-Tracking
  - Benutzerspezifische Warenkörbe
- **Docker-Support** für einfaches Deployment
- **Umfassende Test-Suite**:
  - Unit-Tests
  - End-to-End-Tests mit Playwright

---

## Voraussetzungen

Stellen Sie sicher, dass folgende Software installiert ist:

- **Java Development Kit (JDK)**: Version 21 oder höher
- **Apache Maven**: Version 3.9 oder höher
- **Catalog-Service**: Muss laufen (siehe Konfiguration)
- **Docker** (optional): Für containerisierte Ausführung

---

## Installation und Start

### Lokale Entwicklung mit Maven

```bash
# Repository klonen
git clone https://github.com/mladen98/order.git
cd order

# Sicherstellen, dass der Catalog-Service läuft
# (siehe Catalog-Service Dokumentation)

# Anwendung bauen
./mvnw clean package

# Anwendung starten
./mvnw spring-boot:run

# Docker Image erstellen
docker build -t order:latest .

# Container starten
docker run -p 8081:8081 \
  -e CATALOG_BASE_URL=http://catalog:8080 \
  order:latest


