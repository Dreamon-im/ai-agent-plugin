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

Ask your agent to take a screenshot of an app, or run the script from the
installed `macos-app-screenshot` skill folder:

```bash
./scripts/shot Deckhand
./scripts/shot preview ~/shots/preview.png   # partial names work
```

The first command saves to `~/Desktop/Deckhand-<timestamp>.png`. PNG dimensions
depend on the window size and display scaling; the drop shadow is omitted.
Requires macOS, Swift (from Xcode or Xcode Command Line Tools), **Screen
Recording** permission for the terminal or app running the agent, and a visible
(not minimized) window. Create the output directory first when using a custom path.

### iphone-home-screen

Ask in plain language, e.g. "move Carro into the SGLife folder". The skill
shows the planned moves and writes to the phone only after you confirm. Every
write is backed up to `~/Library/Application Support/homespring/`.

- Ships a prebuilt universal `iconctl` binary (about 15 MB) targeting macOS 13
  or later, for Apple silicon and Intel. No compilation or Homebrew dependencies
  are needed to run it. Verified on macOS 27, with the Intel build under Rosetta;
  older macOS releases and physical Intel Macs have not yet been tested.
- The iPhone must be unlocked and trust this Mac. Connect by USB, or enable
  "Show this iPhone when on Wi-Fi" in Finder and keep both devices on the same
  Wi-Fi network. USB is preferred when plugged in.
- A locked or sleeping iPhone may disappear from Wi-Fi discovery. Unlock it
  and retry if no device is found.

Source code, build scripts and tests live in
[Dreamon-im/iphone-home-screen](https://github.com/Dreamon-im/iphone-home-screen).

## Install

Choose one install method per skill and agent to avoid loading duplicate copies.
For marketplace installs, add the marketplace once, then run only the install
commands for the plugins you want. Both skills need local execution on a Mac
with access to the app window or iPhone; a cloud session alone cannot access them.

**npx (requires Node.js/npm; supports Claude Code, Codex and other agents):**

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
`macos-app-screenshot@dreamon-plugins`. The Claude Code marketplace declares a
rename from `ai-agent-plugin` to `macos-app-screenshot`; clients that support
marketplace renames can migrate it when the marketplace is refreshed.

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
Run `claude plugin validate .` before pushing. This checks Claude Code manifests;
verify Codex support separately by installing the plugin with the Codex CLI.

To add a skill to an existing plugin instead, create
`plugins/<plugin>/skills/<skill-name>/SKILL.md`; the standard `skills/` directory
is already discovered. Bump the plugin version in both manifests and update any
version pinned in a marketplace entry so existing users receive the update.
