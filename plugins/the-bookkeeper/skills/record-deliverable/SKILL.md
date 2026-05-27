---
name: record-deliverable
description: Record a service deliverable for a client. Use when the user says "log this for [client]," "I just delivered X," "record this work," "add to [client]'s engagement," or otherwise indicates they completed work for a client they want tracked. Captures service type, client, value (referencing the catalog), date, courtesy/discount status. Persists to a local engagement log so The Bookkeeper can later generate itemized statements.
---

# Record a Deliverable

Use this when the user has just delivered work for a client and wants it tracked. The Bookkeeper records every deliverable with its market value — even (especially) work being given away as a courtesy.

## How to use this skill

When invoked, gather the following from the user (or from context if available):

| Field | Required | Notes |
|---|---|---|
| **Client name / identifier** | Yes | The business or person the work was done for. |
| **Service type** | Yes | Should map to the [[catalog]] skill if possible. If novel, accept user's description. |
| **Date delivered** | Yes | Defaults to today if not specified. |
| **Market value** | Yes | Reference [[catalog]] for mid-rate, or accept user-specified. |
| **Courtesy / discount amount** | No | Default $0. If the work was a gift, partial gift, or write-off — capture the discount amount and the reason. |
| **Net amount** | Computed | `market_value - courtesy_amount`. Often $0 for relationship-tier clients. |
| **Notes / context** | Optional | Why the work was done, what was scoped, any details for the statement narrative. |

## Where to write

The engagement log lives at:

```
~/.bookkeeper/clients/{client-slug}/engagements.jsonl
```

Where `{client-slug}` is the client name lowercased with hyphens (e.g., `iscooling-hvac`, `alabaster-herbs-and-apothecary`).

If the directory doesn't exist, create it.

Each engagement is one JSON line with this schema:

```json
{
  "ts": "2026-05-27T14:00:00Z",
  "date_delivered": "2026-05-27",
  "client": "Iscooling HVAC",
  "client_slug": "iscooling-hvac",
  "service": "Custom small-business website",
  "category": "Web & Digital",
  "market_value_usd": 2200,
  "courtesy_amount_usd": 2200,
  "net_amount_usd": 0,
  "courtesy_reason": "Relationship-tier client; launch gift",
  "source_citation": "$1,500-$3,500 per Upwork Pro / Fiverr Pro; mid-point applied",
  "notes": "Single-page responsive site. Custom HVAC palette. Deployed to iscooling.com.",
  "statement_id": null
}
```

`statement_id` is null until this engagement is included on an itemized statement (then it gets the statement number).

## Confirmation

After writing, confirm to the user:

```
✓ Recorded: [service] for [client]
  Market value:      $X,XXX
  Courtesy discount: -$X,XXX
  Net amount:        $X.XX
  Logged to:         ~/.bookkeeper/clients/[slug]/engagements.jsonl
```

If the value was a courtesy gift, **emphasize the value** the user is providing — that's the whole point of The Bookkeeper. The gift should feel real.

## Edge cases

- **Recurring service?** Record one entry per delivery cycle (e.g., "monthly hosting" gets one entry per month).
- **Multi-line engagement?** Record each line as a separate entry (one for the website, one for the copywriting, one for the brand identity). The statement will compile them.
- **Outside the catalog?** Record the user's description and value. Optionally note `category: "uncatalogued"` so a future catalog refresh can normalize it.
- **Adjusting after the fact?** The log is append-only. If a value was wrong, write a correction entry with notes referencing the original.
