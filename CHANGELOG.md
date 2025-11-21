# Changelog

All notable changes to the OmniFetch plugin will be documented in this file.

## [0.1.28] - 2025-11-21

### Added
- Initial public release
- Power monitoring with cost tracking
- Hardware sensors integration (lm-sensors)
- GPU monitoring support
- VM snapshot management
  - Create snapshots with memory state
  - Delete snapshots with cleanup
  - Revert snapshots (removes after revert)
- Unraid WebUI snapshot compatibility
- System management operations (shutdown, reboot)
- Extended GraphQL API

### Fixed
- Snapshot database array vs object handling
- Snapshot filename generation for extensionless files
- External snapshot revert with proper VM state restoration

### Technical
- External QCOW2 snapshots with live memory capture
- Automatic Unraid snapshot database synchronization
- Proper backing chain management
- Command injection protection with shell escaping

## Version Format

This project uses [Semantic Versioning](https://semver.org/):
- **MAJOR.MINOR.PATCH** (e.g., 0.1.28)
- MAJOR: Breaking changes
- MINOR: New features (backwards compatible)
- PATCH: Bug fixes (backwards compatible)
