# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This repository is designed to prepare a presentation about Python packaging and production best practices for software engineers at Taboola's Taipei office. The content targets two main audiences:

- **Data Scientists/ML Engineers**: Strong Python skills but limited packaging knowledge
- **Backend Engineers**: Strong Java/software engineering practices but new to Python ecosystem

The goal is to bridge the gap between "script mentality" and "production software" mindset through a top-down breakdown of production-level Python packaging and library design.

## Content Structure

```
python-packaging-good-practices/
├── CLAUDE.md                           # This guidance file
├── python-packaging-presentation.md    # Main presentation (comprehensive, updated)
└── resources/
    ├── Modern Python Packaging Best Practices: A Comprehensive Guide for 2024-2025.md
    └── Production Python: Packaging & Library Design Best Practices.md
```

### File Descriptions

- **python-packaging-presentation.md** - Main presentation file containing comprehensive, integrated content for Taboola engineers. Ready for Slidev conversion.
- **resources/Modern Python Packaging Best Practices: A Comprehensive Guide for 2024-2025.md** - Source material covering lock files, Python version management, pyproject.toml, dependency layers, and AI-friendly documentation
- **resources/Production Python: Packaging & Library Design Best Practices.md** - Source material covering applications vs libraries, dependency management strategies, and common anti-patterns
- **CLAUDE.md** - This guidance file for Claude Code instances

## Key Concepts Covered

### Lock File Revolution (2024-2025)
- Complete dependency graph with cryptographic hashes
- Solves "works on my machine" syndrome permanently
- Two-file strategy: flexible constraints + exact pins
- Applications always commit lock files, libraries consider it

### Application vs Library Distinction
- Libraries use permissive dependency constraints for compatibility
- Applications pin exact versions for reproducibility
- Different distribution and packaging strategies apply
- Clear separation of concerns and anti-pattern avoidance

### Tool Specialization and Performance
- **uv**: 10-100x faster, Rust-based, integrated Python management
- **Ruff**: Unified linting/formatting replacing 5+ tools
- **Poetry**: Feature-rich development experience
- **Conda limitations**: Performance degradation beyond 100 developers

### Six-Layer Dependency Hierarchy
- System level → Python runtime → Virtual environments → Project config → Package managers → Lock files
- Understanding which tools control which layers and their interactions

### Modern Standards and Practices
- **pyproject.toml**: PEP 518/517/621 evolution, security benefits
- **src layout**: Modern standard for import safety and packaging integrity
- **AI-friendly documentation**: Future-proofing for coding assistants
- **Semantic versioning**: Flexible constraints with clear boundaries

### Production Considerations
- Common anti-patterns and their solutions
- Balancing flexibility with reproducibility
- Scale-dependent tool choices
- Enterprise environments and private PyPI
- Supply chain security with cryptographic verification

## Presentation Development Process

The intended workflow is:
1. **First Phase**: Develop comprehensive MDC (markdown) content with focus on top-down architectural breakdown
2. **Second Phase**: Convert MDC content to Slidev presentation format

## Content Development Guidelines

When working with this repository:
- **Primary Focus**: Top-down breakdown of production-level Python packaging layers and responsibilities
- **Visual Elements**: Include diagrams and tables using mermaid, plantuml, or ASCII art
- **Audience Consideration**: Bridge knowledge gaps between strong programming skills and packaging practices
- **Architectural Emphasis**: Clear separation of layers, factors, and component responsibilities
- **Production Mindset**: Transition from script-level thinking to production software design

## Target Deliverable

A comprehensive presentation that helps Taboola engineers understand:
- The lock file revolution and its impact on reproducible builds
- Six-layer dependency management hierarchy and tool responsibilities
- Modern tooling ecosystem with performance considerations
- Application vs library design patterns and anti-patterns
- Production-ready practices for 2024-2025 including security and scale

## Current Status

The main presentation file (`python-packaging-presentation.md`) has been comprehensively updated with:
- Lock file revolution concepts and implementation strategies
- Tool specialization insights (uv, Ruff, conda limitations)
- Six-layer dependency management architecture
- pyproject.toml standardization and PEP evolution details
- Source layout recommendations and AI-friendly documentation practices
- Common anti-patterns with practical solutions
- Scale-dependent recommendations for enterprise environments
- Implementation roadmap with specific phases for Taboola teams

Ready for conversion to Slidev format when needed.

## Recent Memories

- The file @python-packaging-presentation.md is the target of the first phase