# Agentic Trading Team (Claude Code + Robinhood)

A starter kit for running a **multi-agent equities trading team** on
[Claude Code](https://code.claude.com), connected to a **Robinhood Agentic** account
via MCP. One master "Portfolio Manager" agent orchestrates a team of specialists
(research, fundamentals, technicals, risk, a red-team, and more) that run in parallel,
propose trades with rationale, and **wait for your approval** before anything is placed.

It ships in **Phase 2** mode by default: the team researches and proposes, but every
order requires explicit human approval and starts small. You scale up only when *your
own track record* says you should.

---

## ⚠️ Read this first — disclaimer

> **This is not financial advice.** This software places, or helps you place, **real
> trades with real money**. You can lose some or all of it.
>
> - **LLM agents make mistakes.** They can hallucinate tickers or news, act on stale
>   data, misread instructions, and behave unexpectedly. Do not trust them blindly.
> - **Beating the S&P 500 consistently is very hard** — most professional managers
>   don't. Treat any outperformance as unproven until your journal shows otherwise, and
>   be willing to switch to plain index investing if it's winning.
> - **Robinhood does not supervise these agents.** Per Robinhood, you assume all risk
>   for orders placed by an agent connected to your account.
> - **Fund the agentic account only with money you can afford to lose entirely.**
> - This project is provided **as-is, with no warranty** (see [LICENSE](LICENSE)). The
>   authors and contributors are not liable for any financial losses.
>
> Start at Phase 0/1 (no real trades) if you're new. Keep the human-approval gate on.

---

## What this is (and isn't)

**It is:** a disciplined, auditable framework that removes emotional mistakes, enforces
hard risk limits, diversification, and position sizing, red-teams its own ideas, and
keeps a written record so you can honestly judge whether it's adding value.

**It is not:** a money printer or a guaranteed market-beating strategy. The
stock-picking "edge" is the weakest part of any LLM trading system — by the time news
is public it's largely priced in. The real value here is *process and risk control*.

---

## How it works — the agent team

A master agent (defined in `CLAUDE.md`) is the only one allowed to touch Robinhood's
trading tools. Each daily cycle, it dispatches specialists in parallel via Claude
Code's Task tool, synthesizes their findings, risk-checks and red-teams the ideas, then
presents a proposal and waits for you.

| Agent | Role |
|-------|------|
| **Portfolio Manager** (`CLAUDE.md`) | Master orchestrator. Owns the rulebook, makes the final call, the only agent that can place orders. |
| `macro-regime` | Reads the overall market: risk-on or risk-off backdrop. |
| `research-catalyst` | One per sector/ticker — news, catalysts, sentiment → bull/bear thesis + conviction score. |
| `fundamentals` | Valuation and financial health; separates substance from hype. |
| `technicals-timing` | Trend and entry timing; avoids buying blow-off tops. |
| `benchmark-performance` | Portfolio vs S&P 500 on a rolling, risk-adjusted basis. |
| `risk-management` | Concentration, sizing, drawdown — holds **veto power** over trades. |
| `devils-advocate` | Red-teams every proposed trade to catch hallucinations and hype. |
| `journaler` | Writes a dated audit trail of every decision to `journal/`. |

**Safety design baked in:** hard position/sector/cash caps, daily-loss and drawdown
circuit breakers, a risk-management veto, a red-team pass, a human-approval gate, and a
persistent journal — all configured in `config/investment-policy.md`.

---

## Prerequisites

- [Claude Code](https://code.claude.com) installed and authenticated (`claude --version`).
- A Claude plan that supports Claude Code (and, optionally, Remote Control for phone use).
- A **Robinhood Agentic** account (separate, ring-fenced; fund it small to start) and
  its MCP connection details — see Robinhood's official agentic-trading support guide.
- VS Code (optional but recommended) for editing and for the Pixel Agents visualizer.
- Node.js LTS (Claude Code requirement).

---

## Quickstart

```bash
# 1. Get the code
git clone <your-fork-url> agentic-trading
cd agentic-trading

# 2. Create your private config from the template
cp config/investment-policy.example.md config/investment-policy.md
#    then edit config/investment-policy.md — add your sectors/tickers and confirm caps.
#    (A filled example is in config/investment-policy.semiconductor.example.md)

# 3. Connect the Robinhood MCP (follow Robinhood's official connect guide)
claude mcp add ...   # use the URL/auth Robinhood gives you

# 4. Start Claude Code in this folder
claude
```

Then inside the Claude Code session:

```
/mcp        # confirm the Robinhood trading tools loaded — note their exact names
/agents     # confirm all 8 specialists loaded
```

See **[SETUP.md](SETUP.md)** for the detailed, click-by-click version (models, Pixel
Agents, scheduling, moving to Phase 3).

---

## 🚀 The starting prompt

**First prompt — orient and configure (do this before trading anything):**

```
Read CLAUDE.md and every file in .claude/agents/, then explain in plain terms how this
trading system works and how the agents coordinate. Then walk me through setting up
config/investment-policy.md for my interests: <e.g. semiconductors and clean energy>.
Do NOT propose or place any trades yet — I only want to understand and configure it.
```

**Operating prompt — run a cycle once you're configured:**

```
Run the daily cycle.
```

The master will pull your account, dispatch the team in parallel, red-team and
risk-check the ideas, then show you a proposal table and **stop**. Approve with e.g.
`approve 1,3`, `approve all`, or `skip`. Nothing is placed until you do.

---

## Control it from your phone (optional)

Claude Code's **Remote Control** lets you monitor and approve trades from the Claude
mobile app while the session keeps running on your machine. In your session, run `/rc`
(optionally `/rename` it first), then open the Claude app → **Code** tab → tap your
session. Every trade approval can be done from your phone. Note: your machine must stay
on, awake, and online; the terminal must stay open.

## Visualize the agents (optional)

The [Pixel Agents](https://github.com/pixel-agents-hq/pixel-agents) VS Code extension
renders each Claude Code terminal and its sub-agents as characters in a pixel office —
a fun way to watch the team work. It's observational only. **Do not** launch it with
`--dangerously-skip-permissions` for this project; you want approval prompts on.

---

## Repo structure

```
agentic-trading/
├── README.md                       # you are here
├── SETUP.md                        # detailed setup, scheduling, Phase 3
├── CONTRIBUTING.md
├── LICENSE                         # MIT (no warranty)
├── .gitignore                      # keeps secrets + personal trades out of git
├── CLAUDE.md                       # the master orchestrator + guardrails
├── config/
│   ├── investment-policy.example.md            # template — copy to investment-policy.md
│   └── investment-policy.semiconductor.example.md  # a filled-in example
├── .claude/agents/                 # the 8 specialist agents
└── journal/                        # dated decision log lands here (gitignored)
```

---

## Building on top of it

- **Add an agent:** drop a new markdown file in `.claude/agents/` with YAML frontmatter
  (`name`, `description`, `tools`, `model`) and a focused system prompt, then reference
  it from the daily cycle in `CLAUDE.md`. Restart the session to load it.
- **Change risk appetite:** edit the hard caps in your `config/investment-policy.md`.
- **Change cadence or models:** see SETUP.md.
- **Move toward autonomy (Phase 3):** only after the journal shows real, risk-adjusted
  value. SETUP.md explains exactly what to change — and why you should wait.

Ideas worth contributing: backtesting harness, broader data sources, a real dashboard,
tax-lot awareness, alternative brokers via MCP.

---

## Contributing

PRs welcome — see [CONTRIBUTING.md](CONTRIBUTING.md). Please keep the safety defaults
(human gate, hard caps, honest framing) intact in the main branch.

## License

[MIT](LICENSE). Provided as-is, with no warranty. Not financial advice. Trade at your
own risk.
