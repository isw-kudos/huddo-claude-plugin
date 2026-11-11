---
name: collab
description: Work with the Huddo collab apps (Wikis, Ideas, Spaces, Discussions, Links, Search, notifications) through the huddo MCP server: search content, read and write wiki pages, submit and vote on ideas, comment on anything, find spaces and people, triage the inbox. Use when the user mentions a Huddo wiki, page, idea lab, idea, space, saved link, or a Huddo URL, or asks what changed or what needs their attention in Huddo. Not for Huddo Boards (kanban); that is the huddo-boards plugin.
---

# Huddo collab

The Huddo collab suite is Wikis, Ideas, Spaces, Discussions, Links, Search and
the notification inbox, usually installed alongside HCL Connections. The
`huddo` MCP server, bundled with this plugin, talks to the configured install
and authenticates with OAuth. Every call runs as the signed-in user, so the
user's own access rules apply: a tool can never see or change more than the
user can in the UI.

Tools are named `<app>_<action>`. An install registers only the apps it runs
and the tool sets its admin enables, so some tools below can be absent. Call
`huddo_whoami` when unsure which apps are installed.

## Data model

- A **Space** is a team area (a group). It can own **Wikis**, idea **Labs**
  and whiteboards, and has an activity feed.
- A **Wiki** holds a tree of **Pages**. A page has a Markdown body,
  properties (key/value), children, revisions and comment threads. A wiki
  with approvals turned on keeps new revisions unpublished until approved.
- A **Lab** asks a question; **Ideas** are submitted to it and voted on
  under the lab's voting type and stage.
- **Discussions** are the comment threads on pages, ideas and space feeds.
- **Links** are the user's bookmarks (private or shared with the org).
- Any Huddo URL names one object. `huddo_resolve` turns a URL into
  `{app, type, ids, title, url}`, and every tool that takes a `url` goes
  through the same resolver.

## Tool map

Orientation and cross-app:

| Tool | Use for |
|------|---------|
| `huddo_whoami` | The user, org, role and the installed apps with their URLs. |
| `huddo_resolve` | What a pasted Huddo URL is. Start here when the user gives a link. |
| `huddo_recent`, `huddo_digest` | What the user touched lately; "my week" (unread inbox, changes in their spaces, lab movement). |
| `search_content` | Permission-aware search across apps, with filters by app, type, label, creator and container. |
| `huddo_get_comments`, `huddo_comment` | Read or post comments on a page, idea or space feed by URL. |
| `huddo_related`, `huddo_link`, `huddo_unlink` | Links, backlinks and related content; add or remove a manual link. |
| `huddo_upload_file`, `huddo_delete_file` | Attach a file or image to a page, idea or space; delete one. |
| `ideas_to_page` | Turn an idea into a wiki page, linked both ways. |

People and inbox: `core_find_people`, `core_get_people`, `core_org_members`,
`notifications_inbox`, `notifications_mark_read`,
`notifications_get_preferences`, `notifications_set_preferences`.

Wikis:

| Tool | Use for |
|------|---------|
| `wikis_list_wikis`, `wikis_get_wiki` | Find a wiki; its overview and page outline. |
| `wikis_find_pages` | Pages by title. For body text use `search_content`. |
| `wikis_get_page`, `wikis_get_page_history` | A page as Markdown; revisions and diffs. |
| `wikis_recent_changes`, `wikis_property_report`, `wikis_list_templates` | What changed since a date; structured property records; templates. |
| `wikis_create_wiki`, `wikis_create_page`, `wikis_import` | New wiki; new page from Markdown; import Markdown or a web page. |
| `wikis_update_page`, `wikis_update_page_details`, `wikis_move_page` | Edit body (replace, or one section by heading); title and excerpt; re-parent. |
| `wikis_review_queue`, `wikis_review_action` | Pending approvals and flagged pages; approve, request changes, publish. |
| `wikis_set_reaction` | Like or follow a page; follow a wiki. |

Ideas: `ideas_find_labs`, `ideas_get_lab`, `ideas_lab_summary`,
`ideas_find_ideas`, `ideas_get_idea`, `ideas_create_lab`, `ideas_submit_idea`,
`ideas_update_idea`, `ideas_vote`, `ideas_follow`.

Spaces: `spaces_find`, `spaces_get_space`, `spaces_join`, `spaces_star`,
`spaces_create_space`.

Links: `links_find`, `links_get_link`, `links_save`, `links_update_bookmark`,
`links_preview`.

Discussions (the by-id tools behind `huddo_comment`): `discussions_get_thread`,
`discussions_reply`, `discussions_edit_message`, `discussions_set` (react,
mute, resolve).

Deletes (`wikis_delete_page`, `wikis_delete_wiki`, `ideas_delete_idea`,
`ideas_delete_lab`, `spaces_delete_space`, `links_delete_bookmark`,
`discussions_delete_message`) need a confirm field that matches the object's
title, name or URL.

## Working style

- **URL first.** When the user pastes a Huddo URL, pass it to the tool that
  takes a `url` (or `huddo_resolve`) rather than searching for the object.
- **Cheap-first.** Outputs are trimmed: lists default to 20 items, bodies are
  capped by `maxChars` and say `truncated: true` when cut. Ask for more only
  when the task needs it.
- **Read before overwrite.** `wikis_update_page` takes `expectedSequence`
  from the page you read; prefer a section splice over replacing the whole
  body. Warn the user that an open editor session on the same page
  overwrites on save.
- **Approvals.** On a wiki with approvals, a new page or revision stays
  unpublished until approved. Report the `next` field from a create result
  so the user knows whether the page is live.
- **Confirm destructive calls.** Ask before any delete, publish or full-body
  overwrite.
- **Images.** Upload with `huddo_upload_file` and use the Markdown it returns;
  `data:` images in Markdown are refused.

## Auth

The server uses OAuth. If tool calls fail with an auth error, ask the user to
run `/mcp` in an interactive Claude Code session and authenticate `huddo`.
