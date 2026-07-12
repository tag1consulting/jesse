# Diet Logging Flow

Exact sequence for every meal, exercise, and weigh-in log. Execute in order.

---

## Data Architecture: CSV-First

**The `diet-logs/*.csv` files are the single source of truth.** Three append-only RFC-4180 CSV files at `diet-logs/` off the workspace root, one row per item, all time, the permanent record:

| File | Columns |
|---|---|
| `diet-logs/food-log.csv` | `Date,Meal,Item,Amount,Unit,Cal_per_100g,Grams,Calories,Protein_g,Fat_g,Carbs_g,Notes,Time,Meal_Type,Fiber_g` |
| `diet-logs/exercise-log.csv` | `Date,Type,Description,Distance_km,Duration,Pace_min_per_km,Elevation_m,Avg_HR,Cadence,Calories,Plan_Source,Notes,Start_Time` |
| `diet-logs/weight-log.csv` | `Date,Weight_lbs,Weight_kg,Phase,BodyFat_pct,MuscleMass_lbs,Notes` |

Optional fourth file: `diet-logs/daily-targets.csv` (`Date,Mode,Cal_Target,Carb_Target_g,Protein_Target_g,Fat_Target_g,Exercise_Cal,Notes,Fiber_Target_g`) if a per-day record of the targets themselves is wanted.

Rules that keep the store trustworthy:

