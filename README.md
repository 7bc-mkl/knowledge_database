# knowledge_database

A knowledge base stored as plain Markdown files, with indexes designed to be navigated by an LLM agent through a dedicated skill — plus the Python tooling that keeps it up to date.

## What this is

The database is **the Markdown files themselves**. There is no server, no query engine, and no vector store in the critical path: knowledge lives in `.md` files on disk, and the indexes alongside them are what make that corpus navigable. The indexes are written for an agent reader rather than a human browser — their job is to let an agent find the relevant document, and the relevant part of it, in as few reads as possible, without loading the whole corpus into context.

A dedicated skill is the intended access path. Rather than teaching every agent how this corpus is laid out, the layout is documented once in a skill that knows how to enter at an index, narrow down, and read only what it needs.

Two halves, then:

1. **The database** — Markdown documents plus the indexes that make them navigable by an agent.
2. **The tools** — Python utilities that update the database: ingest a new document, convert it to Markdown, and refresh the indexes so the addition is discoverable.

## Ingestion

Documents arrive in whatever format they exist in. **PDF is the common case**, but Markdown, DOCX, and EPUB are all expected inputs, and the format list is open-ended by design. The tooling's job is to normalize each source into the Markdown corpus and then update the indexes — adding a document without reindexing it leaves it invisible to the agent, so the two steps belong to one operation.

## Design constraints

These follow from the purpose and are worth stating up front, because they rule out otherwise reasonable designs:

- **Plain text, diffable, greppable.** Markdown on disk under version control means the corpus is reviewable in a PR, searchable without special tooling, and readable by any agent with file access. Nothing should require a running service to read.
- **The indexes are a contract.** The navigating skill depends on their structure. Changing an index format is a breaking change for every agent that reads it, not an internal refactor.
- **Agent-first, not human-first.** Where index design trades human browsing convenience for agent navigation efficiency, agent navigation wins. Humans have `grep` and the file tree.

## Status

Early. The repository currently contains the agent delivery pipeline (see `SDLC.md` and `AGENTS.md`) and this description; the corpus layout, the index format, and the ingestion tools are still to be built. The Python toolchain the validation gate expects — `ruff` and `pytest` — is not yet installed either, so the gate will fail on a missing executable until the first substantive change lands.

## For agents

Read `AGENTS.md` first: it carries the task-routing table, the validation commands, and pointers to the delivery process. `SDLC.md` documents how work flows from ticket to merged PR.
