# iOS Obsidian Quick-Note Shortcut

A two-tap iOS Shortcut for capturing notes from your phone directly into your vault's `Inbox/` folder. Each capture lands as a separate timestamped file ready for Jesse to find, act on, and archive.

## The Problem

Ideas, follow-ups, and quick reminders happen away from the desk — in the car, on a walk, mid-meeting, in the kitchen. Without a frictionless capture path, they either rot in your head or get scattered across notes apps, email-to-self, and whatever scratch surface was nearest. Jesse's Inbox flow only works if there's a fast, reliable way to drop notes into it from a phone.

## The Solution

An iOS Shortcut named "Add To AI Inbox" that, on a single tap:

1. Pops up a multi-line text input.
2. Generates a timestamp-prefixed filename (`YYYY-MM-DD-HHMM-Quick-Note.md`).
3. Creates that file in the vault's `Inbox/` folder via Obsidian's `obsidian://new` URL scheme, with the typed text as the body.

Pin the shortcut to the Home Screen, Lock Screen, or an Action Button gesture and capture-to-Jesse becomes a sub-second operation.

## Prerequisites

- The Obsidian iOS app installed, with your vault opened at least once on the device (so the `obsidian://` URL scheme is registered).
- An `Inbox/` folder in your vault (already part of the Jesse template).
- The Apple Shortcuts app (built into iOS).

## Configuration

Create a new shortcut and add the following six actions in order. Each action below maps to a tile you drop in from the Shortcuts action library; values inside each tile are configured by tapping the tile.

### 1. Ask for Input

- Action: **Ask for Input**
- Input Type: **Text**
- Prompt: `Note`
- Allow Multiple Lines: **On** (so dictation and multi-paragraph captures work)

This fires the prompt when you run the shortcut.

### 2. Current Date

- Action: **Current Date**

No configuration. Outputs the current timestamp into the variable chain.

### 3. Format Date

- Action: **Format Date**
- Date Format: **Custom**
- Format String: `yyyy-MM-dd-HHmm`
- Locale: **Default**
- Input: the **Current Date** variable from step 2

Produces a string like `2026-05-01-0631`. The `HHmm` token (24-hour time, no separator) keeps the filename shell- and link-friendly — no spaces, colons, or AM/PM tokens.

### 4. URL Encode

- Action: **URL Encode**
- Input: the **Provided Input** variable from step 1
- Mode: **Encode** (default)

Turns line breaks, spaces, and other characters into safe URL escape sequences so they survive being embedded in the URL in step 5.

### 5. URL

- Action: **URL** (the action that holds a URL string with variable interpolation)
- Value:

  ```
  obsidian://new?vault=YourVaultName&file=Inbox/[Formatted Date]-Quick-Note&content=[URL Encoded Text]&overwrite
  ```

  Where the bracketed pieces are variable references inserted by tapping into the URL field and choosing the variable from the picker — they should NOT be typed as literal text:
  - `[Formatted Date]` is the **Formatted Date** variable from step 3.
  - `[URL Encoded Text]` is the **URL Encoded Text** variable from step 4.

What each piece does:

| URL piece | Purpose |
|-----------|---------|
| `obsidian://new` | Obsidian's URL scheme handler for "create a new note." |
| `vault=YourVaultName` | Tells Obsidian which vault to create the note in. URL-encoded — replace spaces with `%20`. |
| `file=Inbox/...-Quick-Note` | The path (relative to the vault root) and filename. The `.md` extension is added automatically. |
| `content=...` | The body of the new note — your captured text, URL-encoded. |
| `overwrite` | Tells Obsidian to overwrite if a file with that name already exists. With minute-level timestamps in the filename, collisions are essentially impossible — the flag is there as a safety net. |

### 6. Open URLs

- Action: **Open URLs**
- Input: the **URL** variable from step 5

This is what actually launches Obsidian and triggers the note creation.

## Customization

Two pieces will need to change for your setup:

1. **Vault name.** Replace `YourVaultName` with the URL-encoded name of your vault. If your vault is `MyNotes` (no spaces), use `vault=MyNotes`. If it's `Personal Vault`, use `vault=Personal%20Vault`. Easiest way: type your vault name into any URL-encoder and copy the result.
2. **Inbox folder name.** Replace `file=Inbox/` with whatever capture folder you use — e.g., `file=00-Inbox/`, `file=Daily/`, or `file=Quick-Capture/`. The folder must already exist in your vault; Obsidian's `obsidian://new` action does not create missing folders.

The filename suffix (`-Quick-Note`) is also stylistic — change it to whatever makes the captures easy to spot in the Inbox folder.

## How It Works

1. You tap the shortcut (Home Screen icon, Lock Screen widget, Action Button, or a Back Tap gesture).
2. iOS prompts you for text, accepting typing or dictation.
3. The shortcut builds a filename from the current date and time and a URL containing your text.
4. Obsidian opens, creates the note in your `Inbox/` folder, and shows it on screen.
5. Next time Jesse runs, the file is in the Inbox queue alongside any other captures from desktop, email, or other sources.

## Tips and Gotchas

**Add the shortcut to your Home Screen, Lock Screen, or Action Button.** Otherwise you have to dig through the Shortcuts app every time — kills the friction-free capture promise. Share → Add to Home Screen for an icon; Lock Screen widget for zero-tap access from a locked phone; Settings → Accessibility → Touch → Back Tap for a gesture trigger.

**Minute-resolution timestamps are the sweet spot.** A minute prefix is short enough to read at a glance and precise enough to never collide unless you fire two captures in the same minute (rare). Going to the second adds noise; going to just the day means a second capture overwrites the first.

**The `overwrite` flag is a safety net, not a feature.** If you find yourself relying on it, your filename pattern isn't unique enough — bump the resolution.

**Obsidian must have opened the vault at least once.** The `obsidian://` URL scheme registers itself on first vault open. Reinstalling the app or wiping the device requires opening the vault once before the shortcut works again.

**Test with the Run button before pinning.** Running the shortcut from inside the Shortcuts app surfaces error messages directly. Errors triggered from a Home Screen icon are easier to miss.

**Dictation works.** "Hey Siri, run Add To AI Inbox" + spoken text + tap-confirm produces the same file. Useful while driving or hands-busy.
