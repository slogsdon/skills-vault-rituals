---
name: log-pro
description: Use when /log-pro is invoked, or when Shane says "professional log", "work log", "log this to work", "log to professional", or wants to capture a lightweight session note in the PROFESSIONAL (Global Payments / WorldPay) vault. Appends a timestamped entry to today's Professional daily note under Session Log. For the Personal vault use /log instead.
---

# Skill: /log-pro

Lightweight session capture for the **Professional** vault. No Qwen needed — direct write.

## Vault selection

This is the Professional variant of `/log`. It targets the Professional vault.

```bash
PROF_VAULT="$HOME/Library/Mobile Documents/iCloud~md~obsidian/Documents/Professional"
```

**I/O mechanism:** The `obsidian` CLI cannot reach the Professional vault — Obsidian only has the Personal vault open/registered, and `vault=Professional` silently falls back to Personal. So this skill operates on the Professional vault with the **Read/Write/Edit tools and `git` directly** on `$PROF_VAULT`. This is the intended, safe path for a vault Obsidian never opens. (If Shane later opens Professional in Obsidian, direct file ops still work unchanged.)

Daily notes live at `$PROF_VAULT/Daily Notes/[YYYY-MM-DD].md`.

## Steps

1. Determine today's date (YYYY-MM-DD format).
2. Get the current time (HH:MM format).
3. Capture Shane's session note from his message (everything after the command).
4. Append the entry to today's Professional daily note:
   - File path: `$PROF_VAULT/Daily Notes/[today's date].md`
   - If it **exists**: append `\n**[HH:MM]** [Shane's note]` under the `## Session Log` heading (use Edit, or read → append). If the file has no `## Session Log` heading, add `\n## Session Log\n\n` before the entry.
   - If it **does not exist**: create it with:
     ```markdown
     # [YYYY-MM-DD]

     ## Session Log

     **[HH:MM]** [Shane's note]
     ```
5. Commit the Professional vault:
   ```bash
   PROF_VAULT="$HOME/Library/Mobile Documents/iCloud~md~obsidian/Documents/Professional"
   git -C "$PROF_VAULT" add -A && git -C "$PROF_VAULT" commit -m "docs: log session entry [HH:MM]"
   ```
6. Confirm to Shane: "Logged to Professional vault at [HH:MM]."

## Notes

- Keep it fast — this skill should feel like a 2-second action.
- Do **not** use the `obsidian` CLI here; it would write to the Personal vault.
