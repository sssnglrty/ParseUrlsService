# ParseUrlService

A distributed URL scraping service that uses browser automation to extract URLs from web pages. The system consists of a Server component that controls Chrome browser and a Client component that stores scraped URLs in PostgreSQL database.

## Architecture

```
┌─────────────────┐         ┌─────────────────┐
│    Client       │◄───────►│     Server      │
│  (Port 8080)    │ WebSocket│ (Port 8081)    │
│                 │          │                │
│ • Connects to   │          │ • Opens Chrome │
│   server        │          │ • Scrapes URLs │
│ • Stores URLs   │          │ • Controls     │
│   in PostgreSQL │          │   mouse/scroll │
└─────────────────┘          └─────────────────┘
```

## Features

- **Browser Automation**: Uses UI automation (mmarquee) to interact with Chrome browser
- **URL Extraction**: Automatically finds and extracts URLs from web pages
- **Mouse Control**: Supports mouse movement and scrolling to view more content
- **Display Resizing**: Can resize display to find additional URLs
- **WebSocket Communication**: Real-time communication between client and server
- **Database Storage**: PostgreSQL database for persistent URL storage

## Prerequisites

- Java 17+
- Maven
- Chrome browser installed
- PostgreSQL database
- Windows operating system (required for UI automation)

## Setup

### 1. Clone the repository

```bash
git clone https://github.com/orlovzzz/ParseUrlService
cd ParseUrlService
```

### 2. Start PostgreSQL database

```bash
docker-compose up -d
```

### 3. Configure Chrome path

Edit `Server/src/main/resources/application.yaml`:

```yaml
chrome:
  path: "C:/Program Files/Google/Chrome/Application/chrome.exe"
```

Update the path to match your Chrome installation location.

### 4. Build and run

Build both modules:

```bash
# From project root
cd Client
mvn clean install
cd ../Server
mvn clean install
```

Run Server first (it opens Chrome):

```bash
cd Server
mvn spring-boot:run
```

Then run Client:

```bash
cd Client
mvn spring-boot:run
```

## API Documentation

- **Server API**: http://localhost:8081/swagger-ui/index.html
- **Client API**: http://localhost:8080/swagger-ui/index.html

## Client API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST   | `/config` | Set connection properties (IP, port, frequency) |
| GET    | `/start`  | Connect to server |
| GET    | `/stop`   | Disconnect from server |
| GET    | `/log`    | Save scraped URLs to text file |

## Server Configuration

Edit `Server/src/main/resources/application.yaml`:

```yaml
chrome:
  path: "C:/Program Files/Google/Chrome/Application/chrome.exe"

server:
  port: 8081
```

## Client Configuration

Edit `Client/src/main/resources/application.yaml`:

```yaml
spring:
  datasource:
    url: jdbc:postgresql://localhost:5433/urls_db
    username: root
    password: root
  jpa:
    hibernate:
      ddl-auto: update
```

## Technology Stack

- **Backend**: Spring Boot 3.3.1
- **Database**: PostgreSQL with JPA/Hibernate
- **Communication**: Java WebSocket
- **Browser Automation**: mmarquee UI Automation
- **Build Tool**: Maven
- **Optional**: Lombok, MapStruct

## Project Structure

```
ParseUrlService/
├── Client/          # URL storage service
│   ├── pom.xml
│   └── src/main/java/org/example/
│       ├── controller/    # REST API endpoints
│       ├── dto/           # Data transfer objects
│       ├── entity/        # JPA entities
│       ├── interfaces/    # Service interfaces
│       ├── repository/    # Data repositories
│       └── service/       # Service implementations
├── Server/           # Browser automation service
│   ├── pom.xml
│   └── src/main/java/org/example/
│       ├── controller/    # WebSocket server
│       ├── dto/           # Request/response DTOs
│       ├── interfaces/    # Windows API interfaces
│       └── service/       # Automation logic
├── docker-compose.yaml   # PostgreSQL container
└── README.md
```