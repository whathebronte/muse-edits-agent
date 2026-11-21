# The nightly practice system

Standalone instructions for setting up the practice engine described in section 5 of `BRIEFING.md`. The Edits agent runs this; the creator only reviews the morning delivery and gives feedback.

## What runs

- **Nightly build job** (scheduled, small hours, weekdays by default): builds three sets overnight.
- **Morning delivery job** (scheduled, fixed morning time): reads the review board first (verdicts and feedback left there count as rulings and are never re-asked), then posts a short pointer to the board in the creator's chat. Only items in play are mentioned; approved or closed items drop off.
- **Watchdog** (event driven): switched on by the build job when a run starts, switched off by the delivery job when it ends. While active it checks the handoff file and run log for movement; on a stall of roughly 12 to 35 minutes it first tries to unblock the run itself (nudge or re-dispatch), and only escalates to the creator if the unblock fails or the blocker needs the creator.
- **Board relay** (scheduled, short interval, 15 minutes works): polls the Edits Queue board and forwards new taps (a job started or queued, a verdict, a feedback note, a progress mark flipping to done) to the agent's chat, deduped against a saved state file that is only rewritten after a successful send.
- **Disk audit** (scheduled, weekly): runs the staging sweep and the project retention tiers, and reports what it deleted and any candidates awaiting the creator's verdict. Silent when there is nothing to report.

## The three sets

Each set is one reel plus one story, distinct from the others.

1. **Benchmark.** Recreate one of the creator's own posted stories from its raw footage, plus a fresh reel from the same footage window. Build the agent's version first, then diff it against the creator's actual post: shots picked, hook, beat order and durations, what was left out. The diff is the lesson and is recorded in the run log. If no benchmark window can be verified on a given night (raws deleted, window unclear), run a third fresh or experiment set instead, name the miss in the delivery, and build a make up benchmark once a window verifies. Never fake a benchmark.
2. **Fresh.** A new cut, faithful to the creator's style, from footage never posted.
3. **Experiment.** One new hook structure, format or technique, flagged as the experiment in the delivery so it is judged as one.

Voiceover discipline: only one of the three reels carries a voiceover. The other two are text hook montages.

