---
description: Analyze diary entries to identify patterns and propose CLAUDE.md updates
---

# Reflect on Diary Entries and Synthesize Insights

You are going to analyze multiple diary entries to identify recurring patterns, synthesize insights, and propose updates to the user's CLAUDE.md file.

## Parameters

The user can provide:
- **Date range**: "from YYYY-MM-DD to YYYY-MM-DD" or "last N days"
- **Entry count**: "last N entries" (e.g., "last 10 entries")
- **Project filter**: "for project [project-path]" (optional - filter to specific project)
- **Pattern filter**: "related to [keyword]" (e.g., "related to testing" or "related to React")

If no parameters are provided, default to analyzing the **last 10 diary entries**.

## Steps to Follow

1. **Check processed entries log**:
   - Read `~/.claude/memory/reflections/processed.log` to find already-processed diary entries
   - Format: `[diary-filename] | [reflection-date] | [reflection-filename]`
   - Example: `2025-11-07-session-1.md | 2025-11-08 | 2025-11-reflection-1.md`
   - If file doesn't exist, all entries are unprocessed
   - Create the file if it doesn't exist: `touch ~/.claude/memory/reflections/processed.log`

2. **Locate diary entries**:
   - Directory: `~/.claude/memory/diary/`
   - Entries are named: `YYYY-MM-DD-session-N.md`
   - List all entries, sorted by date (newest first)
   - **Exclude already-processed entries** (unless user explicitly requests re-analysis)

3. **Filter entries based on parameters**:
   - If date range specified: only include entries within that range
   - If entry count specified: take the N most recent entries
   - If project filter specified: only include entries where the Project field matches
   - If pattern filter specified: only include entries that mention the keyword in any section

