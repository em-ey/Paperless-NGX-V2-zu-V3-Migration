# Anleitung: Upgrade von Paperless-ngx v2 auf v3

Offizieller Migration Guide: https://docs.paperless-ngx.com/migration-v3/

> Alle Befehle werden als `root` ausgeführt. Die `docker-compose.yml` liegt unter `/home/paperless/`.

```bash
cd /home/paperless/
```

## 1. Zwischenschritt: Update auf v2.20.15

Vor dem Upgrade auf v3 muss zunächst auf die empfohlene Version **2.20.15** aktualisiert werden.

### 1.1 Container stoppen

```bash
docker compose down
```

### 1.2 `docker-compose.yml` anpassen

```bash
nano docker-compose.yml
```

Folgende Zeile:

```yaml
webserver:
    image: ghcr.io/paperless-ngx/paperless-ngx:latest
```

ändern in:

```yaml
webserver:
    image: ghcr.io/paperless-ngx/paperless-ngx:2.20.15
```

### 1.3 Update durchführen

```bash
docker compose pull
```

### 1.4 Container starten

```bash
docker compose up -d
```

### 1.5 Logs prüfen

```bash
docker compose logs -f webserver
```

### 1.6 Container stoppen, wenn alles okay ist

```bash
docker compose down
```

## 2. Upgrade auf v3

### 2.1 `docker-compose.yml` erneut anpassen

```bash
nano docker-compose.yml
```

Folgende Zeile:

```yaml
webserver:
    image: ghcr.io/paperless-ngx/paperless-ngx:2.20.15
```

wieder ändern in:

```yaml
webserver:
    image: ghcr.io/paperless-ngx/paperless-ngx:latest
```

Im Abschnitt `environment:` folgende Einträge **hinzufügen**:

```yaml
environment:
    PAPERLESS_SECRET_KEY: $SECRET_KEY  # z. B. in der .env-Datei setzen
    PAPERLESS_DBENGINE: postgresql
    PAPERLESS_DB_OPTIONS: "sslmode=disable"
```

Und folgenden Eintrag **entfernen**:

```yaml
PAPERLESS_DBSSLMODE: disable
```

### 2.2 Container mit angepasster Compose-Datei aktualisieren

```bash
docker compose pull
```

### 2.3 Container starten

```bash
docker compose up -d
```

### 2.4 Optional: Logs beim Start beobachten

```bash
docker compose logs -f webserver
```

## Zusammenfassung der Änderungen

| Vorher | Nachher |
|---|---|
| `PAPERLESS_DBSSLMODE: disable` | `PAPERLESS_DB_OPTIONS: "sslmode=disable"` |
| *(nicht vorhanden)* | `PAPERLESS_SECRET_KEY: $SECRET_KEY` |
| *(nicht vorhanden)* | `PAPERLESS_DBENGINE: postgresql` |
| `image: ...:latest` (v2) | `image: ...:2.20.15` → zurück auf `latest` (v3) |

## Referenzen

- [Offizieller Migration Guide (v3)](https://docs.paperless-ngx.com/migration-v3/)
