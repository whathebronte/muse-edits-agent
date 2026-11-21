# Practice system: [creator name]

The single home for how the nightly practice runs. When a rule changes, it changes here.

## Schedule

- Build: [nights, e.g. Mon to Fri], starting [time, local]
- Delivery: [time, local], in [chat name]
- Watchdog: event driven, on at build start, off at delivery
- Board relay: every [interval, e.g. 15 min], forwarding board taps to [chat name]
- Disk audit: weekly, [day and time, local]

## Slots

1. Benchmark: recreation of a posted story from raws, plus a fresh reel from the same window
2. Fresh: new cut from unposted footage, faithful to house style
3. Experiment: one new hook structure, format or technique, flagged in the delivery

Voiceover: one of the three reels only.

## Rubric

- Version: 1.0 ([date])
- Pass mark: 80 of 100
- Full rubric: see the reviewer instructions in the setup repo (`prompts/03-reviewer-rubric.md`), copied here once adopted: [paste or link]
- Goalpost rule: any 100, or a rolling 7 day v2 average of 95, tightens the rubric within a day

## Standing rules

- [Rule, date added, source: creator feedback or reviewer catch]
-

## Pool index

- Albums and recents categorised, clip counts, used log: [location]
- Refreshed: [cadence, e.g. weekly]

## Files

- Run log: [location]
- Feedback file: [location]
- Learnings checklist: [location]
- Handoff file: [location]
- Track ledger: [location] (one line per track use across all editing work; 14 day cooldown; see `templates/TRACK-LEDGER.md`)
- Board relay state file: [location]

## Storage

- Staging workspace: [external drive folder, or named local staging folder]
- Fallback staging folder (internal disk, hard 3 day cap, swept at every build and delivery): [location]
- Free space gate: park export heavy work and flag the creator under [threshold, e.g. 15 GB] free on the internal disk
- Cleanup: staging copies on an external drive are deleted outright; copies on the internal disk are binned; the weekly disk audit permanently clears the agent's own binned staging copies after 3 days and reports both trash sizes
- Project retention: closed practice projects auto-delete 3 days after the creator's ruling; scout and scratch projects auto-delete at 3 days old; a reopened project restarts its window; delivered brand projects are candidates at 30 days and are deleted only on the creator's verdict on a named list; projects tied to a deliverable still owed are never touched

## Review board

- Space: [name and slug, e.g. Edits Queue]
- Wiring: builds patch the board as events happen (job start, export done, second pass done, each finished piece into Ready for review, job cleared); a relay polls the board and forwards taps to the agent's chat; verdicts and feedback given in chat are written back to the board in the same flow, with the relay state file synced for exactly the items written so the write-back is not echoed as a fresh tap
- Redelivery: a delivered fix pass returns its item to Ready for review in the same flow as the drive update (the board's redeliver action, with the new version and the drive link if it changed); a fixed item never sits in Changes requested after its fix has landed
- Titles: work item titles and job names never contain an em dash or an en dash; use the colon pattern (Job: Reel) or the practice comma pattern (Night 5, Set 1 Reel); a wrong title is fixed with the board's rename action, never by deleting and re-adding
- Review ordering: Ready for review and Changes requested sort newest night group first, then Set 1 to Set 3, Reel before Story; Passed and Did not pass sort newest first
