# Nearly Fully Automated Software Development Template

[日本語版はこちら](README-ja.md)

A project template that **nearly fully automates** the software development process using AI coding agent multi-agent capabilities. The user only does three things: describe the concept, make key decisions, and accept the deliverables.

## AI Platform Support

Default configuration targets **Claude Code** (Anthropic), but the framework is portable to other AI coding agents.

| Status | Platform |
|---|---|
| Ready to use | Claude Code |
| Porting guide available | OpenAI Codex CLI, Gemini CLI, Cursor, Windsurf, Cline, Roo Code, Aider |

- ~70% of files are portable (deliverables, code, process rules) — no changes needed
- ~15% require find-and-replace (vendor names, model names, directory paths)
- ~15% require format conversion (agent definition YAML frontmatter) — prompt body is reusable

**Tell your AI to read [`process-rules/porting-guide-ja.md`](process-rules/porting-guide-ja.md) and auto-convert.** If it can't handle that, it can't handle this framework.

## Language Support

Default language is English (`-en.md`). To switch:

1. Delete all files with the unwanted language suffix (e.g., `-ja.md`)
2. Have your AI translate the remaining `-en.md` files to your target language (e.g., `-vi.md`)

Files without a language suffix (`CLAUDE.md`, `.claude/agents/*.md`, etc.) do not need translation.

> Both English and Japanese are maintained in this repository. Consistency between languages is verified by AI agents before each release.

## Quick Start

### Step 1. Install

```bash
git clone https://github.com/your-username/claude-code-full-auto-dev.git my-project
cd my-project
```

### Step 2. Switch AI platform (skip if using Claude Code)

Have your AI read [`process-rules/porting-guide-ja.md`](process-rules/porting-guide-ja.md) and auto-convert.

### Step 3. Switch language (skip if using English)

Delete unwanted language files, then have your AI translate the rest.

### Step 4. Start building

Write your concept in `user-order.md`, then launch:

```
/full-auto-dev
```

The AI proposes a project configuration, conducts a structured interview, generates a specification, and proceeds through design, implementation, testing, and delivery — all automatically.

## Documentation

- [Process Rules](process-rules/full-auto-dev-process-rules-ja.md) — Phase definitions, agents, quality gates
- [Document Rules](process-rules/full-auto-dev-document-rules-ja.md) v0.0.0 — Naming, block structure, versioning
- [Porting Guide](process-rules/porting-guide-ja.md) — How to convert to other AI platforms
- [Spec Format & Design Rationale](essays/) — ANMS / ANPS / ANGS three-tier spec system

## License

See [LICENSE](LICENSE).
