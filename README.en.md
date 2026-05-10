# AI Project Management Skeleton (ai-pm-skeleton)

**English** | [简体中文](README.md)

A **tool-agnostic** project management rule skeleton for AI coding assistants (Claude Code / Codex / Gemini) to manage all your projects under unified conventions (addressing, initialization, indexing, templates, red lines).

The skeleton itself is just a `Project/` directory — place it anywhere and it works. It serves as the "starting point" (workspace root) for all your AI-managed projects.

---

## Naming Explained (Important)

The project code `ai-pm-skeleton` breaks down as:

- **ai** — AI coding assistants (Claude Code / Codex / Gemini, etc.)
- **pm** — **Project Management**, **NOT Product Manager**
- **skeleton** — Skeleton (contains only rules, templates, and indexes; no business code)

> ⚠️ This project has **nothing to do** with product management roles, PRDs, user stories, or any product-related functions. It solves **engineering collaboration workflow** problems: how AI assistants are uniformly constrained in a multi-project environment, how they address projects, and how they initialize projects from templates. If you're looking for product management tools, this is not it.

---

## Quick Start (Two Required Steps)

```bash
# 1. Copy the local-only external project index (original is .gitignored)
cp Project/PROJECTS_LOCAL.md.example Project/PROJECTS_LOCAL.md

# 2. Edit DESC_ENV.md for your local machine (it's a placeholder template — AI will give wrong commands if you don't)
#    Windows: notepad / VS Code; macOS/Linux: vim / nano
```

Once done, point your AI tool's (Claude Code / Codex / Gemini) working directory to `Project/` and start chatting.

---

## Usage Examples

### Example 1: Create and Initialize a New Project in the Skeleton

> Scenario: You want to create a new project called `aiBovmantH` and initialize it within the skeleton.

