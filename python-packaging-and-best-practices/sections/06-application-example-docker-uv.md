# Section 06: Application Development with Docker + uv

> How applications differ from libraries: production deployment, reproducibility, and environment control

## Application Characteristics

Building on the library concepts from Section 05, applications have fundamentally different requirements:

### 🎯 **Full Environment Control** 
*Unlike libraries which have no environment control*

- **You own the deployment**: Control OS, Python version, system packages
- **Reproducible environments**: Exact dependency versions across dev/staging/production
- **Security hardening**: Minimal attack surface, non-root execution
- **Performance optimization**: Tailored for specific use cases

### 🔒 **Exact Dependencies**
*Opposite of library flexibility*

- **Lock files required**: `uv.lock` pins exact versions for reproducibility
- **Predictable behavior**: Same code, same dependencies, same results
- **Security compliance**: Track specific versions for vulnerability management
- **Change control**: Deliberate dependency updates, not automatic

### 🚀 **Production-First Design**
*Beyond library development concerns*

- **Deployment artifacts**: Docker images, not PyPI packages  
- **Infrastructure integration**: Load balancers, databases, monitoring
- **Operational concerns**: Health checks, logging, metrics
- **Scaling considerations**: Horizontal scaling, stateless design

### ⚡ **Performance Focus**
*Different optimization targets than libraries*

- **Startup time**: Fast application boot for containers
- **Memory efficiency**: Optimized for specific workload patterns
- **Build optimization**: Cached layers, minimal final image size
- **Runtime efficiency**: Application-specific performance tuning

## Application vs Library: Side-by-Side Comparison

Building on Section 05's library patterns, here's what changes for applications:

| Aspect | **Libraries** (Section 05) | **Applications** (This Section) |
|--------|---------------------------|----------------------------------|
| **Environment Control** | ❌ None - must adapt | ✅ Full - you control everything |
| **Dependencies** | `>=1.0,<2.0` (flexible) | `==1.5.3` (exact, via lock files) |
| **Project Structure** | `src/` layout + pyproject.toml | **+ Dockerfile + uv.lock** |
| **Distribution** | PyPI packages (.whl, .tar.gz) | **Docker images** |
| **Testing Strategy** | Matrix testing (multiple versions) | **Integration testing (specific stack)** |
| **User Installation** | `pip install your-library` | **`docker run your-app`** |
| **Updates** | Users control when to update | **You control deployment timing** |

**Key Insight**: Applications **add** production deployment concerns to the library foundation.

## Application Project Structure

### Extended Structure from Libraries

Building on the library structure from Section 05, applications add deployment-specific files:

```
my-api/                          # Application repository
├── 📋 Library Foundation (from Section 05)
│   ├── pyproject.toml           # Dependencies with flexible ranges
│   ├── README.md                # Project description
│   └── .python-version          # Development Python version
├── 📁 Source Code (same as libraries)
│   ├── src/
│   │   └── my_api/              # Python package
│   │       ├── __init__.py
│   │       └── main.py          # Application entry point
│   └── tests/
│       └── test_main.py
├── 🐳 Production Deployment (NEW)
│   ├── Dockerfile               # Container definition
│   ├── uv.lock                  # Exact dependency versions
│   └── .dockerignore            # What to exclude from image
├── 🔧 Development Tools
│   └── .github/workflows/
       ├── test.yml              # CI testing
       └── deploy.yml            # CD deployment
```

### What's Different from Libraries

**🔒 Lock Files** (`uv.lock`):
- **Purpose**: Pin exact dependency versions for reproducibility
- **Libraries don't need this**: They use flexible ranges
- **Applications require this**: Same behavior across environments

**🐳 Docker Files**:
- **Dockerfile**: Defines the complete application environment
- **.dockerignore**: Excludes development files from production images
- **Libraries don't use Docker**: They're components, not deployable units

**🚀 Production Configuration**:
- **Environment variables**: Runtime configuration
- **Health checks**: Application readiness monitoring  
- **Security settings**: Non-root users, minimal permissions

## Lock Files and Reproducibility

### Why Applications Need Lock Files

**❌ Library Problem** (flexible ranges can cause issues in production):
```toml
# pyproject.toml (what you specify)
dependencies = ["fastapi>=0.104,<0.105"]

# What actually gets installed?
# Dev environment: fastapi==0.104.0 (latest at dev time)
# Production: fastapi==0.104.3 (latest at deploy time)
# Result: Different behavior between environments!
```

**✅ Application Solution** (lock files ensure consistency):
```toml
# pyproject.toml (still use flexible ranges for compatibility)
dependencies = ["fastapi>=0.104,<0.105"]

# uv.lock (auto-generated, pins exact versions)
[[package]]
name = "fastapi"  
version = "0.104.0"  # Exact version used everywhere
```

### How uv.lock Works

