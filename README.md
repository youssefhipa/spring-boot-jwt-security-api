# Task 3 - Securing a REST API with JWT & Spring Security

This repository contains Task 3 implementation for **Architecture of Massively Scalable Applications (Spring 2026)**.

## Student Info
- Name: `Youssef Hipa`
- ID: `16001371`

## What is implemented
- JWT configuration and token service
- Registration and login endpoints
- BCrypt password hashing
- Spring Security stateless filter chain
- JWT auth filter
- Custom `UserDetailsService`
- Role-based access control on product endpoints

## Prerequisites
- JDK `25`
- Maven `3.9+`
- Docker Desktop (running)

## Project identity configuration
Already configured in this repo:
- `src/main/resources/application.yaml`
  - `USER_NAME: Youssef_Hipa_Local`
  - `ID: 16001371`
- `Dockerfile`
  - `ENV USER_NAME=Docker_Youssef_Hipa`
  - `ENV ID=Docker_16001371`
- `docker-compose.yaml`
  - app service enabled on host port `1371`
  - `USER_NAME: Compose_Youssef_Hipa`
  - `ID: Compose_16001371`

## Run locally (without Docker app container)
Start PostgreSQL with Docker:

```bash
docker compose up -d postgres
```

Run application from Maven:

```bash
mvn spring-boot:run
```

Quick checks:

```bash
curl http://localhost:8080/api/health
curl http://localhost:8080/api/seed
```

## Run with full Docker Compose
Build jar first:

```bash
mvn clean package
```

Start full stack:

```bash
docker compose down -v
docker compose up -d --build
```

Health:

```bash
curl http://localhost:1371/api/health
```

Seed sample data:

```bash
curl http://localhost:1371/api/seed
```

## API command cheatsheet
Use base URL:

```bash
BASE=http://localhost:1371
```

Register:

```bash
curl -X POST "$BASE/api/auth/register" \
  -H "Content-Type: application/json" \
  -d '{"name":"Test User","email":"test@guc.edu.eg","password":"secret123"}'
```

Login user:

```bash
curl -X POST "$BASE/api/auth/login" \
  -H "Content-Type: application/json" \
  -d '{"email":"test@guc.edu.eg","password":"secret123"}'
```

Login admin:

```bash
curl -X POST "$BASE/api/auth/login" \
  -H "Content-Type: application/json" \
  -d '{"email":"admin@guc.edu.eg","password":"admin123"}'
```

Set tokens from JSON output:

```bash
USER_TOKEN=<paste-user-token>
ADMIN_TOKEN=<paste-admin-token>
```

Protected endpoint examples:

```bash
# USER or ADMIN
curl "$BASE/api/products" -H "Authorization: Bearer $USER_TOKEN"
curl "$BASE/api/products/1" -H "Authorization: Bearer $USER_TOKEN"
curl "$BASE/api/products/search?name=Code" -H "Authorization: Bearer $USER_TOKEN"
curl "$BASE/api/products/category/BOOKS" -H "Authorization: Bearer $USER_TOKEN"

# ADMIN only
curl -X POST "$BASE/api/products" \
  -H "Authorization: Bearer $ADMIN_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"name":"Admin Product","description":"Created by admin","price":199.99,"category":"ELECTRONICS","stockQuantity":10}'
```

## Build and test
```bash
mvn clean package
mvn test
```

## Run official grader
Important: mount repo **without `:ro`** so grader can write audit/session files.

```bash
docker run --rm --tmpfs /grader-ram:exec \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v "$PWD":/repo \
  -e TASK=3 \
  -e REPO_PATH=/repo \
  abuelmagd/scalable-grading-system:spring2026-latest
```

## Current public grading result
- Public checks: `77/77 PASSED`
