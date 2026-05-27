---
name: client-summary
description: Summarize all the work that has been delivered to a single client over time. Use when the user asks "what have we done for [client]," "show me the engagement history," "how much have I given [client]," "what's the running total." Reads the client's engagement log and produces a chronological summary with running totals of value delivered, courtesy applied, and net amount.
---

# Client Engagement Summary

When the user wants to see the full picture of what's been delivered to one client — across all engagements, all statements, all time — this skill produces a clean chronological summary.

## Workflow

### 1. Identify the client

Ask for or extract the client name. Match to a client slug under `~/.bookkeeper/clients/{slug}/engagements.jsonl`.

### 2. Read all engagements

Load every engagement from the client's JSONL log. Sort chronologically by `date_delivered`.

### 3. Render the summary

Present as a clean markdown summary:

```
## Engagement Summary · {CLIENT}

**First engagement:**    {DATE}
**Most recent:**         {DATE}
**Total engagements:**   {COUNT}
**Statements issued:**   {STATEMENT_COUNT}

---

### Running totals

| Metric | Value |
|---|---|
| Total value delivered (market rates) | ${TOTAL_VALUE} |
| Total courtesy applied | -${TOTAL_COURTESY} |
| Total invoiced (net) | ${TOTAL_INVOICED} |
| Currently on open statements | ${OPEN_INVOICED} |
| Paid (per user records) | ${PAID} |
| Outstanding | ${OUTSTANDING} |

---

### Engagement log (chronological)

| Date | Service | Market | Courtesy | Net | Statement |
|---|---|---|---|---|---|
| 2026-05-26 | Strategic business consultation | $450 | -$450 | $0 | TS-2026-001 |
| 2026-05-26 | Custom HVAC business website | $2,200 | -$2,200 | $0 | TS-2026-001 |
| 2026-05-26 | Website copywriting | $450 | -$450 | $0 | TS-2026-001 |
| ... | ... | ... | ... | ... | ... |

---

### Statements issued

| ID | Date | Client | Value | Courtesy | Due |
|---|---|---|---|---|---|
| TS-2026-001 | 2026-05-26 | Iscooling HVAC | $4,280 | -$4,280 | $0 |
```

### 4. Offer next actions

After the summary, offer:
- *"Generate a new statement for engagements not yet billed?"*
- *"Export this summary as PDF?"*
- *"Show me the full statement TS-2026-XXX?"*

## Multi-client view (bonus)

If the user asks for a portfolio-level view ("show me everyone" / "all clients" / "running portfolio total"), aggregate across `~/.bookkeeper/clients/*/engagements.jsonl` and show:

| Client | Engagements | Value Delivered | Courtesy | Net |
|---|---|---|---|---|
| Iscooling HVAC | 7 | $4,280 | -$4,280 | $0 |
| Alabaster Herbs | 4 | $3,150 | -$2,400 | $750 |
| ... | ... | ... | ... | ... |
| **TOTAL** | **23** | **$11,420** | **-$8,200** | **$3,220** |

This gives the user a portfolio-level view of value delivered vs invoiced — useful for tracking the productized-agency thesis at the macro level.
