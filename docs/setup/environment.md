# MediCare+ Development Environment Specification

This document defines the baseline local development environment for MediCare+, an advanced modular personal healthcare platform. It provides reproducible instructions for configuring and verifying local developer workstations.

---

## 1. System & Hardware Baseline

- **Operating System**: Microsoft Windows 10 Pro 64-bit (Build 19045 or higher)
- **Architecture**: x86_64 / x64
- **Processor Constraints**: Dual-core CPU baseline (Intel Core i5-7300U / 4 threads)
- **System Memory**: 8.0 GB RAM
- **GPU**: Integrated Graphics (Intel HD Graphics 620, non-CUDA)
- **Storage Requirement**: Minimum 20 GB free disk space

### Hardware Limitations & Guidelines
- **Local AI / LLMs**: In-memory execution of medium-to-large language models (7B+ parameters) is unsupported due to 8 GB RAM and integrated GPU constraints. Generative AI operations are delegated to cloud-hosted APIs (e.g., Google Gemini API Free Tier).
- **Machine Learning**: Standard CPU-based classical algorithms (Scikit-learn, small embedding models via ONNX/FastEmbed) are fully supported.
- **Docker Usage**: Docker Desktop is deferred to preserve memory and disk headroom. Local services run natively on the host OS.

---

## 2. Core Runtimes & Versions

| Runtime / Tool | Recommended Version | Verification Command |
| :--- | :--- | :--- |
| **Python** | 3.14.x | `python --version` |
| **Node.js** | 22.x LTS (Active) | `node -v` |
| **pnpm** | 10.x | `pnpm -v` |
| **Git** | 2.45+ | `git --version` |
| **PostgreSQL** | 18.x | `psql --version` |

---

## 3. Database Specification

- **Engine**: PostgreSQL 18.0 (64-bit)
- **Windows Service Name**: `postgresql-x64-18`
- **Default Port**: `5432`
- **Default Installation Location**: `C:\Program Files\PostgreSQL\18`
- **Client Executable (`psql`)**: `C:\Program Files\PostgreSQL\18\bin\psql.exe`
- **Administrative Tooling**: pgAdmin 4 runtime located at `C:\Program Files\PostgreSQL\18\pgAdmin 4\runtime\pgAdmin4.exe`

### Database Verification
Verify service status via PowerShell:
```powershell
Get-Service postgresql-x64-18
```
Verify client connectivity:
```powershell
& "C:\Program Files\PostgreSQL\18\bin\psql.exe" -U postgres -h localhost -p 5432
```

---

## 4. Python Virtual Environment Setup

The backend utilizes an isolated virtual environment (`.venv`) created using Python 3.14 standard library tooling.

### Step-by-Step Reproduction

1. **Create the isolated virtual environment**:
   ```powershell
   python -m venv .venv
   ```

2. **Activate the virtual environment**:
   ```powershell
   .\.venv\Scripts\Activate.ps1
   ```

3. **Install baseline backend dependencies**:
   ```powershell
   .\.venv\Scripts\pip install "fastapi" "uvicorn" "pydantic" "pydantic-settings" "SQLAlchemy>=2.0,<3.0" "alembic" "psycopg[binary]" "pytest"
   ```

4. **Verify package imports and CLI tools**:
   ```powershell
   .\.venv\Scripts\python -c "import fastapi, uvicorn, pydantic, pydantic_settings, sqlalchemy, alembic, psycopg, pytest; print('All core backend packages imported successfully.')"
   .\.venv\Scripts\pytest --version
   .\.venv\Scripts\alembic --version
   ```

---

## 5. Minimal Backend Dependencies

| Package | Purpose | Package Type |
| :--- | :--- | :--- |
| **FastAPI** | High-performance asynchronous REST API framework | Pure Python |
| **Uvicorn** | Asynchronous Server Gateway Interface (ASGI) runtime | Pure Python / Asyncio |
| **Pydantic** | Data validation and domain schema enforcement | Rust-backed CPython wheel |
| **pydantic-settings**| Environment variable and application settings management | Pure Python |
| **SQLAlchemy (2.x)** | Type-safe Object Relational Mapper and database toolkit | C-extension / Binary wheel |
| **Alembic** | Database migration management for PostgreSQL schemas | Pure Python |
| **psycopg (v3)** | Modern async-first PostgreSQL driver (`psycopg[binary]`) | Binary wheel |
| **Pytest** | Automated unit and integration testing suite | Pure Python |

---

## 6. Deferred Technologies & Phased Roadmap

To maintain minimal disk and memory footprint during initial setup, the following components are deliberately deferred:

- **Frontend Application (Next.js / React / TypeScript)**: To be initialized in Stage 3 using `pnpm`.
- **Machine Learning Packages (Scikit-learn / Matplotlib)**: To be introduced in Stage 4 for clinical risk scoring.
- **Optical Character Recognition (Tesseract OCR)**: To be integrated in Stage 5 for document extraction.
- **Containerization (Docker Desktop)**: Deferred to preserve 4 GB+ host RAM on 8 GB workstations.
- **Distributed Cache (Redis)**: Deferred until distributed caching or pub/sub scaling is required.

---

## 7. Environment Verification Checklist

Execute the following commands in the workspace root to validate developer workstation readiness:

```powershell
# 1. Check Git configuration
git status
git remote -v

# 2. Check Python Virtual Environment
.\.venv\Scripts\python --version
.\.venv\Scripts\pip --version

# 3. Test Core Package Imports
.\.venv\Scripts\python -c "import fastapi, uvicorn, pydantic, sqlalchemy, alembic, psycopg, pytest; print('Environment Verified.')"

# 4. Check PostgreSQL Service
Get-Service postgresql-x64-18
```
