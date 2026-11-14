# agy-auto Installation Guide

## Overview
`agy-auto` is a safety wrapper for the `agy` (Antigravity CLI) that adds project root validation, sandboxing, and directory confinement before executing commands.

## Prerequisites
- `agy` binary must be installed and executable
- Bash shell (Linux/macOS/WSL)

## Installation Options

### Option 1: Clone and Use Directly
```bash
git clone https://github.com/yourusername/agy-tools.git
cd agy-tools
chmod +x agy-auto
```

### Option 2: Install to PATH
```bash
# Copy to a directory in your PATH
sudo cp agy-auto /usr/local/bin/agy-auto
# OR for user-only
mkdir -p ~/.local/bin
cp agy-auto ~/.local/bin/agy-auto
chmod +x ~/.local/bin/agy-auto
```

## How agy-auto Finds the agy Binary

The wrapper searches for `agy` in this order:

1. **`AGY_BIN` environment variable** (highest priority)
   ```bash
   export AGY_BIN=/custom/path/to/agy
   agy-auto your-command
   ```

2. **PATH lookup** via `which agy`
   ```bash
   # If agy is installed in a PATH directory
   agy-auto your-command
   ```

3. **Relative to the wrapper script location**
   ```bash
   # Project structure:
   /my-project/
     agy-auto    <-- wrapper
     agy         <-- binary
   cd /my-project
   ./agy-auto your-command
   ```

## Configuration

### Environment Variables
| Variable | Description | Default |
|----------|-------------|---------|
| `AGY_BIN` | Path to agy binary | Auto-detected |
| `AGY_PROJECT_ROOT` | Project root directory | Current directory |
| `AGY_ALLOW_DANGEROUS_SKIP` | Allow `--dangerously-skip-permissions` | `0` (disabled) |

### Setting AGY_BIN permanently
Add to your shell config (`.bashrc`, `.zshrc`, etc.):
```bash
export AGY_BIN=/usr/local/bin/agy
# OR
export AGY_BIN=/opt/agy/agy
```

## Usage

```bash
# Run from your project directory
cd /path/to/your/project
agy-auto <agy-flags>...

# Example with flags
agy-auto --sandbox --add-dir /my/project --model gpt-4 "your prompt"

# With dangerous skip (requires AGY_ALLOW_DANGEROUS_SKIP=1)
AGY_ALLOW_DANGEROUS_SKIP=1 agy-auto --dangerously-skip-permissions "prompt"

# Specify project root explicitly
AGY_PROJECT_ROOT=/path/to/project agy-auto "prompt"
```

## Safety Features

- **Project root validation**: Refuses to run from `/`, `/home`, or `$HOME` directly
- **Directory confinement**: Ensures current directory is within project root
- **Sandbox mode**: Adds `--sandbox --add-dir "$PROJECT_ROOT"` automatically
- **Permission guard**: Blocks `--dangerously-skip-permissions` unless explicitly allowed

## Troubleshooting

### "agy-auto: could not find agy binary"
```bash
# Solution 1: Install agy to PATH
sudo mv agy /usr/local/bin/agy
chmod +x /usr/local/bin/agy

# Solution 2: Set AGY_BIN
export AGY_BIN=/path/to/your/agy
```

### "agy-auto: refusing broad project root"
```bash
# Run from a project subdirectory
cd /path/to/your/project
agy-auto ...

# OR set project root explicitly
AGY_PROJECT_ROOT=/path/to/project agy-auto ...
```

### "agy-auto: current directory is outside project root"
```bash
# Change to a directory within your project
cd /path/to/project/subdir
agy-auto ...
```

## Project Structure Recommendation

```
your-project/
├── agy-auto      # This wrapper
├── agy           # The agy binary (optional, if not in PATH)
├── .agy/         # Project config (optional)
└── src/          # Your code
```

## Updating

```bash
cd /path/to/agy-tools
git pull
chmod +x agy-auto
```

## License
MIT - See LICENSE file for details.