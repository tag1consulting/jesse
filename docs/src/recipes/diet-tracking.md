# Diet & Exercise Tracking

Daily nutrition and exercise tracking with a Markdown journal, append-only CSV logs, a chat dashboard, and a live HTML dashboard. Includes a first-run setup wizard that derives your targets from first principles, a floors/ceilings/windows macro model, an extensible day-style registry, adaptive calorie targets with a tracker-overestimation haircut, weight tracking with progress visualization, body composition metrics, alcohol enforcement, and weekly accountability reports.

> **This recipe is optional.** When disabled, the assistant never mentions food, calories, exercise, or weight tracking. It does not include meal planning, recipe suggestions, diet advice, or nutrition coaching. The system records and displays -- it doesn't lecture.

## What You Get

- **Daily journal** -- `Projects/Diet/YYYY-MM-DD.md` with structured meal tables, activity logs, and running totals
- **CSV logs** -- `diet-logs/food-log.csv`, `diet-logs/exercise-log.csv`, `diet-logs/weight-log.csv` (weight is its own log, not a workbook sheet) at workspace root
- **Chat dashboard** -- Colored emoji bars with macro flags and adaptive calorie display after every food or exercise log
- **Live HTML dashboard** -- `Dashboard-Fancy.html` at vault root; a static shell that renders external JS data files -- macro bars update on every log via `diet-today.js`, the weight chart and pace bars via data files regenerated on weigh-in. The HTML itself never changes for data
- **Weight tracking** -- Two progress bars toward user goals, trend analysis, body composition metrics (optional), phases for different training cycles
- **Coach's Notes Log** -- `Knowledge/Health/Coach-Notes-Log.md` tracks dashboard notes across days for consistency; prevents advice from contradicting itself or chasing noise
- **Weekly accountability** -- Sunday routine with automated analysis report (four sections: Great, Good, Bad, Ugly) plus a floor-miss pattern check, saved to Knowledge/Health/Weekly-Diet-Analysis/
- **Setup wizard** -- A first-run conversational flow that computes your calorie and macro targets from age, height, weight, body composition, activity, and goal; uses the sex-independent Katch-McArdle formula when body-fat % is known and self-corrects via a calibration loop
- **Floors / ceilings / windows macro model** -- Each macro is a floor (protein, carbs), a ceiling (calories), or a window (fat); goal chips, bar colors, and flags all derive from the type
- **Day-style registry** -- An extensible table of day styles (normal, endurance, refeed, sick, carb-load, fasting) that sets which macros are floors, ceilings, or windows for the day
- **Adaptive calorie target** -- Exercise add-back with an overestimation haircut: discount the logged burn (default 25%), then eat back only half — keeps training-day deficits meaningful without trusting noisy tracker estimates
- **Alcohol enforcement** -- Alcohol always logged as a separate entry; next-day journal note required with calorie count and percentage of daily target

## Getting Started: The Setup Wizard

The tracker works for anyone — weight loss, maintenance, recomposition, or athletic training, for any sex — without hand-tuning targets. The first time you turn it on (or say "set up my diet targets"), the assistant runs a short conversational **setup wizard** that derives your starting calorie and macro targets from first principles. Full spec: `Knowledge/Jesse-Guidelines/Diet-Setup-Wizard.md`.

It asks, in order: units, age, height, current and goal weight, **body-fat % if you know it**, sex *only if body-fat % is unknown*, lifestyle activity level (excluding workouts — those are added per-session), your goal and target rate, and any training events.

**How your targets are computed:**

- **BMR.** If you know your **body-fat %**, the wizard uses **Katch-McArdle** (`BMR = 370 + 21.6 × lean-mass-kg`) — it's based on lean mass, so it's both more accurate and needs no sex input at all. Otherwise it uses **Mifflin-St Jeor**, which needs a sex coefficient (`+5` male / `−161` female).
- **TDEE** = BMR × your activity factor (lifestyle only).
- **Base calorie target** = TDEE minus a deficit (loss), TDEE (maintain), or plus a surplus (gain), capped at ~1% bodyweight/week of loss to protect lean mass.
- **Macros:** protein floor (~1.8–2.2 g/kg), fat floor (~0.5 g/kg) up to a calorie-share cap, carbs as the remainder.