1. Place `Project/` wherever you want (e.g., `D:\Project\` or `~/workspace/`), and point your AI tool's working directory to it.
2. Just tell the AI:

   > I want to create a new project `aiBovmantH` for planning the acquisition of OpenAI, and initialize it.

3. Expected AI behavior (from `CLAUDE.md` / `AGENTS.md` / `DESC_DIR.md`):
   - On startup, automatically reads `RULES_GLOBAL.md` / `PROJECTS_INDEX.md` / `DESC_ENV.md` / `DESC_DIR.md` / `PROJECTS_LOCAL.md`.
   - Checks `PROJECTS_INDEX.md` for name conflicts, then suggests a folder name (format: `20260509_aiBovmantH`, using today's date).
   - After your confirmation, reads `_template/PROJECT.md` and `_template/PROGRESS.md`, replaces placeholders (`{project_name}`, `{YYYY-MM-DD}`, etc.) with actual values, and writes them to the new folder.
   - Appends a row to the "Active Projects" table in `PROJECTS_INDEX.md`.
   - All subsequent file operations for this project are locked within its folder (`DESC_DIR.md` rule #4).

Final directory structure:

```
Project/
├── PROJECTS_INDEX.md          ← one new row: 20260509_aiBovmantH
└── 20260509_aiBovmantH/
    ├── PROJECT.md             ← filled from template
    └── PROGRESS.md
```

### Example 2: Link an Existing Local Project

> Scenario: You already have an old project on your machine (Windows: `D:\develop\workspace\my-old-project`; macOS/Linux: `~/code/my-old-project`) and want to manage it under these rules **without physically moving it** into the skeleton directory.

1. Place `Project/` anywhere as your workspace root, and point your AI tool's working directory to it.
2. Just tell the AI:

   > Please link `D:\develop\workspace\my-old-project` to the current workspace as an external project and initialize it.

3. Expected AI behavior:
   - Reads the 5 rule files on startup.
   - Appends a row to the "External Projects" table in `PROJECTS_LOCAL.md` (absolute path + project name + status + notes).
     `PROJECTS_LOCAL.md` is used instead of `PROJECTS_INDEX.md` because the former is **local-only, not synced** (suitable for machine-specific paths); the latter syncs with the repo and tracks skeleton-internal projects.
   - Enters the external project directory, reads (or creates) `PROJECT.md`, and fills in missing documentation from `_template/`.
   - All subsequent modifications to this project are locked within `D:\develop\workspace\my-old-project`, under the same red-line constraints.

Row added to `PROJECTS_LOCAL.md`:

```
| `D:\develop\workspace\my-old-project` | My Old Project | Active | External reference, governed by workspace rules |
```

---

## How This Skeleton Works

- **Unified entry point**: Whether you use Claude Code, Codex or Gemini, each session is guided to read 5 rule files first (`CLAUDE.md` for Claude Code, `AGENTS.md` for Codex, Gemini users can instruct it in their first prompt).
- **Single source of truth**: All project state, changes, and conventions are recorded only in `PROJECTS_INDEX.md` (skeleton-internal) / `PROJECTS_LOCAL.md` (local external) / sub-project `PROJECT.md`. Tool-specific config files (`.cursorrules` / `~/.claude/CLAUDE.md` etc.) are read-only — rules are 100% portable across tools.
- **Directory red lines**: `plan/` and `archive/` are silent by default; new projects must follow `[YYYYMMDD]_[name]` naming; cross-project modifications and writing business code to the workspace root are strictly forbidden. See `DESC_DIR.md`.

---

## Directory Tree

```
Project/                       ← Workspace root, works in any location
│
├── CLAUDE.md                  ← Claude Code startup protocol: reads 5 rule files on session start
├── AGENTS.md                  ← Codex / OpenAI tools startup protocol (equivalent to CLAUDE.md)
│
├── RULES_GLOBAL.md            ← Global behavior rules: language, pre-confirmation, context-first, handshake, anti-fragmentation, proxy fallback
├── DESC_ENV.md                ← Local environment: OS, PowerShell/Bash, Python/Node versions, etc.
├── DESC_DIR.md                ← Directory red lines: addressing rules, silent zones, new project flow, templates, operation boundaries
│
├── PROJECTS_INDEX.md          ← Repo-synced global project index (skeleton-internal projects)
├── PROJECTS_LOCAL.md          ← Local-only external project index (absolute paths, not synced)
│
├── _template/                 ← New project templates, read-only protected
│   ├── PROJECT.md             ← Project doc skeleton (overview/structure/resources/core content/progress log)
│   └── PROGRESS.md            ← Progress tracking skeleton (phases, tasks, status, timeline, verification checklist)
│
├── plan/                      ← Planning/brainstorming zone, AI stays silent by default
│
└── archive/                   ← Archive zone, completed or deprecated projects, AI stays silent by default
```

Quick reference:

| File / Directory | Who Reads | Purpose |
|-----------------|-----------|---------|
| `CLAUDE.md` | Claude Code (auto) | Forces reading 5 rule files on startup |
| `AGENTS.md` | Codex (auto) | Same as above, Codex version |
| `RULES_GLOBAL.md` | AI on startup | Behavior rules: language, pre-confirmation, proxy, anti-fragmentation, etc. |
| `DESC_ENV.md` | AI on startup | Ensures AI gives commands matching your local setup (PowerShell vs Bash, etc.) |
| `DESC_DIR.md` | AI on startup | Directory red lines and workflow (addressing, silent zones, new projects, templates) |
| `PROJECTS_INDEX.md` | AI on startup | Project map, updated when creating/archiving skeleton-internal projects |
| `PROJECTS_LOCAL.md` | AI on startup | Links existing local external projects |
| `_template/` | AI when creating projects | Project skeleton source, strictly read-only |
| `plan/` | Not read by default | Only enters when you explicitly ask |
| `archive/` | Not read by default | Same as above |

---

## Notes

- **Tool-specific configs are read-only**: You may already have `~/.claude/CLAUDE.md`, `.cursorrules`, Gemini hidden configs, etc. This skeleton strictly forbids writing state/rules to those files, ensuring portability.
- **State can only be written to three places**: `PROJECTS_INDEX.md`, `PROJECTS_LOCAL.md`, and sub-project `PROJECT.md`.
- **New project naming**: `[YYYYMMDD]_[project_name]`, date must be the actual system date, no fabrication.
- **Proxy**: By default, the skeleton assumes a local HTTP/SOCKS5 mixed proxy at `127.0.0.1:10808`, used only as fallback on network issues. If your machine uses a different port or no proxy, edit `RULES_GLOBAL.md` rule #6.
- **Environment description**: `DESC_ENV.md` in the repo is a placeholder template. **First thing to do on a new machine: edit it to match your setup**, otherwise AI will keep giving commands for the wrong shell.
- **Local external index**: `PROJECTS_LOCAL.md` is `.gitignored`; the repo contains `PROJECTS_LOCAL.md.example`. Just `cp` one on first use (see "Quick Start" above).
- **First use**: `PROJECTS_INDEX.md` and `PROJECTS_LOCAL.md` start with empty table headers, waiting for your first project to be added naturally via the examples above.