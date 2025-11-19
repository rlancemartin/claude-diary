# Claude Diary   

A long-term memory Claude Code plugin that learns from your activity and continuously improves Claude's understanding of your preferences, patterns, and workflows.

- **Diary**: Generate a diary entry for any Claude Code session 
- **Reflection**: Performs reflection across multiple diary entries
- **Memory**: Reflection updates your `CLAUDE.md` with actionable rules

<img width="951" height="667" alt="cd" src="https://github.com/user-attachments/assets/d0b1deef-b027-4014-b761-67e36c221edc" />

## Quickstart 

1. **Clone this repository**:
```bash
git clone https://github.com/rlancemartin/claude-diary claude-diary
cd claude-diary
```

2. **Install commands**:
```bash
cp commands/*.md ~/.claude/commands/
```

3. **Install PreCompact hook** (for auto-diary generation):
```bash
mkdir -p ~/.claude/hooks
cp hooks/pre-compact.sh ~/.claude/hooks/pre-compact.sh
chmod +x ~/.claude/hooks/pre-compact.sh
```

4. **Use the system**:
   - Run `/diary` after important sessions (or let the PreCompact hook auto-generate)
   - After 5-10 entries, run `/reflect` to analyze patterns
   - `CLAUDE.md` automatically updated with learnings
   - Repeat: more sessions → more patterns → smarter Claude

## Overview

The `CLAUDE.md` files serve as [memory](https://code.claude.com/docs/en/memory) for Claude Code. But, they are updated manually (e.g., via the `#` shortcut or `/memory` command). This plugin automates the process of creating and saving memories to `CLAUDE.md`. It is inspired by [an interview between Dan Shipper and Cat Wu / Boris Cherny from the Claude Code team](https://www.youtube.com/watch?v=IDSAMqip6ms&t=352s), which discusses creating a feedback loop where Claude learns from sessions and automatically updates its memory. The approach is borrowed from the [Generative Agents paper](https://arxiv.org/pdf/2304.03442), which introduces a memory architecture with three key components:

1. **Observations**: Raw experiences recorded in natural language (like our diary entries)
2. **Reflection**: Synthesizing observations into higher-level insights and patterns (like our `/reflect` command)
3. **Retrieval**: Dynamically accessing relevant memories to inform behavior (like reading CLAUDE.md into each session)

Our implementation applies this architecture to Claude Code: diary entries capture observations from each session, reflection synthesizes patterns across multiple entries, and CLAUDE.md serves as the retrieved memory that shapes future interactions.

### Diary Entries

The `/diary` command creates structured diary entries from the current session. It uses a context-first approach, reflecting on the conversation already in Claude Code's context. It only falls back to parsing JSONL transcripts when context is insufficient (e.g., after session compaction). See [diary.md](commands/diary.md) for details. The `diary` command can be run in two ways:

1. **Automatic**: PreCompact hook runs before conversation compacts (long sessions, 200+ messages)
2. **Manual**: Run `/diary` anytime to capture important context in the current session

#### How the Diary Command Works

**Primary approach (most sessions):**
- Reflects on current conversation in memory
- No JSONL parsing needed
- Captures design decisions, user preferences, PR feedback, challenges, and solutions
- Zero tool calls for typical sessions

**Fallback approach (compacted sessions):**
- Only used when context is insufficient
- Locates JSONL transcript file in the project's directory 
- Extracts metadata (tool counts, files modified)
- Faster than previous implementations

**Output:**
- Structured markdown entry saved to `~/.claude/memory/diary/YYYY-MM-DD-session-N.md`
- Includes: task summary, work done, design decisions, user preferences, challenges, solutions

### Reflection

The `/reflect` command analyzes diary entries to identify patterns and automatically updates `CLAUDE.md`. See [reflect.md](commands/reflect.md) for details. The `reflect` command can be run in one way currently:

**Manual**: Run `/reflect` anytime to have diary entries accumulated and want to update the `CLAUDE.md` file with the new learnings.

**Key features:**
- Identifies patterns appearing 2+ times across sessions
- Detects violations of existing CLAUDE.md rules (strengthens them)
- Tracks processed entries in `processed.log` (avoids re-analysis)
- Automatically appends actionable rules to CLAUDE.md
- Generates reflection document with insights

**Patterns extracted:**
- Persistent preferences → CLAUDE.md rules
- Successful approaches → design templates
- Anti-patterns → things to avoid
- Rule violations → strengthened rules

#### Reflect Command Options

```bash
# Analyze last 20 unprocessed entries
/reflect last 20 entries

# Analyze specific date range
/reflect from 2025-01-01 to 2025-01-31

# Filter by project
/reflect for project /Users/username/Code/my-app

# Filter by topic/keyword
/reflect related to testing

# Combine filters
/reflect last 15 entries for project /Users/username/Code/my-app related to React

# Re-analyze including already-processed entries
/reflect include all entries

# Re-analyze specific entry
/reflect reprocess 2025-11-07-session-1.md
```

## Directory Structure

The plugin creates and uses these directories:

```
~/.claude/
├── hooks/
│   └── pre-compact.sh           # Auto-generates diary before compact (long sessions)
├── memory/
│   ├── diary/                   # Session diary entries
│   │   ├── 2025-01-15-session-1.md
│   │   ├── 2025-01-15-session-2.md
│   │   └── 2025-01-16-session-1.md
│   └── reflections/             # Synthesized insights & tracking
│       ├── 2025-01-reflection-1.md
│       ├── 2025-02-reflection-1.md
│       └── processed.log        # Tracks which entries have been analyzed
└── CLAUDE.md                    # Rules automatically updated by /reflect
```

These directories are automatically created on first use.

**processed.log format**: `[diary-entry] | [reflection-date] | [reflection-file]`

## Contributing

This plugin follows the [Claude Code plugin structure](https://code.claude.com/docs/en/plugins):

```
cc-memory/
├── .claude-plugin/
│   └── plugin.json
├── commands/
│   ├── diary.md
│   └── reflect.md
├── README.md
└── scope.md
```

To modify or extend:
1. Edit command files in `commands/`
2. Test locally using the local marketplace approach
3. Share your improvements

## License

MIT License - feel free to use, modify, and share.
