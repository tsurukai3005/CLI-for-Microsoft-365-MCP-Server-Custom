# Guidelines

This document defines the project's top-level rules. Detailed rules are in `.claude/rules/`.

## Top-Level Rules

- To maximize efficiency, **if you need to execute multiple independent processes, invoke those tools concurrently, not sequentially**.
- **You must think exclusively in English**. However, you are required to **respond in Japanese**.
- To understand how to use a library, **always use the Context7 MCP** to retrieve the latest information.
- For temporary notes for design, create a markdown in `.tmp` and save it. Follow the `.tmp/` file naming conventions in [rules/core.md](rules/core.md).
- **After using Write or Edit tools, ALWAYS verify the actual file contents using the Read tool**, regardless of what the system-reminder says.
- Please respond critically and without pandering to my opinions, but please don't be forceful in your criticism.

## Project Overview

This is a **CLI for Microsoft 365 MCP Server** — a Model Context Protocol server that allows AI assistants to manage Microsoft 365 tenants using natural language. Built with TypeScript, Node.js, and the `@modelcontextprotocol/sdk`.

## Tech Stack

- **Language**: TypeScript (strict mode)
- **Runtime**: Node.js >= 20.0.0
- **Module System**: ESM (`"type": "module"`, NodeNext resolution)
- **Key Dependencies**: `@modelcontextprotocol/sdk`, `fuse.js`
- **Build**: `tsc` (TypeScript compiler)
- **Entry Point**: `src/index.ts` → `dist/index.js`

## Rules Index

| File | Contents |
|------|----------|
| [rules/core.md](rules/core.md) | `.tmp/` file conventions, global rule overrides |
| [rules/development.md](rules/development.md) | Core principles, programming rules, unified planning |
| [rules/agents.md](rules/agents.md) | Parallel agent execution, agent communication log |
| [rules/commands.md](rules/commands.md) | Custom commands reference |
