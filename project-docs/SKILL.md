---
name: project-docs
description: Manage and generate data-driven static project documentation (project-docs/): scaffold structure, record granular change entries, validate schemas, rebuild static pages, verify internal links, and assemble production distribution. Use whenever asked to document changes, maintain changelogs, initialize docs, or manage architecture and release records across repositories.
---

# project-docs

A zero-dependency, generic, data-driven static documentation generator for software project lifecycles.

- **Source of truth:** `project-docs/data/` (JSON files for project metadata, architecture, releases, and granular changes).
- **Generator tool:** `project-docs/tools/Build-ProjectDocs.ps1` (Pure PowerShell; works with PowerShell 5.1 on Windows and PowerShell Core `pwsh` on Windows, Linux, and macOS).
- **Production output:** `dist/project-docs/` (Self-contained static site ready for GitHub Pages, Netlify, or any static hosting).

---

## Directory Architecture

```text
<repo-root>/
├── project-docs/
│   ├── index.html                      # Homepage (GEN:* blocks managed by generator)
│   ├── changelog.html                  # Changelog with dynamic search & filter
│   ├── architecture.html               # Architecture overview & component tables
│   ├── changes/
│   │   ├── index.html                  # Changes archive listing
│   │   └── YYYY/MM/<slug>.html         # Granular change detail pages (stable permanent URLs)
│   ├── releases/
│   │   ├── index.html                  # Release archive listing
│   │   └── <version>.html              # Granular release milestone pages
│   ├── assets/
│   │   ├── css/ (main, components, animations)
│   │   └── js/ (app, changelog, diagrams, navigation)
│   ├── components/                     # Reusable HTML snippets (__BRAND__, __TOKENS__)
│   ├── data/
│   │   ├── project.json                # Project name, brand, description, stack
│   │   ├── releases.json               # Release milestones & mapped changes
│   │   ├── architecture/
│   │   │   └── current.json            # Active components, roles, and pipelines
│   │   └── changes/
│   │       ├── YYYY-MM-DD-<slug>.json  # Granular change entries (1 file per change)
│   │       └── index.json              # Compiled manifest (auto-generated)
│   ├── templates/                      # Starter scaffolding templates
│   └── tools/
│       ├── Build-ProjectDocs.ps1       # Core build, validation, and assembly script
│       ├── Change-Template.html        # Detail page template for changes
│       └── Release-Template.html       # Detail page template for releases
└── dist/
    └── project-docs/                   # Production build artifact
```

---

## Core Workflows

### 1. Initializing in a Target Repository

To scaffold project documentation in any repository:

```powershell
# Using PowerShell Core (Cross-platform: Windows / Linux / macOS)
pwsh project-docs/tools/Build-ProjectDocs.ps1 -Init

# Or Windows PowerShell 5.1
powershell -ExecutionPolicy Bypass -File project-docs/tools/Build-ProjectDocs.ps1 -Init
```

The `-Init` switch will:
1. Create the complete directory structure (`data/`, `changes/`, `releases/`, `assets/`, `components/`).
2. Scaffold starter HTML pages and metadata configs if not already present.
3. Seed an initial baseline change entry in `data/changes/`.
4. Validate the schema, render detail pages, check links, and assemble `dist/project-docs/`.

### 2. Adding a New Change Entry

1. **File naming convention:**
   `project-docs/data/changes/YYYY-MM-DD-<slug>.json`
   Example: `2026-01-15-token-refresh.json`

