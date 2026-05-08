# Fancy Dashboard Build Rules

Single source of truth for generating `Dashboard-Fancy.html` at the vault root. Read this file in full before building or modifying the dashboard. Do not guess at field names, rendering rules, or architecture — the contracts below are exact.

---

## Architecture Overview

The fancy dashboard uses a **two-layer architecture**:

**Layer 1 — Static HTML** (`Dashboard-Fancy.html`, vault root)
- Contains the weight chart, metric cards, pace/composition bars, progress bars, and coach's notes
- Rebuilt on: morning weigh-in, any change to weight history, explicit user request, date change (new day)
- Do NOT rebuild on every food or exercise log — that's what Layer 2 handles

**Layer 2 — Dynamic JS** (`diet-today.js`, vault root)
- Contains today's macro bars, food log, exercise cards, net calorie line, remaining line, and flags
- Rewritten on: every food log, every exercise log, every correction
- The static HTML `<script>` tag loads this file; updating it auto-refreshes live sections on browser reload

**Why two layers?**

Weight trend data changes once daily. Rebuilding Chart.js and all pace bars on every food log is wasteful and slow. Splitting the work means most logs only rewrite a small JS file while the expensive chart and bars stay cached.

---

## `diet-today.js` Contract

`diet-today.js` lives at the vault root. It assigns exactly one global:

```javascript
window.DIET_TODAY = {
  // Date context
  date: "YYYY-MM-DD",           // e.g. "2026-05-02"
  dayLabel: "Fri May 2",        // display label, no year
  mode: null,                   // null, or string e.g. "CARB-LOAD DAY 1/2"

  // Calorie data
  caloriesActual: 0,            // total calories logged today
  caloriesTarget: 1900,         // adaptive target (baseline + exercise adjustment)
  caloriesPct: 0,               // integer percent: (caloriesActual / caloriesTarget) * 100
  baselineTarget: 1900,         // base target from Overview.md before exercise adjustment
  exerciseCalories: 0,          // total exercise calories burned today
  netCalories: 0,               // caloriesActual - exerciseCalories

  // Macro data
  proteinActual: 0,
  proteinTarget: 150,
  carbsActual: 0,
  carbsTarget: 180,
  fatActual: 0,
  fatTarget: 70,

  // Remaining
  caloriesRemaining: 1900,      // caloriesTarget - caloriesActual
  proteinRemaining: 150,        // proteinTarget - proteinActual

  // Meals (one entry per meal logged today)
  meals: [
    // {
    //   name: "Breakfast",
    //   items: [{ item, amount, grams, calories, protein, fat, carbs, notes }],
    //   totals: { calories, protein, fat, carbs }
    // }
  ],

  // Exercises (one entry per activity logged today)
  exercises: [
    // { type: "Run", description: "8.5km, 45 min", calories: 600 }
  ],

  // Flags (array of flag code strings; see Dynamic Sections for display mapping)
  flags: [],

  // Weight (if logged today; null if no weigh-in)
  weightLbs: null,
  weightKg: null,
};
```

### Meal item shape

```javascript
{
  item: "Oatmeal",
  amount: "1 cup",
  grams: 234,
  calories: 166,
  protein: 6,
  fat: 4,
  carbs: 28,
  notes: ""
}
```

---

## HARD RULE: Field Name Contract

The static HTML reads these exact property names from `window.DIET_TODAY`. **Do not change, abbreviate, or restructure them.** If you add a field, add it to this spec and the HTML renderer simultaneously. If you rename a field, update both in the same step.

| Property | Type | Notes |
|----------|------|-------|
| `date` | string | ISO date |
| `dayLabel` | string | Display label |
| `mode` | string\|null | Active mode label or null |
| `caloriesActual` | number | |
| `caloriesTarget` | number | Adaptive target |
| `caloriesPct` | number | Integer percent |
| `baselineTarget` | number | Pre-exercise base |
| `exerciseCalories` | number | 0 if no exercise |
| `netCalories` | number | |
| `proteinActual` | number | |
| `proteinTarget` | number | |
| `carbsActual` | number | |
| `carbsTarget` | number | |
| `fatActual` | number | |
| `fatTarget` | number | |
| `caloriesRemaining` | number | |
| `proteinRemaining` | number | |
| `meals` | array | |
| `exercises` | array | |
| `flags` | array | |
| `weightLbs` | number\|null | |
| `weightKg` | number\|null | |

**Never guess property names.** If the HTML reads `d.caloriesActual`, the JS must assign `caloriesActual:`, not `calories_actual:` or `cal:` or `actualCalories:`.

---

## When to Rebuild HTML vs. Rewrite `diet-today.js`

| Event | Action |
|-------|--------|
| Morning weigh-in logged | Rebuild full HTML + rewrite `diet-today.js` |
| Food log added or corrected | Rewrite `diet-today.js` only |
| Exercise log added or corrected | Rewrite `diet-today.js` only |
| Day date changes (new day) | Rebuild full HTML + reset `diet-today.js` |
| User requests dashboard refresh | Rebuild full HTML + rewrite `diet-today.js` |
| Phase or goal changed in `Overview.md` | Rebuild full HTML |
| Weight history edited | Rebuild full HTML |

