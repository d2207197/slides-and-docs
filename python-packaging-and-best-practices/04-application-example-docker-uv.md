# Section 06: Server Application Development with Docker + uv

> How server applications differ from libraries: production deployment, containerization, and full environment control

## What Makes a Good Server Application

Successful server applications share four key traits:

### 🎯 **Full Environment Control**
- **Exact dependencies**: `==1.5.3` not `>=1.0,<2.0`
- **Controlled deployment**: Docker images, Kubernetes
- **Security hardening**: Non-root users, minimal images

### 🔒 **Reproducible Deployments**
- **Lock files**: `uv.lock` pins exact versions
- **Container images**: Same environment everywhere
- **Staged rollouts**: Dev → staging → production

### 🚀 **Production-First Design**
- **Health checks**: `/health` endpoints
- **Graceful shutdown**: Signal handling
- **Horizontal scaling**: Stateless design

### ⚡ **Fast Development**
- **Local iteration**: `uv run` without Docker
- **Quick builds**: Multi-stage containers
- **Efficient testing**: Isolated environments

**Key Insight**: Server applications succeed by being **reliable and deployable**.

## Server Application vs Library: Side-by-Side Comparison

Building on Section 05's library patterns, here's what changes for server applications:

| Aspect | **Libraries** (Section 05) | **Server Applications** (This Section) |
|--------|---------------------------|----------------------------------|
| **Environment Control** | ⚠️ Minimal - specify ranges | ✅ Full - you control everything |
| **Dependencies** | `>=1.0,<2.0` (flexible) | `==1.5.3` (exact, via lock files) |
| **Project Structure** | `src/` layout + pyproject.toml | **+ Dockerfile + uv.lock** |
| **Distribution** | PyPI packages (.whl, .tar.gz) | **Docker images for deployment** |
| **Testing Strategy** | Matrix testing (multiple versions) | **Integration testing (specific stack)** |
| **User Access** | `pip install your-library` | **HTTP APIs, web interfaces** |
| **Deployment** | Users control installation | **Container orchestration (K8s, Docker Swarm)** |

**Key Insight**: Server applications **add** production deployment concerns to the library foundation.

## Server Application Project Structure

### Extended Structure from Libraries

Building on the library structure from Section 05, server applications add deployment-specific files:

```
my-api/                          # Server application repository
├── pyproject.toml               # Same as libraries
├── README.md                    # Same as libraries  
├── src/my_api/                  # Same as libraries
├── tests/                       # Same as libraries
├── Dockerfile                   # NEW: Container definition
├── uv.lock                      # NEW: Exact dependency versions
├── .dockerignore                # NEW: Exclude files from Docker build
├── .python-version              # NEW: Pin Python version for deployment
└── .github/workflows/           # NEW: CI/CD for deployment
    ├── test.yml
    └── deploy.yml
```

### Key Deployment Files (vs Libraries)

**`uv.lock`** - Exact dependency versions:
- Libraries: Flexible ranges (`>=1.0,<2.0`) for maximum compatibility
- Server apps: Exact versions (`==1.5.3`) for reproducible deployments

**`Dockerfile`** - Container environment definition:
- Libraries: Distributed as packages, users handle environment
- Server apps: Complete environment packaged for deployment

**`.dockerignore`** - Build optimization:
- Excludes development files (tests/, docs/, .git/) from production images

**`.python-version`** - Python version for local development:
- Libraries: Support multiple Python versions for broad compatibility
- Server apps: Pin specific version for local testing (should match Dockerfile)

## Dependency Strategy for Server Applications

Server applications use **conservative dependency ranges** compared to libraries:

**🎯 Version Strategy**:
- **Libraries**: Major versions (`>=1.0,<2.0`) for maximum compatibility
- **Server Apps**: Minor versions (`>=0.100,<0.200`) for stability + security patches

**Example**: 
```toml
# Conservative but practical ranges
dependencies = [
    "fastapi>=0.100,<0.200",    # Minor version range
    "uvicorn>=0.20,<0.30",      # Patch updates OK, minor changes tested  
    "psycopg2>=2.9,<2.10",      # Very conservative for database drivers
]
```

## Lock Files and Reproducibility

### Why Applications Need Lock Files

