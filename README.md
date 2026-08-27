# Huddo Claude Code plugins

[![Validate plugins](https://github.com/isw-kudos/huddo-claude-plugin/actions/workflows/validate.yml/badge.svg?branch=main)](https://github.com/isw-kudos/huddo-claude-plugin/actions/workflows/validate.yml)
[![Workflow Security (zizmor)](https://github.com/isw-kudos/huddo-claude-plugin/actions/workflows/zizmor.yml/badge.svg?branch=main)](https://github.com/isw-kudos/huddo-claude-plugin/actions/workflows/zizmor.yml)
[![License: Apache-2.0](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](LICENSE)

The official [Claude Code](https://code.claude.com) plugin marketplace for
[Huddo Boards](https://boards.huddo.com).

## Plugins

| Plugin | What it does |
|--------|--------------|
| [huddo-boards](plugins/huddo-boards/) | Bundles the Huddo Boards MCP server (boards, lists, cards, assignments, comments) with a skill that teaches Claude the Boards data model. |

## Install

```bash
claude plugin marketplace add isw-kudos/huddo-claude-plugin
```

```bash
claude plugin install huddo-boards@huddo
```

Then run `/mcp` in Claude Code and sign in to `huddo-boards` (OAuth).

## Repository layout

```
.claude-plugin/marketplace.json   # the marketplace manifest (name: huddo)
plugins/<name>/                   # one directory per plugin
  .claude-plugin/plugin.json      # plugin manifest
  .mcp.json                       # bundled MCP server config
  skills/                         # skills shipped with the plugin
```

## Contributing

- Validate before pushing: `claude plugin validate .` and
  `claude plugin validate ./plugins/<name>` (CI runs the same checks).
- Bump the plugin's `version` in its `plugin.json` for any user-visible change,
  so installed copies pick up the update.
- Workflow changes are gated by [zizmor](https://docs.zizmor.sh); actions are
  pinned to commit SHAs and kept current by Renovate.
- Optional local hooks: `pre-commit install` (needs
  [pre-commit](https://pre-commit.com) and [uv](https://docs.astral.sh/uv/)).

> Merges to main require the green "Required Checks" context (repository ruleset).
