# ai-agent-plugin

**Dreamon's collection of agent skills**, packaged for Claude Code, Codex, and
other agents that read the [Agent Skills](https://agentskills.io) format.

## Skills

| Skill | What it does |
|---|---|
| [macos-app-screenshot](plugins/macos-app-screenshot/skills/macos-app-screenshot/) | Capture a retina, shadow-free screenshot of any macOS app's window **by app name** — no window-id hunting. |

More skills land here as they are built; installing the plugin installs all of
them.

```bash
scripts/shot <AppName> [output.png]
# shot Deckhand                       → ~/Desktop/Deckhand-<timestamp>.png
# shot preview ~/shots/preview.png    # partial names work
```

2000×1280-class PNG at 2× retina, drop shadow omitted. Requires **Screen
Recording** permission for the terminal running the agent, and a visible (not
minimized) window.

## Install

**npx (auto-detects Claude Code, Codex, Cursor, and 20+ agents):**

```bash
npx skills add Dreamon-im/ai-agent-plugin/plugins/macos-app-screenshot/skills/macos-app-screenshot
```

**Claude Code plugin:**

```
/plugin marketplace add Dreamon-im/ai-agent-plugin
/plugin install ai-agent-plugin@dreamon-plugins
```

**OpenAI Codex plugin:** add this repo as a plugin marketplace
(`.agents/plugins/marketplace.json`), or copy the skill folder into
`~/.codex/skills/`.

**Any agent, by hand:** copy or symlink the skill folder you want into that
agent's skills directory (e.g. `~/.claude/skills/`, `~/.codex/skills/`,
`~/.agents/skills/`).

## Structure

```
.agents/plugins/marketplace.json         OpenAI/Codex marketplace manifest
.claude-plugin/marketplace.json          Claude Code marketplace manifest
plugins/
└── macos-app-screenshot/
    ├── .codex-plugin/plugin.json        OpenAI plugin manifest
    ├── .claude-plugin/plugin.json       Claude Code plugin manifest
    └── skills/
        └── macos-app-screenshot/
            ├── SKILL.md                 instructions + gotchas
            └── scripts/shot             the capture script
```

## Adding a skill

Create `plugins/<plugin>/skills/<skill-name>/SKILL.md` (name + description
front matter) plus any scripts. The marketplaces pick it up from the
directory listing; no manifest changes are needed when adding a skill to an
existing plugin.

The skill body and script exist once; every ecosystem points at them.
