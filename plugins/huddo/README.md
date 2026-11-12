# huddo plugin

Claude Code plugin for the Huddo suite (Wikis, Ideas, Spaces,
Discussions, Links, Search and notifications). It bundles the Huddo MCP
server that the `huddo` Helm chart deploys, plus a skill that teaches Claude
the Huddo data model and how to use the tools efficiently.

This is a separate server from Huddo Boards. For kanban boards, install the
[huddo-boards](../huddo-boards/) plugin.

## What you get

- **MCP server `huddo`**: the remote HTTP server of your install, with OAuth
  sign-in. Tools cover search, people, the inbox, links, wikis, ideas,
  discussions and spaces, plus cross-app tools (resolve a URL, comment on
  anything, related content, my week).
- **Skill `huddo:huddo`**: data-model and working-style guidance so Claude
  reads cheaply and writes safely.

## Install

From this repository's plugin marketplace:

```bash
claude plugin marketplace add isw-kudos/huddo-claude-plugin
```

```bash
claude plugin install huddo@huddo
```

Or try it locally without installing:

```bash
claude --plugin-dir ./plugins/huddo
```

## Configure the endpoint

Every Huddo install runs on its own host, so there is no default endpoint.
Set **Huddo MCP endpoint** when Claude Code prompts on plugin enable (or later
via `/plugin`). The value is the install's `HUDDO_URI` followed by `/mcp`, for
example `https://connections.example.com/huddo/mcp`. If the install sets
`MCP_PUBLIC_URL`, use that value instead.

The OAuth client id is fixed at `huddo-mcp`: Claude Code does not substitute
configuration values inside the `oauth` block, so it cannot be made a setting.
The install's user service must register the `huddo-mcp` OAuth client (a
public PKCE client with `http://localhost/callback` in its redirect URLs) in
`OAUTH_CLIENTS`.

## Authenticate

The server uses OAuth. In an interactive session, run `/mcp`, pick `huddo`,
and complete the browser sign-in. Until then the tools are listed but
unusable.

> If you previously added `huddo` as a user-level or project MCP server
> (`claude mcp list` shows it outside the plugin), remove it with
> `claude mcp remove huddo` to avoid two copies of the same server.

## Releasing changes

The plugin manifest is [.claude-plugin/plugin.json](.claude-plugin/plugin.json);
bump `version` when the skill or server config changes so installed copies
pick up updates. Keep the skill's tool map in sync with the tools the MCP
server registers (the `apps/mcp` service in the Huddo codebase).
