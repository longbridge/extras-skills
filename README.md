# extras-skills

Non-trading, non-financial [Agent Skills](https://agentskills.io/specification) for Longbridge — quizzes, growth/engagement tools, and similar. Kept separate from [longbridge/skills](https://github.com/longbridge/skills), which covers securities/trading functionality and has its own CLI/MCP prerequisites that don't apply here.

These skills are **prompt-only**: no Longbridge CLI, MCP server, or login required. Just install and use.

---

## Install

### npx / bun (recommended — works with 50+ desktop AI tools)

```bash
# Global install, all agents it detects on your machine (lands in ~/.agents/skills/,
# symlinked into each tool's own skills directory — Claude Code, Cursor, Windsurf,
# Cline, Codex, Gemini CLI, OpenCode, Amp, Antigravity, and more)
npx skills add longbridge/extras-skills -g
bunx skills add longbridge/extras-skills -g

# Target specific desktop tools only
npx skills add longbridge/extras-skills -g --agent cursor windsurf claude-code

# Install just one skill
npx skills add longbridge/extras-skills -g --skill longbridge-lbti
```

> ⚠️ `npx skills` defaults to **project** scope. Without `-g` the skill lands in the current directory's `.agents/skills/` instead of the user-wide one — use `-g` unless you specifically want a project-local install.
>
> Run `npx skills add longbridge/extras-skills -g --agent '*'` to install to every desktop AI tool the installer supports in one shot, or `npx skills add longbridge/extras-skills -g -l` to just list what's available without installing.

### Claude Code plugin marketplace

```text
/plugin marketplace add longbridge/extras-skills
/plugin install extras@extras-skills
```

### Codex plugin marketplace

```bash
codex plugin marketplace add longbridge/extras-skills
codex plugin add extras@extras-skills
```

### Manual install (any Agent-Skills-compatible tool)

Skills are plain Markdown + JSON/assets, so any tool that reads the [Agent Skills spec](https://agentskills.io/specification) can use them without the installer — clone the repo and symlink the skill folder into that tool's skills directory:

```bash
git clone https://github.com/longbridge/extras-skills.git
ln -s "$PWD/extras-skills/skills/longbridge-lbti" ~/.claude/skills/longbridge-lbti   # Claude Code
ln -s "$PWD/extras-skills/skills/longbridge-lbti" ~/.gemini/skills/longbridge-lbti   # Gemini CLI
ln -s "$PWD/extras-skills/skills/longbridge-lbti" ~/.opencode/skills/longbridge-lbti # OpenCode
```

### Claude Desktop / claude.ai / mobile (true GUI apps, no CLI)

These aren't covered by the `npx skills` installer — they use their own **Settings → Skills** upload flow, which only accepts a `.zip` with the skill folder's contents at the zip **root** (not nested inside another folder).

**Prebuilt zip (no clone needed):**

```
https://github.com/longbridge/extras-skills/releases/latest/download/longbridge-lbti.zip
```

Download that, then in Claude Desktop (or claude.ai / the mobile app): **Settings → Skills → "+" → Create skill → upload `longbridge-lbti.zip`**. It syncs across desktop, browser, and mobile automatically since it's tied to your account, not the device. Toggle it on/off per-conversation like any other skill.

**Build it yourself instead** (e.g. testing an uncommitted change):

```bash
git clone https://github.com/longbridge/extras-skills.git
cd extras-skills/skills/longbridge-lbti
zip -r ../longbridge-lbti.zip .
```

> No other mainstream desktop chat app (ChatGPT Desktop, Gemini app, etc.) currently supports third-party Agent-Skills-spec uploads — only Claude's apps do as of this writing.
>
> **Maintainers**: the prebuilt zip is attached to a [GitHub Release](https://github.com/longbridge/extras-skills/releases), not committed to the repo — committing a 13MB binary alongside its source would double repo size and bloat further with every future edit, since git can't diff binaries efficiently. Cut a new release (tag `<skill>-vX.Y.Z`, attach a freshly built zip) whenever a GUI-app-relevant skill changes.

### Update

```bash
npx skills update -g
# or a single skill:
npx skills update longbridge-lbti -g
```

Claude Desktop / claude.ai skills don't auto-update — re-zip and re-upload (same steps as install) to replace an existing custom skill with a new version.

---

## What's inside

| Skill                                       | Description                                                                                                                       |
| -------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| [longbridge-lbti](./skills/longbridge-lbti) | 長橋 LBTI 投資風格測試 — a 5-question quiz that sorts users into 8 investing-personality types, returning a result card + QR code. |

Click a skill's name above to see what it can do.

---

## For developers

See [CLAUDE.md](./CLAUDE.md) for conventions when adding a new skill to this repo.

## License

MIT. See [LICENSE](./LICENSE).
