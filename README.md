# ai-agent-plugin

**Dreamon's collection of agent skills**, packaged for Claude Code, Codex, and
other agents that read the [Agent Skills](https://agentskills.io) format.

## Skills

| Skill | What it does |
|---|---|
| [macos-app-screenshot](plugins/macos-app-screenshot/skills/macos-app-screenshot/) | Capture a retina, shadow-free screenshot of any macOS app's window **by app name** — no window-id hunting. |
| [iphone-home-screen](plugins/iphone-home-screen/skills/iphone-home-screen/) | Rearrange an iPhone home screen over USB or Wi-Fi by asking: move apps, create and tidy folders, find offloaded apps. Plans every change first, backs up, and verifies. |

Each skill is its own plugin, so you install only the ones you want.

### macos-app-screenshot

```bash
scripts/shot <AppName> [output.png]
# shot Deckhand                       → ~/Desktop/Deckhand-<timestamp>.png
# shot preview ~/shots/preview.png    # partial names work
```

2000×1280-class PNG at 2× retina, drop shadow omitted. Requires **Screen
Recording** permission for the terminal running the agent, and a visible (not
minimized) window.

### iphone-home-screen

Ask in plain language, e.g. "move Carro into the SGLife folder". The skill
shows the planned moves and writes to the phone only after you confirm. Every
write is backed up to `~/Library/Application Support/homespring/`.

- macOS 13 or later, Apple silicon or Intel. Ships a prebuilt `iconctl`
  binary (about 15 MB); nothing else to install.
- The iPhone must be unlocked and trust this Mac, over USB, or over Wi-Fi
  once "Show this iPhone when on Wi-Fi" is on in Finder.

Source code, build scripts and tests live in
[Dreamon-im/iphone-home-screen](https://github.com/Dreamon-im/iphone-home-screen).

## Install

**npx (auto-detects Claude Code, Codex, Cursor, and 20+ agents):**

```bash
npx skills add Dreamon-im/ai-agent-plugin/plugins/macos-app-screenshot/skills/macos-app-screenshot
npx skills add Dreamon-im/ai-agent-plugin/plugins/iphone-home-screen/skills/iphone-home-screen
```

**Claude Code plugin:**

```
/plugin marketplace add Dreamon-im/ai-agent-plugin
/plugin install macos-app-screenshot@dreamon-plugins
/plugin install iphone-home-screen@dreamon-plugins
```

**OpenAI Codex plugin:**

```bash
codex plugin marketplace add Dreamon-im/ai-agent-plugin
codex plugin add macos-app-screenshot@dreamon-plugins
codex plugin add iphone-home-screen@dreamon-plugins
```

If you installed the screenshot plugin under its old name `ai-agent-plugin`,
remove it (`codex plugin remove ai-agent-plugin@dreamon-plugins`) and add
`macos-app-screenshot@dreamon-plugins`. Claude Code moves existing installs
over on its own.

**Any agent, by hand:** copy or symlink the skill folder you want into that
agent's skills directory (e.g. `~/.claude/skills/`, `~/.codex/skills/`,
`~/.agents/skills/`).

## Structure

```
.agents/plugins/marketplace.json         OpenAI/Codex marketplace manifest
.claude-plugin/marketplace.json          Claude Code marketplace manifest
plugins/
├── macos-app-screenshot/
│   ├── .codex-plugin/plugin.json        OpenAI plugin manifest
│   ├── .claude-plugin/plugin.json       Claude Code plugin manifest
│   └── skills/
│       └── macos-app-screenshot/
│           ├── SKILL.md                 instructions + gotchas
│           └── scripts/shot             the capture script
└── iphone-home-screen/
    ├── .codex-plugin/plugin.json
    ├── .claude-plugin/plugin.json
    └── skills/
        └── iphone-home-screen/
            ├── SKILL.md
            ├── references/              layout format and recipes
            └── bin/iconctl              prebuilt universal binary
```

## Adding a skill

As its own plugin: create `plugins/<name>/skills/<name>/SKILL.md` (name +
description front matter) plus any scripts, add `.claude-plugin/plugin.json`
and `.codex-plugin/plugin.json` next to `skills/`, and add an entry to both
`.claude-plugin/marketplace.json` and `.agents/plugins/marketplace.json`.
Run `claude plugin validate .` before pushing.

To add a skill to an existing plugin instead, create
`plugins/<plugin>/skills/<skill-name>/SKILL.md`; no manifest changes are needed.

## Updating iphone-home-screen

The skill is developed in
[Dreamon-im/iphone-home-screen](https://github.com/Dreamon-im/iphone-home-screen),
whose `skill/` folder is the source of truth. After a change there is pushed,
copy it here and bump `version` in both of its `plugin.json` files:

```bash
rsync -a --delete <path-to>/iphone-home-screen/skill/ plugins/iphone-home-screen/skills/iphone-home-screen/
```

The skill body and script exist once; every ecosystem points at them.
