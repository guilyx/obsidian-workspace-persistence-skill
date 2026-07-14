# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]

## [0.1.0] - 2026-07-14

### Added

- `saving-obsidian-context` Cursor Agent Skill — persist knowledge to Obsidian via official CLI
- Vault resolution chain: project config → env vars → CWD → active vault → `vaults verbose`
- CLI reference, vault layout guide, and note templates under `.cursor/skills/saving-obsidian-context/`
- `.agents/obsidian-context.example.json` per-project configuration template
- `PLAN.md` architecture document

### Changed

- Expanded `README.md` with setup and branch workflow
- Branch model: `develop` for integration PRs, `main` for releases (tagged from `main`)
- Merged `develop` into `main` for first release
