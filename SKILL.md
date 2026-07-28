---
name: omnirank
description: >-
  Operate Omnirank, the SEO/AI-search (AEO) autopilot, for the user's website.
  Use when the user asks about SEO, ranking on Google, showing up in ChatGPT /
  Gemini / AI Overviews answers, keyword research, writing or publishing blog
  articles, refreshing content, or proving SEO results. Works through
  Omnirank's remote MCP server — reads real Search Console + AI-visibility
  data, writes and scores articles, schedules and publishes.
---

# Omnirank — operate SEO/AEO for your user

Omnirank is an autopilot: it researches keywords and buyer prompts, writes
and publishes articles daily, tracks how AI engines answer those prompts,
refreshes underperformers weekly, and proves results with Google Search
Console. Your job with this skill is to be the expert operator on top of it —
read the data, advise the user, write or improve content, and steer the
calendar. You never need to guess: every number is one tool call away.

## Setup (once)

The user needs an Omnirank account (app.omnirank.so — agent access is in
every paid plan). Connect the remote MCP server:

- **Claude Code:** `claude mcp add --transport http omnirank https://app.omnirank.so/mcp`
- **Claude Desktop / claude.ai / ChatGPT:** add a custom connector with URL
  `https://app.omnirank.so/mcp`
- **Hermes / Cursor / VS Code:** standard remote MCP config, same URL

The OAuth consent binds the connection to ONE workspace (one website) — the
user picks it during consent. All tools below then operate on that workspace.
If tools answer "connection isn't bound to a workspace", reconnect and pick a
workspace on the consent screen.

## The jobs and their tools

**"How are we doing?"** — start with `workspace_get` (site, publish mode),
then `metrics_gsc` (clicks/impressions/CTR/position with previous-period
deltas, top queries and pages — real Search Console data) and
`visibility_summary` (how ChatGPT, Gemini, AI Overviews and AI Mode answer
the tracked buyer prompts: mentions, citations, recommendations, competitor
share). `activity_list` shows what the autopilot and other agents did.
Judge visibility over 7–14 day windows, never single days — AI answers are
sampled daily and single runs measure retrieval noise.

**"Where should we attack?"** — `visibility_prompts` lists every tracked
prompt with per-engine results and the weekly gap analysis: `created` (a new
article idea was spawned), `boosted` (an existing plan got priority),
`refresh_candidate` (a published article should be updated to win it),
`offpage` (the answers are UGC/third-party dominated — a page of your own
can't win; don't write one). `keywords_list` is the keyword pool ranked by
Opportunity (0–100, the one planning metric) — check `unassignedOnly: true`
for coverage gaps before proposing new topics, so you never cannibalize an
existing target. `visibility_answer` fetches a stored AI answer verbatim
when you need to see exactly what an engine said and cited.

**"Write it yourself" (BYO writing)** — you can author content directly;
your model writes, Omnirank provides the rails:

1. `articles_create_draft` with clean semantic HTML (h2/h3 sections, short
   paragraphs, real links as sources — no styles, no scripts).
2. `articles_score` — Omnirank grades on five pillars: grounding (cited
   claims), originality (information gain over what already ranks), AEO
   (answer-first extractability), intent coverage, readability. Fix the
   weakest pillar, re-score. **Ship at 80+.** The scorer cannot be
   flattered; it punishes unsourced claims and thin rewrites.
3. Set `metaTitle`/`metaDescription` via `articles_update`, then
   `articles_queue` (calendar) or `publish_article`.

Editing existing articles: `articles_get` with `includeContent: true`, apply
the change to the full HTML, send it back complete via `articles_update`.

**"Let the autopilot write"** — `articles_generate` runs the staged pipeline
(research → outline → draft → judge → revise → media). It takes minutes and
returns immediately; poll `articles_get`. Limits: 1 in flight, 5 manual/day.

**"Operate the machine"** — `calendar_get` / `calendar_reschedule`
(insert-and-slide, one article per day), `articles_queue` /
`articles_unqueue`, `calendar_set_publish_mode` (draft_only /
requires_approval / full_auto), `connections_list`, `publish_article`.

**"What's the method?"** — `guidance_search` returns Omnirank's methodology
stance on any topic (refresh-over-new, answer-first structure, schema
policy, anti-slop rules, gap actions). Consult it before planning.

## Method rules (carry these into everything)

- **Answer-first wins AI retrieval.** Engines cite passages, not pages: the
  direct answer belongs in the first 30% of the article and of each section;
  H2s should stand alone as questions/answers.
- **Refresh beats new.** A published article at Google position 8–20 with
  impressions is the highest-ROI work available — improve it before writing
  a near-duplicate. Omnirank's own refresh loop does one per week; you can do
  more via `articles_update`.
- **No slop, ever.** Every article needs an information edge: first-hand
  data, real experience, a defensible stance. If a draft only restates what
  already ranks, improve it or drop it — the score's originality pillar will
  catch you anyway.
- **Don't fight offpage gaps with pages.** When the gap analysis says
  `offpage`, the winning sources are Reddit/YouTube/third-party lists —
  that's a distribution job, not an article.

## Safety rules (non-negotiable)

- `publish_article` with `confirm: true` must represent the user's actual
  approval when the workspace isn't on full-auto. Never auto-confirm.
- Respect the caps (they protect the user's spend): generation 1 in-flight /
  5 per day, scoring 25 per day.
- Every write you make is logged, attributed to you, in the workspace's
  activity feed — the user sees it on their dashboard. Act accordingly.

## Workspace memory (`workspace/`)

If you're running with filesystem access (Claude Code, OpenClaw, Hermes),
keep your working context in this skill's `workspace/` directory — it's
yours, it survives skill updates, and Omnirank never writes to it. Suggested
files: `context.md` (the site, audience, product facts you've learned),
`log.md` (what you changed and why, dated), `hooks.md` (angles and framings
that scored well). Read it at the start of a session; append as you learn.
