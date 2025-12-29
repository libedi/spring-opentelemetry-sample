# Spring Boot 4 + Observability 로컬 샘플

이 프로젝트는 **Spring Boot 4 환경에서 관측성(Observability: Metrics + Traces)을 로컬에서 직접 체험**하기 위한 샘플입니다.

- **Metrics**: Micrometer + Actuator → Prometheus → Grafana
- **Traces**: OpenTelemetry (OTLP/HTTP) → OpenTelemetry Collector → Tempo → Grafana

> 로컬 머신에는 **Java, Docker Desktop**이 필요합니다.

---

## 1. 전체 아키텍처

```
Spring Boot App (localhost:8080)
  ├─ Metrics → /actuator/prometheus (scrape)
  │     └─ Prometheus (Docker) → Grafana (Metrics)
  └─ Traces  → OTLP HTTP (/v1/traces)
        └─ OpenTelemetry Collector (Docker)
              └─ Tempo (Docker) → Grafana (Traces)
```

---

## 2. 프로젝트 구성

```
spring-opentelemetry-sample/
  ├─ spring-opentelemetry-sample-app/
  │  ├─ build.gradle
  │  └─ src/main/resources/config/application.yml
  └─ observability/
     ├─ docker-compose.yml
     ├─ otel-collector.yaml
     ├─ prometheus.yaml
     ├─ tempo.yaml
     └─ grafana-datasources.yaml
```

- **spring-opentelemetry-sample-app**: Spring Boot 4 애플리케이션
- **observability**: Prometheus, Grafana, Tempo, OTel Collector 실행용 Docker 구성

---

## 3. 준비 사항

- Java **25** (Gradle toolchain 기준)
- Docker Desktop (Docker Compose 포함)

버전 확인:
```bash
docker --version
docker compose version
```

---

## 4. 빠른 시작 (Quick Start)

1) 관측성 스택 실행
```bash
cd observability
docker compose up -d
```

2) Spring Boot 실행
```bash
cd spring-opentelemetry-sample-app
./gradlew bootRun
```

중지:
```bash
cd observability
docker compose down
```

---

## 5. 동작 확인

1) 애플리케이션 상태
- http://localhost:8080/actuator/health
  - 응답 예시: `{"status":"UP"}`

2) Metrics (Actuator)
- http://localhost:8080/actuator/prometheus

3) Prometheus 타깃 확인
- http://localhost:9090/targets
  - `spring` 타깃이 **UP**

4) Trace 생성
PowerShell:
```powershell
1..30 | % { iwr http://localhost:8080/hello | Out-Null }
```

5) Grafana 확인
- Metrics: http://localhost:3000 → Explore → Prometheus
  - Query: `http_server_requests_seconds_count`
- Traces: http://localhost:3000 → Explore → Tempo
  - Service Name: `spring-opentelemetry-sample`

---

## 6. 주요 설정 요약

`spring-opentelemetry-sample-app/src/main/resources/config/application.yml`:
```yaml
management:
  endpoints:
    web:
      exposure:
        include:
        - health
        - prometheus
  opentelemetry:
    tracing:
      export:
        otlp:
          endpoint: http://localhost:4318/v1/traces
  tracing:
    sampling:
      probability: 1.0
```

---

## 7. 다음 단계

- Metrics: Alertmanager 연동
- Traces: Collector Tail Sampling 적용
- Logs: Loki 연동 (LGTM 스택)
- 보안: Actuator 노출 정책 강화
