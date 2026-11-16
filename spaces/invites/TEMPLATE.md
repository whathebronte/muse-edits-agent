# Invites space: template

A tracker for invitations: media events, tastings, previews, launches. It answers three questions at a glance: what needs my reply, what is coming up, and what clashes.

Build it in the design language in `../../DESIGN-SYSTEM.md`. This file adds only what is specific to this space.

## Layout, top to bottom

1. **Header card:** wordmark `invites.` (lowercase, green dot) and a small status line (last refreshed, or an error in dark red).
2. **Closing soon strip:** at most 3 cards for items whose RSVP deadline is near, soonest first. Hidden when nothing is closing soon.
3. **Tastings section, then Events section:** the live items as a stack of cards, ordered as below.
4. **Already passed and closed group:** collapsed, below the live sections.
5. **Hidden group:** collapsed, at the bottom, with unhide.
6. **Colour legend:** at the very bottom of the page, dots and short labels only.

## The card

**Face, in this fixed order, nothing else:**
- Title
- When (a friendly date label, e.g. "Tue 3 Dec, 7pm")
- Where (venue name, area)
- Then one callout in its fixed spot: a status pill, an RSVP chip ("RSVP by Sun 6pm"), an "Already passed" tag, or a clash flag ("Flag: Clashes with [other event]")

Two-line clamp on any preview text. Tap anywhere on the face except Hide expands the card inline; only one card expanded at a time; the expanded card stays inside the normal column.

**Expanded card adds:** host, contact name and organisation, plus-one (whether one is offered or confirmed), the ask (what the host wants: attendance, a post, coverage), notes as a label plus one short fact per line (never a paragraph block), the clash note if any, and the actions: set status, hide, and "Ask [assistant] in chat" for reply help (it prepares a handoff summary of the invite in your main chat, where the reply itself is drafted and sent only with your approval).

## Statuses and colours

| Status | Colour | Meaning |
|---|---|---|
| open | yellow | new invite, awaiting your reply |
| accepted | green | you are going |
| replied | blue | replied, scheduled or waiting on the host |
| declined | red | you passed |
| closed | neutral grey | RSVP window closed |
| archived | neutral grey | past or no longer relevant |
| hidden | neutral grey | you hid it: treated as passed |

Red is also used for a passed RSVP deadline and for clash flags.

**Ordering:** items you have acted on (accepted, replied) sit above untouched open ones within each section; after that, by event date. Closing soon is purely by RSVP deadline.

## Data model

One record per invite:

- kind: tasting or event
- title
- host
- event date and time, plus a display date label
- venue
- RSVP deadline, plus a display label
- status (above)
- plus one: offered / confirmed / none
- ask: what the host is asking for
- contact name, contact organisation
- notes
- clash note
- source: which message it came from (for dedupe)
- dedupe key, unique

Plus a small meta store (last refreshed time, handoff state).

## Behaviours that matter

- **Your status is yours.** Refresh and import never overwrite a status, hidden flag or note you set. They only add genuinely new invites and update facts (date, venue, deadline) on items you have not decided.
- **Dedupe properly:** same kind plus title is the same invite; also treat as duplicates when kind and date match and either the host matches or the titles overlap heavily. Duplicates merge, keeping your status.
- **Hidden means passed.** A hidden invite is never flagged to you again, including near its RSVP deadline. Hidden is not the same as attended: confirmations and logistics for events you accepted still surface normally.
- **Clashes:** on refresh, compare event dates against your calendar and set the clash flag with the other event's name.
- **Refresh:** scans your connected mailbox(es) for new invitations, tastings and RSVP mail since the last refresh, applies the rules above, and reports only what changed.
- **Reply help:** the chat handoff carries the invite's facts to your main chat. Drafts are made there, shown to you, and sent only after your approval.

## Setup parameters (your agent asks you)

- Which mailbox(es) to scan for invites
- Your calendar, for clash checks
- Your timezone

## Starting state

Empty. No sample records, or at most clearly fake placeholders you approve. The space fills from your first refresh or your manual entries.
