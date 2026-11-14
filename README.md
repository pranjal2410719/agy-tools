# agy-tools

> Safety wrapper and tooling for the [Antigravity CLI (agy)](https://github.com/antigravity-ai/agy)

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Shell: Bash](https://img.shields.io/badge/shell-bash-green.svg)]()
[![Platform: Linux/macOS/WSL](https://img.shields.io/badge/platform-Linux%20%7C%20macOS%20%7C%20WSL-blue.svg)]()

## Overview

`agy-tools` provides a secure wrapper script (`agy-auto`) that adds essential safety guardrails around the Antigravity CLI (`agy`). It enforces project boundaries, enables sandboxing by default, and prevents accidental execution in unsafe contexts.

### Why use agy-auto?

| Without agy-auto | With agy-auto |
|------------------|---------------|
| Runs from any directory | Refuses `/`, `/home`, `$HOME` |
| No sandbox by default | Auto-enables `--sandbox --add-dir` |
| No directory confinement | Ensures cwd ⊆ project root |
| `--dangerously-skip-permissions` works | Blocked unless `AGY_ALLOW_DANGEROUS_SKIP=1` |
| Hardcoded binary path | Flexible detection (env → PATH → relative) |

## Quick Start

```bash
# 1. Clone
git clone https://github.com/pranjal2410719/agy-tools.git ~/agy-tools

# 2. Install agy binary (one-time)
curl -fsSL https://github.com/antigravity-ai/agy/releases/latest/download/agy-linux-amd64 -o /tmp/agy
sudo mv /tmp/agy /usr/local/bin/agy
chmod +x /usr/local/bin/agy

# 3. Link wrapper to PATH
sudo ln -sf ~/agy-tools/agy-auto /usr/local/bin/agy-auto

# 4. Use from any project
cd /your/project
agy-auto "your prompt"
```

## Files in This Repository

| File | Description |
|------|-------------|
| `agy-auto` | Main wrapper script (executable bash) |
| `INSTALL.md` | Detailed installation guide |
| `MASTER_SETUP_PROMPT.md` | AI-agent-ready setup prompt |
| `README.md` | This file |
| `LICENSE` | MIT license |
| `CHANGELOG.md` | Version history |
| `CONTRIBUTING.md` | Contribution guidelines |

## Features

### Project Root Validation
Prevents execution from dangerous locations:
- `/` (root filesystem)
- `/home` (all users)
- `$HOME` (user home directory)

```
$ cd /home && agy-auto --help
agy-auto: refusing broad project root: /home
agy-auto: run from a project directory or set AGY_PROJECT_ROOT=/path/to/project
```

### Automatic Sandboxing
Every command runs with:
```bash
--sandbox --add-dir "$PROJECT_ROOT"
```

### Directory Confinement
Ensures current working directory is within the project root:
```
$ cd /tmp && AGY_PROJECT_ROOT=~/project agy-auto --help
agy-auto: current directory is outside project root
  cwd:          /tmp
  project root: /home/user/project
```

### Permission Guard
Blocks `--dangerously-skip-permissions` unless explicitly allowed:
```bash
# Blocked by default
agy-auto --dangerously-skip-permissions "prompt"
# agy-auto: refusing --dangerously-skip-permissions

# Allowed with env var
AGY_ALLOW_DANGEROUS_SKIP=1 agy-auto --dangerously-skip-permissions "prompt"
```

### Flexible Binary Detection
The wrapper finds `agy` in priority order:
1. `AGY_BIN` environment variable
2. `which agy` (PATH lookup)
3. Relative to wrapper: `$(dirname "$0")/agy`

## Configuration

### Environment Variables

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `AGY_BIN` | No | Auto-detected | Path to agy binary |
| `AGY_PROJECT_ROOT` | No | `$PWD` | Project root directory |
| `AGY_ALLOW_DANGEROUS_SKIP` | No | `0` | Allow `--dangerously-skip-permissions` |

### Shell Configuration (Optional)

Add to `~/.bashrc`, `~/.zshrc`, or `~/.profile`:

```bash
# If agy is not in PATH
export AGY_BIN=/usr/local/bin/agy

# Enable dangerous skip globally (not recommended)
# export AGY_ALLOW_DANGEROUS_SKIP=1

# Default project root (optional)
# export AGY_PROJECT_ROOT=/path/to/default/project
```

## Usage Examples

### Basic Usage
```bash
cd /my/project
agy-auto "refactor the user service"
```

### With Model Selection
```bash
agy-auto --model gpt-4 --effort high "complex task"
```

### Continue Previous Conversation
```bash
agy-auto --continue
agy-auto --conversation abc-123-def
```

### Interactive Mode
```bash
agy-auto -i
agy-auto --prompt-interactive "start with this"
```

### Print Mode (Non-interactive)
```bash
agy-auto -p "one-shot prompt"
agy-auto --print --output-format json "structured output"
```

### With Explicit Project Root
```bash
AGY_PROJECT_ROOT=/home/user/my-project agy-auto "prompt"
```

### Dangerous Skip (Use with Caution)
```bash
AGY_ALLOW_DANGEROUS_SKIP=1 agy-auto --dangerously-skip-permissions "prompt"
```

## Project Structure Recommendation

```
your-project/
├── agy-auto          # Symlink or copy of wrapper
├── agy               # Binary (if not in PATH)
├── .agy/             # Project config (optional)
├── src/              # Your source code
├── tests/            # Tests
└── README.md         # Your project docs
```

## Installation Methods

### Method 1: System-wide (Recommended)
```bash
git clone https://github.com/pranjal2410719/agy-tools.git ~/agy-tools
sudo ln -sf ~/agy-tools/agy-auto /usr/local/bin/agy-auto
# Install agy to /usr/local/bin/agy
```

### Method 2: User-local
```bash
git clone https://github.com/pranjal2410719/agy-tools.git ~/agy-tools
mkdir -p ~/.local/bin
ln -sf ~/agy-tools/agy-auto ~/.local/bin/agy-auto
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

### Method 3: Project-local (No PATH required)
```bash
# In each project
curl -fsSL https://raw.githubusercontent.com/pranjal2410719/agy-tools/main/agy-auto -o agy-auto
chmod +x agy-auto
# Place agy binary next to it or set AGY_BIN
./agy-auto "prompt"
```

### Method 4: AI-Agent Automated Setup
Use the [`MASTER_SETUP_PROMPT.md`](MASTER_SETUP_PROMPT.md) with any AI agent.

## Troubleshooting

| Error | Cause | Fix |
|-------|-------|-----|
| `could not find agy binary` | agy not in PATH, AGY_BIN not set | Install agy to PATH or `export AGY_BIN=/path/to/agy` |
| `refusing broad project root` | Running from `/`, `/home`, `$HOME` | `cd /your/project` or set `AGY_PROJECT_ROOT` |
| `current directory is outside project root` | cwd not in project root | `cd` into project or set `AGY_PROJECT_ROOT` |
| `Permission denied` | Wrapper not executable | `chmod +x agy-auto` |
| `Antigravity CLI is not executable` | agy binary not executable | `chmod +x /path/to/agy` |

## Requirements

- **Bash** 4.0+ (Linux, macOS, WSL)
- **realpath** command (coreutils)
- **agy** binary (Antigravity CLI)

## Compatibility

| OS | Status |
|----|--------|
| Linux (glibc) | ✅ Tested |
| macOS (Darwin) | ✅ Compatible |
| WSL 1/2 | ✅ Compatible |
| FreeBSD | ⚠️ Untested |
| Windows (native) | ❌ Use WSL |

## Related Projects

- [Antigravity CLI (agy)](https://github.com/antigravity-ai/agy) - The CLI this wraps
- [Agent Manager](https://github.com/kilocode/agent-manager) - Multi-agent orchestration

## License

MIT License - see [LICENSE](LICENSE) for details.

## Support

- **Issues**: [GitHub Issues](https://github.com/pranjal2410719/agy-tools/issues)
- **Discussions**: [GitHub Discussions](https://github.com/pranjal2410719/agy-tools/discussions)
- **agy CLI Issues**: [antigravity-ai/agy](https://github.com/antigravity-ai/agy/issues)

---

**Made with ❤️ for safer AI-assisted development**