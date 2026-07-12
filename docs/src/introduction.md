<div class="jesse-hero">
  <img src="images/jesse-logo.svg" alt="Jesse logo">
  <h1>Jesse</h1>
  <p class="jesse-tagline">An AI assistant built from a folder of text files. No product, no account, no lock-in.</p>
  <p class="jesse-subline">You own the files. The agent does the legwork. Nothing gets sent without your sign-off.</p>
</div>

<div class="jesse-cta">
  <a class="primary" href="getting-started.html">Set it up in 10 minutes</a>
  <a class="secondary" href="how-jesse-works.html">See how it works</a>
</div>

Your day is probably spread across a handful of apps -- email, calendar, Slack, a task list, for example, though everyone's mix is different. Whatever tools your agent can reach, Jesse gathers what's new, sorts it by what actually needs you, and hands you one briefing.

<p class="jesse-section-label">An example morning briefing</p>

<div class="jesse-briefing">
  <div class="titlebar"><span class="dots"><span></span><span></span><span></span></span> morning briefing</div>
  <pre>
📅 Calendar -- Today
- 10:00 AM -- 1:1 with Sarah (prep: review her project update from Friday)
- 2:00 PM  -- Team standup
&nbsp;
📧 Email -- 6 new
🔴 Needs response today: Client asking about timeline for deliverable
🟡 This week: Vendor sent contract for review
🟢 FYI: 4 automated notifications
&nbsp;
💬 Slack
- @mention in #projects -- someone asking about deploy schedule
- DM from manager with a question about budget
&nbsp;
🔔 Reminders
- Follow up with Alex on proposal (set 3 days ago)</pre>
</div>

You scan it, correct anything it got wrong, and start your day already caught up. Behind that briefing, Jesse has also updated your task list, prepared agendas for today's meetings, and acted on any notes you dropped in from your phone.

<p class="jesse-section-label">How it works</p>

<div class="jesse-diagram">
<svg viewBox="0 0 640 265" xmlns="http://www.w3.org/2000/svg" role="img" aria-label="Diagram: your tools feed an AI agent, guided by an instruction file, which reads and writes your vault and delivers a briefing to you">
  <defs>
    <linearGradient id="jgrad" x1="0" y1="0" x2="1" y2="1">
      <stop offset="0" stop-color="#6366F1"/>
      <stop offset="1" stop-color="#7C3AED"/>
    </linearGradient>
    <marker id="jarrow" markerWidth="8" markerHeight="8" refX="7" refY="4" orient="auto">
      <path d="M0,0 L8,4 L0,8 z" fill="currentColor" opacity="0.7"/>
    </marker>
  </defs>
  <g font-family="sans-serif" font-size="13" fill="currentColor">
    <rect x="245" y="10" width="150" height="46" rx="8" fill="none" stroke="url(#jgrad)" stroke-width="2"/>
    <text x="320" y="29" text-anchor="middle" font-weight="bold">Instruction file</text>
    <text x="320" y="46" text-anchor="middle" font-size="11" opacity="0.75">your rules and routines</text>
    <line x1="320" y1="56" x2="320" y2="92" stroke="currentColor" stroke-width="1.5" marker-end="url(#jarrow)" opacity="0.7"/>
    <rect x="15" y="97" width="150" height="72" rx="8" fill="none" stroke="currentColor" stroke-width="1.5" opacity="0.8"/>
    <text x="90" y="122" text-anchor="middle" font-weight="bold">Your tools</text>
    <text x="90" y="140" text-anchor="middle" font-size="11" opacity="0.75">email, calendar, chat,</text>
    <text x="90" y="155" text-anchor="middle" font-size="11" opacity="0.75">whatever you use</text>
    <rect x="245" y="97" width="150" height="72" rx="8" fill="url(#jgrad)"/>
    <text x="320" y="128" text-anchor="middle" font-weight="bold" fill="#fff">AI agent</text>
    <text x="320" y="146" text-anchor="middle" font-size="11" fill="#fff" opacity="0.9">any one you choose</text>
    <rect x="475" y="97" width="150" height="72" rx="8" fill="none" stroke="currentColor" stroke-width="1.5" opacity="0.8"/>
    <text x="550" y="122" text-anchor="middle" font-weight="bold">Your vault</text>
    <text x="550" y="140" text-anchor="middle" font-size="11" opacity="0.75">markdown files,</text>
    <text x="550" y="155" text-anchor="middle" font-size="11" opacity="0.75">Jesse's memory</text>
    <line x1="165" y1="133" x2="240" y2="133" stroke="currentColor" stroke-width="1.5" marker-end="url(#jarrow)" opacity="0.7"/>
    <text x="202" y="125" text-anchor="middle" font-size="10" opacity="0.7">reads</text>
    <line x1="400" y1="125" x2="470" y2="125" stroke="currentColor" stroke-width="1.5" marker-end="url(#jarrow)" opacity="0.7"/>
    <line x1="470" y1="145" x2="400" y2="145" stroke="currentColor" stroke-width="1.5" marker-end="url(#jarrow)" opacity="0.7"/>
    <text x="435" y="117" text-anchor="middle" font-size="10" opacity="0.7">writes</text>
    <text x="435" y="160" text-anchor="middle" font-size="10" opacity="0.7">remembers</text>
    <line x1="320" y1="169" x2="320" y2="205" stroke="currentColor" stroke-width="1.5" marker-end="url(#jarrow)" opacity="0.7"/>
    <text x="320" y="228" text-anchor="middle" font-weight="bold">You</text>
    <text x="320" y="246" text-anchor="middle" font-size="11" opacity="0.75">one briefing, drafts to review, nothing sent without approval</text>
  </g>
