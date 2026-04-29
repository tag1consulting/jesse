# Diet & Exercise Tracking

Daily nutrition and exercise tracking with three synced views: a Markdown journal in your vault, Excel spreadsheets for long-term data, and a compact dashboard in chat for instant feedback. Includes weight tracking with progress visualization, body composition metrics, adaptive calorie targets based on exercise, and weekly accountability reports.

> **This recipe is optional.** When disabled, the assistant never mentions food, calories, exercise, or weight tracking. It does not include meal planning, recipe suggestions, diet advice, or nutrition coaching. The system records and displays -- it doesn't lecture.

## What You Get

- **Daily journal** -- `Projects/Diet/YYYY-MM-DD.md` with structured meal tables, activity logs, and running totals
- **Spreadsheets** -- `food-log.xlsx` (meals + weight tracking), `exercise-log.xlsx` (activities) at workspace root
- **Chat dashboard** -- Colored emoji bars with macro flags and adaptive calorie display after every food or exercise log
- **Weight tracking** -- Progress bars toward user goals, trend analysis, body composition metrics (optional), phases for different training cycles
- **Weekly accountability** -- Sunday routine with automated analysis report (four sections: Great, Good, Bad, Ugly) saved to Knowledge/Health/Weekly-Diet-Analysis/
- **Adaptive calorie target** -- Exercise automatically increases daily targets; configurable burn rate to prevent over-eating back calories

## Vault Structure

```
Projects/
  Diet/
    Overview.md              # Targets, goals, phases, body composition goals
    YYYY-MM-DD.md            # Daily journals (one per day)
Knowledge/
  Jesse-Guidelines/
    Diet-Logging-Flow.md           # Logging process and real-time updates
    Diet-Dashboard-Display.md       # Dashboard format, colored bars, sync rules
    Weight-Tracker-Guidelines.md    # Weight tracking, progress bars, trends, phases
    Sunday-Weekly-Diet-Analysis.md  # Weekly accountability report format
  Health/
    Weekly-Diet-Analysis/          # Archive of weekly reports (date-prefixed)
food-log.xlsx                # Food log (Date, Meal, Item...) + Weight sheet (Date, Weight, Phase...)
exercise-log.xlsx            # One row per activity (workspace root)
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
- **Base calories:** Your maintenance or deficit/surplus baseline (e.g. TDEE - 300 for a cut)
- **Exercise multiplier:** 7% by default (suggested for most people). Multiply exercise calories burned × 0.07 and add to base target.
  - Example: 800 kcal run × 0.07 = +56 cal → target becomes 1,900 + 56 = 1,956 cal
  - This prevents bonking without eating back the full burn
  - Edit the multiplier in Diet-Dashboard-Display.md to adjust

**Macros:**
- Protein: set first, then carbs and fat split the remaining calories
- Fat: ~30% of base calorie target / 9 cal per gram
- Carbs: remainder after protein and fat

## Day Types

Define what qualifies as each type for your schedule:
- **Rest day:** No structured training (walking, light activity is fine)
- **Training day:** Structured run, bike, swim, gym session, or sports
- **Long event day:** Runs/rides over [X] km, races, multi-hour activities

## Weight Phases

Define phases for different training cycles. The Weight sheet in food-log.xlsx includes a Phase column.

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

### 3. Add the guidelines files

Create these files in `Knowledge/Jesse-Guidelines/`:

**Diet-Logging-Flow.md** (process for logging meals and exercise):

````markdown
# Diet Logging Flow

When the user logs a meal or exercise:

1. **Parse the input** -- Extract food items, quantities, or exercise type/duration
2. **Estimate nutrition** -- Use knowledge base; when possible, reuse recent entries from food-log.xlsx for that food (prevents drift)
3. **Update daily journal** -- Add row(s) to the meal table for that day in Projects/Diet/YYYY-MM-DD.md
4. **Update food-log.xlsx** -- Append one row per food item with: Date, Meal, Item, Amount, Unit, Grams, Calories, Protein, Fat, Carbs, Notes
5. **Update running totals** -- Recalculate the Running Totals table in the daily journal
6. **Refresh dashboard** -- Show the latest nutrition dashboard per Diet-Dashboard-Display.md
7. **If exercise** -- Also update exercise-log.xlsx with: Date, Type, Description, Distance, Duration, Pace, Elevation, Avg HR, Cadence, Calories, Plan Source, Notes

**Food identification rule:** When the user names a food (e.g., "medium banana", "grilled chicken breast"), check food-log.xlsx for recent entries of that food (last 30 days) and reuse those nutritional values. This prevents drift between planned and actual logging values.

**Correction workflow:** If the user corrects a calorie estimate ("that was 350 not 300"), update:
- The daily journal table
- The corresponding row in food-log.xlsx
- The Running Totals
- The dashboard
````

**Diet-Dashboard-Display.md** (format and display rules):

````markdown
# Diet Dashboard Display Guidelines

After every meal or exercise log, show this dashboard format.

## Bar Format

20-character colored emoji bars using 🟥🟨🟩🟦⬜:
- 🟥 red: under or over depending on metric type
- 🟨 yellow: approaching a boundary
- 🟩 green: on target
- 🟦 blue: informational (net calories)
- ⬜ white: remaining

All blocks in a bar share the same color based on the current zone.

### Floor metrics (Protein, Carbs)
- 0-49%: 🟥 red (needs more)
- 50-79%: 🟨 yellow (on pace)
- 80-100%: 🟩 green (hit or exceeded target)

### Ceiling metrics (Fat, Calories)
- 0-79%: 🟩 green (room to eat)
- 80-99%: 🟨 yellow (approaching limit)
- 100%+: 🟥 red (over target)

## Sample Dashboard

Header shows day type, date, and any active event-prep mode:

```
=== Tue Apr 29 | Training Day | 8km tempo run ===