**Sex is a calculation input, not an identity question.** The coefficient reflects average lean-mass differences. If you'd rather not answer, you can enter a measured BMR/TDEE from a lab test or tracker, or use the midpoint of the two coefficients — and either way the **calibration loop** corrects everything from your real data after 2–3 weeks of logging (comparing your actual weight trend to the prediction, 7,700 kcal ≈ 1 kg). The initial estimate is just a seed.

### The macro model: floors, ceilings, and windows

Every macro target is one of three types, and the type — not the number — decides what "good" looks like and how the bar is colored:

| Type | Symbol | Good means | Examples |
|------|--------|------------|----------|
| **Floor** | `≥` | at or above target | protein, carbs |
| **Ceiling** | `≤` | at or under target | calories on normal days |
| **Window** | `↕` | between a floor and a cap | fat (floor + cap); calories on high-fuel days |

Protein is a floor because, on a deficit, adequate protein is what makes you lose fat instead of muscle. Fat is a *window*, not just a cap: too little fat suppresses hormones and blocks fat-soluble vitamin absorption (a risk that rises with age and at low body-fat), so the fat bar turns **red when you're too low**, not only when you're over. Carbs are a remainder floor. Each dashboard bar shows a colored goal chip for its type, and a compact legend sits at the bottom of the macro panel.

### Adaptive calorie target (the exercise haircut)

Logged exercise raises your calorie target in two steps, because trackers overestimate burn and eating back the full burn erases your deficit:

```
Calorie target = base + 0.50 × (logged burn × (1 − 0.25))
```

First discount the logged burn by 25% (the tracker haircut — swims and ellipticals over-report the most), then add back only half. A 1,000-kcal logged session raises your target by ~375, not 500. Both rates are configurable. Rest days log no exercise, so the target stays at base — rest vs. training is handled by the add-back, not a separate category.

### Day styles

Most days are `normal`. For special days, set a **day style** and the dashboard reshapes the bars accordingly — for example, on a carb-load day calories become a *window* and fat a *minimize-it ceiling* to free room for carbs. The styles ship as an extensible registry (`normal`, `long-run`/`endurance`, `refeed`, `sick`, `carb-load-training`, `carb-load-race`, `fasting`); adding one is a single table row. The canonical table lives in `Diet-Logging-Flow.md`, and the HTML keeps a matching `STYLE_PROFILES` map in sync.

## Vault Structure

```
Dashboard-Fancy.html         # Live HTML dashboard (vault root)
diet-today.js                # Today's food/macro data for the HTML dashboard (vault root)
diet-logs/                   # Append-only CSV logs (workspace root) -- the permanent record
  food-log.csv               # One row per food item (Date, Meal, Item, ..., Fiber_g)
  exercise-log.csv           # One row per activity
  weight-log.csv             # One row per weigh-in (Date, Weight_lbs, ..., Phase, ...)
Projects/
  Diet/
    Overview.md              # Targets, goals, phases, body composition goals
    YYYY-MM-DD.md            # Daily journals (one per day)
Knowledge/
  Jesse-Guidelines/
    Diet-Setup-Wizard.md           # First-run target derivation (BMR/TDEE, macros, calibration)
    Diet-Logging-Flow.md           # Per-log-event flow, diet-today.js spec, macro model, day-style registry
    Diet-Dashboard-Guidelines.md   # ASCII dashboard format, goal chips, floor/ceiling/window colors, gated flags
    Weight-Tracker-Guidelines.md   # Weight tracking, progress bars, trends, phases
    Fancy-Dashboard-Build.md       # HTML dashboard architecture — single source of truth
    Sunday-Weekly-Diet-Analysis.md # Weekly accountability report format
  Health/
    Coach-Notes-Log.md             # Rolling log of dashboard coach's notes (30-day retention)
    Weekly-Diet-Analysis/          # Archive of weekly reports (date-prefixed)
```

