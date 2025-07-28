# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This repository is designed to prepare a presentation about Python packaging and production best practices for software engineers at Taboola's Taipei office. The content targets two main audiences:

- **Data Scientists/ML Engineers**: Strong Python skills but limited packaging knowledge
- **Backend Engineers**: Strong Java/software engineering practices but new to Python ecosystem

**Core Theme**: The presentation focuses on **environment control as the fundamental driver of Python dependency management**. The goal is to help engineers understand how different Python tool types (libraries, frameworks, applications) control their execution environments, and how this determines dependency management strategies.

The approach bridges the gap between "script mentality" and "production software" mindset through understanding who controls what in the Python ecosystem stack.

## Content Structure

```
python-packaging-good-practices/
├── CLAUDE.md                           # This guidance file
├── python-packaging-presentation.md    # Original comprehensive presentation
├── sections/                           # Split presentation sections
│   ├── README.md                      # Table of contents for sections
│   ├── 01-introduction-context.md     # Why this matters, learning objectives
│   ├── 02-application-framework-library.md  # Core environment control concepts
│   ├── 03-production-software-architecture.md
│   ├── 04-dependency-management.md
│   ├── 05-development-practices.md
│   ├── 06-build-systems.md
│   ├── 07-module-subpackage-design.md
│   ├── 08-environment-management.md
│   ├── 09-deployment-distribution.md
│   ├── 10-skill-levels.md
│   ├── 11-practical-guidelines.md
│   └── 12-conclusion.md
└── resources/
    ├── Modern Python Packaging Best Practices: A Comprehensive Guide for 2024-2025.md
    └── Production Python: Packaging & Library Design Best Practices.md
```

### File Descriptions

- **sections/02-application-framework-library.md** - **CORE SECTION**: Establishes environment control as the fundamental concept driving dependency management strategies. This is the foundation for all subsequent content.
- **sections/01-introduction-context.md** - Updated to emphasize environment control theme and learning objectives
- **sections/README.md** - Navigation guide for all presentation sections
- **resources/** - Source materials that informed the presentation development
- **CLAUDE.md** - This guidance file for Claude Code instances

## Key Concepts Covered

### Environment Control (CORE CONCEPT)
- **No Control (Libraries)**: Must work in shared environments, use flexible version ranges
- **Partial Control (Frameworks)**: Manage plugin ecosystems, balance stability with extensibility  
- **Full Control (Applications)**: Own their environments, can pin exact versions for reproducibility
- Environment control level directly determines dependency management flexibility needs

### Application vs Framework vs Library Distinction (FOUNDATION)
- **Control Flow**: Libraries you call → Frameworks call you (IoC) → Applications run independently
- **Testing Responsibility**: Libraries (integration tests) → Frameworks (plugin compatibility) → Applications (end-to-end tests)
- **Reusability Requirements**: Libraries & Frameworks maximize reuse → Applications maximize specific functionality
- **Visual Architecture**: Mermaid diagrams showing containment and control relationships

### Dependency Management Strategy (PRACTICAL APPLICATION)
- Environment control drives version strategy: `numpy>=1.20,<2.0` vs `pandas==2.1.3`
- Testing responsibility explains why applications need strict version control
- This foundation enables understanding of the complete Python environment stack

### Modern Python Environment Stack (ROADMAP TO SUBSEQUENT CONTENT)
- System level → Python runtime → Virtual environments → Project config → Package managers → Lock files
- Understanding which tools control which layers and their interactions
- How environment control principles apply at each stack level

### Tool Specialization and Performance
- **uv**: 10-100x faster, integrated Python management
- **Ruff**: Unified linting/formatting
- **Conda limitations**: Performance degradation beyond 100 developers
- Scale-dependent tool choices for enterprise environments

## Presentation Development Process

The intended workflow is:
1. **First Phase**: Develop comprehensive MDC (markdown) content with focus on top-down architectural breakdown
2. **Second Phase**: Convert MDC content to Slidev presentation format

## Content Development Guidelines

When working with this repository:
- **Core Theme**: Environment control as the fundamental driver of dependency management decisions
- **Teaching Approach**: Shallow-to-deep progression suitable for presentation format
- **Visual Elements**: Mermaid diagrams showing control relationships, concise tables over lengthy explanations
- **Audience Consideration**: Bridge knowledge gaps between strong programming skills and packaging practices
- **Practical Focus**: Connect abstract concepts to real dependency management decisions
- **Production Mindset**: Transition from "script mentality" to understanding production software architecture
- **Foundation Building**: Section 02 (application-framework-library) establishes core concepts for all subsequent content

## Target Deliverable

A comprehensive presentation that helps Taboola engineers understand:
- **Environment Control Fundamentals**: How libraries, frameworks, and applications control their execution environments differently
- **Dependency Strategy Logic**: Why environment control level determines version constraint flexibility (flexible ranges vs exact pins)
- **Testing Responsibility Architecture**: How different testing requirements drive dependency management decisions
- **Python Environment Stack**: Complete System → Python → Libraries → Dependencies management hierarchy
- **Production Tooling**: Modern ecosystem with performance considerations (uv, Ruff, conda limitations)
- **Enterprise Application**: Scale-dependent tool choices and practices for production environments

## Current Status

**Phase 1 Complete**: Content has been split into focused sections suitable for presentation format.

**Core Foundation Established** in `sections/02-application-framework-library.md`:
- Environment control as the fundamental concept driving dependency management
- Visual mermaid diagram showing application/framework/library containment relationships  
- Comprehensive table linking environment control → dependency strategy → testing responsibility
- Clear progression from shallow concepts to deep implementation details
- Foundation established for subsequent sections on Python environment management

**Key Sections Updated**:
- `sections/01-introduction-context.md`: Updated learning objectives to emphasize environment control theme
- `sections/README.md`: Navigation guide for all presentation sections
- `CLAUDE.md`: Updated to reflect core themes and presentation focus

**Ready for**: 
- Development of subsequent sections building on environment control foundation
- Conversion to Slidev presentation format when needed
- Integration with Python environment stack management content

## Recent Memories and Key Insights

- **sections/02-application-framework-library.md** is the foundational section that establishes environment control concepts
- Core insight: Environment control level (No/Partial/Full) determines dependency management flexibility needs
- Teaching approach: Shallow-to-deep suitable for presentations, avoiding overwhelming detail
- Visual emphasis: Mermaid diagrams and concise tables preferred over lengthy explanations
- Connection established: This foundation enables understanding of complete Python environment stack management