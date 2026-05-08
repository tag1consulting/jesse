# Diet Dashboard Display

Read-only status display — used when the user asks about their tracking state without logging new data.

## Triggers

- "Where am I today?"
- "Show my tracking" / "show my dashboard"
- Weight progress queries without new data
- "How am I doing?" in a nutrition context

## Flow

1. **Read `diet-today.js`** — extract today's totals (calories, protein, fat, carbs, exercise)
2. **Compute adaptive target** — same formula as Diet-Logging-Flow (baseline + exercise × rate)
3. **Render ASCII dashboard in chat** — per [[Knowledge/Jesse-Guidelines/Diet-Dashboard-Guidelines]] format
4. **Weight tracker** (optional) — show if it's a weigh-in day OR if the user explicitly asks about weight/progress

## Rules

- **Display only.** Never modify xlsx, daily journal, `diet-today.js`, or any config files.
- **No dashboard if no data.** If `diet-today.js` doesn't exist or has no meals, say so rather than rendering empty bars.
- **Fancy dashboard link.** If the user has `Dashboard-Fancy.html`, mention they can also open it in a browser for the visual version.
- **Context-sensitive.** If the user asks "how am I doing?" outside a nutrition context, respond conversationally — don't force the dashboard.
