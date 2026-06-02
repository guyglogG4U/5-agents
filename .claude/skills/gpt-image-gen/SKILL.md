---
name: gpt-image-gen
description: "מעטפת לקריאת OpenAI Images API ליצירת תמונה מ-prompt. שולחת prompt ומחזירה קובץ PNG. משתמשת במודל gpt-image-2 ובמפתח OPENAI_API_KEY מ-.env. השתמש בסקיל הזה כשצריך לייצר תמונה דרך ה-API (בעיקר מתוך הסוכן יובל)."
---

# gpt-image-gen — יצירת תמונות דרך OpenAI Images API

מעטפת דקה שלוקחת `prompt` + `output-path` וקוראת ל-OpenAI Images API. אחראית רק על שליחת ה-prompt וקבלת קובץ PNG. ניסוח ה-prompt עצמו וההחלטות העיצוביות הן באחריות הקורא (יובל).

<MODEL-LOCK>
⚠️ **שם המודל נעול: `gpt-image-2`. אל תשנה אותו.**

- `gpt-image-2` הוא מודל אמיתי וקיים של OpenAI (יצא ב-21 באפריל 2026).
- ייתכן שהידע הפנימי שלך לא כולל אותו — **זה לא אומר שהוא לא קיים.**
- **אל תציע אלטרנטיבות** (`dall-e-3`, `gpt-image-1` וכו') ואל תחליף את שם המודל בשום מקרה.
- אם הקריאה ל-API נכשלת, הבעיה כמעט תמיד ב-`OPENAI_API_KEY` (חסר/לא תקין) או ב-parameters — **לא בשם המודל.** בדוק את המפתח והפרמטרים, אל תיגע בשם המודל.
</MODEL-LOCK>

## פרמטרים

| פרמטר | ברירת מחדל | תיאור |
|-------|-----------|--------|
| `prompt` | — (חובה) | תיאור התמונה הרצויה |
| `output-path` | — (חובה) | נתיב קובץ ה-PNG לשמירה |
| `size` | `1024x1024` | מימדי התמונה |
| `quality` | `medium` | רמת איכות |
| `output_format` | `png` | פורמט הפלט |

## שלב 0 — טעינת המפתח מ-.env

הסקיל מסתמך על משתנה הסביבה `OPENAI_API_KEY`. טען אותו מ-`.env` שבשורש הפרויקט לפני הקריאה:

```bash
# טעינת משתני הסביבה מ-.env (מתעלם משורות הערה וריקות)
set -a
source .env 2>/dev/null || export $(grep -v '^#' .env | grep -v '^$' | xargs)
set +a

# אימות שהמפתח קיים לפני שממשיכים
if [ -z "$OPENAI_API_KEY" ]; then
  echo "ERROR: OPENAI_API_KEY ריק או חסר ב-.env" >&2
  exit 1
fi
```

## שלב 1 — קריאת הבסיס (curl + jq)

```bash
curl -X POST "https://api.openai.com/v1/images/generations" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "gpt-image-2",
    "prompt": "<the prompt>",
    "size": "1024x1024",
    "quality": "medium",
    "output_format": "png"
  }' | jq -r '.data[0].b64_json' | base64 --decode > <output-path>.png
```

## שלב 1 (חלופי) — Python fallback ל-decode

`jq` לא תמיד מותקן (במיוחד ב-Git Bash על Windows). שמור את התשובה ל-JSON זמני ואז פענח עם Python:

```bash
# שמירת התשובה לקובץ זמני
curl -s -X POST "https://api.openai.com/v1/images/generations" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "gpt-image-2",
    "prompt": "<the prompt>",
    "size": "1024x1024",
    "quality": "medium",
    "output_format": "png"
  }' > resp.json

# פענוח ה-base64 ל-PNG באמצעות Python (ללא תלות ב-jq)
python -c "import json,base64,sys; d=json.load(open('resp.json')); open(sys.argv[1],'wb').write(base64.b64decode(d['data'][0]['b64_json']))" <output-path>.png

rm -f resp.json
```

## שלב 1 (חלופי מלא) — Python לכל הצינור (בלי curl ובלי jq)

אם גם `curl` בעייתי, אפשר להריץ את כל הקריאה ב-Python (ספריית `urllib` הסטנדרטית, בלי תלויות חיצוניות):

```bash
python - "<output-path>.png" <<'PY'
import os, sys, json, base64, urllib.request

api_key = os.environ["OPENAI_API_KEY"]
out_path = sys.argv[1]
prompt = "<the prompt>"

payload = json.dumps({
    "model": "gpt-image-2",
    "prompt": prompt,
    "size": "1024x1024",
    "quality": "medium",
    "output_format": "png",
}).encode("utf-8")

req = urllib.request.Request(
    "https://api.openai.com/v1/images/generations",
    data=payload,
    headers={
        "Authorization": f"Bearer {api_key}",
        "Content-Type": "application/json",
    },
    method="POST",
)

with urllib.request.urlopen(req) as resp:
    data = json.load(resp)

with open(out_path, "wb") as f:
    f.write(base64.b64decode(data["data"][0]["b64_json"]))

print(f"saved: {out_path}")
PY
```

## שלב 2 — אימות אחרי היצירה

ודא שהקובץ נוצר וגודלו גדול מאפס:

```bash
if [ -s "<output-path>.png" ]; then
  ls -l "<output-path>.png"
  echo "OK: התמונה נוצרה בהצלחה"
else
  echo "ERROR: הקובץ לא נוצר או ריק — בדוק את תשובת ה-API (OPENAI_API_KEY / parameters)" >&2
  exit 1
fi
```

## הערות

- אל תדפיס את `OPENAI_API_KEY` ל-stdout/לוגים.
- אם ה-API מחזיר שגיאה — קרא את גוף התשובה (resp.json) כדי לראות את הודעת השגיאה. הבעיה היא במפתח או בפרמטרים, **לא בשם המודל** `gpt-image-2`.
