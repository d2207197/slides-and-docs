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
   - Complete environment stack (6 layers)
   - Hardware to dependencies layer breakdown
   - Environment control cascade and practical implications

4. [Python Environment Tools: Cross-Layer Control](04-python-environment-tools.md)
   - Multi-layer control tools (Docker, uv, conda, poetry)
   - Tool control scope across environment layers
   - Java tools comparison
   - Tool combination strategies and selection guide

5. [Library Repository Structure & pyproject.toml Design](05-library-repository-structure.md)
   - Standard Python library repository structure
   - pyproject.toml design for library projects
   - Module and subpackage organization patterns
   - Library distribution and publishing workflows
   - uv vs hatch for library development

6. [Evolution of Modern Python Tools](06-evolution-modern-tools.md)
   - Modern Python tool ecosystem evolution
   - Performance improvements and adoption trends
   - Tool specialization and enterprise considerations

7. [Build Systems & Package Management](07-build-systems.md)
   - pyproject.toml: The standardization revolution
   - Modern Python build ecosystem
   - Build backend comparison
   - Tool specialization and performance (2024-2025)
   - Source layout: The modern standard
   - AI-friendly documentation

8. [Module & Subpackage Design](08-module-subpackage-design.md)
   - Module design principles
   - Package structure patterns
   - __init__.py design patterns
   - Advanced package patterns
   - Common anti-patterns to avoid
   - Module design checklist

9. [Environment Management](09-environment-management.md)
   - Python version management
   - pyenv: Python version management
   - Virtual environment strategies
   - Conda: Critical scale limitations (2024-2025 update)
   - Docker for Python applications

10. [Deployment & Distribution](10-deployment-distribution.md)
    - Distribution strategies
    - On-premise environment considerations
    - CI/CD pipeline example

11. [Skill Levels: From Beginner to Expert](11-skill-levels.md)
    - Application developer levels
    - Library developer levels
    - Skill progression matrix

12. [Practical Guidelines & Recommendations](12-practical-guidelines.md)
    - Common anti-patterns and solutions (2024-2025)
    - Balancing flexibility with reproducibility
    - For data scientists & ML engineers
    - For backend engineers (coming from Java)
    - Project template structure
    - Key takeaways

13. [Conclusion](13-conclusion.md)
    - The modern Python packaging paradigm
    - Implementation roadmap for Taboola teams
    - Success metrics
    - Next steps for Taboola engineers

---

*This presentation covers the essential knowledge for building production-ready Python software at Taboola. Focus on mastering the fundamentals before moving to advanced topics.*