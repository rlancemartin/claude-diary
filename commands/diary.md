---
description: Create a structured diary entry from the current session transcript
---

# Create Diary Entry from Current Session

You are going to create a structured diary entry that documents what happened in the current Claude Code session. This entry will be used later for reflection and pattern identification.

## Steps to Follow

### 1. Locate the Current Session Transcript

Run this single command to find the transcript file:

```bash
# Find the most recent session file for this project
CWD="{{ cwd }}" && \
PROJECT_HASH=$(echo "$CWD" | sed 's/\//-/g' | sed 's/^-//') && \
SESSION_FILE=$(ls -t ~/.claude/projects/${PROJECT_HASH}/*.jsonl 2>/dev/null | head -1) && \
if [ -z "$SESSION_FILE" ]; then \
  echo "ERROR: No session file found for project: $CWD" && \
  echo "Looked in: ~/.claude/projects/${PROJECT_HASH}/" && \
  ls -la ~/.claude/projects/ | head -20; \
else \
  echo "FOUND: $SESSION_FILE" && \
  ls -lh "$SESSION_FILE"; \
fi
```

**What this does:**
- Converts current directory to project hash format (e.g., `/Users/name/Code/app` → `-Users-name-Code-app`)
- Finds the most recent `.jsonl` file in that project's directory
- Reports the file location and size

**Important:** Save the `SESSION_FILE` path from this output for the next step.

### 2. Extract Session Data

Run this single batched command to extract all necessary information:

```bash
SESSION_FILE="[path-from-step-1]" && \
echo "=== SESSION METADATA ===" && \
echo "File: $SESSION_FILE" && \
echo "Lines: $(wc -l < "$SESSION_FILE")" && \
echo "Size: $(ls -lh "$SESSION_FILE" | awk '{print $5}')" && \
echo "" && \
echo "=== GIT BRANCH ===" && \
jq -r 'select(.gitBranch) | .gitBranch' "$SESSION_FILE" | head -1 && \
echo "" && \
echo "=== TIMESTAMP ===" && \
jq -r '.timestamp' "$SESSION_FILE" | head -1 && \
echo "" && \
echo "=== USER MESSAGES (First 5) ===" && \
jq -r 'select(.message.role == "user") | .message.content' "$SESSION_FILE" | head -5 && \
echo "" && \
echo "=== USER MESSAGES (Last 3) ===" && \
jq -r 'select(.message.role == "user") | .message.content' "$SESSION_FILE" | tail -3 && \
echo "" && \
echo "=== TOOL USAGE COUNTS ===" && \
jq -r 'select(.message.content[]?.name) | .message.content[].name' "$SESSION_FILE" | sort | uniq -c && \
echo "" && \
echo "=== FILES MODIFIED ===" && \
grep -o '"filePath":"[^"]*"' "$SESSION_FILE" | sort -u && \
echo "" && \
echo "=== GIT OPERATIONS ===" && \
grep -E 'git commit|git push|git merge' "$SESSION_FILE" | grep -o '"command":"[^"]*"' | head -5 && \
echo "" && \
echo "=== ERRORS (if any) ===" && \
grep -i '"Exit code [^0]' "$SESSION_FILE" | head -3
```

**What this extracts:**
- Session metadata (file size, line count)
- Git branch and timestamp
- User messages (first 5 and last 3 for context)
- Tool usage statistics (how many Edit, Read, Write, etc.)
- Files that were modified
- Git operations performed
- Any errors encountered

**Note:** For large sessions (>1MB), this command samples strategically rather than reading everything.

### 3. Create the Diary Entry

Based on the extracted data above, create a structured markdown diary entry with these sections:

```markdown
# Session Diary Entry

**Date**: [YYYY-MM-DD from timestamp]
**Time**: [HH:MM:SS from timestamp]
**Session ID**: [uuid from filename]
**Project**: [project path]
**Git Branch**: [branch name if available]

## Task Summary
[2-3 sentences: What was the user trying to accomplish based on the user messages?]

## Work Summary
[Bullet list of what was accomplished:]
- Features implemented
- Bugs fixed
- Documentation added
- Tests written

## Design Decisions Made
[IMPORTANT: Document key technical decisions and WHY they were made:]
- Architectural choices (e.g., "Used React Context instead of Redux because...")
- Technology selections
- API design decisions
- Pattern selections

## Actions Taken
[Based on tool usage and file operations:]
- Files edited: [list paths from "FILES MODIFIED"]
- Commands executed: [from git operations]
- Tools used: [from tool usage counts]

## Code Review & PR Feedback
[CRITICAL: Capture any feedback about code quality or style:]
- PR comments mentioned
- Code quality feedback
- Linting issues
- Style preferences

## Challenges Encountered
[Based on errors and user corrections:]
- Errors encountered [from "ERRORS" section]
- Failed approaches
- Debugging steps

## Solutions Applied
[How problems were resolved]

## User Preferences Observed
[CRITICAL: Document preferences for commits, testing, code style, etc.]

### Commit & PR Preferences:
- [Any patterns around commit messages, PR descriptions]

### Code Quality Preferences:
- [Testing requirements, linting preferences]

### Technical Preferences:
- [Libraries, patterns, frameworks preferred]

## Code Patterns and Decisions
[Technical patterns used]

## Context and Technologies
[Project type, languages, frameworks]

## Notes
[Any other observations]
```

### 4. Save the Diary Entry

Run this command to save the entry:

```bash
# Create directory if needed
mkdir -p ~/.claude/memory/diary && \
# Determine filename
TODAY=$(date +%Y-%m-%d) && \
N=1 && \
while [ -f ~/.claude/memory/diary/${TODAY}-session-${N}.md ]; do N=$((N+1)); done && \
DIARY_FILE=~/.claude/memory/diary/${TODAY}-session-${N}.md && \
# Save entry (you'll need to write the content)
echo "[diary-content]" > "$DIARY_FILE" && \
echo "Saved to: $DIARY_FILE"
```

Use the Write tool to actually write the diary content to the determined file path.

### 5. Confirm Completion

Display:
- Path where diary was saved
- Brief summary: number of user messages, tools used, files modified

## Important Guidelines

- **Be factual and specific**: Include concrete details (file paths, error messages)
- **Capture the 'why'**: Explain reasoning behind decisions
- **Document ALL user preferences**: Especially around commits, PRs, linting, testing
- **Include failures**: What didn't work is valuable learning
- **Keep it structured**: Follow the template consistently

## Error Handling

- If session file not found, explain where you looked and show available project directories
- If transcript is malformed, document what you could parse
- If any extraction fails, note it and continue with available data

## For Large Sessions (>1MB)

The extraction command above already samples strategically (first/last messages, counts instead of full extracts). This keeps the data manageable while capturing the essential information.

If you need more detailed analysis, use the Read tool to examine the session file directly, or use grep for specific keywords.