## Configuration

### 1. Create the Diet directory

```bash
mkdir -p Projects/Diet Knowledge/Health/Weekly-Diet-Analysis
```

### 2. Create your Overview file

Create `Projects/Diet/Overview.md` with your targets and goals. Use this template:

````markdown
# Diet Journal -- Overview

Daily food tracking. Individual daily logs in this directory, named `YYYY-MM-DD.md`.

---

## Goals

Your personal goals drive progress bars and phases. Examples:
- Lose weight for an event (wedding, reunion, vacation, photo shoot)
- Hit a target weight or body composition
- Train for a race or athletic event
- Build muscle / body recomposition
- Improve energy levels or manage a health condition
- General longevity and health

**My goal(s):**
- [your goal here]

---

## Current Metrics

- **Current weight:** [weight] lbs / [kg]
- **Current body fat %:** [if you have it; optional]
- **Current muscle mass:** [if you have it; optional]
- **Age/sex:** [for TDEE context]

## Target Metrics

- **Target weight:** [weight] lbs / [kg]
- **Target body fat %:** [optional]
- **Target muscle mass:** [optional]

## Calorie Targets

| Metric | Rest Day | Training Day | Long Event Day |
|--------|----------|--------------|----------------|
| Base calories | [e.g., 1,900] | [e.g., 2,200] | [e.g., 2,500] |
| Protein | [e.g., 130g] | [e.g., 150g] | [e.g., 160g] |
| Carbs | [calculated] | [calculated] | [calculated] |
| Fat | [calculated] | [calculated] | [calculated] |

**How adaptive targets work:**
- **Base calories:** Your maintenance or deficit/surplus baseline (e.g. TDEE − 300 for a cut). The setup wizard derives this; see `Diet-Setup-Wizard.md`.
- **Exercise add-back with haircut:** discount the logged burn by `[TRACKER_HAIRCUT]` (default 25%), then add back `[ADD_BACK_RATE]` (default 50%): `base + 0.50 × (burn × 0.75)`.
  - Example: 800 kcal run → 800 × 0.75 = 600, × 0.50 = +300 → target becomes 1,900 + 300 = 2,200 cal
  - Discounting first guards against noisy tracker estimates; eating back only half keeps the deficit meaningful
  - Edit `[TRACKER_HAIRCUT]` and `[ADD_BACK_RATE]` here to adjust

**Macros (floors, ceilings, windows):**
- Protein: a **floor** (≥), set first — ~1.8–2.2 g/kg, higher while cutting
- Fat: a **window** (↕) — a floor (~0.5 g/kg, protects hormones and vitamin absorption) up to a cap (~30% of calories / 9 cal per gram)
- Carbs: a **floor** (≥), the remainder after protein and fat
- Calories: a **ceiling** (≤) on normal days; a window on high-fuel days

## Day Styles

Most days are `normal` (rest or training — the exercise add-back differentiates them). Set a `dayStyle` only for special days; the registry in `Diet-Logging-Flow.md` is canonical:
- **`normal`:** default — calories ceiling, fat window, protein/carbs floors
- **`long-run` / `endurance`:** a long session that day; adds a glycogen carb bonus
- **`refeed`:** periodic diet-break on a long cut; pauses the deficit, extra calories to carbs
- **`sick`:** illness/recovery; eat to maintenance, floor-miss flags suppressed
- **`carb-load-training` / `carb-load-race`:** before a long event; calories become a window, fat a minimize-it ceiling
- **`fasting`:** optional, off by default; confirm your protocol's numbers

## Weight Phases

Define phases for different training cycles. `diet-logs/weight-log.csv` includes a Phase column.

Example phases:
- **Phase 1: Aggressive cut** (target -1.5 lbs/week)
- **Phase 2: Moderate cut** (target -0.75 lbs/week)
- **Phase 3: Maintenance** (stable)
- **Phase 4: Build** (slight surplus)

