# Release Notes

## v5.0.0 (2026-03-05)

### Breaking Changes

**Forked from [obra/superpowers](https://github.com/obra/superpowers) — Claude Code exclusive**

This is a personal fork of Superpowers v4.3.1, stripped down to Claude Code only. All other platform support has been removed.

Removed:
- `.codex/` — Codex installation and support
- `.opencode/` — OpenCode plugin and installation
- `.cursor-plugin/` — Cursor plugin manifest
- `lib/skills-core.js` — OpenCode shared module
- `tests/opencode/` — OpenCode test suite
- `docs/README.codex.md`, `docs/README.opencode.md` — Platform-specific docs
- Cursor compatibility in SessionStart hook (`additional_context` field)

For the full upstream release history, see the [original repository](https://github.com/obra/superpowers/blob/main/RELEASE-NOTES.md).
