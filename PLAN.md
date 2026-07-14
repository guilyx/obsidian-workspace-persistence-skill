# Plan: Obsidian Workspace Persistence Skill

## Goal

Create a Cursor Agent Skill that persists institutional memory to an Obsidian vault instead of (or alongside) workspace files. The skill mirrors `saving-workspace-context` but routes knowledge through the official Obsidian CLI (v1.12.4+).

## Design Principles

1. **Obsidian-first, workspace-fallback** — Prefer the vault for durable knowledge; keep lightweight project pointers in-repo when useful.
2. **CLI over filesystem hacks** — Use `obsidian` commands so link resolution, frontmatter, and vault settings stay consistent.
3. **Discover, don't guess** — Resolve vault and paths through a deterministic resolution chain before writing.
4. **Append, never clobber** — Context notes grow with dated, reverse-chronological entries.
5. **Progressive disclosure** — Keep `SKILL.md` actionable; move CLI details and templates into `references/` and `assets/`.
6. **Auto-apply** — Skill applies automatically during agent work (`disable-model-invocation` not set).

## Vault Resolution Chain

Resolve in order; stop at the first match:

| Priority | Source | How |
|----------|--------|-----|
| 1 | Project config | `.agents/obsidian-context.json` → `vault.name` or `vault.path` |
| 2 | Environment | `OBSIDIAN_VAULT` (name) or `OBSIDIAN_VAULT_PATH` (absolute path) |
| 3 | CWD | Shell `pwd` is inside a vault folder → `obsidian vault info=path` |
| 4 | Active vault | `obsidian vault info=name` (Obsidian must be running) |
| 5 | Multi-vault | `obsidian vaults verbose` → match config or ask user once, then save to config |

### CLI prerequisites

```bash
obsidian version          # verify CLI is registered (1.12.4+)
obsidian vaults verbose   # list vaults with paths
obsidian vault info=path  # current/default vault path
```

Obsidian desktop must be running. First command may launch it — wait for startup before bulk writes.

## Vault Folder Layout

Default layout under `{vault}/Projects/{project-slug}/`:

```
Projects/
  {project-slug}/
    project-context.md      # identity, goals, constraints
    context/
      {topic-slug}.md       # research, decisions, learnings
    logs/
      {YYYY-MM-DD}/
        {descriptive-title}.md   # session work logs
    templates/                # optional reusable snippets
```

`project-slug` = git repo folder name or `name` field in config.

Session logs follow the user's convention: hierarchical by date, one descriptive `.md` per entry.

## Signal → Destination Mapping

| Signal | Obsidian destination | CLI pattern |
|--------|---------------------|-------------|
| Product / project identity | `Projects/{slug}/project-context.md` | `prepend` (newest first) or `append` to dated section |
| Research on topic | `Projects/{slug}/context/{topic}.md` | `create` if missing, else `prepend` |
| Strategy / decisions | same as context | dated `## YYYY-MM-DD — title` blocks |
| Session work summary | `Projects/{slug}/logs/{date}/{title}.md` | `create` |
| Reusable template | `Projects/{slug}/templates/` or vault `Templates/` | `create` with `template=` when applicable |
| Repeatable workflow | `.cursor/skills/` in repo | ask permission first |
| Persistent constraint | `.cursor/rules/` in repo | ask permission first |

## Write Operations

| Intent | Command | Notes |
|--------|---------|-------|
| New note | `obsidian vault="X" create path="..." content="..."` | omit `overwrite` unless intentional |
| Append entry | `obsidian vault="X" prepend path="..." content="## date — title\n\nbody"` | `prepend` inserts after frontmatter (newest-first journals) |
| Read before write | `obsidian vault="X" read path="..."` | avoid duplicates |
| Check existence | `obsidian vault="X" file path="..."` | non-zero exit if missing |
| Search existing | `obsidian vault="X" search query="..."` | find related notes |
| Set metadata | `obsidian vault="X" property:set path="..." name=tags value=...` | optional tagging |

Always pass `vault="Name"` when multiple vaults exist. Quote values with spaces.

## In-Repo Artifacts

| File | Purpose |
|------|---------|
| `.cursor/skills/saving-obsidian-context/SKILL.md` | Agent instructions |
| `.cursor/skills/saving-obsidian-context/references/` | CLI cheat sheet, layout docs |
| `.cursor/skills/saving-obsidian-context/assets/` | Note templates |
| `.agents/obsidian-context.example.json` | Copy to `obsidian-context.json` per project |
| `CHANGELOG.md` | Track unreleased changes |

## Git Workflow

- `master` — initial bootstrap branch
- `main` — integration branch (created from `master`)
- Feature branches → PRs target **`main`**, not `master`

## Out of Scope (v1)

- Headless / Sync-only vault access without desktop app
- Bulk migration of existing `context/` dirs (document manual one-time sync instead)
- Obsidian Publish integration

## Success Criteria

- [ ] Skill auto-applies and documents full vault resolution
- [ ] Agent can discover vault via CLI without hardcoded paths
- [ ] Templates and references are loadable on demand
- [ ] Example config and README enable zero-to-working setup
- [ ] PR merges to `main`
