# SETUP — agentic trading team (Phase 2)

This folder is a ready-to-run Claude Code project. Phase 2 = the team researches and
proposes trades, but **every order waits for your explicit approval and starts small.**

## What's in here

```
agentic-trading/
├── CLAUDE.md                       # the master orchestrator + guardrails (read this)
├── config/
│   ├── investment-policy.example.md             # copy this to investment-policy.md
│   └── investment-policy.semiconductor.example.md  # a filled-in example
├── .claude/agents/                 # the specialist team
│   ├── macro-regime.md
│   ├── research-catalyst.md
│   ├── fundamentals.md
│   ├── technicals-timing.md
│   ├── benchmark-performance.md
│   ├── risk-management.md          # has veto power
│   ├── devils-advocate.md          # red-teams every trade
│   └── journaler.md                # writes the audit trail
└── journal/                        # dated decision log lands here
```

## One-time setup

1. **Put this folder where you want it**, e.g. `~/agentic-trading`, and open it in
   VS Code (File → Open Folder). Open the integrated terminal (Ctrl+` / Cmd+`).

2. **Create and edit your config.** Copy the template, then edit your private copy:
   `cp config/investment-policy.example.md config/investment-policy.md`. Fill in your
   watchlist/sectors and confirm the caps. Set the **per-trade max notional** to
   something genuinely small to start. (A filled-in example lives at
   `config/investment-policy.semiconductor.example.md`.) Your `investment-policy.md` is
   gitignored so you won't accidentally publish it.

3. **Connect the Robinhood Trading MCP.** Create your Robinhood Agentic account in the
   app, fund it with a small, losable amount, then follow Robinhood's official connect
   guide (servers at agent.robinhood.com; guide at
   robinhood.com/us/en/support/agentic-trading). In this folder, add it with
   `claude mcp add` (or a `.mcp.json`) using the URL/auth from Robinhood.

4. **Start Claude Code** in this folder: run `claude`. Then run `/mcp` and **note the
   exact Robinhood tool names** it lists — the master uses whatever names appear, it
   does not assume them. Run `/agents` to confirm the 8 specialists loaded. (If you
   add or edit an agent file later, restart the session to reload.)

5. **Recommended models:** run the master session on Opus for the final reasoning;
   the specialists are set to Sonnet (or `inherit` for risk + red-team). You can tune
   this with the `model:` field in each agent file.

6. **(Optional) Pixel Agents:** install the extension, open its panel, and spawn the
   master via **+ Agent** so you can watch the team work. Don't use the
   `--dangerously-skip-permissions` launch option for this project — you want the
   approval prompts ON.

## Running a cycle

In the Claude Code session, type:

```
Run the daily cycle.
```

The master will pull your account, dispatch the team in parallel, red-team and
risk-check the ideas, then show you a proposal table and **wait**. Approve with e.g.
`approve 1,3`, or `approve all`, or `skip`. Nothing is placed until you do. After
execution it writes the day's journal entry.

### Run it automatically each day (still gated by your approval)

Long-term bias = you don't need high frequency. A daily pre-market run is plenty.
Either use Claude Code's scheduled **routines**, or a local cron that starts a session
and sends "Run the daily cycle." Note: scheduling the *research* is fine, but in
Phase 2 the **execution still pauses for your approval** — so check the session when
it pings you. Don't automate away the human gate while you're in Phase 2.

## Moving to Phase 3 later (optional, much later)

Only after a real track record in the journal. Phase 3 would let the master
auto-execute **within** the hard caps and circuit breakers, with no per-order
approval. To get there you'd: (a) confirm the journal shows the system actually adding
risk-adjusted value, (b) tighten the circuit breakers, and (c) edit CLAUDE.md's
"Operating mode" to allow auto-execution inside caps. Until then, keep the human gate.

## Publishing this as a public repo

This kit is safe to publish *as a starter* — the `.gitignore` keeps secrets and your
personal data out. Before your first push, double-check you are NOT committing:
`.mcp.json`, any `.env`, or `config/investment-policy.md` (your real one). Run
`git status` and confirm only template/`.example` files and the agent definitions show.

```bash
cd agentic-trading
git init
git add .
git status          # verify NO secrets / no investment-policy.md (only the .example)
git commit -m "Agentic trading team starter (Phase 2)"
git branch -M main
git remote add origin <your-empty-github-repo-url>
git push -u origin main
```

If you ever accidentally staged a secret, remove it with `git rm --cached <file>`
before committing. If a secret was already pushed, rotate/disconnect it (e.g.
disconnect the Robinhood agent) — scrubbing git history is not enough on its own.

## Reminders

- Stocks only. No options/margin/short — these are hard limits in CLAUDE.md.
- Fund only what you can afford to lose entirely. Robinhood is explicit that it does
  not supervise the agent and you assume all risk for its orders.
- Beating the S&P 500 consistently is hard; most pros don't. Let the journal tell you
  the truth, and be willing to switch to plain index investing if it's winning.
- This is not financial advice.
