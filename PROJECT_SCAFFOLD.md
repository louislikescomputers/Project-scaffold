# PROJECT_SCAFFOLD.md

> **Purpose:** Open or paste this file into any AI chat. The AI will generate a complete set of markdown files for LLM-assisted software development (including an MCP server and an agent skill so the project works with AI agents out of the box) and package everything into a `.tar.gz` and a `.zip` archive.

---

## 0. How to use

1. Paste or attach this file in a new chat.
2. Add one line describing the project, for example:
   `Project: ipgen. Language: Rust. A pausable/resumable IPv4 range generator. Targets: Windows, Linux, macOS.`
3. The AI generates every file below, tailored to your project, and bundles them into archives.

Quick-start line to send with this file:

```
Use PROJECT_SCAFFOLD.md. Project: <name>. Language: <lang>. Description: <one or two sentences>. Targets: <platforms>. Profile: full.
```

---

## 1. Instructions to the AI (read first, follow exactly)

You are a project scaffolding engine. Your job is to produce a complete, project-specific documentation and agent-integration kit, then archive it.

### 1.1 Inputs

Gather these from the user's message. If any required input is missing, ask for all missing items in a **single** message, once. Otherwise proceed without asking.

| Input | Required | Default if omitted |
|---|---|---|
| `PROJECT_NAME` | yes | none (ask) |
| `LANGUAGE` / stack | yes | none (ask) |
| `DESCRIPTION` (1-3 sentences) | yes | none (ask) |
| `TARGET_PLATFORMS` | no | Windows, Linux, macOS |
| `PROFILE` | no | `full` (`minimal` = only the Minimum Viable Set in section 6) |
| `MCP_LANGUAGE` | no | Same as `LANGUAGE`; use TypeScript or Python if the main language has no mature MCP SDK |
| `LICENSE` | no | MIT |

State any assumptions you made in one short line at the end of your reply.

### 1.2 Output mode (pick the first that applies)

**Mode A: you can run code or create files** (code execution, file tools, or a sandbox):
1. Create the directory tree from section 4 under a folder named `<PROJECT_NAME>-scaffold/`.
2. Write every file with real, project-specific content (see section 2).
3. Build both archives (section 7), verify them, and present the files and archives to the user.

**Mode B: no code execution, but long outputs are allowed:**
Output a single self-extracting script (section 8) that writes every file and builds both archives when the user runs it. Provide the script in one code block. If it would exceed your output limit, split it into numbered parts (`part1.py`, `part2.py`, ...) where each part appends to the same manifest, plus a final `build_archive.py`. Tell the user the exact commands to run.

**Mode C: fallback:**
Output each file as its own fenced block with its path as a heading, then give the archive commands from section 7 for the user to run locally.

Never claim you created an archive unless you actually did in Mode A.

---

## 2. Generation rules

1. **Project-specific, not generic.** Every file must reflect `PROJECT_NAME`, `LANGUAGE`, and `DESCRIPTION`. No lorem ipsum, no `TODO: fill in` for things you can reasonably infer. Where something is genuinely unknown, put it in `OPEN_QUESTIONS.md`.
2. **Concise and scannable.** Prefer short sections, tables, and checklists. Agents read these every session, so avoid padding. Target under ~150 lines per file except `SPEC.md`, `ARCHITECTURE.md`, and `MCP.md`.
3. **Single source of truth.** Do not duplicate content across files. Link to the owning file instead (`See [ARCHITECTURE.md](ARCHITECTURE.md)`).
4. **Consistent naming.** Use the terms defined in `GLOSSARY.md` everywhere.
5. **Real commands.** Build, test, and run commands must be correct for `LANGUAGE` and `TARGET_PLATFORMS`.
6. **Dates and versions.** Use today's date in `PROGRESS.md` and `CHANGELOG.md`. Start versioning at `0.1.0`.
7. **Agent-ready by default.** Every project ships with an MCP server and a skill (section 5). Do not skip them unless `PROFILE` is `minimal`.
8. **Security first.** No secrets in any file. `SECURITY.md` must list what the MCP server may and may not expose.

---

## 3. Design rules (write these into `AGENTS.md` and `SPEC.md`)

