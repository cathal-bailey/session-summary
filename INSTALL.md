# Installation Guide

## Quick Install (Recommended)

### 1. Install the CLI Tool Globally

```bash
# Clone this repository
git clone https://github.com/yourusername/session-summary.git
cd session-summary

# Make the script executable (already done)
chmod +x session-summary

# Install globally (choose one method)

# Option A: Copy to /usr/local/bin (requires sudo)
sudo cp session-summary /usr/local/bin/

# Option B: Copy to ~/bin (no sudo needed)
mkdir -p ~/bin
cp session-summary ~/bin/
echo 'export PATH="$HOME/bin:$PATH"' >> ~/.bashrc  # or ~/.zshrc
source ~/.bashrc  # or source ~/.zshrc

# Option C: Create a symlink
sudo ln -s "$(pwd)/session-summary" /usr/local/bin/session-summary
```

### 2. Verify Installation

```bash
session-summary --version
# Should output: AI Session Summary Tool v1.0.0
```

### 3. Initialize in Your Project

```bash
# Navigate to any project directory
cd ~/my-project

# Copy the sessions directory from this repo
cp -r /path/to/session-summary/sessions .

# Or initialize (will show instructions)
session-summary --init
```

---

## Usage in Any Repository

Once installed, you can use the tool in ANY repository:

### Basic Usage

```bash
# In any project directory
cd ~/my-other-project

# Make sure sessions/ directory exists
cp -r /path/to/session-summary/sessions .

# Generate session summary
session-summary auth-refactor
```

This will output a formatted prompt that you copy and paste to your AI assistant.

### What Happens

1. **You run:** `session-summary auth-refactor`
2. **Tool outputs:** A formatted prompt for your AI
3. **You copy/paste** the prompt to your AI (Claude, ChatGPT, etc.)
4. **AI reads** `sessions/INSTRUCTIONS.md` and generates the summary
5. **AI creates** the summary files in `sessions/`

---

## Detailed Setup

### For Each New Project

When starting work in a new repository:

```bash
# 1. Navigate to your project
cd ~/my-new-project

# 2. Copy the sessions directory
cp -r /path/to/session-summary/sessions .

# 3. (Optional) Initialize git if not already done
git init

# 4. (Optional) Customize config
nano sessions/config.json

# 5. Start working with your AI assistant
# When done, run:
session-summary my-feature
```

### One-Time Setup Per Machine

```bash
# Create a template directory
mkdir -p ~/.session-summary-template
cp -r /path/to/session-summary/sessions ~/.session-summary-template/

# Create an alias for easy setup
echo 'alias init-sessions="cp -r ~/.session-summary-template/sessions ."' >> ~/.bashrc
source ~/.bashrc

# Now in any project:
cd ~/new-project
init-sessions
```

---

## Alternative: Manual Usage (No Installation)

If you don't want to install globally:

```bash
# In your project directory
/path/to/session-summary/session-summary auth-refactor
```

Or create a project-specific alias:

```bash
# In your project
alias ss="/path/to/session-summary/session-summary"

# Use it
ss auth-refactor
```

---

## Uninstallation

```bash
# Remove the global command
sudo rm /usr/local/bin/session-summary
# or
rm ~/bin/session-summary

# Remove from any projects
cd ~/my-project
rm -rf sessions
```

---

## Troubleshooting

### "Command not found"

Make sure the script is in your PATH:

```bash
# Check if ~/bin is in PATH
echo $PATH | grep "$HOME/bin"

# If not, add it
echo 'export PATH="$HOME/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

### "Tool not initialized"

You need the `sessions/` directory in your project:

```bash
cp -r /path/to/session-summary/sessions .
```

### "Permission denied"

Make the script executable:

```bash
chmod +x /path/to/session-summary
```

---

## Platform-Specific Notes

### macOS

```bash
# Use /usr/local/bin (recommended)
sudo cp session-summary /usr/local/bin/

# Or use ~/bin
mkdir -p ~/bin
cp session-summary ~/bin/
echo 'export PATH="$HOME/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

### Linux

```bash
# Use /usr/local/bin
sudo cp session-summary /usr/local/bin/

# Or use ~/.local/bin
mkdir -p ~/.local/bin
cp session-summary ~/.local/bin/
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

### Windows (Git Bash / WSL)

```bash
# Use ~/bin
mkdir -p ~/bin
cp session-summary ~/bin/
echo 'export PATH="$HOME/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

---

## Next Steps

After installation:

1. ✅ Verify: `session-summary --version`
2. ✅ Copy `sessions/` to your project
3. ✅ Work with your AI assistant
4. ✅ Run: `session-summary [name]`
5. ✅ Copy output and paste to AI
6. ✅ Review generated summary

See [README.md](README.md) for full documentation.