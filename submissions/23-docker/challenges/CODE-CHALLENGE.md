# Code Challenges: Docker

Docker challenges to practice containerization skills. Each challenge requires creating specific files.

---

## Challenge 1: Multi-Stage Build
**Files:** `multi-stage/Dockerfile`, `multi-stage/app.js`

Create a multi-stage Dockerfile for a Node.js application that:
- Uses `node:18-alpine` for building
- Uses `node:18-alpine` for production
- Installs only production dependencies in final image
- Final image should be under 150MB

```bash
docker build -t multi-stage-app ./multi-stage
docker images multi-stage-app  # Check size
```

---

## Challenge 2: Health Check
**Files:** `healthcheck/Dockerfile`, `healthcheck/server.py`

Create a simple Python HTTP server with Docker health check.

**Requirements:**
- Python server on port 8080
- `/health` endpoint returns 200 OK
- Dockerfile includes `HEALTHCHECK` instruction
- Check interval: 30 seconds
- Timeout: 10 seconds
- Retries: 3

```bash
docker build -t healthcheck-app ./healthcheck
docker run -d healthcheck-app
docker ps  # Should show health status
```

---

## Challenge 3: Docker Compose Stack
**Files:** `compose-stack/docker-compose.yml`

Create a docker-compose file for a web application stack:

**Services:**
- `web`: nginx serving static files (port 80)
- `api`: any simple backend (port 3000)
- `db`: MySQL 8.0
- `cache`: Redis

**Requirements:**
- Define proper `depends_on`
- Use named volumes for database persistence
- Create custom network
- Use environment variables for credentials
- Include resource limits (memory: 512MB max)

---

## Challenge 4: Custom Network
**Files:** `networking/docker-compose.yml`

Create a multi-network setup:

```
┌─────────────────┐     ┌─────────────────┐
│   frontend-net  │     │   backend-net   │
│  ┌───────────┐  │     │  ┌───────────┐  │
│  │   nginx   │◄─┼─────┼─►│    api    │  │
│  └───────────┘  │     │  └─────┬─────┘  │
└─────────────────┘     │        │        │
                        │  ┌─────▼─────┐  │
                        │  │  database │  │
                        │  └───────────┘  │
                        └─────────────────┘
```

**Requirements:**
- `nginx` only on frontend network
- `api` on both networks
- `database` only on backend network
- `nginx` cannot directly reach `database`

---

## Challenge 5: Volume Backup
**Files:** `volume-backup/backup.sh`, `volume-backup/restore.sh`

Write scripts to backup and restore Docker volumes.

**backup.sh:**
```bash
./backup.sh myvolume /backups
# Creates: /backups/myvolume-20250115-120000.tar.gz
```

**restore.sh:**
```bash
./restore.sh /backups/myvolume-20250115-120000.tar.gz newvolume
# Restores backup to 'newvolume'
```

**Requirements:**
- Use temporary container to access volume
- Compress backup with gzip
- Verify volume exists before backup
- Handle errors gracefully

---

## Submission
Place all challenge files in: `23-docker/challenges/`

| Challenge | Files | Status |
|-----------|-------|--------|
| Multi-Stage Build | `multi-stage/*` | ⬜ |
| Health Check | `healthcheck/*` | ⬜ |
| Compose Stack | `compose-stack/*` | ⬜ |
| Custom Network | `networking/*` | ⬜ |
| Volume Backup | `volume-backup/*` | ⬜ |
