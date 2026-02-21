# Prior Art Declaration — example4.ai

## Concept

**example4.ai** — An MCP server that replaces traditional software documentation with buildable, testable code examples navigated semantically by AI agents via LSAI.

## Authorship

- **Author**: Ladislav Sopko
- **Company**: 0ics srl, Bologna, Italy
- **Date of conception**: February 21, 2026
- **First documented**: This repository, initial commit

## Context

example4.ai was developed as a natural extension of the LSAI (Language Server for AI) project. During the development of LSAI — a semantic code analysis server that wraps Language Server Protocol servers for AI consumption via MCP — it became clear that the most token-efficient way to teach an AI agent about a software library is not documentation. It is working code.

LSAI already provides the infrastructure for AI agents to navigate codebases semantically: symbol search, type hierarchy, call graph analysis, source code extraction. The missing piece was a standardized way to create, maintain, and serve small example projects that demonstrate library usage — projects that an AI agent can open, explore, and learn from without ever reading a line of prose.

## Key Innovation

The `manifest.json` protocol as a standard for defining example repositories of software libraries, where:

1. Each library has a dedicated git repository containing small, buildable, testable example projects
2. A `manifest.json` at the repository root declares all examples with metadata (name, description, tags, complexity, build/test commands)
3. CI continuously validates that all examples build and pass tests
4. AI agents discover and navigate examples via MCP, using LSAI for semantic code analysis
5. When the library updates, examples are automatically updated and validated

This is the first system that combines:
- **MCP** (Model Context Protocol) for AI agent connectivity
- **Semantic code analysis** (via LSAI / Language Server Protocol) for intelligent navigation
- **Buildable, testable examples** as a complete replacement for traditional documentation
- **manifest.json** as a declarative protocol for example repository definition
- **CI-driven verification** as the quality gate (if it's green, it's correct)

## Differentiation from Existing Solutions

### vs. DeepWiki and AI-generated documentation
DeepWiki generates prose from source code. example4.ai serves working code that the AI agent navigates directly. Prose is unverifiable; code is compiled and tested.

### vs. Traditional documentation
Traditional docs are hand-written, go stale, and are token-expensive. example4.ai examples are CI-validated and token-efficient via semantic navigation.

### vs. Context7 and MCP documentation servers
These serve markdown/prose via MCP. example4.ai serves code via MCP + LSAI. The fundamental unit is a buildable project, not a text document.

### vs. Code snippet databases (StackOverflow, GitHub Gists)
Snippets are isolated, unversioned, and untested. example4.ai examples are full projects with dependency management, build scripts, and test suites.

## Related Work

- **LSAI (Language Server for AI)** — The semantic code analysis engine that powers example4.ai's navigation capabilities. Protocol: [github.com/LadislavSopko/lsai-protocol](https://github.com/LadislavSopko/lsai-protocol)
- **MCP (Model Context Protocol)** — Anthropic's open standard for AI tool integration. Specification: [modelcontextprotocol.io](https://modelcontextprotocol.io)

## Legal Notice

This document establishes prior art for the example4.ai concept, its manifest.json protocol, and the combination of technologies described above. The concept, its documentation, and the manifest protocol are released under CC BY-NC 4.0. Commercial use requires a separate license agreement.

Contact: ladislav.sopko@gmail.com
