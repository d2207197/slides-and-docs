# Python Packaging & Production Best Practices - Sections

This guide has been split into focused sections covering Python packaging and production best practices. The content emphasizes **environment control as the fundamental driver of dependency management** in the Python ecosystem.

## Core Theme

Understanding who controls what in the Python ecosystem is crucial for making effective architectural decisions. This presentation bridges the gap between "script mentality" and "production software" mindset through understanding environment control principles.

## Table of Contents

### Foundational Concepts

1. **[Introduction & Context](01-introduction-context.md)**
   - Why this matters for modern development teams
   - Learning objectives
   - Target audiences (Data Scientists/ML Engineers & Backend Engineers)

2. **[Application vs Framework vs Library](02-application-framework-library.md)** ⭐ CORE SECTION
   - The fundamental distinction: Who controls the environment?
   - Environment control levels (No/Partial/Full)
   - Testing responsibility and dependency management implications
   - Visual architecture diagrams

3. **[Environment Architecture Layers](03-environment-architecture-layers.md)**
   - 6-layer environment stack (Hardware → OS → System → Runtime → Isolation → Dependencies)
   - Library/Framework/Application flexibility strategies per layer
   - Layer separation benefits and anti-patterns

### Practical Implementation

4. **[Python Environment Tools](04-python-environment-tools.md)**
   - Modern tool ecosystem with Java architectural comparisons
   - Tool specialization by environment layer
   - Performance considerations (uv 10-100x faster)
   - Enterprise scale limitations (conda beyond 100 developers)

5. **[Library Repository Structure](05-library-repository-structure.md)**
   - Modern Python library layout (src/ structure)
   - pyproject.toml design for maximum reusability
   - uv vs hatch for library development
   - Distribution and publishing workflows

6. **[Module & Subpackage Design](06-module-subpackage-design.md)**
   - Design principles and anti-patterns
   - Import management and namespace control
   - Advanced patterns (lazy loading, plugin systems)
   - Environment control implications

### Historical Context

7. **[Python Packaging Before the Modern Era](07-python-packaging-before-modern-era.md)**
   - The Wild West period (2000-2016)
   - Problems with setup.py approach
   - Tool proliferation and fragmentation
   - Why Java developers had it easier

8. **[Evolution of Modern Python Tools](08-python-evolution-modern-tools.md)**
   - The Renaissance period (2016-2024)
   - Performance revolution and tool consolidation
   - Modern best practices emergence
   - Future directions

### Reference

- **[Appendix: Python & Java Tools Reference](appendix-tools-reference.md)**
   - Side-by-side tool comparison
   - Quick reference for both ecosystems
   - Tool selection guide

## Navigation

Each section includes:
- **Environment Control Focus**: How the topic relates to the core theme
- **Learning Objectives**: What you'll understand after reading
- **Next/Previous Section**: Easy navigation between topics

## Key Takeaways

1. **Environment control** determines dependency management strategy
2. **Libraries** have no control → flexible version ranges
3. **Frameworks** have partial control → balance stability with extensibility
4. **Applications** have full control → pin exact versions
5. **Modern tools** (uv, Ruff) offer 10-100x performance improvements
6. **Right tool for right layer** prevents architectural problems

---

*This presentation helps development teams transition from "just writing Python scripts" to building production-grade Python software with proper dependency management and architectural understanding.*