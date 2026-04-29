# Diet Dashboard Guidelines

This guide specifies how to render daily nutrition dashboards in your vault. Dashboards reflect intake against adaptive targets, use emoji bars for quick visual scanning, and flag zones of attention without nagging.

## Core Purpose

The diet dashboard is your **current-state summary** for any given day. It answers:
- How much intake relative to target?
- Which macros need attention before the day ends?
- What's the net balance (intake minus exercise)?
- How am I tracking against weight-loss pace?

Dashboards appear in two contexts:
1. **Daily journal** (`todo-list/Projects/Diet/YYYY-MM-DD.md`) — rendered at the top after your morning weigh-in and updated throughout the day.
2. **Chat display** — when you ask about your nutrition state without logging new data, render this dashboard format in the response.

Dashboards do not appear unless the conversation is food-related.

---

## Dashboard Format

### Header

```
=== Mon Mar 30 | 8.5km run, 600 cal | Target: 1,942 ===
```

Components:
- **Day and date:** Full weekday and date (e.g., `Mon Mar 30`)
- **Exercise summary:** Distance/duration and estimated calories burned (if logged)
- **Calorie target:** The adaptive target for today (baseline plus exercise adjustment)

### Macro Bars (20 chars wide)

```
Cal      🟩🟩🟩🟩🟩🟩⬜⬜⬜⬜⬜⬜⬜⬜⬜⬜⬜⬜⬜⬜    625 / 1,942   (32%)
  Net    ░░░░░░░░░░░░░░░░░░░░     25 / 1,942         ← after 600 cal run
Protein  🟨🟨🟨🟨🟨🟨🟨⬜⬜⬜⬜⬜⬜⬜⬜⬜⬜⬜⬜⬜     51g / 150g    (34%)
Carbs    🟥🟥⬜⬜⬜⬜⬜⬜⬜⬜⬜⬜⬜⬜⬜⬜⬜⬜⬜⬜     18g / 180g    (10%)  ← low
Fat      🟩🟩🟩🟩🟩🟩🟩🟩🟩🟩🟩🟩🟩🟩⬜⬜⬜⬜⬜⬜     36g / 70g     (51%)

Remaining (baseline): ~1,317 cal, 99g protein
```

#### Row Layout

**Calories (floor metric — lower bound)**
- Line 1: Full intake vs adaptive target
- Line 2 (indented `Net`): Intake minus exercise calories (informational, always ░ gray, shown only when exercise is logged)

**Protein** (floor metric)
- Goal: user defines in `Overview.md`

**Carbs** (floor metric)
- Goal: user defines in `Overview.md`

**Fat** (ceiling metric — upper bound)
- Goal: user defines in `Overview.md`

**Remaining line** (informational, not a bar)
- Summarizes unmet baseline target and protein gap
- Helps plan remaining meals

#### Bar Rules

**Physical:**
- Each bar is exactly **20 characters** wide
- Empty position = `⬜`
- Filled positions: all blocks use the **same color** for that metric (consistency rule)
- Percentage calculation: `(actual / target) × 100`, rounded to nearest integer
- If percentage rounds to 100%, the bar **must** be fully filled (20/20 blocks)

**Color Zones (Floor Metrics: Calories, Protein, Carbs)**
- **0–49%:** 🟥 Red (low)
- **50–79%:** 🟨 Yellow (moderate, may want to eat more)
- **80–100%:** 🟩 Green (on target)
- **>100%:** 🟩 Green + overage badge (e.g., `[+15g over]`)

**Color Zones (Ceiling Metrics: Fat on normal days, normal-day calories)**
- **0–79%:** 🟩 Green (good)
- **80–99%:** 🟨 Yellow (approaching limit)
- **≥100%:** 🟥 Red + overage badge (e.g., `[+5g over]`)

**Special: Net Calories Line**
- Always render as ░ (light gray blocks, not colored)
- Purpose: show the informational net after exercise, no goal zone
- Only appears if exercise is logged

---

## Adaptive Calorie Target

The calorie target adjusts automatically based on exercise, removing the need for rigid day-type categories.

### Formula

```
Adaptive Target = Base Target + (Exercise Calories × Adjustment Rate)
```

**Base Target**
- User defines this in `todo-list/Projects/Diet/Overview.md`
- Typically: TDEE − desired daily deficit (e.g., 2,000 − 100 = 1,900)

**Adjustment Rate**
- Default: 7% of exercise calories
- User can override in `Overview.md` (e.g., "Rate: 10%" for aggressive, "Rate: 5%" for conservative)
- Rationale: typical efficiency loss in fueling exercise; not a 1:1 eat-back model

