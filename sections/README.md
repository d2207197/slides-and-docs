# Python Packaging & Production Best Practices
## A Guide for Software Engineers

This guide has been split into multiple sections for easier navigation and maintenance. Each section covers a specific aspect of Python packaging and production best practices.

## Table of Contents

1. [Introduction & Context](01-introduction-context.md)
   - Why this matters for Taboola
   - Learning objectives

2. [Application vs Framework vs Library: Understanding the Fundamental Differences](02-application-framework-library.md)
   - The core distinction: Who controls whom?
   - Testing requirements and approaches
   - Examples from Python and Java worlds
   - Hybrid tools that are both library and application
   - Dependency management implications

3. [Environment Architecture Layers](03-environment-architecture-layers.md)
   - Complete environment stack (7 layers)
   - Hardware to application layer breakdown
   - Environment control cascade and practical implications

4. [Dependency Management Strategy](04-dependency-management.md)
   - The lock file revolution (2024-2025)
   - Modern dependency hierarchy - six layers
   - System, application, and library dependencies
   - Dependency resolution examples

5. [Development Practices & Quality Assurance](05-development-practices.md)
   - Code quality pyramid
   - Code formatting
   - Type checking with mypy
   - Testing with pytest
   - Testing strategy

6. [Build Systems & Package Management](06-build-systems.md)
   - pyproject.toml: The standardization revolution
   - Modern Python build ecosystem
   - Build backend comparison
   - Tool specialization and performance (2024-2025)
   - Source layout: The modern standard
   - AI-friendly documentation

7. [Module & Subpackage Design](07-module-subpackage-design.md)
   - Module design principles
   - Package structure patterns
   - __init__.py design patterns
   - Advanced package patterns
   - Common anti-patterns to avoid
   - Module design checklist

8. [Environment Management](08-environment-management.md)
   - Python version management
   - pyenv: Python version management
   - Virtual environment strategies
   - Conda: Critical scale limitations (2024-2025 update)
   - Docker for Python applications

9. [Deployment & Distribution](09-deployment-distribution.md)
   - Distribution strategies
   - On-premise environment considerations
   - CI/CD pipeline example

10. [Skill Levels: From Beginner to Expert](10-skill-levels.md)
    - Application developer levels
    - Library developer levels
    - Skill progression matrix

11. [Practical Guidelines & Recommendations](11-practical-guidelines.md)
    - Common anti-patterns and solutions (2024-2025)
    - Balancing flexibility with reproducibility
    - For data scientists & ML engineers
    - For backend engineers (coming from Java)
    - Project template structure
    - Key takeaways

12. [Conclusion](12-conclusion.md)
    - The modern Python packaging paradigm
    - Implementation roadmap for Taboola teams
    - Success metrics
    - Next steps for Taboola engineers

---

*This presentation covers the essential knowledge for building production-ready Python software at Taboola. Focus on mastering the fundamentals before moving to advanced topics.*