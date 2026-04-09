# Changelog

## v2 — 2026-04-09

Hardened pass over the walkthrough after Jason reviewed the original `peptide-install-prompt.md`. Goal: every Claude Code prompt in the walkthrough is actually runnable by Claude Code on a leader's Mac without hitting a prohibited syscall, an unverified CLI subcommand, or a sudo password prompt.

### Fixed — Claude Code could not complete these steps in v1

- **Phase 6.2 "Install Tailscale"** — was a Claude Code prompt, but `brew install --cask tailscale` and `sudo tailscale up` require sudo. Now a clearly-labeled **manual** step with copy-paste Terminal commands.
- **Phase 6.3 "Keep Mac Running 24/7"** — was a Claude Code prompt using `sudo pmset -a sleep 0`. Claude Code is not allowed to modify system power settings. Now a **manual** step, and marked **optional** with a softer `caffeinate -i -t 28800` alternative for leaders who don't want to permanently disable sleep.

### Fixed — unverified `openclaw` CLI syntax

Several subcommands in v1 weren't verified against the real OpenClaw CLI and would have errored out on a leader's Mac. Corrected against the verified surface documented at [npmjs.com/package/openclaw](https://www.npmjs.com/package/openclaw), [github.com/openclaw/openclaw](https://github.com/openclaw/openclaw), and the HackerNoon setup guide.

- **Phase 2.3 gateway setup** — `openclaw gateway` → `openclaw gateway start`; added verified `openclaw config set gateway.bind loopback` and `openclaw config set gateway.auth.mode token`; added `openclaw status --all` for the full diagnostic; added a fallback to `openclaw --help` if any command errors.
- **Phase 3.3 Discord connection** — removed the speculative `openclaw config set channels.discord.token --ref-provider default --ref-source env --ref-id` chain. Now uses an env var in `~/.zshrc` plus the verified interactive `openclaw configure` flow, with a fallback to direct edits of `~/.openclaw/openclaw.json`.
- **Phase 4.1-4.3 agent creation** — replaced unverified `openclaw agents add <name> --workspace <path>` with direct edits to the `agents` section of `~/.openclaw/openclaw.json`, which is the documented watched config path. Same SOUL.md content, different registration method.
- **Phase 5.2 verify bindings** — removed unverified `openclaw agents list --bindings`. Uses `openclaw status --all` + `openclaw doctor` + direct inspection of the `bindings` section of `openclaw.json` instead.
- **Phase 7.2 health check** — same fix as 5.2.

### Added

- **Phase 1.5** now installs `gh` (GitHub CLI) alongside git/jq/wget so leaders aren't stuck at a password prompt the next time they push to a repo. Also makes the Xcode command line tools installer explicit (`xcode-select --install`).
- **Phase 6 header** — marked **optional** with a new explainer paragraph. Two new warning boxes:
  - ⚠️ **Per-machine keys only** — leaders must each get their own Tailscale auth key. Never share keys across Macs (one leak = all Macs compromised).
  - 🔒 **Claude Code can't run sudo** — explains up front why the remaining Phase 6 steps are manual.
- **Phase 8 — Weekly Kaizen Loop** (new) — ported from the old `peptide-install-prompt.md` STEP 7. Adds a 5-minute setup with three steps:
  - 8.1 (manual) Create a Discord webhook for `#chief-of-staff`
  - 8.2 (Claude Code) Write `~/scripts/kaizen-friday.sh`, chmod it, add a `0 9 * * 5` cron entry, and fire a test ping
  - 8.3 (manual) Verify the test ping landed in Discord
- **v2 footer signature** — small monospace line at the bottom of the page so future redeploys can verify the v2 content is actually what Pages is serving (`grep "walkthrough v2"` on the live URL).
- **Daily Use cheat sheet** now mentions the Friday Kaizen ping.
- **TOC** updated with Phase 8.

### Deliberately NOT added (deferred to a possible v3)

- **Nightly GitHub backup of `~/.openclaw/`** — adds per-leader repo creation + PAT auth friction, too heavy for a lean v2. Leaders can add this themselves later if they want it.
- **`jason-admin` remote user / dscl account creation** — correctly dropped from the HTML walkthrough in v1 and staying dropped. Claude Code is not allowed to create macOS user accounts or modify security/admin group membership. If remote support is needed, Tailscale + a leader's own user account is sufficient.
- **Extra channels (Telegram, WhatsApp, Slack)** — OpenClaw supports them but v2 stays focused on Discord.
- **Agent skill extensions** — future topic.

### What didn't change

- Visual style, fonts, hero, TOC chrome, copy-button JS.
- The 3-agent architecture (Security / Systems / CoS) and all the SOUL.md content.
- Phases 1.1 — 1.4 (Terminal / Homebrew / Node + Claude Code / Log In).
- Phase 2.1 (OpenClaw install + `openclaw onboard --install-daemon`).
- Phase 2.2 (Anthropic provider connection).
- Phases 3.1, 3.2, 3.4 (Discord server / bot creation / test connection).
- Phase 4's SOUL.md content for all 3 agents.
- Phase 5.1 bindings prompt (was already correct — edits `openclaw.json` directly).
- Phase 7.1 test prompts for each channel.
