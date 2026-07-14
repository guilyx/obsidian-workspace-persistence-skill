# Vault Layout Reference

## Default project tree

```
{vault}/
  Projects/
    {project-slug}/
      project-context.md
      context/
        {topic-slug}.md
      logs/
        {YYYY-MM-DD}/
          {descriptive-entry}.md
      templates/
        {template-name}.md
```

## Naming conventions

| Element | Rule | Example |
|---------|------|---------|
| `project-slug` | kebab-case, matches repo folder or config | `obsidian-workspace-persistence-skill` |
| `topic-slug` | kebab-case, noun-ish | `vault-resolution`, `api-auth` |
| Log folder | ISO date `YYYY-MM-DD` | `2026-07-14` |
| Log file | kebab-case, action-oriented | `obsidian-skill-created.md` |
| Context file | one topic per file | `context/deployment.md` |

## project-slug resolution

1. `projectSlug` in `.agents/obsidian-context.json`
2. Git repo root directory name
3. Ask user once → save to config

## basePath resolution

1. `basePath` in config (e.g. `Projects/my-app`)
2. Default: `Projects/{project-slug}`
3. User-specific override via `OBSIDIAN_CONTEXT_BASE_PATH` env var

## Example: Cloud vault (Erwin)

```
Cloud/
  Projects/
    sirb-autonomy/
      project-context.md
      context/
      logs/
        2026-07-14/
          colcon-build-fix.md
  Logs/                    # optional global logs (user preference)
    2026-07-14/
      session-summary.md
```

Per-project logs under `Projects/{slug}/logs/` are preferred. Global `Logs/` is acceptable when configured in `obsidian-context.json`:

```json
{
  "vault": { "name": "Cloud" },
  "projectSlug": "sirb-autonomy",
  "basePath": "Projects/sirb-autonomy",
  "sessionLogsPath": "Logs"
}
```

When `sessionLogsPath` is set, session logs go to `{vault}/{sessionLogsPath}/{date}/` instead of `{basePath}/logs/{date}/`.

## Wikilinks

Prefer plain markdown in agent-written notes. Wikilinks `[[note]]` work when filenames are unique. Use `path=` in CLI for unambiguous targeting.

## Frontmatter (optional)

```yaml
---
tags:
  - agent-context
project: my-repo
created: 2026-07-14
---
```

Set via `property:set` after `create`, or include in `content` when creating.
