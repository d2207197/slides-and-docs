# Slides and Docs

This repository contains public slides and documents written in Markdown or other open text formats. Currently, Markdown is our primary choice as it maximizes compatibility with modern development tools like Cursor and Claude Code, enabling seamless integration with generative AI tools while maintaining flexibility.

## Philosophy

Using open text formats (currently primarily Markdown) enables:
- **Tool Freedom**: Compatible with any editor or AI-assisted development environment
- **AI Integration**: Optimized for generative AI tools and automated content workflows
- **Future-Proof**: Open formats that won't lock you into specific tools
- **Version Control**: Perfect for collaborative development with git
- **Format Flexibility**: Not limited to Markdown - any open text format that serves the content best

## Contents

### 📦 [Python Packaging & Production Best Practices](python-packaging-and-best-practices/)

A comprehensive guide covering modern Python packaging and production deployment strategies. This presentation bridges the gap between "script mentality" and "production software" mindset through understanding environment control principles.

**Key Topics:**
- Environment control as the fundamental driver of dependency management
- Application vs Framework vs Library architectural decisions
- Modern Python tooling (uv, ruff) with 10-100x performance improvements
- Docker deployment strategies and development workflows
- Historical context: How Python overcame its packaging challenges (2000-2025)

**Target Audience:** Data Scientists, ML Engineers, and Backend Engineers transitioning to production-grade Python development.

[→ Start Reading](python-packaging-and-best-practices/README.md)

---

## Documentation Principles

This repository follows "Documentation for Coding Agent Minimalism" - documents serve both human developers and coding agents:

1. **Clear Organization**
   - Explicit file names and titles
   - Well-organized hierarchy with effective navigation
   - This README serves as the primary index with brief descriptions

2. **Concise Length**
   - Documents should be focused and not overly long
   - Provide just enough examples
   - Details belong in code, not documentation

3. **Shared Principles (Developer-Agent Consensus)**
   - Define expected coding style, documentation style, and architecture
   - These principles guide both developers and agents
   - Must be actively updated as practices evolve
   - Serve as the living agreement between human intent and agent behavior

4. **Tool-Agnostic Minimalism**
   - Documentation is general, not tailored to specific coding agents
   - Agent-specific files (CLAUDE.md, cursor rules) remain minimal
   - Agent configs reference this index rather than duplicating content
   - Prefer text-based formats (Mermaid diagrams over images) for universal accessibility

## Contributing

All content is written in open text formats (primarily Markdown) and optimized for AI-assisted development workflows. Feel free to suggest improvements or corrections through issues and pull requests.

## License

See [LICENSE](LICENSE) for details.