Story effort bar: a practice story that is one clip plus a music track is not a deliverable, however nice the clip and the song. It adds nothing the creator could not do themselves in seconds, and it fails on effort. Practice stories need visible editorial work: a cut structure, a deliberate choice, something made. (The creator's own posted stories are often single takes; that restraint is their style. The bar applies to practice output, where the point is the agent learning to edit.)

## Sourcing

- Practice may scout the camera roll and albums freely: unused and unposted footage first, reuse allowed and logged.
- No-repeat windows: an event or session window that anchored a set may not anchor another set the next night, and should not repeat within the same week unless the creator asks. At least two of the three sets each night come from windows the practice has never built from. Unused clips from a used window are not fresh content; when a benchmark hunt blocks, hunt a fresh window, not the leftovers.
- Maintain a pool index: albums and recents categorised, with clip counts and a used log, refreshed weekly.
- The creator's posted archive timestamps point at their densest shooting windows; unused footage clusters there. Hunt there first.
- Ask the creator to keep raw clips at least 90 days after posting, then archive to a drive rather than deleting.

## Files the system keeps

Create these from the templates in `templates/` at setup, and treat each as the single home for its facts:

- **System file** (`templates/SYSTEM.md`): slots, rubric version, schedule, standing rules. When a rule changes, it changes here.
- **Run log** (`templates/RUN-LOG.md`): one entry per night.
- **Feedback file** (`templates/FEEDBACK.md`): the creator's feedback in their own words, the rule distilled from it, and where that rule now lives. Entries never expire when the rubric version changes.
- **Learnings checklist**: the cumulative pre-build checklist each run starts from, distilled from the feedback file and reviewer catches.
- **Track ledger** (`templates/TRACK-LEDGER.md`): one line per music track use across ALL editing work, practice and real jobs. Checked before every pick; a track used in any delivered piece sits out original builds for 14 days (exceptions logged: recreation mirror, a track the creator names, a written brand requirement). Approval is per-use and never raises a track's priority. Each night's uses are appended on delivery.
- **Handoff file**: the living state document for the current run. Updated at every checkpoint: scouted, planned, v1 built, review in, v2 built, exported, delivered. A disrupted run is picked up cold from the last checkpoint, without rebuilding or re-spending voice credits.
- **Board relay state file**: a JSON snapshot of the review board as last reported by the relay (running job, waiting jobs, every work item's status and feedback). The relay diffs against it; the agent also syncs it after writing chat verdicts back to the board (see the review board section below).

## The build loop, per set

1. Plan first: concept in one line, exact hook text, beat list, music pick with a one line justification (tone match, freshness, adds something) plus the pick's source and last-used date from the track ledger (or "never used"), target length.
2. Script (for the one voiceover reel): written to be spoken, anti-AI-sounding pass, creator approval is not required for practice scripts unless the creator asks, but the script must pass the same quality bar as a real job.
3. Build v1.
4. Independent review (see `prompts/03-reviewer-rubric.md`).
5. Fix the catches into v2. Re-score v2.
6. Export and second pass on the export itself.
7. Only v2 is presented in the morning delivery, with its score, a ready-to-post caption, and at most one or two specific asks for the creator.

## Speed gates and queue discipline

Speed is earned by removing waits and rework, never by skipping the second pass or the review loop. Four gates, learned from where the hours actually went:

- **Ingest gate.** A job may not claim the editing app until every source clip on its plan is verified present as a local file on the Mac, staged in the workspace, not merely visible in the photo library. Sync waits happen while the job waits in the queue, never inside a build; if a job's media is not fully local, the next ready job takes the app and the gated job is re-checked at each checkpoint.
- **Plan completeness gate.** The written plan is finished before the app opens: clips with ranges, trims, hook and caption copy, music pick with its catalog id resolved and its track ledger check done, and the export list. A catalog collision, an unresolved id or an unledgible track discovered mid build is logged as a plan defect, because the plan was not complete.
- **Checkpoint budget and preemption.** Every job checkpoints at least per finished piece. A job that cannot progress (blocked on sync, the app, or media) parks at its checkpoint and yields the app to the next queued job instead of holding it; a fix pass on an already delivered item preempts a long first build when the creator is waiting on the fix. The watchdog's stall nudge is the backstop, not the mechanism.
- **One meter, one pre-flight.** One loudness tool is the only meter whose figures may be logged or scored, for builder and reviewer alike; a figure from any other meter is not the export's loudness. Before any voice generation or build, the pre-flight is checked in writing: the locked voice settings row (including speaker boost on, where the clone uses it), the export preset, the standing text size, music from frame 0. A generation discarded because a checklist item was skipped is logged as a process defect.
- **Clock discipline.** Phase clocks are logged live per video at each checkpoint, from file timestamps, never reconstructed at close. Every run log closes with a compact speed block: intake minutes, scout and transfer minutes, plan minutes, v1 minutes per video, review and v2 minutes, total wall clock, re-render count, discarded generation count, and gate incidents (ingest wait inside a build, plan defect found mid-build, meter rework, checklist-caused discard), each as a plain number. A run without the block counts as unmeasured, and that is reported as such.

## The review board

Finished work is reviewed on the Edits Queue space (build spec: `spaces/edits-queue/TEMPLATE.md`). The wiring matters more than the board itself:

- **Patch the board as events happen.** The build starts its job when it starts (a resumed build too), marks export done and second pass done as they complete, adds each finished set to Ready for review as it lands, and clears the job when it is fully done. Never add an item that is already on the board; match on title and night label first.
- **The relay brings the creator's taps to you.** It polls the board on its interval and forwards job starts, verdicts, feedback notes and progress marks to your chat. Treat a started job as a content request to pick up, and a verdict or note as the creator's ruling on that item: log it, never re-ask it.
- **Chat verdicts go back to the board.** When the creator rules on an item in chat, submit the verdict to the board in the same flow, with their notes condensed to the actionable points as the feedback; a note without a verdict is saved as feedback, status untouched. Immediately after, sync the relay state file for exactly the items written, from a fresh board read, so the relay does not echo your own write-back as a fresh tap.
- **The morning delivery reads the board first.** Anything already ruled on, in the space or in chat, is logged and dropped from the delivery. The delivery itself is a short pointer: night ready, items waiting in Ready for review, one line per set, blockers, at most one or two asks.
- **Fix passes come back to the board.** When a fix on a Changes requested item is delivered, return the item to Ready for review in the same flow as the drive update, using the board's redeliver action with the new version (and the drive link if it changed). A fixed item must never be left sitting in Changes requested after its fix has landed: downstream surfaces read the board and would keep reporting the old state. Board titles never contain an em dash or an en dash; use the colon pattern (Job: Reel) or the practice comma pattern (Night 5, Set 1 Reel), and fix a wrong title with the board's rename action, which changes the title only and preserves everything else.

## Staging and disk

- Stage in the one named workspace agreed at setup (an external drive if the creator has one, otherwise a named local folder), with a fallback folder on the internal disk used when the drive is not mounted. The fallback is capped at 3 days: every build and delivery run first sweeps it and bins anything older.
- Before any Photos export, check the internal disk's free space. Under 15 GB free, park the export heavy work and flag the creator.
- Log every staged file's path in the run log; delivery cleanup clears exactly those paths. Staging copies on an external drive are deleted outright; copies on the internal disk are binned.
- Project retention is the weekly disk audit's job (see What runs): closed practice projects auto-delete 3 days after the creator's ruling; delivered brand projects only on a named-list verdict.

## Feedback handling

- The creator gives feedback quick fire, note by note. Batch the notes per set; apply them in one fix pass with one verification and re-export.
- No round cap: rounds continue until the creator approves or says stop.
- Every correction becomes a distilled rule in the feedback file and, where it belongs, an edit to the system file or learnings checklist, so the next night starts from it.
- If the creator rewrites a supplied caption, log the rewrite as taste data.

## Failure honesty

If a night fails (Mac asleep, app closed, sync incomplete), the morning delivery says plainly what was missed and why. No invented output, no padded sets, no silent skips.

**Transient retry.** On a transient failure mid build (a photo library window lost, a device hiccup, an app action failing, a connector 401 or 5xx, a timeout), retry the failed step once after a short wait before parking anything, re-reading state before repeating any step that writes or sends. A second failure parks that set, not the whole night: checkpoint it, log the failure class, and build the rest. A 429 or rate limit stop is never retried. Log every retry in the run log, so the morning delivery can report attempts, not just outcomes.