**❌ Problem with Flexible Ranges** (even conservative ones can drift):
```toml
# pyproject.toml (what you specify)
dependencies = ["fastapi>=0.100,<0.200"]

# What actually gets installed?
# Dev environment: fastapi==0.104.0 (latest at dev time)
# Production: fastapi==0.108.2 (latest at deploy time)
# Result: Different behavior between environments!
```

**✅ Application Solution** (lock files ensure consistency):
```toml
# pyproject.toml (conservative but flexible ranges)
dependencies = ["fastapi>=0.100,<0.200"]

# uv.lock (auto-generated, pins exact versions)
[[package]]
name = "fastapi"  
version = "0.104.0"  # Exact version used everywhere
```

### How uv.lock Works

```bash
# Create/update lock file (run this during development)
uv lock                         # Resolves all dependencies once

# Install exact versions from lock file (run this in production)
uv sync --frozen                 # Fast install - no dependency resolution needed

# Lock file contains complete dependency tree
# fastapi==0.104.0 depends on starlette==0.27.0, etc.
# All transitive dependencies already resolved to exact versions
```

**📝 Lock File Standards**: Currently each tool uses its own format (`uv.lock`, `poetry.lock`, etc.). **PEP 751** (accepted March 2025) proposes a standard `pylock.toml` format that all tools will eventually support.

**⚡ Performance Benefit**: Lock files skip dependency resolution during installation, making deployments significantly faster since all version conflicts are already resolved.

### Lock File Benefits

Lock files solve the core server application challenges:

- **🔄 Reproducibility**: Same versions across all environments  
- **⚡ Speed**: Skip dependency resolution during deployment
- **🛡️ Security**: Track exact versions for vulnerability management
- **🐛 Debugging**: Eliminate "works on my machine" issues

## Server Application Development Workflow

### uv Commands for Server Applications

**Key Difference from Libraries**: Server applications use lock files for exact reproducibility:

```bash
# Setup and dependency management
uv init my-api                # Initialize server application
uv add "fastapi>=0.100,<0.200"  # Conservative version ranges
uv lock                       # Generate uv.lock with exact versions

# Development workflow
uv sync                       # Install dependencies (allows updates)
uv run uvicorn my_api.main:app --reload --host 0.0.0.0 --port 8000

# Production deployment
uv sync --frozen              # Install exact versions from lock file
```

**🔄 Development vs Production Pattern**:
- **Development**: `uv sync` (updates allowed)
- **Production**: `uv sync --frozen` (exact versions only)

## Docker Integration

### Multi-Stage Docker Build

Applications use Docker to package the complete runtime environment. Here's an optimized Dockerfile:

```dockerfile
# === STAGE 1: Build Dependencies ===
FROM python:3.11-slim as builder  # Should match .python-version

# Install system build dependencies
RUN apt-get update && apt-get install -y \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

# Install uv
COPY --from=ghcr.io/astral-sh/uv:latest /uv /bin/uv

# Set working directory
WORKDIR /app

# Copy dependency definitions
COPY pyproject.toml uv.lock ./

# Install dependencies into .venv
# This layer is cached unless dependencies change
RUN uv sync --frozen --no-dev

# === STAGE 2: Runtime (Production) ===
FROM python:3.11-slim  # Same version as .python-version

# Create non-root user for security
RUN useradd --create-home --shell /bin/bash app

# Install minimal runtime dependencies
RUN apt-get update && apt-get install -y \
    libpq5 \
    && rm -rf /var/lib/apt/lists/*

# Copy Python environment from builder stage
COPY --from=builder --chown=app:app /app/.venv /app/.venv

# Copy application code
WORKDIR /app
COPY --chown=app:app src/ src/

# Switch to non-root user
USER app

# Activate virtual environment
ENV PATH="/app/.venv/bin:$PATH"

# Health check
HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 \
    CMD python -c "import requests; requests.get('http://localhost:8000/health')"

# Run application
EXPOSE 8000
CMD ["python", "-m", "my_api.main"]
```

### Why Multi-Stage Builds?

**🏗️ Build Stage Benefits**:
- **Contains build tools**: gcc, build-essential for compiling C extensions
- **Larger but temporary**: Gets discarded after building dependencies
- **Build caching**: Dependency layer cached unless pyproject.toml/uv.lock changes