**No Exercise**
- Adaptive Target = Base Target
- Single calorie line shown (Cal, no Net)

**Exercise Logged**
- Both lines appear: Cal (colored, vs adaptive target) and Net (gray, informational)
- Exercise burns energy; target rises to account for the additional fuel demand

### Example Scenarios

| Base | Exercise | Rate | Adaptive | Notes |
|------|----------|------|----------|-------|
| 1,900 | 600 cal | 7% | 1,942 | 600 × 0.07 = 42 cal added |
| 1,900 | 200 cal | 7% | 1,914 | 200 × 0.07 = 14 cal added |
| 1,900 | 0 cal | 7% | 1,900 | No exercise = baseline |
| 1,900 | 800 cal | 10% | 1,980 | User-set 10% rate: 800 × 0.10 = 80 added |

---

## Two Calorie Lines (When Exercise is Logged)

**Cal line (primary, colored)**
- Renders as: intake vs **adaptive target**
- Color zone: ceiling metric (0–79% green, 80–99% yellow, ≥100% red)
- Purpose: main tracking line — shows if you're in fuel balance
- Flag rule: if ≥90% with meals remaining → `← high` (see Flags below)

**Net line (secondary, informational, always gray)**
- Renders as: intake − exercise calories vs **baseline target**
- Always ░ (light gray blocks, never colored)
- Not a goal zone, just data
- Purpose: show what you've consumed net of activity — useful for understanding recovery fuel
- **Important:** Do not suggest "eating back" exercise calories. The adaptive target already accounts for the adjustment.

### Example

```
Cal      🟩🟩🟩🟩🟩🟩⬜⬜⬜⬜⬜⬜⬜⬜⬜⬜⬜⬜⬜⬜    625 / 1,942   (32%)
  Net    ░░░░░░░░░░░░░░░░░░░░     25 / 1,942         ← net after 600 cal exercise
```

