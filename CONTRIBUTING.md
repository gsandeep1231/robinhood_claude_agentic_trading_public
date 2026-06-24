# Contributing

Thanks for your interest in improving this starter kit.

## Ground rules

This project handles **real money**, so the safety defaults are not negotiable in the
main branch:

- The **human-approval gate** (Phase 2) stays the default.
- **Hard caps and circuit breakers** in the IPS stay enforced.
- The **honest framing** stays — no marketing the system as a guaranteed market-beater.
- Never commit secrets, broker credentials, a real `.mcp.json`, or a personal
  `config/investment-policy.md`. The `.gitignore` is set up to help; don't override it.

## Good contributions

- A backtesting / paper-trading harness so people can evaluate before risking money.
- Additional or improved specialist agents (keep them read-only unless there's a strong
  reason; only the master places trades).
- Better data sources for the research/fundamentals agents.
- A real monitoring dashboard (the journal is currently the source of truth).
- Tax-lot awareness, alternative brokers via MCP, additional asset classes.

## How to contribute

1. Fork and create a feature branch.
2. Keep changes focused; explain *why* in the PR, especially anything touching risk
   logic or the execution path.
3. If you change agent behavior, update the relevant docs (`README.md` / `SETUP.md`).
4. Open a PR. Be patient and kind in review — this is a community starting point.

## Disclaimer

By contributing you agree your contributions are provided under the project's
[MIT License](LICENSE), as-is and without warranty. This project is not financial
advice.
