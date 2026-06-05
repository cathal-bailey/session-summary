# AI Session Summary Tool

A model-agnostic tool for capturing and documenting AI-assisted development sessions. Provides developers with clear visibility into tasks completed, concepts learned, and file changes made during AI interactions.

## Overview

When working with AI assistants, it's easy to lose track of what was changed, why decisions were made, and what concepts were covered. This tool solves that problem by creating comprehensive session summaries that:

- **Document all file changes** with diffs and explanations
- **Capture concepts and learning** with references and difficulty levels
- **Track tasks completed** for easy review and continuation
- **Enable session continuity** when resuming work later
- **Monitor token usage** to prevent context window degradation
- **Work with any AI model** through standardized instructions

## Features

**Model Agnostic** - Works with GPT-4, Claude, Gemini, or any AI assistant  
**Dual Format Output** - Human-readable Markdown + machine-readable JSON  
**Git Integration** - Automatic file change detection via git diff  
**Token Monitoring** - Track usage and get warnings before hitting limits  
**Comprehensive Documentation** - Captures tasks, concepts, files, and context  
**Session Continuity** - Resume work seamlessly with full context  
**Version Controlled** - Session summaries tracked in your repository  

## Quick Start

### 1. Install the CLI Tool

```bash
# Clone this repository
git clone https://github.com/yourusername/session-summary.git
cd session-summary

# Install globally (choose one)
sudo cp session-summary /usr/local/bin/
# or
mkdir -p ~/bin && cp session-summary ~/bin/
echo 'export PATH="$HOME/bin:$PATH"' >> ~/.bashrc && source ~/.bashrc

# Verify installation
session-summary --version
```

See [INSTALL.md](INSTALL.md) for detailed installation instructions.

### 2. Set Up in Your Project

```bash
# Navigate to your project
cd ~/my-project

# Copy the .sessions directory
cp -r /path/to/session-summary/.sessions .

# Or use the init command
session-summary --init
```

### 3. Work with Your AI Assistant

Use your AI assistant normally for any task:
- Writing code
- Debugging
- Learning concepts
- Reviewing PRs

### 4. Generate Session Summary

When ready to end your session, run:

```bash
session-summary my-feature-name
```

This outputs a formatted prompt. **Copy and paste it to your AI assistant.**

### 5. AI Generates Summary

The AI will automatically:
1. Read `.sessions/INSTRUCTIONS.md`
2. Analyze the conversation
3. Detect file changes via git
4. Generate markdown and JSON summaries
5. Save to `.sessions/` directory

### 6. Review Your Summary

Find your summaries in `.sessions/`:
- `session-my-feature-name-YYYY-MM-DD-HHMMSS.md` - Human-readable
- `session-my-feature-name-YYYY-MM-DD-HHMMSS.json` - Machine-readable

## Usage Examples

### Example 1: Coding Session

```bash
# Work with your AI on a feature
You: "Help me refactor the authentication module"
AI: [helps with code changes]
[... work happens ...]

# When done, run the CLI command
$ session-summary auth-refactor

# Copy the output and paste to your AI
You: [paste the formatted prompt]

# AI generates the summary automatically
AI: "Reading .sessions/INSTRUCTIONS.md..."
AI: "Analyzing conversation and detecting file changes..."
AI: "Generated session-auth-refactor-2026-06-04-140000.md and .json"
```

### Example 2: Discussion Session (No Git)

```bash
# Have a discussion with AI
You: "Explain how this PR implements the caching strategy"
AI: [explains in detail]
[... discussion continues ...]

# Generate summary
$ session-summary cache-review

# Paste to AI
You: [paste prompt]
AI: "No files modified - this was a review session. Generating summary..."
```

### Example 3: With Token Monitoring

```bash
# Enable token monitoring in .sessions/config.json first
You: "Let's work on the database refactor"
AI: [helps with refactoring]
AI: "⚠️ Token Usage: 70% of threshold reached."
[... wrap up work ...]

# Generate summary
$ session-summary db-refactor
You: [paste to AI]
AI: [generates summary with token usage info]
```

### Example 4: Continuing Previous Session

```bash
# Load previous session
You: "Read .sessions/session-auth-refactor-2026-06-04-140000.md and continue"
AI: [reads summary]
AI: "I can see we refactored JWT validation. Next steps were integration tests."
[... continue work ...]

# Generate new summary when done
$ session-summary auth-tests
```

## Configuration

Edit `.sessions/config.json` to customize:

```json
{
  "tokenThreshold": 100000,        // Maximum tokens before warning
  "warningThreshold": 0.7,         // Warn at 70% of threshold
  "criticalThreshold": 0.85,       // Critical warning at 85%
  "tokenMonitoring": {
    "enabled": false,              // Enable/disable token monitoring (opt-in)
    "autoNotify": false,           // Automatically notify at thresholds
    "notifyAtWarning": true,       // Notify at 70% threshold
    "notifyAtCritical": true       // Notify at 85% threshold
  },
  "defaultModel": "unknown",       // Default AI model name
  "maxConceptBulletPoints": 10,    // Max concepts in summary
  "maxTaskBulletPoints": 5         // Max tasks in summary
}
```

### Token Monitoring (Opt-In)

Token monitoring is **disabled by default** to avoid interruptions. To enable:

1. Set `tokenMonitoring.enabled` to `true` in config.json
2. Optionally enable `autoNotify` for automatic threshold warnings
3. Configure which thresholds trigger notifications

**When enabled**, the AI will:
- Track token usage throughout the session
- Notify you at 70% and 85% thresholds (if configured)
- Suggest creating a session summary before hitting limits
- Help prevent context window degradation

