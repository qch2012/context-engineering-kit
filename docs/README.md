---
icon: brain-circuit
---

# Context Engineering Kit

The Context Engineering Kit (CEK) is a curated marketplace of advanced context engineering techniques and patterns designed specifically for Claude Code. It provides prompts for extensibly tested and benchmarked techniques that enhance LLM output quality, speficially focusing on code genration, research and problem solving.

## Key Features

* **Simple to Use** - Easy to install and use without any dependencies. Contains automatically used skills and self-explanatory commands.
* **Token-Efficient** - Carefully crafted prompts and architecture, preferring commands over skills, to minimize populating context with unnecessary information.
* **Quality-Focused** - Each plugin is focused on meaningfully improving agent results in a specific area.
* **Granular** - Install only the plugins you need. Each plugin loads only its specific agents, commands, and skills. Each without overlap and redundant skills.
* **Scientifically proven** - Plugins are based on proven techniques and patterns that were tested by well-trusted benchmarks and studies, with exception to development workflows that based on popular projects and frameworks.

## IDEs and CLIs support

Currenty this project support only Claude Code CLI, but we plan to support other IDEs and CLIs in the future. For now you can simply copy paste prompt files in your projects. Alternatively, any support PRs are welcome.

## Getting Started

Start here to get up and running quickly:

* [Getting Started](getting-started.md) - Installation, setup, and your first plugin
* [User Guide](user-guide.md) - Common workflows and usage patterns
* [Core Concepts](concepts/) - Understanding context engineering principles

## Explore Plugins

Browse our specialized plugins organized by area of focus:

### Quality & Refinement

* [Reflexion](plugins/reflexion/) - Self-refinement loops (8-21% quality improvement)
* [Code Review](plugins/code-review/) - Multi-agent code review system
* [Kaizen](plugins/kaizen/) - Continuous improvement methodology

### Development Workflows

* [Spec-Driven Development](plugins/sdd/) - Feature specification to implementation
* [Test-Driven Development](plugins/tdd/) - TDD methodology and anti-patterns
* [Subagent-Driven Development](plugins/sadd/) - Task isolation with quality gates
* [Domain-Driven Development](plugins/ddd/) - Clean Architecture and SOLID principles

### Developer Tools

* [Git](plugins/git/) - Commit creation and PR management
* [Docs](plugins/docs/) - Documentation generation and updates
* [Tech Stack](plugins/tech-stack/) - Language and framework best practices

### Agents Implevements and Extensions

* [Customaize Agent](plugins/customaize-agent/) - Build your own commands and skills
* [MCP](plugins/mcp/) - Model Context Protocol server integration

## Contributing

We welcome contributions! See our [Contributing Guide](https://github.com/NeoLabHQ/context-engineering-kit/blob/main/CONTRIBUTING.md) for details.
