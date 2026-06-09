---
name: decide
description: Use when Shane is stuck on a decision, wants to cut through avoidance, or invokes /decide. Takes a stuck decision as input, loads accountability + patterns context, and outputs a structured session — Mirror (what you're actually doing) → Real Decision → Recommendation → Next Step. Hard mode strips diplomatic framing. Trigger on /decide, "I keep going back and forth on", "I can't decide", "help me think through", "I've been avoiding", or any stuck decision framing.
---

# Skill: /decide

Decision clarity session. One stuck decision in, one committed action out.

## Input

The decision comes from the user's message. If none is given, ask: "What decision are you avoiding?"

If the user says `/decide hard` or includes "hard mode", activate hard mode (see below).

## Steps

1. Load context:
   - `obsidian read file='Context/accountability'`
   - `obsidian read file='Context/patterns'`
   - `obsidian read file='Context/avoidance-map'` (silently skip if the file doesn't exist yet)

2. Check avoidance-map: has this decision (or a close variant) appeared before? Note how many times if so — this changes the Mirror framing.

3. Run the four-part session:

**Mirror**
Name what's actually happening — not the stated problem, but the real behavior. What pattern is this? (confrontation avoidance, commitment fear, perfectionism, change resistance, low-stakes procrastination dressed up as complexity.) If this decision has appeared in avoidance-map before, say so plainly: "You've circled this one N times." Don't soften the Mirror. The user is here because they already know they're avoiding something.

**Real Decision**
Strip the framing. What is the actual choice? One sentence, binary or bounded. No "it depends."

**Recommendation**
One move, this week. Not a list. Not "consider X." A specific action with enough detail to act on it without further clarification.

**Next Step**
The smallest concrete thing to do today or tomorrow to start executing the recommendation.

4. After presenting the session, ask: "Want me to log this to your avoidance map?"
   - If yes (or if the decision already exists in the map): append a row to `Context/avoidance-map` and commit the vault.
   - Format: `| [Decision summary] | [today's date] | [N] | [brief note from this session] |`
   - If a row already exists for this decision, update it in place (increment count, update last note).

5. Vault commit (if written):
   ```bash
   VAULT="$HOME/Library/Mobile Documents/iCloud~md~obsidian/Documents/Personal"
   git -C "$VAULT" add -A && git -C "$VAULT" commit -m "docs: log decision session to avoidance-map"
   ```

## Hard mode

When activated, apply these changes to Mirror and Recommendation:
- Mirror: name the avoidance pattern in plain terms, without softening. Call the fear by its real name if you can identify it. No diplomatic preamble.
- Recommendation: remove hedging language. State what to do, not what to consider.

Hard mode doesn't change the structure — just the tone. It's for when Shane already knows what he needs to hear and wants it said plainly.

## Output format

Four labeled blocks, blank line between each. Recommendation and Next Step are single sentences — not lists.

```
**Mirror**
[what's actually happening]

**Real Decision**
[the actual binary/bounded choice, one sentence]

**Recommendation**
[one specific action, this week]

**Next Step**
[smallest thing to do today or tomorrow]
```
