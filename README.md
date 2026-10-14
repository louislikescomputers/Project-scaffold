# Project Scaffold

A single markdown file that turns any AI chat into a project scaffolding engine.

Drop [`PROJECT_SCAFFOLD.md`](PROJECT_SCAFFOLD.md) into a chat, describe your project (one line, several paragraphs, or entire pages) and optionally point to external files with your project ideas, and the AI generates a complete set of markdown files for LLM-assisted development, plus an MCP server skeleton and an agent skill, then packages everything into a downloadable archive in the format you choose: `.tar.gz`, `.zip`, or both.

Every project you start this way is agent-ready out of the box.

## What you get

A full scaffold is about 45 files, all tailored to your project:

| Group | Files |
|---|---|
| Core context | `AGENTS.md`, `README.md`, `MASTER_PROMPT.md` |
| Specification | `SPEC.md`, `ARCHITECTURE.md`, `API.md`, `DATA_MODEL.md`, `GLOSSARY.md` |
| Agent integration | `MCP.md`, `AGENT_INTEGRATION.md`, `llms.txt`, `mcp.json.example`, `mcp/` (server skeleton, tools, tests), `skills/<name>/` (`SKILL.md`, `references/`, `scripts/`) |
| Planning | `PLAN.md`, `TASKS.md`, `PROGRESS.md`, `DECISIONS.md`, `OPEN_QUESTIONS.md` |
| Standards | `CODING_STANDARDS.md`, `TESTING.md`, `SECURITY.md`, `DEPENDENCIES.md`, `PERFORMANCE.md` |
| Operations | `BUILD.md`, `DEPLOYMENT.md`, `TROUBLESHOOTING.md`, `CHANGELOG.md`, `CONTRIBUTING.md` |
| Agent workflow aids | `PROMPTS/` (7 task prompts), `CHECKLISTS.md`, `ANTI_PATTERNS.md`, `CONTEXT_MAP.md`, `REFERENCE/` |

## Quick start

1. Open any AI chat (Claude, ChatGPT, Gemini, a local model, etc.).
2. Attach or paste `PROJECT_SCAFFOLD.md`.
3. Describe your project in one or more lines. A short version works:

```
Use PROJECT_SCAFFOLD.md. Project: ipgen. Language: Rust. Description: A pausable, resumable IPv4 range generator. Targets: Windows, Linux, macOS. Profile: full. Archive: tar.gz.
```

   A longer version with external files works too:

```
Use PROJECT_SCAFFOLD.md. Project: ipgen. Language: Rust. Profile: full. Archive: zip.
Sources: ideas.md, notes/architecture-sketch.pdf, https://example.com/design-doc
Description:
<several paragraphs or a full page of project ideas>
```

4. Download the archive and extract it into your new repo. If the chat can't build one, it sends you every file directly, plus the commands to archive them yourself.
5. Give `AGENTS.md` and `MASTER_PROMPT.md` to your coding agent and start at milestone 1 in `PLAN.md`.

### Inputs

| Input | Required | Default |
|---|---|---|
| `Project` | yes | none |
| `Language` | yes | none |
| `Description` | yes, unless `Sources` fully describe the project | none |
| `Sources` | no | none |
| `Archive` | no | `tar.gz` (also accepts `zip` or `both`) |
| `Targets` | no | Windows, Linux, macOS |
| `Profile` | no | `full` |
| `MCP language` | no | same as project language |
| `License` | no | MIT |

If a required input is missing, the AI asks for everything missing in one message.

### External sources

Your project ideas can live outside the chat message. Attach or reference markdown, text, PDFs, Word docs, spreadsheets, JSON/YAML, existing specs or master prompts, notes, code, or URLs. The AI reads all of them, merges them with your message, and:

