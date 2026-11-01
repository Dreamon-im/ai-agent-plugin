# macos-app-screenshot

Capture a retina, shadow-free screenshot of any macOS app's window **by app
name** — no window-id hunting. One skill, installable four ways.

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
npx skills add Dreamon-im/macos-app-screenshot-plugin/plugins/macos-app-screenshot/skills/macos-app-screenshot
```

**Claude Code plugin:**

```
/plugin marketplace add Dreamon-im/macos-app-screenshot-plugin
/plugin install macos-app-screenshot@dreamon-plugins
```

**OpenAI Codex plugin:** add this repo as a plugin marketplace
(`.agents/plugins/marketplace.json`), or copy the skill folder into
`~/.codex/skills/`.

**Any agent, by hand:** copy or symlink
`plugins/macos-app-screenshot/skills/macos-app-screenshot/` into that agent's
skills directory (e.g. `~/.claude/skills/`, `~/.codex/skills/`,
`~/.agents/skills/`).

## Structure

```
.agents/plugins/marketplace.json         OpenAI/Codex marketplace manifest
.claude-plugin/marketplace.json          Claude Code marketplace manifest
plugins/macos-app-screenshot/
├── plugin.json                          OpenAI plugin manifest
├── .claude-plugin/plugin.json           Claude Code plugin manifest
└── skills/macos-app-screenshot/
    ├── SKILL.md                         instructions + gotchas
    └── scripts/shot                     the capture script
```

The skill body and script exist once; every ecosystem points at them.
