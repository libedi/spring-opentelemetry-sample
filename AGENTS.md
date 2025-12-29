# Repository Guidelines

## Project Structure & Module Organization
- Root contains `spring-opentelemetry-sample-app/` (Spring Boot app) and `observability/` (Docker Compose stack for Prometheus, Grafana, Tempo, and the OTel Collector).
- Application code lives in `spring-opentelemetry-sample-app/src/main/java/com/example/demo`, with controllers under `controller/`.
- Tests live in `spring-opentelemetry-sample-app/src/test/java/com/example/demo`.
- Runtime configuration is in `spring-opentelemetry-sample-app/src/main/resources/config/application.yml`.
- Observability configs are in `observability/docker-compose.yml` plus `otel-collector.yaml`, `prometheus.yaml`, and `tempo.yaml`.

## Build, Test, and Development Commands
- `cd spring-opentelemetry-sample-app`
- `./gradlew bootRun` runs the app locally on `http://localhost:8080`.
- `./gradlew test` executes JUnit 5 tests.
- `./gradlew build` builds the runnable artifact.
- `cd observability && docker compose up -d` starts the local metrics/traces stack.
- `docker compose down` stops the observability stack.

## Coding Style & Naming Conventions
- Java packages follow `com.example.demo`; classes use PascalCase and methods use camelCase.
- Keep Spring MVC endpoints under `controller/` and prefer descriptive handler names.
- Follow existing formatting: Java/Gradle files use tabs in this repo, and YAML uses two-space indentation.
- Configuration keys in `application.yml` should follow Spring Boot conventions (`spring.*`, `management.*`).

## Testing Guidelines
- JUnit 5 is the test platform; Spring Boot test starters are already configured.
- Use `*Tests.java` naming (for example, `SpringOpentelemetrySampleApplicationTests`).
- Keep test sources under `src/test/java` and prefer focused tests in addition to `@SpringBootTest` when adding new endpoints.

## Commit & Pull Request Guidelines
- Use short, imperative commit summaries (for example, "Update observability stack configs").
- PRs should describe changes, list verification commands, and link issues when applicable.
- Include screenshots if Grafana dashboards or UI behavior changes.

## Security & Configuration Tips
- Local services assume default ports (app `8080`, Prometheus `9090`, Grafana `3000`, Tempo `3200`).
- OTLP trace export points to `http://localhost:4318/v1/traces`; update both `application.yml` and the collector config if you change it.
