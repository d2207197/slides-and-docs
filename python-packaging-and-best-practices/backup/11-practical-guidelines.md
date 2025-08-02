# Practical Guidelines & Recommendations

### Common Anti-Patterns and Solutions (2024-2025)

Several anti-patterns repeatedly cause issues in Python projects:

#### Library Anti-Patterns

```python
# ❌ Anti-pattern: Pinned dependencies in libraries
# pyproject.toml for a library (WRONG)
dependencies = [
    "requests==2.31.0",  # Too restrictive!
    "pandas==2.1.0",     # Prevents updates!
]

# ✅ Correct: Flexible constraints
dependencies = [
    "requests>=2.25.0,<3.0.0",  # Semantic versioning
    "pandas>=2.0.0,<3.0.0",     # Major version compatibility
]
```

#### Build/Runtime Dependency Mixing

```toml
# ❌ Anti-pattern: Mixed dependencies
dependencies = [
    "requests>=2.25.0",
    "wheel>=0.40.0",      # Build tool, not runtime!
    "pytest>=7.0.0",      # Test tool, not runtime!
]

# ✅ Correct: Proper separation
[project]
dependencies = [
    "requests>=2.25.0",   # Runtime only
]

[build-system]
requires = ["hatchling", "wheel"]  # Build time

[project.optional-dependencies]
test = ["pytest>=7.0.0"]          # Development time
```

#### Circular Import Solutions

```python
# ❌ Anti-pattern: Circular imports
# models.py
from .services import UserService

class User:
    def save(self):
        UserService().save(self)

# services.py  
from .models import User  # Circular!

# ✅ Solution: Dependency inversion
# interfaces.py
from typing import Protocol

class UserRepository(Protocol):
    def save(self, user: 'User') -> None: ...

# models.py
class User:
    def __init__(self, repo: UserRepository):
        self.repo = repo
    
    def save(self):
        self.repo.save(self)
```

### Balancing Flexibility with Reproducibility

The **two-file strategy** has become the modern standard:

```ascii
Flexibility vs Reproducibility Solution

pyproject.toml (Flexible)
├── dependencies = [
│       "requests>=2.25.0,<3.0.0",  # Range for compatibility
│       "fastapi>=0.100.0,<1.0.0",  # Allows updates
│   ]
│
└── Enables: Easy updates, broad compatibility

uv.lock / poetry.lock (Exact)
├── requests==2.31.0
│   ├── certifi==2023.7.22
│   ├── charset-normalizer==3.2.0
│   └── urllib3==2.0.4              # Complete dependency tree
│
└── Ensures: Reproducible builds, production stability
```

#### Modern Workflow

```bash
# Development: Use flexible constraints
uv add "fastapi>=0.100.0,<1.0.0"

# Lock file automatically updated
uv sync  # Installs exact versions from lock

# Production: Deploy with lock file
DOCKER> COPY uv.lock ./
DOCKER> RUN uv sync --no-dev
```

### For Data Scientists & ML Engineers

#### Moving from Scripts to Production

```python
# ❌ Script mentality
import pandas as pd
import numpy as np

df = pd.read_csv('data.csv')
# ... 200 lines of analysis ...
df.to_csv('output.csv')
```

```python
# ✅ Production mentality
from pathlib import Path
from typing import Optional
import pandas as pd
from pydantic import BaseModel

class DataProcessor:
    """Processes customer data for ML pipeline."""

    def __init__(self, config: ProcessingConfig):
        self.config = config

    def process(self, input_path: Path, output_path: Optional[Path] = None) -> pd.DataFrame:
        """Process input data and optionally save to output path."""
        df = self._load_data(input_path)
        df = self._clean_data(df)
        df = self._feature_engineering(df)

        if output_path:
            self._save_data(df, output_path)

        return df

    def _load_data(self, path: Path) -> pd.DataFrame:
        """Load and validate input data."""
        # Implementation with error handling

    def _clean_data(self, df: pd.DataFrame) -> pd.DataFrame:
        """Clean and normalize data."""
        # Implementation

    def _feature_engineering(self, df: pd.DataFrame) -> pd.DataFrame:
        """Create features for ML model."""
        # Implementation
```

#### Recommended Stack for ML Projects

