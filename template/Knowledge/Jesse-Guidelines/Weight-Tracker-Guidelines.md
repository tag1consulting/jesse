# Weight Tracker Guidelines

## Overview

This document describes the weight tracking system: data pipeline, phase model, visualization design, and composition tracking. The system uses a 14-day trailing window for pace analysis, a 28-day window for body composition, and a range-based visualization that shows uncertainty rather than false precision.

**Key principle:** Pace tracking is fundamentally uncertain. The visualization reflects that by showing a range (trough vs. raw regression) rather than collapsing to a single number.

---

## Data Pipeline

### Source
Weight data lives in `food-log.xlsx` on the **Weight** sheet.

### Required Columns
| Column | Type | Notes |
|--------|------|-------|
| Date | Date | YYYY-MM-DD format |
| Weight (lbs) | Number | Daily measurement |
| Weight (kg) | Number | Derived: lbs ÷ 2.20462 |
| Phase | Text | User-defined phase label (see Phase Model below) |
| Body Fat % | Number | Optional; only log when measured (not estimated) |
| Muscle Mass (lbs) | Number | Optional; only when Body Fat % is logged |
| Notes | Text | Context: travel, illness, equipment variance, hydration level |

### Precision Requirements
- **Weight:** One decimal place (e.g., 184.3 lbs). Multiple measurements per day are averaged at end of day.
- **Body Fat & Muscle Mass:** Log only from calibrated instruments (bioelectrical impedance, DEXA) on consistent days (same time, same conditions). Once a week or bi-weekly is sufficient.

### Window Definitions
- **Trailing window:** Most recent 14 consecutive days of logged weight data (not calendar days).
- **Composition window:** Most recent 28 consecutive days (used only if BF% and MM are logged).

---

## Phase Model

Users define their own phases in `Overview.md` (or the relevant diet/tracking overview file). Each phase represents a distinct goal with its own weight range and ideal pace.

### Phase Configuration Template

Copy this table into your tracking overview and fill in your own phases:

| Phase | Weight Range | Ideal Pace (lbs/week) | Scale Cap | Zone Breakdown |
|-------|--------------|----------------------|-----------|---|
| Phase 1 | > 200 lbs | 1.0 – 2.0 | 3.0 | TOO SLOW: <1.0 / SLOW: 1.0–2.0 / IDEAL: 1.0–2.0 / TOO FAST: >2.0 |
| Phase 2 | 200 – 180 lbs | 0.75 – 1.5 | 2.5 | TOO SLOW: <0.75 / SLOW: 0.75–1.5 / IDEAL: 0.75–1.5 / TOO FAST: >1.5 |
| Maintenance | ≤ 180 lbs | 0.0 – 0.25 | — | Widget disabled |

### How to Define Your Phases

1. **Identify weight ranges** for each distinct goal (e.g., aggressive loss, moderate loss, stabilization).
2. **Set ideal pace** based on health, sustainability, and sport/activity demands. (Aggressive is 1.0–2.0 lbs/wk; moderate is 0.5–1.5 lbs/wk; maintenance is ±0.25 lbs/wk.)
3. **Set zone boundaries** within or outside your ideal range:
   - **TOO SLOW:** Pace is slower than the low end of ideal (you want to accelerate).
   - **SLOW:** Pace is within or near ideal, trending toward the lower bound (still good, heads-up).
   - **IDEAL:** Pace is comfortably within ideal range.
   - **TOO FAST:** Pace exceeds the high end of ideal (risk of unsustainability, muscle loss, rebound).
4. **Scale cap** controls the visualization height. Set it to 1.5× your highest zone boundary so blocks have room to breathe.

**Important:** Phases are NOT hard cutoffs. If your weight is 180.5 lbs and Phase 2 covers 200–180, you're in Phase 2 (still active). Only disable the widget when you reach maintenance and no longer want to track pace.

---

## Pace Bar: Range-as-Subset Design

The pace bar is the visual heart of the tracker. It shows a range (trough vs. raw pace regression) rather than a single number, because pace estimation has inherent uncertainty.

### Why This Design?

1. **Honest about uncertainty:** Two different regression approaches (trough and raw) rarely agree. Their range shows the margin of estimation error.
2. **Prevents false precision:** A single "pace number" suggests confidence we don't have from 14 days of data.
3. **Shows signal:** When the range is narrow, the signal is strong. When it's wide, you're early in a trend or noise is high.
4. **Visual simplicity:** Empty blocks (⬜) are easy to parse; colored blocks (one to four) draw the eye to the actual signal.

### Algorithm

The bar has **20 blocks**, each representing `scale_cap ÷ 20` lbs/week.

For each block `i` from 0 to 19:

