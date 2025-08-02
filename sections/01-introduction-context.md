# Section 01: Introduction & Context

> Setting the stage for modern Python packaging and production best practices

## Context: Who This Is For and Why

Modern software teams often include diverse backgrounds transitioning to Python:

- **Data Scientists & ML Engineers**: Strong Python skills for analysis and modeling, but limited packaging and production deployment experience
- **Backend Engineers**: Deep software engineering practices from Java/JVM world, but new to Python's unique ecosystem
- **Common Challenge**: Moving from "script mentality" to building production-grade Python software

This presentation bridges these gaps through a fundamental insight: **environment control drives all Python packaging decisions**.

## Core Concept: Environment Control

The key to understanding Python packaging is recognizing how different project types control their execution environments:

- **Libraries** (Minimal Control) → Must use flexible version ranges to work anywhere
- **Frameworks** (Partial Control) → Balance stability with extensibility
- **Applications** (Full Control) → Can pin exact versions for reproducibility

This single principle explains why NumPy uses `>=1.20,<2.0` while your web app uses `==1.23.5`. Every packaging decision flows from this foundation.

## What You'll Learn

1. **Why** applications, frameworks, and libraries require different packaging strategies
2. **How** the 6-layer environment stack (Hardware → OS → System → Runtime → Isolation → Dependencies) works
3. **Which** modern tools to use for each layer and when (uv, Poetry, Hatch, pip-tools)
4. **When** to use flexible vs pinned dependencies based on your project type
5. **How** to structure repositories and modules for maintainability
6. **Why** Python packaging was broken for 15+ years and how modern tools fixed it

## How This Guide Differs

Unlike typical Python packaging tutorials, we:

1. **Explain the 6-layer architecture** - A unique perspective rarely found elsewhere
2. **Provide historical context** - Why Python was late and what we learned
3. **Start with "why" not "how"** - Architecture before tools
4. **Use Java comparisons** - Leverage existing knowledge
5. **Focus on modern best practices** - What actually works in 2024+

## Prerequisites

- Basic Python programming knowledge
- Familiarity with `pip install` (internals knowledge not required)
- Interest in building maintainable software

## Let's Begin

The journey starts with understanding the fundamental distinction between applications, frameworks, and libraries - because this distinction drives every other decision in Python packaging.

---

**Next Section**: [02-application-framework-library.md](02-application-framework-library.md) - The core architectural concepts
**Duration**: This section ~3 minutes