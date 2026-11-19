# Edits Queue space: template

The durable home of all editing work: a job queue on top, and below it every delivered piece as a review card the creator rules on with one tap. It is the third leg of the editing system, wired two ways: the agent's builds patch it as they go, and the creator's verdicts, whether tapped here or spoken in chat, land on it and flow back to the agent.

Build it in the design language in `../../DESIGN-SYSTEM.md`. This file adds only what is specific to this space, plus the wiring, which is the actual point of it.

## Layout, top to bottom

1. **Header card:** wordmark `editsqueue.` (lowercase, green dot).
2. **Queue section:** the one running job, then the waiting jobs in order.
3. **Ready for review:** delivered pieces awaiting the creator's verdict.
4. **Changes requested:** pieces the creator sent back, with their notes.
5. **Passed:** approved pieces.
6. **Did not pass:** rejected pieces, each with the creator's one line reason.
7. **Colour legend:** at the very bottom of the page.

Work items are grouped by day or night (for practice, the night they belong to) as collapsible groups; older groups start collapsed.

## The queue and the one-running-job lock

One job runs at a time, ever. This mirrors the one-driver rule in the editing app itself: two builds in flight at once corrupt each other's picture of what is done.

A job carries: a name, a note (the brief, in the creator's or agent's words), and two progress marks: **export done** and **second pass done**.

- Starting the next job is refused while another job is running, and refused while the running job has not got both marks. The queue never leapfrogs an unfinished job.
- Clearing a job off the board is refused until both marks are set. A job leaves the board only when it is genuinely finished.
- A separate remove action exists for jobs that should never have been queued; it is the escape hatch, not the normal path.

## The work item card

Each delivered piece is one card:

