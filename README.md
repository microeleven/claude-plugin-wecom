# WeChat Work (WeCom) Channel Plugin for Claude Code

Enterprise WeChat (企业微信) smart bot channel plugin for [Claude Code](https://claude.com/claude-code). Bridges WeCom smart bot messages to Claude Code using the official WebSocket long-connection protocol.

## Features

- **WebSocket long-connection** — no public IP required, outbound WSS only
- **Built-in access control** — pairing codes, allowlists, DM policy management
- **Markdown replies** — rich formatted responses via WeCom Markdown
- **Message queue** — poll-based fallback via `get_messages` tool
- **Auto-reconnect** — exponential backoff with up to 100 retry attempts

## Prerequisites

- [Bun](https://bun.sh/) runtime
- WeCom admin account with a **smart bot** (智能机器人) configured
- Bot ID and Secret from WeCom admin console

## Installation

### 1. Add the marketplace

```bash
/plugin marketplace add microeleven/claude-plugin-wecom
```

### 2. Install the plugin

```bash
/plugin install wecom@claude-plugin-wecom
```

### 3. Enable the channel

Start Claude Code with the channel flag:

```bash
claude --dangerously-load-development-channels plugin:wecom@claude-plugin-wecom
```

> **Note:** This flag is required for channel notifications (messages auto-appearing in your session). Without it, you can still use the `get_messages` tool to poll for messages manually.

### 4. Configure credentials

In Claude Code, run:

```
/wecom:configure
```

Enter your Bot ID and Secret when prompted.

### 5. Pair your WeCom account

Send any message to the bot in WeCom. You'll receive a pairing code. Then in Claude Code:

```
/wecom:access pair <code>
```

## Skills

| Skill | Description |
|-------|-------------|
| `/wecom:configure` | Set up bot credentials |
| `/wecom:access` | Manage access control — pair users, edit allowlists, set DM policy |

## Tools

| Tool | Description |
|------|-------------|
| `reply` | Send a reply to a WeCom chat (supports Markdown) |
| `get_messages` | Poll for new messages from the queue |
| `edit_message` | Update a previously sent message |

## Architecture

```
WeCom App ←→ WeCom Cloud ←WSS→ Plugin (MCP Server) ←stdio→ Claude Code
```

The plugin maintains a persistent WebSocket connection to `wss://openws.work.weixin.qq.com`, subscribes as the configured bot, and bridges messages bidirectionally via the MCP channel protocol.

## State

All state is stored in `~/.claude/channels/wecom/`:

| File | Purpose |
|------|---------|
| `credentials.json` | Bot ID and Secret |
| `access.json` | Access control policy and allowlist |

## Debug

Set `WECOM_DEBUG=1` environment variable to enable debug logging to stderr.

## License

MIT
