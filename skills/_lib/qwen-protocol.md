# Qwen Delegation Protocol

## Invocation

Call `mcp__ollama-agent__qwen_start` (standalone) or `mcp__plugin_shane-config_ollama-agent__qwen_start` (plugin — use whichever is available) with:
- `task`: the task string defined in the calling skill
- `skill`: the calling skill's name
- `context`: any relevant context from the current conversation

## Loop

If `status` is `"running"`, call `mcp__ollama-agent__qwen_continue` (or `mcp__plugin_shane-config_ollama-agent__qwen_continue` in plugin) with `session_id`. Repeat until `status` is `"done"` or `"error"`.

If `status` is `"error"`, surface the `result` field as an error message and stop — do not continue the loop.

## Vault access (for Qwen task strings)

When Qwen needs vault access, prefix the task string with:
```
Vault access (bash only, no MCP tools): `obsidian search query='TERM' limit=10`, `obsidian read file='Note Name'` (no .md), `obsidian append file='Note Name' content='TEXT'`, `obsidian backlinks note='Note Name'`, `obsidian daily:read`, `obsidian daily:append content='TEXT'`.
```
