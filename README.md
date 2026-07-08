# extras-skills

Non-trading, non-financial [Agent Skills](https://agentskills.io/specification) for Longbridge — quizzes, growth/engagement tools, and similar. Kept separate from [longbridge/skills](https://github.com/longbridge/skills), which covers securities/trading functionality and has its own CLI/MCP prerequisites that don't apply here.

These skills are **prompt-only**: no Longbridge CLI, MCP server, or login required. Just install and use.

---

## Install

### npx / bun (recommended)

```bash
# Global install (lands in ~/.claude/skills/, reachable from any project)
npx skills add longbridge/extras-skills -g
bunx skills add longbridge/extras-skills -g

# Install just one skill
npx skills add longbridge/extras-skills -g --skill longbridge-lbti
```

> ⚠️ `npx skills` defaults to **project** scope. Without `-g` the skill lands in the current directory's `.claude/skills/` instead of the user-wide one — use `-g` unless you specifically want a project-local install.

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

### Update

```bash
npx skills update -g
# or a single skill:
npx skills update longbridge-lbti -g
```

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
