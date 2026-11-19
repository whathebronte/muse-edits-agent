# Prompt: build the Invites, Brand Collabs and Edits Queue spaces

Paste this to your Muse after the design system is in place (see the design prompt in the repo README, or `../DESIGN-SYSTEM.md`). It builds all three spaces, empty, from the templates in `../spaces/`. The Edits Queue also needs its wiring set up, not just the space built; that part belongs with the practice system setup, and this prompt covers it so the board works from day one.

---

Build me three spaces from this repo: https://github.com/whathebronte/muse-edits-agent

Read DESIGN-SYSTEM.md in full first, then spaces/invites/TEMPLATE.md, spaces/brand-collabs/TEMPLATE.md and spaces/edits-queue/TEMPLATE.md. Build me an Invites space, a Brand Collabs space and an Edits Queue space that follow those templates almost exactly: same layout, same card faces, same statuses and colours, same behaviours, in the design language of DESIGN-SYSTEM.md.

Rules:
- All three spaces start empty. Never invent records, contacts or sample data for me; at most, clearly fake placeholders that I approve first.
- Before building the refresh, ask me: which mailbox or mailboxes to scan, my timezone, and (for Brand Collabs) my main currency and any others my fees arrive in. If I do not want a mailbox connected, build them for manual entry instead.
- The behaviours in the templates are the point, not decoration: a status or stage I set is never overwritten by a refresh, hidden items stay hidden across refreshes, fees are stored exactly as stated and never inferred, duplicates merge instead of piling up.
- The Edits Queue is different from the other two: nothing refreshes it from a mailbox. It fills from the editing agent's builds and from my taps, through the wiring in its template. So as part of this build: create the space with its published actions (get board, add job, start next, mark export done, mark second pass done, clear job, remove job, add work item, get work items, submit verdict, save feedback), then wire the two directions: a scheduled relay that polls the board and forwards my taps to the editing agent's chat, deduped against a state file, and the write-back behaviour, where a verdict or feedback note I give in chat is written to the board in the same flow and the relay state file is synced for exactly those items so my own instruction is not echoed back as a fresh tap. Ask me which chat the relay delivers to, and where the relay state file should live.
- The queue's lock is part of the build, not a nicety: one running job at a time, no starting the next job while the running one lacks its export and second pass marks, no clearing a job before both marks are set.
- Start my UI learnings doc from templates/UI-LEARNINGS.md if it does not exist yet, and keep it updated with my feedback on these spaces.
- Verify each space at phone width exactly as DESIGN-SYSTEM.md specifies, checking collapsed and expanded cards, before you call any of them done.
- Show me each finished space once, with a short summary of what is in it and what my refresh (or, for the Edits Queue, the wiring) will do.
