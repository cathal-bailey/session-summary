# AI Session Summary Generation Instructions

This document provides step-by-step instructions for AI models to generate comprehensive session summaries. These instructions are model-agnostic and designed to work with any AI assistant capable of reading files and executing commands.

---

## Overview

When a developer says **"complete session summary"** or **"generate session summary"**, follow these instructions to create a detailed summary of the AI-assisted development session.

---

## Step-by-Step Process

### Step 1: Read Configuration and Template

1. Read `.sessions/config.json` to understand token thresholds and settings
2. Read `.sessions/TEMPLATE.md` to understand the expected output format
3. Read `.sessions/schema.json` to understand the JSON structure requirements

### Step 2: Determine Session Metadata

Gather the following information:

- **Session Name**: Ask the developer for a 1-2 word descriptive name, or infer from the main task
- **Timestamp**: Use current date/time in ISO-8601 format
- **Duration**: Calculate from session start (if tracked) or estimate based on conversation
- **AI Model**: Identify yourself if possible (e.g., "GPT-4", "Claude-3.5", "Gemini"), otherwise use "unknown"
- **Token Count**: Check if `.sessions/.session-state.json` exists and read token count, or estimate based on conversation length

### Step 3: Analyze Git Changes (If Applicable)

**Check if git is available and initialized:**

```bash
# Check if git repository exists
git rev-parse --git-dir 2>/dev/null
```

**If git is available**, execute the following commands to detect file changes:

```bash
# Get list of all changed files since last commit
git status --short

# Get detailed diff of all changes
git diff

# Get list of untracked (new) files
git ls-files --others --exclude-standard

# Get diff including staged changes
git diff HEAD
```

**If git is NOT available or NOT initialized:**
- Skip git commands entirely
- Note in the summary: "Git not available - file changes tracked via conversation analysis only"
- Rely solely on conversation history to identify files discussed
- Mark this limitation in the "Additional Notes" section

**For non-coding sessions** (e.g., PR explanations, concept discussions, architecture reviews):
- Git changes may not be relevant
- Focus on concepts covered and knowledge shared
- File sections can note: "No files were modified - this was a discussion/review session"
- Still capture valuable information about what was learned or reviewed

### Step 4: Determine Session Type

Identify what type of session this was:

**Coding Session**: Files were created, modified, or deleted
- Use git diffs for detailed changes
- Focus on implementation details
- Include code snippets and technical explanations

**Discussion/Review Session**: No code changes, but concepts were explained
- Examples: PR explanations, architecture reviews, concept learning
- Focus on knowledge transfer and understanding
- Emphasize concepts covered and references
- File sections note: "No files modified - discussion/review session"

**Mixed Session**: Both coding and discussion
- Document both aspects appropriately
- Balance technical details with conceptual explanations

### Step 5: Self-Reflection on Conversation

Review the entire conversation history and extract:

#### Tasks Completed (Max 5)
- What specific tasks were accomplished?
- What problems were solved?
- What features were implemented?
- Keep it concise - one line per task

#### Concepts Covered (Max 10)
For each concept discussed:
- **Name**: Clear, concise title
- **Description**: Explain from foundational level, building up complexity
- **References**: Include any external resources mentioned:
  - Website links (documentation, tutorials)
  - GitHub repositories referenced
  - Peer-reviewed papers (only if relevant and cited)
- **Difficulty Level**: Beginner, Intermediate, or Advanced

**Ordering**: Start with most basic concepts, progress to more advanced

#### Files Accessed
Categorize all files into:

1. **Read Only**: Files examined but not modified
   - Include the purpose of reading each file
   
2. **Modified**: Files that were changed
   - Brief summary of what changed
   - Why the change was necessary
   
3. **Created**: New files added
   - Purpose of each new file
   
4. **Deleted**: Files removed
   - Reason for deletion

**Important**: If a category is empty, explicitly state "No files were [read/modified/created/deleted] during this session."

### Step 5: Generate Detailed File Changes

For each modified file:

1. **Extract the git diff** for that specific file
2. **Summarize the changes** in plain language
3. **Explain the reason** for the changes
4. **Include the diff output** in a code block with diff syntax highlighting
5. **Count lines changed** (+X, -Y format)

Example:
```diff
@@ -10,5 +10,8 @@ function example() {
-  return null;
+  if (!data) {
+    throw new Error('Data required');
+  }
+  return data;
```