**Face:** title, one line concept or hook, version, score (with the previous version's score where there is one), and the drive link.

**Expanded card adds:** the feedback field, and the three verdict buttons: **Approve**, **Changes requested**, **Did not pass**. Submitting a verdict moves the item to the matching section, and the feedback note stays attached to the item wherever it moves. A note can also be saved on its own, without a verdict, leaving the status untouched.

**Ordering is deterministic, not insertion order.** Ready for review and Changes requested sort the same way: newest day or night group first, then set order (Set 1, Set 2, Set 3), and Reel before Story within a set. Passed and Did not pass sort newest first. The creator should always find last night's Set 1 Reel in the same place, however the builds happened to finish.

## Data model

One record per job:

- name, note
- status: waiting or running
- export done flag, second pass done flag
- created at

One record per work item:

- title, one line concept or hook
- version, score, previous version's score (optional)
- drive link
- group label (the day or night it belongs to)
- status: review, changes, passed, failed
- feedback note (optional)
- created at

## Published actions

The agent drives the space through published actions; the creator only taps. Build at least these:

- `getboard` — the running job (or none) and the waiting jobs in order
- `addjob` — add a job to the end of the waiting queue, with name and note
- `startnext` — start the first waiting job, enforcing the lock above
- `markexportdone` — set the running job's export mark
- `marksecondpassdone` — set the running job's second pass mark
- `clearjob` — remove the running job once both marks are set
- `removejob` — remove any job regardless of state (escape hatch)
- `addworkitem` — add a delivered piece to Ready for review
- `getworkitems` — all work items grouped by status, in the deterministic order above
- `submitverdict` — record a verdict (approve, changes, fail) with an optional feedback note; moves the item
- `savefeedback` — save or replace a feedback note without changing status

## The wiring, direction one: the board is the live picture

**Builds patch the board at the moment things happen, not at delivery time.** A board that is only updated in a morning batch is a report; a board patched as events occur is a live picture the creator can trust at any hour. The moments are:

- A build starts (including a resumed build): start its job. If the job is not on the board yet, add it, then start it.
- The job's exports finish: mark export done.
- The second pass finishes: mark second pass done.
- Each set or piece is finished (final export verified in the drive, review complete): add it to Ready for review immediately, so pieces land for review as they finish instead of waiting for the whole job.
- The job is fully done with both marks set: clear it.

Two safeguards: never add a work item that is already on the board (match on title and group label; add only what is genuinely missing), and a failed board action is logged and never holds the build or the delivery hostage. The board serves the work, not the other way round.

**The relay: board taps reach the agent by polling.** The creator acts on the board by tapping: starting a job, submitting a verdict, leaving a feedback note, flipping the progress marks. Those taps have to reach the editing agent promptly, as instructions: a started job is a content request to pick up, a verdict is a ruling, a feedback note is a note on that item. Be honest about the mechanism: a tap inside a space fires no webhook and no event an agent can subscribe to, so a small scheduled relay polls the board instead. It works like this:

1. Every interval (15 minutes works), the relay reads the board (jobs and work items) and reads its state file.
2. The state file is a JSON snapshot of what the relay last reported: the running job's identity and marks, the waiting job ids, and every work item's id, status and feedback.
3. It diffs. New events are: a job started or joined the queue; a work item whose status changed (a verdict); a work item whose feedback changed; a progress mark flipping to done.
4. If there are events, it sends one message to the editing agent's chat listing them plainly: for a job, its name and its note in full; for a verdict, the item title and the new verdict; for feedback, the item title and the note.
5. Only after that send succeeds does it rewrite the state file from the current board. If the send fails, the state stays untouched so the next run retries. Nothing is forwarded twice, and nothing is silently dropped.
6. A first run with no state file writes the baseline and sends nothing: a first sighting is a baseline, never a relay.

## The wiring, direction two: chat is a full input channel

The creator gives verdicts and feedback wherever they happen to be, and will often say it in chat rather than opening the board. They must never have to say the same thing twice, once in chat and once on the board. So:

- When the creator gives a verdict on a delivered item in chat, the agent writes it to the board in the same flow: submit verdict, with their notes condensed to the actionable points as the feedback. Approve goes to Passed, changes to Changes requested, fail to Did not pass.
- Notes without a verdict go through save feedback, leaving the status untouched.
- **Echo guard, part of the same step.** The relay diffs the board against its state file, so the agent's own write-back would look exactly like a fresh tap by the creator and get relayed straight back as news. Immediately after any write-back, the agent reads the work items fresh and updates the state file's entries for exactly the items it wrote (their status and feedback, to the post-write values). The relay then sees no diff for those items and stays silent about them. A genuine later tap by the creator on any item still relays normally, because it changes the board away from the synced state.

## The morning delivery reads the board first

The practice system's morning delivery does not ask the creator to re-review what they already ruled on. Before presenting, it reads the work items: a verdict or feedback note left in the space counts as the creator's ruling, exactly like a chat verdict (it is logged in the run record and the feedback file), and the delivery never re-asks for it. The chat delivery itself is then a short pointer: the night is ready, its items are waiting in Ready for review, one line per set, blockers stated honestly, at most a couple of specific asks. The table, links and scores live on the cards, not in chat.

## Behaviours that matter

- **The lock is real.** One running job; no starting over an unfinished job; no clearing before both marks. The board's discipline mirrors the editing app's one-driver rule.
- **A verdict keeps its note.** The feedback travels with the item across every section, so the reason is never separated from the ruling.
- **No duplicates.** Builds match on title and group label before adding; the board never shows the same piece twice.
- **The board never blocks the work.** A failed action is logged in the run record and the build or delivery continues.
- **The relay only advances on success.** State is rewritten after a delivered message, never before.

## Setup parameters (your agent asks you)

- Which chat the relay delivers board events to (normally the editing agent's chat)
- The polling interval for the relay (15 minutes is a good default)
- Where the relay state file lives (beside the practice system's files works well)
- The group label convention for work items (for example, "Night [n] · [date]" for practice, the event name for real jobs)

## Starting state

Empty: no jobs, no work items. If the creator already has ruled editing work they want visible as history, the agent can seed the passed and failed sections once from the run records; that is a one-off import, not sample data.