Rule of thumb: if only today's food/exercise data changed, rewrite `diet-today.js`. If weight trend data changed, rebuild the full HTML.

---

## Static HTML Sections Spec

Static sections are rendered from weight history and phase config embedded at build time.

### Metric Cards (top row)

Four cards in a responsive row:

1. **Current Weight** — latest weight entry, shown as `X.X lbs / X.X kg`
2. **Lost Since Start** — start weight minus current weight, shown as `−X.X lbs`
3. **Phase** — current phase label from user's `Overview.md`
4. **Pace Range** — trough/raw range shown as `X.X – X.X lbs/wk` (from the 14-day trailing window)

### Weight Chart

Chart.js line chart with four series:

1. **Daily weight** — scatter dots, semi-transparent
2. **7-day moving average** — solid line
3. **Goal weight line(s)** — horizontal dashed line(s) for each goal in `Overview.md`
4. **Trajectory line** — a straight reference line through four reference weights: start weight, first goal weight, second goal weight (if defined), and the projected weight at the current pace on the goal-target date (if a target date is set). Shown as a thin dotted line to distinguish from actual data.

Y-axis in lbs; X-axis shows dates (last 90 days, or full history if shorter). Minimal gridlines.

### Composition Bar (optional)

Rendered only when ≥10 clean body composition entries exist in the 28-day window. Shows FatLb and LeanΔ bars using the single-block highlight algorithm. See Body Composition Bars in [[Knowledge/Jesse-Guidelines/Weight-Tracker-Guidelines]].

### Progress Bars

One bar per user-defined goal weight (from `Overview.md`). Uses fill-to-position algorithm. See Bar Rendering section below.

### Pace/FatLb/LeanΔ Bars

Three bars using the range-as-subset (pace) and single-block highlight (composition) algorithms. Full spec in [[Knowledge/Jesse-Guidelines/Weight-Tracker-Guidelines]].

### Coach's Notes

A text block below the bars. Content is written by the assistant at HTML rebuild time. See Coach's Notes Spec below.

---

## Dynamic Sections Spec

These sections are re-rendered by JavaScript each time the browser loads `diet-today.js`. They operate on `window.DIET_TODAY`.

### Macro Bars

Rendered from the macro fields. One bar per metric, 20 blocks each (12×16 px):

- **Calories** — ceiling metric on normal days; floor metric on carb-load days (check `mode`)
- **Net calories** — gray bar (`░` blocks, 12×16 px), shown only when `exerciseCalories > 0`
- **Protein** — floor metric
- **Carbs** — floor metric on normal days; floor metric with raised target on carb-load days
- **Fat** — ceiling metric

Color zones for floor metrics (Protein, Carbs, normal-day Calories in carb-load mode):
- 0–49%: red
- 50–79%: amber
- 80–100%+: green

Color zones for ceiling metrics (Fat, Calories on normal days):
- 0–79%: green
- 80–99%: amber
- ≥100%: red + overage badge

### Food Log

Rendered from `DIET_TODAY.meals`. One table per meal: header row, item rows (Item, Amount, Cal, P, F, C), totals row.

### Exercise Cards

Rendered from `DIET_TODAY.exercises`. One card per exercise: type, description, calories.

### Net Calorie Line

Shown only when `exerciseCalories > 0`. Displays `netCalories / baselineTarget` in gray.

### Remaining Line

Text: `Remaining (baseline): ~X cal, Xg protein`. From `caloriesRemaining` and `proteinRemaining`.

### Flags Line

One line per entry in `DIET_TODAY.flags`. Code-to-display mapping:

| Flag code | Display text |
|-----------|-------------|
| `carbs_low` | `← low carbs` |
| `protein_low_breakfast` | `← low protein at breakfast` |
| `fat_approaching` | `← fat approaching limit` |
| `calories_high` | `← approaching calorie limit` |
| `calories_over` | `← over calorie target` |

---

## HARD RULE: Error Isolation

Chart.js and the food renderer **must** run in separate `try/catch` blocks. If one fails, the other must still render.

```javascript
// Chart block — isolated
try {
  const ctx = document.getElementById('weightChart');
  if (ctx) {
    new Chart(ctx, { /* ... */ });
  }
} catch (e) {
  console.error('Chart render failed:', e);
  const container = document.getElementById('chart-container');
  if (container) container.innerHTML = '<p class="error">Chart unavailable</p>';
}

// Food renderer block — isolated, always runs regardless of chart outcome
try {
  renderFoodLog(window.DIET_TODAY);
  renderMacroBars(window.DIET_TODAY);
  renderExerciseCards(window.DIET_TODAY);
} catch (e) {
  console.error('Food renderer failed:', e);
  const section = document.getElementById('food-section');
  if (section) section.innerHTML = '<p class="error">Food data unavailable</p>';
}
```

**All DOM access must be null-guarded:**

