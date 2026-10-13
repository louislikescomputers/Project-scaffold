# Project Scaffold

A single markdown file that turns any AI chat into a project scaffolding engine.

Drop [`PROJECT_SCAFFOLD.md`](PROJECT_SCAFFOLD.md) into a chat, describe your project in one line, and the AI generates a complete set of markdown files for LLM-assisted development, plus an MCP server skeleton and an agent skill, then packages everything into `.tar.gz` and `.zip` archives.

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
3. Send one line describing your project:

```
Use PROJECT_SCAFFOLD.md. Project: ipgen. Language: Rust. Description: A pausable, resumable IPv4 range generator. Targets: Windows, Linux, macOS. Profile: full.
```

4. Download the archive (or run the script the AI gives you) and extract it into your new repo.
5. Give `AGENTS.md` and `MASTER_PROMPT.md` to your coding agent and start at milestone 1 in `PLAN.md`.

### Inputs

| Input | Required | Default |
|---|---|---|
| `Project` | yes | none |
| `Language` | yes | none |
| `Description` | yes | none |
| `Targets` | no | Windows, Linux, macOS |
| `Profile` | no | `full` |
| `MCP language` | no | same as project language |
| `License` | no | MIT |

If a required input is missing, the AI asks for everything missing in one message.

## Profiles

- **`full`** (default): everything in the table above.
- **`minimal`**: `AGENTS.md`, `SPEC.md`, `ARCHITECTURE.md`, `PLAN.md`, `PROGRESS.md`, `DECISIONS.md`, `TESTING.md`, `MCP.md`, `mcp/`, and `skills/<name>/SKILL.md`.

## Output modes

The prompt adapts to what the chat can do:

| Mode | When | Result |
|---|---|---|
| A | Chat has code execution or file tools | AI creates the files and builds `<name>-scaffold-<date>.tar.gz` and `.zip` for you to download |
| B | No code execution | AI outputs one Python script (`make_scaffold.py`); run it to write all files and build both archives |
| C | Fallback | AI outputs each file as its own block, plus archive commands to run locally |

If you get Mode B, run:

```bash
python make_scaffold.py
```

If the script comes in several parts, run them in order; the final part builds the archives.

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
