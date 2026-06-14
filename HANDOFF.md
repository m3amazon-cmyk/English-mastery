# Handoff — English Mastery Web App

## Who the user is
Mohammad — Kuwaiti, English Masters from Penn State (Rochester, 2017–2019), teaches at Kuwait University. Building a personal grammar-refresher app to master English grammar in **2 months** and be able to teach it confidently. Content is personalized; keep all references (Mohammad, Kuwait University, Penn State, Pennsylvania, Rochester).

---

## Current live site
**URL:** https://m3amazon-cmyk.github.io/English-mastery/
**Repo:** https://github.com/m3amazon-cmyk/English-mastery
**Local path:** `/Users/malhusaini/Desktop/ENGLISH WEBSITE/`
**Key file:** `index.html` — single-file vanilla JS app, ~212K chars
**Roadmap/plan:** `nimbalyst-local/plans/english-mastery-roadmap.md` (local, gitignored) — 7-milestone plan. Current state in "Roadmap status" + "Next up (start here)" below.

---

## Supabase (cross-device sync — already wired up)
- **Project URL:** `https://wcfoymiqfpzoxctexdub.supabase.co`
- **Publishable key:** `sb_publishable_vyCD7qOBTheSMhM6fgXeLw_me3ViUg6`
- **Table:** `progress(user_id text PK, data jsonb, updated_at timestamptz)`
- **Auth:** passphrase stored in `localStorage` key `em_passphrase`
- **localStorage key:** `em_full_v1` → `{xp, completed[], actDone{}, retryCount{}, mastery{}, streak, bestStreak, lastStudyDay, dailyXP{date,xp}, goalXP, studySeconds{total,today,day}, qpool{"lid_act":{order,pos}}, exam{"p<i>":{best,passed}}, examples{"lid":[{task,text,rating,date}]}, mistakes[{type,label,prompt/b,opts,ans,ac,exp,key}]}`

---

## What the app does RIGHT NOW

### Layout
- **Desktop:** 250px sidebar (always visible) + main content area
- **Mobile:** sidebar hidden, mobile header with logo/XP/theme toggle
- Built with: Plus Jakarta Sans, blue primary `#4F6EF7`, dark mode toggle, 14px border radius