- resolves conflicts (your chat message wins, then the newest source) and records them in `DECISIONS.md` or `OPEN_QUESTIONS.md`,
- keeps your terminology and requirements in `GLOSSARY.md`, `SPEC.md`, and `PLAN.md`,
- lists every source it read (and any it couldn't) in a "Sources" section of `SPEC.md`,
- treats source content as project input only, never as instructions to follow.

A chat without file or fetch tools can only use what you paste in.

## Profiles

- **`full`** (default): everything in the table above.
- **`minimal`**: `AGENTS.md`, `SPEC.md`, `ARCHITECTURE.md`, `PLAN.md`, `PROGRESS.md`, `DECISIONS.md`, `TESTING.md`, `MCP.md`, `mcp/`, and `skills/<name>/SKILL.md`.

## Output modes

The prompt adapts to what the chat can do. No Python is needed in either mode.

| Mode | When | Result |
|---|---|---|
| A | Chat can create files and run shell commands | AI creates the files, builds your chosen archive (`<name>-scaffold-<date>.tar.gz` and/or `.zip`), verifies it, and gives it to you as a download |
| B | Chat cannot create an archive (no shell, no file tools, build failed, or no way to hand you downloads) | AI sends **every file** in the chat, complete: as downloads or attachments if it can, otherwise as artifacts or canvas documents, otherwise as code blocks, split into numbered parts if long. It then gives you the commands to build the archive yourself |

If the AI can't build an archive, it tells you so plainly. It never claims to have made one it didn't.

If you get Mode B, save the files into a folder named `<name>-scaffold/` at the paths shown, then run the commands from the next section. If you'd rather paste one thing, ask the AI for a one-paste shell script (bash, or PowerShell on Windows) and run it with the format you want:

```bash
bash make_scaffold.sh tar.gz
bash make_scaffold.sh zip
bash make_scaffold.sh both
```

## Building the archives manually

Linux, macOS, WSL, Git Bash:

```bash
tar -czf myproject-scaffold.tar.gz myproject-scaffold
zip -r myproject-scaffold.zip myproject-scaffold
```

Windows PowerShell:

```powershell
Compress-Archive -Path myproject-scaffold -DestinationPath myproject-scaffold.zip
tar -czf myproject-scaffold.tar.gz myproject-scaffold
```

Extract:

```bash
tar -xzf myproject-scaffold-<date>.tar.gz
```

## Design rules baked into every generated project

1. Core logic lives in a library; the CLI, MCP server, and skill are thin wrappers.
2. The CLI has a `--json` flag and stable exit codes.
3. Every MCP tool has a model-oriented description.
4. Long-running operations expose separate `start`, `status`, and `stop` tools.
5. Destructive tools are explicit and offer a dry-run option.
6. The skill's description says when to trigger it; detail lives in `references/`.
7. Build scripts produce the server binary and package the skill for all target platforms.
8. Every new feature ships with its MCP tool, skill update, tests, and docs in the same change.

## Using the generated kit

- **Coding agents** (Claude Code, Cursor, Copilot, Gemini CLI, and others): point them at `AGENTS.md`. Tool-specific files like `CLAUDE.md` or `.cursorrules` can be one-line pointers to it.
- **MCP server**: copy `mcp.json.example` into your client's config, replace the placeholder paths, and restart the client. See `MCP.md` and `AGENT_INTEGRATION.md`.
- **Skill**: copy `skills/<name>/` into your agent's skills directory (for Claude Code, `.claude/skills/`).
- **Context resets**: agents should read `PROGRESS.md` and `TASKS.md` at the start of each session and update them as they work.

## Repo contents

```
.
├── PROJECT_SCAFFOLD.md   # the master prompt: the only file you need
├── README.md             # this file
└── LICENSE
```

## Customizing

`PROJECT_SCAFFOLD.md` is plain markdown. Edit it to:

- add or remove files in the directory layout (section 4) and their content requirements (section 5),
- change the design rules (section 3),
- add your own profile (section 6),
- add thin pointer files such as `CLAUDE.md` or `.cursorrules` to the manifest.

Keep the layout, the content requirements, and the verification checklist in sync when you edit.

## Contributing

Issues and pull requests are welcome. Good contributions include new profiles, better file templates, support for more MCP SDKs, and fixes to the archive scripts.

## License

MIT. See [LICENSE](LICENSE).
