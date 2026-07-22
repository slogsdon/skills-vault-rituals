---
name: morning
description: Use when /morning is invoked or when Shane wants to start his day with a focus review. Reads accountability context and yesterday's daily note, surfaces carryovers, runs a knowledge briefing, and writes Today's Focus to today's daily note.
---

# Skill: /morning

Start the day by surfacing what carry-overs demand attention, where they align with OKRs, and what happened in the vault overnight.

**Don't:** flag items marked ✅ in patterns.md as carry-overs — patterns.md is authoritative, not accountability.md. Don't surface more than one primary focus and two secondaries.

## Steps

1. Determine today's and yesterday's dates (YYYY-MM-DD).
2. Read via obsidian CLI:
   - `obsidian read file='Knowledge/Context/accountability'`
   - `obsidian read file='Knowledge/Context/patterns'`
   - `obsidian read file='Daily/[yesterday's date]'` — if absent, try D-2, D-3, D-4 in order; label the actual date used.
3. Follow [Qwen Protocol](_lib/qwen-protocol.md) with:
   - `task`: "You are Shane's morning accountability agent. Sources: (1) accountability.md — OKR context, may have stale status lines; (2) patterns.md — authoritative completion log, ✅ rows are definitively done; (3) most recent daily note. Cross-reference all three. Do NOT flag ✅ items in patterns.md as carry-overs. Surface only items unresolved in patterns.md. Propose today's one primary focus and 2 secondaries based on OKR alignment. Be direct, no fluff."
   - `skill`: "morning"
   - `context`: full content of all three files with clear section headers indicating source
4. Follow [Knowledge Briefing Protocol](../../skills-vault-knowledge/skills/_lib/knowledge-briefing.md).
5. Write focus + briefing to today's daily note:
   - If exists: `obsidian append file='Daily/[today]' content='## Today'\''s Focus\n\n[Qwen result]\n\n## Knowledge Briefing\n\n[briefing output]'`
   - If new: `obsidian create name='Daily/[today]' content='# [today]\n\n## Today'\''s Focus\n\n[Qwen result]\n\n## Knowledge Briefing\n\n[briefing output]' silent`
6. Walk check (yesterday):
   - Scan yesterday's note for `🚶 Walk:`
   - If found: skip to step 7.
   - If not found: ask "Did you walk yesterday? (y/n)"
     - yes → `obsidian append file='Daily/[yesterday]' content='**[HH:MM]** 🚶 Walk: yes'`
     - no → note briefly, continue
   - Nudge: "Walk scheduled for today?" (one-liner, no reply required)
7. Commit the vault:
   ```bash
   VAULT="$HOME/Library/Mobile Documents/iCloud~md~obsidian/Documents/Personal"
   git -C "$VAULT" add -A && git -C "$VAULT" commit -m "docs: write today's focus to [today] daily note"
   ```
8. Present the focus summary and knowledge briefing to Shane.

## Fallback

If Qwen is unavailable, execute directly:

Steps 1–2: same as above.

Step 3 (direct): Identify carry-overs — items in patterns.md without ✅ COMPLETED. Cross-check yesterday's note EOD Audit for same-day completions not yet in patterns.md. Propose focus: Primary = highest-priority OKR-aligned item (prefer carry-overs with 2+ deferrals). Secondary = next 2 OKR-aligned tasks. Flag any avoidance pattern matches from accountability.md.

Steps 4–8: same as above.
