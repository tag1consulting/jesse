<div class="jl">
<section class="jl-band jl-band-white jl-hero">
<div class="jl-inner">
<div>
<p class="t1-eyebrow">Open source, from Tag1 Consulting</p>
<h1 id="jesse">An AI assistant built from a folder of text files</h1>
<p class="jl-lede">Jesse pairs an AI agent with your notes, tasks and contacts in markdown, and one instruction file that tells the agent how you work. Say "good morning" and it works through your notes and whatever tools you've connected, then hands you a briefing. I'm Jeremy Andrews, and I built it to run my own day at Tag1 and at&nbsp;home.</p>
<div class="jl-actions">
<a class="jl-btn" href="getting-started.html"><span>Quick start</span><span aria-hidden="true"></span></a>
<a class="jl-link" href="how-jesse-works.html">How Jesse works</a>
</div>
<p class="jl-note">Jesse is free and MIT licensed, and <a class="jl-link" href="https://github.com/tag1consulting/jesse/tree/main/template">the template is on GitHub</a>. You'll need an AI agent that can work with files on your computer and call tools, such as Claude in Cowork or Claude Code, and you pay that agent's provider.</p>
</div>
<div class="jl-hero-art t1-frame"><img src="images/jesse-logo.svg" alt="Jesse logo, a lowercase j followed by a &gt; prompt"></div>
</div>
</section>
<section class="jl-band jl-band-navy">
<div class="jl-inner jl-works t1-frame">
<div class="jl-head jl-head-center">
<p class="t1-eyebrow">The parts</p>
<h2 id="the-parts">What Jesse is made of</h2>
</div>
<div class="jl-cols">
<div class="jl-col">
<h3 id="the-vault">The vault</h3>
<p>The vault is a folder of markdown files holding your task list (<code>Dashboard.md</code>), today's plan (<code>Today.md</code>), projects, people and reminders. At the start of every session the agent reads the task list and today's plan to see where things stand; it doesn't need to remember anything from the last one.</p>
</div>
<div class="jl-col">
<h3 id="the-instruction-file">The instruction file</h3>
<p>The instruction file, <code>JESSE.md</code>, is where the magic happens. It tells the agent who you are, what your priorities look like, what tools are connected, and exactly what to do when you start a session. When you correct the agent, it fixes the rule in <code>JESSE.md</code> (or in a guideline file <code>JESSE.md</code> points to), and the next session reads the fixed version.</p>
</div>
<div class="jl-col">
<h3 id="the-agent">The agent</h3>
<p>I use Claude in Cowork and in Claude Code. If you use Claude too, rename <code>JESSE.md</code> to <code>CLAUDE.md</code>. Other agents should work after some changes to the instruction file, and if you switch, the vault comes with you.</p>
</div>
</div>
</div>
</section>
<section class="jl-band jl-band-blue">
<div class="jl-inner jl-brief-wrap">
<div class="jl-head">
<p class="t1-eyebrow">Start of day</p>
<h2 id="start-of-day">What a morning looks like</h2>
<p>Jesse reads the notes you've dropped from your phone or desktop into the vault's <code>Inbox/</code> folder, plus whatever you've connected (email, calendar, Slack, WhatsApp). It does the research or drafting your notes asked for, rebuilds <code>Today.md</code> and writes agendas for today's meetings.</p>
<p>The briefing includes Jesse's judgment calls (what it decided, and what it chose not to do), so you can catch a bad decision early.</p>
</div>
<div>
<div class="jl-window">
<div class="jl-window-bar"><span>Morning briefing</span><span>Tuesday</span></div>
<div class="jl-brief">
<p class="jl-brief-label">Calendar</p>
<dl>
<dt>10:00</dt><dd>1:1 with Sarah. Prep notes from her Friday update are in the agenda.</dd>
<dt>14:00</dt><dd>Team standup. Agenda ready.</dd>
</dl>
<p class="jl-brief-label">Email, 6 new</p>
<dl>
<dt><span class="jl-flag jl-flag-now"></span>Urgent</dt><dd>Client asking for a delivery date</dd>
<dt><span class="jl-flag jl-flag-week"></span>This Week</dt><dd>Vendor contract to review</dd>
<dt><span class="jl-flag jl-flag-fyi"></span>FYI</dt><dd>4 automated notifications</dd>
</dl>
<p class="jl-brief-label">From your notes</p>
<dl>
<dt>Done</dt><dd>"Research office insurance renewals": the report is in <code>Projects/Research/</code></dd>
</dl>
<div class="jl-judgment"><p><strong>Judgment calls:</strong> Put the vendor contract under This Week, not Urgent; they want it back by Friday. Left the 4 notifications off <code>Dashboard.md</code>. Tell me if either is wrong.</p></div>
</div>
</div>
<p class="jl-caption">This is a made-up example; yours will look like what your instruction file asks for.</p>
</div>
</div>
</section>
<section class="jl-band jl-band-white">
<div class="jl-inner">
<div class="jl-head">
<p class="t1-eyebrow">Ground rules</p>
<h2 id="ground-rules">The decisions stay yours</h2>
</div>
<div class="jl-cards">
<div class="jl-card t1-frame">
<h3 id="your-notes-give-the-orders">Your notes give the orders</h3>
<p>Notes you put in <code>Inbox/</code> are instructions to the agent. Email and chat are only information, so if a message wants something done, the agent checks with you first. Replies and agendas it writes go in <code>Projects/drafts/</code> for you to review and edit. Whether it can send anything itself depends on the tools you connect and your agent's approval settings.</p>
</div>
<div class="jl-card t1-frame">
<h3 id="plain-files">Plain files</h3>
<p>Your notes, tasks and rules are plain markdown that any editor can open (I&nbsp;use Obsidian). If you keep the vault in git, every change the agent makes to it shows up as a diff you can review or roll back.</p>
</div>
<div class="jl-card t1-frame">
<h3 id="tracked">Tracked until you sign off on it</h3>
<p>Anything from your <code>Inbox/</code> notes, email or chat that needs action goes on <code>Dashboard.md</code> with the date it was added, and stays there until you say it's done.</p>
</div>
</div>
</div>
</section>
<section class="jl-band jl-band-cream">
<div class="jl-inner jl-story">
<div>
<h2 class="t1-eyebrow" id="why-i-built-it">Why I built it</h2>
<blockquote>
<p>I run Tag1, help where I can at my wife's vineyard, and have two young kids, chickens, dogs, cats and a house in Tuscany that always needs something fixed. My TODO list is relentless.</p>
<p>I wanted one place to look each morning instead of dozens, so I could get through the administrative overhead and then close the laptop. I still wanted to make the decisions myself. Jesse is what came out of that, and it keeps changing as I find better ways to do things.</p>
<p class="jl-sig">Jeremy Andrews<span>Founding Partner and CEO, Tag1 Consulting</span></p>
</blockquote>
</div>
<div class="jl-reads">
<h3 class="t1-eyebrow" id="more-on-the-tag1-blog">More on the Tag1 blog</h3>
<a class="jl-read t1-frame" href="https://www.tag1.com/blog/building-my-ai-assistant/"><strong>Building My AI Assistant</strong><span>My March 2026 post: how Jesse started and what goes in the instruction file.</span></a>
<a class="jl-read t1-frame" href="https://www.tag1.com/blog/jesse-missing-user-manual/"><strong>Jesse: The Missing User Manual</strong><span>Tag1's Hank VanZile on what it took to make Jesse work for him.</span></a>
</div>
</div>
</section>
<section class="jl-band jl-band-purple">
<div class="jl-inner jl-cta t1-frame">
<div>
<h2 id="set-up-your-own">Set up your own</h2>
<p>Copy the template folder and point your agent at it, then keep adjusting the instruction file until Jesse works the way you do. The quick start takes about ten minutes. Connecting your calendar and other tools takes longer, and how you do it depends on your agent.</p>
</div>
<div class="jl-cta-side">
<div class="jl-actions">
<a class="jl-btn" href="getting-started.html"><span>Quick start</span><span aria-hidden="true"></span></a>
<a class="jl-link" href="https://github.com/tag1consulting/jesse/tree/main/template">Template on GitHub</a>
</div>
<p class="jl-more">Then add the <a class="jl-link" href="recipes/index.html">recipes</a> you need: step&#8209;by&#8209;step add&#8209;ons for things like email, Slack, WhatsApp, quick notes from an iPhone, birthday reminders and vault search.</p>
</div>
</div>
</section>
<footer class="jl-foot">
<div class="jl-inner">
<p>Jesse is an open source project from <a href="https://www.tag1.com/">Tag1&nbsp;Consulting</a>.</p>
<p><a href="https://github.com/tag1consulting/jesse/blob/main/LICENSE">MIT license</a> · <a href="https://github.com/tag1consulting/jesse">Source on GitHub</a></p>
</div>
</footer>
</div>
