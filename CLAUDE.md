# ideal-guacamole

Spring Boot 3.2.2 on Java 21, built with Maven, targeting AWS. Owner: Swapnil
(`swapnil-srivastava`). Bare Spring Boot app: no controllers yet, only the actuator (`/actuator/health`).

## Commands

- Build and test: `./mvnw -B package` (what CI runs). Tests only: `./mvnw -B test`.
- Run locally: `./mvnw spring-boot:run` (port 8080). Health: `GET /actuator/health`.
- Docker: `./mvnw -B package && docker build -t swapnilsrivastava/aws .` (the Dockerfile copies `target/*.jar`).
- The devcontainer has Java 21, Maven and Docker.

## Layout

- Code: `src/main/java/eu/swapnilsrivastava/swapnilsrivastava/`, base package
  `eu.swapnilsrivastava.swapnilsrivastava`. Tests mirror it under `src/test/java`.
- Config: `src/main/resources/application.properties`. `application.properties` is empty. Add settings as `${ENV_VAR}` placeholders.

## CI and deploy

- `Java CI with Maven` runs on every push and pull request to `main`.
- When it succeeds on `main`, `Publish Docker image` pushes `swapnilsrivastava/aws` to Docker Hub.
- There is no deploy step in this repo. CI only builds and pushes the Docker image; anything running on AWS was deployed by hand.

## Things to know before changing anything

- **JDK mismatch:** `pom.xml` builds for Java 21 but the `Dockerfile` runs on `openjdk:17.0.2-jdk-slim` (Java 17), so the pushed image cannot start the jar. Known and not yet fixed; fix it (e.g. `eclipse-temurin:21-jre`) only when asked or when the task is to get the image running.
- Secrets (database, Kafka, Docker Hub, cloud keys) live in GitHub secrets or the cloud
  console. Never commit or print them; tell Swapnil which ones to add.
- Don't deploy, create cloud resources or change cloud settings yourself. Open a PR
  and say what merging it will trigger.

## How this fits with the website

The main project is the website, `swapnil-srivastava/reimagined-guide`
(swapnilsrivastava.eu, Next.js + Supabase on Vercel). This repo is one of six small
backend services kept beside it as cloud/framework experiments. The website does not
depend on it for anything visitors use today. The agreed way to wire a backend in
later is a Next.js rewrite on the website with the backend URL in an env var
(`/svc/<name>/...`), so the browser never calls this service directly and CORS is
not needed.

For step-by-step work (new endpoint, config, shipping) use the `spring-service` skill
in `.claude/skills/`.
