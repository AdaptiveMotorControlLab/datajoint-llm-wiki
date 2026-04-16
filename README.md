# DataJoint Wiki + Coding Assistant

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

A pre-populated wiki covering all [DataJoint 2.x](https://datajoint.com) documentation, paired
with a coding assistant skill that grounds its answers in the wiki rather than guessing. Novel
Q&A grows the wiki as shareable syntheses.

> Based on [LLM Wiki Agent](https://github.com/SamurAIGPT/llm-wiki-agent) — a general-purpose
> agent skill for building persistent, interlinked wikis from source documents.

---

## What's in the box

**Pre-populated wiki** — 54 source pages, 12 concept pages, and growing syntheses covering the
full DataJoint 2.x documentation: table tiers, query algebra, computation model, object storage,
migration patterns, configuration, error reference, and more.

```
wiki/
├── index.md       catalog of all pages
├── overview.md    living synthesis across all sources
├── sources/       54 pages — one per DataJoint doc
├── concepts/      12 pages — deep dives (TableTiers, QueryAlgebra, EntityIntegrity, …)
├── entities/      Schema, dj.Instance
└── syntheses/     saved Q&A — grows with use
```

**Raw source docs** — `raw/` is not shipped with this repo. It is populated on demand from
[datajoint/datajoint-docs src/](https://github.com/datajoint/datajoint-docs/tree/main/src)
by running `/wiki-update-docs`. The wiki itself is fully self-contained without it.

**Coding assistant skill** (`skills/datajoint-wiki/SKILL.md`) — answers DataJoint questions
grounded in the wiki. Knows the fundamentals without reading a file; consults the wiki for
nuanced design questions, API details, migration patterns, and errors. Offers to save non-obvious
answers as syntheses so the wiki grows over time.

**Multi-tool adapters** — the same wiki and core concepts are exposed to:

| Tool | File |
|------|------|
| Claude Code (skill, auto-trigger) | `skills/datajoint-wiki/SKILL.md` |
| OpenAI Codex | `AGENTS.md` |
| GitHub Copilot | `.github/copilot-instructions.md` |
| Cursor | `.cursor/rules/datajoint.mdc` |

---

## Install

No Python or API key needed for the wiki and skill.

### Global (Claude Code — auto-triggers on DataJoint work)

```bash
git clone <repo-url> ~/.claude/skills/datajoint-wiki
```

Claude Code discovers `SKILL.md` under `~/.claude/skills/` and automatically invokes the skill
whenever you are writing table definitions, `make()` methods, queries, or schema design. No
slash command needed.

### Project-scoped (Claude Code — explicit `/datajoint-wiki` only)

```bash
git clone <repo-url> .datajoint-wiki
cp .datajoint-wiki/SKILL.md .claude/commands/datajoint-wiki.md
```

Or as a submodule:
```bash
git submodule add <repo-url> .datajoint-wiki
cp .datajoint-wiki/SKILL.md .claude/commands/datajoint-wiki.md
```

Project-scoped installs require an explicit `/datajoint-wiki` invocation. Use the global install
if you want the assistant to engage automatically.

### Other tools (Codex, Copilot, Cursor)

Clone the repo anywhere and open it as your project. The adapter files (`AGENTS.md`,
`.github/copilot-instructions.md`, `.cursor/rules/datajoint.mdc`) are picked up automatically
by their respective tools.

---

## Usage

### Coding assistant (Claude Code) — global install

With a **global install** (`~/.claude/skills/datajoint-wiki`) the skill auto-triggers. Open any
project in Claude Code and start working on DataJoint code — the skill engages automatically
when Claude detects you are writing table definitions, `make()` methods, queries, or schema
design. No slash command needed.

You can also invoke it explicitly at any time:

```
/datajoint-wiki how should I design the PK for a session table?
/datajoint-wiki my Computed table needs a new primary key attribute — what's the migration path?
/datajoint-wiki when should I use a Part table vs a downstream Computed table?
```

When the skill runs it will:
1. Answer basic DataJoint questions immediately from baked-in knowledge (no file reads)
2. For nuanced questions, locate the wiki and read the relevant pages
3. Cite sources inline with `[[PageName]]` links
4. Offer to save non-obvious answers as a synthesis so the wiki grows

### Coding assistant (Claude Code) — project-scoped install

With a **project-scoped install** (`.claude/commands/datajoint-wiki.md`) the skill does not
auto-trigger. Invoke it explicitly with:

```
/datajoint-wiki <your question or task>
```

### Wiki commands

These work in both install modes and inside the repo itself:

```
/wiki-query how does populate() determine key_source?
/wiki-update-docs
/wiki-ingest raw/my-new-doc.md
/wiki-lint
```

---

## Growing the wiki

When the skill saves a synthesis, it prompts you with the contribution command:

```bash
# Global install:
cd ~/.claude/skills/datajoint-wiki

# Project-scoped install:
cd .datajoint-wiki

git add wiki/syntheses/ wiki/log.md wiki/index.md
git commit -m "synthesis: <title>"
git push && gh pr create
```

PRing syntheses back upstream makes the wiki richer for everyone.

---

## Tips

- The wiki is a git repo — full version history of every synthesis and ingest
- `/wiki-lint` catches orphan pages, broken links, and data gaps
- Standalone Python scripts in `tools/` (ingest, query, lint, refresh) work without a coding
  agent — requires `ANTHROPIC_API_KEY` and `pip install litellm`

---

## License

MIT — see [LICENSE](LICENSE) for details.
