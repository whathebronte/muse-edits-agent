# The nightly practice system

Standalone instructions for setting up the practice engine described in section 5 of `BRIEFING.md`. The Edits agent runs this; the creator only reviews the morning delivery and gives feedback.

## What runs

- **Nightly build job** (scheduled, small hours, weekdays by default): builds three sets overnight.
- **Morning delivery job** (scheduled, fixed morning time): posts the approval table to the creator's chat. Only items in play appear; approved or closed items drop off.
- **Watchdog** (event driven): switched on by the build job when a run starts, switched off by the delivery job when it ends. While active it checks the handoff file and run log for movement; on a stall of roughly 12 to 35 minutes it first tries to unblock the run itself (nudge or re-dispatch), and only escalates to the creator if the unblock fails or the blocker needs the creator.

## The three sets

Each set is one reel plus one story, distinct from the others.

1. **Benchmark.** Recreate one of the creator's own posted stories from its raw footage, plus a fresh reel from the same footage window. Build the agent's version first, then diff it against the creator's actual post: shots picked, hook, beat order and durations, what was left out. The diff is the lesson and is recorded in the run log. If no benchmark window can be verified on a given night (raws deleted, window unclear), run a third fresh or experiment set instead, name the miss in the delivery, and build a make up benchmark once a window verifies. Never fake a benchmark.
2. **Fresh.** A new cut, faithful to the creator's style, from footage never posted.
3. **Experiment.** One new hook structure, format or technique, flagged as the experiment in the delivery so it is judged as one.

Voiceover discipline: only one of the three reels carries a voiceover. The other two are text hook montages.

## Sourcing

- Practice may scout the camera roll and albums freely: unused and unposted footage first, reuse allowed and logged.
- Maintain a pool index: albums and recents categorised, with clip counts and a used log, refreshed weekly.
- The creator's posted archive timestamps point at their densest shooting windows; unused footage clusters there. Hunt there first.
- Ask the creator to keep raw clips at least 90 days after posting, then archive to a drive rather than deleting.

## Files the system keeps

Create these from the templates in `templates/` at setup, and treat each as the single home for its facts:

- **System file** (`templates/SYSTEM.md`): slots, rubric version, schedule, standing rules. When a rule changes, it changes here.
- **Run log** (`templates/RUN-LOG.md`): one entry per night.
- **Feedback file** (`templates/FEEDBACK.md`): the creator's feedback in their own words, the rule distilled from it, and where that rule now lives. Entries never expire when the rubric version changes.
- **Learnings checklist**: the cumulative pre-build checklist each run starts from, distilled from the feedback file and reviewer catches.
- **Handoff file**: the living state document for the current run. Updated at every checkpoint: scouted, planned, v1 built, review in, v2 built, exported, delivered. A disrupted run is picked up cold from the last checkpoint, without rebuilding or re-spending voice credits.

## The build loop, per set

1. Plan first: concept in one line, exact hook text, beat list, music pick with a one line justification (tone match, freshness, adds something), target length.
2. Script (for the one voiceover reel): written to be spoken, anti-AI-sounding pass, creator approval is not required for practice scripts unless the creator asks, but the script must pass the same quality bar as a real job.
3. Build v1.
4. Independent review (see `prompts/03-reviewer-rubric.md`).
5. Fix the catches into v2. Re-score v2.
6. Export and second pass on the export itself.
7. Only v2 is presented in the morning delivery, with its score, a ready-to-post caption, and at most one or two specific asks for the creator.

## Feedback handling

- The creator gives feedback quick fire, note by note. Batch the notes per set; apply them in one fix pass with one verification and re-export.
- No round cap: rounds continue until the creator approves or says stop.
- Every correction becomes a distilled rule in the feedback file and, where it belongs, an edit to the system file or learnings checklist, so the next night starts from it.
- If the creator rewrites a supplied caption, log the rewrite as taste data.

## Failure honesty

If a night fails (Mac asleep, app closed, sync incomplete), the morning delivery says plainly what was missed and why. No invented output, no padded sets, no silent skips.
