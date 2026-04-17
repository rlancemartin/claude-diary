# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A Claude Code plugin ("claude-diary" / `claude-memory`) that gives Claude Code long-term memory by:
1. Generating per-session diary entries (observations) under `~/.claude/memory/diary/YYYY-MM-DD-session-N.md`
2. Reflecting across entries to synthesize patterns and auto-update `~/.claude/CLAUDE.md` (and per-project CLAUDE.md files)
3. Triggering diary generation automatically via a `PreCompact` hook

There is no build system, package manager, or test suite. The plugin is pure markdown + a bash hook, consumed by Claude Code itself.

## Repository layout

- `.claude-plugin/plugin.json` — plugin manifest (`name: claude-memory`). Edit this if changing version/metadata.
- `commands/diary.md` — prompt driving `/diary`. Defines the diary entry template and the context-first extraction strategy (only falls back to parsing `~/.claude/projects/*.jsonl` transcripts when in-context history is insufficient).
- `commands/reflect.md` — prompt driving `/reflect`. Defines pattern analysis, global-vs-project rule classification, and the auto-update protocol for CLAUDE.md files. This is the most complex file in the repo — treat it as the source of truth for memory semantics.
- `hooks/pre-compact.sh` — bash hook invoked by Claude Code's `PreCompact` event; emits `/diary` so a diary entry is written before compaction discards context.
- `examples/` — reference outputs showing the expected shape of a diary entry and a reflection document. Use these to validate changes to the command prompts.
- `INSTALL.md`, `README.md` — user-facing docs. Installation is a manual file copy (`cp commands/*.md ~/.claude/commands/`, `cp hooks/pre-compact.sh ~/.claude/hooks/`) plus a `settings.json` hook registration.

## Architecture: the memory loop

The three pieces form a generative-agents-style loop and must stay internally consistent:

1. **Observation** (`/diary`) writes one markdown file per session with a fixed section schema (Task Summary, Work Summary, Design Decisions, User Preferences Observed, Code Review Feedback, Challenges, Solutions, etc.).
2. **Reflection** (`/reflect`) reads those files, dedupes against `~/.claude/memory/reflections/processed.log`, and produces a reflection doc plus edits to CLAUDE.md files. It requires 2+ occurrences for a pattern and 3+ for high confidence.
3. **Retrieval** happens implicitly when Claude Code loads CLAUDE.md at session start.

Consequence: the section headers in `commands/diary.md` are a contract with `commands/reflect.md`. Adding or renaming a diary section without updating the corresponding extraction logic in `reflect.md` will silently break pattern detection. When editing either prompt, cross-check the other and `examples/sample-diary-entry.md` / `examples/sample-reflection.md`.

## External paths the plugin owns

These paths are hard-coded into the command prompts — keep them consistent across `commands/*.md`, `hooks/*.sh`, `README.md`, and `INSTALL.md`:

- `~/.claude/memory/diary/` — diary entries
- `~/.claude/memory/reflections/` — reflection documents
- `~/.claude/memory/reflections/processed.log` — dedup ledger (`[diary-file] | [date] | [reflection-file]`)
- `~/.claude/CLAUDE.md` — global rules target
- `[project]/CLAUDE.md` — per-project rules target (only written to paths that appear in analyzed diary entries)
- `~/.claude/projects/-<escaped-cwd>/*.jsonl` — raw session transcripts (fallback source for `/diary`)

## Working in this repo

- Changes here are prompt engineering, not code — validate by reading the examples and mentally tracing the flow, not by running tests.
- When modifying `commands/reflect.md`, preserve the global-vs-project-specific routing logic and the "rule violation detection" priority — these are load-bearing for the update protocol documented in the README.
- The hook script deliberately just `echo`s `/diary`; that's how PreCompact hooks inject slash commands. Don't "fix" it into something that calls Claude directly.