### Structure
4 Phases, 15 lessons. **ALL 15 lessons are fully authored — the curriculum is complete.**
- ✅ Phase 1: `pos`, `articles`, `tenses`, `agreement`
- ✅ Phase 2: `phrases`, `clauses`, `sentencetypes`, `punctuation`
- ✅ Phase 3 (Precision & Style): `mistakes`, `register`, `clarity`, `cohesion`
- ✅ Phase 4 (The Teacher's Edge): `explaining`, `erroranalysis`, `academic`
- 🏅 **All four phase final exams are live** (`phaseExamReady(i)` true for every phase). Each lesson has a full `LD` entry, 3–4 `SLD` sub-lessons, a `LEARN` overview, a `WRITE` bank, and `EXAM_EXTRA` supplements.

### Activities per lesson (10 tabs)
Overview, Flashcards, MCQ Quiz (6 q), Fill in Blank (5 q), Match Pairs, Tap Word, **Error Fix** (6 q), **Write** (self-check writing, 3–4 prompts), **Comprehension** (cumulative MCQ + Error-Fix pooled from all lessons up to this one, 12 q, fresh each retry — `renderComp`/`getCompQs`/`buildCompPool`/`priorLessonIds`), **Teacher** (read-only teaching toolkit, see below)

### Retention features (Review My Misses + Weak Spots)
- **Mistake log:** every wrong answer (mcq/fill/err/comp/sub-lesson/exam) is captured via `logMiss()` from each `*Chk` handler + `examSubmit`, deduped by `missKey`, stored in `ST.mistakes`. Surfaced as a home card (`reviewCardHTML`) and sidebar row.
- **Review My Misses:** `openReview()` re-serves the log one item at a time on the `scr-review` screen; a correct answer calls `clearMiss(key)` and removes it. Items are normalized to `{type:"choice"|"fill", ...}`.
- **Weak Spots Drill:** `buildWeakSpots()` ranks attempted `(lid,act)` by `ST.mastery` ascending, pulls a 12-question drill from the 4 weakest lessons' mcq/err/fill banks; `openWeak()` runs it through the same `renderReview` engine (`RV.kind==="weak"` — does **not** clear misses). Orange home card (`weakCardHTML`) + sidebar row.

### Teacher Mode tab (per lesson)
- `TEACH[lid]` = `{errors:[{w,r,n}], script, ccq:{q,a}, examples[], activity}` for all 15 lessons; attached via `LD[k].teach=TEACH[k]`. `renderTeacher(lid)` shows common student errors (✗/✓/note), a plain-English teaching script, a model CCQ, board-ready examples, and a mini-activity. Read-only (no grading/XP). To extend, add to the `TEACH` object.

### Write (create-your-own-sentences) — self-check writing activity
- `WRITE[lid]` = array of prompts `{task, model, checklist[]}`; attached via `LD[k].writeBank=WRITE[k]` in a loop (like `EXAM_EXTRA`). Authored for all 5 active lessons (3–4 prompts each).
- Flow: type a sentence → **Reveal model answer** → shows model + a green-checked checklist → self-rate **Nailed it (+10 XP)** / **Needs review (+5 XP)**.
- Each rated sentence is saved to a personal **example bank** (`ST.examples[lid]=[{task,text,rating,date}]`), shown below the activity with delete (×) — reusable as Mohammad's classroom examples.
- Mastery % = nailed / total (via `markActDone(lid,"write",score,total)`). **Not** part of the 5-activity completion gate (like Comprehension — it's an extra tab). Functions: `renderWrite`/`writeBank`/`wType`/`wReveal`/`wRate`/`saveExample`/`delExample`/`exampleBankHTML`/`getExamples`. `esc()` HTML-escapes user text. To add prompts for new lessons, add a `WRITE` entry.

### Completion logic
A lesson is "complete" when all 5 graded activities (mcq, fill, match, tap, **err**) are done → +50 XP (Write & Comprehension are not gated)

### Sub-lessons (drill-down from Overview tab)
The Overview is now a teach-first **Learn page** (see architecture notes); sub-lessons render below it as "deep dives". Each of the 8 active lessons has 3–4 sub-lessons with:
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
| Phrases | Noun Phrases · Verb Phrases · Prepositional Phrases · Adjective & Adverb Phrases |
| Clauses | Independent · Dependent · Relative · Noun Clauses |
| Sentence Types | Simple · Compound · Complex & Compound-Complex · Sentence Purposes |
| Punctuation | The Comma · Semicolons & Colons · Apostrophes · Quotation Marks/Dashes/Hyphens |
| Common Mistakes | Confusing Pairs · Pronoun Case · Fewer/Less & Mix-ups · Double Negatives |
| Word Choice & Register | Formal vs Informal · Connotation · Collocations · Precise Word Choice |
| Conciseness & Clarity | Cutting Wordiness · Redundancy · Active Voice · Cutting Filler |
| Coherence & Cohesion | Transitions · Reference & Substitution · Topic Sentences · Parallelism |
| Explaining Grammar Simply | Plain Language · Example-First · CCQs · Scaffolding |
| Error Analysis | Errors vs Mistakes · Causes (L1) · Global vs Local · Feedback & Codes |
| Academic vs Everyday | Features · Hedging · Impersonal Voice · Academic Vocabulary |

Sub-lesson ID prefixes (4 each): `pos_*`, `art_*`, `ten_*`, `agr_*`, `phr_*`, `cla_*` (clauses), `sen_*`, `pun_*`, `mis_*`, `reg_*`, `clr_*` (clarity — note: NOT `cla_`), `coh_*`, `expl_*`, `ea_*`, `acad_*`

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
- pos: 33 MCQ, 16 fill, 6 match sets, 14 tap, 10 err
- articles: 32 MCQ, 16 fill, 6 match sets, 10 tap, 10 err
- tenses: 30 MCQ, 16 fill, 6 match sets, 10 tap, 10 err
- agreement: 24 MCQ, 10 fill, 4 match sets, 8 tap, 10 err
- phrases: 14 MCQ, 12 fill, 5 match sets, 10 tap, 6 err
- clauses: 14 MCQ + 8 EXAM_EXTRA, 12 fill, 5 match sets, 10 tap, 6 err + 4 EXAM_EXTRA
- sentencetypes: 14 MCQ + 8 EXAM_EXTRA, 12 fill, 5 match sets, 10 tap, 6 err + 4 EXAM_EXTRA
- punctuation: 14 MCQ + 8 EXAM_EXTRA, 12 fill, 5 match sets, 10 tap, 6 err + 4 EXAM_EXTRA
- `EXAM_EXTRA` (after the sublesson attachments) holds supplemental MCQ/Error-Fix, merged into their banks at load. Covers Lessons 1–4 (deepened Phase 1 pool to ~159) **and now 6–8** (Phase 2 cumulative pool ~275) so each 50-question exam gives 3 fully fresh attempts. Add to it (or any bank) to keep exams/comprehension fresh. (Phrases has no EXAM_EXTRA yet — a good place to deepen further.)

### Key JS architecture notes
- `LD` object holds all lesson data. Sub-lessons registered into `LD` dynamically via `LD[slid]` when first opened
- `SLD` object holds sub-lesson data arrays; attached to LD via `LD.pos.sublessons = SLD.pos` etc.
- `DS` object holds in-session activity state (keyed by `lid_act`)
- `MAIN_LESSONS` Set used to guard `checkComplete` — sub-lesson IDs are excluded
- `ds(lid, act)` / `getQs(lid, act)` / `resetDS(lid, act)` work for both lessons and sub-lessons
- `curL` = current lesson id; `curSL` = current sub-lesson id
- `hl(text)` auto-highlights grammar terms + ALL-CAPS words (bold blue `.hl`) in questions, tasks, fill sentences, explanations, flashcards, and concept cards — applied at render time, so authored content stays plain text (no manual markup needed)
- `getQs` serves questions via `drawQ`/`ST.qpool` (persistent shuffled queue per lesson+activity) — no repeats until the bank cycles. To increase retry variety, enlarge the banks; note `err` banks (6) show all 6, so they need more items for truly fresh retries.
- Phase exams: `openExam(i)`/`renderExam`/`examSubmit` on the `scr-exam` screen; cumulative pool via `buildCompPool(phaseLastLid(i))`, graded at the end, pass 80%, results in `ST.exam["p"+i]={best,passed}`; home card shown when `phaseExamReady(i)` (all phase lessons authored). `examN`=50 questions per exam (capped at pool size); pools must hold ≥150 for 3 non-overlapping attempts
- Lesson Overview is a teach-first "Learn" page: `renderOV` renders `LEARN[lid]` = {intro (short paragraphs, raw HTML allowed), table{head,rows}, hook, watch, tips (teaching tip), takeaways[], check{q,opts,ans,exp}}. Concept cards reuse `defs`; helpers `callout()`/`tableHTML()`/`lcPick()` (inline self-check, no persistence); sub-lessons render below as deep dives. Author a `LEARN` entry for every new lesson.
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
- Use `Edit` tool (not `Write`) for changes — file is large (~212K chars)
- **No Node** — to syntax-check JS: extract the inline `<script>` and run `osascript -l JavaScript` with `new Function(code)`. To visually verify: build a seeded copy (stub Supabase + preset `localStorage`) and screenshot via headless Chrome (`/Applications/Google Chrome.app/Contents/MacOS/Google Chrome --headless=new --screenshot`).
- Use `mcp__nimbalyst-mcp__developer_git_commit_proposal` or standard git for commits

---

## Git state
Branch: `main`. All changes committed and pushed. Recent commits (newest first):
- _(latest)_ Teacher Mode tab (per-lesson teaching toolkit, all 15 lessons)
- `00fd24a` Weak Spots Drill (targeted practice from lowest-mastery lessons)
- `8a1b036` mistake log + "Review My Misses" session
- `f7a8bc7` Phase 4 lessons 13–15 (Teacher's Edge) — all 15 lessons now authored
- `203e796` Phase 3 lessons 9–12 (Precision & Style)
- `b0d75b8` phase final exam added to the left sidebar
- `37ce53f` Phase 2 lessons 6–8 (Clauses, Sentence Types, Punctuation)
- `7e56c55` create-your-own-sentences Write activity + example bank
- `c6a2f91` teach-first Learn overview on every lesson
- `9c6db21` 50-question phase exams with 3 fresh attempts
- `d3c84ae` graded phase final exams
- `bd70790` cumulative Comprehension quiz per lesson
- `23c7e76` stop repeating questions on activity retries
- `96e6300` bigger question text with highlighted keywords
- `c3cc5c5` Lesson 5 (Phrases) with sub-lessons and activities
- `f0645b2` daily streak, goal ring, countdown, study timer (M1)

---

## Roadmap status

See `nimbalyst-local/plans/english-mastery-roadmap.md` for the full 7-milestone plan and locked decisions (target date **2026-08-06**; lessons **co-authored** — Claude drafts, Mohammad supplies select examples; Teacher Mode = **per-lesson tab**).

- ✅ **M1 — Consistency layer** (daily streak, daily-goal ring, Aug-6 countdown, study timer) — shipped 2026-06-06
- ✅ **M2 — Phase 2 content (Lessons 5–8)** — DONE (final exam live).
- ✅ **M3 — Phase 3 content (Lessons 9–12, Precision & Style)** — DONE (final exam live).
- ✅ **M4 — Phase 4 content (Lessons 13–15, The Teacher's Edge)** — DONE (final exam live). **All 15 lessons + all 4 phase exams shipped.**
- Remaining: M5 Teacher Mode · M6 Review/retention (mistake log, weak-spots) · M7 Polish.

---

## Next up (start here) — polish & extras
✅ **Curriculum complete** (15 lessons, 4 exams) AND ✅ **Retention pack** (mistake log + Review My Misses + Weak Spots Drill) AND ✅ **Teacher Mode** all shipped. Remaining ideas (all optional polish):

1. **Progress/stats dashboard** — XP over time, strongest/weakest lessons, exam history, mastery heatmap, Aug-6 trajectory. (Mohammad was offered this; he chose retention + teacher mode first — it's the natural next pick.)
2. **Spaced-repetition flashcards** — schedule cards by recall difficulty (the mistake log + weak-spots already cover targeted re-review, so this is lower priority now).
3. **Export** — print a lesson's Teacher Mode notes or the Write sentence bank as a PDF/handout for class.
4. **Keyboard shortcuts** (A–D for MCQ, Enter to check), and an AI grammar-check for the Write tab (serverless function — biggest lift).

**Authoring pattern for a lesson** (if ever revisited): add an `LD.<id>` entry (intro, rule, defs[8], fc[8], mcqBank~14, fillBank~12, matchBank 5, tapBank~10, errBank 6); an `SLD.<id>` array of 3–4 sub-lessons (`{id,icon,title,desc,intro,rule,points[5],mcqBank[4],fillBank[4]}`); `LD.<id>.sublessons=SLD.<id>`; a `LEARN.<id>` entry; a `WRITE.<id>` entry; an `EXAM_EXTRA.<id>` (`{mcq,err}`); flip `unlocked:true` in `PHASES`. **Watch sub-lesson ID prefixes for collisions** (clarity uses `clr_`, not `cla_` which is clauses). Syntax-check by extracting the inline `<script>` (from `const SB=` to `</script>`) and running `new Function(code)` via `osascript -l JavaScript` (see Environment).
4. **Teacher Mode** — per-lesson tab: common student errors + plain-English explanation scripts + classroom examples (expands on the per-lesson Teaching tip).

## Working style with Mohammad (important)
- For each feature: briefly propose the design, confirm choices with an interactive prompt, **build**, **screenshot to verify** (headless-Chrome workflow under Environment), then **commit + push** (auto-deploys). He likes seeing the result and shipping each piece.
- Content is **co-authored**: draft strong defaults, personalize (Mohammad / Kuwait University / Penn State / Rochester), and flag where his own examples would add most value.
- He prefers **concise, scannable** content over long paragraphs (a few short paragraphs are fine), with **bold/highlighted keywords**.

## Suggested next features (original brainstorm — superseded by the roadmap above)

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
1. Read this file.
2. Explore `index.html` by **grepping** for specific functions/sections — it is ~212K chars, so don't read it whole.
3. Build the next queued item (see **"Next up (start here)"**), following the **Working style** above: confirm design → build → screenshot-verify → commit + push. Quick-confirm with Mohammad first if a design choice is open.