Calories  🟩🟩🟩🟩🟩🟩🟩🟩🟩🟩🟩🟩🟩⬜⬜⬜⬜⬜⬜⬜  1,480 / 2,256  (66%)
  Net     ░░░░░░░░░░░░░░░░░░░░    768 / 2,256  (34%)  ← after 712 kcal burn
Protein   🟩🟩🟩🟩🟩🟩🟩🟩🟩🟩🟩🟩🟩⬜⬜⬜⬜⬜⬜⬜  104g / 150g  (69%)
Carbs     🟥🟥🟥🟥🟥⬜⬜⬜⬜⬜⬜⬜⬜⬜⬜⬜⬜⬜⬜⬜  68g / 250g   (27%)  ← low
Fat       🟨🟨🟨🟨🟨🟨🟨🟨🟨🟨🟨🟨🟨🟨🟨🟨🟨⬜⬜⬜  62g / 73g    (85%)  ← high

Remaining (baseline): ~970 cal, 46g protein
```

**Flags:**
- `← low` : macro is 25%+ below target (floor metric)
- `← high` : macro is 25%+ above target (ceiling metric)
- `← [mode]` : event-prep mode is active (e.g., "← Carb Load")

**When exercise is logged:**
- Line 1 (Calories): shows baseline intake only
- Line 2 (Net): shows intake minus burn, reflects adaptive target
- The adaptive target is: base_target + (exercise_calories × rate)
- Rate default: 0.07 (7%). Edit in this file to adjust.

## Sync Rule

After every log, all three views are in sync:
- Daily journal updated
- Spreadsheets appended
- Dashboard shown
- No manual syncing needed
````

**Weight-Tracker-Guidelines.md** (weight tracking and progress):

````markdown
# Weight Tracker Guidelines

## Weight Sheet (food-log.xlsx)

Add a "Weight" sheet to food-log.xlsx with these columns:

| Date | Weight | Weight (kg) | Phase | Body Fat % | Muscle Mass | Notes |
|------|--------|------------|-------|------------|-------------|-------|
| 2026-04-29 | 175.0 | 79.4 | Phase 1 | 22.5 | 142.0 | Morning, post-workout |

**Notes field:** Context like "morning", "evening", "clothes", "hydration level", etc. helps interpret variance.

## Progress Bars

Show a 20-block progress bar toward the user's target weight. Format:

```
Current: 175.0 lbs
Target:  160.0 lbs

Progress: 🟩🟩🟩🟩🟩🟩🟩🟩🟩⬜⬜⬜⬜⬜⬜⬜⬜⬜⬜⬜  75% done (15.0 lbs down, 15.0 to go)
```

Calculation: (current_weight - start_weight) / (target_weight - start_weight) × 20 blocks

## Pace Visualization

Calculate the **rate of change** using linear regression over the last 7 days:
- Regression slope = average lbs/day (or kg/day)
- Project to target: days_remaining = (current - target) / slope
- Show as: "At current pace: 45 days to target" or "Ahead of pace"
- If flat or gaining: "Pace: holding steady" or "Not moving in target direction"

Example output:
```
Rate of change (last 7 days): -0.65 lbs/day
Days to target at current pace: 23 days
[adjust factors: exercise consistency, adherence, sleep?]
```

## Body Composition (Optional)

If your scale reports body fat % and muscle mass:

```
Weight: 175.0 lbs | Body Fat: 22.5% | Muscle Mass: 142.0 lbs
Progress: 
  Fat mass:   39.4 lbs → 32.0 lbs target (7.4 lbs to lose)
  Muscle:    142.0 lbs → 145.0 lbs target (3.0 lbs to build)
  
