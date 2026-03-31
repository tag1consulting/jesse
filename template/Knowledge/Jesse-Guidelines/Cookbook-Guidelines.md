# Cookbook Guidelines

Jesse maintains a personal cookbook in `Knowledge/Cookbook/`. Recipes are living documents that grow with each cook.

---

## Recipe File Format

Every recipe follows this structure:

### Header

```markdown
# [Recipe Name]

**Source:** [where it came from -- developed in conversation, adapted from website/book, family recipe, etc.]
**Tags:** [comma-separated: quick, high-protein, weeknight, weekend, grill, kamado, baking, kid-friendly, date-night, etc.]
**Verdict:** [one-line summary -- "Family hit", "Good for solo lunch", "Did not like -- try different prep"]
**Last made:** [YYYY-MM-DD]
**Times made:** [count]

[1-2 sentence description of what this is and why it works (or doesn't)]
```

### Body

```markdown
## Recipe

**Prep:** [time] | **Cook:** [time] | **Serves:** [count]

### Ingredients

- [precise quantities -- grams where possible for nutrition accuracy]
- [group by component if multi-part: "For the sauce:", "For the rub:", etc.]

### Steps

1. [numbered, concise, with temperatures in both C and F]
2. [include timing cues: "until golden, about 3 minutes"]

### Cooking Notes

[Lessons from actual cooks. Updated each time. Format:]
- **[YYYY-MM-DD]:** [what happened, what to change next time]
- **[YYYY-MM-DD]:** [second cook -- what changed, did it improve?]

### Nutrition (per serving)

~[cal] cal | ~[protein]g protein | ~[fat]g fat | ~[carbs]g carbs

[If diet tracker is active, note which day types this fits well: "Good for rest days," "Training day fuel"]

### Family Reviews

| Person | Reaction | Notes |
|--------|----------|-------|
| [name] | [Hit / OK / Miss] | [specifics -- what they liked/disliked] |

[Reviews accumulate. Don't overwrite -- add new rows with dates if reactions change over time.]

### Tool References

[If the recipe uses specific equipment, link to the tool reference:]
- Kamado setup: [[Knowledge/Cookbook/Tools/Tool-Name]] -- [specific section]
```

---

## Directory Structure

```
Projects/
  drafts/                         # New recipes start here as drafts
    [Recipe-Name].md              # First cook, untested, or in-progress
Knowledge/
  Cookbook/
    [Recipe-Name].md              # Keepers and worth-repeating
    did-not-like/                 # Recipes that didn't work
      [Recipe-Name].md            # Preserved with verdict explaining why
    Ideas/                        # Untested concepts, meal planning ideas
      [Idea-Name].md
    Tools/                        # Equipment reference guides
      [Tool-Name].md              # Comprehensive reference, not recipes
```

**Filing rules:**

- New recipes start in `Projects/drafts/` like any other draft. They follow the standard draft lifecycle and get archive footers.
- After cooking and reviewing, file to `Knowledge/Cookbook/` if it's a keeper.
- If the verdict after 1-2 cooks is negative, file to `Knowledge/Cookbook/did-not-like/` with a clear note on what didn't work and what to try differently.
- `Ideas/` is for "someday" items, ingredient combinations to try, or meal plans not yet executed.
- Tool files are reference material -- temperatures, setups, techniques. They live in `Knowledge/Cookbook/Tools/` and don't move.

---

## Ingredient-Driven Meal Design

When the user describes what they have available and asks what to make:

1. **Inventory what's available.** Fridge, pantry, garden, freezer -- whatever the user mentions.
2. **Check the cookbook.** Search existing recipes that use those ingredients. Prioritize recipes tagged as hits.
3. **Check cooking tools.** What equipment is available? A kamado opens different options than a stovetop-only kitchen.
4. **Check constraints.** Time available, number of people, skill level, dietary restrictions.
5. **If the diet tracker is active:** Check remaining macro budget. A user with 600 cal and 40g protein remaining needs a different meal than one with 1,500 cal to spend. Prioritize meals that fill macro gaps (high-protein if protein is low, carb-heavy if carbs are low on a training day).
6. **Design the meal.** Either adapt an existing recipe or create a new one from scratch. Provide the full recipe format with estimated nutrition. Save the new recipe to `Projects/drafts/`.
7. **After cooking:** The user reports back. Update the recipe with cooking notes and family reviews. Once reviewed, file to `Knowledge/Cookbook/` or `Knowledge/Cookbook/did-not-like/`.

The cookbook gets smarter over time -- Jesse knows what ingredients the user tends to have, what the household likes, what tools are available, and what the macro situation looks like.

---

## Tool Reference Files

Tool files live in `Knowledge/Cookbook/Tools/` and follow this format:

```markdown
# [Tool Name] -- [Model if applicable]

[Quick reference table -- all items at a glance with temps, times, notes]

## [Category: e.g., Beef, Pork, Fish]

### Setup
- [Heat/temperature]
- [Fuel/charcoal/wood type]
- [Accessories needed]

### Method
1. [Step-by-step]

### Temperature Targets
- [Internal temps, resting times]

### Notes
- [Tips, warnings, lessons learned]
```

Tool files are comprehensive -- they should be the "I forgot how to set up the grill for ribs" reference. They grow as the user tries new things on the equipment.

---

## Updating Recipes

**After every cook**, update the recipe file:

- Add a new entry under Cooking Notes with the date and what happened
- Update Family Reviews if new people tried it or opinions changed
- Update the "Last made" and "Times made" fields
- If the recipe improved significantly, update the main Steps/Ingredients to reflect the current best version (keep the old version in Cooking Notes for reference)
- If the verdict flipped (liked -> didn't like, or vice versa), move the file accordingly

**Don't silently update.** When modifying a recipe after a cook, briefly note what changed in the Cooking Notes so the user can see the evolution.

---

## Integration with Diet Tracker

When both the Diet Tracker and Cookbook are enabled:

- **Meal logging from recipes:** When the user makes a tracked recipe, auto-populate the food journal with the recipe's nutrition data (adjusted for actual portion size). The user just says "I made the Chicken Pasta, 1 serving" and the journal entry fills in.
- **Macro-aware meal suggestions:** When the user asks "what should I make for dinner?", check the remaining macro budget first. If protein is low, suggest high-protein recipes. If it's a training day and carbs are behind, suggest pasta/rice/potato-based meals.
- **Ingredient + macro intersection:** The most useful mode. "I have chicken and some vegetables, what fits my macros?" combines ingredient availability with nutritional targeting.
- **Recipe nutrition stays in the recipe.** The diet tracker references the recipe's nutrition section. If the recipe is updated (different portion, added ingredient), the nutrition updates in one place.

When only the Cookbook is active (no diet tracker), nutrition sections in recipes are informational only -- no dashboards, no macro tracking, no spreadsheet updates. The recipes still work perfectly; they just don't feed into a tracking system.
