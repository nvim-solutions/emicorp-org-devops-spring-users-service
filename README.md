# emicorp-org-devops-spring-users-service

Spring Boot microservice that exposes the `users` API for the Emicorp Platform Engineering Lab.

This repository contains **only the application code**. Deployment artifacts live in companion repositories:

| Concern | Repository |
|---|---|
| Application code (this repo) | `emicorp-org-devops-spring-users-service` |
| Helm chart for this service | `emicorp-org-devops-helm-users-service` |
| Platform / GitOps configuration | `emicorp-org-devops-platform` |

---

## Stack

- Java 17
- Spring Boot 4.x (web, data-jpa, actuator)
- PostgreSQL (runtime)
- Maven (build)
- Docker (containerization)

## Local build

```bash
./mvnw clean package -DskipTests
```

The fat jar is produced at `target/users-ms-0.0.1-SNAPSHOT.jar`.

## Docker image

```bash
docker build -t users-service:v1.0 .
```

For Kind local clusters:

```bash
kind load docker-image users-service:v1.0 --name emicorp-org
```

## Runtime configuration

The application reads database connection details from environment variables (injected by Kubernetes via ConfigMap + Secret in the platform repo):

| Variable | Source |
|---|---|
| `DB_HOST`, `DB_PORT`, `DB_NAME` | `ConfigMap/users-config` |
| `DB_USERNAME`, `DB_PASSWORD` | `Secret/service-secrets` |

## CI/CD (planned)

GitHub Actions workflow will:

1. Build the JAR with Maven.
2. Build the container image.
3. Push to GHCR as `ghcr.io/emicorp-org/users-service:<git-sha>` and `:<semver>`.
4. (Future) Open a PR on `emicorp-org-devops-platform` bumping `image.tag` for the target environment.
