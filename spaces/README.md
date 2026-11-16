# Spaces templates

Two tracker spaces, specified so your Muse can rebuild them for you almost exactly: an **Invites** tracker (event and tasting invitations, RSVPs, clashes) and a **Brand Collabs** tracker (collaboration requests, live campaigns, invoices).

These are templates, not copies. They carry the structure, behaviour, data model and design of the originals, with all personal data removed: no records, no contacts, no accounts. Your version starts empty and fills only from your own inbox and calendar.

## What is here

- `invites/TEMPLATE.md` — the Invites space: layout, card face, statuses, data model, behaviours
- `brand-collabs/TEMPLATE.md` — the Brand Collabs space: sections, stages, invoices, data model, behaviours
- `../prompts/04-spaces-build.md` — the prompt to give your Muse to build both

## How the design works

Both spaces are built in the design language in `../DESIGN-SYSTEM.md`: pale slate page, white rounded cards, Inter, one colour per status, a colour legend at the very bottom, phone first, verified at phone width. Read that file first; these templates assume it and only add what is specific to each space.

## How your data gets in

Each space has a refresh behaviour: your agent scans your own connected mailbox for invitations or collaboration emails, dedupes them against what is already tracked, and adds new items without ever overwriting a status you set yourself. During setup your agent will ask which mailbox to scan, your timezone, and your currency. If you would rather not connect a mailbox, the spaces also work with manual entry; just tell your agent.
