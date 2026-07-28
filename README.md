# Omnirank agent skill

Teach your AI agent to run SEO and AI-search visibility (AEO) for your
website through [Omnirank](https://omnirank.so) — the autopilot that
researches keywords, writes and publishes articles daily, tracks how ChatGPT,
Gemini and Google's AI answer your buyers' prompts, refreshes
underperformers, and proves results with Search Console data.

With this skill installed, your agent (Claude Code, Claude Desktop, ChatGPT,
Cursor, Hermes, OpenClaw) can:

- read your real Search Console + AI-visibility data and explain it
- write articles itself on your subscription — and grade them against
  Omnirank's five-pillar scorer until they're worth publishing
- trigger Omnirank's own generation pipeline, steer the calendar, publish
- do it all attributably: every agent action shows up in your workspace's
  activity feed

## Install

The skill is the manual; the hands are Omnirank's remote MCP server.

1. Get an Omnirank account at [app.omnirank.so](https://app.omnirank.so)
   (agent access is included in every plan).
2. Connect the MCP — e.g. for Claude Code:

   ```sh
   claude mcp add --transport http omnirank https://app.omnirank.so/mcp
   ```

3. Install this skill so your agent knows how to use it well:

   ```sh
   npx skills add omnirank-so/omnirank-skill
   ```

The OAuth consent binds the connection to one workspace (one website).

## What's in here

- [`SKILL.md`](SKILL.md) — the agent-facing manual: tools by job, the
  writing/scoring loop, method rules, safety rules.
- [`workspace/`](workspace/) — your agent's persistent working memory for
  this site. Yours, git-ignorable, survives skill updates.

## Links

- Agent guide (for LLM consumption): <https://omnirank.so/llm-info>
- MCP server card: <https://app.omnirank.so/.well-known/mcp/server-card.json>
- Product: <https://omnirank.so>
