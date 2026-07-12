# Fancy Dashboard Build Rules

Single source of truth for `Dashboard-Fancy.html` (vault root) and its external data files. Read this file in full before building or modifying the dashboard. Do not guess at field names, rendering rules, or architecture -- the contracts below are exact.

---

## Hard Rules (read first)

- **The `?v=Date.now()` cache-busting loader is mandatory.** The data files load via the `document.write` loader (see "HARD RULE: Cache-Bust the Data Files"), NEVER plain static `<script src>` tags. Reverting to static tags reintroduces a browser-cache staleness bug (fresh data on disk, stale bars on screen -- worst on phones). Any HTML rebuild MUST preserve the loader.
- **Rebuild the HTML only on layout/feature/render-bug change -- never on a data update.** Day-to-day updates are data-file rewrites only. The HTML itself is stable.
- **Never regenerate the HTML from scratch -- edit the current file in place.** From-scratch regeneration reliably loses bar algorithms, field names, and error isolation. Start from the existing `Dashboard-Fancy.html`.
- **Journal before rolling to a new day.** The previous day's journal is generated from the still-current `diet-today.js`; write it BEFORE overwriting the file for the new day (full rule below).

---

## Architecture: Static Shell + External Data Files

The HTML is a **static shell** -- structure, CSS, and rendering JavaScript only. It contains NO data. All data lives in external JS files loaded via the per-load cache-busting loader. The user reloads the browser; the loader fetches the current data files with a fresh query string and the HTML renders.

**This means the HTML itself almost never needs rebuilding.** Only rebuild when the layout, styling, or rendering logic changes. Day-to-day updates are data file rewrites only.

### Data Files (all at the vault root, alongside `Dashboard-Fancy.html`)

| File | Global Variable | Updated When | Contents |
|---|---|---|---|
| `diet-today.js` | `DIET_TODAY` | Every food/exercise log | Meals (with timestamps), exercise (with timestamps), targets, weight, dayType/dayStyle |
| `diet-weight-history.js` | `DIET_WEIGHT_HISTORY` | Morning / weigh-in | Array of `{d, w}` for the weight trend chart |
| `diet-progress.js` | `DIET_PROGRESS` | Morning / weigh-in | Start weight, goals, numeric pace/scale/zone values, bar labels, trajectory |
| `diet-coach-notes.js` | `DIET_COACH` | Morning / weigh-in | Coach's notes items, what's-ahead items, quote |
| `proposed-diet-today.js` | `PROPOSED_DIET` | When a meal plan exists / after food log | **Optional.** Planned meals not yet eaten. Removed as meals are logged. File absent = section hidden. |

**Metrics cards** and **body composition** are computed at render time from `DIET_TODAY.weight` + `DIET_PROGRESS` constants -- no separate data file needed.

### What the HTML loads

The five data files load via a **per-load cache-busting loader**, NOT static `<script src>` tags:

```html
<script>
(function () {
  var v = Date.now();
  var files = ['diet-today.js','diet-weight-history.js','diet-progress.js','diet-coach-notes.js','proposed-diet-today.js'];
  for (var i = 0; i < files.length; i++) {
    document.write('<script src="' + files[i] + '?v=' + v + '"><\/script>');
  }
})();
</script>
```

All five scripts still load BEFORE any rendering JavaScript -- `document.write` of parser-inserted `<script>` tags executes them synchronously in order, exactly like static tags. The fifth (`proposed-diet-today.js`) is optional: if the file doesn't exist or fails to load, the Meal Ideas section simply doesn't render (the renderer checks for `window.PROPOSED_DIET` before doing anything).

### HARD RULE: Cache-Bust the Data Files

**Root cause of the staleness class of bugs:** when the data files load as plain `<script src="diet-today.js">`, the server has the freshly-regenerated file but the **browser caches it** and a normal reload serves the stale cached copy -- so a correctly-regenerated file still looks stale on screen. Phone browsers are the worst offenders. This is a client-side cache problem, NOT a data problem; regenerating the file does nothing to bust a browser cache.

