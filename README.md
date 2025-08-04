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

1. **Clear Naming**: File names and titles must be explicit and descriptive
2. **Concise Length**: Documents should be focused and not overly long
3. **Clear Structure**: Provide just enough examples; details belong in code, not documentation
4. **High-Level Principles**: Include principles that both humans and agents can follow
5. **Effective Index**: This README serves as the index with brief descriptions of each document
6. **Minimal Agent Config**: CLAUDE.md and similar files remain minimal, referencing this index
7. **Tool Agnostic**: Documentation is general, not tailored to specific coding agents

## Contributing

All content is written in open text formats (primarily Markdown) and optimized for AI-assisted development workflows. Feel free to suggest improvements or corrections through issues and pull requests.

## License

See [LICENSE](LICENSE) for details.