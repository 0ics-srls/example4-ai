# example4.ai — Concept Document

## Vision

> In the world of AI, the best documentation format is working code.

Documentation was designed for humans reading text. AI agents don't read — they parse, search, and navigate. The most token-efficient, most accurate, and most verifiable format for an AI agent to learn how a library works is a small, buildable, testable project that uses that library.

example4.ai replaces traditional documentation with working code examples served via MCP and navigated semantically through LSAI.

## Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    AI Coding Agent                       │
│              (Claude, Copilot, Codex, ...)               │
└──────────────────────┬──────────────────────────────────┘
                       │ MCP (Model Context Protocol)
                       ▼
┌─────────────────────────────────────────────────────────┐
│                 example4.ai MCP Server                   │
│                                                         │
│  ┌─────────────┐  ┌──────────────┐  ┌───────────────┐  │
│  │  Manifest    │  │   Example    │  │    LSAI       │  │
│  │  Registry    │  │   Resolver   │  │   Bridge      │  │
│  └──────┬──────┘  └──────┬───────┘  └───────┬───────┘  │
│         │                │                   │          │
│         ▼                ▼                   ▼          │
│  ┌─────────────────────────────────────────────────┐    │
│  │              Example Repository Pool             │    │
│  │                                                  │    │
│  │  ┌──────────┐ ┌──────────┐ ┌──────────────────┐ │    │
│  │  │ library-a│ │ library-b│ │ library-c        │ │    │
│  │  │ manifest │ │ manifest │ │ manifest.json    │ │    │
│  │  │ examples/│ │ examples/│ │ examples/        │ │    │
│  │  └──────────┘ └──────────┘ └──────────────────┘ │    │
│  └─────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────┘
                       │
                       │ Language Server Protocol
                       ▼
┌─────────────────────────────────────────────────────────┐
│                    LSAI Instance                         │
│         (Semantic code analysis per language)            │
│                                                         │
│  search | outline | usages | hierarchy | callers | ...  │
└─────────────────────────────────────────────────────────┘
```

### Components

1. **Manifest Registry** — indexes all known library manifests, resolves library name + version to example repository
2. **Example Resolver** — clones/caches example repos, validates build/test status, serves example projects to LSAI
3. **LSAI Bridge** — opens example projects as LSAI workspaces, enabling semantic navigation (symbol search, type hierarchy, call graph, source extraction)
4. **Example Repository Pool** — git repositories containing buildable examples, each with a `manifest.json`

## The manifest.json Protocol

Every example repository MUST contain a `manifest.json` at its root. This file is the contract between example authors and the example4.ai server.

### Required Fields

| Field | Type | Description |
|---|---|---|
| `$schema` | string | Schema URL for validation |
| `library` | string | Library name (e.g., "Newtonsoft.Json") |
| `version` | string | Library version these examples target |
| `language` | string | Programming language (csharp, typescript, python, java, etc.) |
| `repository` | string | Git URL of this example repository |
| `examples` | array | List of example projects |
| `build` | string | Default build command |
| `test` | string | Default test command |

### Example Entry Fields

| Field | Type | Description |
|---|---|---|
| `name` | string | Unique identifier for this example |
| `description` | string | What this example demonstrates |
| `path` | string | Relative path to the example project |
| `tags` | string[] | Searchable tags for discovery |
| `complexity` | enum | `beginner`, `intermediate`, `advanced` |
| `build` | string | Build command (overrides root default) |
| `test` | string | Test command (overrides root default) |

### Design Principles

- **Flat, not nested** — each example is a self-contained project, not a subfolder of a monolith
- **Buildable in isolation** — `cd examples/basic && dotnet build` must work
- **Testable in isolation** — `cd examples/basic && dotnet test` must pass
- **Minimal dependencies** — only the target library + test framework, nothing else
- **Descriptive names** — example name = the concept it demonstrates

## Operational Flow

### Creation Flow

```
Library maintainer registers library
        │
        ▼
example4.ai analyzes library API surface
        │
        ▼
Generates example projects (one per concept)
        │
        ▼
Creates manifest.json
        │
        ▼
Pushes to example repository
        │
        ▼
CI validates: build ✓ test ✓
        │
        ▼
Example repository is LIVE
```

### Sync Flow

```
Library publishes new version (NuGet, npm, Maven, PyPI)
        │
        ▼
example4.ai detects version bump
        │
        ▼
Updates library reference in example projects
        │
        ▼
