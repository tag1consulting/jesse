# Diet & Exercise Tracking

Daily nutrition and exercise tracking with three synced views: a Markdown journal in your vault, Excel spreadsheets for long-term data, and a compact dashboard in chat for instant feedback.

> **This recipe is optional.** When disabled, Jesse never mentions food, calories, or exercise tracking. It does not include meal planning, recipe suggestions, weight loss advice, or nutrition coaching. The system records and displays -- it doesn't lecture.

## What You Get

- **Daily journal** -- `Projects/Diet/YYYY-MM-DD.md` with structured meal tables, activity logs, and running totals
- **Spreadsheets** -- `food-log.xlsx` (one row per food item) and `exercise-log.xlsx` (one row per activity) at workspace root
- **Chat dashboard** -- A compact bar-chart visual after every food or exercise log, with macro flags and net calorie tracking

Sample dashboard (training day, after lunch):

```
=== Thu Apr 2 | Training Day | 7.2km tempo run ===

Calories  ████████████░░░░░░░░  1,480 / 2,450  (60%)  ← baseline (what you ate)
  Net     ██████░░░░░░░░░░░░░░    768 / 2,450  (31%)  ← after 712 kcal run
Protein   ██████████████░░░░░░  104g / 150g    (69%)
Carbs     █████░░░░░░░░░░░░░░░  68g / 250g     (27%)  ← low
Fat       █████████████████░░░  62g / 73g      (85%)  ← high

Remaining (baseline): ~970 cal, 46g protein
```

Flags appear when macros are off-pace: `← low` for behind, `← high` for ahead. Exercise triggers a two-line calorie display (baseline intake vs. net after burn).

## Vault Structure

```
Projects/
  Diet/
    Overview.md              # Your targets, goals, day type definitions
    YYYY-MM-DD.md            # Daily journals (one per day)
Knowledge/
  Jesse-Guidelines/
    Diet-Dashboard-Guidelines.md  # Dashboard format, sync rules, behavior rules
food-log.xlsx                # One row per food item (workspace root)
exercise-log.xlsx            # One row per activity (workspace root)
```

## Configuration

### 1. Create the Diet directory

```bash
mkdir -p Projects/Diet
```

### 2. Create your Overview file

Create `Projects/Diet/Overview.md` with your targets. Use this template:

````markdown
# Diet Journal -- Overview

Daily food tracking. Individual daily logs in this directory, named `YYYY-MM-DD.md`.

**Goal:** [Your goal here -- weight loss, maintenance, performance, etc.]

---

## Weight

- **Current:** [weight] lbs/kg
- **Target:** [target] lbs/kg
- **Age/sex:** [for TDEE calculation context]

## Targets

| Metric | Rest Day | Training Day | Long Run Day |
|--------|----------|--------------|--------------|
| Calories | [e.g., 1,900] | [e.g., 2,450] | [e.g., 3,000] |
| Protein | [e.g., 120g] | [e.g., 150g] | [e.g., 150g] |
| Carbs | [calculated] | [calculated] | [calculated] |
| Fat | [calculated] | [calculated] | [calculated] |

*Carbs and fat: calculate from remaining calories after protein. Fat ~30% of calorie target / 9 cal per gram. Carbs get the rest.*

## Day Types

Define what qualifies as each day type for your schedule:
- **Rest day:** No structured training (walking, light activity is fine)
- **Training day:** Structured run, bike, swim, or intense gym session
- **Long run day:** Runs over [X] km/miles, races, or multi-hour activities

## Snack Library

*Add foods you like, sorted by what they're good for.*

### High Protein
- [your items]

### Quick Energy (training days)
- [your items]

## Running Averages

*Updated as journals accumulate.*

| Period | Avg Calories | Avg Protein | Days Tracked | Notes |
|--------|-------------|-------------|--------------|-------|
| [week] | -- | -- | 0 | |
````

### 3. Add the guidelines file

The template includes `Knowledge/Jesse-Guidelines/Diet-Dashboard-Guidelines.md`. This controls dashboard format, the three-view sync rule, and behavior rules (don't nag, don't recalculate targets). Edit it to adjust flag thresholds or dashboard layout.

### 4. Add to your instruction file

Add to your instruction file's `## Rules` section (or a Recipes section if you have one):

```markdown
### Diet Tracking

Daily food journal in [[Projects/Diet/Overview]] (targets and goals defined there). Daily logs are `Projects/Diet/YYYY-MM-DD.md`. Excel trackers at workspace root: `food-log.xlsx` (meals) and `exercise-log.xlsx` (activities). When the user logs a meal or exercise, update the daily journal, both relevant spreadsheets, and show the nutrition dashboard per [[Knowledge/Jesse-Guidelines/Diet-Dashboard-Guidelines]].
```

And add to the guidelines index in the Vault Structure section:

```markdown
Knowledge/Jesse-Guidelines/Diet-Dashboard-Guidelines.md
```

### 5. Create spreadsheet templates

On first use, Jesse will create both spreadsheets with these columns:

**food-log.xlsx:** Date, Meal, Item, Amount, Unit, Grams, Calories, Protein, Fat, Carbs, Notes

**exercise-log.xlsx:** Date, Type, Description, Distance (km), Duration, Pace (min/km), Elevation (m), Avg HR, Cadence, Calories, Plan Source, Notes

Or create them manually with those headers if you prefer to have them ready.

## Daily Journal Format

Each day's journal follows this structure:

````markdown
# Food Journal -- [Day], [Date]

**Day type:** [Rest / Training / Long Run] ([activity context])
**Weight:** [if weighed in]

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

**Target ([day type]):** [cal] cal / [protein]g protein
**Remaining:** [cal] cal, [protein]g protein
**Exercise burn:** [total] kcal ([breakdown])
````

## How It Works

1. The user says "log breakfast: two eggs, toast with butter, coffee with milk" or similar.
2. Jesse estimates macros from general knowledge (no external API), updates the daily journal table, appends rows to `food-log.xlsx`, and shows the dashboard in chat.
3. For exercise: "logged a 7km run, 38 minutes, avg HR 155" updates the journal's Activity section, appends to `exercise-log.xlsx`, and the dashboard switches to two-line calorie display.
4. The user can correct any estimate ("that was 300 cal not 250") and all three views update.

## Tips and Gotchas

**Estimates are estimates.** Calorie and macro values come from Jesse's training knowledge, not a food database API. They're close enough for tracking trends but not laboratory-precise. Correct values when you know better.

**The dashboard is opinionated about format.** The 20-character bar width, the flag thresholds, the two-line calorie display with exercise -- these are all configurable in Diet-Dashboard-Guidelines.md. Edit the file to change them.

**Day types are yours to define.** The template suggests Rest/Training/Long Run but you can define whatever categories fit your schedule. The targets table in Overview.md adapts.

**Don't eat back exercise calories.** The dashboard shows net calories for information but the "Remaining" line always uses baseline (intake only). Burn estimates are averages and eating them back can stall weight loss. This is a deliberate design choice baked into the guidelines.

**Spreadsheets are the long-term record.** The daily journals are good for day-of review. The spreadsheets are what you analyze when looking at weekly/monthly trends.

**Removing this recipe** is clean: delete `Projects/Diet/`, the two spreadsheets, `Diet-Dashboard-Guidelines.md`, and the instruction file hook. Nothing else references it.
