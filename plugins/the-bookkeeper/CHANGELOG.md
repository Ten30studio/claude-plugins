# Changelog — The Bookkeeper

All notable changes to The Bookkeeper plugin will be documented here.

## [1.0.2] — 2026-05-27

### Fixed
- Escape `$` signs in the catalog skill so price values render correctly in the Claude Code terminal (markdown renderer was interpreting bare `$X...$Y` as inline LaTeX math, making catalog prices invisible).
- Kept bare `$` in the other skills (record-deliverable, generate-statement, client-summary, agent) because those contain HTML/JSON templates Claude uses to generate output — escaping there would produce `\$4,280` literals in final statements.

## [1.0.1] — 2026-05-27

### Fixed
- Removed explicit `skills` and `agents` path overrides from `plugin.json`. Claude Code's plugin manifest schema validator rejected `"agents": "./agents/"` as invalid input. Default discovery in `./skills/` and `./agents/` still works without explicit declaration — these fields are optional path overrides, and we don't need to override the convention.

## [1.0.0] — 2026-05-27

### Added
- Initial release.
- **Catalog skill** — market-rate price reference covering ~22 common digital/agency services across Web & Digital, Brand & Identity, AI & Automation, Business Formation & Compliance, and Content & Research categories. Each entry includes mid-rate and source citation.
- **Record-deliverable skill** — capture client, service type, market value, courtesy discount, and net amount. Persists to `~/.bookkeeper/clients/{slug}/engagements.jsonl`.
- **Generate-statement skill** — produces a polished itemized HTML statement with sourced market rates and visible courtesy discounts. Statement numbering convention `TS-{YYYY}-{NNN}` (configurable prefix on first run).
- **Client-summary skill** — chronological engagement history per client with running totals; portfolio view across all clients.
- **Bookkeeper agent** — orchestrating persona that routes user requests to the appropriate skill.

### Notes
- All data persists locally under `~/.bookkeeper/`. Nothing is sent to external services.
- Designed to complement (not replace) payment processors. The Bookkeeper records value; processors collect money.
- First customer use case: Ten30 Studio's TS-2026-001 statement issued to Iscooling HVAC ($4,280 delivered, $4,280 courtesy, $0 due).