*Format: Name | Start Date | Target Weight | Target Rate | Notes*

| Phase | Start | Target Weight | Target Rate | Notes |
|-------|-------|---------------|-------------|-------|
| [phase 1] | [date] | [weight] | [rate] | |
| [phase 2] | [date] | [weight] | [rate] | |

## Snack Library

*Add foods you eat regularly; sort by macro benefit or category.*

### High Protein (quick snacks)
- [your items]

### Quick Carbs (training fuel)
- [your items]

### Balanced Options
- [your items]

## Event Prep Modes

Optional: If you're training for an event, define special target overrides. These override normal targets temporarily (e.g., carb loading, diet break, refeed days).

| Mode | Trigger | Calorie Mod | Carb Mod | Duration | Notes |
|------|---------|------------|----------|----------|-------|
| [e.g., Carb Load] | 2 days before race | +300 | +50g | 2 days | race prep |

## Running Averages

*Updated as journals accumulate.*

| Period | Avg Calories | Avg Protein | Days Tracked | Notes |
|--------|-------------|-------------|--------------|-------|
| Last week | -- | -- | 0 | |
| Last 2 weeks | -- | -- | 0 | |
````

### 3. Copy the guidelines files from the template

The template ships these files in `Knowledge/Jesse-Guidelines/`. Copy them to your vault:

| File | Purpose |
|------|---------|
| `Diet-Setup-Wizard.md` | First-run target derivation — BMR/TDEE math, sex/non-binary handling, macros, calibration loop |
| `Diet-Logging-Flow.md` | Per-log-event flow, `diet-today.js` spec, macro model, calorie haircut formula, Day-Style Registry, floor-miss monitoring |
| `Diet-Dashboard-Guidelines.md` | ASCII dashboard format, goal chips, floor/ceiling/window colors, time-gated flags, sync rules |
| `Weight-Tracker-Guidelines.md` | Weight tracking, 14-day dual regression, progress bars, phases, body composition |
| `Fancy-Dashboard-Build.md` | HTML dashboard architecture — single source of truth for `Dashboard-Fancy.html` |
| `Sunday-Weekly-Diet-Analysis.md` | Weekly accountability report format (Great / Good / Bad / Ugly) + floor-miss pattern |

Also copy `Knowledge/Health/Coach-Notes-Log.md` from the template.

### 4. Add to your instruction file

Add to your instruction file's `## Rules` section:

```markdown
### Diet Tracking

Daily food journal at `[[Projects/Diet/Overview]]` (targets and goals defined there). Daily logs are `Projects/Diet/YYYY-MM-DD.md`. CSV logs in `diet-logs/` at workspace root: `food-log.csv`, `exercise-log.csv`, `weight-log.csv` -- the single source of truth; append the CSV row first on any log event. When the user logs a meal or exercise, follow `[[Knowledge/Jesse-Guidelines/Diet-Logging-Flow]]`. When the user asks about their state without new data, show the ASCII dashboard per `[[Knowledge/Jesse-Guidelines/Diet-Dashboard-Guidelines]]`. Weight tracking uses `diet-logs/weight-log.csv` per `[[Knowledge/Jesse-Guidelines/Weight-Tracker-Guidelines]]`. HTML dashboard lives at `Dashboard-Fancy.html` (vault root) — see `[[Knowledge/Jesse-Guidelines/Fancy-Dashboard-Build]]` for build rules. Every Sunday, produce a weekly accountability report per `[[Knowledge/Jesse-Guidelines/Sunday-Weekly-Diet-Analysis]]` and save to `Knowledge/Health/Weekly-Diet-Analysis/`.
```

### 5. Create the CSV logs

On first use, create `diet-logs/` at the workspace root with three CSV files. Each holds exactly its header row until the first log event; every log appends one complete row.

