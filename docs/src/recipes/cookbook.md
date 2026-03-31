# Cookbook

A living personal cookbook that tracks family reviews, cooking tool references, iteration notes, and ingredient-driven meal design.

> **This recipe is optional.** When disabled, Jesse doesn't manage cooking recipes or suggest meals. It works standalone or alongside the [Diet & Exercise Tracking](./diet-tracking.md) recipe -- when both are active, meal logging from tracked recipes auto-populates nutrition data, and meal suggestions consider remaining macro budget.

## What You Get

- **Structured recipes** -- Living Markdown files with ingredients, steps, cooking notes, per-person reviews, and nutrition estimates
- **Verdict system** -- Keepers in `Knowledge/Cookbook/`, duds in `did-not-like/` (preserved so they're not re-attempted blindly), untested ideas in `Ideas/`
- **Cooking tool references** -- Equipment-specific guides in `Tools/` (grill temps, setups, techniques) linked from recipes that use them
- **Ingredient-driven meal design** -- Describe what you have on hand; Jesse designs a meal from your ingredients, existing recipes, available tools, and (if diet tracker is active) remaining macro budget
- **Iteration tracking** -- Recipes evolve with each cook. Cooking notes accumulate, reviews update, ingredients and steps improve over time

## Vault Structure

```
Projects/
  drafts/                           # New recipes start here as drafts
Knowledge/
  Cookbook/
    [Recipe-Name].md                # Keepers and worth-repeating
    did-not-like/                   # Recipes that didn't work (preserved with notes)
    Ideas/                          # Untested concepts and meal ideas
    Tools/                          # Equipment reference guides
  Jesse-Guidelines/
    Cookbook-Guidelines.md           # Format, filing rules, meal design behavior
```

## Configuration

### 1. Create the Cookbook directories

```bash
mkdir -p Knowledge/Cookbook/did-not-like Knowledge/Cookbook/Ideas Knowledge/Cookbook/Tools
```

### 2. Add the guidelines file

The template includes `Knowledge/Jesse-Guidelines/Cookbook-Guidelines.md`. This controls recipe format, the verdict/filing system, ingredient-driven meal design behavior, tool reference format, and diet tracker integration rules.

### 3. Add to your instruction file

Add to your instruction file's `## Rules` section:

```markdown
### Cookbook

Personal cookbook in [[Knowledge/Cookbook/]] managed per [[Knowledge/Jesse-Guidelines/Cookbook-Guidelines]]. Recipes track family reviews, cooking tool references, and iteration notes. New recipes start in `Projects/drafts/` and file to `Knowledge/Cookbook/` after cooking and review. When the user describes available ingredients, design meals from what's on hand. If the Diet Tracker recipe is also active, bridge nutrition tracking with meal design.
```

And add to the Vault Structure section:

```markdown
Knowledge/Cookbook/               -- Personal cookbook (recipes, tools, ideas)
Knowledge/Jesse-Guidelines/Cookbook-Guidelines.md
```

## Recipe Lifecycle

1. **New recipe created** -- Jesse writes it to `Projects/drafts/` like any other draft, with an archive footer.
2. **User cooks it** -- Reports back with results. Jesse updates cooking notes and family reviews.
3. **Filing decision:**
   - Keeper → move to `Knowledge/Cookbook/`
   - Didn't work → move to `Knowledge/Cookbook/did-not-like/` with notes on what went wrong
   - Needs another try → stays in `Projects/drafts/` until the next cook

This follows the standard draft lifecycle -- recipes in `Projects/drafts/` get archive footers and are processed like any other draft.

## Recipe Format

Every recipe follows a consistent structure. Here's a condensed example:

````markdown
# Lamb Chops with Rosemary and Smashed Potatoes

**Source:** Developed 2026-04-05, adapted from grill reference
**Tags:** grill, kamado, weekend, high-protein, date-night
**Verdict:** Big hit for adults, mixed for kids.
**Last made:** 2026-04-12
**Times made:** 2

Weekend dinner -- grilled lamb chops with rosemary-garlic smashed potatoes.

## Recipe

**Prep:** 10 min | **Cook:** 35 min | **Serves:** 4

### Ingredients

#### For the Lamb
- 8 lamb loin chops (~800g total)
- 2 tbsp olive oil
- 3 cloves garlic, minced
- 2 sprigs fresh rosemary, chopped
- Salt, pepper

### Steps

1. Set up grill for direct heat at 260C / 500F.
2. Rub chops with oil, garlic, rosemary, salt, pepper. Rest 15 min.
3. Grill 3 min per side for medium-rare (internal 57C / 135F).
4. Rest 5 min tented with foil.

### Cooking Notes

- **2026-04-05:** First cook. Grill ran hot (280C), chops closer to medium.
  Still excellent. Kids ate 1 chop each, left the fat.
- **2026-04-12:** Dialed to 260C, much better. Added Dijon to the oil
  rub -- subtle but good, worth keeping.

### Nutrition (per serving)

~680 cal | ~48g protein | ~38g fat | ~35g carbs

### Family Reviews

| Person | Reaction | Notes |
|--------|----------|-------|
| Adult 1 | Hit | Rosemary crust is great. Dijon addition is a keeper. |
| Adult 2 | Hit | Prefers medium-rare -- second cook was better. |
| Kid 1 | OK | Ate the meat, left the fat and rosemary bits. |
| Kid 2 | OK -> Hit | Liked second cook with trimmed, smaller chops. |

### Tool References

- Grill setup: [[Knowledge/Cookbook/Tools/Grill-Reference]] -- Lamb section
````

## "Did Not Like" Files

When a recipe doesn't work, file it to `Knowledge/Cookbook/did-not-like/` instead of deleting. The file preserves what went wrong and what to try differently:

````markdown
# Halibut Acqua Pazza

**Source:** Classic Italian, first cook 2026-03-29
**Tags:** fish, Italian, poached, weeknight
**Verdict:** Did not like. Poaching method made the halibut bland and
textureless. Try pan-seared with crispy skin or grilled instead.
**Last made:** 2026-03-29
**Times made:** 1

### What Went Wrong

Acqua pazza works better with thinner, more flavorful fish (branzino,
snapper). Halibut is too thick and mild -- it steams in the liquid and
comes out flat.

### What to Salvage

The smashed potatoes from this meal were a huge hit -- split into their
own recipe: [[Knowledge/Cookbook/Crispy-Smashed-Potatoes]].
````

## Ingredient-Driven Meal Design

The most powerful feature. Describe what you have and Jesse designs a meal:

**You:** "I've got chicken thighs, arborio rice, parmesan, and chard from the garden. What should I make?"

**Jesse checks:**
1. Existing recipes using those ingredients (prioritizes hits)
2. Available cooking tools
3. Time constraints (weeknight default ~40 min unless stated)
4. Remaining macro budget (if diet tracker is active)

**Jesse responds** with a full recipe -- ingredients, steps, estimated nutrition -- saved to `Projects/drafts/`. After cooking, you report back, and Jesse updates cooking notes and family reviews, then files to `Knowledge/Cookbook/`.

The cookbook gets smarter over time. Jesse learns what ingredients you tend to have, what the household likes, what tools are available, and (with the diet tracker) what the macro situation looks like.

## Tool Reference Files

Equipment-specific guides live in `Knowledge/Cookbook/Tools/`. These are comprehensive references, not recipes -- the "I forgot how to set up the grill for ribs" quick-lookup. They cover setup, temperatures, fuel, techniques, and lessons learned. Recipes link to the relevant tool section when technique matters.

Tool files are optional. Users without specialized equipment get full functionality -- tool links just won't appear.

## Integration with Diet Tracker

When both the Cookbook and Diet Tracker are enabled:

- **Auto-populated meal logging** -- "I made the Chicken Risotto, 1 serving" fills in the food journal from the recipe's nutrition data
- **Macro-aware meal suggestions** -- "What should I make for dinner?" considers remaining calorie and macro budget
- **Ingredient + macro intersection** -- "I have chicken and vegetables, what fits my macros?" combines what's available with nutritional targeting

When only the Cookbook is active, nutrition sections are informational only -- no dashboards, no tracking, no spreadsheet updates. Everything still works; it just doesn't feed into a tracking system.

## Tips and Gotchas

**Reviews are per-person, not per-recipe.** A meal can be a hit for one person and a miss for another. Track individual reactions so Jesse can suggest meals the whole household enjoys.

**Recipes evolve.** After every cook, update cooking notes, reviews, and (if it improved) the main steps/ingredients. Keep old versions in Cooking Notes for reference.

**The verdict drives filing.** Keepers go to `Knowledge/Cookbook/`. Duds go to `did-not-like/`. Ideas live in `Ideas/` until tried. New recipes start in `Projects/drafts/` and file after review.

**Family review names are yours.** No assumptions about household structure. Use real names, nicknames, or roles -- whatever makes sense.

**Removing this recipe** is clean: delete `Knowledge/Cookbook/`, `Cookbook-Guidelines.md`, and the instruction file hook. Nothing else references it.
