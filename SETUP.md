# BMS Validator - Detailed Setup Guide

This guide provides step-by-step instructions for setting up BMS Validator in various environments.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Quick Start with Docker](#quick-start-with-docker)
- [Manual Development Setup](#manual-development-setup)
- [Production Deployment](#production-deployment)
- [Troubleshooting](#troubleshooting)

---

## Prerequisites

### Required Software

| Software | Version | Purpose |
|----------|---------|---------|
| Python | 3.11+ | Backend runtime |
| Node.js | 18+ | Frontend build |
| PostgreSQL | 14+ | Primary database |
| Redis | 7+ | Task queue & cache |
| Docker | 24+ | Containerization (optional) |

### System Requirements

- **Minimum**: 4GB RAM, 2 CPU cores, 10GB disk
- **Recommended**: 8GB RAM, 4 CPU cores, 50GB disk
- **For large files (100MB+)**: 16GB RAM recommended

---

## Quick Start with Docker

The fastest way to get started.

### 1. Clone and Configure

```bash
# Clone the repository
git clone https://github.com/csg09/bms-validator.git
cd bms-validator

# Create environment file
cp .env.example .env

# Edit .env if needed (defaults work for local development)
```

### 2. Start Services

```bash
# Build and start all containers
docker-compose up -d

# Check status
docker-compose ps

# View logs
docker-compose logs -f
```

### 3. Initialize Database

```bash
# Run migrations
docker-compose exec backend alembic upgrade head

# (Optional) Seed sample data
docker-compose exec backend python -m app.db.seed
```

### 4. Access Application

- **Frontend**: http://localhost:3000
- **API Docs**: http://localhost:8000/docs
- **API Health**: http://localhost:8000/health

### Docker Commands Reference

```bash
# Stop all services
docker-compose down

# Stop and remove volumes (fresh start)
docker-compose down -v

# Rebuild after code changes
docker-compose up -d --build

# View specific service logs
docker-compose logs -f backend
docker-compose logs -f celery-worker
```

---

## Manual Development Setup

For development without Docker.

### 1. Database Setup

#### PostgreSQL

```bash
# macOS (Homebrew)
brew install postgresql@15
brew services start postgresql@15

# Ubuntu/Debian
sudo apt install postgresql postgresql-contrib
sudo systemctl start postgresql

# Windows
# Download installer from https://www.postgresql.org/download/windows/
```

Create database and user:

```sql
-- Connect to PostgreSQL
psql -U postgres

-- Create database and user
CREATE USER bms_user WITH PASSWORD 'bms_password';
CREATE DATABASE bms_validator OWNER bms_user;
GRANT ALL PRIVILEGES ON DATABASE bms_validator TO bms_user;

-- Exit
\q
```

#### Redis

```bash
# macOS
brew install redis
brew services start redis

# Ubuntu/Debian
sudo apt install redis-server
sudo systemctl start redis

# Windows
# Use WSL2 or download from https://github.com/microsoftarchive/redis/releases
```

### 2. Backend Setup

```bash
cd backend

# Install Poetry (if not installed)
curl -sSL https://install.python-poetry.org | python3 -
# Or on Windows:
# pip install poetry

# Install dependencies
poetry install

# Activate virtual environment
poetry shell

# Create environment file
cp .env.example .env
# Edit .env with your database credentials

# Run migrations
alembic upgrade head

# (Optional) Seed data
python -m app.db.seed
```

#### Start Backend Services

Terminal 1 - API Server:
```bash
cd backend
poetry run uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

Terminal 2 - Celery Worker:
```bash
cd backend
poetry run celery -A app.tasks.celery_app worker --loglevel=info --pool=solo
```

### 3. Frontend Setup

```bash
cd frontend

# Install dependencies
npm install

# Create environment file (optional - defaults work)
cp .env.example .env.local

# Start development server
npm run dev
```

### 4. Verify Installation

1. Open http://localhost:5173 (Vite dev server)
2. API docs at http://localhost:8000/docs
3. Try uploading a test file

---

## Production Deployment

### Using Docker Compose

1. **Configure Environment**

```bash
cp .env.example .env

# Edit .env with production values
nano .env
```

Important production settings:
```env
ENVIRONMENT=production
DEBUG=false
SECRET_KEY=<generate-secure-random-string>
POSTGRES_PASSWORD=<strong-password>
CORS_ORIGINS=https://yourdomain.com
```

2. **Deploy**

```bash
docker-compose -f docker-compose.yml up -d
```

3. **Set up reverse proxy** (nginx example):

```nginx
server {
    listen 80;
    server_name yourdomain.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl;
    server_name yourdomain.com;

    ssl_certificate /path/to/cert.pem;
    ssl_certificate_key /path/to/key.pem;

    location / {
        proxy_pass http://localhost:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }

    location /api {
        proxy_pass http://localhost:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

### Cloud Deployment

See platform-specific guides:
- [AWS ECS Deployment](docs/deploy-aws.md) (coming soon)
- [Google Cloud Run](docs/deploy-gcp.md) (coming soon)
- [Azure Container Apps](docs/deploy-azure.md) (coming soon)

---

## Troubleshooting

### Common Issues

#### "Connection refused" to PostgreSQL

```bash
# Check if PostgreSQL is running
sudo systemctl status postgresql  # Linux
brew services list               # macOS

# Check connection
psql -h localhost -U bms_user -d bms_validator
```

#### "Connection refused" to Redis

```bash
# Check if Redis is running
redis-cli ping  # Should return "PONG"

# Start Redis if needed
sudo systemctl start redis  # Linux
brew services start redis   # macOS
```

#### Celery worker not processing tasks

```bash
# Check if Celery is connected to Redis
celery -A app.tasks.celery_app inspect ping

# Check active tasks
celery -A app.tasks.celery_app inspect active

# Restart worker with debug logging
celery -A app.tasks.celery_app worker --loglevel=debug --pool=solo
```

#### Database migration errors

```bash
# Check current migration state
alembic current

# Show migration history
alembic history

# Reset migrations (CAUTION: destroys data)
alembic downgrade base
alembic upgrade head
```

#### Frontend can't connect to API

1. Check CORS settings in `.env`:
   ```env
   CORS_ORIGINS=http://localhost:5173,http://localhost:3000
   ```

2. Verify API is running:
   ```bash
   curl http://localhost:8000/health
   ```

3. Check browser console for errors

### Getting Help

- **GitHub Issues**: [Report a bug](https://github.com/csg09/bms-validator/issues/new?template=bug_report.md)
- **Discussions**: [Ask questions](https://github.com/csg09/bms-validator/discussions)

---

## Next Steps

After setup:

1. 📖 Read the [User Guide](docs/user-guide.md)
2. 🔧 Configure [Validation Rules](docs/validation-rules.md)
3. 🔌 Explore the [API Reference](http://localhost:8000/docs)
4. 🤝 Check out [Contributing Guide](CONTRIBUTING.md)
