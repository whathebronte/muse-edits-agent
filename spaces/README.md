# Spaces templates

Three spaces, specified so your Muse can rebuild them for you almost exactly: an **Invites** tracker (event and tasting invitations, RSVPs, clashes), a **Brand Collabs** tracker (collaboration requests, live campaigns, invoices), and an **Edits Queue** (the review board for editing work: a job queue plus every delivered piece as a card you rule on, wired two ways between the board, your agent's builds and your chat).

These are templates, not copies. They carry the structure, behaviour, data model and design of the originals, with all personal data removed: no records, no contacts, no accounts. Your versions start empty and fill only from your own inbox, calendar, builds and verdicts.

## What is here

- `invites/TEMPLATE.md` — the Invites space: layout, card face, statuses, data model, behaviours
- `brand-collabs/TEMPLATE.md` — the Brand Collabs space: sections, stages, invoices, data model, behaviours
- `edits-queue/TEMPLATE.md` — the Edits Queue space: the queue and its one-running-job lock, the review sections, the published actions, and the two-way wiring (event-driven patching, the polling relay, and chat write-back)
- `../prompts/04-spaces-build.md` — the prompt to give your Muse to build all three

## How the design works

All three spaces are built in the design language in `../DESIGN-SYSTEM.md`: pale slate page, white rounded cards, Inter, one colour per status, a colour legend at the very bottom, phone first, verified at phone width. Read that file first; these templates assume it and only add what is specific to each space.

## How your data gets in

Invites and Brand Collabs each have a refresh behaviour: your agent scans your own connected mailbox for invitations or collaboration emails, dedupes them against what is already tracked, and adds new items without ever overwriting a status you set yourself. During setup your agent will ask which mailbox to scan, your timezone, and your currency. If you would rather not connect a mailbox, the spaces also work with manual entry; just tell your agent.

The Edits Queue fills differently, and that difference is the point of it. Nothing scans a mailbox for it. The editing agent's builds patch it as events happen (a job starts, exports finish, a piece lands in Ready for review), a relay polls it and forwards your taps to the agent's chat, and verdicts you give in chat are written back onto it. Its template specifies the whole loop.
