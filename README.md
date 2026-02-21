# example4.ai

**Real code examples for AI agents. Docs are dead.**

## What is example4.ai?

example4.ai is an MCP (Model Context Protocol) server that serves real, buildable, testable code examples for software libraries. Instead of reading documentation, AI agents connect via MCP and navigate working example projects semantically through [LSAI (Language Server for AI)](https://github.com/LadislavSopko/lsai-protocol).

Each library has a small git repository containing example projects defined by a `manifest.json`. Every example compiles, every test passes. CI is the quality gate. Zero prose to maintain.

## The Problem

Traditional documentation is broken for AI agents:

- **Token-expensive** — generating, updating, and reading prose burns tokens at every step
- **Not verifiable** — no way to know if a code snippet actually works
- **Goes stale** — library updates break examples silently, nobody notices until someone copies broken code
- **Not executable** — you can read it but you can't run it

AI-generated documentation sites (DeepWiki and similar) add another layer of problems:

- Synthesized prose that may or may not reflect reality
- Static snapshots that drift from the actual library
- No compilation, no tests, no CI — zero verification
- Text-based search instead of semantic code navigation

## The Solution

Replace documentation with working code.

Every library gets a small git repository with buildable, testable example projects. A `manifest.json` defines what examples exist, how to build and test them, and what concepts they demonstrate. AI agents navigate these examples semantically via LSAI — searching for symbols, exploring type hierarchies, reading implementations — exactly like a developer would explore a codebase.

If it compiles and the tests pass, the example is correct. If the library updates and an example breaks, CI fails immediately. Fix the example, push, done. The examples are always in sync with the library.

## How It Works

```
Library update → Example breaks → CI fails → Fix pushed → Examples in sync
                                                    ↑
AI Agent → MCP → example4.ai server → LSAI → Semantic navigation of example code
```

1. **Library maintainers** (or example4.ai automation) create small example repos with `manifest.json`
2. **CI runs continuously** — build + test on every push and on library version bumps
3. **AI agents connect via MCP** and discover available examples for any library
4. **LSAI provides semantic navigation** — search symbols, explore hierarchies, read source, understand patterns
5. **Token savings of 41-86%** compared to navigating traditional documentation

## example4.ai vs Alternatives

| Feature | example4.ai | DeepWiki & similar | Traditional docs |
|---|---|---|---|
| Content type | Compilable code | Synthesized prose | Hand-written prose |
| Verifiable | CI: build + test | None | None |
| Freshness | Push-triggered sync | Static snapshot | Manual updates |
| Navigation | LSAI semantic analysis | Text search / grep | Text search |
| Token cost | Low (structured data) | High (verbose prose) | High (verbose prose) |
| Accuracy | Guaranteed (if CI green) | Probabilistic | Decays over time |

## The Manifest Protocol

Every example repository contains a `manifest.json` that describes the library, its examples, and how to build/test them. See [manifest-spec.json](manifest-spec.json) for the full specification.

```json
{
  "library": "Newtonsoft.Json",
  "version": "13.0.3",
  "language": "csharp",
  "examples": [
    {
      "name": "basic-serialization",
      "description": "Serialize and deserialize POCO objects",
      "path": "./examples/basic-serialization",
      "tags": ["serialization", "deserialization"],
      "complexity": "beginner",
      "build": "dotnet build",
      "test": "dotnet test"
    }
  ]
}
```

## SaaS Model

example4.ai offers automated generation and maintenance of example repositories:

- **Library maintainers** register their library — example4.ai generates and maintains example repos automatically
- **Push-triggered sync** — when the library publishes a new version, examples are updated and CI validates them
- **Zero maintenance** — the examples stay current without human intervention
- **Enterprise support** — private example repos for internal libraries and frameworks

## Technology Stack

- **[LSAI](https://github.com/LadislavSopko/lsai-protocol)** — Language Server for AI, semantic code navigation via MCP
- **[MCP](https://modelcontextprotocol.io)** — Model Context Protocol, the standard for AI tool integration
- **manifest.json** — declarative protocol defining example repositories

## Concept Date

February 21, 2026

## Authors

**Ladislav Sopko** — [0ics srl](https://0ics.it), Bologna, Italy

## License

[CC BY-NC 4.0](LICENSE) — Creative Commons Attribution-NonCommercial 4.0 International

Commercial licensing available. Contact: ladislav.sopko@gmail.com
