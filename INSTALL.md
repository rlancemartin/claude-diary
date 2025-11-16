# Installation Guide

This guide will help you install and set up the Claude Code Memory System plugin.

## Prerequisites

- Claude Code installed and working
- Access to your `~/.claude` directory
- Basic familiarity with command line

## Installation Methods

### Method 1: Local Marketplace (Recommended)

This method allows you to manage the plugin like any other Claude Code plugin.

1. **Clone or download this repository**:
   ```bash
   cd ~/Desktop/Code  # or wherever you keep your code
   git clone https://github.com/rlancemartin/claude-diary cc-memory
   # OR download and extract the ZIP file
   ```

2. **Create a local marketplace directory** (if you don't have one):
   ```bash
   mkdir -p ~/.claude/marketplaces/local
   ```

3. **Create or edit your marketplace.json file**:
   ```bash
   nano ~/.claude/marketplaces/local/marketplace.json
   ```

4. **Add the plugin to your marketplace.json**:
   ```json
   {
     "name": "local-plugins",
     "owner": {
       "name": "Local"
     },
     "plugins": [
       {
         "name": "claude-memory",
         "source": "/Users/YOUR_USERNAME/Desktop/Code/cc-memory",
         "description": "Long-term memory system for Claude Code"
       }
     ]
   }
   ```

   **Important**: Replace `/Users/YOUR_USERNAME/Desktop/Code/cc-memory` with the actual path where you cloned/downloaded the plugin.

5. **Install the plugin in Claude Code**:
   - Open Claude Code
   - Run the plugin install command (check Claude Code docs for current command)
   - Select "local-plugins" marketplace
   - Select "claude-memory" plugin
   - Confirm installation

6. **Verify installation**:
   ```bash
   # In Claude Code, try:
   /diary
   /reflect
   ```

### Method 2: Manual Command Installation

This method copies just the commands without full plugin structure.

1. **Copy commands to your global commands directory**:
   ```bash
   cp commands/diary.md ~/.claude/commands/
   cp commands/reflect.md ~/.claude/commands/
   ```

2. **Verify installation**:
   ```bash
   # In Claude Code, try:
   /diary
   /reflect
   ```

**Note**: With this method, you won't get automatic updates if the plugin is updated.

## Post-Installation Setup

### 1. Create Memory Directories

The plugin will create these automatically on first use, but you can create them manually if you prefer:

```bash
mkdir -p ~/.claude/memory/diary
mkdir -p ~/.claude/memory/reflections
```

### 2. Verify Permissions

Make sure you have write permissions:

```bash
ls -la ~/.claude/memory/
```

You should see both `diary/` and `reflections/` directories.

### 3. Test the Plugin

1. **Create a test diary entry**:
   - Do some work in Claude Code (any session)
   - Run `/diary`
   - Check that a file was created: `ls -la ~/.claude/memory/diary/`

2. **Test reflection** (after you have 2-3 diary entries):
   - Run `/reflect`
   - Verify a reflection file was created: `ls -la ~/.claude/memory/reflections/`

## Troubleshooting

### "Command not found: /diary"

**Problem**: Claude Code doesn't recognize the command.

**Solutions**:
1. Check that the command file exists: `ls ~/.claude/commands/diary.md`
2. Restart Claude Code
3. Try the full plugin installation method instead

### "Cannot find session transcript"

**Problem**: The `/diary` command can't locate your session files.

**Solutions**:
1. Make sure you're running `/diary` from within a Claude Code session (not a regular terminal)
2. Check that session files exist: `ls ~/.claude/projects/`
3. Try running `/diary` at the end of a session, not during

### "Permission denied" creating directories

**Problem**: Can't write to `~/.claude/memory/`

**Solutions**:
1. Check ownership: `ls -la ~/.claude/`
2. Fix permissions: `chmod 755 ~/.claude/memory/`
3. Manually create directories: `mkdir -p ~/.claude/memory/{diary,reflections}`

### "No diary entries found" when running /reflect

**Problem**: `/reflect` can't find any diary entries.

**Solutions**:
1. Run `/diary` first to create some entries
2. Check that diary files exist: `ls ~/.claude/memory/diary/`
3. Verify file permissions: `ls -la ~/.claude/memory/diary/`

## Updating the Plugin

### If installed via marketplace:
1. Pull the latest changes: `cd ~/Desktop/Code/cc-memory && git pull`
2. Restart Claude Code (changes should be picked up automatically)

### If installed manually:
1. Pull the latest changes: `cd ~/Desktop/Code/cc-memory && git pull`
2. Copy updated commands:
   ```bash
   cp commands/diary.md ~/.claude/commands/
   cp commands/reflect.md ~/.claude/commands/
   ```
3. Restart Claude Code

## Uninstalling

### If installed via marketplace:
- Use Claude Code's plugin uninstall command

### If installed manually:
```bash
rm ~/.claude/commands/diary.md
rm ~/.claude/commands/reflect.md
```

**Note**: This does not delete your diary entries or reflections. To remove those:
```bash
rm -rf ~/.claude/memory/
```

## Next Steps

Once installed:
1. Read the [README.md](README.md) for usage instructions
2. Check [examples/sample-diary-entry.md](examples/sample-diary-entry.md) to see what diary entries look like
3. Check [examples/sample-reflection.md](examples/sample-reflection.md) to see what reflections look like
4. Start using `/diary` after your Claude Code sessions!

## Getting Help

- Check the [README.md](README.md) for usage guidelines
- Review [scope.md](scope.md) to understand the system design
- Check Claude Code documentation for general plugin issues
- Create an issue in the repository if you find bugs