Composition: You're losing fat while gaining/holding muscle (good recomp!)
```

## Phase System

Phases represent different training goals. When you enter a new phase, update the "Phase" column in the Weight sheet.

Each phase has:
- Start date
- Target weight
- Target rate of change (lbs/week or kg/week)
- Notes (e.g., "aggressive cut", "maintenance", "bulk")

Show the current phase and its target in the dashboard header.

## Trend Chart

Generate an HTML file with Chart.js showing:
- X-axis: dates (last 60 days or all data)
- Y-axis: weight
- Line chart with actual data points
- Linear regression trend line
- Horizontal line at target weight
- Color zones (green = on pace, yellow = slow, red = off track)

File saved to: Projects/Diet/weight-trend.html

The user can open this in a browser any time for a visual overview.

**Update frequency:** Regenerate after every weight entry.
````

**Sunday-Weekly-Diet-Analysis.md** (weekly accountability report):

````markdown
# Sunday Weekly Diet Analysis

Runs every Sunday at start of day. Produces a saved report with four sections.

## Report Format

Filename: `Knowledge/Health/Weekly-Diet-Analysis/YYYY-MM-DD-weekly-report.md`

### 1. Great

What went well this week. Use actual data:
- Days on target for specific macros
- Consistency streak (e.g., "logged 6 of 7 days")
- Personal wins ("nailed protein 5 days in a row")
- Exercise consistency or PRs

### 2. Good

Solid efforts, minor room for improvement:
- Days close to target (within 5%)
- Good trends (weight moving in right direction)
- Activity logged regularly

### 3. Bad

Areas that need attention, ranked by impact:
1. Biggest deviation (e.g., "underate protein Wed-Fri")
2. Consistency gap (e.g., "logged 4 of 7 days")
3. Pattern (e.g., "weekends higher calories than planned")

### 4. Ugly

Significant blocker or pattern needing action:
- Weight stalled for 3+ weeks despite plan
- Large macro misses 4+ days/week
- Injury or external factor affecting plan

---

## Improvement Suggestions

Ranked by data impact, not motivation:

1. **Target:** [specific macro or goal]
   **Issue:** [what the data shows]
   **Action:** [specific, testable change]
   **Why:** [data-driven reason]

Example:
1. **Target:** Carb adequacy on training days
   **Issue:** Logged 4 training days, avg carbs 180g (target 250g). Likely cause: missing snacks post-workout.
   **Action:** Prep a portable carb snack (banana, rice cake, date) to eat within 30 min of training.
   **Why:** Underfueling carbs impairs recovery; small snacks close the gap without adding meal prep.

---

## Week-over-Week Trends

Show last 4 weeks of data:

| Week | Avg Calories | Avg Protein | Days Logged | Weight Change | Notes |
|------|-------------|------------|-------------|---------------|-------|
| Apr 20-26 | 1,950 | 128g | 6 | -0.8 lbs | good week |
| Apr 13-19 | 1,900 | 125g | 7 | -1.2 lbs | on pace |
| Apr 6-12 | 2,100 | 115g | 5 | +0.3 lbs | weekend creep |
| Mar 30-Apr 5 | 1,850 | 135g | 6 | -1.5 lbs | strong start |

---

## Data Tables (Appendix)

Full daily breakdown (optional if space is a concern):

| Date | Calories | Protein | Carbs | Fat | Weight | Phase | Notes |
|------|----------|---------|-------|-----|--------|-------|-------|
| ... | | | | | | | |

---

## Next Steps

1. [action from improvement suggestions #1]
2. [action from improvement suggestions #2]
3. [data collection gap if any]
````

### 4. Add to your instruction file

Add to your instruction file's `## Rules` section:

```markdown
### Diet Tracking

Daily food journal at `[[Projects/Diet/Overview]]` (targets and goals defined there). Daily logs are `Projects/Diet/YYYY-MM-DD.md`. Excel trackers at workspace root: `food-log.xlsx` (meals + weight) and `exercise-log.xlsx` (activities). When the user logs a meal or exercise, follow `[[Knowledge/Jesse-Guidelines/Diet-Logging-Flow]]`. When the user asks about their state without new data, use `[[Knowledge/Jesse-Guidelines/Diet-Dashboard-Display]]` to show current progress. Weight tracking uses the Weight sheet in food-log.xlsx per `[[Knowledge/Jesse-Guidelines/Weight-Tracker-Guidelines]]`. Every Sunday, produce a weekly accountability report per `[[Knowledge/Jesse-Guidelines/Sunday-Weekly-Diet-Analysis]]` and save to `Knowledge/Health/Weekly-Diet-Analysis/`.
```

