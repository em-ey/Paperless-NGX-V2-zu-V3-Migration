# Guide: Upgrading Paperless-ngx from v2 to v3

Official migration guide: https://docs.paperless-ngx.com/migration-v3/

> All commands are run as `root`. The `docker-compose.yml` is located at `/home/paperless/`.

```bash
cd /home/paperless/
```

## 1. Intermediate step: Update to v2.20.15

Before upgrading to v3, you first need to update to the recommended version **2.20.15**.

### 1.1 Stop the containers

```bash
docker compose down
```

### 1.2 Adjust `docker-compose.yml`

```bash
nano docker-compose.yml
```

Change this line:

```yaml
webserver:
    image: ghcr.io/paperless-ngx/paperless-ngx:latest
```

to:

```yaml
webserver:
    image: ghcr.io/paperless-ngx/paperless-ngx:2.20.15
```

### 1.3 Pull the update

```bash
docker compose pull
```

### 1.4 Start the containers

```bash
docker compose up -d
```

### 1.5 Check the logs

```bash
docker compose logs -f webserver
```

### 1.6 Stop the containers once everything looks fine

```bash
docker compose down
```

## 2. Upgrade to v3

### 2.1 Adjust `docker-compose.yml` again

```bash
nano docker-compose.yml
```

Change this line:

```yaml
webserver:
    image: ghcr.io/paperless-ngx/paperless-ngx:2.20.15
```

back to:

```yaml
webserver:
    image: ghcr.io/paperless-ngx/paperless-ngx:latest
```

In the `environment:` section, **add** the following entries:

```yaml
environment:
    PAPERLESS_SECRET_KEY: $SECRET_KEY  # e.g. set in the .env file
    PAPERLESS_DBENGINE: postgresql
    PAPERLESS_DB_OPTIONS: "sslmode=disable"
```

And **remove** the following entry:

```yaml
PAPERLESS_DBSSLMODE: disable
```

### 2.2 Update the containers with the adjusted compose file

```bash
docker compose pull
```

### 2.3 Start the containers

```bash
docker compose up -d
```

### 2.4 Optional: watch the logs on startup

```bash
docker compose logs -f webserver
```

## Summary of changes

| Before | After |
|---|---|
| `PAPERLESS_DBSSLMODE: disable` | `PAPERLESS_DB_OPTIONS: "sslmode=disable"` |
| *(not present)* | `PAPERLESS_SECRET_KEY: $SECRET_KEY` |
| *(not present)* | `PAPERLESS_DBENGINE: postgresql` |
| `image: ...:latest` (v2) | `image: ...:2.20.15` → back to `latest` (v3) |

## References

- [Official migration guide (v3)](https://docs.paperless-ngx.com/migration-v3/)