CI runs: build + test
        │
        ├── All pass → auto-merge, manifest version updated
        │
        └── Failures → flag for review, generate fix suggestions
```

### Query Flow (AI Agent)

```
Agent: "How do I use custom JsonConverters in Newtonsoft.Json?"
        │
        ▼
MCP → example4.ai → resolve("Newtonsoft.Json", tag: "converter")
        │
        ▼
Returns example: "custom-converters" in newtonsoft-json-examples repo
        │
        ▼
LSAI opens workspace → agent navigates semantically:
  - search("JsonConverter") → finds CustomDateConverter class
  - outline("CustomDateConverter.cs") → sees WriteJson, ReadJson methods
  - source("CustomDateConverter.WriteJson") → reads implementation
  - usages("CustomDateConverter") → sees how it's registered
        │
        ▼
Agent has working, verified code to reference. Zero prose consumed.
```

## Target Users

### AI Coding Agents
Claude Code, GitHub Copilot, OpenAI Codex, and any MCP-compatible agent. They get verified, navigable code examples instead of error-prone documentation parsing.

### Library Maintainers
Instead of writing and maintaining documentation pages, they maintain (or let example4.ai maintain) small example projects. CI guarantees correctness. No more "the docs say X but the code does Y".

### Enterprise Development Teams
Internal libraries and frameworks get the same treatment. Private example repos, private LSAI instances, same semantic navigation.

## Competitor Analysis

### DeepWiki
- **What it does**: Generates prose documentation from source code using AI
- **Weakness**: Generated text is not verifiable. It may describe behavior that doesn't exist or miss edge cases. Static snapshots that drift from reality. No CI, no tests, no compilation.
- **example4.ai advantage**: Every example compiles and passes tests. CI is the quality gate. If it's green, it's correct.

### Traditional Documentation (MDN, docs.microsoft.com, etc.)
- **What it does**: Hand-written prose with code snippets
- **Weakness**: Expensive to create and maintain. Code snippets are not compiled or tested. Goes stale. Token-expensive for AI agents to parse.
- **example4.ai advantage**: Zero prose. Code IS the documentation. Verified by CI. Token-efficient via LSAI semantic navigation.

### Context7, MCP documentation servers
- **What they do**: Serve library documentation as MCP resources
- **Weakness**: Still serving prose/markdown. Still unverified. Still token-expensive.
- **example4.ai advantage**: Serves code, not prose. Buildable, testable, semantically navigable.

## Token Savings

Based on LSAI benchmarks across 5 programming languages:

| Operation | Traditional docs | example4.ai + LSAI | Savings |
|---|---|---|---|
| Find how to use a class | Read 2-3 doc pages (~4000 tokens) | `search` + `outline` (~600 tokens) | **85%** |
| Understand method usage | Read API reference (~2000 tokens) | `usages` + `source` (~340 tokens) | **83%** |
| Learn inheritance pattern | Read architecture docs (~3000 tokens) | `hierarchy` (~500 tokens) | **83%** |
| Full feature understanding | Read tutorial (~6000 tokens) | `search` + `outline` + `source` (~1200 tokens) | **80%** |

**Estimated aggregate savings: 41-86%** depending on query complexity and language.

## Key Technologies

### LSAI (Language Server for AI)
Semantic code analysis server that wraps Language Server Protocol servers and exposes their capabilities via MCP. Supports C#, Python, TypeScript, JavaScript, and Java. Provides symbol search, type hierarchy, call graph, source extraction, and more — all optimized for AI token efficiency.

- Protocol: [github.com/LadislavSopko/lsai-protocol](https://github.com/LadislavSopko/lsai-protocol)

### MCP (Model Context Protocol)
Anthropic's open standard for connecting AI agents to external tools and data sources. example4.ai is an MCP server that any MCP-compatible agent can connect to.

- Specification: [modelcontextprotocol.io](https://modelcontextprotocol.io)

### manifest.json
The declarative protocol that defines example repositories. Simple, machine-readable, versionable. See [manifest-spec.json](manifest-spec.json) for the specification.

## Concept Origin

- **Date**: February 21, 2026
- **Author**: Ladislav Sopko
- **Company**: 0ics srl, Bologna, Italy
- **Context**: Developed as a natural extension of LSAI (Language Server for AI), recognizing that the most token-efficient way to teach an AI agent about a library is not documentation — it's working code that the agent can navigate semantically.