```bash
# Create/update lock file (run this during development)
uv lock

# Install exact versions from lock file (run this in production)
uv sync --frozen

# Lock file contains complete dependency tree
# fastapi==0.104.0 depends on starlette==0.27.0, etc.
# All transitive dependencies pinned to exact versions
```

### Lock File Benefits for Applications

**🔄 Reproducible Deployments**:
- **Same code + same dependencies = same behavior**
- **Dev, staging, production all use identical package versions**
- **No surprise behavior changes from dependency updates**

**🛡️ Security and Compliance**:
- **Track exact versions for vulnerability scanning**
- **Controlled dependency updates through lock file updates**
- **Audit trail of what's deployed when**

**🐛 Easier Debugging**:
- **Eliminate "works on my machine" issues**
- **Reproduce production issues locally with same dependencies**
- **Rollback includes dependencies, not just application code**

## Docker Integration

### Multi-Stage Docker Build

Applications use Docker to package the complete runtime environment. Here's an optimized Dockerfile:

```dockerfile
# === STAGE 1: Build Dependencies ===
FROM python:3.11-slim as builder

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
FROM python:3.11-slim

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

## Production Deployment Patterns

### Environment-Specific Configuration

**⚙️ pyproject.toml** (base configuration):
```toml
[project]
name = "my-api"
version = "1.0.0"
dependencies = [
    "fastapi>=0.104,<0.105",
    "uvicorn>=0.24,<0.25",
    "psycopg2>=2.9,<3",
]

[project.optional-dependencies]
dev = ["pytest>=7.0.0", "black>=22.0.0"]
```

**🔒 uv.lock** (exact versions for reproducibility):
```yaml
# Auto-generated, don't edit manually
version = 1
requires-python = ">=3.11"

[[package]]
name = "fastapi"
version = "0.104.0"
source = { registry = "https://pypi.org/simple" }
# ... complete dependency tree with exact versions
```

**🐳 Dockerfile** (production environment):
```dockerfile
# Production configuration
ENV PYTHONUNBUFFERED=1
ENV PYTHONDONTWRITEBYTECODE=1

# Application configuration via environment variables
ENV API_HOST=0.0.0.0
ENV API_PORT=8000
ENV DATABASE_URL=${DATABASE_URL}
```

### Development vs Production Workflows

**💻 Development Workflow**:
```bash
# Local development (flexible, fast iteration)
uv sync           # Install with dev dependencies
uv run pytest    # Run tests
uv run uvicorn my_api.main:app --reload
```

**🚀 Production Workflow**:
```bash
# Production build (reproducible, optimized)
docker build -t my-api:latest .
docker run -p 8000:8000 \
  -e DATABASE_URL=postgresql://... \
  my-api:latest
```

### Layer Separation in Practice

Applications implement the 6-layer environment architecture from Section 03:

| Layer | **Libraries** | **Applications** | Tool Responsibility |
|-------|---------------|------------------|-------------------|
| **6. Dependencies** | Flexible ranges | ✅ **Exact versions (uv.lock)** | uv manages reproducibility |
| **5. Virtual Environment** | Development only | ✅ **Production .venv** | uv creates, Docker packages |
| **4. Python Runtime** | User's choice | ✅ **Controlled version** | Docker base image |
| **3. System Dependencies** | User's system | ✅ **Dockerfile apt packages** | Docker manages |
| **2. Operating System** | User's OS | ✅ **Container OS** | Docker provides |
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

### Common Anti-Patterns Avoided

**❌ What NOT to do**:
1. **Single-stage builds**: Bloated images with build tools in production
2. **Root user execution**: Security vulnerability
3. **No lock files**: Non-reproducible deployments
4. **Mixed dev/prod configs**: Environment inconsistencies
5. **Large image layers**: Poor caching and slow deployments

**✅ What this pattern provides**:
1. **Multi-stage builds**: Optimized production images
2. **Security hardening**: Non-root user, minimal attack surface  
3. **Reproducible deployments**: Lock files ensure consistency
4. **Layer separation**: Each tool manages appropriate concerns
5. **Development parity**: Same dependencies in dev and production

## Key Takeaways

1. **Applications extend library patterns** → add Docker, lock files, production concerns
2. **Full environment control enables reproducibility** → exact versions, controlled infrastructure  
3. **Multi-stage builds optimize production images** → build tools separate from runtime
4. **Lock files ensure deployment consistency** → same dependencies across environments
5. **Layer separation prevents architectural problems** → right tool for each concern
6. **Security requires deliberate design** → non-root users, minimal attack surface
7. **Production-first thinking drives application design** → deployment, not just development

---

**Next Section**: [07-module-subpackage-design.md](07-module-subpackage-design.md) - Module organization and import management  
**Previous Section**: [05-library-repository-structure.md](05-library-repository-structure.md) - Simple library example with uv