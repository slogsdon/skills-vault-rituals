> **[shane.logsdon.io](https://shane.logsdon.io)** — writing and projects on agentic workflows, web standards, and payments engineering.

# skills-vault-rituals — ARCHIVED

> **This plugin is retired.** Its rituals have been merged into the
> [Loop & Gate Accountability Kit](https://github.com/slogsdon/loop-and-gate-accountability-kit),
> which is portable (works with plain markdown or an Obsidian vault) and runs on
> Claude alone.
>
> | Was here | Now |
> |---|---|
> | `morning`, `eod`, `log`, `plan-tomorrow` | accountability-kit (portable versions) |
> | `decide` | accountability-kit `decide` (on-demand ritual) |
> | `inbox-process` | superseded by `loop-and-gate-foundation:inbox-triage` |
> | `morning-pro` / `eod-pro` / `log-pro` (Qwen + Professional vault) | dropped — moving to the work machine separately |
>
> Removed from the `slogsdon-claude-code-config` marketplace. Kept read-only for
> history.

Daily and session ritual skills for Claude Code. 5 skills for start-of-day, end-of-day, planning, logging, and inbox triage.

Install via the [slogsdon-claude-code-config marketplace](https://github.com/slogsdon/claude-code-config).

## Skills

- **morning** — Start-of-day focus review, surfaces carryovers, writes Today's Focus
- **eod** — End-of-day audit, diffs focus vs session log, flags deferrals
- **plan-tomorrow** — Proposes tomorrow's focus based on today's EOD audit
- **log** — Lightweight session note, appended to today's daily note
- **inbox-process** — Interactive Obsidian inbox triage with routing recommendations

### Professional vault variants

These target the **Professional** (Global Payments / WorldPay) vault instead of Personal. Because Obsidian only has the Personal vault open/registered (and the `obsidian` CLI silently falls back to Personal for an unknown `vault=`), the pro variants read/write the Professional vault with the Read/Write/Edit tools and `git` directly — they do **not** use the `obsidian` CLI.

- **morning-pro** — Professional start-of-day focus review against the GP OKRs ("professional morning", "work morning")
- **eod-pro** — Professional end-of-day audit against the GP accountability context ("work eod")
- **log-pro** — Lightweight session note appended to today's Professional daily note ("work log")

## License

MIT — see [LICENSE](LICENSE).
