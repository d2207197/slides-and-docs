# Application Example: Docker + uv Production Pattern

## Repository Structure
```
my-api/                 # Repository name (kebab-case)
├── pyproject.toml      # uv manages dependencies + environment (Layer 5-6)
├── uv.lock             # Locked versions for reproducibility (Layer 6)
├── Dockerfile          # Docker manages OS/system (Layer 2-3)
├── .python-version     # Python version specification (Layer 4)
├── src/
│   └── my_api/         # Python package (snake_case, matches repo name)
│       ├── __init__.py
│       └── main.py     # FastAPI application
└── tests/
    └── test_main.py    # Test files
```

## Key Configuration Files

**pyproject.toml** - Dependencies with flexible ranges:
```toml
[project]
name = "my-api"
version = "1.0.0"
requires-python = ">=3.11"
dependencies = [
    "fastapi>=0.104,<0.105",
    "uvicorn>=0.24,<0.25",
    "numpy>=1.24,<2",        # C extensions
    "psycopg2>=2.9,<3",      # Needs libpq-dev
]

[dependency-groups]
dev = ["pytest==7.4.3", "ruff==0.1.5", "mypy==1.7.0"]
```

**uv.lock** pins exact versions (e.g., `fastapi==0.104.1`) for reproducibility.

## Development Commands
```bash
uv sync                    # Setup environment
uv run pytest             # Run tests
uv run uvicorn src.my_api.main:app --reload
```

## Dockerfile - Production Deployment

### Why Multi-Stage Build?

Single-stage builds include everything needed for both **building** and **running**, creating bloated images:

```dockerfile
# ❌ Single-stage (bloated: ~1.2GB)
FROM python:3.11.7-slim
RUN apt-get update && apt-get install -y \
    gcc g++ python3-dev libpq-dev build-essential  # ALL stay in final image
RUN pip install uv
COPY pyproject.toml uv.lock ./
RUN uv sync --frozen
COPY src/ ./src/
CMD ["uvicorn", "src.my_api.main:app"]
```

**Multi-stage separates concerns**:
- **Builder stage**: Heavy build tools (gcc, headers) + dependency compilation
- **Runtime stage**: Only compiled artifacts + minimal runtime dependencies

```dockerfile
# ✅ Multi-stage build (optimized: ~200MB)

# === STAGE 1: Build Dependencies ===
FROM python:3.11.7-slim as builder

# Install build dependencies (these will be discarded)
RUN apt-get update && apt-get install -y \
    gcc g++ python3-dev libpq-dev \
    && rm -rf /var/lib/apt/lists/*

RUN pip install uv==0.1.5

WORKDIR /app
COPY pyproject.toml uv.lock ./
# Build all dependencies into .venv (includes compilation)
RUN uv sync --frozen --no-group dev --no-install-project

# === STAGE 2: Runtime (Clean Start) ===
FROM python:3.11.7-slim
RUN useradd -m -u 1000 appuser

WORKDIR /app
# Copy ONLY the compiled .venv (no build tools!)
COPY --from=builder /app/.venv /app/.venv
COPY src/ ./src/

ENV PATH="/app/.venv/bin:$PATH"
USER appuser

CMD ["uvicorn", "src.my_api.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

### Key Benefits

| Aspect | Single-Stage | Multi-Stage | Improvement |
|--------|-------------|-------------|-------------|
| **Image Size** | ~1.2GB | ~200MB | **6x smaller** |
| **Build Tools** | Included in runtime | Discarded after build | **Reduced attack surface** |
| **Layer Caching** | Poor (tools + deps mixed) | Excellent (separate concerns) | **Faster rebuilds** |
| **Security** | More packages = more CVEs | Minimal runtime packages | **Fewer vulnerabilities** |

### Caching Strategy
```dockerfile
# Dependencies change rarely → cached layer
COPY pyproject.toml uv.lock ./
RUN uv sync --frozen

# Code changes frequently → separate layer  
COPY src/ ./src/
```

**Result**: Code changes don't trigger dependency rebuilds, making development iterations much faster.

## Why This Pattern Works

**Key Benefits**:
- **⚡ Fast local development**: `uv sync` in seconds, no Docker overhead
- **🛠️ Full IDE integration**: Debuggers, profilers work natively
- **🔄 Instant iteration**: Change code → test immediately
- **🐳 Production parity**: Docker ensures deployment consistency
- **📦 Single source of truth**: `uv.lock` pins versions everywhere

## Layer Separation in Practice

This example demonstrates the 6-layer architecture in action:

| Layer | Tool/Technology | Files | Purpose |
|-------|----------------|-------|---------|
| **1. Hardware Infrastructure** | AWS/Google Cloud | — | Physical compute resources |
| **2. Operating System** | Docker | `Dockerfile` | Container OS (Ubuntu-based) |
| **3. System Dependencies** | apt | `Dockerfile` | System libraries (gcc, python3-dev, libpq-dev) |
| **4. Language Runtime** | uv/Docker | `.python-version`, `Dockerfile` | Python 3.11.7 |
| **5. Runtime Environment** | uv | `.venv/` (created by uv) | Isolated Python environment |
| **6. Dependency Management** | uv | `pyproject.toml`, `uv.lock` | Application dependencies |

**The Pattern**: 
- **Local development**: Use uv for layers 4-6 (fast iteration)
- **Production deployment**: Use Docker for layers 2-3 + uv for layers 4-6 (reproducible deployment)

## Best Practices Demonstrated

### Environment Control Strategy
- **Application type**: Full control over environment
- **Dependency strategy**: Pin exact versions (`uv.lock`) for reproducibility
- **Testing responsibility**: End-to-end tests ensure all layers work together

### Development Workflow
```bash
# Local development (fast)
uv sync                              # Setup environment
uv add requests                      # Add new dependency
uv run python -m pytest             # Run tests
uv run uvicorn src.my_api.main:app --reload  # Local server

# Production build (reproducible)
docker build -t my-api .             # Build optimized image
docker run -p 8000:8000 my-api       # Deploy
```

### File Organization
- **Repository naming**: `kebab-case` for GitHub/Docker compatibility
- **Package naming**: `snake_case` for Python import compatibility
- **Structure**: Standard `src/` layout for better packaging
- **Configuration**: Modern `pyproject.toml` + lock files

## Common Pitfalls Avoided

### ❌ Anti-Patterns This Example Avoids
1. **Installing dependencies in Dockerfile**: Bypasses uv's dependency resolution
2. **No lock files**: Non-reproducible deployments
3. **Root user in container**: Security vulnerability
4. **Single-stage build**: Bloated images with build tools
5. **Mixed development/production configs**: Environment inconsistencies

### ✅ Best Practices Applied
1. **Layer separation**: Each tool manages its appropriate layers
2. **Reproducible builds**: Lock files ensure consistent environments
3. **Security hardening**: Non-root user, minimal attack surface
4. **Optimized images**: Multi-stage builds, proper caching
5. **Development parity**: Same dependencies in dev and prod

---

**Next Section**: [06-library-repository-structure.md](06-library-repository-structure.md) - Modern library structure and organization
**Previous Section**: [04-python-environment-tools.md](04-python-environment-tools.md) - Python environment tools and patterns