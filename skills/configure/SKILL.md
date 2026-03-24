---
name: configure
description: Set up the WeCom channel — save bot credentials and review access policy.
user-invocable: true
allowed-tools:
  - Read
  - Write
  - Bash(ls *)
  - Bash(mkdir *)
---

# /wecom:configure — WeCom Channel Setup

Writes bot credentials to `~/.claude/channels/wecom/credentials.json`
and orients the user on access policy. The server reads credentials at boot.

Arguments passed: `$ARGUMENTS`

---

## Dispatch on arguments

### No args — status and guidance

1. **Credentials** — check `~/.claude/channels/wecom/credentials.json`.
   Show set/not-set; if set, show botId and first 6 chars of secret masked.

2. **Access** — read `~/.claude/channels/wecom/access.json`.
   Show: DM policy, allowed senders, pending pairings.

3. **Next steps** based on state:
   - No credentials → "Run `/wecom:configure <botId> <secret>`"
   - Credentials set, nobody allowed → "Send a message to the bot in WeCom,
     then approve with `/wecom:access pair <code>`"
   - Ready → "Ready. Message the bot to reach Claude."

Push toward `allowlist` policy once IDs are captured.

### `<botId> <secret>` — save credentials

1. Parse arguments: first = botId (starts with `aib-`), second = secret.
2. `mkdir -p ~/.claude/channels/wecom`
3. Write credentials.json:
   ```json
   {
     "botId": "aib-xxx",
     "secret": "xxx",
     "wsUrl": "wss://openws.work.weixin.qq.com"
   }
   ```
4. File mode 0o600.
5. Confirm, then show status.

### `clear` — remove credentials

Delete credentials.json.

### `baseurl <url>` — change WebSocket URL

Update `wsUrl` in credentials.json for custom/private deployments.

---

## Implementation notes

- Credentials are read once at boot. Changes need session restart.
- access.json is re-read on every inbound message — policy changes are immediate.
- Default WebSocket URL: `wss://openws.work.weixin.qq.com`
