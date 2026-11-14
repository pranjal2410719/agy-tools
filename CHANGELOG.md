# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added
- Initial release of agy-auto wrapper
- Flexible agy binary detection (AGY_BIN env var → PATH → relative)
- Project root validation (refuses /, /home, $HOME)
- Directory confinement (ensures cwd ⊆ project root)
- Automatic sandboxing (--sandbox --add-dir)
- Permission guard for --dangerously-skip-permissions
- Comprehensive documentation (README.md, INSTALL.md, MASTER_SETUP_PROMPT.md)
- MIT License

### Changed
- N/A (initial release)

### Deprecated
- N/A

### Removed
- N/A

### Fixed
- N/A

### Security
- N/A

## [1.0.0] - 2024-11-14

### Added
- Initial public release
- agy-auto wrapper script with all safety features
- INSTALL.md - detailed installation guide
- MASTER_SETUP_PROMPT.md - AI-agent-ready setup prompt
- README.md - comprehensive documentation
- LICENSE - MIT license
- CHANGELOG.md - this file

---

## Version History

| Version | Date | Description |
|---------|------|-------------|
| 1.0.0 | 2024-11-14 | Initial release |

---

## Release Notes Template

### [X.Y.Z] - YYYY-MM-DD

#### Added
- New features

#### Changed
- Changes in existing functionality

#### Deprecated
- Soon-to-be removed features

#### Removed
- Now removed features

#### Fixed
- Bug fixes

#### Security
- Vulnerability fixes