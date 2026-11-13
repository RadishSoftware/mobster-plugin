# Mobster for Claude Code and other agents

This repository holds the Mobster plugin for Claude Code and the `ios-testing` Agent Skill. With them, your coding agent builds your iOS app, runs it on a simulator Mobster manages or on the iPhone you set up in Mobster for Mac, taps through it and gets a verdict from assertions on the screen's accessibility tree.

This repository holds no server. The plugin starts Mobster's MCP server from [Mobster for Mac](https://mobster.dev/download) when it's installed, else from the `mobster` command, else from `mobster-cli` on PyPI through uv. You don't need the app to test on a simulator.

## Install

In Claude Code:

```text
/plugin marketplace add RadishSoftware/mobster-plugin
/plugin install mobster@mobster
```

The skill on its own, for Claude Code, Codex, Cursor and other agents that read Agent Skills:

```sh
npx skills add RadishSoftware/mobster-plugin
```

The skill needs the `mobster` MCP server in your agent. In Mobster for Mac, open Settings › Connectors and, under **Use Mobster from…**, press **Add** beside Claude Code, Claude Desktop, Cursor or Codex.

## What you get

| Part | What it does |
|---|---|
| MCP server `mobster` | `verify_start`, `screen`, `tap`, `type_text`, `swipe`, `verify_finish`, `test_pr`, `list_devices` and the rest, served by Mobster for Mac or the Mobster CLI |
| Skill `ios-testing` | The verify loop, assertions, a pull request's checks and the rules for a real iPhone |
| `/mobster:verify` | Builds the app for the simulator, checks your last UI change and reports the verdict |
| `/mobster:test-pr` | Tests the screens your branch changed and reports with frames and GIFs |

## What it runs

`scripts/mobster-mcp` starts the first of these it finds:

1. Mobster for Mac: `/Applications/Mobster.app/Contents/MacOS/mobster-agent mcp`, the command Settings › Connectors › Use Mobster from… › **Add** writes.
2. The `mobster` command: `mobster mcp`.
3. uv: `uvx --python 3.12 --from mobster-cli mobster mcp`. The first start downloads `mobster-cli` from PyPI.

It looks for `Mobster.app` in `MOBSTER_APP`, `/Applications`, `~/Applications`, then wherever Spotlight finds it. `MOBSTER_APP_PATHS`, a list of `Mobster.app` paths separated by colons, replaces that search. It looks for `mobster` and `uvx` on `PATH`, then in `MOBSTER_BIN_DIRS` (`~/.local/bin`, `/opt/homebrew/bin` and `/usr/local/bin` by default), because an app opened from the Dock doesn't see your shell's `PATH`. When the app's settings file `~/Library/Application Support/app.mobster.desktop/agent.env` exists and only you can read it, the script adds `--env-file` with that path, so Smart (`verify_start` with `smart`) uses the model key the app uses. The key never goes into a config. With none of the three, the script prints the install line on stderr and serves only two tools that do nothing, `gate_note` and `gate`, so Claude Code shows no error.

`scripts/mobster-mcp --check` runs the server's own check instead: it starts the server, lists its tools and calls `status`.

## Requirements

- Mobster for Mac 0.4.0 or later in Applications, the `mobster` command, or uv with `uvx` on your `PATH`.
- A Mac with Apple silicon, Xcode and an iOS Simulator runtime.
- For a real iPhone: the phone set up in Mobster for Mac.

## What it sends

The plugin sends nothing to Mobster's servers. With Smart on, `verify_start` with `smart` sends the steps you give it and screenshots of the simulator or iPhone to your model provider, under your account with them. [Privacy](https://mobster.dev/privacy) lists every place Mobster sends data.

## Docs and support

- [Add Mobster to your agent](https://docs.mobster.dev/agents)
- [MCP server reference](https://docs.mobster.dev/mcp-server)
- [Support](https://mobster.dev/support)

MIT license.
