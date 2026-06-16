# DataIngestorApp

Parent repository for the Data Ingestor platform. It composes two independent services as [Git submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules):

| Submodule | Repository | Role |
|-----------|------------|------|
| [DataIngestorService](DataIngestorService/) | [fjord-code/DataIngestorService](https://github.com/fjord-code/DataIngestorService) | Polls WeakApp, publishes normalized messages to RabbitMQ |
| [DataStatisticsService](DataStatisticsService/) | [fjord-code/DataStatisticsService](https://github.com/fjord-code/DataStatisticsService) | Consumes queue messages, persists and aggregates data |

## Clone

```bash
git clone --recurse-submodules https://github.com/fjord-code/DataIngestorApp.git
cd DataIngestorApp
```

If you already cloned without submodules:

```bash
git submodule update --init --recursive
```

## Local development (Docker)

The root `docker-compose.yml` includes both service stacks. DataIngestorService creates the shared `app-network` and RabbitMQ broker; DataStatisticsService joins that network.

```bash
docker compose up -d
```

Service endpoints (defaults):

| Service | URL |
|---------|-----|
| Data Ingestor API | http://localhost:5000 |
| Data Statistics API | http://localhost:5100 |
| RabbitMQ management | http://localhost:15672 |
| WeakApp | http://localhost:8080 |
| Grafana | http://localhost:3000 |

Copy environment files from each submodule before first run (see submodule READMEs).

## Working on a submodule

Submodule changes are a **two-step** process:

1. **Inside the submodule** — branch, commit, and push as usual:
   ```bash
   cd DataIngestorService
   git checkout -b feature/my-change
   git commit -am "Describe change"
   git push -u origin feature/my-change
   ```

2. **In this parent repo** — record the new submodule commit pointer:
   ```bash
   cd ..
   git add DataIngestorService
   git commit -m "Bump DataIngestorService to <short-sha>"
   git push
   ```

## Updating submodules

Pull parent changes and sync submodules to the pinned commits:

```bash
git pull
git submodule update --init --recursive
```

Advance a submodule to the latest remote `main` (or its tracked branch):

```bash
git submodule update --remote DataIngestorService
git add DataIngestorService
git commit -m "Bump DataIngestorService to latest main"
```

## Integration contract

Cross-service messaging uses RabbitMQ only — no HTTP calls between services. See [`.cursor/data-ingestor-integration.mdc`](.cursor/data-ingestor-integration.mdc) for the message contract, topology, and layering rules.

## Publish to GitHub

Create an empty repository at `https://github.com/fjord-code/DataIngestorApp` (no README), then push this parent repo:

```bash
git remote add origin https://github.com/fjord-code/DataIngestorApp.git   # skip if already set
git push -u origin main
```