```
block_size = scale_cap / 20
v_center = (i + 0.5) * block_size  // center point of block i

low = min(trough_pace, raw_pace)
high = max(trough_pace, raw_pace)

if low <= v_center <= high:
    color = zone_color(v_center)  // TOO_SLOW, SLOW, IDEAL, or TOO_FAST
else:
    color = EMPTY (⬜)
```

### Calculating Trough vs. Raw Pace

Both are computed from the 14-day trailing window.

**Trough pace (conservative signal):**
1. Compute 7-day rolling minimum of weight within the 14-day window. This finds the lowest dip, accounting for daily variance.
2. Regress days (0–13) against rolling-min weight values (least-squares fit).
3. Slope = weight change per day; multiply by 7 to get lbs/week.
4. Negate: slope is negative (weight loss is downward), so negate to show positive lbs/week.

**Raw pace (direct signal):**
1. Regress days (0–13) directly against daily weight (no rolling min).
2. Slope = weight change per day; multiply by 7 to get lbs/week.
3. Negate for the same reason.

**Example:**
- 14-day window: [180.2, 180.0, 179.8, 179.9, 179.5, 179.3, 179.1, 179.4, 179.0, 178.8, 178.5, 178.6, 178.4, 178.2]
- 7-day rolling min: [180.0, 179.8, 179.8, 179.5, 179.3, 179.1, 179.0, 178.8, 178.5, 178.4, 178.2]
- Trough regression slope: ≈ −0.142 lbs/day → 0.994 lbs/week (after negate).
- Raw regression slope: ≈ −0.138 lbs/day → 0.966 lbs/week.
- Range: [0.966, 0.994] lbs/week.
- If scale cap is 2.5 and IDEAL is 0.75–1.5:
  - Block size = 2.5 ÷ 20 = 0.125 lbs/week per block.
  - Blocks 8–12 (centers 1.0–1.5) are IDEAL; blocks 7–8 (center 0.875–1.0) are SLOW.
  - Both 0.966 and 0.994 fall in SLOW or IDEAL range, so 1–3 blocks are colored.

---

## Progress Bars

Progress bars show movement toward **user-defined goal weights**. Define these in your tracking overview (e.g., "Goal 1: 180 lbs", "Goal 2: 165 lbs").

### Design

- **Bar represents:** Full range from current weight (or highest logged) to goal weight.
- **Fill:** Proportional position from start to goal. Formula: `(current_weight - goal_weight) ÷ (start_weight - goal_weight) × 100%`.
- **Color:** Zone color of the **trough pace** (the conservative signal). This reflects the type of progress you're making.
- **Label:** `X.X / Y lbs to Z (P%)` where X.X is weight lost so far, Y is goal distance, Z is the goal weight, P is percent complete.

### Example

- Start: 200 lbs
- Current: 186.5 lbs
- Goal: 175 lbs
- Trough pace: 1.2 lbs/week (IDEAL zone, if range is 0.75–1.5)

Progress bar fills 13.5 ÷ 25 = 54%, colored IDEAL green. Label reads: `13.5 / 25 lbs to 175 (54%)`.

---

## Body Composition Bars (Optional)

Only render composition bars when:
1. User has logged **Body Fat %** and **Muscle Mass (lbs)** entries.
2. There are ≥10 clean entries in the 28-day window (logged on consistent days, from the same instrument).

If these conditions aren't met, show a note: "Log Body Fat % and Muscle Mass for composition tracking."

### Fat Loss Bar (FatLb)

Shows the rate of fat loss in lbs/week.

**Calculation:**
1. From the 28-day window, select only dates where both BF% and MM were logged.
2. For each pair of consecutive measurement dates, compute:
   - `fat_lbs = weight × (BF% ÷ 100)`
   - `delta_days` and `delta_fat_lbs` between measurements
   - `fat_pace = (delta_fat_lbs ÷ delta_days) × 7`
3. Linear regression on all pairs to get a single fat_pace (lbs/week).

**Visualization:**
- 20 blocks, scale 0 → scale_cap (use 2.0 lbs/week as a reasonable cap).
- Single-block highlight: find the block whose center is closest to fat_pace.
- All other blocks are empty.

**Pseudocode:**
```
scale_cap = 2.0
block_size = scale_cap / 20
fat_pace = regression_slope(fat_lbs_pairs)

for i in 0..19:
    v_center = (i + 0.5) * block_size
    if i == argmin_i(|v_center - fat_pace|):
        color = zone_color(fat_pace)
    else:
        color = EMPTY (⬜)
```

### Lean Mass Δ Bar (LeanΔ)

Shows the change in lean (non-fat) mass in lbs/week.

**Calculation:**
1. From the 28-day window, select dates where both BF% and MM were logged.
2. For each measurement:
   - `lean_lbs = weight × (1 - BF% ÷ 100)` (derived from body fat %)
   - Compare to earlier measurement to compute change per week.