1. Core logic lives in a library. The CLI, MCP server, and skill are thin wrappers over it, so they never drift apart.
2. The CLI has a `--json` flag and stable exit codes, so agents without MCP can still drive it.
3. Every MCP tool has a description written for a model: what it does, when to use it, and what it returns.
4. Long-running operations expose `start`, `status`, and `stop` as separate tools rather than one blocking call.
5. Destructive tools are explicit and offer a dry-run option where possible.
6. The skill's description says when to trigger it. Its body stays short, with detail pushed into `references/`.
7. Build scripts produce the server binary and package the skill for all target platforms.
8. Any new feature ships with its MCP tool, skill update, tests, and docs in the same change.

---

## 4. Directory layout to generate

```
<PROJECT_NAME>-scaffold/
├── AGENTS.md
├── README.md
├── MASTER_PROMPT.md
├── SPEC.md
├── ARCHITECTURE.md
├── API.md
├── DATA_MODEL.md
├── GLOSSARY.md
├── MCP.md
├── AGENT_INTEGRATION.md
├── llms.txt
├── mcp.json.example
├── PLAN.md
├── TASKS.md
├── PROGRESS.md
├── DECISIONS.md
├── OPEN_QUESTIONS.md
├── CODING_STANDARDS.md
├── TESTING.md
├── SECURITY.md
├── DEPENDENCIES.md
├── PERFORMANCE.md
├── BUILD.md
├── DEPLOYMENT.md
├── TROUBLESHOOTING.md
├── CHANGELOG.md
├── CONTRIBUTING.md
├── CHECKLISTS.md
├── ANTI_PATTERNS.md
├── CONTEXT_MAP.md
├── PROMPTS/
│   ├── implement-feature.md
│   ├── fix-bug.md
│   ├── refactor.md
│   ├── code-review.md
│   ├── write-tests.md
│   ├── security-audit.md
│   └── add-mcp-tool.md
├── REFERENCE/
│   ├── mcp-spec-notes.md
│   └── skill-format-notes.md
├── mcp/
│   ├── README.md
│   ├── server.<ext>
│   ├── tools/            (one file per tool)
│   ├── resources/        (optional)
│   ├── prompts/          (optional)
│   └── tests/
└── skills/
    └── <PROJECT_NAME>/
        ├── SKILL.md
        ├── references/
        └── scripts/
```

`LESSONS_LEARNED.md` may be merged into `ANTI_PATTERNS.md`. `ROADMAP.md` may be merged into `PLAN.md`. `STATUS.md` is the same as `PROGRESS.md`.

---

## 5. File-by-file content requirements

### Core context (read every session)

**`AGENTS.md`**: the universal entry point for coding agents. Include: one-paragraph project summary; exact build, test, lint, and run commands; repo layout; conventions; boundaries and "never do" rules; the design rules from section 3; and a "read these first" list pointing to `SPEC.md`, `ARCHITECTURE.md`, `PROGRESS.md`. Also state that `CLAUDE.md`, `GEMINI.md`, `.cursorrules`, and `.github/copilot-instructions.md` should be thin pointers to this file.

**`README.md`**: what the project is, install and run instructions, a usage example, and a short "Using with AI agents" section linking to `MCP.md` and the skill.

**`MASTER_PROMPT.md`**: the standing brief for a coding agent: goals, constraints, definition of done, how to work (plan, implement, test, update `PROGRESS.md`), when to stop and ask (`OPEN_QUESTIONS.md`), and the instruction to keep the MCP server and skill in sync with every feature.

### Specification

**`SPEC.md`**: functional requirements (numbered, testable), inputs and outputs, edge cases, non-goals, acceptance criteria, and the requirement that the project ships with an MCP server and a skill.

**`ARCHITECTURE.md`**: components, data flow, module boundaries, dependency direction. Include a text diagram showing the core library with CLI, MCP server, and skill as wrappers.

**`API.md`**: public function signatures, CLI commands and flags, schemas, error contracts, exit codes.

**`DATA_MODEL.md`**: schemas, file formats, persisted state (including pause/resume state if relevant), versioning.

**`GLOSSARY.md`**: domain terms with one-line definitions and the canonical name to use.

### Agent integration

**`MCP.md`**: documents every tool: name, purpose, parameters (typed), example call and response, error cases. Also: transports (stdio, optional streamable HTTP), how to run and debug (MCP Inspector or equivalent), and versioning rules for tool schemas.

