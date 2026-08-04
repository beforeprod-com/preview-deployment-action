# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added
- `SHPR_VERSION` file recording the bundled CLI release tag (`shpr_v0.0.41`)

### Changed
- Bundled `shpr` binary updated to **`shpr_v0.0.41`** (Improved args handling for stop deployments)
- Bundled `shpr` binary updated to **`shpr_v0.0.40`** (Improved args handling for deployments)
- `action.yml`: `actions/github-script` **v7 → v9** (Node 24; clears Node 20 deprecation warnings)
- Test workflows and README examples: `actions/checkout@v7`, `actions/setup-go@v7`
- Rewrote root README (permissions, secrets, usage); slimmed `.github/README.md` to point at the root README

### Fixed
- (via `shpr_v0.0.40`) Preview deploy no longer panics on `shpr app start <platform> <folder>` without a third URL argument

### Notes
- Callers may set `SHPR_APP_ALIAS` on the preview step for distinct monorepo aliases; an `app_alias` action input is still a follow-up.

### Removed
- Removed separate cleanup-action.yml file
- Removed local action directory structure (.github/actions/)
- Removed duplicate action definitions
- Removed local shpr binary build in favor of distribution from werft repo releases (still local but put in from releases)
- Removed redundant Docker-related files and configurations

## [0.0.1] - 2024-04-28

### Added
- Preview URL logging when no PR exists for direct branch deployments
- Automatic PR description updates with deployment URLs
- Friendly footer with BeforeProd call-to-action in PR descriptions
- Support for composite action approach
- Improved error handling for URL extraction
- PR cleanup action for automatic deployment cleanup when PRs are closed
- Automatic extraction of deployment URLs from PR descriptions for cleanup
- New action directory structure for better organization and maintainability
- Separate cleanup workflow triggered on PR close events
- Automatic deployment URL extraction from PR descriptions for cleanup
- Independent binary management for each action
- Environment variables (BP_USER and BP_PASSWORD) for cleanup action

### Changed
- Improved PR description updates with retry mechanism and robust error handling
- Improved logging to show preview URL when no PR exists
- Converted from Docker-based to composite action
- Renamed step IDs from "shpr" to "beforeprod" for consistency
- Updated documentation to reflect new features and approach
- Improved workflow structure by removing redundant steps
- Reorganized actions into separate directories for better maintainability
- Updated workflow references to use new action directory structure
- Modified workflow triggers to separate deployment and cleanup events
- Updated README with comprehensive documentation for both actions
- Renamed deployment action to preview_app for better clarity
- Moved binary to action-specific directories for better version control

### Removed
- Docker-based execution approach
- Redundant URL and time output steps from workflow
- Dockerfile and entrypoint.sh as they're no longer needed

## [0.0.0] - 2024-04-14

### Added
- Initial release of the BeforeProd GitHub Action
- Support for Go applications
- Basic deployment functionality
- URL extraction from deployment output
- Secure credential handling through GitHub Secrets
