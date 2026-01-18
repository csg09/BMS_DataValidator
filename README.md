# BMS Data Validator

<div align="center">

![BMS Validator](https://img.shields.io/badge/BMS-Data%20Validator-blue?style=for-the-badge)
![Python](https://img.shields.io/badge/Python-3.11+-green?style=for-the-badge&logo=python)
![FastAPI](https://img.shields.io/badge/FastAPI-0.100+-teal?style=for-the-badge&logo=fastapi)
![React](https://img.shields.io/badge/React-18+-blue?style=for-the-badge&logo=react)
![License](https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge)

**Enterprise-grade Building Management System (BMS) data validation platform**

*Transform 2-4 hours of manual validation into minutes of automated processing*

[Features](#features) • [Quick Start](#quick-start) • [Documentation](#documentation) • [Screenshots](#screenshots) • [Contributing](#contributing)

</div>

---

## 🎯 Overview

BMS Data Validator is a production-ready platform that automates the validation of Building Management System exports. It detects data quality issues, generates professional reports, and provides actionable insights for facility managers, BMS integrators, and data engineers.

### The Problem

BMS data exports from vendors like Honeywell, Johnson Controls, Siemens, and Schneider Electric often contain:
- Missing or null sensor readings
- Anomalous spikes indicating sensor malfunctions
- Junk values from communication errors
- Non-standard naming conventions
- Out-of-range measurements

Manual validation of these files takes **2-4 hours per file** and is error-prone.

### The Solution

BMS Validator **automates this entire process**, reducing validation time to **minutes** while providing:
- Comprehensive data quality scoring (0-100)
- Severity-categorized issue detection (Critical/High/Medium/Low)
- Professional PDF and Excel reports for stakeholders
- Vendor-specific feedback templates
- Real-time processing with progress tracking

---

## ✨ Features

### 🔍 Intelligent Validation Engine

| Validator | Description |
|-----------|-------------|
| **Null Detection** | Identifies missing values, empty strings, and placeholder nulls |
| **Spike Detection** | Multi-method anomaly detection (Z-Score, IQR, Modified Z-Score, Rate of Change) |
| **Junk Detection** | Detects communication errors, encoding issues, and garbage values |
| **Range Validation** | Validates sensor readings against physical constraints |
| **Naming Validation** | Checks compliance with vendor-specific naming conventions |

### 📊 Professional Reporting

- **Excel Reports** - Detailed workbooks with data, issues, and charts
- **PDF Summaries** - Executive-ready reports with visualizations
- **Vendor Feedback** - Email templates for vendor communication
- **JSON Export** - Machine-readable format for API integration

### 🚀 Production-Ready Architecture

- **Async Processing** - Celery workers handle large files (up to 200MB)
- **Real-time Progress** - WebSocket/polling for live status updates
- **Multi-tenant Ready** - User isolation and role-based access
- **Scalable Storage** - PostgreSQL + Redis for reliability

---

## 🏗️ Tech Stack

### Backend
- **FastAPI** - High-performance async API framework
- **Celery** - Distributed task queue for background processing
- **PostgreSQL** - Primary database
- **Redis** - Task broker and caching
- **SQLAlchemy** - ORM with Alembic migrations
- **Pandas/Dask** - Data processing (auto-switches based on file size)

### Frontend
- **React 18** - Modern UI with hooks
- **TypeScript** - Type-safe development
- **Material-UI (MUI)** - Professional component library
- **React Query** - Server state management
- **Vite** - Fast build tooling

### DevOps
- **Docker & Docker Compose** - Containerized deployment
- **Poetry** - Python dependency management
- **GitHub Actions** - CI/CD pipelines (coming soon)

---

## 🚀 Quick Start

### Prerequisites

- Python 3.11+
- Node.js 18+
- PostgreSQL 14+
- Redis 7+
- Docker (optional, for containerized setup)

### Option 1: Docker Compose (Recommended)

```bash
# Clone the repository
git clone https://github.com/csg09/bms-validator.git
cd bms-validator

# Copy environment file
cp .env.example .env

# Start all services
docker-compose up -d

# Access the application
open http://localhost:3000
```

### Option 2: Manual Setup

#### Backend Setup

```bash
cd backend

# Install Poetry if not installed
pip install poetry

# Install dependencies
poetry install

# Set up environment variables
cp .env.example .env
# Edit .env with your database credentials

# Run database migrations
poetry run alembic upgrade head

# Seed initial data (optional)
poetry run python -m app.db.seed

# Start the API server
poetry run uvicorn app.main:app --reload --host 0.0.0.0 --port 8000

# In a separate terminal, start Celery worker
poetry run celery -A app.tasks.celery_app worker --loglevel=info --pool=solo
```

#### Frontend Setup

```bash
cd frontend

# Install dependencies
npm install

# Start development server
npm run dev

# Access at http://localhost:5173
```

---

## 📖 Documentation

### API Documentation

Once running, access the interactive API docs:
- **Swagger UI**: http://localhost:8000/docs
- **ReDoc**: http://localhost:8000/redoc

### Project Structure

```
bms-validator/
├── backend/
│   ├── app/
│   │   ├── api/            # API routes and endpoints
│   │   ├── core/           # Configuration and security
│   │   ├── db/             # Database models and session
│   │   ├── models/         # SQLAlchemy models
│   │   ├── services/       # Business logic
│   │   │   ├── validators/ # Validation engines
│   │   │   └── report_generators/  # Report generation
│   │   └── tasks/          # Celery background tasks
│   ├── alembic/            # Database migrations
│   ├── storage/            # File storage (uploads, reports)
│   └── tests/              # Test suite
├── frontend/
│   ├── src/
│   │   ├── components/     # React components
│   │   ├── hooks/          # Custom hooks
│   │   ├── pages/          # Page components
│   │   ├── services/       # API client
│   │   └── types/          # TypeScript types
│   └── public/             # Static assets
└── docker/                 # Docker configurations
```

### Configuration

Key environment variables:

| Variable | Description | Default |
|----------|-------------|---------|
| `DATABASE_URL` | PostgreSQL connection string | `postgresql://...` |
| `REDIS_URL` | Redis connection string | `redis://localhost:6379` |
| `CELERY_BROKER_URL` | Celery broker URL | `redis://localhost:6379/0` |
| `UPLOAD_DIR` | File upload directory | `./storage/uploads` |
| `REPORTS_DIR` | Generated reports directory | `./storage/reports` |
| `LARGE_FILE_THRESHOLD_MB` | Switch to Dask above this size | `50` |

---

## 📸 Screenshots

### Dashboard
*Upload files and view validation status at a glance*

### Validation Results
*Comprehensive issue breakdown with severity categorization*

### Reports
*Professional PDF and Excel reports ready for stakeholders*

---

## 🔧 Supported File Formats

| Format | Extension | Max Size |
|--------|-----------|----------|
| Excel | `.xlsx`, `.xls` | 200 MB |
| CSV | `.csv` | 200 MB |
| Tab-Separated | `.tsv`, `.txt` | 200 MB |

### Supported BMS Vendors

- Honeywell
- Johnson Controls
- Siemens
- Schneider Electric
- Tridium/Niagara
- Custom (configurable)

---

## 🧪 Testing

```bash
# Run backend tests
cd backend
poetry run pytest

# Run with coverage
poetry run pytest --cov=app --cov-report=html

# Run frontend tests
cd frontend
npm test
```

---

## 🛣️ Roadmap

- [ ] WebSocket real-time updates
- [ ] Multi-file batch processing
- [ ] Custom validation rule builder
- [ ] Historical trend analysis
- [ ] API rate limiting
- [ ] SSO/SAML authentication
- [ ] Kubernetes deployment configs
- [ ] Mobile-responsive UI improvements

---

## 🤝 Contributing

Contributions are welcome! Please read our [Contributing Guide](CONTRIBUTING.md) for details.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgments

- Built with guidance from [AutoCoder](https://github.com/leonvanzyl/autoCoder) framework by Leon van Zyl
- Inspired by the need for better BMS data quality tools in the building automation industry
- Thanks to the FastAPI, React, and open-source communities

---

## 📬 Contact

**CSG** - [@csg09](https://github.com/csg09)

Project Link: [https://github.com/csg09/bms-validator](https://github.com/csg09/bms-validator)

---

<div align="center">

**⭐ Star this repo if you find it useful! ⭐**

</div>