3. Linear regression on all pairs.

**Visualization:**
- 20 blocks, scale 0 → +1.0 lbs/week (lean gain is a positive outcome; scale cap is fixed).
- Single-block highlight at lean_pace.
- All other blocks are empty.

**Zone coloring for lean gain:**
- **TOO FAST (red):** >0.75 lbs/week gain (muscle is being built, but risk of fat gain if calories are too high).
- **IDEAL (green):** 0.25–0.75 lbs/week.
- **SLOW (blue):** 0–0.25 lbs/week.
- **TOO SLOW (gray):** Negative (lean loss; may indicate undereating or inadequate stimulus).

---

## Fancy Dashboard (HTML File)

A self-contained HTML dashboard provides a richer view than Markdown blocks. Place it in your tracking directory as `dashboard.html`.

### Contents

1. **Line chart (Chart.js):**
   - Daily weight (scatter).
   - 7-day moving average (line).
   - Goal weight lines (dashed).
   - Y-axis labeled in lbs; legend shows all series.

2. **Metric cards:**
   - Current weight, weight lost from start, days to goal.
   - Current body fat %, lean mass (if available).
   - Phase, trough pace, raw pace.

3. **Bar visualizations:**
   - Pace bar, progress bars, composition bars — all rendered as 12×16px colored blocks for crisp presentation.
   - Hover tooltip showing block value range.

4. **Light/dark mode:**
   - Reads system preference; user can toggle.
   - Colors adjusted for contrast in both modes.

5. **Coach's notes section:**
   - Pattern-based observations extracted from the last 7 days:
     - "Weight stable, pace slow."
     - "Fat dropping, lean stable—good deficit."
     - "High variance; ignore day-to-day noise."
   - Generated from the pace range and composition trends; not manually edited.

### How to Use

1. Export your Weight sheet from `food-log.xlsx` as CSV (or keep it as XLSX and load via JavaScript library).
2. Update the `data` array in the HTML with your weight entries.
3. Update phase config, goal weights, and scale caps.
4. Open in a browser. Dashboard auto-detects light/dark mode.

---

## Design History: Why This Approach

The current design (range-as-subset pace bar) is the result of five iterations. Each failed approach led to the next. **Document this history to prevent regression.**

### Failed Approach 1: Fill-to-Position (Uniform Color)

**Concept:** Bar fills from left to position of user's pace; entire filled region is one color.

**Why it failed:**
- Users misread it as "progress toward goal" (like a progress bar).
- No distinction between IDEAL and TOO FAST; both looked like "winning."
- Collapsed trough and raw into one number, hiding uncertainty.

**Lesson:** Visual metaphor matters. Progress bars are trained into us; repurposing them confuses signal.

### Failed Approach 2: Fill-to-Position (Zone-Colored)

**Concept:** Bar fills from left; each block is colored by its zone (slow = blue, ideal = green, fast = red).

**Why it failed:**
- Users misread the filled region as "all good paces" (e.g., all shades of green).
- If pace was 1.2 lbs/week (IDEAL) but IDEAL range was 1.0–1.5, the bar showed all blocks 0–1.2 as green, implying 0–1.0 was also ideal.
- Double confusion: fill metaphor + zone misinterpretation.

**Lesson:** Coloring every block up to the position shows zones the user isn't in. That's misleading.

### Failed Approach 3: Zone Ribbon (Terrain Map)

**Concept:** Bar shows three colored regions (TOO SLOW, SLOW, IDEAL, TOO FAST) like a horizontal heatmap. User's pace is marked with a pointer.

**Why it failed:**
- No visual indication of *position* along the scale. Pointer was small and easy to miss.
- Users had to interpret the zone color separately from the pace value—two different mental steps.
- Dense, hard to scan quickly.

**Lesson:** Position information (where are you on the scale?) is critical. Ribbon lost it.

### Failed Approach 4: Fill with Per-Block Zone Colors

**Concept:** Bar fills from left to user's pace position, and each filled block is colored by its zone.

**Why it failed:**
- Worst of both failed approaches: still misread as progress + still showed zones user isn't in.
- If pace was 1.8 lbs/week and TOO FAST started at 2.0, blocks 0–1.8 would be colored (SLOW, IDEAL, TOO FAST), making it look like "you're in TOO FAST but also in SLOW"—confusing.

**Lesson:** Filling from zero to position will always show the user zones they're not in. Must stop filling.

### Current Approach 5: Range-as-Subset (THE FIX)

**Concept:** Bar shows trough and raw pace as a **range**, with only blocks overlapping the range colored by their zone. Everything outside is empty.