**The fix is the `?v=Date.now()` loader above** -- every page load fetches the data files with a unique query string, so the browser can never serve a stale copy. **Any HTML rebuild MUST preserve this loader.** If staleness is still reported with the loader in place, the cause is upstream (the file genuinely wasn't regenerated) -- check the served file's contents, not the browser.

### Data Preservation

The JS data files are a **display cache**, not an archive. They get overwritten daily. The permanent historical record lives in:

| Permanent record | Contents |
|---|---|
| `diet-logs/food-log.csv` | Every meal ever |
| `diet-logs/weight-log.csv` | Every weigh-in ever |
| `diet-logs/exercise-log.csv` | Every exercise session ever |
| `Projects/Diet/YYYY-MM-DD.md` | Daily journals |
| `Knowledge/Health/Coach-Notes-Log.md` | Coach's notes history with dates |

Deleting or overwriting any `diet-*.js` file loses nothing that matters -- everything regenerates from the CSVs and the notes log.

---

## When to Update What

| Event | Action |
|---|---|
| **Mid-day food or exercise log** | Rewrite `diet-today.js`. If `proposed-diet-today.js` exists, remove the matching meal idea. HTML picks up changes on browser reload. |
| **Morning (start of day)** | **STEP 0 -- generate the previous day's journal FIRST (see HARD RULE below), before touching `diet-today.js`.** Then roll `diet-today.js` to the new day (set `date`/`dayStyle`/`dayType`/base `targets`, `weight: null`, empty `meals`/`exercise`). Then regenerate the three computed files. **Clear `proposed-diet-today.js`** if its `date` is not today (delete it or set `ideas` to an empty array); recreate it only if a meal plan exists for today. Do NOT rebuild the HTML. |
| **Weigh-in** | Regenerate the three computed data files (`diet-weight-history.js`, `diet-progress.js`, `diet-coach-notes.js`) and rewrite `diet-today.js` with the `weight` object. Do NOT rebuild the HTML. |
| **Explicit weight/progress query** | No action needed. Point to the existing HTML. |
| **Phase or goal changed in `Overview.md`** | Regenerate `diet-progress.js` (the constants live there, not in the HTML). |
| **Layout or rendering logic change** | Rebuild the HTML. This is rare -- only when adding features or fixing render bugs. |

**The HTML is stable.** The only trigger for an HTML rebuild is a layout/feature change, never a data update.

### HARD RULE: Journal Before Rolling to a New Day

Rolling the dashboard to a new day overwrites `diet-today.js`, which is the **source** for the previous day's journal (`Projects/Diet/YYYY-MM-DD.md`). The journal MUST be written (or verified complete) first. Ordering is non-negotiable:

1. **Read the current `diet-today.js`** (still holding the previous day's meals, exercise, weigh-in, targets).
2. **Write or verify `Projects/Diet/<previous-day>.md`** -- match the journal format (day type, weight line, activity section, per-meal tables with subtotals, running totals). Verify the totals arithmetically.
3. **Only then** overwrite `diet-today.js` for the new day and regenerate the other data files.

If `diet-today.js` was already overwritten before the journal was written (mistake), the previous day is NOT lost -- reconstruct the journal from `diet-logs/food-log.csv` (rows where `Date` equals that day), the matching `weight-log.csv` row, and `exercise-log.csv`. But the correct order avoids the reconstruction entirely. **Never roll to a new day without first confirming the prior day's journal exists.**

---

## Section Order (in the HTML)

1. **Metrics** -- card grid (weight, BF%, lean mass, from-start, goal countdown). Computed from `DIET_TODAY.weight` + `DIET_PROGRESS`.
2. **Macro Remaining bars** -- Cal/Protein/Carbs/Fat/Fiber block bars + Net calorie bar + remaining line. From `DIET_TODAY`.
3. **Exercise Journal** -- exercise cards with timestamps. From `DIET_TODAY.exercise`.
4. **Food Journal** -- meal table with timestamps, subtotals, grand total. From `DIET_TODAY.meals`.
4b. **Meal Ideas** *(optional)* -- planned meals not yet eaten. From `PROPOSED_DIET`. Hidden when file absent or empty.
5. **Coach's Notes** -- pattern-based observations. From `DIET_COACH.notes`.
6. **What's Ahead** -- upcoming events. From `DIET_COACH.ahead`.
7. **Weight Trend** -- line chart. From `DIET_WEIGHT_HISTORY` + goal weights from `DIET_PROGRESS.goals`.
8. **Progress & Pace** -- goal/pace/FatLb/LeanD bars. From `DIET_PROGRESS`.
9. **Body Composition** -- horizontal stacked bar. Computed from `DIET_TODAY.weight`.
10. **Quote** -- at the bottom. From `DIET_COACH.quote`.

Sections 1-4b change throughout the day (as food/exercise are logged and meal ideas are consumed). Sections 5-10 change once daily (morning/weigh-in data file regeneration).

---

## `diet-today.js` Contract

The canonical object shape and field-format rules live in [[Knowledge/Jesse-Guidelines/Diet-Logging-Flow]] "diet-today.js Format Spec" -- this file does not restate them (duplication causes drift). What this file owns is the HTML-rendering-specific field-NAME contract below and the chronological-ordering behavior; those are consumption details of the HTML, not the file format.

### HARD RULE: Field Name Contract

The HTML reads these exact property names from `window.DIET_TODAY`. Wrong names evaluate to `undefined` silently -- bars go blank or `NaN` with no error. **Do not change, abbreviate, or restructure them.** If you add a field, add it to this spec and the HTML renderer simultaneously. If you rename a field, update both in the same step.

| Property | Type | Notes |
|----------|------|-------|
| `date` | string | ISO date |
| `dayType` | string | Free-text human label (header) |
| `dayStyle` | string | Key into `STYLE_PROFILES`; defaults to `normal` if absent |
| `mode` | string\|null | Legacy banner; null on normal days |
| `targets.calories` | number | Adaptive ceiling, or window floor on window days |
| `targets.caloriesCap` | number\|null | Window upper bound; null = pure ceiling |
| `targets.protein` | number | Protein floor |
| `targets.fat` | number | Fat cap |
| `targets.fatFloor` | number\|null | Fat floor; null/absent = fat is a pure ceiling |
| `targets.carbs` | number | Carb floor |
| `targets.fiber` | number | Fiber floor; optional, renderer defaults to 38 when absent |
| `weight` | object\|null | `{ lbs, kg, bf, mm }` or null |
| `weight.lbs` / `weight.kg` | number | |
| `weight.bf` / `weight.mm` | number | Body-fat % / muscle mass; optional, absent when not measured |
| `meals` | array | `{ name, time, items: [{ item, amount, cal, p, f, c, fiber }] }` |
| `exercise` | array | `{ type, time, desc, distance, unit, duration, pace, avgHR, calories }` |

Totals (calories, protein, fat, carbs, fiber) are summed from `meals[].items` by the renderer -- they are **not** stored as fields. Exercise burn is summed from `exercise[].calories`. `item.fiber` should always be present (write `0` for items without a fiber value) so the Fiber bar's sum is well-defined.

**Never guess property names.** If the HTML reads `t.calories`, the JS must assign `targets: { calories: ... }`, not `caloriesTarget:` or `cal:`.

**A note on parameterization:** this contract deliberately carries the fat window (`fatFloor`/`fat`) and the calorie window (`calories`/`caloriesCap`) as data-file numbers rather than hardcoding them in the HTML. The shell stays free of user values; a phase change is a data-file edit, never an HTML edit. Keep it that way when syncing changes in from any other implementation.

### Timestamps and chronological ordering

Both meals and exercise include a `time` field (bare `HH:MM`, e.g. `"07:15"`). The HTML sorts both arrays by `time` before rendering, so entries appear in chronological order regardless of logging order. The `time` field is optional -- entries without it sort to the beginning -- but all new entries should include it. Never prefix `time` with `~` or text; it breaks the sort (approximate times belong in `proposed-diet-today.js`, which uses `~HH:MM` by convention).

---

## `diet-weight-history.js` Contract

```javascript
window.DIET_WEIGHT_HISTORY = [
    {d:'04-02', w:209.4},
    {d:'04-04', w:208.8},
    // ... every weigh-in, chronological
    {d:'06-20', w:196.5}
];
```

- `d` = MM-DD date label (chart x-axis label)
- `w` = weight in lbs
- Array order = chronological
- Source: `diet-logs/weight-log.csv` (all rows; `Date` becomes the MM-DD label, `Weight_lbs` becomes `w`)

---

## `diet-progress.js` Contract

All Progress & Pace values are **numeric fields plus pre-formatted labels**. The bar rendering algorithms (range-as-subset for Pace, single-block for FatLb/LeanD, fill-to-position for goals) are baked into the HTML's JavaScript -- the data file just provides the numbers. Personal numbers below are placeholders; every value comes from the user's `Overview.md` and `weight-log.csv`.

```javascript
window.DIET_PROGRESS = {
  // Constants (from Overview.md)
  startWeight: 210,
  goalDate: "2026-09-15",            // optional; drives the countdown metric card

  // One entry per user-defined goal weight, in order. Rendered as
  // fill-to-position progress bars AND as goal lines on the weight chart.
  goals: [
    { label: "Goal 1", weight: 185, filled: 11, info: "13.5 / 25 lbs to 185 (54%)" },
    { label: "Maint",  weight: 170, filled: 7,  info: "13.5 / 40 lbs to 170 (34%)" }
  ],
  barColor: "#639922",               // trough-pace zone color; ALL progress bars inherit it

  // Pace (14-day trailing window) -- numeric values, algorithms in the HTML
  troughPace: 1.0,                   // trough regression (smoothed, primary signal)
  rawPace: 1.2,                      // raw regression (noisy cross-check)
  paceScale: 2.5,                    // phase scale cap; the bar spans 0 to paceScale
  paceZones: { slowMin: 0.75, idealMin: 1.0, idealMax: 1.5 },  // phase zone boundaries
  paceInfo: "1.0 - 1.2 lbs/wk",      // label right of the bar
  paceSubs: [                        // sub-lines below the bar, rendered in order
    "Scale 0 to 2.5 · Plan 1.0-1.5 (IDEAL) · >1.5 (TOO FAST)",
    "Low 1.0 = trough regression (smoothed, primary signal)",
    "High 1.2 = raw regression (noisy - short-term water fluctuation)"
  ],

  // Composition (28-day window) -- OPTIONAL. Omit fatPace/leanPace entirely to
  // hide both bars (insufficient clean data); never carry stale values forward.
  fatPace: 0.9,
  fatColor: "#639922",
  fatInfo: "0.9 lb/wk fat loss · IDEAL",
  fatSub: "Scale 0 to 2.5 · Fat share of plan 0.75-1.13",
  leanPace: 0.2,
  leanScale: 1.0,                    // LeanD scale cap (phase-independent)
  leanColor: "#639922",
  leanInfo: "0.2 lb/wk lean loss · GOOD",
  leanSub: "Scale 0 to 1.0 loss · GOOD <0.5 · WATCH 0.5-1.0 · CONCERN >1.0",

  // Trajectory (optional, rendered as a text line below the bars)
  trajectory: "215.0 (Mar 1) -> 207.2 (Apr 15) -> 201.0 (May 20) -> 196.5 (today)"
};
```

Field notes:

- **`paceZones`** carries the current phase's zone boundaries so the shell stays free of user numbers: below `slowMin` = TOO SLOW (red), `slowMin` to `idealMin` = SLOW (yellow), `idealMin` to `idealMax` = IDEAL (green), above `idealMax` = TOO FAST (orange).
- **`filled`** on each goal is the pre-computed block count (0-20): `round((startWeight - current) / (startWeight - goal) * 20)`, clamped.
- **`barColor`** is the zone color of the **trough** pace (the conservative signal) -- progress bars follow trough, never raw, so raw noise never flashes a false warning.
- **Labels are pre-formatted** (`info`, `paceInfo`, `paceSubs`, `fatInfo`, ...) -- the generator does the wording (including "(off scale)" suffixes and converged single-value labels); the HTML renders them as-is.

---

## `diet-coach-notes.js` Contract

```javascript
window.DIET_COACH = {
    date: "June 20, 2026",
    title: "Weigh-In",        // or "Rest Day", "Training", etc.
    notes: [
        "<strong>Steady at 196.5 lbs.</strong> Pace range 1.0-1.2 lbs/wk, consistent for 10 days ...",
        // 2-4 items, HTML allowed
    ],
    ahead: [
        "<strong>Long session planned Saturday.</strong> ...",
        // 0-3 items, HTML allowed
    ],
    quote: {
        text: "You have power over your mind -- not outside events.",
        author: "Marcus Aurelius"
    }
};
```

HTML entities work in the strings since they're injected via `.innerHTML`.

---

## `proposed-diet-today.js` Contract (optional)

This file is **optional**. When absent or when `PROPOSED_DIET.ideas` is empty, the Meal Ideas section is hidden entirely. The HTML's renderer checks `window.PROPOSED_DIET` before attempting to render.

```javascript
window.PROPOSED_DIET = {
  date: "2026-06-20",
  source: "Dinner planning conversation",   // where this plan came from
  ideas: [
    { name: "Lunch", time: "~13:00", items: [
      { item: "Pasta (dry)", amount: "120g", cal: 430, p: 15, f: 2, c: 86 }
    ], notes: null },
    { name: "Dinner", time: "~19:30", items: [
      { item: "Grilled chicken", amount: "~180g", cal: 300, p: 56, f: 7, c: 0 }
    ], notes: "Keep sides light." }
  ],
  gapNote: "After these meals: ~250 cal and 20g protein short of target."  // optional, shown in orange
};
```

### Lifecycle rules

- **Created:** when a meal plan or eating strategy exists for the day -- during the morning routine, when the user discusses meal ideas, or when an event-prep plan is active. Only include meals NOT YET eaten.
- **Updated on food log:** when the user logs a meal, remove the corresponding idea. This applies whether they followed the plan or deviated -- the idea served its purpose. Recalculate planned totals after removal.
- **Cleared:** when all ideas are consumed/removed, delete the file or set `ideas` to an empty array. The section disappears on next reload.
- **Next morning:** start fresh. A stale file whose `date` is not today must be cleared before anything else renders -- stale meal ideas must never persist across days.
- **Multiple options:** a single meal slot can have several ideas (same `time`); all are removed when that slot is logged.
- **Approximate times:** use the `~HH:MM` prefix for planned times (vs exact logged times in `diet-today.js`).

Field names match `diet-today.js` food items: `item.item`, `item.amount`, `item.cal`, `item.p`, `item.f`, `item.c`. Meals use `meal.name`, `meal.time`, `meal.items`, `meal.notes`.

---

## HARD RULE: Error Isolation

Each major renderer runs in its own `try/catch` block, in separate scopes:

1. Metrics cards
2. Daily log (exercise + food + macro bars)
3. Meal Ideas
4. Coach's notes + What's Ahead + Quote
5. Chart weight trend
6. Progress & Pace bars

If the chart library fails to load from CDN (offline, blocked, slow), the food log and macro bars still render, and vice versa.

**All DOM access must be null-guarded:**

```javascript
// Correct
const el = document.getElementById('card-weight-value');
if (el) el.textContent = data.weightLbs + ' lbs';

// Wrong -- crashes silently if element is absent
document.getElementById('card-weight-value').textContent = data.weightLbs + ' lbs';
```

**Never use `textContent` on a container that has child elements** -- it destroys them. Target the specific leaf node.

```javascript
// Wrong -- destroys child elements inside the card
document.getElementById('card-weight').textContent = '196.5 lbs';

// Correct -- targets the value span inside the card
document.getElementById('card-weight-value').textContent = '196.5 lbs';
```

---

## Section Rendering Specs

### 1. Metric cards

Computed from `DIET_TODAY.weight` + `DIET_PROGRESS` constants: Weight (lbs + kg), Body Fat % (+ fat mass lbs), Lean Mass, From Start (delta from `startWeight`), Goal Day (countdown from `goalDate`, if set).

**No-weigh-in fallback (HARD RULE).** On a non-weigh-in day (`DIET_TODAY.weight` is null -- common on travel days), the Weight card falls back to the **last entry in `DIET_WEIGHT_HISTORY`** so it shows the current weight (kg derived from lbs) with a `· last weigh-in` sub-label instead of rendering zeros or hiding. Body Fat and Lean Mass are **NOT** carried forward -- they render blank with a `no weigh-in today` sub-label (blank is the honest signal; never carry BF%/MM forward, matching the weigh-in rule in [[Knowledge/Jesse-Guidelines/Diet-Logging-Flow]]). The Body Composition section self-hides when `weight.bf` is absent.

### 2. Macro bars

Rendered from `DIET_TODAY.targets` plus the resolved day style. One bar per metric, 20 blocks each (14x16 px). Each bar carries a **goal chip**, and a single compact **legend** sits at the bottom of the panel.

**Day-Style Resolution: `STYLE_PROFILES`.** The HTML holds a `STYLE_PROFILES` map: `dayStyle` to `{ calType, fatType, carbInRemaining }`. It encodes **bar types only** -- the target *numbers* come from `DIET_TODAY.targets`. This map must stay in sync with the canonical Day-Style Registry in [[Knowledge/Jesse-Guidelines/Diet-Logging-Flow]] (the HTML cannot read that markdown table). When a style is added to the registry, add the matching row here.

```javascript
var STYLE_PROFILES = {
  'normal':              { calType: 'ceiling', fatType: 'window',           carbInRemaining: true  },
  'long-run':            { calType: 'ceiling', fatType: 'window',           carbInRemaining: true  },
  'endurance':           { calType: 'ceiling', fatType: 'window',           carbInRemaining: true  },
  'refeed':              { calType: 'ceiling', fatType: 'window',           carbInRemaining: true  },
  'sick':                { calType: 'ceiling', fatType: 'window',           carbInRemaining: true  },
  'carb-load-training':  { calType: 'window',  fatType: 'minimize-ceiling', carbInRemaining: false },
  'carb-load-race':      { calType: 'window',  fatType: 'minimize-ceiling', carbInRemaining: false },
  'fasting':             { calType: 'ceiling', fatType: 'floor',            carbInRemaining: true  }
};
```

Resolution: explicit `dayStyle` wins, else detect a `CARB-LOAD` marker in `dayType` (back-compat), else `normal`.

Bars, in order:

- **Calories** -- `calType` (`ceiling` normally; `window` on carb-load, using `targets.calories` floor and `targets.caloriesCap`)
- **Net calories** -- two-tone blue bar, shown only when total exercise burn > 0: solid blue for net calories retained (intake minus burn), striped blue for the exercise-burned portion. Label: `X net (Y%) · Z cal burned`.
- **Protein** -- floor
- **Carbs** -- floor
- **Fat** -- `fatType` (`window` with `targets.fatFloor`/`targets.fat`; `minimize-ceiling` on carb-load; `floor` on fasting)
- **Fiber** -- floor, measured against `targets.fiber` (default 38), summed from `item.fiber` across all meals. **Suspended on carb-load days** (window `calType`): the row shows the fiber total as a plain muted number with no bar and no color judgment, because low-residue eating before a long effort is deliberate.

**Goal chips** -- first token on each bar's info line: green `>= floor`, blue `<= cap`, amber up-down arrow for windows.

**Color zones** (bar colors are **never** time-gated):

- *Floor* (percent of floor): 0-49% red · 50-79% amber · 80%+ green.
- *Ceiling / minimize-ceiling* (percent of cap): 0-79% green · 80-100% amber · >100% red.
- *Window* (vs floor and cap): below floor **red** · floor to 80% of cap green · 80-100% of cap amber · over cap red. A window colors red when too **low**, unlike a pure ceiling.

**Legend** -- one compact line (~10px) at the bottom of the macro panel, each item fully colored (symbol *and* text the same color): `>= floor` (green) · `<= cap` (blue) · window (amber). Keep it one line.

**Time-Gated Low Flags (HARD RULE).** Under-floor textual flags (protein low, fat under floor, carbs low, fiber low) render **only after `LOW_FLAG_HOUR`** (default 16) -- an unfilled floor mid-day is expected, not a problem. Over-cap flags (fat over cap, calories over) are **never** gated. **Bar colors are never gated** -- only the textual flags. `LOW_FLAG_HOUR` is a single configurable constant in the script. Full flag table in [[Knowledge/Jesse-Guidelines/Diet-Dashboard-Guidelines]].

| Flag | Trigger | Gated |
|------|---------|-------|
| `← low protein` | protein < ~90% of floor | after `LOW_FLAG_HOUR` |
| `← fat under floor` | fat < `fatFloor` | after `LOW_FLAG_HOUR` |
| `← low carbs` | carbs < floor | after `LOW_FLAG_HOUR` |
| `← low fiber` | fiber < ~50% of floor | after `LOW_FLAG_HOUR`; never on carb-load days |
| `← fat over cap` | fat > `fat` cap | never gated |
| `← over calorie target` | calories over ceiling / window cap | never gated |

**Remaining line** below the bars: `Remaining: ~X cal, Xg protein` (floored at 0), plus carbs when the style's `carbInRemaining` is true.

### 3. Exercise Journal

Exercise cards sorted chronologically by `time`. Each card: timestamp, type, description, detail line (duration, distance, pace, HR, calories joined by ` · `).

### 4. Food Journal

Meals sorted chronologically by `time`. All meals render inside ONE table: one header row (`Item / Amount / Cal / P / F / C / Fib`), then per meal a spanning header row (timestamp + meal name), item rows, a subtotal row; grand total as the final row of the same table.

### 4b. Meal Ideas (optional)

From `PROPOSED_DIET`. Same table format as the food journal (meal headers with approximate times, item rows, subtotals, planned grand total). Source line and gap note below. Hidden when the file is absent or `ideas` is empty. Own `try/catch` scope.

### 5-6. Coach's Notes / What's Ahead

From `DIET_COACH`. Date + title as header, items as a bulleted list in the notes panel. `ahead` renders in its own section; empty array hides it.

### 7. Weight Trend Chart

From `DIET_WEIGHT_HISTORY` + `DIET_PROGRESS.goals`. Line chart with four series:

1. **Daily weight** -- points, primary color
2. **7-day moving average** -- dashed line, no points (computed by the renderer from the history array)
3. **Goal weight line(s)** -- one horizontal dashed line per entry in `DIET_PROGRESS.goals`, at `goal.weight`
4. **Trajectory line** *(optional)* -- a thin dotted reference line through the start weight and goal weights, distinguishing intent from data

Y-axis bounds are computed from the data and goal weights with padding -- never hardcoded. Light/dark mode for grid and tick colors.

### 8. Progress & Pace

From `DIET_PROGRESS`. See Bar Rendering Algorithms below. Order: one fill-to-position bar per `goals` entry, then Pace (with its sub-lines), then FatLb and LeanD (skipped entirely when `fatPace`/`leanPace` are absent -- insufficient composition data), then the trajectory line.

### 9. Body Composition

Computed from `DIET_TODAY.weight`. Horizontal stacked bar: lean (green) vs fat (red), with lbs and percentages. Self-hides when `weight` or `weight.bf` is absent.

### 10. Quote

From `DIET_COACH.quote`. Centered, italic, bottom of dashboard.

---

## HARD RULE: Bar Rendering Algorithms (regression prevention)

**These algorithms are baked into the HTML's JavaScript. They read numeric values from `diet-progress.js`, but the rendering logic does NOT change between days.** That is the point of the static shell -- the algorithms are stable, only the data changes. Zone definitions and the design history live in [[Knowledge/Jesse-Guidelines/Weight-Tracker-Spec]].

### Pace bar: range-as-subset

```
block_size = paceScale / 20
low  = min(troughPace, rawPace)
high = max(troughPace, rawPace)

if (high - low) < 0.1:                  # converged-range collapse
    single-block highlight at the block closest to the value,
    colored by its zone; label shows one value, not a range
else:
    for i in 0..19:
        v_center = (i + 0.5) * block_size
        if low <= v_center <= high:
            block[i] = zone_color(v_center)   # from paceZones
        else:
            block[i] = EMPTY
```

**Most blocks are EMPTY.** Only the 1-4 blocks whose center falls inside [low, high] get colored. `zone_color(v)`: below `paceZones.slowMin` red, to `idealMin` yellow, to `idealMax` green, above orange (TOO FAST). Negative values (gaining) are blue.

**Off-scale rendering:** if the range extends past `paceScale`, blocks are colored up to the cap (the label, pre-formatted in the data file, carries the actual value with an `(off scale)` suffix, e.g. `2.6 - 3.1+ lbs/wk (off scale)`). If even `low` is past the cap, color the rightmost block in `low`'s zone color so the bar never renders fully empty on a real signal.

### FatLb and LeanD bars: single-block highlight

```
FatLb:  block_size = paceScale / 20     # same scale as Pace
LeanD:  block_size = leanScale / 20

best = argmin over i of |((i + 0.5) * block_size) - pace|
block[i] = (i == best) ? zoneColorFromDataFile : EMPTY
```

**Exactly ONE block is colored** (color from `fatColor` / `leanColor`). Off-scale: rightmost block. Do not apply range-as-subset to composition bars.

### Progress bars (goals): fill-to-position

These are the ONLY bars that use fill-to-position. `goal.filled` blocks on the left, rest empty. Color = `barColor` (the trough zone color from the data file) for every goal bar.

---

## Verification Step

After updating the HTML or the progress data file, count colored blocks:

| Bar | Expected colored blocks |
|-----|------------------------|
| Pace | 1-4 (if 0: values outside the scale and the off-scale guard failed; if 20: `paceScale` too low) |
| FatLb | Exactly 1 (when composition data present) |
| LeanD | Exactly 1 (when composition data present) |
| Each goal bar | 0-20, proportional to percent complete |

If the pace bar has 0 blocks, check that `troughPace` and `rawPace` were computed from the correct 14-day window. If it has all 20, raise the phase's scale cap in `Overview.md`.

---

## Coach's Notes Spec

Coach's notes live in `diet-coach-notes.js`, regenerated each morning / weigh-in.

### Content Rules

- 2-4 short items; plain prose; data-cited, not generic advice
- Tone: neutral, observational -- describe patterns without motivation-speak
- Reference actual values where meaningful ("pace range 0.9-1.1 lbs/wk, consistent for 10 days")
- Do not reference user-specific names, event dates, or values not derivable from the data
- **Surface a floor-miss trend** when one is active -- a floor missed on `[FLOOR_MISS_COUNT]`+ of the trailing `[FLOOR_MISS_WINDOW]` logged days (defaults 3 of 7). State it as a pattern ("protein under floor 4 of the last 7 days"), not a single-day nag. Definition in [[Knowledge/Jesse-Guidelines/Diet-Logging-Flow]].

### Continuity Rules (HARD RULE)

Before writing new notes:

1. Read the last 7 entries from `Knowledge/Health/Coach-Notes-Log.md`
2. Identify running themes; carry forward active ones, retire themes the data no longer supports
3. Introduce new themes only when the data clearly warrants it -- not one-day noise

After writing the data file, append to `Knowledge/Health/Coach-Notes-Log.md`:

```markdown
## YYYY-MM-DD

[exact notes rendered on the dashboard]

Context: [brief data summary -- pace range, current weight, composition trend if available]
```

### Quote Selection

Draw quotes from the user's actual interests (books they've read, fields they care about), thematically resonant with the moment rather than literally about the day's activity. No single-hobby ruts -- if the user is a runner, running quotes at most once in any 7-day window. Never repeat a quote within 14 days (check the Coach-Notes-Log). Avoid motivational-poster fare.

---

## Design Rules

1. **Self-contained HTML** -- inline CSS, inline JS; CDN chart library only (pin the major version)
2. **File location** -- `Dashboard-Fancy.html` at vault root, not inside any subdirectory
3. **Data loading** -- the `?v=Date.now()` cache-busting `document.write` loader (see HARD RULE), which runs BEFORE any rendering scripts. Never plain static `<script src>` tags.
4. **Light/dark mode** -- reads `prefers-color-scheme`; chart canvas cannot resolve CSS variables, use hardcoded hex per mode
5. **Block sizes** -- 14x16 px per block, 2px gap, for all bar visualizations
6. **Zone colors:**
   - TOO SLOW: `#E24B4A` (red)
   - SLOW: `#EF9F27` (yellow -- **slow-side only, never used for fast**)
   - IDEAL: `#639922` (green)
   - TOO FAST: `#F97316` (orange -- reads as a warning; never yellow, never blue)
   - GAINING: `#378ADD` (blue -- **reserved for gaining only**; anomalous, different category)
   - Empty block: `#ddd` (light) / `#404040` (dark)
   - Net calories bar: solid blue for retained, striped blue for the burned portion
7. **No emoji** in the HTML dashboard -- colored blocks only (emoji belong in the ASCII chat dashboard)
8. **No hardcoded user values** -- all targets, phases, goals, scales, and zone boundaries come from the external data files at load time; none are hardcoded in the HTML shell. `STYLE_PROFILES` is *structural* (bar types only), not user data. Keep each renderer in its own `try/catch` and null-guard every `getElementById`.
