---
name: saving-obsidian-context
description: Persist research, decisions, learnings, and session logs to an Obsidian vault via the official CLI so knowledge survives across conversations. Load vault context at session start; save proactively during work.
metadata:
  requires: obsidian-cli-1.12.4+
  companion: saving-workspace-context
---

# Saving Obsidian Context

You build institutional memory in the user's Obsidian vault. As you work, capture knowledge that should outlast this conversation and write it through the **Obsidian CLI** — not raw filesystem edits when the CLI is available.

## Prerequisites

1. Obsidian desktop **1.12.4+** with CLI enabled: **Settings → General → Command line interface → Register CLI**
2. Obsidian app **running** (CLI talks to the live app)
3. Shell can run `obsidian` (`obsidian version` succeeds)

If CLI is unavailable, fall back to workspace files per `saving-workspace-context` and mention the limitation.

## At the Start of Every Conversation

Load existing context **before** doing task work.

### Step 1 — Resolve vault

Run the resolution chain (stop at first match):

```bash
# 0. Verify CLI
obsidian version

# 1. Project config (preferred)
#    Read .agents/obsidian-context.json if present

# 2. Environment
#    OBSIDIAN_VAULT or OBSIDIAN_VAULT_PATH

# 3. Discover all vaults (multi-vault setups)
obsidian vaults verbose

# 4. Active / default vault
obsidian vault
obsidian vault info=path
```

**Config file** (copy from `.agents/obsidian-context.example.json`):

```json
{
  "vault": { "name": "Cloud" },
  "projectSlug": "my-repo",
  "basePath": "Projects/my-repo"
}
```

Use `vault.name` with `vault="..."` on every command when multiple vaults exist. Use `basePath` as the root for this project's notes inside the vault.

### Step 2 — Load project context

```bash
VAULT="Cloud"
BASE="Projects/my-repo"

obsidian vault="$VAULT" read path="$BASE/project-context.md" 2>/dev/null || true
obsidian vault="$VAULT" files folder="$BASE/context"
obsidian vault="$VAULT" search query="path:$BASE"
```

Read any `context/{topic}.md` files relevant to the current task. Skim `logs/` for recent session entries under today's or yesterday's date folder.

### Step 3 — Check workspace fallbacks

Also check in-repo pointers if they exist: `context/`, `PROJECT.md`, `.agents/product-marketing-context.md`, domain dirs (`docs/`, `research/`). Obsidian is primary; workspace files may mirror or link to vault notes.

## During a Conversation

Save as soon as you recognize durable value — don't wait until the end.

| Signal | Where in vault | How |
|--------|----------------|-----|
| Product details, positioning, ICP | `{basePath}/project-context.md` | `prepend` dated section (newest first) |
| Research on company, person, topic | `{basePath}/context/{topic-slug}.md` | `create` if new, else `prepend` |
| Strategy decisions or learnings | `{basePath}/context/{topic}.md` | dated `## YYYY-MM-DD — title` entry |
| Session / work log | `{basePath}/logs/{YYYY-MM-DD}/{descriptive-name}.md` | `create` one file per entry |
| Reusable template | `{basePath}/templates/` or vault `Templates/` | `create` |
| Repeatable multi-step workflow | `.cursor/skills/` in repo | **ask permission** |
| Persistent constraint | `.cursor/rules/` in repo | **ask permission** |

### Write rules

- **Don't ask permission** for small context saves — save and mention what you wrote
- **Do ask permission** before new skills or rules
- **Read before write** — `obsidian read path="..."` to avoid duplicate entries
- **Prepend for journals** — `prepend` inserts after YAML frontmatter, keeping newest entries on top
- **Never overwrite** unless the user explicitly requests it — omit the `overwrite` flag
- **Quote paths with spaces** — `vault="My Vault"` `path="Projects/My App/context/foo.md"`
- **Use `\n` for newlines** in inline content: `content="## 2026-07-14 — Title\n\nBody"`

### Example commands

```bash
VAULT="Cloud"
BASE="Projects/obsidian-workspace-persistence-skill"
DATE="2026-07-14"

# New context note
obsidian vault="$VAULT" create \
  path="$BASE/context/vault-resolution.md" \
  content="$(cat <<'EOF'
# Vault Resolution

## 2026-07-14 — Resolution chain defined

Priority: config → env → CWD → active vault → vaults verbose.
EOF
)"

# Add entry to existing note (newest first)
obsidian vault="$VAULT" prepend \
  path="$BASE/context/vault-resolution.md" \
  content="## 2026-07-14 — CLI verified\n\nobsidian version returned 1.12.7.\n"

# Session log
obsidian vault="$VAULT" create \
  path="$BASE/logs/$DATE/obsidian-skill-created.md" \
  content="# Obsidian persistence skill created\n\nImplemented saving-obsidian-context skill with CLI vault discovery.\n"

# Tag a note (optional)
obsidian vault="$VAULT" property:set \
  path="$BASE/context/vault-resolution.md" \
  name=tags value="agent-context,obsidian"
```

For detailed CLI parameters, read `references/obsidian-cli.md`. For note shapes, use templates in `assets/`.

## At the End of a Conversation

Before finishing, check:

- Did I learn anything about this project not yet in the vault?
- Did I do research that would be painful to redo?
- Did I discover a pattern that should become a skill or rule?
- Did I create content that could be templated?

If yes to any, save now. Write a brief session log under `logs/{today}/` summarizing what was done.

## File Formats

### Context notes (`{basePath}/context/{slug}.md`)

Use template: `assets/context-note-template.md`

```markdown
# {Topic}

## {YYYY-MM-DD} — {Brief title}

{What was learned, decided, or discovered}

## {Earlier date} — {Earlier entry}

{Previous context}
```

Reverse-chronological. Date every entry.

### Project context (`{basePath}/project-context.md`)

Use template: `assets/project-context-template.md`

### Session logs (`{basePath}/logs/{YYYY-MM-DD}/{name}.md`)

Use template: `assets/session-log-template.md`. One file per entry; folder per day.

## Linking vault ↔ repo

When helpful, add a short in-repo pointer (not a full duplicate):

```markdown
# Context

Persistent knowledge lives in Obsidian: `Cloud/Projects/{slug}/`
```

Optionally maintain `.agents/obsidian-context.json` with `basePath` so future sessions resolve quickly.

## Troubleshooting

| Problem | Fix |
|---------|-----|
| `obsidian: command not found` | Enable CLI in Obsidian settings; restart terminal |
| Wrong vault | Set `vault.name` in config; prefix every command with `vault="..."` |
| Empty read | Note doesn't exist yet — `create` instead of `prepend` |
| CLI hangs | Ensure Obsidian is running; wait for first-launch startup |
| Bulk writes slow | CLI is per-command IPC — batch content into fewer `prepend`/`create` calls |

## Rules

- Be proactive — save without being asked, but mention what you saved
- Keep note titles and paths scannable — future agents search by folder listing
- Don't save trivia — skip what is easily re-derived
- Date everything that accumulates
- Search before creating — `obsidian search query="topic"` to avoid duplicate notes
- Prefer Obsidian CLI over direct file writes when the app is available
