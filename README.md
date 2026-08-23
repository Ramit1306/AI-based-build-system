# AI Build System

**Intelligent, Hermetic, Graph‑Based Build System with LLM Integration**  
*Inspired by the best of Bazel and CMake – but with native YAML/TOML configuration, direct execution, and AI‑powered automation using Qwen*

AI Build System is a next‑generation build tool that provides a **structured, reproducible, and intelligent** approach to building software. Unlike traditional meta‑build systems (CMake, Make, Ninja), it **executes build actions directly**, uses a **precise dependency graph** for parallelism and incrementality, and enforces **hermetic sandboxing** for truly reproducible builds.  

Where it truly shines is its **built‑in LLM integration**: using open‑source **Qwen** models from HuggingFace, it can:
- **Auto‑generate** build definitions from source code.
- **Suggest optimizations** for build rules.
- **Explain build errors** and dependency issues.
- **Automate** repetitive tasks (dependency updates, version bumps, etc.).

The system is designed to be **cross‑platform** (Linux, macOS, Windows), **fast**, and **extensible** – perfect for monorepos, polyglot projects, and teams that demand reliability and developer productivity.

---

## Table of Contents

- [Key Features](#key-features)
- [Architecture Overview](#architecture-overview)
- [Core Differentiators](#core-differentiators)
- [Technology Stack](#technology-stack)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
- [Usage](#usage)
- [LLM Integration](#llm-integration)
- [Evaluation & Benchmarking](#evaluation--benchmarking)
- [Contributing](#contributing)
- [License](#license)

---

## Key Features

- **Graph‑Based Execution** – Build actions are modelled as nodes in a directed acyclic graph (DAG), enabling precise dependency resolution, parallelism, and incremental builds.
- **Direct Execution** – No intermediate generation of Makefiles or Ninja files. The system invokes compilers, linkers, test runners, and other tools directly – saving time and reducing complexity.
- **Hermetic Sandboxing** – Each build action runs in an isolated environment (namespaces on Linux, sandbox on macOS, Job Objects on Windows) with controlled access to files, network, and environment variables. This guarantees **reproducible builds** that are immune to host‑system contamination.
- **Flexible Configuration** – Build definitions are written in **YAML** or **TOML** files (your choice), providing a clean, declarative syntax. No custom scripting language – just data.
- **Powerful Caching** – Content‑addressable storage (CAS) for artifacts and action‑result caching at the fine‑grained level, supporting both local and **remote caches** (via HTTP/gRPC) for team‑wide sharing.
- **Remote Execution** – Built‑in gRPC‑based remote execution protocol, allowing you to offload builds to a cluster of workers (like Bazel's RBE). Supports both on‑prem and cloud deployments.
- **LLM‑Driven Assistance** – Integrated Qwen LLM can:
  - **Generate** build definitions (`build.yaml` / `build.toml`) from source trees.
  - **Suggest** performance improvements and rule refactoring.
  - **Explain** build failures and offer fix suggestions.
  - **Automate** dependency management and version updates.
- **Extensible Rules** – Comes with built‑in rules for C/C++, Python, Rust, Java, Go, Shell, and filegroups. Users can define **custom rules** via a plugin system.
- **Cross‑Platform** – Runs on Linux, macOS, and Windows with consistent behavior.
- **CLI with Full Control** – Familiar subcommands: `build`, `test`, `run`, `clean`, `query`, `llm‑assist`, and more.

---

## Architecture Overview

The system is structured as a **modular, layered** architecture:

1. **Configuration Layer** – Parses YAML/TOML build files into an internal AST, resolves target dependencies, and builds the workspace model.
2. **Graph Layer** – Constructs a DAG of actions (compile, link, test, etc.) from the resolved targets, performing validation and cycle detection.
3. **Execution Layer** – Schedules actions for parallel execution, respecting dependencies and resource limits. Each action is spawned inside a **sandbox**.
4. **Sandboxing Layer** – Provides isolated execution environments, capturing inputs/outputs for caching and reproducibility.
5. **Cache Layer** – Content‑addressable store for artifacts and action results, with local and remote backends.
6. **Remote Execution Layer** – Optional gRPC server/client for distributing build actions across a cluster.
7. **LLM Layer** – Qwen model integration, enabling intelligent assistance, auto‑generation, and explanation.
8. **CLI & Integration** – Command‑line interface and optional LSP support for build files.

All components are designed to be **replaceable and extensible**, allowing users to swap in different caching backends, sandbox implementations, or LLM models.

---

## Core Differentiators

| Feature | AI Build System | CMake | Bazel |
|---------|----------------|-------|-------|
| **Execution Model** | Direct, no intermediate files | Meta‑build (generates Make/Ninja) | Direct (Bazel itself drives compilers) |
| **Config Language** | YAML / TOML (declarative) | Imperative CMake script | Starlark (restricted Python) |
| **Hermeticity** | Built‑in sandboxing, full isolation | Host‑dependent, implicit dependencies | Full sandboxing, explicit deps |
| **Caching** | Fine‑grained CAS + action cache, local/remote | External (ccache, Ninja) | Built‑in CAS + action cache, local/remote |
| **Remote Execution** | Native gRPC support | Not supported | Built‑in RBE |
| **LLM Integration** | Native Qwen integration for auto‑generation, suggestion, explanation | None | None |
| **Cross‑Platform** | Windows, macOS, Linux | All | All |
| **Learning Curve** | Low (YAML/TOML) | Medium‑High | High (Starlark) |
| **Scalability** | Monorepo‑ready, parallel & distributed | Limited to local parallelism | Monorepo‑optimized |

---

## Technology Stack

| Component           | Technology / Language           |
|---------------------|---------------------------------|
| **Core Engine**     | Rust (or Python optional)       |
| **Configuration**   | Serde (YAML/TOML parsers)       |
| **Sandboxing**      | Linux namespaces, macOS sandbox, Windows Job Objects |
| **Caching**         | Content‑addressable store (CAS) + action cache |
| **Remote Execution**| gRPC (with Protobuf)            |
| **LLM**             | Qwen (HuggingFace) – local or API |
| **CLI**             | Clap (Rust) / Click (Python)    |
| **Testing**         | Built‑in test runner (supports multiple frameworks) |
| **Logging**         | Structured logging (JSON / plain) |
| **Observability**   | Prometheus metrics, Grafana dashboards |