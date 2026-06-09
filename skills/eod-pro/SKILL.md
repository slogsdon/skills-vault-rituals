---
name: eod-pro
description: Use when /eod-pro is invoked, or when Shane says "professional eod", "work eod", "close out my work day", "end of work day", or wants to run his end-of-day accountability audit against the PROFESSIONAL (Global Payments / WorldPay) vault and its GP OKRs. Diffs Today's Focus vs Session Log, increments deferrals in the Professional patterns.md, flags 3+ deferrals, and writes an EOD Audit to today's Professional daily note. For the Personal vault use /eod instead.
---

# Skill: /eod-pro

End-of-day accountability audit for the **Professional** vault. Delegate analysis to Qwen; Claude handles writes. Professional variant of `/eod`.

## Vault selection

This skill targets the **Professional** vault and its GP-specific accountability context.

```bash
PROF_VAULT="$HOME/Library/Mobile Documents/iCloud~md~obsidian/Documents/Professional"
```

**I/O mechanism:** The `obsidian` CLI cannot reach the Professional vault — Obsidian only has the Personal vault open/registered, and `vault=Professional` silently falls back to Personal. So this skill reads and writes the Professional vault with the **Read/Write/Edit tools and `git` directly** on `$PROF_VAULT`. Do not use the `obsidian` CLI here; it would read/write the Personal vault.

Relevant paths:
- Daily notes: `$PROF_VAULT/Daily Notes/[YYYY-MM-DD].md`
- Accountability (GP OKRs): `$PROF_VAULT/Context/accountability.md`
- Patterns: `$PROF_VAULT/Context/patterns.md`
- Avoidance map: `$PROF_VAULT/Context/avoidance-map.md`

## Steps

1. Determine today's date (YYYY-MM-DD format).
2. Read directly (Read tool) from the Professional vault:
   - `$PROF_VAULT/Daily Notes/[today's date].md`
   - `$PROF_VAULT/Context/patterns.md`
   - `$PROF_VAULT/Context/accountability.md`
3. Call `mcp__ollama-agent__qwen_start` (standalone) or `mcp__plugin_shane-config_ollama-agent__qwen_start` (plugin — use whichever is available) with:
   - `task`: "You are Shane's EOD accountability agent for his Global Payments / WorldPay developer-advocacy work. Compare the 'Today's Focus' section against the 'Session Log' section in today's daily note (provided). For each focus item NOT reflected in the session log, flag it as deferred. Check patterns.md for existing deferral counts and increment them. Flag any item now at 3+ deferrals with: 'PATTERN ALERT: [task] has been deferred [N] times. Is this actually a priority?' Output: (1) EOD Audit block for the daily note, (2) updated rows for patterns.md Deferred Tasks Log."
   - `skill`: "eod-pro"
   - `context`: content of all three files.
4. Loop: if `status` is `"running"`, call `mcp__ollama-agent__qwen_continue` (or `mcp__plugin_shane-config_ollama-agent__qwen_continue` in plugin) with `session_id`; repeat until `status` is `"done"` or `"error"`.
5. Walk check:
   - Ask Shane: "Did you walk today? (y/n / skipped)"
   - Append to today's session log: `\n**[HH:MM]** 🚶 Walk: [yes/no/skipped]` in `$PROF_VAULT/Daily Notes/[today's date].md`.
   - If yes: in `$PROF_VAULT/Context/patterns.md` find `walk_streak_missed` and reset to 0 (skip if field absent).
   - If no or skipped: find or add `walk_streak_missed` in patterns.md, increment by 1; flag if 3+.
6. Decision avoidance scan (before writing the audit):
   - Scan focus items and session log entries for language like "figure out", "decide on", "choose between", "TBD", "still thinking about", or any item that's clearly an undecided decision rather than a task.
   - For each one found, check `$PROF_VAULT/Context/avoidance-map.md` to see if it already exists.
   - If it appears in the map already: note it as recurring in the EOD Audit — "Decision recurring: [item] — consider running /decide".
   - Do not write to avoidance-map here. Just surface it in the audit.
7. Parse Qwen's `result` and write directly:
   - Append `\n## EOD Audit\n\n[audit block]` to `$PROF_VAULT/Daily Notes/[today's date].md` (Edit / read → append).
   - Update the Deferred Tasks Log rows in `$PROF_VAULT/Context/patterns.md` (Edit: insert new rows / increment existing).
   - For each item Qwen marks as completed today: open `$PROF_VAULT/Context/accountability.md`, find the matching status line, and update it to reflect completion with the date. Use the Edit/Write tool on the Professional path directly. This keeps the GP OKR accountability current so `/morning-pro` always sees accurate state.
8. If the day had no session log entries at all, add a `## Logging Gap` entry to today's note.
9. Commit the Professional vault:
   ```bash
   PROF_VAULT="$HOME/Library/Mobile Documents/iCloud~md~obsidian/Documents/Professional"
   git -C "$PROF_VAULT" add -A && git -C "$PROF_VAULT" commit -m "docs: append EOD audit to [today's date]"
   ```
10. Present the audit summary to Shane.

## Fallback (if qwen_start/qwen_continue unavailable)

Execute the skill directly:

1. Determine today's date (YYYY-MM-DD).
2. Read via the Read tool (same three Professional files as above).
3. Diff focus vs session log:
   - Extract items from `## Today's Focus`.
   - Extract completed items from `## Session Log`.
   - Items in focus but NOT in session log = deferred today.
3a. Walk check:
    - Ask "Did you walk today? (y/n / skipped)".
    - Note current time (HH:MM); append `\n**[HH:MM]** 🚶 Walk: [yes/no/skipped]` to today's note.
    - If yes: find `walk_streak_missed` in patterns.md; reset to 0 if present.
    - If no or skipped: find or add `walk_streak_missed` in patterns.md, increment by 1; add PATTERN ALERT if 3+.
3b. Decision avoidance scan:
    - Scan focus items and session log for "figure out", "decide on", "choose between", "TBD", "still thinking about", or any clearly-undecided decision.
    - For each found, check `$PROF_VAULT/Context/avoidance-map.md` (if it exists) for an existing entry.
    - If recurring: add to the EOD Audit block: "Decision recurring: [item] — consider running /decide".
    - Do not write to avoidance-map here.
4. For each deferred item, check the Deferred Tasks Log in `$PROF_VAULT/Context/patterns.md`:
   - If it exists: increment the deferral count.
   - If new: add a row with count = 1.
   - If count reaches 3+: add a PATTERN ALERT line.
5. Build the EOD Audit block:
   ```markdown
   ## EOD Audit

   **Completed:** [items from session log that matched focus]
   **Deferred:**
   - [task] (deferred [N] times total) [PATTERN ALERT if N >= 3]

   **Logging gaps:** [note if session log was empty]
   **OKR alignment:** [brief assessment of whether completed work mapped to active GP OKRs]
   ```
6. Append the audit block to `$PROF_VAULT/Daily Notes/[today's date].md`.
7. Update the Deferred Tasks Log section in `$PROF_VAULT/Context/patterns.md`.
8. If the session log was empty, also add a `## Logging Gap` entry to today's note.
9. Commit the Professional vault:
   ```bash
   PROF_VAULT="$HOME/Library/Mobile Documents/iCloud~md~obsidian/Documents/Professional"
   git -C "$PROF_VAULT" add -A && git -C "$PROF_VAULT" commit -m "docs: append EOD audit to [today's date]"
   ```
10. Present the audit summary to Shane.