**diet-logs/food-log.csv:**
```
Date,Meal,Item,Amount,Unit,Cal_per_100g,Grams,Calories,Protein_g,Fat_g,Carbs_g,Notes,Time,Meal_Type,Fiber_g
```

**diet-logs/exercise-log.csv:**
```
Date,Type,Description,Distance_km,Duration,Pace_min_per_km,Elevation_m,Avg_HR,Cadence,Calories,Plan_Source,Notes,Start_Time
```

**diet-logs/weight-log.csv:**
```
Date,Weight_lbs,Weight_kg,Phase,BodyFat_pct,MuscleMass_lbs,Notes
```

Optionally add `diet-logs/daily-targets.csv` (`Date,Mode,Cal_Target,Carb_Target_g,Protein_Target_g,Fat_Target_g,Exercise_Cal,Notes,Fiber_Target_g`) if you want a per-day record of what the targets were, not just what was eaten.

Blank cells stay blank -- an empty BodyFat_pct or Fiber_g field is the honest "not measured" signal, never `0` or `None`. Every appended row is complete (its own Date, Meal or Type, and Time).

## Why CSV Files

The logs are plain CSV on purpose. An earlier iteration of this system used Excel
workbooks (`food-log.xlsx` with a Weight sheet, `exercise-log.xlsx`); they were replaced
with three append-only CSV files, and the difference shows up every day:

- **Agent-native.** The assistant reads and appends CSV with ordinary file tools -- no
  spreadsheet library, no binary round-trip, no risk of a half-written workbook. A log
  event is one appended line.
- **Git-diffable.** A day of eating is a handful of added rows in `git diff`. Binary
  workbooks are opaque blobs; CSVs give you history, blame, and clean merges in a vault
  that is already a git repository.
- **Human-inspectable.** `head`, `grep`, and any text editor can audit any entry. Nothing
  hides in cell formatting or formulas.
- **Atomic appends.** Each log is one new row at the end of one file. There is no
  workbook-wide rewrite, so an interrupted write can't corrupt months of history.
- **No Excel dependency.** Nothing to install, license, or version-match -- any language,
  any tool, indefinitely.
- **Merge-conflict-resolvable.** If two machines both appended rows, the conflict is
  visible text with an obvious resolution. With xlsx it's a coin flip.

Two rules keep the CSVs trustworthy:

- **RFC-4180 quoting.** Food items and notes routinely contain commas ("Egg (whole,
  large)"). Any field containing a comma, double-quote, or newline must be wrapped in
  double-quotes with embedded quotes doubled (`""`).
- **Always parse with a real CSV reader** -- Python's `csv`, Node's `csv-parse`, or
  equivalent. Never `line.split(',')`; it silently mangles quoted fields, and the Notes
  column will eventually contain a comma.

The daily journal and the dashboard JS files are *derived* views. If they ever disagree
with the CSVs, the CSVs win -- regenerate the views, don't edit them by hand.

## Daily Journal Format

Each day's journal follows this structure:

````markdown
# Food Journal -- [Day], [Date]

**Day type:** [Rest / Training / Long Event] ([activity context])
**Weight:** [if weighed in]
**Phase:** [current phase, if tracking]

## Breakfast

| Item | Amount | Grams | Calories | Protein | Fat | Carbs | Notes |
|------|--------|-------|----------|---------|-----|-------|-------|
| | | | | | | | |
| **BREAKFAST TOTAL** | | **g** | **cal** | **g** | **g** | **g** | |

## Lunch

[same table format]

## Dinner

[same table format]

## Snack

[same table format -- added when snacks are logged]

## Activity

**Activity 1:** [type]
**Workout stats:** [duration] | avg HR [bpm] | [distance if applicable] | [calories burned]
**Notes:**

## Running Totals

| Meal | Calories | Protein | Fat | Carbs |
|------|----------|---------|-----|-------|
| Breakfast | | | | |
| Lunch | | | | |
| Dinner | | | | |
| Snack | | | | |
| **Day Total** | | | | |

