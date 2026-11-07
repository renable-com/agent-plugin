# Renable for Claude

This plugin connects Claude to your [Renable](https://renable.com) account and teaches it to move a product feed
setup from another feed tool to Renable.

It holds two things:

- **The Renable connector** (`.mcp.json`), Renable's MCP server at `https://api.renable.com/mcp`. Through it Claude
  works in your account as you: it reads your catalogs, rules, products and channels, creates projects, catalogs and
  channels, and edits and publishes when you ask. What it can and cannot do is described in
  [Connect Claude or another AI agent](https://renable.com/help/connecting-an-agent).
- **One skill, `renable-migration`**, which Claude loads when you want to move a feed setup to Renable or check one.
  The skill only points Claude at the server's migration guide and prompts. The guide's content comes from Renable's
  server, so it always matches the product as it works today. What a move looks like from your side is in
  [Move your feed setup from another feed tool](https://renable.com/help/move-from-another-feed-tool).

## Install

**In Claude on the web or in the desktop app:** add the plugin from Claude's plugin settings, either as a zip of this
folder or from the marketplace `renable-com/agent-plugin`.

**From Renable's plugin repository:** Renable's public repository `renable-com/agent-plugin` is a plugin
marketplace named `renable`. In Claude Code, run `claude plugin marketplace add renable-com/agent-plugin` and then
`claude plugin install renable@renable`.

**Claude Code, from this folder:** `claude --plugin-dir <this folder>` loads it for one session.

## Sign in

The connector signs in through Renable's own sign-in (OAuth). In Claude on the web or in the desktop app, connect
Renable when Claude asks, or from the plugin's settings. In Claude Code, run `/mcp` and follow the sign-in in your
browser.
Either way Renable opens in your browser: sign in if asked, check what Renable shows you, and click **Allow**.

You can disconnect at any time under **Connected agents** in your Renable account menu.

## What data it sends

The plugin itself holds no data and no credentials: it is Markdown and JSON. Everything Claude does in Renable goes
to `https://api.renable.com/mcp` as you, under the access you allowed. Renable never returns stored passwords or
API keys to the agent. During a move, Claude reads your old feed tool's setup and published feeds itself, in your
browser or from what you give it; Renable's server fetches nothing from the old tool.