**Why it works:**
1. **No false progress metaphor:** Empty blocks make it clear this is a measurement, not a progress tracker.
2. **Honest about uncertainty:** The range (trough vs. raw) shows the margin of estimation error. Users learn that two weeks of data produce an uncertain signal.
3. **Clear position:** Colored blocks show exactly where on the scale the pace falls.
4. **Shows only relevant zones:** Blocks outside the user's range stay empty. Only the zones they're actually in are colored.
5. **Narrow range = strong signal; wide range = noisy signal.** Users naturally learn to interpret the width.

**Key insight:** Two different methods (trough, raw) will rarely match. Instead of forcing them into one number, show both. The difference IS the information.

---

## What NOT to Change: Regression Prevention Rules

Future edits may be tempted to "simplify" or "improve" the design. These rules protect against regressions to failed approaches.

### Rule 1: Don't collapse trough and raw into one number.
The range IS the signal. If you average or pick one, you lose information about estimation confidence.

### Rule 2: Don't go back to fill-to-position for the pace bar.
Ever. It triggers the progress metaphor and shows zones the user isn't in. If you want a simpler visual, redesign from scratch with a different metaphor.

### Rule 3: Don't color blocks outside the user's range.
If raw pace is 1.2 and ideal is 1.0–1.5, coloring blocks 0.0–1.0 as "slow" is misleading. Only color blocks overlapping [trough_min, raw_max].

### Rule 4: Don't go back to a zone ribbon.
It loses position information. The pace bar MUST show where on the scale the measurement falls.

### Rule 5: Don't use yellow for "too fast."
Yellow reads as "caution / slow down" in everyday UI. Use red or blue (reserved for gain-too-fast or loss-too-fast). Yellow is for truly intermediate zones (SLOW range) if needed, but prefer green for IDEAL and blue for TOO FAST.

### Rule 6: Keep FatLb and LeanΔ as single-block highlights, not trough/raw ranges.
Body composition changes are slower and noisier than weight pace. A single block (closest to the computed pace) is more appropriate than a range. Don't apply the range-as-subset design to composition bars.

### Rule 7: Don't render composition bars unless ≥10 clean entries in 28 days.
If BF% and MM are logged inconsistently (different instruments, different times of day), regression noise is too high. The minimum threshold is 10 consistent entries. Warn the user until the threshold is met.

### Rule 8: Maintain the three-bar structure (Pace + Progress + Composition).
Even if composition is hidden, the layout should reserve space. Don't merge or drop progress bars if composition is unavailable. Each serves a different question.

### Rule 9: Don't mix pace ranges (trough/raw) with single-block composition in one visualization.
They answer different questions. Composition is "how fast am I losing/gaining fat/lean?"—a single pace. Pace range is "how uncertain is my weight pace estimate?"—an interval. Don't blur the distinction.

### Rule 10: Keep scale caps user-configurable per phase.
Phase 2 cap might be 2.0, but Phase 1 cap is 3.0. If you hardcode caps, users can't adjust for their goals. Always read caps from the phase config.

---

## Implementation Checklist

- [ ] Define phases in tracking overview (weight ranges, ideal paces, zone boundaries, scale caps).
- [ ] Populate `food-log.xlsx` Weight sheet with dates, daily weights, phases, and (optional) body fat % + muscle mass.
- [ ] Create Markdown tracker file with pace bar (trough/raw), progress bars (vs. goals), and composition bars (if data available).
- [ ] Optional: Create `dashboard.html` with Chart.js visualization, metric cards, and coach's notes.
- [ ] Set a weekly reminder to review pace trends and update notes (every Sunday or Monday).
- [ ] Document your phase boundaries and scale caps clearly so future edits reference them.

---

## Troubleshooting

**"My pace bar is wide (big gap between trough and raw)."**
- This is normal for weeks 1–2. Trough and raw tend to converge after 2–3 weeks. If it stays wide past week 3, variance in your measurements is high (different time of day, hydration, scale calibration). Log time of day and check for patterns.

**"I have one body composition entry. Why can't I see the FatLb bar?"**
- You need ≥10 entries in the 28-day window to render composition. Log BF% and MM on a consistent schedule (e.g., same time, same instrument, every 3 days) for 4 weeks.

**"My fat pace and lean pace don't add up to my weight pace."**
- That's expected. Weight pace is (weight change per day × 7); fat/lean paces are derived from BF% changes, which are noisier than raw weight. They're correlated but not identical.

**"I changed my phase. Do I need to update the tracker?"**
- Update your phase config in the overview, but the tracker will auto-detect the new phase from the "Phase" column in `food-log.xlsx`. No manual edit needed.

---

## References

- **Overview file:** Where phase definitions, goals, and tracking rules live. Update this whenever your targets change.
- **food-log.xlsx:** Single source of truth for all weight data. Formulas should auto-compute kg from lbs.
- **dashboard.html:** Optional rich visualization. Sync data from food-log.xlsx export (CSV or direct load).
