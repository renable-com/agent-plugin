---
name: renable-migration
description: Move a product feed setup from another feed tool, or a hand-built feed, into Renable, or check that a move already made sends the same products and fields. Use when the person wants to switch, migrate or move their feeds, rules or channels to Renable from the tool they use today, or compare what Renable sends with the feed another tool publishes.
---

# Moving a feed setup to Renable

The Renable connector in this plugin serves the migration playbook. This skill only tells you where it is. Read the
playbook from the server every time, never from memory: it ships with the product, so it names the tools and the
steps exactly as they work today.

## Read the playbook

- **You can always fetch it yourself** with the `getPlaybook` tool. Its arguments list the playbooks and sections
  the server offers. Start with the overview, then read each section when you reach its step.
- **The person may attach a section** from the connector's menu in Claude, or mention it with `@` in Claude Code.
  Treat an attached section as the current text of that section.

| Section | Read it when |
|---|---|
| `renable://playbook/migration/overview` | before anything else |
| `renable://playbook/migration/inventory` | taking stock of the old setup, before you create anything |
| `renable://playbook/migration/order-of-operations` | building the setup in Renable, step by step |
| `renable://playbook/migration/verify` | checking products against the old feed, and before any cut-over |
| `renable://playbook/migration/no-equivalent` | something in the old setup has no Renable counterpart |

The playbook describes Renable, never the old tool. You learn the old setup from the old tool itself, as the
inventory section says.

## Prompts the person can start

The connector also offers prompts. The person picks one from the connector's menu in Claude, or from the `/` list in
Claude Code. Each one embeds the sections it needs.

- `inventory-current-setup`: taking stock of the setup in the old tool before anything is built.
- `verify-migration`: checking a catalog and a channel, optionally for named products, against the old feed.

Never tell the person to pick a prompt their menu does not show: read the sections with `getPlaybook` instead.

When the person asks for a move in their own words, you need no prompt: read the sections with `getPlaybook`.
Everything else, including what to refuse and how to report, is in the playbook.