```toml
# pyproject.toml for ML projects
[project]
dependencies = [
    "pandas>=2.0.0,<3.0.0",
    "numpy>=1.24.0,<2.0.0",
    "scikit-learn>=1.3.0,<2.0.0",
    "pydantic>=2.0.0,<3.0.0",      # Data validation
    "click>=8.0.0,<9.0.0",         # CLI interfaces
    "loguru>=0.7.0,<1.0.0",        # Better logging
]

[project.optional-dependencies]
dev = [
    "pytest>=7.0.0",
    "pytest-cov>=4.0.0",
    "jupyter>=1.0.0",
    "ipykernel>=6.0.0",
]
viz = [
    "matplotlib>=3.7.0,<4.0.0",
    "seaborn>=0.12.0,<1.0.0",
    "plotly>=5.0.0,<6.0.0",
]
ml = [
    "torch>=2.0.0,<3.0.0",
    "transformers>=4.30.0,<5.0.0",
    "optuna>=3.0.0,<4.0.0",       # Hyperparameter tuning
]
```

### For Backend Engineers (Coming from Java)

#### Java vs Python Concepts

| Java Concept | Python Equivalent | Notes |
|--------------|-------------------|-------|
| Maven/Gradle | poetry/uv | Dependency management |
| JAR file | Wheel (.whl) | Distribution format |
| Interface | Protocol (typing) | Structural typing |
| Package | Module/Package | Similar but more flexible |
| @Override | No direct equivalent | Duck typing philosophy |
| Spring Boot | FastAPI/Flask | Web frameworks |
| JUnit | pytest | Testing frameworks |

#### Recommended Stack for Backend Services

```toml
# pyproject.toml for backend services
[project]
dependencies = [
    "fastapi>=0.104.0,<1.0.0",     # Web framework
    "uvicorn[standard]>=0.24.0,<1.0.0",  # ASGI server
    "pydantic>=2.5.0,<3.0.0",      # Data validation
    "sqlalchemy>=2.0.0,<3.0.0",    # ORM
    "alembic>=1.12.0,<2.0.0",      # Database migrations
    "redis>=5.0.0,<6.0.0",         # Caching
    "celery>=5.3.0,<6.0.0",        # Task queue
    "prometheus-client>=0.19.0,<1.0.0",  # Metrics
    "structlog>=23.0.0,<24.0.0",   # Structured logging
]
```

### Project Template Structure

```ascii
Modern Python Project Structure

my-project/
├── .github/
│   └── workflows/
│       ├── ci.yml              # Continuous integration
│       └── release.yml         # Release automation
├── docs/
│   ├── conf.py                 # Sphinx configuration
│   ├── index.rst               # Documentation index
│   └── api/                    # API documentation
├── src/
│   └── my_project/
│       ├── __init__.py         # Package initialization
│       ├── cli.py              # Command-line interface
│       ├── config.py           # Configuration management
│       ├── models/             # Data models
│       ├── services/           # Business logic
│       └── utils/              # Utility functions
├── tests/
│   ├── conftest.py             # pytest configuration
│   ├── unit/                   # Unit tests
│   ├── integration/            # Integration tests
│   └── fixtures/               # Test data
├── .gitignore                  # Git ignore patterns
├── .pre-commit-config.yaml     # Pre-commit hooks
├── pyproject.toml              # Project configuration
├── README.md                   # Project documentation
├── CHANGELOG.md               # Version history
└── LICENSE                     # License file
```

### Key Takeaways

1. **Lock Files Revolution**
   - Always use lock files for applications
   - Consider lock files for library development
   - Embrace cryptographic verification for security
   - Understand the two-file strategy: flexible + exact

2. **Tool Specialization (2024-2025)**
   - **uv**: Speed-critical workflows, 10-100x faster
   - **Poetry**: Feature-rich, user experience focused
   - **Ruff**: Unified linting/formatting (replaces 5+ tools)
   - **mypy**: Type checking (still separate, still essential)

3. **Modern Standards**
   - **pyproject.toml** exclusively (no more setup.py)
   - **src layout** for new projects (import safety)
   - **AI-friendly documentation** (future-proofing)
   - **Semantic versioning** with flexible constraints

4. **Scale Considerations**
   - Conda limitations beyond 100 developers
   - uv performance benefits for large teams
   - Container strategy for complex dependencies
   - Private PyPI for enterprise environments

5. **Production Readiness Checklist (Updated)**
   - [ ] Lock files for reproducible builds
   - [ ] pyproject.toml configuration (PEP 621)
   - [ ] src layout structure
   - [ ] Type checking with mypy (progressive adoption)
   - [ ] Ruff for unified linting/formatting
   - [ ] pytest with comprehensive coverage
   - [ ] AI-friendly documentation
   - [ ] Security scanning in CI/CD
   - [ ] Performance monitoring
   - [ ] Deployment automation

6. **Cultural Shift (Enhanced)**
   - From "works on my machine" to "reproducible everywhere"
   - From manual tooling to automated workflows
   - From isolated development to AI-assisted collaboration
   - From ad-hoc processes to standardized practices
   - From tool proliferation to unified solutions
