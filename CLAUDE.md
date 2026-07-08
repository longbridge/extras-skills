# CLAUDE.md — repo conventions for Claude Code

This file briefs Claude Code when working **inside this repo** (adding new skills, editing existing ones). Not for end-users — they install via the paths in [README.md](./README.md).

## What this repo is

Non-trading, non-financial [Agent Skills](https://agentskills.io/specification) for Longbridge — quizzes, growth/engagement tools, and similar. Sibling repo to [longbridge/skills](https://github.com/longbridge/skills), which covers securities/trading and has its own CLI/MCP prerequisites. Skills here are **prompt-only** unless a skill has a clear runtime need (image/asset lookup, DOCX generation, etc.) — no Longbridge CLI or MCP dependency by default.

## Layout

```
extras-skills/
├── .claude-plugin/marketplace.json    # Claude Code plugin marketplace entry
├── .codex-plugin/plugin.json          # Codex plugin entry
├── skills/
│   └── <slug>/
│       ├── SKILL.md                   # required
│       ├── references/                # optional — on-demand detail
│       ├── assets/                    # optional — images, static files the skill returns as-is
│       └── scripts/                   # optional — helpers when there's a clear runtime need
├── CLAUDE.md                          # this file
├── README.md
└── LICENSE                            # MIT
```

## Conventions for adding or editing a skill

### 1. Slug + directory

- Directory name must match `^[a-z0-9]+(-[a-z0-9]+)*$` (lowercase ASCII + hyphens), and must equal the `name:` value in SKILL.md frontmatter.
- **Slugs are immutable once published.** Installers (`npx skills update`) match by slug — renaming orphans the old install on every user's machine instead of upgrading it. Reorganize content under the existing slug; treat a genuinely unavoidable rename as a breaking change.

### 2. Frontmatter

Required: `name`, `description`. Recommended: `license: MIT`, `metadata` (author, version).

`description` must be **≤ 1024 characters** and must include a `Triggers:`-style list of multilingual keywords/phrases — that list is the only thing used to decide whether the skill activates.

### 3. Language coverage

Match whatever languages the skill's own content is written in (this repo doesn't standardize on a single language the way `longbridge/skills` does — check each skill's audience). If a skill responds in a fixed language regardless of input, say so explicitly in the SKILL.md body so behavior is predictable.

### 4. Assets returned as-is

If a skill ships pre-made result assets (images, cards, etc.) meant to be returned unmodified, say so explicitly in SKILL.md: fetch and return the file as-is, never regenerate or redraw it, so every user gets output identical to the design.

### 5. Mutating operations

Any skill that writes/changes state on the user's behalf must use a two-step preview + confirm protocol, in a separate turn from execution — never combine preview and execute in one response.

### 6. Fallback behavior

If a skill's primary output format may not be supported by the client (e.g. images), document a text-only fallback in SKILL.md so the skill degrades gracefully instead of erroring or going silent.

## Quick checklist for adding a new skill

1. Create `skills/<slug>/` with `SKILL.md`.
2. Frontmatter: `name`, `description` (≤1024 chars, with triggers), `license: MIT`, `metadata`.
3. Body: what the skill does, when to use it, the workflow, and a fallback if its primary output can't be delivered.
4. Update README.md's "What's inside" table.
5. Marketplace files (`.claude-plugin/marketplace.json`, `.codex-plugin/plugin.json`) auto-discover skills under `./skills/` — no per-skill edit needed there.

## License

MIT. See [LICENSE](./LICENSE).
