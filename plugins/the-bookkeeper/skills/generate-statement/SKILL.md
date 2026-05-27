---
name: generate-statement
description: Generate an itemized service-delivery statement for a client. Use when the user asks to "create a statement," "send the invoice," "generate the bill," "show what we've done for [client]," or wants to produce a formal record of work delivered. Reads the client's engagement log, compiles into a polished HTML invoice with market-rate citations and courtesy discounts visible. Statement numbering is sequential (TS-{YYYY}-{NNN}).
---

# Generate an Itemized Statement

Compile all the engagements for a single client into a formal service-delivery statement. The Bookkeeper's job here is to **make the value visible** — itemize the work, cite market rates, show discounts/write-offs honestly, present the net amount due.

## When to use

- User asks for an invoice, statement, or bill for a specific client.
- User wants to show a client the value they've received (especially when net is $0).
- User wants a clean record of work delivered (for tax, audit, portfolio, future renegotiation).

## Workflow

### 1. Identify the client

Ask: *"Which client's statement should I generate?"* — or use context if a client was already specified. Match to a client slug under `~/.bookkeeper/clients/`.

### 2. Determine statement scope

By default, include **all engagements** for that client that don't yet have a `statement_id` assigned. If the user specifies a date range, filter accordingly.

### 3. Pick a statement number

The convention is `{PREFIX}-{YYYY}-{NNN}` where:
- `PREFIX` defaults to `TS` (Ten30 Studio) or the user's preferred prefix (ask or use prior history)
- `YYYY` is the current calendar year
- `NNN` is sequential within year

To find the next sequential number, check `~/.bookkeeper/statements/index.jsonl` for the highest NNN this year and add 1. If no prior statements this year, start at 001.

### 4. Compile the statement

For each engagement in scope, produce a line with:
- Service name (bold)
- Description (1-2 sentences)
- Source citation (italic, smaller — *"Source range: $X-$Y per [vendor]; mid-point applied"*)
- Market rate (right-aligned)
- Courtesy discount (right-aligned, green if non-zero, "-$X")
- Net (right-aligned, **bold** if non-zero, "$0" if fully courtesy)

Sum the totals.

### 5. Render to HTML

Write the statement to:

```
~/.bookkeeper/statements/{statement-id}.html
```

Use this HTML structure (clean, printable, brand-neutral by default — the user can override styling):

