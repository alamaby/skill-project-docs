# Project Memory Index

Last Updated: 2026-10-05 17:11:00 +07:00
Format Version: 1.0.0

## Current State
- Repository `skill-project-docs` contains the generic project documentation generator and standardized AI agent skill.
- Multiplatform GitHub Actions CI workflow configured for Linux and Windows runners.
- README.md refactored to prioritize AI Agent-First workflow (/project-docs slash command) and comprehensive installation guides for Claude Code, OpenCode, Codex, Antigravity, and Cursor.

## Active Decisions
- Zero external runtime dependencies: generator and validation scripts execute in pure PowerShell (`pwsh` / PowerShell 5.1).
- Isolated test directory execution in CI ensures repository source files remain untouched during test runs.
- Agent-First documentation: Quickstart focuses on `/project-docs` slash command invocation, keeping manual CLI usage in an alternative section.

## Open Items & Blockers
- None.

## Legacy Archives
- None.

## Recent Entries
- [2026-10-05 17:11:00 - Restructure README for Agent-First Workflow and Add Multi-Harness Installation](2026-10-05/171100-restructure-readme-agent-quickstart.md)
- [2026-10-05 16:49:30 - Add GitHub Actions CI Workflow for Validation & Scaffolding](2026-10-05/164930-add-github-ci-workflow.md)