**`mcp/`**: a working server skeleton in `MCP_LANGUAGE`:
- `server.<ext>`: entry point, stdio by default, optional HTTP transport behind a flag.
- `tools/`: one file per tool with a typed input schema, a model-oriented description, and structured output. Generate at least a `status`/`info` tool and one tool per primary CLI command. Long-running work gets `start_*`, `get_*_status`, `stop_*` tools.
- `tests/`: contract tests that call each tool and validate its schema and error behavior.
- Suggested SDKs: Python `mcp` (FastMCP), TypeScript `@modelcontextprotocol/sdk`, Rust `rmcp`. Check current docs if you can search.

**`mcp.json.example`**: ready-to-paste client config for Claude Desktop, Claude Code, Cursor, and similar clients. Use placeholders for absolute paths.

**`skills/<PROJECT_NAME>/SKILL.md`**: the agent skill.
- YAML frontmatter with `name` and a `description` that says *what* the skill does and *when* to trigger it.
- Short body: when to use the project, which MCP tools or CLI commands to call and in what order, common pitfalls, verification steps.
- `references/`: deeper docs loaded only when needed (flag reference, file formats, troubleshooting).
- `scripts/`: helper scripts the agent can run directly.

**`AGENT_INTEGRATION.md`**: explains MCP server vs skill vs CLI, when to use each, how to install each, and how to verify an agent can use the project end to end.

**`llms.txt`**: short index of the docs (title, one-line summary, relative link per entry).

### Planning and tracking

**`PLAN.md`**: phased milestones in build order. Must include the core library, CLI, MCP server, skill, and packaging as explicit milestones.

**`TASKS.md`**: checklist with status markers (`[ ]`, `[~]`, `[x]`) and owners. Agents update it as they work.

**`PROGRESS.md`**: running log: done, broken, next. Survives context resets. Start with an initial entry.

**`DECISIONS.md`**: ADR-style entries (context, decision, consequences, status). Seed with the major stack choices.

**`OPEN_QUESTIONS.md`**: unresolved items the agent should ask about rather than guess.

### Standards

**`CODING_STANDARDS.md`**: language idioms, naming, error handling, logging, file layout.
**`TESTING.md`**: strategy, frameworks, coverage expectations, how to run, including MCP contract tests.
**`SECURITY.md`**: threat model, secrets handling, input validation, banned patterns, what the MCP server may expose or execute, and path and command-injection rules.
**`DEPENDENCIES.md`**: approved libraries with versions, what to avoid and why.
**`PERFORMANCE.md`**: budgets and benchmarks (mark "not applicable" if irrelevant).

### Operations

**`BUILD.md`**: environment setup, cross-platform build steps, building the MCP server binary, packaging the skill.
**`DEPLOYMENT.md`**: release process, CI/CD, artifact publishing.
**`TROUBLESHOOTING.md`**: known failure modes and fixes, including MCP connection issues.
**`CHANGELOG.md`**: Keep a Changelog format, including tool and schema changes.
**`CONTRIBUTING.md`**: workflow, branching, PR rules, the "feature ships with MCP tool and skill update" rule.

### Agent workflow aids

**`PROMPTS/`**: reusable task prompts. Each has: goal, inputs, steps, constraints, definition of done. Files: `implement-feature.md`, `fix-bug.md`, `refactor.md`, `code-review.md`, `write-tests.md`, `security-audit.md`, `add-mcp-tool.md`.
**`CHECKLISTS.md`**: pre-commit, pre-release, and definition-of-done checks, including "MCP tool and skill updated for any new feature."
**`ANTI_PATTERNS.md`**: mistakes to avoid, seeded with common LLM failure modes (inventing APIs, skipping tests, duplicating logic, editing unrelated files, silently changing public schemas).
**`CONTEXT_MAP.md`**: which files matter for which kinds of task, so the agent does not load the whole repo.
**`REFERENCE/`**: condensed notes on the MCP spec and skill format, plus any third-party library or protocol the project depends on that models may know poorly.

---

## 6. Profiles

**`minimal`** generates only: `AGENTS.md`, `SPEC.md`, `ARCHITECTURE.md`, `PLAN.md`, `PROGRESS.md`, `DECISIONS.md`, `TESTING.md`, `MCP.md`, `mcp/`, `skills/<PROJECT_NAME>/SKILL.md`.

**`full`** (default) generates everything in section 4.

---

## 7. Archive step

Name the archives `<PROJECT_NAME>-scaffold-<YYYYMMDD>.tar.gz` and `<PROJECT_NAME>-scaffold-<YYYYMMDD>.zip`. Both must contain the entire `<PROJECT_NAME>-scaffold/` folder: all markdown files plus the MCP skeleton, skill, `mcp.json.example`, and `llms.txt`.