**Target ([day type]):** [adaptive cal] cal / [protein]g protein
**Remaining:** [cal] cal, [protein]g protein
**Exercise burn:** [total] kcal

**Progress to goals:**
- Weight: [current] → [target] ([days to goal at current pace])
- [Other goals as applicable]
````

## How It Works

### Dashboard Architecture: Static Shell + Data Files

`Dashboard-Fancy.html` (vault root) is a **static shell** -- structure, CSS, and rendering JavaScript only, with no data inside. All data lives in small external JS files next to it, loaded with a cache-busting loader so the browser never serves a stale copy:

- **`diet-today.js`** -- today's meals, exercise, targets, and weigh-in as `window.DIET_TODAY`. Rewritten on every food or exercise log; drives the macro bars, food journal, and exercise cards.
- **`diet-weight-history.js`**, **`diet-progress.js`**, **`diet-coach-notes.js`** -- weight trend, progress/pace values, and coach's notes. Regenerated on morning roll and weigh-in; drive the chart, metric cards, pace and progress bars, and notes.
- **`proposed-diet-today.js`** -- optional meal plan (see Meal Planning below).

Reloading the browser picks up whatever changed. The HTML itself is rebuilt only when its layout or rendering logic changes -- never for data. Contracts and build rules: `Fancy-Dashboard-Build.md`.

### Logging Meals

1. User says: "log breakfast: two eggs, toast with butter, coffee with whole milk"
2. Assistant estimates macros from knowledge base; checks `food-log.csv` for recent entries of the same items (real CSV parser) to reuse values (prevents drift)
3. Appends the row(s) to `food-log.csv` first, then updates the daily journal table and recalculates running totals
4. Rewrites `diet-today.js` with updated macro totals, meal items, and flags
5. Shows the ASCII dashboard in chat with colored emoji bars
6. Fancy dashboard auto-updates on next browser reload via `diet-today.js`

### Logging Exercise

1. User says: "logged a 7km run, 38 minutes, avg HR 155"
2. Assistant estimates calories burned, appends the row to `exercise-log.csv`, updates the daily journal Activity section
3. Rewrites `diet-today.js`; ASCII dashboard shows two-line calorie display:
   - Line 1: baseline intake vs. adaptive target
   - Line 2: net (intake − burn), gray bars, informational only
4. Adaptive target uses the exercise add-back with an overestimation haircut: discount the logged burn by 25%, then add back 50% (`base + 0.50 × (burn × 0.75)`). Both rates configurable in `Overview.md`.

### Logging Weight (Weigh-In)

1. User says: "weighed in: 175.5 lbs"
2. Assistant appends a row to `weight-log.csv`
3. Regenerates the dashboard data files (`diet-weight-history.js`, `diet-progress.js`, `diet-coach-notes.js`): weight chart updates, pace bars recalculate from the 14-day trailing window (trough + raw regression shown as a range), progress bars refresh, coach's notes regenerate -- all on the next browser reload, with no HTML rebuild
4. New notes appended to `Knowledge/Health/Coach-Notes-Log.md`

### Logging Alcohol

Alcohol is always logged as a separate **Alcohol** meal entry:
1. Calories logged under meal name "Alcohol"
2. The next day's journal includes a note: total alcohol calories and percentage of daily target

### Day Styles (carb-load, refeed, sick, endurance, fasting)

Set `dayStyle` in `diet-today.js` to reshape the day's bars. The styles live in an extensible registry (canonical table in `Diet-Logging-Flow.md`; matching `STYLE_PROFILES` in the HTML). For example, on a carb-load style:
- Calories render as a **window** (green only within `targets.calories`–`targets.caloriesCap`, not ceiling-only)
- Fat becomes a **minimize-it ceiling** to free calorie room for carbs; carbs get a high floor
- Activation cross-check at day-end: calories in window, carbs ≥ floor, fat ≤ cap

