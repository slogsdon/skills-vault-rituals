---
name: morning-pro
description: Use when /morning-pro is invoked, or when Shane says "professional morning", "work morning", "start professional day", "start my work day", or wants to start his GP/WorldPay work day with a focus review against the PROFESSIONAL vault's GP OKRs. Reads Professional accountability context and the most recent Professional daily note, surfaces carryovers, and writes Today's Focus to today's Professional daily note. For the Personal vault use /morning instead.
---

# Skill: /morning-pro

Start the **work** day by surfacing what carry-overs demand attention and where they align with the GP/WorldPay OKRs. Professional variant of `/morning`.

**Don't:** flag items marked ✅ in patterns.md as carry-overs — patterns.md is authoritative, not accountability.md. Don't surface more than one primary focus and two secondaries.

## Vault selection

This skill targets the **Professional** vault.

```bash
PROF_VAULT="$HOME/Library/Mobile Documents/iCloud~md~obsidian/Documents/Professional"
```

**I/O mechanism:** The `obsidian` CLI cannot reach the Professional vault — Obsidian only has the Personal vault open/registered, and `vault=Professional` silently falls back to Personal. So this skill reads and writes the Professional vault with the **Read/Write/Edit tools and `git` directly** on `$PROF_VAULT`. Do not use the `obsidian` CLI here; it would read/write the Personal vault.

Daily notes live at `$PROF_VAULT/Daily Notes/[YYYY-MM-DD].md`. Accountability context lives in `$PROF_VAULT/Context/`.

## Steps

1. Determine today's and yesterday's dates (YYYY-MM-DD).
2. Read directly (Read tool) from the Professional vault:
   - `$PROF_VAULT/Context/accountability.md` (GP OKRs — source of truth)
   - `$PROF_VAULT/Context/patterns.md`
   - `$PROF_VAULT/Daily Notes/[yesterday].md` — if absent, try D-2, D-3, D-4 in order; label the actual date used. If none exist yet, note "no prior Professional daily note" and proceed.
3. Follow the [Qwen Protocol](../_lib/qwen-protocol.md) (shared with `/morning`) with:
   - `task`: "You are Shane's morning accountability agent for his Global Payments / WorldPay developer-advocacy work. Sources: (1) accountability.md — GP OKR context, may have stale status lines; (2) patterns.md — authoritative completion log, ✅ rows are definitively done; (3) most recent Professional daily note. Cross-reference all three. Do NOT flag ✅ items in patterns.md as carry-overs. Surface only items unresolved in patterns.md. Propose today's one primary focus and 2 secondaries based on GP OKR alignment. Be direct, no fluff."
   - `skill`: "morning-pro"
   - `context`: full content of all three files with clear section headers indicating source.
4. Write focus to today's Professional daily note at `$PROF_VAULT/Daily Notes/[today].md`:
   - If it **exists**: append `\n## Today's Focus\n\n[Qwen result]` (Edit / read → append).
   - If **new**: create it with:
     ```markdown
     # [today]

     ## Today's Focus

     [Qwen result]
     ```
   (No Knowledge Briefing here — the Professional vault has no Concepts knowledge graph; this is intentionally lighter than `/morning`.)
5. Walk check (yesterday):
   - Scan yesterday's note for `🚶 Walk:`.
   - If found: skip to step 6.
   - If not found: ask "Did you walk yesterday? (y/n)"
     - yes → append `\n**[HH:MM]** 🚶 Walk: yes` to `$PROF_VAULT/Daily Notes/[yesterday].md`
     - no → note briefly, continue
   - Nudge: "Walk scheduled for today?" (one-liner, no reply required)
6. Commit the Professional vault:
   ```bash
   PROF_VAULT="$HOME/Library/Mobile Documents/iCloud~md~obsidian/Documents/Professional"
   git -C "$PROF_VAULT" add -A && git -C "$PROF_VAULT" commit -m "docs: write today's focus to [today] daily note"
   ```
7. Present the focus summary to Shane.

## Fallback

If Qwen is unavailable, execute directly:

Steps 1–2: same as above.

Step 3 (direct): Identify carry-overs — items in patterns.md without ✅ COMPLETED. Cross-check the most recent Professional daily note's EOD Audit for same-day completions not yet in patterns.md. Propose focus: Primary = highest-priority GP-OKR-aligned item (prefer carry-overs with 2+ deferrals). Secondary = next 2 GP-OKR-aligned tasks. Flag any avoidance pattern matches from `$PROF_VAULT/Context/avoidance-map.md`.

Steps 4–7: same as above.
