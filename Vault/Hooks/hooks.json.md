# 🪝 hooks.json

## מה הוא עושה
קובץ הגדרת Hooks ברמת הפלאגין. מגדיר אילו פקודות לרוץ באיזה אירוע.

## מיקום
`.claude/hooks/hooks.json`

## תוכן
```json
{
  "hooks": {
    "SessionStart": [{
      "matcher": "startup|clear|compact",
      "hooks": [{
        "type": "command",
        "command": "\"${CLAUDE_PLUGIN_ROOT}/hooks/run-hook.cmd\" session-start"
      }]
    }]
  }
}
```

**אירוע:** `SessionStart` — בכל פתיחת סשן, `/clear`, או `/compact`
**פקודה:** מריץ את [[session-start]] דרך `run-hook.cmd`

## קבצים נוספים
- `hooks-cursor.json` — גרסת Cursor (אותו הגיון, פורמט שונה)
- `run-hook.cmd` — מריץ hooks ב-Windows/Mac/Linux

## קבצים קשורים
- [[session-start]]
- [[../Config/settings.json]]