Other styles: `refeed` (diet-break — pause the deficit, extra calories to carbs), `sick` (eat to maintenance, floor-miss flags off), `long-run`/`endurance` (glycogen carb bonus), `fasting` (optional, off by default). Rest vs. training is **not** a style — the exercise add-back handles it.

### Weekly Accountability (Sunday)

1. Every Sunday at start of day, assistant generates a report with four sections: Great, Good, Bad, Ugly
2. Ranked improvement suggestions based on actual data
3. Week-over-week trend table
4. Saved to Knowledge/Health/Weekly-Diet-Analysis/ with date prefix
5. Brief summary shown in morning briefing

### Correcting Entries

If the user corrects a value ("that was 350 not 300"):
- Update the corresponding row in `food-log.csv`
- Update the daily journal table
- Recalculate running totals
- Rewrite `diet-today.js` with corrected values
- Refresh ASCII dashboard in chat

## Optional: DuckDB for Dynamic Queries

The baseline flow needs nothing beyond the CSV files and a text editor. If your logs grow
to months of data and you want ad-hoc analysis -- weekly roll-ups, compliance counts,
alcohol totals, rolling averages -- [DuckDB](https://duckdb.org/) can query the CSVs in
place. No import step, no database file, read-only:

```bash
duckdb -c "SELECT Date, SUM(Calories) AS cal, SUM(Protein_g) AS protein_g
           FROM read_csv_auto('diet-logs/food-log.csv')
           GROUP BY Date ORDER BY Date DESC LIMIT 7"
```

`read_csv_auto` infers the schema from the header row and handles RFC-4180 quoting
correctly. You get full SQL -- joins across the food, exercise, and weight logs, window
functions for rolling averages -- and it stays instant well past the point where
hand-parsing becomes slow or error-prone.

A pattern that works well: keep your weekly-report queries in a `week.sql` file and run
them every Sunday (`duckdb -c ".read week.sql"`), pasting the output tables into the
accountability report verbatim. The queries do the counting so the analysis can do the
thinking -- and the report's numbers stop depending on anyone's mental arithmetic.

This is strictly optional. The CSVs remain the single source of truth; DuckDB is a
read-only lens over them. If you never install it, everything in this recipe still works.

## Tips and Gotchas

**Estimates are estimates.** Calorie and macro values come from the assistant's knowledge, not a food database API. They're close enough for tracking trends but not lab-precise. Correct values when you know better, and the system reuses those values for future entries of the same food.

**The ASCII dashboard is configurable.** The 20-character bar width, colored emojis, flag thresholds, and adaptive calorie formula are all defined in `Diet-Dashboard-Guidelines.md`. Edit that file to change them.

**Day styles are an extensible registry.** The template ships normal, endurance, refeed, sick, carb-load, and fasting styles; adding one is a single row in the registry table in `Diet-Logging-Flow.md` plus a matching entry in the HTML's `STYLE_PROFILES` map. Each style sets which macros are floors, ceilings, or windows for the day.

**The calorie haircut prevents over-fueling on noisy estimates.** Wearables and machines overestimate burn, so the system discounts the logged number (default 25%) before adding back only half of it. A 1,000-kcal logged session raises the target by ~375, not 500. Both rates are configurable in `Diet-Logging-Flow.md` / `Overview.md`.

**Targets self-correct.** The setup wizard's BMR estimate is a seed. After 2–3 weeks of logging, the calibration loop compares your actual weight trend to the prediction and adjusts your TDEE — so the initial body-composition and sex inputs are low-stakes. See `Diet-Setup-Wizard.md`.

**Weight tracking is optional.** If you don't have a scale, skip `weight-log.csv` and the HTML dashboard. The system works fine with just food and exercise data.

**The CSVs are the long-term record.** Daily journals are good for day-of review, and the JS data files are a display cache. The CSVs are the archive -- they feed weekly/monthly trend analysis and the Sunday accountability reports.

**Food identification prevents drift.** The system reuses nutritional values from your recent `food-log.csv` entries for the same item, preventing the slow creep of inconsistent estimates over time.

**Event-prep modes override targets temporarily.** Define a mode in Overview.md; the dashboard header reflects it and targets shift. Regular targets return when the mode period expires.

**Works with the [Cookbook](./cookbook.md).** When both are active, logging a meal from a tracked recipe auto-populates nutrition data, and meal suggestions consider remaining macro budget. Either works standalone.

**Removing this recipe** is clean: delete `Projects/Diet/`, `Dashboard-Fancy.html`, `diet-today.js`, the `diet-logs/` directory, the guidelines files, the coach's notes log, the weekly analysis archive, and the instruction file hooks. Nothing else references it.

---


## Meal Planning (`proposed-diet-today.js`)

The system supports optional meal planning. When you discuss meal ideas with the assistant ("what should I make for dinner?"), it can write a `proposed-diet-today.js` file at the vault root. The HTML dashboard picks this up and shows a "Meal Ideas" section with estimated macros and a gap analysis ("after these meals, you'll be at X cal, Y protein").

**Lifecycle:**
1. Assistant creates `proposed-diet-today.js` with planned meals and their estimated nutrition
2. Dashboard shows the plan in a "Meal Ideas" panel
3. As you log actual meals, the corresponding ideas are removed from the proposed file
4. When all planned meals are logged (or the day ends), the file is deleted

This is entirely optional. If the file doesn't exist, the dashboard simply hides the Meal Ideas panel.

## Viewing in a Browser

The HTML dashboard loads its data files via relative paths (through a cache-busting loader, so a plain reload always shows current data). Browsers block local file loading (`file://` protocol) for security, so you need a lightweight HTTP server.

Any static file server works. One easy option is [miniserve](https://github.com/svenstaro/miniserve) (Rust, single binary, zero config):

```bash
# Install (macOS)
brew install miniserve

# Serve only the dashboard files via a symlink directory
mkdir -p ~/dashboard
ln -s /path/to/vault/Dashboard-Fancy.html ~/dashboard/index.html
ln -s /path/to/vault/diet-today.js ~/dashboard/
ln -s /path/to/vault/diet-weight-history.js ~/dashboard/
ln -s /path/to/vault/diet-progress.js ~/dashboard/
ln -s /path/to/vault/diet-coach-notes.js ~/dashboard/
ln -s /path/to/vault/proposed-diet-today.js ~/dashboard/

# Serve it
miniserve ~/dashboard --port 8080
# Open http://localhost:8080
```

The symlink directory means you serve only the dashboard and its data files — not your entire vault. To auto-start the server on login, add it to your OS's startup mechanism (launchd on macOS, systemd on Linux). The server is stateless; restarting it loses nothing.

After logging food or exercise, just reload the browser tab — the assistant rewrites the JS data files, and the HTML picks up the new values on reload.

## Debugging

**Macro bars show wrong values after a log.** The fancy dashboard reads from `diet-today.js`. The cache-busting loader means a normal reload always fetches fresh data -- so if the bars are stale after a reload, the file on disk is stale: check that the log event actually rewrote `diet-today.js` (and that your server is serving the current file), not the browser.

**Field name mismatch: bars blank or undefined.** If a macro bar in the HTML shows nothing or `NaN`, the JavaScript is reading a field name that doesn't exist in `window.DIET_TODAY`. Compare the field names in `diet-today.js` against the contract table in `Fancy-Dashboard-Build.md`. The contract is exact -- casing and spelling must match.

**Chart missing or broken.** The weight chart and the food log renderer run in separate error isolation blocks. A Chart.js failure will not break the food log, and vice versa. Open the browser console to see which block failed. Common causes: the CDN chart library blocked or offline, or `diet-weight-history.js` missing/empty.

**`textContent` destroying child elements.** If a metric card goes blank after an update, the renderer likely called `element.textContent = value` on a container that has child nodes -- this wipes all children. The fix is to target the specific leaf node (e.g., `card-weight-value` not `card-weight`). See the error isolation section of `Fancy-Dashboard-Build.md`.
