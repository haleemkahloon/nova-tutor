# NOVA STEM Tutor — Project Knowledge Base

## 🤖 INSTRUCTIONS FOR CLAUDE (Read This First)

This file is the **complete knowledge base** for an ongoing app development project. The user (Haleem Masood) is building a web app called **Nova** for his two children. When you are in this project:

1. **Always read this file before making any changes** — it tells you exactly what has been built, what the code structure is, and what bugs have already been fixed.
2. **The actual app code lives in `index.html`** — also in this project. Read it with the view tool when you need to make edits. Never guess at the code structure.
3. **Continue from where we left off** — do not suggest starting over or rebuilding from scratch. The app is live and being used by real kids.
4. **After every change**, remind Haleem to download the updated `NOVA_PROJECT_SUMMARY.md` and replace it in the project so the next chat stays current.
5. **Before delivering any file**, run the brace-balance check and duplicate-const check (see Assembly Rules below).
6. **The app is a single file** — everything lives in `index.html`. Always work on a copy (`cp index.html work.html`), verify, then copy to outputs.

---

## What This App Is

**Nova** is a fully working AI-powered tutoring web app for Haleem's children — Ibraheem (4th grade) and Aiza (3rd grade). It is built as a **single `index.html` file** with no build step, hosted on GitHub Pages.

The app covers STEM subjects, Writing, and Critical Thinking through structured lesson → quiz flows powered by the Claude Haiku AI API. It includes a full Parent Hub for Haleem to monitor progress.

---

## The Family

- **Parent account:** `haleem` (Haleem Masood)
- **Student 1:** `ibraheem` — 4th grade
- **Student 2:** `aiza` — 3rd grade

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Frontend | React 18 via Babel CDN (single HTML file, no build step) |
| AI Tutor | Anthropic Claude Haiku (`claude-haiku-4-5-20251001`) |
| Auth & DB | Supabase (PostgreSQL) |
| Voice | Web Speech API (browser built-in, free) |
| Hosting | GitHub Pages |
| Fonts | Nunito + Fira Code (Google Fonts) |

**Supabase URL:** `https://ifayrtuouxlfnvtyhnjz.supabase.co`
- Table `profiles`: `username`, `password_hash`, `profile_data` (JSON), `updated_at`
- Table `parent_accounts`: `username`, `password_hash`, `full_name`, `children[]`

---

## File Structure (inside `index.html`)

Single file, ~3200 lines. Internal order inside `<script type="text/babel">`:

1. Supabase client (`sb` object)
2. Color constants (`KA`, `KAB`, `MN`, etc.) — **declared ONCE only** (duplicate = Babel crash)
3. Resource library (`RESOURCES` — links per topic)
4. `WRITING_TYPES` array — writing module curriculum
5. Game components (`FootballGame`, `ArrowGame`, `RocketGame`, `FrogGame`, `GameBreakScreen`)
6. `CURRICULUM` object — all 6 STEM subjects + 100+ topics
7. `LEVELS`, `getLevel`, `getNextLevel` — XP system
8. `TKW` — topic keyword detection
9. Helper functions (`parseBlocks`, `buildPrompt`, `Blocks`)
10. `App()` component — all state, auth, screens, learning flow, parent hub
11. `ReactDOM.createRoot(...).render(React.createElement(App))`

---

## Subjects & Curriculum

### STEM (6 subjects, 100+ topics)

| Key | Label | Topics | Standards |
|-----|-------|--------|-----------|
| `math` | 🔢 Math | 27 | Common Core + Mathnasium |
| `science` | ⚛️ Science | 20 | NGSS |
| `chemistry` | 🧪 Chemistry | 7 | NGSS PS1 |
| `biology` | 🧬 Biology | 6 | NGSS LS |
| `coding` | 💻 Coding | 14 | Code.org CS Standards |
| `critical` | 🧠 Think Smart | 27 | CritiKid-inspired (see below) |

### 🧠 Think Smart — 5 Tracks, 27 Lessons (CritiKid-inspired)

**Track 1 — Fallacy Detectors (ct1–ct10, 10 lessons)**
What Is a Fallacy → Anecdotal → Ad Hominem → Bandwagon → Circular Reasoning → False Dilemma → Straw Man → Appeal to Authority → Slippery Slope → Fallacy Detective Challenge

**Track 2 — Fact vs. Fiction (ct11–ct15, 5 lessons)**
Facts/Opinions/Theories → Spot Fake News → Weasel Words → Evaluating Sources → Social Media Smarts