**🚀 Runtime Stage Benefits**:
- **Minimal size**: Only runtime dependencies, no build tools (~200MB vs ~1.2GB)
- **Security**: Smaller attack surface, fewer installed packages
- **Performance**: Faster container startup and deployment

### Docker Layer Optimization

```dockerfile
# ✅ Good: Dependencies change rarely → cached layer
COPY pyproject.toml uv.lock ./
RUN uv sync --frozen --no-dev

# ✅ Good: Code changes frequently → separate layer
COPY src/ src/

# ❌ Bad: Code and dependencies together → cache invalidated often
COPY . .
RUN uv sync --frozen --no-dev
```

**Caching Strategy**:
1. **Base image layers**: Cached across all builds
2. **System packages**: Cached unless OS dependencies change  
3. **Python dependencies**: Cached unless pyproject.toml/uv.lock changes
4. **Application code**: Re-built on every code change

### Development vs Production Integration

**💻 Local Development** (flexible, fast iteration):
```bash
# Fast development cycle with uv
uv run uvicorn my_api.main:app --reload --host 0.0.0.0 --port 8000
```

**🚀 Containerized Production** (reproducible, optimized):
```bash
# Build with exact dependencies from uv.lock
docker build -t my-api:latest .

# Deploy with environment configuration
docker run -p 8000:8000 \
  -e DATABASE_URL=postgresql://... \
  -e PYTHONUNBUFFERED=1 \
  my-api:latest
```

**Key Integration**: uv handles dependency management, Docker handles deployment packaging.

### Layer Separation in Practice

Applications implement the 6-layer environment architecture from Section 03:

| Layer | **Libraries** | **Applications** | Tool Responsibility |
|-------|---------------|------------------|-------------------|
| **6. Dependencies** | Flexible ranges | ✅ **Exact versions (uv.lock)** | **uv** manages reproducibility |
| **5. Virtual Environment** | Development only | ✅ **Production .venv** | **uv** creates, **Docker** packages |
| **4. Python Runtime** | User's choice | ✅ **Controlled version** | **Docker** base image |
| **3. System Dependencies** | User's system | ✅ **Dockerfile apt packages** | **Docker** manages |
| **2. Operating System** | User's OS | ✅ **Container OS** | **Docker** provides |
| **1. Hardware** | User's hardware | ✅ **Cloud infrastructure** | Kubernetes/cloud |

**Key Insight**: Applications control **all layers**, libraries control **none**.

## Application-Specific Best Practices

### Security Hardening

```dockerfile
# ✅ Security best practices
RUN useradd --create-home --shell /bin/bash app  # Non-root user
USER app                                         # Run as non-root
COPY --chown=app:app src/ src/                  # Proper file ownership

# ✅ Minimal attack surface
FROM python:3.11-slim                           # Minimal base image
RUN rm -rf /var/lib/apt/lists/*                 # Clean package cache
```

### Performance Optimization

```dockerfile
# ✅ Python optimization
ENV PYTHONUNBUFFERED=1        # Real-time output
ENV PYTHONDONTWRITEBYTECODE=1 # No .pyc files

# ✅ Build optimization  
RUN uv sync --frozen --no-dev    # Production dependencies only
COPY --from=builder /app/.venv   # Copy compiled environment
```

### Operational Excellence

```dockerfile
# ✅ Health checks
HEALTHCHECK --interval=30s --timeout=10s \
    CMD python -c "import requests; requests.get('http://localhost:8000/health')"

# ✅ Proper signal handling
CMD ["python", "-m", "my_api.main"]  # Proper process management
```

## Key Takeaways

1. **Layer separation is critical** → uv manages Python dependencies, Docker handles deployment
2. **Local development without Docker** → faster iteration with `uv run` for development servers
3. **Lock files enable reproducibility** → same exact versions across all environments
4. **Conservative dependency ranges** → minor version constraints for server applications
5. **Multi-stage Docker builds** → separate build tools from production runtime
6. **Python-first tooling** → use uv for all Python dependency management, not mixed tools

---

**Next Section**: [05-module-subpackage-design.md](05-module-subpackage-design.md) - Module organization and import management  
**Previous Section**: [03-library-repository-structure.md](03-library-repository-structure.md) - Simple library example with uv