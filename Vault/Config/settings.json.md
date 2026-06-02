# settings.json ⚙️

## מה הוא עושה
קובץ הגדרות Claude Code ברמת הפרויקט. מגדיר:

### Hooks
- **SessionStart** — מריץ `session-start` hook בכל פתיחת סשן דרך `run-hook.cmd`

### Marketplaces
- `anthropic-agent-skills` → `anthropics/skills` (GitHub)
- `zerem-obsidian-skills` → `ZeremItay/the-5-agents-obsidian` (GitHub)

### Plugins מופעלים
- `example-skills@anthropic-agent-skills`
- `the-5-agents-obsidian@zerem-obsidian-skills`

## מיקום
`.claude/settings.json`

## למי משויך
- Claude Code — נקרא אוטומטית
- [[../Hooks/session-start]] — ה-hook שמופעל

## קבצים קשורים
- [[../Hooks/hooks.json]]
- [[../Hooks/session-start]]
- [[CLAUDE.md]]