- Intake: 625 cal
- Exercise: 600 cal
- Adaptive target: 1,942 (1,900 baseline + 7% of 600)
- Cal line: 625 / 1,942 = 32% (green, well under target)
- Net line: (625 − 600) / 1,942 = 25 / 1,942 (gray, informational: you've consumed 25 cal net of activity, showing strong recovery fuel availability)

---

## Flags

Flags are **one-line markers** that appear at the end of rows to highlight attention zones. They do not lecture; they state facts that help you make decisions.

### Flag Triggers

**Post-breakfast (after logging breakfast meal):**
- Protein < 25g → `← low` (undershot early protein opportunity)

**Post-lunch (after logging lunch meal), only on exercise days:**
- Carbs < 30% of daily target → `← low` (underfueled for afternoon activity)

**Any meal:**
- Fat ≥80% of daily target → `← high` (approaching ceiling)

**End-of-day (before evening meal or at day-end summary):**
- Calories ≥90% of adaptive target with logged meals remaining → `← high` (little room for evening intake)

### Behavior

- Flags appear inline on the affected row (e.g., `Carbs    🟥🟥⬜⬜ ... 18g / 180g   (10%)  ← low`)
- No explanations or suggestions — the flag is the nudge
- If a flag no longer applies (e.g., you ate breakfast protein and now you're at 50%), remove the flag on the next dashboard update
- Multiple flags per row are okay (e.g., `← low and approaching window`)

---

## Three-View Sync

Every nutrition log (meal, exercise, weigh-in) must update three places:

1. **Daily journal** (`todo-list/Projects/Diet/YYYY-MM-DD.md`)
   - Add meal/exercise entry
   - Rerender the dashboard at the top

2. **Spreadsheet(s)** (optional but recommended)
   - Food log: `food-log.xlsx` at workspace root
   - Exercise log: `exercise-log.xlsx` at workspace root
   - Use these for long-term trend analysis outside the vault

3. **Chat dashboard** (when you query state without logging)
   - Render the dashboard in the response using the same format
   - No dashboard unless conversation is nutrition-related

Syncing prevents drift and keeps all tools speaking the same language.

---

## Weight Tracker Integration

When you log a weigh-in, the dashboard expands to include weight context. Reference [[Knowledge/Jesse-Guidelines/Weight-Tracker-Guidelines]] for the full spec.

**Brief integration:**
- Render weight progress bars below the macro bars on weigh-in days
- Show pace (regression-based rate of change)
- Display optional body composition (if tracked)
- Example:

```
Weight   ████████░░░░░░░░░░░░  210.5 / 175.0 goal  (−35.5 lbs)
Pace (7d): −0.8 lbs/wk → ETA goal in 44 weeks
```

---

## Fancy Dashboard (Optional)

For visual learners, create a standalone HTML file with interactive charts.

**When to use:**
- On weigh-in days, offer both text dashboard (quick) and HTML dashboard (visual)
- User can open the HTML file alongside their vault for at-a-glance trends

**Contents:**
- Chart.js line chart: weight trend (7-day rolling average + raw points)
- Metric cards: current macros, calorie deficit, weekly pace
- Emoji bars: day's macros (same format as text dashboard)
- Progress bar: toward weight goal

**Implementation:**
- Self-contained HTML (no external dependencies except Chart.js from CDN)
- Inline CSS and JavaScript
- Save to `todo-list/Projects/Diet/dashboard.html` (user can refresh it manually)
- Example header:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Diet Dashboard</title>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <style>/* inline CSS */</style>
</head>
<body>
  <!-- weight trend chart, macro bars, progress cards -->
</body>
</html>
```

Keep it simple — the point is visual feedback, not feature bloat.

---

## Event-Prep Mode Overrides

Users may want temporary target adjustments (carb-loading, refeed days, diet breaks, event prep). When active, targets and color zones shift.

**How to set up:**
- Add to `Overview.md`: `Mode: carb-load | Period: Mar 27–29 | Targets: Cal 2,200, Carbs 300g`
- Can define multiple modes (carb-load, refeed, maintenance, etc.)

**Dashboard behavior when mode is active:**
- Header shows mode: `=== Mon Mar 30 | CARB-LOAD MODE | Target: 2,200 ===`
- Macro bars use the mode's targets and may shift color zones
  - Example: in carb-load, a 200g carb target shifts the zone boundaries → what was "high" (80%+) at 180g is now "on track" at 200g
- Mode applies only during the specified period; reverts to baseline after

**Color zone shifts in modes:**
- Ceiling metrics (calories, fat) may become floor metrics in a refeed (you want to *reach* the carb target)
- Consult the mode definition in `Overview.md` for the exact rules; apply them consistently

No mode = use base targets from `Overview.md`.

---

## Behavior Rules

These principles keep dashboards useful without noise.

### Don't Nag

- Flags state facts; they don't give advice
- No "you should eat more carbs" lectures — the ← low flag on the carbs line is the nudge
- User interprets the flag in context and decides what to do

### Don't Recalculate Targets

- Base target and adjustment rate are user-defined in `Overview.md`
- Your job: apply the formula, render the dashboard, flag zones
- Don't suggest changes to targets — user owns that

### Estimates from General Knowledge

- If a meal is estimated (e.g., "a bagel with cream cheese"), use typical nutrition facts, not APIs
- Store estimates in the daily log so user can refine them later
- Always note estimates: `[est. 350 cal]`

### Only Show Dashboard When Food-Related

- If the day's conversation isn't about nutrition, don't render the dashboard unprompted
- If user asks "How am I doing today?" and it's not in a nutrition context, respond conversationally without the dashboard
- Render on request or when logging food

### Food Identification: Check Recent Entries First

- If user logs "toast with peanut butter," check the daily log for recent similar meals (same day or same week)
- Reuse nutrition estimates where possible to maintain consistency
- Fall back to general knowledge only if no recent match exists

---

## Cookbook Integration

If you maintain a tracked recipe collection (e.g., `Knowledge/Cookbook/Recipes/`), integrate it with logging.

**When active:**
- If user logs from a tracked recipe name (e.g., "Sourdough Focaccia"), auto-populate nutrition from the recipe file
- Logging becomes: recipe name + quantity → auto-filled nutrition + dashboard update
- User can override nutrition if needed (e.g., "that focaccia is smaller than usual")

**When not active:**
- Ignore cookbook; estimate from general knowledge

**Example:**
```
Logged: 1 slice Sourdough Focaccia (from Knowledge/Cookbook/Recipes/Focaccia.md)
Nutrition: 240 cal, 28g carbs, 6g protein, 12g fat [from recipe]
Dashboard updated.
```

---

## Summary: Daily Workflow

1. **Morning weigh-in:** Log weight → dashboard shows weight progress + pace
2. **Breakfast:** Log meal → dashboard updates; check protein flag
3. **Exercise:** Log activity (distance, duration, estimated calories) → adaptive target rises, Net line appears
4. **Lunch:** Log meal → dashboard updates; check carbs flag on exercise days
5. **Throughout day:** Snacks and meals update the dashboard
6. **Evening:** Last dashboard of the day shows where you landed; informal notes on how you feel
7. **Next morning:** New day file, new dashboard, cycle repeats

Each log is a data point; the dashboard is your mirror. No rules except the ones you set in `Overview.md`.