**When disabled**, the AI will:
- Still include token count in session summaries (if available)
- Respond to manual token check requests
- Not interrupt your workflow with notifications

## Session Summary Structure

Each session summary includes:

### Metadata
- Date, time, duration
- AI model used
- Token count and threshold percentage

### Tasks Completed
- Brief list of what was accomplished (max 5 items)

### Concepts Covered
- Detailed explanations of new concepts
- External references (websites, papers, repos)
- Difficulty levels (beginner/intermediate/advanced)
- Ordered from basic to advanced

### Files Accessed
- **Read Only**: Files examined but not changed
- **Modified**: Files changed with diffs and explanations
- **Created**: New files added
- **Deleted**: Files removed

### Detailed File Changes
- Full git diffs for each modified file
- Explanation of what changed and why
- Line count statistics

### Prerequisites for Continuation
- Knowledge needed to continue
- Dependencies and setup required
- Current state of work

### Next Steps
- Suggested actions to continue the work

## Token Monitoring (Opt-In Feature)

Token monitoring is **disabled by default**. Enable it in `.sessions/config.json` if you want automatic tracking and notifications.

### Enabling Token Monitoring

Edit `.sessions/config.json`:

```json
{
  "tokenMonitoring": {
    "enabled": true,              // Turn on token monitoring
    "autoNotify": true,           // Get automatic notifications
    "notifyAtWarning": true,      // Notify at 70% threshold
    "notifyAtCritical": true      // Notify at 85% threshold
  }
}
```

### Manual Token Tracking

Update token count manually (whether monitoring is enabled or not):

```bash
# Update current token count
cat > .sessions/.session-state.json << EOF
{
  "sessionStartTime": "2026-06-04T14:00:00Z",
  "currentTokenCount": 50000,
  "lastUpdated": "$(date -u +"%Y-%m-%dT%H:%M:%SZ")",
  "sessionActive": true
}
EOF
```

### Checking Token Usage

You can always ask your AI assistant:
- "How many tokens have we used?"
- "What's our token count?"
- "Check token usage"

### Threshold Warnings (When Enabled)

When token monitoring is enabled, you'll receive warnings:

- **< 70%**: Normal operation
- **70-85%**: ⚠️ Warning - Consider wrapping up
- **85-100%**: ⚠️ Critical - Strongly recommend summary
- **> 100%**: 🚨 Limit exceeded - Summary required

### Best Practices

**When to End a Session:**
- Approaching token threshold (70-85%)
- Completed a major feature or milestone
- Switching to different task or context
- End of work day
- Before code review or PR creation

**Session Size Guidelines:**
- **Small** (10K-30K tokens): Single feature, bug fix
- **Medium** (30K-70K tokens): Multiple related tasks
- **Large** (70K-100K tokens): Complex feature, refactoring
- **Avoid** (>100K tokens): Risk of context degradation

## File Structure

```
.sessions/
├── INSTRUCTIONS.md              # AI instructions for generating summaries
├── TEMPLATE.md                  # Template structure for summaries
├── schema.json                  # JSON schema for validation
├── config.json                  # Configuration settings
├── index.json                   # Index of all sessions
├── .session-state.json          # Current session state (not in git)
├── session-auth-refactor-2026-06-04-140000.md
├── session-auth-refactor-2026-06-04-140000.json
├── session-api-optimization-2026-06-05-093000.md
└── session-api-optimization-2026-06-05-093000.json
```

## Advanced Usage

### Searching Sessions

```bash
# List all sessions
ls -lt .sessions/session-*.md

# Search for specific topic
grep -l "authentication" .sessions/session-*.md

# View session index
cat .sessions/index.json | jq '.sessions'
```

### Programmatic Access

The JSON format allows programmatic analysis:

```javascript
// Load session data
const session = require('./.sessions/session-auth-refactor-2026-06-04-140000.json');

// Analyze file changes
const modifiedFiles = session.files.modified.length;
const linesChanged = session.files.modified.reduce(
  (sum, file) => sum + file.linesAdded + file.linesRemoved, 0
);

console.log(`Modified ${modifiedFiles} files, ${linesChanged} lines changed`);
```

### Integration with CI/CD

```yaml
# Example: GitHub Actions workflow
- name: Generate Session Summary
  run: |
    # Trigger AI to generate summary
    echo "complete session summary" | ai-assistant
    
- name: Commit Session Summary
  run: |
    git add .sessions/
    git commit -m "Add session summary"
    git push
```

## Troubleshooting

### Git Not Available

If git is not initialized:
```bash
git init
git add .
git commit -m "Initial commit"
```

The tool will note in summaries when git is unavailable and track changes via conversation analysis only.

### Session State Missing

If `.session-state.json` doesn't exist, the tool will:
- Estimate duration from conversation length
- Note that token count is estimated
- Suggest initializing session tracking

### Large Diffs

For very large file changes, the tool may truncate diffs. You can always view full diffs with:
```bash
git diff path/to/file
```

## Future Enhancements

Planned features:
- CLI tool for session management
- Real-time token monitoring via API integration
- VSCode extension with status bar integration
- Web UI for browsing sessions
- Session comparison tool
- Export to PDF/HTML formats
- Team collaboration features

## Contributing

This tool is designed to be extended. Contributions welcome for:
- Additional AI model integrations
- Enhanced token tracking methods
- CLI tool implementation
- VSCode extension
- Documentation improvements

## License

MIT License - Feel free to use and modify for your needs.

## Support

For issues or questions:
1. Check the `.sessions/INSTRUCTIONS.md` file
2. Review example sessions in `.sessions/`
3. Ensure git is properly initialized
4. Verify AI assistant can read files and execute commands

---

**Version**: 1.0.0  
**Last Updated**: 2026-06-04