### 5. Create spreadsheet templates

On first use, create both spreadsheets with these columns:

**food-log.xlsx:**
- Sheet 1 (Food Log): Date, Meal, Item, Amount, Unit, Grams, Calories, Protein, Fat, Carbs, Notes
- Sheet 2 (Weight): Date, Weight, Weight (kg), Phase, Body Fat %, Muscle Mass, Notes

**exercise-log.xlsx:** Date, Type, Description, Distance (km), Duration, Pace (min/km), Elevation (m), Avg HR, Cadence, Calories, Plan Source, Notes

Or create them manually with those headers if you prefer to have them ready.

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

### Logging Meals

1. User says: "log breakfast: two eggs, toast with butter, coffee with whole milk"
2. Assistant estimates macros from knowledge base
3. Checks food-log.xlsx for recent entries of similar items to reuse values (prevents drift)
4. Updates the daily journal table, appends to food-log.xlsx, recalculates running totals
5. Shows the dashboard with colored emoji bars

### Logging Exercise

1. User says: "logged a 7km run, 38 minutes, avg HR 155"
2. Assistant estimates calories burned, updates the daily journal Activity section, appends to exercise-log.xlsx
3. Dashboard now shows two-line calorie display:
   - Line 1: baseline intake
   - Line 2: net (intake - burn), with adaptive target reflected
4. Adaptive target = base_target + (exercise_calories × 0.07)

### Logging Weight

1. User says: "weighed in: 175.5 lbs"
2. Assistant adds row to Weight sheet in food-log.xlsx
3. Shows progress bar toward target (20 blocks)
4. Shows pace visualization (days to goal at current rate, trend line)
5. If body composition data is available, shows fat vs. muscle breakdown

### Weekly Accountability (Sunday)

1. Every Sunday at start of day, assistant generates a report with four sections: Great, Good, Bad, Ugly
2. Ranked improvement suggestions based on actual data
3. Week-over-week trend table
4. Saved to Knowledge/Health/Weekly-Diet-Analysis/ with date prefix
5. Brief summary shown in morning briefing

### Correcting Entries

If the user corrects a value ("that was 350 not 300"):
- Update the daily journal table
- Update the corresponding row in food-log.xlsx
- Recalculate running totals
- Refresh dashboard

## Tips and Gotchas

**Estimates are estimates.** Calorie and macro values come from the assistant's knowledge, not a food database API. They're close enough for tracking trends but not lab-precise. Correct values when you know better, and the system reuses those values for future entries of the same food.

**The dashboard is opinionated.** The 20-character bar width, colored emojis, flag thresholds, adaptive calorie formula (7% exercise multiplier) -- all configurable in Diet-Dashboard-Display.md. Edit the file to change them.

**Day types are yours to define.** The template suggests Rest/Training/Long Event but you can define whatever categories fit your schedule. The targets table in Overview.md adapts.

**Adaptive targets prevent over-eating back calories.** The formula base_target + (exercise_calories × 0.07) adds only ~7% of the burn, not the full amount. This prevents the common trap of "I ran so I can eat whatever" while still rewarding activity. Edit the multiplier if you prefer a different approach.

**Weight tracking is optional.** If you don't have a scale, skip the Weight sheet. The system works fine with just food and exercise data. If you add body composition metrics later (BF%, muscle mass), add those columns to the Weight sheet.

**Spreadsheets are the long-term record.** The daily journals are good for day-of review and pattern spotting. The spreadsheets are what you analyze when looking at weekly/monthly trends and feed into the weekly accountability report.

**Food identification prevents drift.** When you log "grilled chicken breast" repeatedly, the system reuses the nutritional values from your recent food-log.xlsx entries for that item. This prevents the creep of "well, maybe this one has more fat..." that accumulates over time.

**Event-prep modes override targets temporarily.** If you're carb-loading before a race or taking a diet break, define a mode in Overview.md and activate it in the dashboard header. Regular targets return when the mode expires.

**Works with the [Cookbook](./cookbook.md).** When both are active, logging a meal from a tracked recipe auto-populates nutrition data, and meal suggestions consider remaining macro budget. Either works standalone.

**Removing this recipe** is clean: delete `Projects/Diet/`, the two spreadsheets, the four guidelines files, the weekly analysis archive, and the instruction file hooks. Nothing else references it.