**Track 3 — Brain Tricks (ct16–ct20, 5 lessons)**
How Your Brain Fools You → Confirmation Bias → False Memories → Occam's Razor → Pareidolia

**Track 4 — Logic & Reasoning (ct21–ct24, 4 lessons)**
Deductive Reasoning → Inductive Reasoning → If-Then Thinking → Socratic Questions

**Track 5 — Debate Club (ct25–ct27, 3 lessons)**
Build an Argument → Steelmanning → Live Debate (Nova argues one side, student argues back, AI coaches)

**Special lesson prompts:**
- `ct27` (Debate): Nova picks a real topic, argues one side, student argues the other, Nova coaches
- `ct10` (Fallacy Challenge): Nova presents 3 real arguments, student identifies each fallacy
- All `ct` nodes use scenario-based quiz questions (real situations, not definitions)

### ✍️ Writing Workshop (separate module, 4 types, 20 lessons)

Accessed via ✍️ Writing card in subject picker. Back to STEM button in sidebar.

| Type | Lessons | Standards |
|------|---------|-----------|
| Essay Writing | 8 | CCSS W.3-5.1/2 |
| Creative Writing | 6 | CCSS W.3-5.3 |
| Persuasive Writing | 3 | CCSS W.3-5.1 |
| Research Writing | 3 | CCSS W.3-5.2/7/8 |

**Writing lesson flow:** Learn → See Example → Write → AI Feedback (praise + suggestions + example fix) → Revise → Done

Writing scores saved to `localStorage` (`nova_writing_${username}`) and `profile.writingHistory` in Supabase.

---

## Key Features

### Voice (Web Speech API)
- Nova speaks every lesson/quiz response
- Best female voice auto-selected: Samantha → Google UK English Female → Microsoft Zira → Karen
- ON/OFF toggle in sidebar. Emojis/markdown stripped before speaking.

### 🎯 Fix This Gap Buttons
Each gap in the sidebar has a "🎯 Fix This Gap" button that finds the weakest topic in that gap area and launches the lesson directly — skipping subject/topic pickers.

### Game Break System
- Popup appears after 15 min of study
- `handleGameReturn()` resets timer to full 15 min from NOW after game ends
- 4 games: 🏈 Field Goal · 🏹 Shoot Arrow · 🚀 Rocket Dodge · 🐸 Frog Jump
- Canvas blank fix: 50ms mount delay in `useAnimFrame`

### XP & Levels (8 levels)
Curious Explorer (0) → STEM Apprentice (100) → Science Scout (250) → Math Maverick (500) → Code Crafter (900) → Nova Scholar (1400) → STEM Champion (2000) → Ivy Bound (3000)

### ⭐ Rewards & Stars Store
Opened by tapping the XP display in sidebar.
- Achievements: Quiz Champion (100% score), Topic Master (5 mastered), Week Warrior (5-day streak)
- Spendable: Bonus Game Break (150 XP), Choose Any Topic (200 XP), Fun Day Pass (100 XP), Home Experiment (250 XP), Story Mode (175 XP)

### 📊 Weekly Report (Parent Hub → Overview)
AI-generated 4-paragraph report: STEM wins + Writing progress + Focus for next week + Activity idea. Downloadable `.txt`. Includes weak/strong writing skills tracking.

### Parent Settings
Edit child name AND grade — fetches fresh from Supabase before saving so nothing gets overwritten.

---

## Profile Data Shape

```json
{
  "username": "aiza",
  "name": "Aiza",
  "grade": "3rd",
  "setupComplete": true,
  "xp": 250,
  "streak": 2,
  "sessions": 8,
  "quizPerfects": 2,
  "mastery": { "math": 45, "science": 30, "coding": 20 },
  "topicMastery": { "m1": 80, "m2": 60 },
  "gaps": ["Fractions & Division"],
  "strengths": ["Counting & Place Value"],
  "interests": ["space", "animals"],
  "sessionLog": [...],
  "redeemedRewards": [...],
  "writingHistory": { "e1": { "score": 8, "skillFocus": "Hook", "date": "..." } },
  "lastWritingSession": "2025-03-27T..."
}
```

---

## Gap Map (must stay in sync across 3 locations in code)

The gap name → topic IDs mapping appears in: `updateGaps`, `getStatus` in TreePanel, and `GAP_MAP` in the sidebar. **All 3 must be updated together when adding new topics.**

