# Obsidian Workspace Persistence Skill

A Cursor Agent Skill that persists institutional memory — research, decisions, learnings, session logs — to your **Obsidian vault** via the official CLI, so knowledge survives across conversations.

Inspired by [saving-workspace-context](https://github.com/anthropics/skills/tree/main/skills/saving-workspace-context); this variant routes durable context through Obsidian instead of repo `context/` files.

## Requirements

- [Obsidian](https://obsidian.md/) desktop **1.12.4+**
- CLI enabled: **Settings → General → Command line interface → Register CLI**
- Obsidian running when the agent writes (CLI connects to the live app)

Verify:

```bash
obsidian version
obsidian vaults verbose
```

## Install

Copy or submodule this repo, or copy `.cursor/skills/saving-obsidian-context/` into your project.

The skill auto-applies when the agent detects durable context worth saving. Invoke manually with `/saving-obsidian-context`.

## Setup (one time per project)

1. Copy the example config:

   ```bash
   cp .agents/obsidian-context.example.json .agents/obsidian-context.json
   ```

2. Set your vault name (from `obsidian vaults verbose`) and project paths:

   ```json
   {
     "vault": { "name": "Cloud" },
     "projectSlug": "my-app",
     "basePath": "Projects/my-app"
   }
   ```

3. Optional env vars (override config):

   - `OBSIDIAN_VAULT` — vault name
   - `OBSIDIAN_VAULT_PATH` — absolute vault path
   - `OBSIDIAN_CONTEXT_BASE_PATH` — e.g. `Projects/my-app`

## Vault layout

```
{vault}/Projects/{project-slug}/
  project-context.md
  context/{topic}.md
  logs/{YYYY-MM-DD}/{entry}.md
  templates/
```

See [PLAN.md](PLAN.md) for architecture and [vault-layout reference](.cursor/skills/saving-obsidian-context/references/vault-layout.md).

## Skill contents

| Path | Purpose |
|------|---------|
| `.cursor/skills/saving-obsidian-context/SKILL.md` | Agent instructions |
| `references/obsidian-cli.md` | CLI cheat sheet |
| `references/vault-layout.md` | Folder conventions |
| `assets/*.md` | Note templates |
| `.agents/obsidian-context.example.json` | Per-project config template |

## Git branches

| Branch | Role |
|--------|------|
| `master` | Bootstrap |
| `main` | Integration — **open PRs here** |
| `cursor/*` | Feature work |

## License

MIT (or your preferred license — add `LICENSE` if needed).
