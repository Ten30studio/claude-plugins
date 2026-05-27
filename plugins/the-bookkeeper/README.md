# The Bookkeeper

> Track the market-rate value of every service you deliver — even (especially) the work you give away.

**The Bookkeeper** is a Claude Code plugin built for productized agencies, solo consultants, freelancers, and AI-native operators who want to make the value of their work visible.

It produces itemized service-delivery statements with:
- **Market-rate pricing** from real sources (Upwork Pro, Fiverr Pro, industry surveys) — not aspirational, not deflated
- **Courtesy discounts visible** — when you gift work to a relationship-tier client, the value of that gift shows up explicitly
- **Source citations** on every line — *"$1,500–$3,500 per Upwork Pro; mid-point applied"*
- **Client-by-client engagement history** — every deliverable tracked, every statement numbered, full audit trail

## Why this exists

Most service-business operators undercharge their work or, worse, *give it away invisibly* — never letting the client see what was actually delivered against market rates. The Bookkeeper fixes both:

1. **For paying clients:** ensures pricing is grounded in real market data, not gut feel.
2. **For relationship-tier clients (the ones you give work to as a gift):** makes the gift visible. The recipient sees the $4,280 of professional services they received, even when invoiced at $0. The gift compounds into a real relationship asset instead of evaporating.

Built originally at [Ten30 Studio](https://ten30studio.com) as part of the [Kitt agent stack](https://github.com/Ten30studio), now packaged for everyone.

## Installation

```bash
# Add the Ten30 Studio plugin marketplace
/plugin marketplace add Ten30studio/claude-plugins

# Install The Bookkeeper
/plugin install the-bookkeeper@ten30studio
```

## What you get

Four skills + one orchestrating agent:

| Capability | Invoke when... |
|---|---|
| **`/the-bookkeeper:catalog`** | You want to look up a market rate, or you're pricing a service for a quote. |
| **`/the-bookkeeper:record-deliverable`** | You just finished work for a client. Log it before you forget. |
| **`/the-bookkeeper:generate-statement`** | You want to send a client an itemized statement (whether they're paying or not). |
| **`/the-bookkeeper:client-summary`** | You want the full engagement history for one client, or a portfolio view across all of them. |

The Bookkeeper agent (`@bookkeeper`) ties them together and will route to the right skill based on what you ask.

## Quick start

```bash
# Tell Claude: "I just delivered a custom website for Iscooling HVAC"
# → The Bookkeeper invokes record-deliverable, looks up market rate from the catalog,
#   asks about courtesy/discount status, writes to ~/.bookkeeper/clients/iscooling-hvac/engagements.jsonl

# Later: "Generate the statement for Iscooling"
# → The Bookkeeper produces TS-2026-001.html with the full itemized breakdown,
#   sourced market rates, and visible courtesy discount.

# Anytime: "What have we done for Iscooling?"
# → Full chronological engagement history with running totals.
```

## Data layout

The Bookkeeper stores everything locally on your machine. Nothing leaves your computer.

```
~/.bookkeeper/
├── clients/
│   ├── iscooling-hvac/
│   │   └── engagements.jsonl
│   └── alabaster-herbs/
│       └── engagements.jsonl
└── statements/
    ├── index.jsonl
    ├── TS-2026-001.html
    └── TS-2026-002.html
```

JSONL is append-only by convention. Your full engagement history is portable, scriptable, and git-friendly.

## The catalog

The Bookkeeper ships with a market-rate catalog covering ~22 common digital/agency services across:

- **Web & Digital** (landing pages, multi-page sites, copywriting, hosting, domains, SEO)
- **Brand & Identity** (lite + full identity packages, business cards)
- **AI & Automation** (agent setup, monthly operation, custom plugin builds)
- **Business Formation & Compliance** (LLC, EIN, GBP setup, license guidance)
- **Content & Research** (pitch decks, market briefs, outreach copy)

Each entry includes the **mid-rate** and the **source range** with citation. The catalog refreshes roughly quarterly.

See `skills/catalog/SKILL.md` for the full table.

## Statement numbering

Default convention: `TS-{YYYY}-{NNN}` (TS = "The Studio" / Ten30 Studio). On first run, The Bookkeeper asks for your preferred prefix and remembers.

Example: `TS-2026-001`, `TS-2026-002`, ...

## What The Bookkeeper does NOT do

- Does not collect payment. Use Stripe, LemonSqueezy, Paystack, etc. for that.
- Does not decide discounts. You decide; The Bookkeeper records.
- Does not advise on tax, legal, or compliance. Those go through your accountant or counsel.

## Roadmap

V1.0 (this release):
- Market-rate catalog with ~22 services
- Record deliverable + courtesy tracking
- Generate itemized HTML statement
- Client engagement summary + portfolio view

V2.0 (planned):
- Dynamic rate-fetching (live update of catalog from internet sources)
- Auto-statement generation triggered by deliverable patterns
- PDF export
- CSV/QuickBooks export for tax season
- Multi-currency support (USD, NGN, GBP, EUR)
- Integration with The Counsel agent for tax/compliance gate

## License

MIT.

## About Ten30 Studio

Ten30 Studio is an AI-native production house building the operating system for chat-first small businesses. Learn more at [ten30studio.com](https://ten30studio.com).

If you find The Bookkeeper useful, we'd love to hear about it: `admin@ten30studio.com`.
