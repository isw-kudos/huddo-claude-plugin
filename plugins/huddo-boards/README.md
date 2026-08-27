# huddo-boards plugin

Claude Code plugin for [Huddo Boards](https://boards.huddo.com). It bundles the
Boards MCP server (`https://boards.api.huddo.com/mcp`) plus a skill that teaches
Claude the Boards data model and how to use the tools efficiently.

## What you get

- **MCP server `huddo-boards`** — the remote HTTP server, with OAuth sign-in.
  Tools cover boards, lists, cards, search, assignments, labels, custom fields,
  comments, and attachments.
- **Skill `huddo-boards:boards`** — data-model and working-style guidance so
  Claude reads boards cheaply and writes results back through.

## Install

From this repository's plugin marketplace:

```bash
claude plugin marketplace add isw-kudos/huddo-claude-plugin
```

```bash
claude plugin install huddo-boards@huddo
```

Or try it locally without installing:

```bash
claude --plugin-dir ./plugins/huddo-boards
```

## Self-hosted Boards deployments

The MCP endpoint is a plugin setting (`userConfig`), defaulting to Huddo cloud
(`https://boards.api.huddo.com/mcp`). With the default the plugin works with
zero configuration. If your organisation runs its own Boards deployment, set
**Boards MCP endpoint** when Claude Code prompts on plugin enable (or later via
`/plugin`).

The OAuth client id is fixed at `boards-mcp`: Claude Code does not substitute
configuration values inside the `oauth` block, so it cannot be made a setting.
A self-hosted deployment must register the `boards-mcp` OAuth client (a public
PKCE client with `http://localhost:*/callback` redirects) in its user service.

## Authenticate

The server uses OAuth. In an interactive session, run `/mcp`, pick
`huddo-boards`, and complete the browser sign-in. Until then the tools are
listed but unusable.

> If you previously added `huddo-boards` as a user-level MCP server
> (`claude mcp list` shows it outside the plugin), remove it with
> `claude mcp remove huddo-boards` to avoid two copies of the same server.

## Releasing changes

The plugin manifest is [.claude-plugin/plugin.json](.claude-plugin/plugin.json);
bump `version` when the skill or server config changes so installed copies
pick up updates. Keep the skill's tool map in sync with the tools the Boards
MCP server registers (the `apps/mcp` service in the Boards codebase).