2. **Mandatory 21-key schema:**
   Every change file must contain the following 21 fields:

   | Field | Type | Description |
   | :--- | :--- | :--- |
   | `id` | string | Exact match of the filename without `.json` extension |
   | `date` | string | `YYYY-MM-DD` |
   | `time` | string | `HH:mm:ss` (from commit timestamp or change time) |
   | `seq` | integer | Sequential index (e.g. `1`, `2`) for ordering changes on the same date |
   | `version` | string | Semantic version (e.g. `v1.2.0`) |
   | `title` | string | Clear, descriptive title of the change |
   | `category` | string | e.g. `feat`, `fix`, `refactor`, `perf`, `chore`, `docs`, `security` |
   | `status` | string | e.g. `released`, `draft`, `deprecated` |
   | `summary` | string | 1-2 sentence high-level summary |
   | `problem_before`| string | Concrete prior state or problem being addressed |
   | `solution` | string | Implemented technical solution |
   | `reason` | string | Motivation and business/engineering justification |
   | `files_affected`| array of strings | Modified source files (e.g. `["src/auth.py"]`) |
   | `user_impact` | string | Observable impact on end users |
   | `tech_impact` | string | Architecture, state, or dependency implications |
   | `validation` | object | `{ "build": "...", "test": "...", "lint": "..." }` |
   | `risks` | array of strings | Identified risks or operational caveats |
   | `breaking_changes`| array of strings| Any breaking changes (empty array if none) |
   | `migration_notes`| string | Guidance for migration (or "None") |
   | `commits` | array of strings | Full or abbreviated git commit hashes |
   | `next_steps` | array of strings | Follow-up tasks or future iterations |
   | `components` | array of strings | Tags for component filtering (e.g. `["auth", "api"]`) |

3. **Factual verification:**
   - Only document verified facts (check `git log`, `git show`, and file contents).
   - If an item is unverified, prefix it with `not-verified: ...`.
   - Never expose API keys, credentials, secrets, `.env` files, internal endpoints, or private data.

### 3. Building and Validating

Run the generator whenever data or templates change:

```powershell
# Full build + validation + dist assembly
pwsh project-docs/tools/Build-ProjectDocs.ps1

# Dry-run validation only (no file writes or dist creation)
pwsh project-docs/tools/Build-ProjectDocs.ps1 -ValidateOnly

# Custom root directory (useful when invoking from external location)
pwsh project-docs/tools/Build-ProjectDocs.ps1 -DocsRoot "./custom/docs" -DistDir "./custom/dist"
```

The script executes 8 automated phases:
1. Reads all `data/changes/*.json` entries.
2. Validates schema completeness and checks for duplicate IDs.
3. Sorts entries chronologically (`date` asc, `time` asc, `seq` asc).
4. Generates/updates `data/changes/index.json`.
5. Renders new change detail pages using `Change-Template.html`.
6. Renders new release pages using `Release-Template.html`.
7. Updates all `<!-- GEN:* -->` blocks (Latest, Timeline, Releases, Stats, Filters, Architecture).
8. Injects dynamic brand branding from `data/project.json`.
9. Verifies all internal relative links to prevent broken links or 404s.
10. Assembles the production-ready distribution in `dist/project-docs/`.

### 4. Customizing Detail Pages

Change detail pages (`project-docs/changes/YYYY/MM/<slug>.html`) are generated once.
- Content **inside** `<!-- GEN:* -->` blocks is updated automatically by the generator.
- Content **outside** `<!-- GEN:* -->` blocks is preserved on subsequent builds.
- You can freely add architecture diagrams, sequence flows, code snippets, or deeper explanations to detail pages.

### 5. Managing Releases & Architecture

- **Releases:** Update `project-docs/data/releases.json`. Set `latest` to the current version. List associated change IDs under each release.
- **Architecture:** Update `project-docs/data/architecture/current.json` to define components, roles, external binaries, and pipeline flows.

---

## Cross-Harness Configuration

This skill is designed to work across all AI coding agent harnesses:

### Antigravity & Gemini CLI
Place in one of:
- Workspace: `.agents/skills/project-docs/`
- Global: `~/.gemini/config/skills/project-docs/`

### Claude Code
Place in one of:
- Workspace: `.claude/skills/project-docs/`
- Standard: `.agents/skills/project-docs/`

### OpenCode
Register in `opencode.json`:
```json
{
  "skills": ["./project-docs"]
}
```

### Cursor, Codex, Windsurf & Cline
Standard workspace skill discovery automatically picks up `.agents/skills/project-docs/SKILL.md`.

---

## Technical Notes

- **File Encoding:** All documentation files are written as **UTF-8 with BOM** for consistent Unicode rendering across PowerShell 5.1, PowerShell Core, and modern web browsers.
- **Markers:** Never delete or alter `<!-- GEN:TAG -->` and `<!-- /GEN:TAG -->` comments in HTML files, as they delineate generator-controlled sections.
