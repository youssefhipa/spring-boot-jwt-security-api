# JWT-Secured REST API (Spring Security)

A Spring Boot REST API for managing products, secured end-to-end with
JWT-based authentication and role-based authorization. Built for a
massively-scalable-applications course module on securing REST APIs.

## What it demonstrates

- Stateless authentication with JSON Web Tokens (register/login issue a
  signed JWT; no server-side sessions)
- Custom `JwtAuthFilter` that validates the bearer token on every request
  and populates the Spring Security context
- `CustomUserDetailsService` backing Spring Security off a JPA `User`
  repository
- BCrypt password hashing
- Role-based access control — `USER` can read products, `ADMIN` can also
  create/update them
- Stateless Spring Security filter chain configuration (`SecurityConfig`)

## Tech stack

- Java 25, Spring Boot, Spring Security, Spring Data JPA
- PostgreSQL
- `jjwt` (JWT creation/parsing)
- Maven, Docker / Docker Compose

## API overview

| Method | Path                          | Access        |
|--------|-------------------------------|---------------|
| POST   | `/api/auth/register`          | public        |
| POST   | `/api/auth/login`              | public        |
| GET    | `/api/products`                | USER / ADMIN  |
| GET    | `/api/products/{id}`           | USER / ADMIN  |
| GET    | `/api/products/search?name=`   | USER / ADMIN  |
| GET    | `/api/products/category/{cat}` | USER / ADMIN  |
| POST   | `/api/products`                | ADMIN only    |
| GET    | `/api/health`                  | public        |
| GET    | `/api/seed`                    | public (dev)  |

## Running locally

Start PostgreSQL:

```bash
docker compose up -d postgres
```

Run the app:

```bash
mvn spring-boot:run
```

Seed sample data and try it out:

```bash
curl http://localhost:8080/api/seed

curl -X POST http://localhost:8080/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{"name":"Test User","email":"test@example.com","password":"secret123"}'

curl -X POST http://localhost:8080/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"test@example.com","password":"secret123"}'
# -> copy the returned token

curl http://localhost:8080/api/products -H "Authorization: Bearer <token>"
```

## Running with Docker

```bash
mvn clean package
docker compose up -d --build
```

App is exposed on host port `1371`.

## Build and test

```bash
mvn clean package
mvn test
```

> Note: the JWT signing secret and default DB credentials in
> `application.yaml` are placeholder values for local development only —
> not intended for production use as-is.
