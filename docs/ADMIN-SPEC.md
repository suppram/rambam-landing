# אפיון — ממשק ניהול עמודי נחיתה (Rambam LP Manager)

**גרסה:** 0.1 · **תאריך:** 2026-06-24 · **סטטוס:** טיוטה לאישור

---

## 1. מטרה

ממשק לניהול ריבוי עמודי נחיתה בוריאציות שונות, מאוחסן על שרת Cloudways הקיים, עם גישה סגורה לכמה מנהלים שיכולים **לערוך, לפרסם, ולשכפל** עמודים — בלי מעורבות מפתח לכל שינוי תוכן.

---

## 2. החלטות אפיון (מאושרות)

| נושא | החלטה |
|---|---|
| מודל עריכה | **שדות מוגדרים** (טופס) — לא WYSIWYG, לא HTML גולמי |
| ארכיטקטורה | **תוסף WordPress** על ה-WP הקיים ב-Cloudways |
| וריאציות | **מספר תבניות (templates)** שונות + ריבוי עמודים מכל תבנית |
| הרשאות | **משתמשי WordPress קיימים** (capability ייעודי) |
| פלט העמוד | **URL עצמאי נקי** — מבודד מ-theme ומתוספים (CookieYes/WP Rocket) |
| תבניות | **מפתח מוסיף תבניות** (קובץ HTML + סכמת שדות); מנהלים בוחרים וממלאים |
| A/B + Analytics | לא כרגע — מעקב דרך GA/פיקסל חיצוני בלבד |
| טופס | **שדה URL ל-iframe של Smoove לכל עמוד** |

---

## 3. ארכיטקטורה

תוסף WordPress יחיד: `rambam-lp-manager`.

```
rambam-lp-manager/
├─ rambam-lp-manager.php        # bootstrap
├─ includes/
│  ├─ class-cpt.php             # Custom Post Type "rambam_lp"
│  ├─ class-templates.php       # רישום וטעינת תבניות
│  ├─ class-admin-ui.php        # מסך עריכה (meta-box / שדות)
│  ├─ class-renderer.php        # מילוי placeholders → HTML מלא
│  ├─ class-router.php          # rewrite ל-URL נקי /lp/{slug}
│  ├─ class-caps.php            # הרשאות
│  └─ class-duplicate.php       # שכפול עמוד
├─ templates/
│  ├─ open-day-v2/
│  │  ├─ template.html          # ה-HTML הקיים עם {{placeholders}}
│  │  ├─ schema.json            # הגדרת השדות הניתנים לעריכה
│  │  └─ assets/                # פונטים/JS/CSS/תמונות ברירת-מחדל
│  └─ <template-נוסף>/...
└─ assets/admin.css|js          # עיצוב מסך הניהול
```

### מודל נתונים
- **CPT `rambam_lp`** — כל עמוד נחיתה = פוסט.
- meta:
  - `_lp_template` — מזהה התבנית הנבחרת.
  - `_lp_fields` — JSON עם ערכי השדות (לפי סכמת התבנית).
  - `_lp_status` — draft / published.
  - `_lp_slug` — ה-slug ל-URL (`/lp/{slug}`).
- תמונות/מדיה — **ספריית המדיה של WordPress** (מזהה attachment נשמר בשדה).

---

## 4. תבניות (Templates)

כל תבנית = תיקייה עם:
1. **`template.html`** — מסמך HTML עצמאי מלא (כמו `variant-2-minimal.html`), עם אסימוני placeholder, למשל:
   ```html
   <h1>{{hero_title}}</h1>
   <p class="form-sub">{{form_sub}}</p>
   {{#bullets}}<li>{{.}}</li>{{/bullets}}
   <iframe src="{{smoove_url}}" ...>
   ```
