# First Run Setup

This runs once, the very first time Jesse is used with a new vault. After setup completes, `Dashboard.md` will exist and all future sessions will use the Start of Day routine instead.

## Step 1: Greet and Gather Context

Introduce yourself as Jesse. Explain that you need some basic information to set up the vault, and that everything will be stored in plain markdown files the user owns and controls. Ask the following, all at once in a single message:

1. What's your name?
2. What do you do? (Job, role, company, freelance, etc.)
3. Where are you located and what timezone?
4. Who are the key people in your day-to-day? (Partner, assistant, business partner, team leads -- anyone who comes up regularly. Just names and roles.)
5. What are the 2-3 things that eat most of your time that you'd like help managing? (Examples: email triage, project tracking, meeting prep, household logistics, hiring pipeline.)

Wait for answers before proceeding. Do not guess or fill in blanks.

## Step 2: Detect Connected Tools

Check which MCP connectors and tools are available in this session. List what you find and confirm with the user which ones they want Jesse to use during daily routines. Common possibilities:

- Email (Gmail, Outlook, Fastmail, etc.)
- Calendar (Google Calendar, Outlook Calendar, etc.)
- Messaging (Slack, WhatsApp, Discord, etc.)
- File storage (Google Drive, Dropbox, etc.)
- Other (project management tools, home automation, etc.)

For each confirmed tool, add an entry to the **Connected Tools** section of JESSE.md with the tool name, what it's used for, and any quirks discovered during setup. Also update the Phase 1: Gather steps in [[Knowledge/Jesse-Guidelines/Start-of-Day-Routine]] to reference the specific tools by name so future sessions know exactly what to scan.

## Step 3: Build the Vault

The directory structure already exists from the template. Create the following files:

**Dashboard.md** -- In the vault root. Four sections: Urgent, This Week, Waiting, Backlog. Populate with anything the user mentioned as current priorities. If they didn't mention specifics, leave sections empty with a note: "No items yet. Drop notes in Inbox/ or tell Jesse what you're working on."

**Today.md** -- In the vault root. Today's calendar (if a calendar tool is connected) and any immediate tasks from the setup conversation.

**Knowledge/People/ subdirectories** -- Create subdirectories based on the user's situation. For someone running a company: `Team/`, `Client/`, `Vendor/`, `Other/`. For a freelancer: `Client/`, `Vendor/`, `Other/`. For personal-only use: just `Other/`. Ask if unsure. Then create a People entry for each key person mentioned in Step 1 using the format defined in [[Knowledge/Jesse-Guidelines/People-KB-Guidelines]].

## Step 4: Populate Who I Am

Using the answers from Step 1, replace the placeholder text in the **Who I Am** section of JESSE.md with real information. This section should read as a factual reference, not a template.

## Step 5: First Briefing

Run a lightweight version of the Start of Day routine using whatever tools are now connected:
- If email is connected, scan the inbox and surface anything that looks urgent or time-sensitive.
- If calendar is connected, show today's schedule.
- If messaging is connected, check for recent messages needing responses.

Deliver a short briefing with what you found. End with: "Jesse is set up. Tomorrow, say 'good morning' and I'll run the full routine."

## After First Run

Once setup is complete, the Start of Day routine runs normally on all subsequent sessions. The first-run steps above will never execute again because Dashboard.md will exist.