```javascript
// Correct
const el = document.getElementById('card-weight-value');
if (el) el.textContent = data.weightLbs + ' lbs';

// Wrong — crashes silently if element is absent
document.getElementById('card-weight-value').textContent = data.weightLbs + ' lbs';
```

**Never use `textContent` on a container that has child elements** — it destroys them. Target the specific leaf node.

```javascript
// Wrong — destroys child elements inside the card
document.getElementById('card-weight').textContent = '185.2 lbs';

// Correct — targets the value span inside the card
document.getElementById('card-weight-value').textContent = '185.2 lbs';
```

---

## Bar Rendering Algorithms

### Pace Bar: Range-as-Subset

Used for: weight pace (trough vs. raw).

```
scale_cap  = phase.scaleCap        // from Overview.md phase config
block_size = scale_cap / 20

low  = min(troughPace, rawPace)
high = max(troughPace, rawPace)

for i in 0..19:
    v_center = (i + 0.5) * block_size
    if low <= v_center <= high:
        block[i] = zone_color(v_center)   // TOO_SLOW / SLOW / IDEAL / TOO_FAST
    else:
        block[i] = EMPTY
```

See [[Knowledge/Jesse-Guidelines/Weight-Tracker-Guidelines]] for zone color definitions and the failed-approaches history explaining why this design was chosen.

### FatLb and LeanΔ Bars: Single-Block Highlight

Used for: fat loss pace and lean mass change rate.

```
scale_cap  = 2.0          // lbs/week (fat); 1.0 for lean mass change
block_size = scale_cap / 20

target_i = argmin over i of |((i + 0.5) * block_size) - pace|

for i in 0..19:
    block[i] = (i == target_i) ? zone_color(pace) : EMPTY
```

One colored block; all others empty. Do not apply range-as-subset to composition bars.

### Progress Bars: Fill-to-Position

Used for: progress toward each user-defined goal weight.

```
fill_pct    = (start_weight - current_weight) / (start_weight - goal_weight)
fill_pct    = clamp(fill_pct, 0.0, 1.0)
filled      = round(fill_pct * 20)

for i in 0..19:
    block[i] = (i < filled) ? zone_color(troughPace) : EMPTY
```

Color is from trough pace zone (conservative signal). Label: `X.X / Y lbs to Z (P%)`.

---

## Verification Step

After generating or updating the HTML dashboard, count colored blocks:

| Bar | Expected colored blocks |
|-----|------------------------|
| Pace | 1–4 (if 0: pace outside scale range; if 20: scale_cap too low) |
| FatLb | Exactly 1 (if composition data available) |
| LeanΔ | Exactly 1 (if composition data available) |
| Each progress bar | 0–20, proportional to percent complete |

If the pace bar has 0 blocks, check whether `troughPace` and `rawPace` were computed from the correct 14-day window. If it has all 20 blocks, the phase's `scaleCap` needs to be raised in `Overview.md`.

---

## Coach's Notes Spec

Coach's notes appear in the static HTML below the bars. They are regenerated at every HTML rebuild.

### Content Rules

- 2–4 sentences; plain prose; no headers or bullets
- Tone: neutral, observational — describe patterns without advice or motivation
- Reference actual values where meaningful ("pace range 0.9–1.1 lbs/wk, consistent for 10 days")
- Do not recommend actions; let the data speak
- Do not reference user-specific names, race dates, or values not derivable from the data object

### Continuity Rules

Before writing new notes:

1. Read the last 7 entries from `Knowledge/Health/Coach-Notes-Log.md`
2. Identify running themes (e.g., "pace has been slow for 3 weeks", "composition stable")
3. Carry forward active themes; retire themes whose underlying data no longer supports them
4. Introduce new themes only when data clearly warrants it — not one-day noise

This prevents notes from contradicting prior advice or oscillating on day-to-day variance.

### After Writing

After rendering the dashboard, append to `Knowledge/Health/Coach-Notes-Log.md`:

```markdown
## YYYY-MM-DD

[exact notes rendered on the dashboard]

Context: [brief data summary — pace range, current weight, composition trend if available]
```

---

## Design Rules

1. **Self-contained HTML** — no external CSS, no external fonts; CDN Chart.js only (pin the major version)
2. **File location** — `Dashboard-Fancy.html` at vault root, not inside any subdirectory
3. **Light/dark mode** — reads `prefers-color-scheme`; toggle button available
4. **Block sizes** — 12×16 px per block for all bar visualizations
5. **Zone colors (light mode):**
   - TOO SLOW: `#ef4444` (red)
   - SLOW: `#f59e0b` (amber)
   - IDEAL: `#22c55e` (green)
   - TOO FAST: `#3b82f6` (blue)
   - Empty block: `#e5e7eb` (light gray)
   - Net calories bar: `#9ca3af` (medium gray, `░`-style)
6. **Dark mode** — use slightly desaturated variants; maintain contrast ratios
7. **No emoji** in the HTML dashboard — use colored blocks only
8. **No hardcoded user values** — all targets, phases, and goals are embedded from `Overview.md` at build time; none are hardcoded in the HTML template
