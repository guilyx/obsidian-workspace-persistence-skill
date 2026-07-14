# Obsidian CLI Quick Reference

Official docs: https://help.obsidian.md/cli

Requires Obsidian desktop **1.12.4+**, app running. Syntax: `obsidian [vault=Name] <command> [param=value] [flags]`

## Vault discovery

```bash
obsidian version                    # CLI + app version
obsidian vault                      # active vault name
obsidian vault info=path            # active vault absolute path
obsidian vault info=name            # vault name only
obsidian vaults                     # list vault names
obsidian vaults verbose             # names + paths (use for setup)
obsidian vaults total               # count
```

**Targeting a vault** — `vault=` must be the **first** parameter:

```bash
obsidian vault="Cloud" search query="project"
obsidian vault=Notes daily
```

**Default vault selection** (when `vault=` omitted):

1. CWD is inside a vault folder → that vault
2. Else → currently active vault in Obsidian

## File operations

```bash
# Read
obsidian vault="V" read path="folder/note.md"
obsidian vault="V" read file="Note Title"     # wikilink resolution

# Create (never pass overwrite unless intentional)
obsidian vault="V" create path="folder/note.md" content="# Title\n\nBody"
obsidian vault="V" create name="Note" template=TemplateName

# Append / prepend
obsidian vault="V" append  path="..." content="footer text"
obsidian vault="V" prepend path="..." content="## New entry\n\nTop of body."

# Existence / metadata
obsidian vault="V" file path="folder/note.md"
obsidian vault="V" files folder="Projects/my-repo/context"
obsidian vault="V" folders folder="Projects/my-repo"

# Move / rename / delete
obsidian vault="V" move  path="old.md" to="archive/"
obsidian vault="V" rename path="old.md" name="new-name"
obsidian vault="V" delete path="draft.md"              # trash (default)
obsidian vault="V" delete path="draft.md" permanent    # skip trash
```

## Search

```bash
obsidian vault="V" search query="meeting notes"
obsidian vault="V" search query="tag:#agent-context" format=json
obsidian vault="V" search:context query="TODO"         # grep-style lines
```

## Properties (frontmatter)

```bash
obsidian vault="V" properties path="note.md"
obsidian vault="V" property:read  path="note.md" name=tags
obsidian vault="V" property:set   path="note.md" name=tags value="a,b"
obsidian vault="V" property:set   path="note.md" name=status value=active type=text
obsidian vault="V" property:remove path="note.md" name=draft
```

Types: `text`, `list`, `number`, `checkbox`, `date`, `datetime`

## Daily notes

```bash
obsidian vault="V" daily
obsidian vault="V" daily:path
obsidian vault="V" daily:read
obsidian vault="V" daily:append content="- [ ] task"
obsidian vault="V" daily:prepend content="## Morning\n"
```

## Tags and tasks

```bash
obsidian vault="V" tags counts
obsidian vault="V" tasks daily
obsidian vault="V" tasks path="note.md"
```

## Content escaping

- Spaces in values: wrap in quotes — `content="hello world"`
- Newlines: `\n` — `content="# H1\n\nParagraph"`
- Tabs: `\t`
- Copy output to clipboard: append `--copy` to any command

## Multi-vault checklist

1. `obsidian vaults verbose` → pick vault name
2. Save to `.agents/obsidian-context.json`
3. Prefix **every** command: `obsidian vault="Exact Name" ...`

## When CLI is wrong tool

- Thousands of files: consider direct filesystem or a script (CLI is IPC per command)
- Obsidian not installed / headless server: use workspace `context/` fallback or Obsidian Headless Sync