- **Append the CSV row FIRST on any log event** -- before the journal, before `diet-today.js`, before the dashboard. If anything later fails, the permanent record is already correct.
- **RFC-4180 quoting (HARD).** Any field containing a comma, double-quote, or newline MUST be wrapped in double-quotes with embedded quotes doubled (`""`). Food items and Notes routinely contain commas.
- **Readers MUST use a real CSV parser** (Node `csv-parse`, Python's `csv`, or equivalent) -- never `line.split(',')`.
- **Blank cells stay blank.** An empty BodyFat_pct, MuscleMass_lbs, or Fiber_g field is the honest "not measured" signal -- never `0` or `None`.
- **Every appended row is complete** -- its own `Date`, `Meal`/`Type`, and `Time`. Never rely on a previous row's context.
- **`diet-today.js` is a DERIVED cache**, regenerated from the CSVs. Never hand-edit its meal, exercise, or weight item data; the only hand-edited fields are the scalars (`dayType`, `dayStyle`, `targets`). If the cache and the CSVs ever disagree, **the CSVs win** -- and if the cache leads (an entry with no CSV row), back-fill the CSV first, then regenerate.
- **Morning reconcile.** Before rolling `diet-today.js` to a new day, confirm every entry in yesterday's cache has a matching CSV row and that the previous day's journal is complete (see the journal-before-roll rule in [[Knowledge/Jesse-Guidelines/Fancy-Dashboard-Build]]).

Why the store is CSV rather than a spreadsheet, and an optional DuckDB pattern for ad-hoc queries over these files, are covered in the diet-tracking recipe documentation (sections "Why CSV Files" and "Optional: DuckDB for Dynamic Queries").

---

## Per-Log-Event Flow

### On every meal or exercise log

1. **Parse input** — extract food items, quantities, or exercise type/duration/calories
2. **Estimate nutrition** — use knowledge base; check `food-log.csv` for recent entries of that food (last 30 days, real CSV parser) and reuse those values to prevent drift
3. **Append CSV first** — add the complete row(s) to `diet-logs/food-log.csv` (meals) or `diet-logs/exercise-log.csv` (exercise), RFC-4180 quoted
4. **Update daily journal** — add row(s) to the meal or activity table in `Projects/Diet/YYYY-MM-DD.md`; recalculate the Running Totals table
5. **Rewrite `diet-today.js`** — write the complete updated `window.DIET_TODAY` object (see spec below); item data must match the CSV rows exactly
6. **Show ASCII dashboard in chat** — per [[Knowledge/Jesse-Guidelines/Diet-Dashboard-Guidelines]] format
7. **Fancy dashboard** — auto-updates on next browser reload via `diet-today.js`; **do not rebuild** `Dashboard-Fancy.html` on food/exercise logs

### On weigh-in

Same steps 1–6 (step 3 appends to `diet-logs/weight-log.csv`), plus:

8. **Regenerate the dashboard data files** — `diet-weight-history.js`, `diet-progress.js`, and `diet-coach-notes.js`, per [[Knowledge/Jesse-Guidelines/Fancy-Dashboard-Build]]. This refreshes the weight chart, pace bars, progress bars, and coach's notes on the next browser reload. Do **not** rebuild `Dashboard-Fancy.html` -- the HTML is a static shell; only data files change.

**Weigh-in judgment:**

- **Weigh-in triple.** If the user's scale reports body-fat % and muscle/lean mass, prompt once when they're missing from a weigh-in; if unavailable, log the weight alone and leave the fields blank. Never infer or carry BF%/MM forward -- blank is the honest signal in the trend.
- **Hydration artifacts.** Bioimpedance BF% readings taken near intense exercise or high hydration variance are unreliable. Log the value for completeness but flag it in the Notes field with the exact marker string `⚠️ hydration artifact` -- the weight tracker filters on that string (see [[Knowledge/Jesse-Guidelines/Weight-Tracker-Guidelines]]), so exclusion is greppable rather than a judgment call per read.

### Correction workflow

If the user corrects a value ("that was 350 not 300"), update:
- The corresponding row in `food-log.csv` (first)
- The daily journal table and Running Totals
- Rewrite `diet-today.js` with corrected values

---

## Macro Model: Floors, Ceilings, and Windows

Every macro target is one of three types. The type — not the number — decides what "good" looks like, which colors the bar takes, and which flags can fire. This is the conceptual core; the dashboard color logic and flags all derive from it.

| Type | Symbol | "Good" means | Examples |
|------|--------|--------------|----------|
| **Floor** | `≥` | at or above the target | protein, carbs, fiber |
| **Ceiling** | `≤` | at or under the target | calories on normal days |
| **Window** | `↕` | between a floor and a cap | fat (floor + cap); calories on high-fuel days |

### Why each macro is the type it is

- **Protein is a floor.** On a calorie deficit, adequate protein is what makes the lost weight come off fat instead of muscle. Under the floor, the deficit eats lean mass. General guidance: 1.6–2.2 g/kg bodyweight, toward the high end while cutting.
- **Fat is a window, not just a cap.** Dietary fat is the substrate for steroid-hormone production and is required to absorb the fat-soluble vitamins (A, D, E, K); chronically too-low fat suppresses hormones and blunts training adaptation — risks that rise with age and at low body-fat. So there is a real *floor* (general guidance ~0.5–0.6 g/kg, with a practical minimum), not only the calorie-budget *cap*. The cap exists because fat is energy-dense (9 kcal/g) and crowds the calorie budget on a deficit — a budgeting limit, not toxicity.
- **Carbs are a floor with no independent ceiling.** The carb target is a remainder — the calories left after protein and fat. Going over it is not bad in itself; it only matters through the calorie ceiling. More carbs on a hard training day is good.
- **"Under calories" isn't "anything goes."** Calories is the master dial for weight, but the floors govern *what* you lose and your health independent of calories: protein decides fat-vs-muscle, fat protects hormones and vitamin absorption, carbs fuel performance. You can't deficit your way out of needing the floors.
- **Fiber is a floor** (default 38g/day, configurable as `targets.fiber`). Adequate fiber matters for cardiovascular health, blunting the glycemic response, satiety (directly useful on a cut), and gut health. There is no upper bound worth flagging -- more fiber is not a budgeting problem the way fat is -- so it's a floor, not a window. Two fiber-specific rules:
  - **Carb-load days suspend the fiber floor.** Low-residue eating before a long endurance effort is deliberate (high fiber close to a hard effort risks GI distress). On carb-load styles the dashboards show the fiber total as a plain number with no bar and no color judgment.
  - **Blank is unmeasured, not zero.** Days whose foods were logged without fiber values are a data gap, not a diet failure. The floor judgment applies only to days logged with fiber values.

Targets are seeded by the setup wizard ([[Knowledge/Jesse-Guidelines/Diet-Setup-Wizard]]) and stored in `Projects/Diet/Overview.md`. The dashboard rendering of floors, ceilings, and windows (goal chips, color zones, legend) is specified in [[Knowledge/Jesse-Guidelines/Diet-Dashboard-Guidelines]].

---

## Adaptive Calorie Target: Exercise Add-Back with Overestimation Haircut

The calorie target rises with logged exercise, but in two steps rather than one — because trackers overestimate burn and because eating back the full burn erases the deficit.

```
Calorie target = [BASE_TARGET] + [ADD_BACK_RATE] × ([LOGGED_EXERCISE_CAL] × (1 − [TRACKER_HAIRCUT]))
```

| Parameter | Default | Why |
|-----------|---------|-----|
| `[TRACKER_HAIRCUT]` | 0.25 | Wearables and cardio machines systematically overestimate calorie burn (swims and ellipticals are worst). Discount the logged number first. |
| `[ADD_BACK_RATE]` | 0.50 | Then eat back only half of the discounted burn. Keeps training-day deficits meaningful but safe, protects recovery and hormones, and guards against accidentally eating into the deficit on noisy estimates. |
| `[BASE_TARGET]` | — | From the setup wizard ([[Knowledge/Jesse-Guidelines/Diet-Setup-Wizard]]), not a literal. |

The two haircuts compound: a 1,000-kcal logged session raises the target by ~375 kcal, not 500.

**Rationale.** Very-low add-back under-fuels training and pushes energy availability toward RED-S (relative energy deficiency) risk. Eating back everything erases the deficit and over-trusts noisy calorie estimates. Half-of-discounted is the balance between the two failure modes.

### Worked examples (rotating personas)

| Session | Logged burn | After haircut (×0.75) | After add-back (×0.50) | Target = base + add-back |
|---------|-------------|-----------------------|------------------------|--------------------------|
| Easy 8 km run | 600 | 450 | 225 | `[BASE_TARGET]` + 225 |
| Masters swim | 800 | 600 | 300 | `[BASE_TARGET]` + 300 |
| Long ride / brick | 1,500 | 1,125 | ~563 | `[BASE_TARGET]` + 563 |

Both rates are configurable in `Overview.md`. Rest days log no exercise, so the target stays at `[BASE_TARGET]` — rest vs. training is handled by the add-back, not a separate day-style (see the registry below).

---

## `diet-today.js` Format Spec

Every log event rewrites `diet-today.js` in full. The file assigns one global object. Field names are a strict contract — do not rename properties. The full field table and the matching HTML renderer live in [[Knowledge/Jesse-Guidelines/Fancy-Dashboard-Build]]; keep the two in sync.

**`diet-today.js` is a derived cache of the CSVs** (see Data Architecture above). Its `meals`/`exercise`/`weight` data must always match the CSV rows; only the scalars (`date`, `dayType`, `dayStyle`, `targets`) are set by hand.

```javascript
window.DIET_TODAY = {
  // Date context
  date: "YYYY-MM-DD",                 // e.g. "2026-05-02"
  dayType: "Training day — 8 km run", // free-text human label (shown in the header)
  dayStyle: "normal",                 // machine field; key into the Day-Style Registry (see below)
  mode: null,                         // legacy free-text banner; null on normal days

  // Targets (numbers from Overview.md / the registry, adjusted for exercise add-back)
  targets: {
    calories: 2225,                   // floor of the calorie window on window days; the ceiling otherwise
    caloriesCap: null,                // upper bound on window days (carb-load); null = pure ceiling
    protein: 150,                     // protein floor
    fat: 70,                          // fat cap (ceiling component of the window)
    fatFloor: 35,                     // fat floor (lower bound of the window); null = treat fat as a pure ceiling
    carbs: 180,                       // carb floor (remainder)
    fiber: 38                         // fiber floor; optional, defaults to 38 when absent
  },

  // Weight (only on weigh-in days; null otherwise)
  weight: null,                       // { lbs, kg, bf, mm } or null

  // Meals logged today (one entry per meal)
  meals: [
    // { name: "Breakfast", time: "07:30",
    //   items: [{ item, amount, cal, p, f, c, fiber }] }
  ],

  // Exercise logged today (one entry per activity)
  exercise: [
    // { type: "Run", time: "06:15", desc: "Easy 8 km", calories: 600 }
  ]
};
```

**Item `fiber`** is grams of dietary fiber per item, mirrored from the CSV's `Fiber_g` column. Write `0` for items logged without a fiber value so the key is always present (the dashboard sums it for the Fiber bar); a blank `Fiber_g` in the CSV stays blank there -- the `0` lives only in the cache.

**Resolution of bar types.** Target *numbers* live in `targets`; bar *types* (floor / ceiling / window) come from the Day-Style Registry, keyed by `dayStyle`. The HTML resolves the style as: explicit `dayStyle` wins → else detect a carb-load marker in `dayType` (back-compat) → else `normal`. Old data files without `dayStyle`, `caloriesCap`, or `fatFloor` still resolve (fat falls back to a pure ceiling, calories to a pure ceiling).

---

## Alcohol Rules

Alcohol is always logged as a separate **"Alcohol"** meal entry, regardless of when it was consumed.

1. Log alcohol in its own meal row under meal name `Alcohol`
2. Include full calorie estimate (standard drinks: beer ~150 cal, wine ~125 cal, 1.5 oz spirit ~100 cal)
3. The next-day journal must include a note with:
   - Total alcohol calories consumed
   - Percentage of the daily target those calories represented
   - Example: `Note: 350 cal alcohol yesterday (18% of 1,942 target)`

Do not embed alcohol calories inside a meal (e.g., "dinner + 2 beers"). Separate logging keeps the food log clean and makes pattern analysis easier.

---

## Day-Style Registry

The day-style system is an explicit, extensible registry — not string-matching. Adding a style is one table row here plus one entry in the HTML's `STYLE_PROFILES` map. **This table is the canonical source of truth.** The HTML encodes only the bar *types* (it cannot read this markdown), so the two must be kept in sync; the dashboard build spec ([[Knowledge/Jesse-Guidelines/Fancy-Dashboard-Build]]) references this registry and notes the sync requirement.

`dayStyle` is the machine field; `dayType` stays the free-text human label. Resolution: explicit `dayStyle` wins; if absent, fall back to detecting a carb-load marker in `dayType` (back-compat); else `normal`.

| `dayStyle` | Calorie target | Cal bar | Protein | Fat | Carbs | When |
|------------|----------------|---------|---------|-----|-------|------|
| `normal` | base + add-back | ceiling | floor | window | remainder floor | default — rest or training (add-back handles exercise) |
| `long-run` / `endurance` | base + add-back + glycogen bonus | ceiling | floor | window | remainder + glycogen carbs | a long endurance session that day |
| `refeed` | ~maintenance + add-back | ceiling | floor | window | high remainder floor | periodic diet-break on a long cut |
| `sick` | ~maintenance, loose | ceiling (guidance) | floor | window | remainder floor | illness/recovery — deficit suspended, floor-miss flags off |
| `carb-load-training` | high window | window | floor | minimize-ceiling | high floor | days before a long endurance event (shorter load) |
| `carb-load-race` | higher window | window | floor | minimize-ceiling | higher floor | days before a goal race (longer load) |
| `fasting` | low ceiling (configurable) | ceiling | floor (muscle-sparing) | reduced floor | minimal | only if the user runs a fasting/TRE protocol |

### Per-style notes

- **Rest vs. training is not a separate style.** The add-back formula differentiates them. `long-run`/`endurance` is named only for the glycogen bonus.
- **Refeed / diet-break** deliberately pauses the deficit to blunt metabolic adaptation and the leptin drop on an extended cut. The extra calories go to carbs (the leptin lever); protein and fat floors are unchanged; expect a transient glycogen-water weight blip.
- **Sick:** eat to ~maintenance, prioritize protein and fluids; floor-miss flags are suppressed (low intake while ill is expected).
- **Carb-load:** fat becomes a *minimize-it ceiling* (not the window) to free calorie room for carbs. Keep the activation cross-check — scan for a qualifying event within the lookahead window, count back by the protocol length, activate the targets. Carb-science baseline is 8–12 g/kg/day for trained endurance athletes; scale to bodyweight.
- **Fasting:** ships as a configurable placeholder profile with sensible muscle-sparing defaults. It is optional and off by default; the user confirms their protocol's numbers.
- **Fiber across styles:** the fiber floor (`targets.fiber`, default 38g) applies on every style except the carb-load styles, which **suspend** it (the dashboards then show fiber as a plain total with no bar). Fiber is not part of the per-style target numbers; it is a constant floor toggled only by the carb-load suspension.

### Carb-load activation cross-check (required before logging complete)

On a carb-load style, verify before marking the day done:

1. Calories within the day's window (`targets.calories` floor to `targets.caloriesCap`)
2. Carbs ≥ the carb floor for the protocol
3. Fat ≤ the minimize-ceiling cap

If any check fails, note it explicitly in the journal.

---

## Floor-Miss Monitoring

A single under-floor day is an incident; the *pattern* is what needs tracking — so the live flags don't nag on isolated misses while a real trend goes unsurfaced.

- **Window:** the trailing `[FLOOR_MISS_WINDOW]` logged days (default 7).
- **Threshold:** a floor missed on `[FLOOR_MISS_COUNT]`+ of those days (default 3) is a trend to surface.
- **What counts as a miss:**
  - **Protein:** below ~90% of the protein floor.
  - **Fat:** below the fat floor.
  - **Fiber:** below ~50% of the fiber floor -- counting only days logged with fiber values (blank-fiber days are unmeasured, not misses) and skipping carb-load days (the floor is suspended there).
- **Suppression:** days with `dayStyle: "sick"` (and, for fat, `fasting`) do not count as misses — low intake is expected then.

Surface a confirmed trend in three places: the coach's notes ([[Knowledge/Jesse-Guidelines/Fancy-Dashboard-Build]]), a check-in, and the weekly analysis report ([[Knowledge/Jesse-Guidelines/Sunday-Weekly-Diet-Analysis]]). Both thresholds are configurable in `Overview.md`.

---

## Food Identification Rule

When the user names a food, check `food-log.csv` with a real CSV parser for recent entries (last 30 days) and reuse nutritional values. This prevents drift between logging sessions. Fall back to general knowledge estimates only if no recent match exists. Always mark estimates with `[est.]`.