Linux / macOS / WSL / Git Bash:

```bash
NAME="<PROJECT_NAME>-scaffold"
STAMP="$(date +%Y%m%d)"
tar -czf "${NAME}-${STAMP}.tar.gz" "${NAME}"
zip -r "${NAME}-${STAMP}.zip" "${NAME}"
tar -tzf "${NAME}-${STAMP}.tar.gz" | head -n 50
unzip -l "${NAME}-${STAMP}.zip" | head -n 50
```

Windows PowerShell:

```powershell
$Name  = "<PROJECT_NAME>-scaffold"
$Stamp = Get-Date -Format yyyyMMdd
Compress-Archive -Path $Name -DestinationPath "$Name-$Stamp.zip"
tar -czf "$Name-$Stamp.tar.gz" $Name
```

Cross-platform Python (no external tools):

```python
import tarfile, zipfile, pathlib, datetime
name = "<PROJECT_NAME>-scaffold"
stamp = datetime.date.today().strftime("%Y%m%d")
root = pathlib.Path(name)
with tarfile.open(f"{name}-{stamp}.tar.gz", "w:gz") as t:
    t.add(root, arcname=name)
with zipfile.ZipFile(f"{name}-{stamp}.zip", "w", zipfile.ZIP_DEFLATED) as z:
    for p in sorted(root.rglob("*")):
        if p.is_file():
            z.write(p, p.as_posix())
```

After building, **verify**: list each archive's contents and confirm the file count matches the manifest in section 4 (for the chosen profile).

---

## 8. Self-extracting script template (Mode B)

Generate a single Python script, `make_scaffold.py`, structured like this. Fill `FILES` with every file's real content.

```python
#!/usr/bin/env python3
"""Creates <PROJECT_NAME>-scaffold/ and builds .tar.gz and .zip archives."""
import tarfile, zipfile, pathlib, datetime

NAME = "<PROJECT_NAME>-scaffold"
STAMP = datetime.date.today().strftime("%Y%m%d")

FILES = {
    "AGENTS.md": r'''...full content...''',
    "SPEC.md": r'''...full content...''',
    # one entry per file in the manifest, using forward-slash relative paths
}

def main():
    root = pathlib.Path(NAME)
    for rel, content in FILES.items():
        path = root / rel
        path.parent.mkdir(parents=True, exist_ok=True)
        path.write_text(content, encoding="utf-8", newline="\n")
    with tarfile.open(f"{NAME}-{STAMP}.tar.gz", "w:gz") as t:
        t.add(root, arcname=NAME)
    with zipfile.ZipFile(f"{NAME}-{STAMP}.zip", "w", zipfile.ZIP_DEFLATED) as z:
        for p in sorted(root.rglob("*")):
            if p.is_file():
                z.write(p, p.as_posix())
    print(f"Created {NAME}-{STAMP}.tar.gz and {NAME}-{STAMP}.zip ({len(FILES)} files)")

if __name__ == "__main__":
    main()
```

Notes: use raw triple-quoted strings; if a file's content contains `'''`, switch that entry to `"""` or escape it. Tell the user to run `python make_scaffold.py`.

---

## 9. Final reply format

Keep the reply short:
1. One-line summary of what was generated (project, language, profile, file count).
2. Links to the two archives (Mode A) or the exact run command (Mode B/C).
3. A compact tree of the top-level layout.
4. One line listing assumptions made.
5. One line suggesting the first step: *give `AGENTS.md` and `MASTER_PROMPT.md` to your coding agent and have it start at milestone 1 in `PLAN.md`.*

---

## 10. Pre-delivery verification checklist

- [ ] Every file in the chosen profile's manifest exists and is non-empty.
- [ ] No unfilled placeholders such as `<PROJECT_NAME>` remain in any file.
- [ ] Build and test commands in `AGENTS.md`, `BUILD.md`, and `TESTING.md` agree with each other.
- [ ] Every MCP tool listed in `MCP.md` has a file in `mcp/tools/` and a contract test.
- [ ] `SKILL.md` has valid YAML frontmatter with `name` and `description`.
- [ ] `mcp.json.example` is valid JSON.
- [ ] `llms.txt` links resolve to files that exist.
- [ ] `SECURITY.md` states what the MCP server may expose.
- [ ] No secrets or real absolute paths appear anywhere.
- [ ] Both archives open and list the expected contents.
