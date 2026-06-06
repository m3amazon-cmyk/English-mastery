# Handoff — English Mastery Web App

## Who the user is
Mohammad — Kuwaiti, English Masters from Penn State (Rochester, 2017–2019), teaches at Kuwait University. Building a personal grammar-refresher app to master English grammar in **2 months** and be able to teach it confidently. Content is personalized; keep all references (Mohammad, Kuwait University, Penn State, Pennsylvania, Rochester).

---

## Current live site
**URL:** https://m3amazon-cmyk.github.io/English-mastery/
**Repo:** https://github.com/m3amazon-cmyk/English-mastery
**Local path:** `/Users/malhusaini/Desktop/ENGLISH WEBSITE/`
**Key file:** `index.html` — single-file vanilla JS app, ~168K chars

---

## Supabase (cross-device sync — already wired up)
- **Project URL:** `https://wcfoymiqfpzoxctexdub.supabase.co`
- **Publishable key:** `sb_publishable_vyCD7qOBTheSMhM6fgXeLw_me3ViUg6`
- **Table:** `progress(user_id text PK, data jsonb, updated_at timestamptz)`
- **Auth:** passphrase stored in `localStorage` key `em_passphrase`
- **localStorage key:** `em_full_v1` → `{xp, completed[], actDone{}, retryCount{}, mastery{}}`

---

## What the app does RIGHT NOW

### Layout
- **Desktop:** 250px sidebar (always visible) + main content area
- **Mobile:** sidebar hidden, mobile header with logo/XP/theme toggle
- Built with: Plus Jakarta Sans, blue primary `#4F6EF7`, dark mode toggle, 14px border radius

### Structure
4 Phases, 15 lessons. **4 lessons have full content:**
- ✅ Lesson 1 — Parts of Speech (`pos`) — unlocked
- ✅ Lesson 2 — Articles & Determiners (`articles`) — unlocked
- ✅ Lesson 3 — Verb Tenses & Aspect (`tenses`) — unlocked
- ✅ Lesson 4 — Subject-Verb Agreement (`agreement`) — unlocked
- 🔒 Lessons 5–15 — no content yet

### Activities per lesson (7 tabs)
Overview, Flashcards, MCQ Quiz (6 q), Fill in Blank (5 q), Match Pairs, Tap Word, **Error Fix** (6 q)

### Completion logic
A lesson is "complete" when all 5 graded activities (mcq, fill, match, tap, **err**) are done → +50 XP

### Sub-lessons (drill-down from Overview tab)
Each of the 4 active lessons has 3–4 sub-lessons with:
- Detailed explanation (key concept cards + rule box)
- 4-question MCQ quiz (+8 XP)
- 4-question fill-in-blank (+8 XP)
- Sub-lessons tracked separately from main lesson activities

| Lesson | Sub-lessons |
|---|---|
| Parts of Speech | Nouns & Pronouns · Verbs · Adjectives & Adverbs · Prepositions & Conjunctions |
| Articles | The Definite Article · A and An · Zero Article |
| Verb Tenses | Present Tenses · Past Tenses · Future Forms · Stative Verbs & Used To |
| Agreement | Basic Rules · Indefinite Pronouns · Collective Nouns |

Sub-lesson IDs: `pos_nouns`, `pos_verbs`, `pos_adj_adv`, `pos_prep_conj`, `art_the`, `art_a_an`, `art_zero`, `ten_present`, `ten_past`, `ten_future`, `ten_stative`, `agr_basic`, `agr_pronouns`, `agr_collective`

### Level system
```js
const LEVELS=[
  {name:"Beginner", emoji:"📖", min:0,   color:"#9CA3AF"},
  {name:"Learner",  emoji:"✏️", min:200,  color:"#4F6EF7"},
  {name:"Practitioner",emoji:"🎯",min:500,color:"#8B5CF6"},
  {name:"Educator", emoji:"🏛️", min:1000, color:"#F59E0B"},
  {name:"Master",   emoji:"🎓", min:2000, color:"#22C55E"},
];
```

