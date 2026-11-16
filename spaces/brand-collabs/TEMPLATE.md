# Brand Collabs space: template

A pipeline tracker for brand collaborations: what has come in, what is live, what is finished, and who still owes you money.

Build it in the design language in `../../DESIGN-SYSTEM.md`. This file adds only what is specific to this space.

## Layout, top to bottom

1. **Header card:** wordmark `brandcollabs.` (lowercase, green dot) and a small status line (last refreshed, or an error in dark red).
2. **Incoming requests section:** new pitches awaiting your decision.
3. **Ongoing section:** accepted, live collaborations.
4. **Invoices section:** with the outstanding total shown at the top of the section.
5. **Completed group:** done and passed, collapsed below.
6. **Hidden group:** collapsed, at the bottom, with unhide.
7. **Colour legend:** at the very bottom of the page.

## The collaboration card

**Face, in this fixed order, nothing else:**
- Brand
- Title, cleaned for display (payment wording such as "Paid" is stripped from the stored title when shown)
- One preview line (the deliverable or the latest state)
- Fee callout, always in the same spot on the card

Tap anywhere on the face except Hide expands the card inline; only one expanded at a time; the expanded card stays inside the normal column.

**Expanded card adds:** agency if any, contact name and email, fee exactly as stated, deliverables, dates, product details (its own expandable block), campaign tips from the brand (its own expandable block), notes as a label plus one short fact per line, and the actions: move stage, hide, and "Ask [assistant] in chat". That button prepares a handoff in your main chat with one of five intents: discuss, accept, decline, ask, or chase. The message itself is drafted there and sent only with your approval.

## Stages and colours

| Stage | Colour | Meaning |
|---|---|---|
| incoming | yellow | new request, awaiting your decision |
| ongoing | green | accepted and live |
| done | neutral grey | delivered and closed |
| passed | neutral grey | you declined or it lapsed |

**Ordering:** paid work first, highest fee at the top, then by date. Incoming is by newest first within that logic.

## Invoices

Each invoice: number, collaboration it belongs to, amount and currency, status, due date, paid date.

| Status | Colour | Meaning |
|---|---|---|
| draft | neutral grey | not sent yet |
| sent | blue | sent, awaiting payment |
| paid | green | paid |
| overdue | red | past the due date and unpaid |

Overdue is derived from the due date, never set by hand. The Invoices section header shows the total outstanding (sent plus overdue) in your main currency.

## Data model

One record per collaboration:

- brand, title
- agency (optional)
- contact name, contact email
- fee, stored as the exact text it arrived as (e.g. "SGD 1,500", "USD 800 + product")
- deliverables
- stage (above)
- product details, campaign tips
- notes
- hidden flag
- source: which message it came from (for dedupe)

One record per invoice, as above. Plus a small meta store (last refreshed time, handoff state, hidden identities for dedupe).

## Behaviours that matter

- **Your stage is yours.** Refresh and import never overwrite a stage, invoice status or hidden flag you set.
- **Fees are never inferred.** The fee is stored exactly as the brand stated it. If no fee was stated, the field stays empty; your agent never guesses one from context.
- **Hidden persists across refreshes.** A hidden collaboration is matched on re-import by identity (brand plus contact email plus a shared date, or the same source message) and stays hidden, so a refreshed inbox never resurrects something you buried.
- **Refresh:** scans your connected mailbox(es) for new collaboration pitches, deliverable updates and invoice mail since the last refresh, dedupes on identity, and reports only what changed.
- **Titles are stored as received**; only the display strips payment wording.

## Setup parameters (your agent asks you)

- Which mailbox(es) to scan for collaboration and invoice mail
- Your timezone
- Your main currency, and any other currencies your fees arrive in

## Starting state

Empty. No seed records: a template that ships with someone else's brands, contacts or invoices would be a leak, not a shortcut. The space fills from your first refresh or your manual entries.
