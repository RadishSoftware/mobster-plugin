# Mobster for Claude Code and other agents

This repository holds the Mobster plugin for Claude Code and the `ios-testing` Agent Skill. With them, your coding agent builds your iOS app, runs it on a simulator Mobster manages or on the iPhone you set up in Mobster for Mac, taps through it and gets a verdict from assertions on the screen's accessibility tree.

The MCP server comes from [Mobster for Mac](https://mobster.dev/download). This repository holds no server: the plugin starts the agent inside `Mobster.app`.

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
| MCP server `mobster` | `verify_start`, `screen`, `tap`, `type_text`, `swipe`, `verify_finish`, `test_pr`, `list_devices` and the rest, served by Mobster for Mac |
| Skill `ios-testing` | The verify loop, assertions, a pull request's checks and the rules for a real iPhone |
| `/mobster:verify` | Builds the app for the simulator, checks your last UI change and reports the verdict |
| `/mobster:test-pr` | Tests the screens your branch changed and reports with frames and GIFs |

## What it runs

`scripts/mobster-mcp` starts the same command that Settings › Connectors › Use Mobster from… › **Add** writes:

```sh
/Applications/Mobster.app/Contents/MacOS/mobster-agent mcp
```

It looks for `Mobster.app` in `MOBSTER_APP`, `/Applications`, `~/Applications`, then wherever Spotlight finds it. `MOBSTER_APP_PATHS`, a list of `Mobster.app` paths separated by colons, replaces that search. When the app's settings file `~/Library/Application Support/app.mobster.desktop/agent.env` exists and only you can read it, the script adds `--env-file` with that path, so Smart (`verify`) uses the model key the app uses. The key never goes into a config. Without the app, the script prints where to download it and exits 127.

`scripts/mobster-mcp --check` runs the server's own check instead: it starts the server, lists its tools and calls `status`.

## Requirements

- Mobster for Mac 0.4.0 or later, in Applications.
- A Mac with Apple silicon, Xcode and an iOS Simulator runtime.
- For a real iPhone: the phone set up in Mobster for Mac.

## What it sends

The plugin sends nothing to Mobster's servers. With Smart on, the `verify` tool sends the steps you give it and screenshots of the simulator or iPhone to your model provider, under your account with them. [Privacy](https://mobster.dev/privacy) lists every place Mobster sends data.

## Docs and support

- [Add Mobster to your agent](https://docs.mobster.dev/agents)
- [MCP server reference](https://docs.mobster.dev/mcp-server)
- [Support](https://mobster.dev/support)

MIT license.
