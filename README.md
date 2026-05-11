# CLAUDE_CONFIG

Personal [Claude Code](https://docs.anthropic.com/en/docs/claude-code) configuration: settings, hooks, skills, agents, memory system, and shell integrations.

## Install

```bash
git clone git@github.com:Nyiuu/CLAUDE_CONFIG.git ~/.claude
```

> Prerequisites: `claude`, `git`, `jq`, `uv`, `node`, `pnpm`.

Re-run `git pull --ff-only` in `~/.claude` to update.

## Private settings

`settings.json` is tracked — it contains hooks, plugins, statusLine, and preferences but **no API keys or model config**. Those live in `settings.local.json` (gitignored), along with `permissions`.

Copy the template and fill in your keys:

```bash
cp ~/.claude/providers/deepseek.json ~/.claude/settings.local.json
# edit settings.local.json — set ANTHROPIC_AUTH_TOKEN, model names, etc.
```

## Optional next steps

```bash
# Wire the shell integration into your rc file (detects your shell, idempotent).
bash ~/.claude/integration-install.sh

# Tally the modern CLI tools preferred by CLAUDE.md against what's installed.
claude "which CLI tools in ~/.claude/CLAUDE.md am I missing?"
```

## What's included

- **settings.json** — hooks, plugins, statusLine, UI preferences (public; no keys)
- **hooks/** — guardrails for safe tool use (block heredocs, enforce Write tool, destructive-op warnings, audit-edits on Stop)
- **skills/** — skill packs (browser automation, translation, shader dev, ast-grep, etc.)
- **agents/** — custom sub-agents (audit, code review, doc review, web research)
- **providers/** — API endpoint templates (deepseek, glm, gpt, openrouter, qwen)
- **memory/** — long-term memory system for Claude (BUILD.md, pages, distill/promote scripts)
- **CLAUDE.md** — global coding preferences and CLI tool rules
- **integration.sh / .fish** — `claude` wrapper, model shortcuts, and `commit` helper
- **integration-providers.sh / .fish** — optional shortcuts that route claude through third-party Anthropic-compatible endpoints
- **statusline.sh** — custom tmux-compatible status line renderer

## Defaults

**`permissions.defaultMode: "bypassPermissions"`** (in `settings.local.json`) — tool calls run without confirmation prompts. Safety lives in `hooks/no-*.sh`: guards against `rm -rf /`, writes to `/dev`/`/etc`/`/proc`/`/sys`, force-push, `git --amend`, `crontab -r`, `killall`, recursive chmod/chown, etc.

To restore standard prompts: set `"defaultMode": "default"` in `settings.local.json`.

## Audit Hook

The Stop hook (`hooks/audit-edits.py`) fires after each response to review for correctness and AI slop patterns. It can run Claude and/or Codex headless; issues are reported back for fixing.

```bash
# View audit history
~/.claude/hooks/audit-edits.py stats
```

Configure via `"AUDIT_BACKEND"` in `settings.local.json`: `none`, `claude`, `codex`, or `both`.
