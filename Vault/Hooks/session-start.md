# 🪝 Hook: session-start

## מה הוא עושה
סקריפט Bash שרץ אוטומטית בתחילת **כל סשן** של Claude Code. מזריק את תוכן הסקיל `using-superpowers` לתוך ה-context של Claude כ-`additionalContext`.

## מיקום
`.claude/hooks/session-start`

## תהליך הפעולה
1. מוצא את `PLUGIN_ROOT`
2. קורא את `.claude/skills/using-superpowers/SKILL.md`
3. בודק אם קיים legacy skills directory (מזהיר אם כן)
4. מזריק הכל כ-JSON עם `additionalContext`

## פורמט פלט
תלוי בפלטפורמה:
- **Claude Code:** `hookSpecificOutput.additionalContext`
- **Cursor:** `additional_context`
- **Copilot CLI:** `additionalContext`

## מופעל על ידי
[[hooks.json]] ← [[../Config/settings.json]]

## קבצים קשורים
- [[hooks.json]]
- `.claude/skills/using-superpowers/SKILL.md`
- `.claude/hooks/run-hook.cmd`