### Mastery & completion tracking
- `ST.mastery = { lid: { mcq: 85, fill: 70, ... } }` — score % per activity
- `lessonMastery(lid)` — average of all activity scores
- `lessonCompletion(lid)` — returns `{done, total:5, pct}` based on activities done
- `subCompletion(slid)` — returns `{done, total:2, pct}` for sub-lesson quiz+fill
- `actScore(lid, act)` — returns mastery % for a specific activity
- Mastery bar shown on lesson cards (color: green ≥80%, blue ≥50%, yellow otherwise)

### Completion % shown everywhere
- **Lesson cards (home):** `2/5 activities · 40%` pill + mastery bar
- **Activity tabs:** `❓ Quiz 83%` — score % in the tab when done
- **Sub-lesson cards:** `Quiz 75%` and `Fill 100%` pills + overall %
- **Sub-lesson overview:** progress bar + pill breakdown at top
- **Sidebar:** `2/5 done · 78% mastery` per lesson

### Question bank sizes
- pos: 21 MCQ, 16 fill, 6 match sets, 14 tap, 6 err
- articles: 22 MCQ, 16 fill, 6 match sets, 10 tap, 6 err
- tenses: 20 MCQ, 16 fill, 6 match sets, 10 tap, 6 err
- agreement: 12 MCQ, 10 fill, 4 match sets, 8 tap, 6 err

### Key JS architecture notes
- `LD` object holds all lesson data. Sub-lessons registered into `LD` dynamically via `LD[slid]` when first opened
- `SLD` object holds sub-lesson data arrays; attached to LD via `LD.pos.sublessons = SLD.pos` etc.
- `DS` object holds in-session activity state (keyed by `lid_act`)
- `MAIN_LESSONS` Set used to guard `checkComplete` — sub-lesson IDs are excluded
- `ds(lid, act)` / `getQs(lid, act)` / `resetDS(lid, act)` work for both lessons and sub-lessons
- `curL` = current lesson id; `curSL` = current sub-lesson id
- Navigation: `goHome()` / `openLesson(lid)` / `openSubLesson(lid, slid)` / `closeSubLesson()`

---

## Design tokens
```css
--primary:#4F6EF7; --green:#22C55E; --yellow:#F59E0B; --purple:#8B5CF6; --red:#EF4444;
--r:14px; --r-sm:10px; --r-full:999px;
--shadow: 0 1px 3px rgba(0,0,0,.08); --shadow-md: 0 4px 12px rgba(0,0,0,.1);
```
Dark mode via `[data-theme="dark"]` on `<html>`. Theme stored in `localStorage('em_theme')`.

---

## Environment
- Apple Silicon (arm64), macOS 15.3.2
- `git` ✅, `brew` ✅, `gh` ✅ (authenticated as `m3amazon-cmyk`)
- GitHub Pages auto-deploys on push to `main`
- No Node/npm needed (plain HTML)
- Use `Edit` tool (not `Write`) for changes — file is large (~168K chars)
- Use `mcp__nimbalyst-mcp__developer_git_commit_proposal` or standard git for commits

---

## Git state
Branch: `main`. All changes committed and pushed. Recent commits:
- `0696e1c` Add completion percentages to lessons, sub-lessons, and activity tabs
- `04a0726` Add 14 sub-lessons with in-depth explanations and exercises
- `1e7dd47` Add sidebar, level system, mastery tracking, Error Fix tab, and Lesson 4
- `efcdea3` Add more question sets to all lesson banks for retry variety

---

## Suggested next features (discussed with user, not yet built)

### High priority
1. **Lessons 5–8 content** (Phase 2: Phrases, Clauses, Sentence Types, Punctuation) — biggest gap
2. **Teacher Mode tab** — per lesson: common student mistakes, explanation scripts, classroom examples. Directly supports Mohammad's teaching goal.
3. **Daily streak & study timer** — streak counter + daily XP goal. Critical for 2-month consistency.
4. **Weak spots review** — auto-detects lowest-scoring activities and generates a personalised review session.

### Medium priority
5. **Spaced repetition for flashcards** — cards answered wrong reappear sooner
6. **Writing correction task** — given a paragraph with 3–5 errors, highlight and fix them
7. **Progress/stats page** — XP over time, strongest/weakest lessons, 2-month countdown

### Lower priority
8. Keyboard shortcuts (A/B/C/D for MCQ, Enter to check)
9. Export progress as PDF

---

## First actions in new session
1. Read this file
2. Read `index.html` (or grep for specific functions as needed)
3. Implement the requested feature
