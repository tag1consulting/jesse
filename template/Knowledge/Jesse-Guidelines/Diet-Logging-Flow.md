# Diet Logging Flow

Exact sequence for every meal, exercise, and weigh-in log. Execute in order.

---

## Per-Log-Event Flow

### On every meal or exercise log

1. **Parse input** — extract food items, quantities, or exercise type/duration/calories
2. **Estimate nutrition** — use knowledge base; check `food-log.xlsx` for recent entries of that food (last 30 days) and reuse those values to prevent drift
3. **Update daily journal** — add row(s) to the meal or activity table in `Projects/Diet/YYYY-MM-DD.md`; recalculate the Running Totals table
4. **Append spreadsheet** — add row(s) to `food-log.xlsx` (meals) or `exercise-log.xlsx` (exercise)
5. **Rewrite `diet-today.js`** — write the complete updated `window.DIET_TODAY` object (see spec below)
6. **Show ASCII dashboard in chat** — per [[Knowledge/Jesse-Guidelines/Diet-Dashboard-Guidelines]] format
7. **Fancy dashboard** — auto-updates on next browser reload via `diet-today.js`; **do not rebuild** `Dashboard-Fancy.html` on food/exercise logs

### On weigh-in

Same steps 1–6, plus:

8. **Rebuild `Dashboard-Fancy.html`** — per [[Knowledge/Jesse-Guidelines/Fancy-Dashboard-Build]]; this rebuilds the weight chart, pace bars, progress bars, and coach's notes

### Correction workflow

If the user corrects a value ("that was 350 not 300"), update:
- The daily journal table and Running Totals
- The corresponding row in `food-log.xlsx`
- Rewrite `diet-today.js` with corrected values

---

## Adaptive Calorie Target: Tiered Exercise Adjustment

The adjustment rate depends on exercise volume for the day:

| Exercise calories burned | Rate | Formula |
|--------------------------|------|---------|
| ≤ 1,500 cal | 7% | `baseline + (exercise_cal × 0.07)` |
| > 1,500 cal | 10% | `baseline + (exercise_cal × 0.10)` |

The higher rate for large training days prevents under-fueling without over-correcting on normal sessions.

Example: 1,600 cal burn on a long run day → adaptive target = baseline + 160 cal (10%).
Example: 600 cal burn on a normal run → adaptive target = baseline + 42 cal (7%).

---

## `diet-today.js` Format Spec

Every log event rewrites `diet-today.js` in full. The file assigns one global object:

```javascript
window.DIET_TODAY = {
  // Date context
  date: "YYYY-MM-DD",           // e.g. "2026-05-02"
  dayLabel: "Fri May 2",        // display label, no year
  mode: null,                   // null, or string e.g. "CARB-LOAD DAY 1/2"

  // Calorie data
  caloriesActual: 0,
  caloriesTarget: 1900,         // adaptive target
  caloriesPct: 0,               // integer: (caloriesActual / caloriesTarget) * 100
  baselineTarget: 1900,         // base from Overview.md before exercise adjustment
  exerciseCalories: 0,
  netCalories: 0,               // caloriesActual - exerciseCalories

  // Macro data
  proteinActual: 0,
  proteinTarget: 150,
  carbsActual: 0,
  carbsTarget: 180,
  fatActual: 0,
  fatTarget: 70,

  // Remaining
  caloriesRemaining: 1900,
  proteinRemaining: 150,

  // Meals
  meals: [
    // {
    //   name: "Breakfast",
    //   items: [{ item, amount, grams, calories, protein, fat, carbs, notes }],
    //   totals: { calories, protein, fat, carbs }
    // }
  ],

  // Exercises
  exercises: [
    // { type: "Run", description: "8.5km, 45 min", calories: 600 }
  ],

  // Flags
  flags: [],

  // Weight (if logged today)
  weightLbs: null,
  weightKg: null,
};
```

Field names are a strict contract. Do not alter property names. See [[Knowledge/Jesse-Guidelines/Fancy-Dashboard-Build]] for the full field name table.

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

## Carb-Load Day Protocol

Triggered 2 days before a long run exceeding 20 km (or per user-defined threshold in `Overview.md`).

### Targets

| Metric | Target | Notes |
|--------|--------|-------|
| Calories | 2,400–2,600 cal | Window (range metric, not a ceiling) |
| Carbs | 400–500g | Reference 450g |
| Protein | 150g | Hold at normal target |
| Fat | ≤ 50g | Hard ceiling, lower than normal |

### Dashboard Behavior

- Header shows: `=== [Day Date] | CARB-LOAD DAY 1/2 === ` (or `2/2` on the second day)
- Calorie bar switches to **window metric** (both floor and ceiling apply; bar is green only in the 2,400–2,600 range)
- `mode` in `diet-today.js` is set to `"CARB-LOAD DAY 1/2"` or `"CARB-LOAD DAY 2/2"`

### Three-Point Cross-Check (required before logging complete)

Verify before marking the day done:

1. Calories in range (2,400–2,600)
2. Carbs ≥ 400g
3. Fat ≤ 50g

If any check fails, note it explicitly in the journal.

---

## Food Identification Rule

When the user names a food, check `food-log.xlsx` for recent entries (last 30 days) and reuse nutritional values. This prevents drift between logging sessions. Fall back to general knowledge estimates only if no recent match exists. Always mark estimates with `[est.]`.
