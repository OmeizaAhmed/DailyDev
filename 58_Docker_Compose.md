# Docker Compose: Run Your App and Its Dependencies Together

A backend that works on one developer’s machine can still fail on another because its database, cache, or environment variables are configured differently. Docker Compose helps make that setup repeatable by describing the parts of an application stack in one YAML file.

## Define the stack once

A Compose file describes services—usually containers—and how they connect. It can also configure ports, environment variables, networks, and persistent volumes. Docker Compose reads that file and starts the services together.

For a small ASP.NET Core API backed by PostgreSQL, a local `compose.yaml` might look like this:

```yaml
services:
  api:
    build: .
    ports:
      - "8080:8080"
    environment:
      ASPNETCORE_URLS: http://+:8080
      ConnectionStrings__DefaultConnection: Host=db;Port=5432;Database=appdb;Username=appuser;Password=devpassword
    depends_on:
      db:
        condition: service_healthy

  db:
    image: postgres:17
    environment:
      POSTGRES_DB: appdb
      POSTGRES_USER: appuser
      POSTGRES_PASSWORD: devpassword
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U appuser -d appdb"]
      interval: 5s
      timeout: 3s
      retries: 5

volumes:
  postgres_data:
```

Run `docker compose up --build` from the directory containing the file. Compose builds the API image, starts PostgreSQL, and connects the services on its default network.

Notice that the API connects to `db`, the Compose service name, rather than `localhost`. Inside a container, `localhost` refers to that same container. Compose makes services discoverable to one another by service name on the shared network.

## Startup order is not the same as readiness

A common mistake is assuming `depends_on` means the database is ready to accept connections. Basic dependency ordering only ensures the database container starts first. The `service_healthy` condition makes Compose wait for the database health check before starting the API.

That helps with startup races, but the API should still handle a database becoming unavailable later. Compose’s startup check does not replace application level retries or resilience.

## Keep local data and secrets in mind

The named volume keeps PostgreSQL data when the container is recreated. Removing the containers with `docker compose down` leaves named volumes in place; `docker compose down -v` also removes them, including the stored database data.

The example password is for local development only. For shared or production environments, provide credentials through an appropriate secrets mechanism instead of committing real passwords to the Compose file.

**Key takeaway:** Docker Compose makes a multi-service development environment easy to describe and start consistently. Use service names for container-to-container connections, health checks for readiness, and persistent volumes for data you want to keep.