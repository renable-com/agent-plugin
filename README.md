# Renable plugins for AI agents

Each folder here is one AI agent's plugin for [Renable](https://renable.com). A plugin connects the agent to your
Renable account through Renable's MCP server, so the agent can read and build catalogs, rules, channels and product
sets as you, and teaches it Renable's own workflows.

| Folder | Agent | How to install |
|---|---|---|
| [`claude/`](claude/) | Claude: Claude Code, and Claude in the desktop app and on the web | [`claude/README.md`](claude/README.md) |

This folder is also a Claude Code plugin marketplace named `renable` (`.claude-plugin/marketplace.json`), so Claude
Code can install the Claude plugin with `claude plugin install renable@renable` once the marketplace is added.

## The rules every folder follows

- **One folder per agent product**, named after the agent (`claude/`; a `cursor/` or `codex/` folder would sit
  beside it). A folder holds only what that agent's plugin format needs.
- **Every folder connects to the same server, `https://api.renable.com/mcp`**, and to nothing else. Sign-in is
  Renable's own OAuth; no folder holds a credential.
- **No folder copies Renable's playbook.** The migration guide and prompts are served by the MCP server, so they
  always match the product. A skill or instruction file names the server's guide sections, prompts and tools and
  never repeats their text.
- **Every folder is pinned by a test** in Renable's source repository, which fails when a folder names a guide
  section, prompt or tool the server does not serve, copies a playbook sentence, or points at another address. A
  new folder lands together with its pins; the test refuses a folder it does not know.
- **Edits are made in Renable's source repository, never here.** The public repository `renable-com/agent-plugin`
  is a verbatim copy of this folder ([`MIRROR.md`](MIRROR.md)).