### Step 6: Identify Prerequisites

List what someone would need to know or have to continue this work:

- Required knowledge or concepts
- Dependencies or tools needed
- Current state of the work
- Any setup or configuration required
- Context about decisions made

### Step 7: Suggest Next Steps

Based on the work completed, suggest logical next actions:

- Remaining tasks from the current feature
- Testing or validation needed
- Documentation updates required
- Related improvements or optimizations
- Follow-up questions or investigations

### Step 8: Generate Output Files

Create two files in the `.sessions/` directory:

#### Markdown File: `session-[name]-YYYY-MM-DD-HHMMSS.md`

Follow the structure in `TEMPLATE.md` exactly. Replace all placeholder text with actual content from the session.

**Naming Convention**:
- `[name]`: 1-2 word description (lowercase, hyphen-separated)
- `YYYY-MM-DD`: Current date
- `HHMMSS`: Current time (24-hour format)

Example: `session-auth-refactor-2026-06-04-135500.md`

#### JSON File: `session-[name]-YYYY-MM-DD-HHMMSS.json`

Follow the structure in `schema.json` exactly. Ensure all required fields are present and properly formatted.

Example: `session-auth-refactor-2026-06-04-135500.json`

### Step 9: Update Session Index

If `.sessions/index.json` exists, append the new session entry. If it doesn't exist, create it with the first entry.

Structure:
```json
{
  "sessions": [
    {
      "sessionId": "session-auth-refactor-2026-06-04-135500",
      "sessionName": "auth-refactor",
      "timestamp": "2026-06-04T13:55:00Z",
      "duration": "45 minutes",
      "tokenCount": 45000,
      "filesModified": 3,
      "filesCreated": 1,
      "tasksCompleted": 4
    }
  ],
  "lastUpdated": "2026-06-04T13:55:00Z"
}
```

### Step 10: Clean Up Session State

If `.sessions/.session-state.json` exists, you may archive it or reset it for the next session (optional).

---

## Token Monitoring Integration (Opt-In)

Token monitoring is **disabled by default**. Check configuration before proceeding.

### Check if Token Monitoring is Enabled

1. Read `.sessions/config.json`
2. Check `tokenMonitoring.enabled` field
3. If `false`, skip all token monitoring steps
4. If `true`, proceed with monitoring

### When Token Monitoring is Enabled

**Check Token Threshold:**

1. Read current token count from `.sessions/.session-state.json` (if exists)
2. Read threshold from `.sessions/config.json`
3. Calculate percentage: `(currentTokens / threshold) * 100`

**Automatic Notifications (if `autoNotify` is true):**

- **At 70% threshold** (if `notifyAtWarning` is true):
  ```
  ⚠️ Token Usage: 70% of threshold reached (70,000/100,000 tokens).
  Consider wrapping up soon. Say "complete session summary" when ready.
  ```

- **At 85% threshold** (if `notifyAtCritical` is true):
  ```
  ⚠️ CRITICAL: 85% of token threshold reached (85,000/100,000 tokens).
  Strongly recommend generating session summary now.
  Would you like me to create the summary?
  ```

**Manual Token Checks (always available when enabled):**

Users can ask:
- "How many tokens have we used?"
- "What's our token count?"
- "Check token usage"

### Include in Summary

Always add token information to the summary header (whether monitoring is enabled or not):
```markdown
**Token Count**: 45,000 tokens (45% of threshold) [Monitoring: Disabled]
```

Or if monitoring was enabled:
```markdown
**Token Count**: 45,000 tokens (45% of threshold)
```

### Threshold Status Messages

If token count is available, include status:

- **< 70%**: "Token usage is within normal range."
- **70-85%**: "⚠️ Approaching token threshold. Consider wrapping up current task."
- **85-100%**: "⚠️ Critical token threshold reached. Strongly recommend ending session."
- **> 100%**: "🚨 Token threshold exceeded. Session summary required before continuing."

### Enabling Token Monitoring

To enable token monitoring, users should:

1. Edit `.sessions/config.json`
2. Set `tokenMonitoring.enabled` to `true`
3. Optionally configure notification preferences:
   - `autoNotify`: Enable automatic threshold notifications
   - `notifyAtWarning`: Notify at 70% threshold
   - `notifyAtCritical`: Notify at 85% threshold

