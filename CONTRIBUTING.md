# Contributing to agy-tools

Thank you for your interest in contributing! This document provides guidelines for contributing to the agy-tools project.

## Code of Conduct

By participating, you are expected to uphold our [Code of Conduct](CODE_OF_CONDUCT.md). Please report unacceptable behavior to the maintainers.

## How to Contribute

### Reporting Bugs

Before creating a bug report:
- Check existing [issues](https://github.com/pranjal2410719/agy-tools/issues)
- Test with the latest version
- Gather relevant information (OS, shell, agy version, error output)

**Bug Report Template:**
```markdown
**Environment:**
- OS: [e.g., Ubuntu 22.04, macOS 14, WSL2]
- Shell: [e.g., bash 5.1, zsh 5.9]
- agy version: [output of `agy --version`]
- agy-tools commit: [git commit hash]

**Steps to Reproduce:**
1. ...
2. ...
3. ...

**Expected Behavior:**
...

**Actual Behavior:**
...

**Error Output:**
```
<paste error here>
```

**Additional Context:**
...
```

### Suggesting Enhancements

Enhancement suggestions are welcome! Please:
- Check existing [discussions](https://github.com/pranjal2410719/agy-tools/discussions)
- Explain the use case and benefit
- Consider backward compatibility

### Pull Requests

1. **Fork** the repository
2. **Create a branch** from `main`: `git checkout -b feature/your-feature-name`
3. **Make changes** with clear, focused commits
4. **Test thoroughly** - see [Testing](#testing) below
5. **Update documentation** if needed
6. **Submit PR** with clear description

## Development Setup

```bash
# Clone your fork
git clone https://github.com/YOUR_USERNAME/agy-tools.git
cd agy-tools

# Add upstream
git remote add upstream https://github.com/pranjal2410719/agy-tools.git

# Make changes to agy-auto, documentation, etc.
# Test your changes
./agy-auto --help  # Should work from a project directory
```

## Testing

### Manual Testing Checklist

Run these tests before submitting:

```bash
# 1. Basic functionality
mkdir -p /tmp/test-project && cd /tmp/test-project
./agy-auto --help | head -1  # Should show "Usage of agy:"

# 2. Project root validation
cd /tmp
./agy-auto --help 2>&1 | grep -q "refusing broad project root" && echo "✓ Root validation"

# 3. Directory confinement
AGY_PROJECT_ROOT=/tmp/test-project ./agy-auto --help 2>&1 | grep -q "outside project root" && echo "✓ Confinement"

# 4. Explicit project root works
cd /tmp/test-project
AGY_PROJECT_ROOT=/tmp/test-project ./agy-auto --help | head -1

# 5. AGY_BIN override
AGY_BIN=/usr/local/bin/agy ./agy-auto --help | head -1

# 6. Dangerous skip blocked
./agy-auto --dangerously-skip-permissions 2>&1 | grep -q "refusing" && echo "✓ Skip blocked"

# 7. Dangerous skip allowed
AGY_ALLOW_DANGEROUS_SKIP=1 ./agy-auto --dangerously-skip-permissions --help | head -1
```

### Automated Testing (Future)

We plan to add automated tests. For now, manual testing is required.

## Coding Standards

### Shell Script Style

- Use `#!/usr/bin/env bash` shebang
- `set -euo pipefail` at top
- 2-space indentation
- Descriptive variable names (UPPER_SNAKE_CASE for globals)
- Quote all variables: `"$VAR"`
- Use `$(...)` not backticks
- Prefer `printf` over `echo` for portability
- Comment complex logic

### Example

```bash
#!/usr/bin/env bash
set -euo pipefail

# Find agy binary with fallback logic
AGY_BIN="${AGY_BIN:-}"
if [[ -z "$AGY_BIN" ]]; then
  AGY_BIN="$(which agy 2>/dev/null || true)"
fi
```

### Documentation Style

- Use Markdown
- Clear headings (##, ###)
- Code blocks with language tags
- Tables for structured data
- Links to relevant sections/files

## Commit Message Convention

Follow [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

Types:
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation only
- `style`: Formatting, no code change
- `refactor`: Code restructuring
- `test`: Adding tests
- `chore`: Maintenance

Examples:
```
feat: add support for custom sandbox directories
fix: handle missing realpath on macOS
docs: update INSTALL.md with WSL instructions
chore: update dependencies
```

## Review Process

1. Maintainers review PRs within 7 days
2. CI checks must pass (when available)
3. At least one approval required
4. Squash and merge to `main`

## Release Process

1. Update `CHANGELOG.md`
2. Tag release: `git tag v1.1.0`
3. Push tags: `git push origin --tags`
4. GitHub Actions creates release (when configured)

## Getting Help

- **Questions**: [GitHub Discussions](https://github.com/pranjal2410719/agy-tools/discussions)
- **Bugs**: [GitHub Issues](https://github.com/pranjal2410719/agy-tools/issues)
- **Security**: Email maintainers directly

## Recognition

Contributors will be acknowledged in:
- `CHANGELOG.md`
- GitHub contributors graph
- Release notes

Thank you for contributing! 🎉