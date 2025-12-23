# Exercise: Docker - Multi-Container Application

## Objective
Containerize a full-stack application with separate frontend and backend containers using Docker Compose.

## Requirements

### Required Files
| File | Description |
|------|-------------|
| `docker-compose.yml` | Multi-container orchestration |
| `backend/Dockerfile` | Backend container definition |
| `frontend/Dockerfile` | Frontend container definition |
| `.dockerignore` | Files to exclude from build |

### Application Architecture
```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│    Frontend     │────▶│    Backend      │────▶│    Database     │
│   (nginx:80)    │     │   (app:8080)    │     │  (mysql:3306)   │
└─────────────────┘     └─────────────────┘     └─────────────────┘
```

### docker-compose.yml Requirements
```yaml
version: '3.8'
services:
  frontend:
    # Build from frontend/Dockerfile
    # Expose port 80
    # Depends on backend

  backend:
    # Build from backend/Dockerfile
    # Expose port 8080
    # Environment variables for DB connection
    # Depends on database
    # Health check

  database:
    # Use official MySQL image
    # Volume for data persistence
    # Environment variables for root password, database name
```

### Backend Dockerfile Requirements
- [ ] Use multi-stage build (builder + runtime)
- [ ] Use specific version tags (not :latest)
- [ ] Set WORKDIR
- [ ] Copy only necessary files
- [ ] Expose correct port
- [ ] Define health check
- [ ] Run as non-root user

### Frontend Dockerfile Requirements
- [ ] Multi-stage build (build + nginx)
- [ ] Use node for building
- [ ] Use nginx:alpine for serving
- [ ] Copy build output to nginx html folder
- [ ] Copy custom nginx config if needed

### Technical Requirements
- [ ] All containers on same network
- [ ] Database data persisted with volume
- [ ] Environment variables for configuration
- [ ] Containers restart on failure
- [ ] Proper dependency order (depends_on)

## Sample docker-compose.yml
```yaml
version: '3.8'

services:
  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    ports:
      - "80:80"
    depends_on:
      - backend
    networks:
      - app-network

  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile
    ports:
      - "8080:8080"
    environment:
      - DB_HOST=database
      - DB_PORT=3306
      - DB_NAME=appdb
    depends_on:
      database:
        condition: service_healthy
    networks:
      - app-network

  database:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: ${DB_ROOT_PASSWORD}
      MYSQL_DATABASE: appdb
    volumes:
      - db-data:/var/lib/mysql
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]
      interval: 10s
      timeout: 5s
      retries: 5
    networks:
      - app-network

volumes:
  db-data:

networks:
  app-network:
    driver: bridge
```

## Commands to Test
```bash
# Build and start
docker-compose up --build

# Run in background
docker-compose up -d

# View logs
docker-compose logs -f

# Stop and remove
docker-compose down

# Remove with volumes
docker-compose down -v
```

## Evaluation Criteria
| Criteria | Points |
|----------|--------|
| docker-compose.yml correct | 25 |
| Backend Dockerfile (multi-stage) | 20 |
| Frontend Dockerfile (multi-stage) | 20 |
| Networking & volumes | 15 |
| Best practices (non-root, health) | 20 |
| **Total** | **100** |

## Submission Checklist
- [ ] All required files present
- [ ] Placed in `23-docker/` folder
- [ ] `docker-compose up` runs successfully
- [ ] All containers communicate
- [ ] Data persists after restart
- [ ] Committed and pushed to repository