**Example Configuration:**
```json
{
  "tokenMonitoring": {
    "enabled": true,
    "autoNotify": true,
    "notifyAtWarning": true,
    "notifyAtCritical": true
  }
}
```

### Command Flags (for future CLI tool)

When CLI tool is implemented, support flags:
- `--enable-token-monitoring` - Enable for this session
- `--disable-token-monitoring` - Disable for this session
- `--check-tokens` - Check current token usage

---

## Quality Guidelines

### Be Accurate
- Only include information from the actual conversation
- Don't invent or assume details not discussed
- If uncertain, state "Details not available" or "Not discussed in this session"

### Be Concise
- Keep task descriptions to one line each
- Summarize concepts clearly but briefly
- Focus on what's essential for continuation

### Be Complete
- Don't skip sections - if empty, say so explicitly
- Include all file changes, even minor ones
- Capture all concepts discussed, even briefly

### Be Helpful
- Write for a developer who will read this later
- Explain "why" not just "what"
- Provide context for decisions made
- Make it easy to resume work

### Be Consistent
- Follow the template structure exactly
- Use consistent formatting
- Match the schema for JSON output
- Use proper markdown syntax

---

## Example Commands

### Git Commands for File Analysis

```bash
# List all modified files
git diff --name-only

# Get diff for specific file
git diff path/to/file.ts

# List new untracked files
git ls-files --others --exclude-standard

# Get diff statistics
git diff --stat

# Show staged and unstaged changes
git diff HEAD
```

### Creating Output Files

Use your file writing capabilities to create:
1. The markdown summary file
2. The JSON summary file
3. Update the index.json file

---

## Error Handling

### If Git is Not Available
- Note in the summary: "Git not available - file changes tracked via conversation analysis"
- List files based on conversation history only
- Mark this limitation in the prerequisites section
- **This is perfectly fine for discussion/review sessions**

### If No Files Were Modified
- Explicitly state: "No files were modified during this session"
- This is normal for:
  - PR explanation sessions
  - Concept learning sessions
  - Architecture review sessions
  - Debugging discussions without code changes
- Focus the summary on concepts covered and knowledge gained
- Still provide value by documenting what was learned or discussed

### If Session State is Missing
- Estimate duration based on conversation length
- Note: "Token count estimated - no session state file found"
- Suggest initializing session tracking for future sessions

### If Information is Incomplete
- Be explicit about what's missing
- Don't fabricate information
- Suggest what would be needed to complete the summary

### For Non-Coding Sessions
- Don't force file changes where none exist
- Emphasize the value of the discussion/learning
- Capture key insights, decisions, or understandings gained
- Include references to external resources discussed
- Document questions answered and problems clarified
- These sessions are just as valuable as coding sessions!

---

## Model-Agnostic Considerations

These instructions are designed to work with any AI model. Adapt as needed:

- **File Access**: Use whatever file reading method your platform provides
- **Command Execution**: Use your platform's command execution capability
- **File Writing**: Use your platform's file writing method
- **Timestamps**: Use your platform's time/date functions

The key is following the structure and content guidelines, regardless of the specific tools available.

---

## Validation Checklist

Before finalizing the summary, verify:

- [ ] All sections from TEMPLATE.md are present
- [ ] Session name is descriptive and concise (1-2 words)
- [ ] Timestamps are in ISO-8601 format
- [ ] Tasks list has ≤ 5 items
- [ ] Concepts list has ≤ 10 items
- [ ] All file categories explicitly addressed (even if empty)
- [ ] Git diffs included for all modified files
- [ ] Prerequisites are clear and actionable
- [ ] Next steps are logical and helpful
- [ ] JSON output validates against schema.json
- [ ] Both .md and .json files created
- [ ] index.json updated
- [ ] Token count included (if available)

---

## Final Notes

**Purpose**: These summaries serve dual purposes:
1. Help developers understand and control AI-assisted changes
2. Enable session continuity when resuming work later

**Audience**: Write for the developer who will read this summary days or weeks later, possibly with a different AI model.

**Success Criteria**: A good summary allows someone to:
- Understand exactly what was done and why
- Continue the work without confusion
- Review changes for accuracy and appropriateness
- Learn from concepts covered

---

**Last Updated**: 2026-06-04