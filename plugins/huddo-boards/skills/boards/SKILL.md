---
name: boards
description: Work with Huddo Boards (kanban) through the huddo-boards MCP server — find boards, read lists and cards, create and move tasks, assign members, complete work, add comments. Use when the user mentions a Huddo board, a board card or task, or asks to file, move, assign, or complete work tracked in Huddo Boards.
---

# Huddo Boards

Huddo Boards is a collaborative task management platform. The `huddo-boards` MCP
server, bundled with this plugin, talks to `https://boards.api.huddo.com/mcp` and
authenticates with OAuth.

## Data model

- A **Board** contains **Lists** (columns). Lists contain **Cards** (tasks).
- Cards can have child cards (sub-tasks), comments, labels, assignments, and
  custom fields. A node's parent is the List (or Card) it sits under.
- A **Group** is a community or team. Boards can be shared with Groups, and
  Groups can be looked up by an external system's community/team id.
- Completing a task is a state change, not a deletion. Boards can be created
  from **templates**.

## Tool map

Read tools:

| Tool | Use for |
|------|---------|
| `getRecentBoards` / `getPinnedBoards` | The boards most relevant to the user right now. Prefer these over `getMyBoards`. |
| `getMyBoards` / `getMyTemplates` | Full list of the user's boards / templates. |
| `getGroup`, `searchGroups`, `getGroupBoards` | Find a team's boards. `getGroup` also resolves a community/team id from another system (provider + externalId). |
| `getBoardLists` | The Lists (columns) of a board, in board order. Start here when reading a board. |
| `getNodeChildren` | Children of one Board, List, or Card — e.g. the cards of a single column. |
| `getBoardCards` | Every card on a board across all lists. Use only when you truly need the whole board. |
| `searchTasks` | Find cards and lists across boards by query. |
| `getAssignedToMe` | The user's assigned todos. |
| `getBoardUsers`, `searchUsers`, `getBoardLabels` | Members, people lookup, and a board's labels. |

Write tools:

| Tool | Use for |
|------|---------|
| `createBoard`, `createList`, `createTask` | New board (optionally from a template), new column, new card under a List. |
| `createAttachment` | New card from a file or `.eml` email (email fields are parsed onto the card). |
| `updateBoard`, `updateTask`, `setCardFields` | Edit a board, edit a card, set custom field values. |
| `moveTask` | Move a card to a different List and/or Board. |
| `setTaskCompleted`, `completeTaskWithSubtasks` | Mark a task done (the latter includes all descendant sub-tasks). |
| `assignTask`, `unassignTask`, `setAssignmentCompleted` | Manage member assignments on a task. |
| `addBoardMember`, `addBoardLabel` | Grant a user or group access to a board; add a label. |
| `addComment` | Comment on a card or board. |

## Working style

- **Cheap-first.** If a board or list id is already known (from the conversation
  or a project doc), use it directly. For discovery, prefer `getRecentBoards`
  over `getMyBoards`, and `getBoardLists` + `getNodeChildren` on one column over
  `getBoardCards` on the whole board.
- **Write-through.** When you finish a piece of work tracked on a board, close
  the card in the same turn (`setTaskCompleted`). Comment the rationale when a
  card is resolved as won't-do rather than done.
- **Creating tasks:** name the card with a short symptom/action line. Put the
  detail (what broke, file refs, fix direction) in the description, not the title.
- Board column order is layout, not priority. Don't infer sequencing from it.

## Auth

The server uses OAuth. If tool calls fail with an auth error, ask the user to run
`/mcp` in an interactive Claude Code session and authenticate `huddo-boards`.