```html
<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8" />
<title>Statement {STATEMENT_ID} — {CLIENT}</title>
<style>
  body { font-family: -apple-system, BlinkMacSystemFont, "SF Pro Text", "Inter", sans-serif;
         color: #1a1a1a; max-width: 840px; margin: 0 auto; padding: 3rem 2.5rem; font-size: 14px; line-height: 1.5; }
  h1, h2 { font-weight: 600; letter-spacing: -0.01em; }
  .header { display: flex; justify-content: space-between; border-bottom: 2px solid #1a1a1a; padding-bottom: 1.5rem; margin-bottom: 2rem; }
  .summary { background: linear-gradient(135deg, #f4f6fa, #e8f0fa); border: 1px solid #d9dde5; border-radius: 10px;
             padding: 1.4rem 1.6rem; margin-bottom: 2rem; display: flex; justify-content: space-between; gap: 1.5rem; }
  .summary .val { font-size: 1.8rem; font-weight: 700; }
  .summary .val.green { color: #2e8b57; }
  table { width: 100%; border-collapse: collapse; }
  thead th { background: #1a1a1a; color: white; padding: .7rem; text-align: left; font-size: .72rem;
             letter-spacing: .12em; text-transform: uppercase; }
  tbody td { padding: .9rem .8rem; border-bottom: 1px solid #d9dde5; vertical-align: top; }
  td.r { text-align: right; font-variant-numeric: tabular-nums; }
  td.r.discount { color: #2e8b57; }
  td.r.net { font-weight: 600; }
  .source { color: #888; font-size: .78rem; font-style: italic; margin-top: .25rem; }
  .totals { margin-top: 1.5rem; display: flex; justify-content: flex-end; }
  .totals-table { min-width: 340px; }
  .row { display: flex; justify-content: space-between; padding: .5rem 0; }
  .row.due { border-top: 2px solid #1a1a1a; padding-top: 1rem; font-size: 1.2rem; font-weight: 700; }
  .note { background: #fffbf1; border-left: 4px solid #c99e5e; padding: 1.2rem 1.4rem; margin-top: 2.5rem; border-radius: 6px; }
</style>
</head>
<body>
  <div class="header">
    <div><h1>{ISSUER_NAME}</h1><div style="color:#888;">Service Delivery Statement</div></div>
    <div style="text-align:right;">
      <strong>STATEMENT #{STATEMENT_ID}</strong><br/>
      Issue date: {ISSUE_DATE}<br/>
      To: {CLIENT}
    </div>
  </div>
  <div class="summary">
    <div><div style="font-size:.75rem;text-transform:uppercase;color:#888;">Value Delivered</div><div class="val">${TOTAL_VALUE}</div></div>
    <div><div style="font-size:.75rem;text-transform:uppercase;color:#888;">Courtesy</div><div class="val green">-${TOTAL_COURTESY}</div></div>
    <div><div style="font-size:.75rem;text-transform:uppercase;color:#888;">Amount Due</div><div class="val">${TOTAL_DUE}</div></div>
  </div>
  <table>
    <thead><tr><th>Service</th><th style="text-align:right;">Market Rate</th><th style="text-align:right;">Courtesy</th><th style="text-align:right;">Net</th></tr></thead>
    <tbody>
      <!-- one <tr> per engagement -->
    </tbody>
  </table>
  <div class="totals"><div class="totals-table">
    <div class="row"><span>Subtotal</span><span>${TOTAL_VALUE}</span></div>
    <div class="row" style="color:#2e8b57;"><span>Courtesy</span><span>-${TOTAL_COURTESY}</span></div>
    <div class="row due"><span>Amount due</span><span>${TOTAL_DUE}</span></div>
  </div></div>
  <div class="note">{NOTE_TO_CLIENT}</div>
</body>
</html>
```

### 6. Update the engagement log

For each engagement now included on this statement, write its `statement_id` back to the JSONL entry (append a corrected line — log is append-only).

### 7. Update the master statements index

Append to `~/.bookkeeper/statements/index.jsonl`:

```json
{"statement_id": "TS-2026-001", "client_slug": "iscooling-hvac", "client": "Iscooling HVAC",
 "issue_date": "2026-05-27", "total_value_usd": 4280, "total_courtesy_usd": 4280, "total_due_usd": 0,
 "engagement_count": 7, "status": "courtesy-fully-discounted",
 "file": "~/.bookkeeper/statements/TS-2026-001.html"}
```

### 8. Report back to the user

Show a compact summary:

```
✓ Statement TS-2026-001 generated for Iscooling HVAC

  Engagements:      7
  Value delivered:  $4,280
  Courtesy:         -$4,280
  Amount due:       $0.00

  File: ~/.bookkeeper/statements/TS-2026-001.html

  Open in browser:  open ~/.bookkeeper/statements/TS-2026-001.html
```

If the statement has a non-zero balance, also offer: *"Want me to draft an email to the client with the statement attached?"*

## Style notes

- Default issuer name is the user's name or "Ten30 Studio" — ask once on first run and remember.
- Default closing note ("Note to client") for courtesy-zero-balance: *"This statement itemizes the professional services delivered to [client] as part of [engagement], valued at $X against current market rates. The full amount is provided as a courtesy — no payment is requested. This document exists so you can see exactly what's been built for you, and what it would have cost on the open market."*
- For invoice mode (non-zero balance): use neutral payment-terms language and include payment instructions or links if the user provides them.
- Always use clean, professional HTML — no emoji in the statement itself unless explicitly requested.
