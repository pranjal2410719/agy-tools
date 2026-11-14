# Master Setup Prompt for agy-auto Wrapper

## Purpose
This prompt enables an AI agent to automatically set up the `agy-auto` wrapper on any new device with zero manual intervention.

---

## Master Setup Prompt

```
You are setting up the agy-auto wrapper for the Antigravity CLI (agy) on a new device.

## REPOSITORY
Clone from: https://github.com/pranjal2410719/agy-tools

## REQUIRED OUTCOME
After setup, the user should be able to run:
```bash
cd /any/project
agy-auto "your prompt"
```
And have it work with all safety features (project root validation, sandboxing, directory confinement).

## SETUP STEPS

### 1. Clone Repository
```bash
git clone https://github.com/pranjal2410719/agy-tools.git ~/agy-tools
cd ~/agy-tools
chmod +x agy-auto
```

### 2. Install the `agy` Binary
The wrapper needs the `agy` binary. Choose ONE method:

**Method A: Install to PATH (recommended)**
```bash
# Download latest agy release
# Check: https://github.com/antigravity-ai/agy/releases
# Or install via package manager if available

# Example for Linux:
curl -fsSL https://github.com/antigravity-ai/agy/releases/latest/download/agy-linux-amd64 -o /tmp/agy
sudo mv /tmp/agy /usr/local/bin/agy
chmod +x /usr/local/bin/agy

# Verify
agy --version
```

**Method B: Set AGY_BIN environment variable**
```bash
# If agy is installed elsewhere
echo 'export AGY_BIN=/path/to/your/agy' >> ~/.bashrc
source ~/.bashrc
```

**Method C: Place agy next to wrapper (project-local)**
```bash
# For each project, copy agy binary to project root
cp /path/to/agy /your/project/agy
```

### 3. Install Wrapper to PATH (optional but recommended)
```bash
# System-wide
sudo ln -sf ~/agy-tools/agy-auto /usr/local/bin/agy-auto

# OR user-only
mkdir -p ~/.local/bin
ln -sf ~/agy-tools/agy-auto ~/.local/bin/agy-auto
# Ensure ~/.local/bin is in PATH
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

### 4. Verify Installation
```bash
# Test from a project directory
mkdir -p ~/test-project
cd ~/test-project
agy-auto --help

# Should show agy usage without errors
# Should NOT show "could not find agy binary" or "refusing broad project root"
```

### 5. Test Safety Features
```bash
# Test project root validation (should fail)
cd /tmp
agy-auto --help 2>&1 | grep -q "refusing broad project root" && echo "✓ Root validation works"

# Test with explicit project root
AGY_PROJECT_ROOT=~/test-project agy-auto --help 2>&1 | head -1
```

## ENVIRONMENT VARIABLES (Document for User)
Add to shell config if needed:
```bash
# ~/.bashrc or ~/.zshrc
export AGY_BIN=/usr/local/bin/agy          # Only if agy not in PATH
export AGY_ALLOW_DANGEROUS_SKIP=0          # Default: blocked
# export AGY_PROJECT_ROOT=/path/to/project  # Optional: override auto-detection
```

## TROUBLESHOOTING CHECKLIST
If `agy-auto` fails:
- [ ] `agy` binary exists and is executable (`which agy` or `$AGY_BIN`)
- [ ] Running from within a project directory (not `/`, `/home`, `$HOME`)
- [ ] Current directory is inside `AGY_PROJECT_ROOT` (or PWD)
- [ ] Wrapper has execute permission (`chmod +x agy-auto`)

## SUCCESS CRITERIA
✅ `agy-auto --help` shows agy usage
✅ `cd /tmp && agy-auto --help` fails with "refusing broad project root"
✅ `AGY_PROJECT_ROOT=~/project agy-auto --help` works
✅ User can run `agy-auto "prompt"` from any project directory

## NOTES FOR AI AGENT
- Do NOT hardcode paths like `/home/dev/.local/bin/agy`
- Use the detection logic in the wrapper (AGY_BIN → PATH → relative)
- The wrapper is at `~/agy-tools/agy-auto` after clone
- The INSTALL.md in the repo has full documentation
- Repository: https://github.com/pranjal2410719/agy-tools
```

---

## Quick One-Liner for AI Agents

```
Clone https://github.com/pranjal2410719/agy-tools, install agy binary to PATH (or set AGY_BIN), make agy-auto executable and optionally link to PATH, verify with `agy-auto --help` from a project directory.
```

---

## Minimal Setup (Copy-Paste Ready)

```bash
# 1. Clone
git clone https://github.com/pranjal2410719/agy-tools.git ~/agy-tools

# 2. Install agy (adjust URL for your OS/arch)
curl -fsSL https://github.com/antigravity-ai/agy/releases/latest/download/agy-linux-amd64 -o /tmp/agy
sudo mv /tmp/agy /usr/local/bin/agy
chmod +x /usr/local/bin/agy

# 3. Link wrapper
sudo ln -sf ~/agy-tools/agy-auto /usr/local/bin/agy-auto

# 4. Test
mkdir -p ~/my-project && cd ~/my-project
agy-auto --help
```