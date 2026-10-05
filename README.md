# skill-project-docs

[![PowerShell 5.1+](https://img.shields.io/badge/PowerShell-5.1%20%7C%207%2B-blue.svg?logo=powershell)](https://github.com/PowerShell/PowerShell)
[![Zero Dependencies](https://img.shields.io/badge/Dependencies-Zero-success.svg)](https://github.com/alamaby/skill-project-docs)
[![Platform](https://img.shields.io/badge/Platform-Windows%20%7C%20Linux%20%7C%20macOS-lightgrey.svg)](#)
[![Agent Skill Standard](https://img.shields.io/badge/Agent%20Skill-Cross--Harness-orange.svg)](#cross-harness-usage)
[![License](https://img.shields.io/badge/License-MIT-informational.svg)](LICENSE)

A generic, zero-dependency, data-driven static documentation website generator and lifecycle tracker for software repositories.

Originating from the standalone `/project-docs` skill specification, this repository provides everything needed to bootstrap, maintain, and publish complete project documentation—including chronological timelines, searchable changelogs, architecture components, and release notes—generated entirely from structured JSON data.

---

## Key Highlights

- **Zero External Toolchains:** Built in pure PowerShell (`pwsh` on Windows, Linux, and macOS, or Windows PowerShell 5.1). No Node.js, Python, or Ruby build dependencies required.
- **Data-Driven Architecture:** All pages, navigation filters (year, version, category, status, component), architecture matrices, and timeline cards are derived directly from JSON manifests in `data/`.
- **One-Command Scaffolding (`-Init`):** Bootstrap a complete documentation site into any new or existing repository in seconds using `-Init`.
- **Cross-Harness AI Skill:** Standardized `SKILL.md` allows AI pair programmers in **Antigravity**, **Gemini CLI**, **Claude Code**, **OpenCode**, **Cursor**, **Codex**, and **Windsurf** to document lifecycle changes autonomously.
- **Integrity Validation & Link Checking:** Automatic schema verification, duplicate ID detection, and recursive internal link checks ensure zero broken links before deployment.
- **Production-Ready Static Distribution:** Generates an optimized, self-contained static site in `dist/project-docs/`, ready for immediate hosting on GitHub Pages, Cloudflare Pages, Netlify, or any static web server.

---

## Directory Layout

```text
skill-project-docs/
├── README.md                           # Repository documentation & guide
└── project-docs/
    ├── SKILL.md                        # Standardized Agent Skill definition
    ├── index.html                      # Documentation homepage (auto-updated GEN blocks)
    ├── changelog.html                  # Searchable changelog with multi-criteria filters
    ├── architecture.html               # Architecture overview and component tables
    ├── changes/
    │   ├── index.html                  # Change archive index
    │   └── YYYY/MM/<slug>.html         # Granular change detail pages (permanent URLs)
    ├── releases/
    │   ├── index.html                  # Release archive index
    │   └── <version>.html              # Granular release milestone pages
    ├── assets/
    │   ├── css/
    │   │   ├── main.css                # Base theme (navy palette, typography, grid)
    │   │   ├── components.css          # Cards, badges, timeline, filters, layout
    │   │   └── animations.css          # Subtle transitions & scroll reveals
    │   └── js/
    │       ├── app.js                  # Year injection & scroll observers
    │       ├── changelog.js            # Client-side filtering & instant search
    │       ├── diagrams.js             # SVG animation triggers
    │       └── navigation.js           # Responsive mobile navigation menu
    ├── components/                     # Reusable HTML snippets (__BRAND__, __TOKENS__)
    ├── data/
    │   ├── project.json                # Project identity (brand, name, description)
    │   ├── releases.json               # Release milestones & mapped changes
    │   ├── architecture/
    │   │   └── current.json            # Active components, roles, and pipelines
    │   └── changes/
    │       ├── YYYY-MM-DD-<slug>.json  # Granular change entries (1 file per change)
    │       └── index.json              # Compiled manifest (auto-generated)
    ├── templates/                      # Starter files used by the -Init routine
    └── tools/
        ├── Build-ProjectDocs.ps1       # Core generator, validator, and builder
        ├── Change-Template.html        # Template for change detail pages
        └── Release-Template.html       # Template for release milestone pages
```

---

## Quickstart

### Step 1: Scaffold Documentation in Your Repository

Copy the `project-docs/` directory into your repository, then run the generator with the `-Init` flag:

```powershell
# Using PowerShell Core (Windows, Linux, macOS)
pwsh project-docs/tools/Build-ProjectDocs.ps1 -Init

# Or Windows PowerShell 5.1
powershell -ExecutionPolicy Bypass -File project-docs/tools/Build-ProjectDocs.ps1 -Init
```

The `-Init` flag automatically creates all missing folders, copies starter templates, seeds baseline data, renders the initial pages, verifies links, and compiles `dist/project-docs/`.

### Step 2: Configure Project Metadata

Edit `project-docs/data/project.json` to reflect your project identity:

```json
{
  "brand": "MyProject Docs",
  "name": "MyProject",
  "description": "High-performance microservices and CLI tools.",
  "docs_version": "1.0.0",
  "repository_root": ".",
  "stack": ["go", "docker", "typescript"]
}
```

### Step 3: Record Verified Changes

Add individual JSON files for each change under `project-docs/data/changes/YYYY-MM-DD-<slug>.json`:

```json
{
  "id": "2026-02-01-database-pool",
  "date": "2026-02-01",
  "time": "15:45:00",
  "seq": 1,
  "version": "v1.1.0",
  "title": "Optimized connection pool with dynamic sizing",
  "category": "perf",
  "status": "released",
  "summary": "Improves database throughput by dynamically adjusting connection pool limits under load.",
  "problem_before": "Fixed pool sizing caused connection starvation during traffic spikes.",
  "solution": "Implemented adaptive pool scaling with health-check monitoring.",
  "reason": "Prevent query timeouts during peak periods.",
  "files_affected": ["internal/db/pool.go", "config/database.yaml"],
  "user_impact": "Lower query latency and zero dropped requests under high concurrency.",
  "tech_impact": "Replaced static mutex lock with atomic counter and worker pool.",
  "validation": {
    "build": "go build ./...",
    "test": "go test -race ./internal/db",
    "lint": "golangci-lint run"
  },
  "risks": ["Higher peak memory footprint under sustained heavy load."],
  "breaking_changes": [],
  "migration_notes": "None.",
  "commits": ["e4d3c2b1a09876543210fedcba9876543210abcd"],
  "next_steps": ["Expose connection pool metrics via Prometheus exporter."],
  "components": ["database", "core"]
}
```

### Step 4: Rebuild & Validate

```powershell
pwsh project-docs/tools/Build-ProjectDocs.ps1
```

The generator will:
1. Validate all fields against the 21-key schema.
2. Sort changes and update `data/changes/index.json`.
3. Generate permanent detail pages in `changes/YYYY/MM/<slug>.html`.
4. Generate release pages in `releases/<version>.html`.
5. Update homepage cards, timeline, stats table, and filter dropdowns.
6. Verify all relative internal links.
7. Output the static build to `dist/project-docs/`.

---

## Cross-Harness Usage

`skill-project-docs` adheres to open agent skill specifications, enabling immediate plug-and-play use across modern AI coding agents.

### Antigravity & Gemini CLI

To make this skill available across your projects in Antigravity or Gemini CLI:

- **Workspace level:** Clone or copy `project-docs` to `.agents/skills/project-docs/` in your workspace.
- **Global level:** Place in `~/.gemini/config/skills/project-docs/`.

Once registered, your agent can be prompted with:
> *"Use project-docs to document the recent changes from git commit HEAD and rebuild the docs site."*

### Claude Code

- Copy `project-docs` into `.claude/skills/project-docs/` or `.agents/skills/project-docs/`.
- Claude Code will discover `SKILL.md` and trigger it when asked to maintain changelogs or project documentation.

### OpenCode

Register this repository in your `opencode.json`:

```json
{
  "skills": ["c:/Works/github.com/alamaby/skill-project-docs"]
}
```

OpenCode discovers the skill under the identifier `project-docs`.

### Cursor, Codex, Windsurf & Cline

Place `project-docs/` in `.agents/skills/project-docs/`. The standard frontmatter in `SKILL.md` is automatically indexed for on-demand tool execution.

---

## 21-Key Schema Reference

Every change entry file (`data/changes/YYYY-MM-DD-<slug>.json`) requires exactly 21 fields:

| Field | Type | Description | Example |
| :--- | :--- | :--- | :--- |
| `id` | `string` | Must match the file basename without `.json` | `"2026-02-01-database-pool"` |
| `date` | `string` | ISO Date format `YYYY-MM-DD` | `"2026-02-01"` |
| `time` | `string` | 24-hour time format `HH:mm:ss` | `"15:45:00"` |
| `seq` | `number` | Order for multiple changes on the same date | `1` |
| `version` | `string` | Associated release version | `"v1.1.0"` |
| `title` | `string` | Human-readable title of the change | `"Optimized connection pool"` |
| `category` | `string` | Category (`feat`, `fix`, `perf`, `refactor`, `docs`, etc.) | `"perf"` |
| `status` | `string` | Lifecycle status (`released`, `draft`, `deprecated`) | `"released"` |
| `summary` | `string` | Concise 1-2 sentence overview | `"Improves database throughput..."` |
| `problem_before` | `string` | Factual description of prior problem or limitation | `"Fixed pool sizing caused starvation."` |
| `solution` | `string` | Technical explanation of the applied fix | `"Implemented adaptive pool scaling."` |
| `reason` | `string` | Business or technical rationale | `"Prevent query timeouts during peaks."` |
| `files_affected` | `array` | List of modified source code paths | `["internal/db/pool.go"]` |
| `user_impact` | `string` | Impact visible to consumers or end users | `"Lower query latency."` |
| `tech_impact` | `string` | Architectural, database, or API implications | `"Replaced static mutex lock."` |
| `validation` | `object` | Build, test, and lint validation evidence | `{"build":"pass", "test":"42 passed"}` |
| `risks` | `array` | Known edge cases, caveats, or trade-offs | `["Higher peak memory footprint"]` |
| `breaking_changes` | `array` | Breaking changes list (empty array if none) | `[]` |
| `migration_notes` | `string` | Guidance for users upgrading to this version | `"None required."` |
| `commits` | `array` | Full or abbreviated git commit hashes | `["e4d3c2b1a0..."]` |
| `next_steps` | `array` | Future improvements or follow-up items | `["Expose Prometheus metrics"]` |
| `components` | `array` | Architectural component tags for filtering | `["database", "core"]` |

---

## CLI Options

`Build-ProjectDocs.ps1` accepts the following parameters:

```text
Syntax:
  Build-ProjectDocs.ps1 [-Init] [-ValidateOnly] [-DocsRoot <string>] [-DistDir <string>]

Options:
  -Init           Scaffolds initial templates, directories, and baseline data.
  -ValidateOnly   Validates data and internal links without writing HTML or dist/.
  -DocsRoot       Specifies an alternate documentation root directory.
  -DistDir        Specifies an alternate production output directory.
```

---

## Design System & Technical Rules

1. **UTF-8 with BOM:** All text and HTML files are written in UTF-8 with BOM to ensure accurate character decoding across Windows PowerShell 5.1, PowerShell Core, and diverse browser engines.
2. **Generator Boundaries:** Generator-managed areas are clearly bounded by `<!-- GEN:NAME -->...<!-- /GEN:NAME -->`. Manual content written outside these markers in detail pages is strictly preserved during rebuilds.
3. **Factual Integrity:** Changes should only document verified facts from git commits and code reviews. Speculative or unverified statements must be marked `not-verified: ...`.
4. **Security by Design:** Never commit API tokens, passwords, `.env` files, internal URLs, or personal data into documentation entries.

---

## License

This project is licensed under the [MIT License](LICENSE).
