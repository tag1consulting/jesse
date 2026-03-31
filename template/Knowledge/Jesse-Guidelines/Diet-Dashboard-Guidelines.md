# Diet Dashboard Guidelines

When the user logs food or asks for a status update on today's eating, generate a compact nutrition dashboard. This goes in the chat response (not saved to a file) so the user gets instant feedback.

---

## Dashboard Format

```
=== Mon Mar 30 | Training Day | 8.57km run ===

Calories  ██████░░░░░░░░░░░░░░  625 / 2,450  (25%)  ← baseline (what you ate)
  Net     ░░░░░░░░░░░░░░░░░░░░  -187 / 2,450        ← after 812 kcal run
Protein   ████████████░░░░░░░░  51g / 150g   (34%)
Carbs     ░░░░░░░░░░░░░░░░░░░░  11g / 250g   (4%)  ← low
Fat       ██████████████░░░░░░  36g / 73g    (49%)

Remaining (baseline): ~1,825 cal, 99g protein
```

## Rules

1. **Bar is 20 characters wide.** `█` for filled, `░` for empty. Round to nearest block.
2. **Show percentage** in parentheses after the numbers.
3. **Two calorie lines whenever exercise is logged** (any type -- running, strength, walking, swimming, etc.):
   - **Calories (baseline):** Intake only vs. target. This is the primary line -- the user uses this to decide what to eat. Label `← baseline (what you ate)`.
   - **Net:** Intake minus exercise burn. Indented, no bar when negative (show all `░`). Informational only -- do not suggest eating back exercise calories because burn estimates are averages and eating them back can stall weight loss.
   - If no exercise is logged for the day, show only the single Calories line (no Net line needed).
4. **Flag concerns** with `← low` or `← high` when a macro is significantly off-pace for the time of day:
   - After breakfast: protein below 25% of target → `← low`
   - After lunch: carbs below 30% of target on a training day → `← low`
   - Any meal: fat already above 80% of target → `← high`
   - Calories (baseline) above 90% of target with meals remaining → `← high`
5. **"Remaining" line uses baseline** (intake only). Never inflate the remaining budget by adding exercise calories back.
6. **Activity line** in the header: show day type and exercise summary if applicable.

## Three-View Sync

Every food or exercise log updates three things:

1. **Daily journal** -- `Projects/Diet/YYYY-MM-DD.md` with structured meal tables, activity logs, and running totals.
2. **Spreadsheets** -- `food-log.xlsx` (one row per food item) and `exercise-log.xlsx` (one row per activity) at workspace root.
3. **Chat dashboard** -- The compact bar-chart visual shown above.

All three must update on every log. If one is skipped, the data drifts.

## Behavior Rules

- **Don't nag.** Show the dashboard, let the numbers speak. No unsolicited diet advice, no "you should eat more protein" commentary. The flags (`← low`, `← high`) are sufficient.
- **Don't recalculate targets.** The user's targets are defined in `Projects/Diet/Overview.md`. Use them as-is. If they seem wrong, mention it once and move on -- don't adjust without being asked.
- **Calorie/macro estimates come from general knowledge**, not an API. The user corrects values if they know better. Keep it simple and dependency-free.
- **Day types are user-defined** in Overview.md. Don't assume a specific sport or training plan.
- **Cookbook integration:** When both the Cookbook and Diet Tracker are active, logging a meal from a tracked recipe in `Knowledge/Cookbook/` auto-populates the food journal with the recipe's nutrition data (adjusted for portion size). When the Cookbook is not active, ignore this rule.
