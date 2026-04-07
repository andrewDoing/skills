---
name: mempalace
description: Persistent AI memory using the MemPalace system. Mine conversations and projects into a searchable palace structure (wings, halls, rooms) with 30x AAAK compression. Search, store, and retrieve context across sessions. Use when the user asks to remember something, recall past decisions, search conversation history, or manage long-term memory.
---

# MemPalace Memory Skill

Give your AI persistent memory across sessions. MemPalace organizes knowledge into a palace structure (wings → halls → rooms → closets → drawers) with semantic search and 30x lossless AAAK compression. Everything runs locally via ChromaDB.

## Prerequisites

```bash
pip install mempalace
```

Requires Python 3.9+. No API keys needed for core functionality.

## Quick Start

Initialize a palace, mine some data, then search:

```bash
mempalace init ~/projects/myapp
mempalace mine ~/projects/myapp
mempalace search "why did we switch to GraphQL"
```

## Commands

| Command | What It Does |
|---------|-------------|
| `mempalace init <dir>` | Guided onboarding, generates wing config and AAAK bootstrap |
| `mempalace mine <dir>` | Mine project files (code, docs, notes) |
| `mempalace mine <dir> --mode convos` | Mine conversation exports (Claude, ChatGPT, Slack) |
| `mempalace mine <dir> --mode convos --extract general` | Auto-classify into decisions, milestones, problems |
| `mempalace search "query"` | Semantic search across all wings |
| `mempalace search "query" --wing myapp` | Search within a specific wing |
| `mempalace search "query" --room auth` | Search within a specific room |
| `mempalace wake-up` | Load L0 + L1 context (~170 tokens) for session start |
| `mempalace compress --wing myapp` | AAAK compress a wing's closets |
| `mempalace status` | Palace overview with wing/room counts |
| `mempalace split <dir>` | Split concatenated transcripts into per-session files |

## MCP Server

For MCP-compatible tools (Claude, Cursor), connect once:

```bash
claude mcp add mempalace -- python -m mempalace.mcp_server
```

This exposes 19 tools: `mempalace_search`, `mempalace_add_drawer`, `mempalace_kg_query`, `mempalace_traverse`, `mempalace_diary_write`, and more. The AI learns AAAK automatically from the `mempalace_status` response.

## Palace Structure

* **Wings** are people or projects. Each gets its own wing.
* **Halls** are memory types within a wing: `hall_facts`, `hall_events`, `hall_discoveries`, `hall_preferences`, `hall_advice`.
* **Rooms** are named topics (e.g., `auth-migration`, `graphql-switch`).
* **Tunnels** connect the same room across different wings automatically.
* **Closets** hold compressed summaries; **drawers** hold verbatim originals.

## Memory Stack

| Layer | Content | Size | When Loaded |
|-------|---------|------|-------------|
| L0 | Identity | ~50 tokens | Always |
| L1 | Critical facts (AAAK) | ~120 tokens | Always |
| L2 | Room recall | On demand | Topic comes up |
| L3 | Deep search | On demand | Explicit query |

Use `mempalace wake-up` to get L0 + L1 context for injection into any model's system prompt.

## When to Use

* User asks to remember something for next time.
* User asks "what did we decide about X?"
* User references past conversations or decisions.
* Starting a new session and need prior context.
* Mining a project or conversation archive for searchable memory.

## Python API

```python
from mempalace.searcher import search_memories

results = search_memories("auth decisions", palace_path="~/.mempalace/palace")
```

## Troubleshooting

| Issue | Fix |
|-------|-----|
| `mempalace: command not found` | Run `pip install mempalace` |
| Empty search results | Run `mempalace mine` on your data first |
| ChromaDB errors | Ensure `chromadb>=0.4.0` is installed |
| Large transcript files | Use `mempalace split <dir>` before mining |

---

Skill wrapping [MemPalace](https://github.com/milla-jovovich/mempalace) by milla-jovovich.