</svg>
</div>

Three parts, each one you can open and read:

<div class="jesse-grid">
  <div class="card">
    <span class="kicker">Memory</span>
    <h3>The vault</h3>
    <p>A folder of markdown files: your tasks, projects, people, notes. The agent starts every session fresh; the vault is what persists. You can read every bit of it in any editor.</p>
  </div>
  <div class="card">
    <span class="kicker">Process</span>
    <h3>The instruction file</h3>
    <p>One file (<code>JESSE.md</code>) that tells the agent who you are, what your rules are, and what to do each morning. Every time it gets something wrong, you tighten a rule.</p>
  </div>
  <div class="card">
    <span class="kicker">Engine</span>
    <h3>The agent</h3>
    <p>Any AI agent that can read files and call tools -- Claude Cowork and Claude Code work out of the box. Switch providers tomorrow and the vault comes with you.</p>
  </div>
</div>

<p class="jesse-section-label">Why people trust it</p>

<div class="jesse-grid">
  <div class="card">
    <h3>Drafts, never sends</h3>
    <p>Jesse writes replies and messages into a drafts folder and waits. Nothing leaves your outbox without your explicit approval.</p>
  </div>
  <div class="card">
    <h3>Plain files you own</h3>
    <p>No database, no export button, no subscription. Markdown in folders, versionable with git, readable in twenty years.</p>
  </div>
  <div class="card">
    <h3>You make the calls</h3>
    <p>Jesse surfaces, flags, and drafts. It shows its judgment calls so you can correct them. It doesn't decide strategy or commit you to anything.</p>
  </div>
</div>

## Not a Product

There's nothing to install, no account to create, nothing to pay for. Jesse is a method: a vault structure, an instruction file, and a set of patterns for working with an AI agent, refined through months of daily use. Copy the template, point your agent at it, and make it yours.

It was built to solve a real problem -- managing the daily overhead of a demanding workload without adding yet another app to the stack. Read the full story in the [blog post on Tag1's website](https://www.tag1.com/blog/building-my-ai-assistant/).

## Where to Go Next

- **[How Jesse Works](./how-jesse-works.md)** -- the mental model, a morning traced end to end, and the safety design.
- **[Quick Start](./getting-started.md)** -- copy the template and run your first session in about 10 minutes.
- **[Recipes](./recipes/index.md)** -- add capabilities piecemeal: email, Slack, meeting agendas, research, reminders, and more.