```
Fallacy Detectors:  ct1-ct10
Fact vs Fiction:    ct11-ct15
Brain Tricks:       ct16-ct20
Logic & Reasoning:  ct21-ct24
Debate Club:        ct25-ct27
Fractions:          m3,m4,m5,m13,m16
Decimals:           m14,m15
Place Value:        m11,m12
Ratios & Percents:  m17,m18,m19
Pre-Algebra:        m20,m21,m22,m27
Geometry:           m6,m7,m8,m23,m24
Data & Probability: m25,m26
Forces & Motion:    s1,s2,s3,s4
Energy & Waves:     s18,s19,s20
States of Matter:   s5,s6,s7
Life Science:       s11,s12,s13,s14
Earth Science:      s15,s16,s17
Chemistry Basics:   ch1,ch2,ch3
Chemical Reactions: ch4,ch5,ch6,ch7
Cell Biology:       b1,b2
Human Body:         b3,b5,b6
Ecology & Evolution:b4
Logic & Loops:      c3,c4
Variables/Functions:c5,c6,c9,c10
Web & Advanced CS:  c11,c12,c13,c14
```

---

## ⚠️ Assembly Rules — CRITICAL

1. **No duplicate `const KA`** — run `grep -c "const KA=" index.html` → must return `1`
2. **Brace balance check** (run before every delivery):
```javascript
const c = require('fs').readFileSync('index.html', 'utf8');
const sm = c.match(/<script type="text\/babel">([\s\S]*?)<\/script>/);
let depth = 0;
for (const ch of sm[1]) { if (ch==='{') depth++; if (ch==='}') depth--; }
console.log("Brace depth:", depth); // must be 0
```
3. **Keep game functions** — `grep -c "function FootballGame" index.html` must return `1`
4. **No `<form>` tags** in JSX — use `onClick` handlers
5. **Work on a copy** — `cp /mnt/user-data/outputs/index.html /home/claude/work.html`, edit, verify, then copy to outputs
6. **Gap map in 3 places** — update all 3 when adding topics: `updateGaps`, `getStatus`, sidebar `GAP_MAP`
7. **After any str_replace**, check for orphaned duplicate code blocks — common source of brace imbalance

---

## Bugs Fixed (do not re-introduce)

| Bug | Fix |
|-----|-----|
| `Identifier 'KA' already declared` | Only one `const KA` allowed |
| Aiza seeing Ibraheem's name | Logout clears `nova_profile`; login wipes stale cache; profiles stamped with username |
| Grade not saving | Fetch fresh from Supabase before merging name/grade |
| Blank game canvas | 50ms delay in `useAnimFrame` |
| Game timer 13 min after 2-min game | `handleGameReturn()` resets `lastBreakTime = Date.now()` and restarts interval |
| Missing quiz explanations | Validate `explain.trim().length > 5`; fallback if empty |
| Hardcoded "Ibraheem" default | Pre-fill from `localStorage.getItem("nova_user")` |
| Duplicate `startLesson` body | Check for orphaned code after str_replace edits |
| Focus Areas not actionable | Replaced static pills with "Fix This Gap" buttons |

---

## Complete Feature List

- ✅ Student + Parent auth (SHA-256, Supabase)
- ✅ Subject → Topic → Lesson → Quiz structured flow
- ✅ 6 STEM subjects, 100+ topics with mastery tracking
- ✅ 🧠 Think Smart — 5 tracks, 27 CritiKid-inspired lessons with scenario-based quizzes
- ✅ ✍️ Writing Workshop — 4 types, 20 lessons, AI coaching with feedback
- ✅ Dynamic names per logged-in user (no hardcoding)
- ✅ Parent Hub — Overview, Heatmap, Session Log, Settings
- ✅ 📊 Weekly AI reports (STEM + Writing, downloadable .txt)
- ✅ ⭐ Rewards & Stars Store (achievements + spendable XP)
- ✅ 🎯 Fix This Gap buttons in sidebar
- ✅ Game break popup after 15 min, timer resets after game
- ✅ 4 mini-games (Football, Arrow, Rocket, Frog)
- ✅ Web Speech API voice (browser built-in, female voice)
- ✅ XP system with 8 levels
- ✅ Topic mastery bars + prerequisite locks (40% threshold)
- ✅ Resource links per topic (Khan Academy, PhET, CK-12, Critikid, etc.)
- ✅ Edit child name + grade from Parent Hub
- ✅ Writing history tracked in localStorage + Supabase

## Potential Next Steps
- Mobile responsive layout
- Daily streak calendar
- More mini-games
- Email weekly reports to parents
- Dark mode
- Shareable progress report card
