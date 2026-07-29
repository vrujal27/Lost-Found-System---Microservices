# Lost and Found Polyglot Microservices Platform

A comprehensive, scalable microservices-based application designed to manage lost and found items. The platform uses a polyglot persistence architecture, event-driven communication, and distributed transaction management (Saga pattern) to ensure data consistency and high performance.

## Architecture Overview

The system is broken down into several specialized microservices, each managing its own domain and database (Polyglot Architecture). Communication between services is primarily asynchronous using a message broker.

### Microservices

1. **Lost Service (Port: 3001)**
   - **Responsibility:** Manages all operations related to reported "Lost" items.
   - **Database:** MongoDB (NoSQL) for flexible document storage.
   - **Caching:** Redis for high-performance caching of frequently accessed data.
   - **Communication:** Publishes events to ActiveMQ when new items are reported.

2. **Found Service (Port: 3002)**
   - **Responsibility:** Manages all operations related to reported "Found" items.
   - **Database:** MySQL (Relational) for structured data storage.
   - **Communication:** Publishes events to ActiveMQ when items are found.

3. **Match Service**
   - **Responsibility:** Listens to events from both Lost and Found services. Runs fuzzy-matching algorithms to detect potential matches between lost and found items.
   - **Communication:** Consumes from and publishes match events to ActiveMQ.

4. **Notification Service**
   - **Responsibility:** Handles sending alerts and notifications to users when a potential match is found or an item status is updated.
   - **Communication:** Subscribes to notification topics on ActiveMQ.

5. **Orchestrator Service (Port: 3005)**
   - **Responsibility:** Implements the **Saga Pattern** to handle distributed transactions across multiple microservices. Specifically manages the "Claim" workflow to securely lock and update the status of items atomically across both MongoDB (Lost) and MySQL (Found) databases.

6. **Frontend (Port: 5173)**
   - **Responsibility:** Modern, responsive UI built with Vite/React (or similar modern JS framework), providing an intuitive dashboard, interactive charts, and item recording workflows.

### Infrastructure & Supporting Tools

- **Message Broker:** ActiveMQ (Port 61616, UI: 8161)
- **Databases:**
  - MongoDB (Port 27017)
  - MySQL 8.0 (Port 3306)
- **Caching:** Redis (Port 6379)
- **Database Management:** phpMyAdmin (Port 8080) pointing directly to the MySQL container.

## Getting Started

### Prerequisites

- Docker
- Docker Compose

### Running the Platform

1. **Clone the repository** (if you haven't already).
2. **Navigate** to the root directory `lost-found-project`.
3. **Start all services** using Docker Compose:

```bash
docker-compose up --build -d
```

This command will build the images for all microservices and spin up the databases and message broker.

### Accessing the Services

- **Frontend Application:** `http://localhost:5173`
- **Orchestrator API:** `http://localhost:3005`
- **Lost Service API:** `http://localhost:3001`
- **Found Service API:** `http://localhost:3002`
- **phpMyAdmin (MySQL UI):** `http://localhost:8080` (Login with `root` / `password`)
- **ActiveMQ Dashboard:** `http://localhost:8161`

## Stopping the Application

To cleanly stop all services and remove the containers, run:

```bash
docker-compose down
```

*Note: The MongoDB and MySQL data are persisted using Docker volumes (`mongodb_data` and `mysql_data`), so data will survive restarts.*
