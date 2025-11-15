# Claude Diary   

A long-term memory Claude Code plugin that learns from your activity and continuously improves Claude's understanding of your preferences, patterns, and workflows.

- **Diary**: Generate a diary entry for any Claude Code session 
- **Reflection**: Performs reflection across multiple diary entries
- **Memory**: Reflection updates your `CLAUDE.md` with actionable rules

<img width="951" height="667" alt="cd" src="https://github.com/user-attachments/assets/d0b1deef-b027-4014-b761-67e36c221edc" />

## Quickstart 

1. **Clone this repository**:
```bash
git clone <repository-url> claude-diary
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

Each Claude Code session is logged to `~/.claude/projects/...` in JSONL format. The `/diary` command parses these transcripts and creates structured diary entries. See [diary.md](commands/diary.md) for details.

**Two ways to create entries:**
1. **Automatic**: PreCompact hook runs before conversation compacts (long sessions, 200+ messages)
2. **Manual**: Run `/diary` anytime to capture current session

**When to manually run `/diary`:**
- ✅ At the end of important work sessions
- ✅ After key decisions or breakthroughs
- ✅ Before switching projects
- ✅ Anytime you want to capture learnings

### Reflection

The `reflect` command is used to analyze the diary entries and create a reflection document. You can see the rules for the `reflect` command in the [reflect.md](commands/reflect.md) file. This then writes update to the `CLAUDE.md` file.

Run `/reflect` when:
- ✅ You have 5-10+ diary entries accumulated
- ✅ You want to identify patterns in a specific area (e.g., testing, React)
- ✅ You're switching projects and want to capture learnings
- ✅ Monthly or weekly as a regular practice
- ✅ Your `CLAUDE.md` feels incomplete or outdated

## How It Works (Automated Flow)

```
┌─────────────────────────────────────────────────────────────┐
│  1. Work with Claude Code normally                          │
└─────────────────┬───────────────────────────────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────────────────────────┐
│  2. Diary entries created (automatic or manual)             │
│     → PreCompact hook: auto-generates (long sessions)       │
│     → Manual: run /diary anytime                            │
│     → Saved to ~/.claude/memory/diary/                      │
└─────────────────┬───────────────────────────────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────────────────────────┐
│  3. Entries accumulate (5-10+ recommended)                  │
└─────────────────┬───────────────────────────────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────────────────────────┐
│  4. Run /reflect (ad hoc, weekly/monthly)                   │
│     → Analyzes unprocessed entries                          │
│     → Identifies patterns (2+ occurrences)                  │
│     → Automatically updates CLAUDE.md                       │
└─────────────────┬───────────────────────────────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────────────────────────┐
│  5. Future sessions use updated rules                       │
│     → Cycle repeats, Claude gets smarter                    │
└─────────────────────────────────────────────────────────────┘
```

### What Gets Captured

**Diary entries capture:**
- Design decisions and architectural choices
- PR feedback and code quality patterns
- User preferences (commit style, testing, workflow)
- Challenges and solutions
- Technologies and code patterns

**Reflection identifies patterns (2+ occurrences):**
- Persistent preferences → actionable CLAUDE.md rules
- Successful approaches → design decision templates
- Anti-patterns → things to avoid
- Project-specific conventions → context-aware rules

### Reflect Command Options

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

### Example

Here's how the system learns in practice:

**Week 1: Sessions Accumulate**
```
Session 1: You work on React app, use conventional commits
  → Diary created: 2025-11-01-session-1.md

Session 2: PR reviewer says "add test coverage"
  → Diary created: 2025-11-02-session-1.md

Session 3: You use conventional commits again
  → Diary created: 2025-11-03-session-1.md

Session 4: PR reviewer says "code looks AI-generated, simplify"
  → Diary created: 2025-11-04-session-1.md

Session 5: You use conventional commits + run tests before commit
  → Diary created: 2025-11-05-session-1.md
```

**Week 1 End: Run Reflection**
```bash
$ /reflect

Analyzing 5 unprocessed diary entries...

Patterns identified:
✅ Conventional commit format (5/5 sessions) → Strong pattern
✅ Test coverage requested (2/5 sessions) → Pattern
✅ Avoid verbose/AI-looking code (1/5 sessions) → Noted

Reflection created: 2025-11-reflection-1.md
CLAUDE.md updated with 3 new rules:
  - git commits: use conventional format (feat:, fix:, refactor:)
  - testing: add test coverage for new features
  - code style: avoid verbose patterns, match existing code

processed.log updated: 5 entries marked as analyzed
```

**Week 2+: Claude Adapts**

In all future sessions, Claude now:
- ✅ Automatically uses conventional commit format
- ✅ Suggests adding tests for new features
- ✅ Writes simpler, less verbose code

**Month 2: More Learning**
```
10 more sessions → 10 more diary entries
Run /reflect again → Identifies new patterns
CLAUDE.md grows → Claude gets even smarter
```

**The Result**: Over time, Claude learns your exact preferences, coding style, and workflow patterns without you having to repeat yourself.

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

### Session Transcripts

Claude Code stores full conversation transcripts at:
```
~/.claude/projects/[encoded-project-path]/[session-uuid].jsonl
```

Each transcript is in JSONL format (JSON Lines) containing:
- User messages
- Assistant responses
- Tool invocations and results
- File operations
- Error messages
- Git operations

The `/diary` command parses these transcripts using `jq` (JSON processor) to create structured memory entries. See the diary command documentation for detailed jq usage examples and parsing strategies.

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