4. **Read and parse filtered diary entries**:
   - Read each diary entry file
   - Extract information from all sections
   - Pay special attention to:
     - User Preferences Observed
     - Code Patterns and Decisions
     - Solutions Applied (what works well)
     - Challenges Encountered (what to avoid)
   - **If a section is missing**: Skip that section (don't fail), and note in Metadata which entries had incomplete structure

5. **Create the reflections directory** (if it doesn't exist):
   - Directory: `~/.claude/memory/reflections/`
   - Use `mkdir -p` to create it automatically

6. **Read current CLAUDE.md files to check for existing rules**:
   - Read `~/.claude/CLAUDE.md` to understand what global rules already exist
   - **For each unique project** found in diary entries:
     - Check if `[project-path]/CLAUDE.md` exists
     - If it exists, read it to understand existing project-specific rules
     - Note which projects have CLAUDE.md files and which don't
   - This is CRITICAL for avoiding duplicates and detecting conflicts

7. **Analyze entries for patterns AND rule violations**:
   - **Frequency analysis**: What preferences/patterns appear in multiple entries?
   - **Consistency check**: Are preferences consistent or contradictory?
   - **Context awareness**: Do patterns apply globally or to specific project types?
   - **Abstraction level**: Can specific instances be generalized into rules?
   - **Signal vs. noise**: Distinguish between:
     - **One-off requests**: "Make this button pink" (appears once)
     - **Recurring patterns**: "Always use TypeScript strict mode" (appears 3+ times)
   - **CRITICAL - Rule Violation Detection**: Check if diary entries show violations of EXISTING CLAUDE.md rules
     - Look in "Code Review & PR Feedback", "Challenges Encountered", "User Preferences Observed" sections
     - If a diary mentions user correcting Claude for violating an existing rule, this is HIGH PRIORITY
     - Example: CLAUDE.md says "no AI attribution" but diary shows "User corrected: Don't add Claude attribution"
     - These violations mean the existing rule needs STRENGTHENING (more explicit, moved to top, zero tolerance language)

   - **CRITICAL - Global vs Project-Specific Classification**:
     For each pattern identified, classify it as GLOBAL or PROJECT-SPECIFIC:

     **GLOBAL rules** (go in `~/.claude/CLAUDE.md`):
     - Apply across ALL projects (error handling, commit style, agent usage)
     - Are about the user's general workflow preferences
     - Are technology-agnostic or apply to multiple projects
     - Examples: "use conventional commits", "never swallow exceptions", "use agents for complex tasks"

     **PROJECT-SPECIFIC rules** (go in project's own CLAUDE.md):
     - Only apply to ONE specific project or technology stack
     - Are about patterns unique to that codebase
     - Would be confusing or wrong if applied elsewhere
     - Examples: Framework-specific patterns, codebase conventions, tool-specific configurations

     **How to decide**: Ask "Would this rule make sense in a completely different project?" If NO → project-specific.

8. **Synthesize insights** organized by category:

   **CRITICAL**: Focus on extracting concise, actionable rules suitable for CLAUDE.md (which is read into every session).

   **PRIORITY: Rule Violations** (MUST address first):
   - Did any diary entries document violations of existing CLAUDE.md rules?
   - If YES, these are HIGHEST PRIORITY and require rule strengthening
   - Document the violation pattern and propose specific strengthening (e.g., "move to top", "add ZERO TOLERANCE", "make more explicit")

   **A. PR Review Feedback Patterns** (from code reviews):
   - Common feedback themes from reviewers
   - Code quality issues flagged repeatedly
   - What reviewers appreciate vs. criticize
   - Patterns in "looks AI-generated" feedback

   **B. Persistent Preferences** (2+ occurrences; 3+ = high confidence):
   - Commit and PR style requirements
   - Code organization and structure
   - Testing and linting workflows
   - Tool and framework choices
   - Communication style

   **C. Design Decisions That Worked** (successful approaches):
   - Architecture choices that solved problems well
   - Technology selections with clear rationale
   - Patterns that led to clean, maintainable code
   - Decision-making frameworks that helped

   **D. Anti-Patterns to Avoid** (2+ occurrences; 3+ = high confidence):
   - Approaches that failed or needed rework
   - Common mistakes that waste time
   - What NOT to do and why
   - Alternatives that work better

   **E. Efficiency Lessons** (save time in future):
   - What workflows worked smoothly
   - What caused delays or friction
   - Tools/commands that proved useful
   - Processes to streamline

   **F. Project-Specific Patterns** (context-dependent):
   - Patterns specific to certain project types (React, CLI, Python, etc.)
   - Technology-specific preferences
   - Framework-specific conventions

9. **Generate a reflection document** with this structure:

   **Scope guidance** (for large analyses):
   - If analyzing >20 entries, group similar patterns to avoid repetition
   - Focus on highest-confidence patterns (3+ occurrences) first
   - Limit detailed examples to 3 per pattern
   - Target length: 300-800 lines (adjust based on pattern density)

```markdown
# Reflection: [Date Range or "Last N Entries"]

**Generated**: [YYYY-MM-DD HH:MM:SS]
**Entries Analyzed**: [count]
**Date Range**: [first-date] to [last-date]
**Projects**: [list of projects if filtered, or "All projects"]

## Summary
[2-3 paragraph overview of key insights discovered across these entries]

## CRITICAL: Rule Violations Detected
[ONLY include this section if violations were found. If NO violations detected, OMIT this entire section including the header.]

**Rule**: [The existing CLAUDE.md rule that was violated]
**Violation Pattern**: [How it appeared in diary entries - quote specific examples]
**Frequency**: [X/Y entries showed this violation]
**Impact**: [Why this is serious - user had to correct multiple times]
**Root Cause**: [Why the existing rule failed - too weak, buried in list, ambiguous wording]
**Strengthening Action**: [Specific changes made to CLAUDE.md rule]
- Move to top of section: [YES/NO]
- Add emphasis (CAPS, ZERO TOLERANCE): [YES/NO]
- Make more explicit/specific: [YES/NO]
- Add override language: [YES/NO]

## Patterns Identified

### A. PR Review Feedback Patterns
[What reviewers commonly flag or appreciate]

1. **[Feedback Pattern]** (appeared in X/Y entries)
   - **Observation**: [What reviewers said/flagged]
   - **Examples**: [Specific feedback quotes]
   - **Lesson**: [What to do/avoid]
   - **CLAUDE.md rule**: `- [succinct actionable rule]`

### B. Persistent Preferences (2+ occurrences; 3+ = high confidence)
[Recurring user preferences across sessions]

1. **[Preference Name]** (appeared in X/Y entries)
   - **Observation**: [What was consistently preferred]
   - **Evidence**: [Which sessions, what happened]
   - **Confidence**: High/Medium/Low
   - **CLAUDE.md rule**: `- [succinct actionable rule]`

### C. Design Decisions That Worked
[Successful technical decisions and approaches]

1. **[Decision Name]**
   - **What worked**: [Brief description]
   - **Why it worked**: [Key reason]
   - **When to use**: [Context/applicability]
   - **CLAUDE.md rule** (if generalizable): `- [succinct rule]`

### D. Anti-Patterns to Avoid (2+ occurrences; 3+ = high confidence)
[Things that failed or caused problems multiple times]

1. **[Anti-pattern Name]** (appeared in X/Y entries)
   - **What didn't work**: [Brief description]
   - **Why it failed**: [Key reason]
   - **What to do instead**: [Alternative]
   - **CLAUDE.md rule**: `- [avoid X, use Y instead]`

### E. Efficiency Lessons
[Workflows, tools, and processes that save time]

1. **[Efficiency Pattern]**
   - **What worked well**: [Description]
   - **Time/effort saved**: [Impact]
   - **When to apply**: [Context]
   - **CLAUDE.md rule** (if applicable): `- [succinct rule]`

### F. Project-Specific Patterns
[Patterns that apply to specific projects - these go in PROJECT CLAUDE.md files, NOT global]

1. **[Pattern Name]** (for [project path])
   - **Observation**: [What was observed]
   - **Context**: [When this applies]
   - **Target file**: `[project-path]/CLAUDE.md`
   - **Rule to add**: `- [action]`

## Notable Mistakes and Learnings
[Key mistakes that taught valuable lessons]
- **Mistake**: [What went wrong]
  - **Why**: [Root cause]
  - **Learning**: [What was learned]
  - **Prevention**: [How to avoid in future]

## One-Off Observations
[Preferences that appeared only once - not patterns yet, but worth noting]
- [Observation from single session]

## Proposed CLAUDE.md Updates

### CRITICAL: The High-Value Bar for CLAUDE.md

CLAUDE.md is loaded into EVERY session. Every rule you add is a tax on every future interaction. Most "learnings" don't clear that bar.

Before proposing ANY rule for global CLAUDE.md, it must pass BOTH tests:

**Test 1 — Hard-won?**
Would the user genuinely burn >30 minutes rediscovering this? Or could it be figured out on the fly from docs / a quick search / one failed run?

- KEEP: "never fabricate URLs" (cost real production outage)
- KEEP: "use agents for complex tasks" (changes workflow approach)
- CUT: "sys.executable over 'python'" (first failure tells you)
- CUT: "bash subshell can't mutate parent arrays" (known bash fact)
- CUT: "rename repo over new repo" (common sense)

**Test 2 — Broadly applicable?**
Does this apply in a meaningful fraction of sessions? Or only in rare specific contexts that deserve a scoped home?

- KEEP in global: git/commit style, agent-first methodology, honest pushback, testing philosophy — every session touches these
- MOVE to tech-rules.md (or equivalent on-demand file): anything technology-specific (Rust, SQLite, Puppeteer, LLM eval) — loaded when relevant
- MOVE to project CLAUDE.md: codebase-specific conventions
- CUT entirely: rare-event rules (open-sourcing a repo, cutting a release) — capture in the reflection document as context, don't load every session

**Default action: CUT.** If a rule is on the fence, it doesn't belong in global CLAUDE.md. Better to under-add than to bloat every session's context. The reflection document itself is the durable record — rules promoted to CLAUDE.md are the unusual few that earn their keep.

**When presenting proposed rules**, explicitly justify each one against both tests. If you can't articulate why it clears the bar, drop it. Format each proposal with a one-line justification, e.g.:

```markdown
- never fabricate URLs — T1: caused prod outage (hard-won). T2: applies to any session that writes external links (broad).
```

Rules that fail either test belong in the reflection document's "One-Off Observations" or "Notable Mistakes and Learnings" sections, NOT in the Proposed CLAUDE.md Updates section.

### Format Requirements

- CLAUDE.md is read into EVERY session, so keep updates **succinct and non-verbose**
- Use bullet points (-, +, or numbered lists)
- Be direct and actionable (imperative tone)
- Avoid explanations - just state the rule
- Group related rules together
- Use context markers when needed (e.g., "for Python projects:", "when reviewing PRs:")

**Good Example** (succinct, actionable):
```markdown
- git commits: use conventional format (feat:, fix:, refactor:, docs:, test:)
- PR descriptions: no Claude Code attribution or AI tool mentions
- testing: always run tests before committing, ensure they pass
```

**Bad Example** (too verbose):
```markdown
- When you are creating git commits, it's important to follow the conventional
  commit format which includes prefixes like feat: for features, fix: for bug
  fixes, refactor: for code refactoring, docs: for documentation changes, and
  test: for test changes. This helps maintain consistency across the codebase.
```

### GLOBAL Rules (for `~/.claude/CLAUDE.md`)

Below are proposed additions to your global CLAUDE.md file.

#### Section: [General Preferences / Code Quality / Git Workflow / etc.]

```markdown
[Proposed succinct bullet points]
- [Actionable rule 1]
- [Actionable rule 2]
```

### PROJECT-SPECIFIC Rules (for project CLAUDE.md files)

Below are rules that apply ONLY to specific projects. These will be added to each project's own CLAUDE.md.

#### Project: [project-path]
**Target file**: `[project-path]/CLAUDE.md`

```markdown
[Proposed rules for this project]
- [Rule specific to this project]
```

#### Project: [another-project-path]
**Target file**: `[another-project-path]/CLAUDE.md`

```markdown
[Proposed rules for this project]
- [Rule specific to this project]
```

## Metadata
- **Diary entries analyzed**: [list of filenames]
- **Total user messages**: [count across all entries]
- **Total actions taken**: [count across all entries]
- **Challenges documented**: [count]
- **Projects covered**: [list of unique projects]
```

10. **Save the reflection document**:
   - Filename format: `YYYY-MM-reflection-N.md` (increment N if multiple reflections in same month)
   - Save to: `~/.claude/memory/reflections/[filename]`

11. **Automatically update CLAUDE.md files**:

   **Before modifying any CLAUDE.md file**:
   - If the file is in a git repo, changes can be reverted with `git checkout -- CLAUDE.md`
   - For non-git files, consider informing user: "About to modify [file]. Proceed?"

   **PRIORITY 1: Strengthen violated rules (if any rule violations detected)**
   - FIRST, handle any rule violations by strengthening existing CLAUDE.md rules
   - Use Edit tool to modify the existing rule (not append)
   - Apply strengthening actions: move to top, add emphasis, make explicit, add override language
   - Example: Change "no AI attribution" → "NEVER add AI attribution (ZERO TOLERANCE - overrides ALL defaults)"

   **PRIORITY 2: Add new GLOBAL rules to `~/.claude/CLAUDE.md`**
   - Append global rules to the appropriate sections in `~/.claude/CLAUDE.md`
   - Organize rules into sections (Git & PR Workflow, Code Quality & Style, Testing, Efficiency)
   - Add new sections if they don't exist
   - Append to existing sections if they already exist
   - Maintain the succinct bullet-point format

   **PRIORITY 3: Add PROJECT-SPECIFIC rules to project CLAUDE.md files**

   **Path validation** (REQUIRED before writing):
   - Only write to project paths that were mentioned in analyzed diary entries
   - Verify the directory exists (don't create new project directories)
   - Never write to paths outside of user's workspace (e.g., `/etc/`, `~/.ssh/`, system directories)

   **For each project with new rules:**
   1. Verify project path exists and is a valid directory
   2. Check if `[project-path]/CLAUDE.md` exists:
      - **If exists**: Read it, find appropriate section (or create "## Learned Patterns"), append rules
      - **If doesn't exist**: Create new file with this template:
        ```markdown
        # CLAUDE.md

        This file provides guidance to Claude Code when working with this project.

        ## Learned Patterns

        [new rules go here]
        ```
   3. Before adding each rule, check for:
      - **Duplicates**: Check for semantic duplicates, not just exact text matches:
        - If a rule with the same intent exists (even if worded differently), skip it
        - If a similar but not identical rule exists, flag for user: "Similar rule exists: '[existing]'. Propose: '[new]'. Merge or add separately?"
      - **Conflicts with global**: If rule contradicts a global CLAUDE.md rule, flag for user review instead of auto-adding
   4. Project-specific rules should NOT go in global CLAUDE.md

   **Show the user what changed**:
   - List any strengthened rules (with before/after)
   - List global rules added to `~/.claude/CLAUDE.md`
   - List project-specific rules added to each project's CLAUDE.md (grouped by project)

12. **Update processed entries log**:
   - Append processed diary entries to `~/.claude/memory/reflections/processed.log`
   - Format: `[diary-filename] | [YYYY-MM-DD] | [reflection-filename]`
   - One line per diary entry processed
   - Example: `2025-11-07-session-1.md | 2025-11-08 | 2025-11-reflection-1.md`

13. **Present completion summary to user**:
   - **FIRST**: Highlight any rule violations detected and how rules were strengthened
   - Display the reflection filename and location
   - Show how many patterns were identified (global vs project-specific)
   - **Global CLAUDE.md**: List sections updated in `~/.claude/CLAUDE.md`
   - **Project CLAUDE.md files**: List each project file updated with rule count
   - Confirm that processed.log was updated

## Important Guidelines

### Global vs Project-Specific Rules

**CRITICAL**: Route rules to the correct CLAUDE.md file.

| Rule Type | Goes In | Examples |
|-----------|---------|----------|
| **Global** | `~/.claude/CLAUDE.md` | Commit style, error handling, agent usage, testing philosophy, technology rules that apply across multiple projects |
| **Project-Specific** | `[project]/CLAUDE.md` | Codebase-specific conventions, project-specific configurations, technology rules unique to one project |

**Decision Test** (ask in order):

1. **Does this rule mention THIS SPECIFIC codebase?** (file paths, service names, custom patterns)
   - YES → PROJECT-SPECIFIC
   - NO → Continue to #2

2. **Is this a technology rule (React, Python, Lambda, etc.)?**
   - Does user have MULTIPLE projects using this technology?
     - YES → GLOBAL (put in "Technology-Specific" section)
     - NO (only one project uses it) → PROJECT-SPECIFIC
   - Not a technology rule → Continue to #3

3. **Would this rule make sense in a completely different project?**
   - YES → GLOBAL
   - NO → PROJECT-SPECIFIC

**Examples of GLOBAL rules**:
- "use conventional commits" - applies to all git repos
- "never swallow exceptions silently" - applies to all code
- "Next.js: use Server Actions for mutations" - technology rule, user has multiple Next.js projects
- "bash scripts: log functions MUST output to stderr" - technology rule across projects

**Examples of PROJECT-SPECIFIC rules**:
- "WebSocket endpoint is /ws/chat not /socket" → codebase-specific path
- "Use get_screen() for Textual screens" → only one TUI project uses Textual
- "Lambda layer must include zod@3.25.76" → specific to this serverless project
- "AI detection: avoid parallel conditionals" → specific to this writing project

**When in doubt**:
- If rule mentions specific file paths, service names, or custom conventions → PROJECT-SPECIFIC
- If rule is about a technology used in only ONE project → PROJECT-SPECIFIC
- If rule is about a technology used in MULTIPLE projects → GLOBAL (Technology-Specific section)

### Pattern Recognition Principles

1. **Frequency matters**: Require 2+ occurrences before proposing a rule
   - **Strong patterns** (3+ occurrences): High confidence, propose for CLAUDE.md
   - **Emerging patterns** (2 occurrences): Lower confidence, propose but note as "emerging"
   - **One-off** (1 occurrence): Document in "One-Off Observations", don't propose for CLAUDE.md yet

2. **Context matters**: Note whether patterns are:
   - Universal (apply everywhere) → GLOBAL
   - Technology-specific (used in multiple projects) → GLOBAL (Technology-Specific section)
   - Technology-specific (used in only one project) → PROJECT-SPECIFIC
   - Codebase-specific (file paths, service names, conventions) → PROJECT-SPECIFIC

3. **Consistency matters**: Flag contradictory preferences for user review

4. **Actionability matters**: Only propose rules that Claude can actually follow

5. **Abstraction matters**: Find the right level:
   - Too specific: "The login button should be blue" ❌
   - Too broad: "Users like colors" ❌
   - Just right: "Use the design system's primary color for CTAs" ✅

6. **Succinctness matters for CLAUDE.md**:
   - Each rule should be ONE line (or short bullet)
   - Use imperative tone: "do X", "use Y", "avoid Z"
   - Add context prefix when needed: "for Python:", "when testing:"
   - NO explanations or rationale in CLAUDE.md - just the rule

### Distinguishing Signal from Noise

**SIGNAL** (add to CLAUDE.md):
- "Always use TypeScript strict mode" (appears in 5 sessions across 3 projects)
- "Prefer functional components in React" (appears in 4 React projects)
- "Run tests before committing" (appears in 6 sessions)

**NOISE** (document but don't add to CLAUDE.md):
- "Make this button pink" (appears once, specific task)
- "Use dark mode for this demo" (appears once, context-specific)
- "Skip tests this time" (contradicted by usual pattern)

### Quality Checks

Before proposing a CLAUDE.md update, verify:
- ✅ Does this apply to future sessions? (not just the past)
- ✅ Is this actionable? (Claude can actually do it)
- ✅ Is this generalizable? (not too specific to one case)
- ✅ Is this consistent? (doesn't contradict other patterns)
- ✅ Is this valuable? (will it improve future interactions)

## Error Handling

- If no diary entries exist, inform the user and suggest running `/diary` first
- If all diary entries have been processed and no new entries are found, inform the user
- If user applied filters but no entries match, inform the user:
  "Found X unprocessed entries, but none match filter '[filter]'. Options:
  1. Remove the filter to analyze all unprocessed entries
  2. Use 'include all entries' to include already-processed entries matching the filter
  3. Try a different filter"
- If fewer than 3 entries are found, proceed but note that pattern confidence is low
- If diary entries are malformed, skip them and document which ones had issues
- If the reflections directory cannot be created, report the error
- If global CLAUDE.md cannot be read or written, report the error but continue with reflection
- If processed.log cannot be read, assume no entries have been processed yet
- If processed.log cannot be written, report the error

**Project CLAUDE.md write failures:**
- If a project directory doesn't exist, skip that project and report in summary
- If a project CLAUDE.md cannot be written (permissions, etc.):
  1. Report which project file failed and why
  2. Continue attempting to write to remaining project files
  3. Include failed files in completion summary with the rules that couldn't be added
- If a rule conflicts with global CLAUDE.md, don't add it automatically - flag for user review

## Handling Already-Processed Entries

**Default behavior**: Skip entries already listed in `~/.claude/memory/reflections/processed.log`

**User can override** with these flags:
- "include all entries" - re-analyze everything including processed entries
- "reprocess [filename]" - re-analyze specific entry
- "last N entries including processed" - analyze N most recent, even if processed

**When to suggest re-processing**:
- User significantly changed their workflow
- User wants to validate previous patterns
- User wants to extract different insights from same sessions

## Example Usage

```
# Analyze last 10 unprocessed entries (default)
/reflect

# Analyze last 20 unprocessed entries
/reflect last 20 entries

# Analyze entries from a date range
/reflect from 2025-01-01 to 2025-01-31

# Analyze entries for specific project
/reflect for project ~/Code/my-app

# Analyze entries related to testing
/reflect related to testing

# Combine filters
/reflect last 15 entries for project ~/Code/my-app related to React

# Re-analyze including already-processed entries
/reflect include all entries

# Re-analyze specific entry
/reflect reprocess 2025-11-07-session-1.md
```