2. **`schema.json`** — מגדיר אילו שדות מופיעים בטופס הניהול, סוג, ולידציה:
   ```json
   {
     "name": "יום פתוח v2",
     "fields": [
       {"key":"hero_title","label":"כותרת ראשית","type":"text","required":true},
       {"key":"event_date","label":"תאריך","type":"text"},
       {"key":"bullets","label":"נקודות","type":"repeater","item":"text","max":6},
       {"key":"doctor_photo","label":"תמונת רופא","type":"image"},
       {"key":"testimonials","label":"עדויות","type":"repeater",
        "fields":[{"key":"img","type":"image"},{"key":"youtube_id","type":"text"},
                  {"key":"quote","type":"text"},{"key":"name","type":"text"}]},
       {"key":"smoove_url","label":"קישור טופס Smoove","type":"url","required":true}
     ]
   }
   ```

**תבנית ראשונה:** `variant-2-minimal.html` הקיים → הופך ל-`open-day-v2` עם השדות לעיל.

---

## 5. מסך הניהול

- תפריט WP: **"עמודי נחיתה"** (נראה רק למי שיש לו ה-capability).
- **רשימה:** כל העמודים — שם, תבנית, סטטוס, תאריך עדכון, פעולות (ערוך / שכפל / תצוגה מקדימה / פרסם / מחק).
- **יצירה:** בחירת תבנית → טופס שדות (נבנה דינמית מ-`schema.json`) → שמירה כטיוטה.
- **עריכה:** טופס שדות, **תצוגה מקדימה חיה** (preview ב-URL נסתר לפני פרסום).
- **שכפול:** יוצר עותק (כולל כל השדות) עם slug חדש — בסיס לוריאציה.
- **פרסום:** טיוטה → פורסם; חושף את `/lp/{slug}` לציבור.

---

## 6. פלט והגשה (הפתרון לבעיות החסימה)

- Rewrite rule: `https://rambam-medicine.org.il/lp/{slug}`.
- ה-renderer מחזיר את ה-**HTML המלא של התבנית בלבד** (DOCTYPE→</html>), **בלי** `wp_head`/`wp_footer`, בלי theme, בלי סקריפטים של תוספים.
  → אין CookieYes, אין WP Rocket lazyload, אין FitVids. יוטיוב וטפסים עובדים. רינדור זהה למקור.
- ביצועים: אפשר לקאשר את ה-HTML כקובץ סטטי ולהגיש ישירות (אופציונלי, שלב 2).
- **אין צורך ב-iframe/Vercel** — העמוד מתארח ומוגש מהדומיין שלכם.

---

## 7. הרשאות

- Capability חדש: `manage_rambam_lp`.
- מוקצה לתפקיד/משתמשים ספציפיים (לא לכל אדמין בהכרח).
- כל פעולות ה-CRUD/פרסום/שכפול מאחורי ה-capability.
- ניצול ה-login וההרשאות הקיימים של WordPress (אין מערכת auth נפרדת).

---

## 8. מה לא נכלל (שלב עתידי)

- A/B testing ופיצול תנועה.
- מעקב המרות מובנה (כרגע GA/פיקסל חיצוני).
- עיצוב תבניות ע"י מנהלים (כרגע מפתח בלבד).
- עורך ויזואלי.

---

## 9. שלבי בנייה מוצעים

1. **שלד תוסף** — CPT, capability, תפריט.
2. **מנוע תבניות + renderer** — טעינת `template.html`/`schema.json`, מילוי placeholders.
3. **המרת `variant-2-minimal.html`** לתבנית `open-day-v2` עם placeholders + schema.
4. **מסך ניהול** — רשימה, טופס שדות דינמי, מדיה, תצוגה מקדימה.
5. **Router** — `/lp/{slug}` מגיש HTML נקי.
6. **פרסום + שכפול**.
7. (אופציונלי) קאשינג סטטי, ייצוא/ייבוא תבניות.

---

## 10. שאלות פתוחות לאישור (הנחות ברירת-מחדל)

- **Slug:** ידני לכל עמוד (לא אוטומטי משם). ✅ הנחה
- **תצוגה מקדימה:** URL נסתר עם token לפני פרסום. ✅ הנחה
- **מדיה:** ספריית המדיה של WP. ✅ הנחה
- **תבנית #1:** `open-day-v2` (העמוד הנוכחי). ✅ הנחה
- כמה תבניות צפויות בהתחלה? (1? 3?) — להשלמה.
