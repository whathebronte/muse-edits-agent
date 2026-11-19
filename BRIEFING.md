# Briefing: setting up an Edits agent that edits like mine

This document explains, in plain language, how my short-form video editing agent works end to end: the setup it needs, the rules it edits by, and the practice system that makes it better every week. It is written so you can hand it to your own Muse (or read it yourself) and set up an equivalent system. Nothing in here is specific to one person: adapt the taste rules to your own style as you go. The structure is the part worth copying exactly; the taste settings are the part worth making your own.

## 1. Prerequisites and setup

### What you need

- A Mac (laptop or desktop) that can stay awake and plugged in during editing sessions, ideally overnight.
- The Muse desktop app on that Mac, paired with your Muse account, and left running. This pairing is what lets your agent see and drive the Mac.
- The Meta Edits app installed on the same Mac, signed in to your Instagram account. Edits is where all cutting happens.
- An automation connection between Muse and the Edits app. In generic terms: Edits exposes an automation interface, and your agent connects to it over your private network so it can open projects, import media, build timelines, add text and music, and export, all without you touching the mouse. Ask your Muse to set this up and confirm it can list your Edits projects before you rely on it.
- Your phone and the Mac signed into the same iCloud account with iCloud Photos on, so clips you shoot appear in the Photos app on the Mac.
- A cloud drive folder (Google Drive works well) where finished exports are delivered to you as links.
- Optional but strongly recommended: a voice cloning account (ElevenLabs or similar) if you want voiceovers in your own voice without recording every time, and a small set of offline analysis tools your agent can install itself (speech detection, word level transcription, face detection, loudness measurement, beat detection). Your agent can set these up; you do not need to.
- Strongly recommended: yt-dlp, a free downloader your agent can install itself (it needs its own Python virtual environment on some systems, which your agent can handle). It downloads reels and other short videos as files, yours and reference creators' alike, so they can be audited properly: extract still frames with ffmpeg and check what is actually on screen, when hook text lands, how long it stays, where cuts fall. Platform tools and APIs mostly return text metadata (captions, counts, dates), so without the file itself an agent is guessing about everything visual. Use it as the pull route whenever another method struggles to fetch a video. One limit: public posts download with no login, but posts a platform shows only to signed-in accounts still need a signed-in browser session.

### How clips get from phone to edit

1. You shoot on your phone as normal.
2. iCloud Photos syncs the clips to the Mac. This is the slowest, least reliable link in the whole chain, so plan around it (see failure modes below).
3. The agent opens Photos on the Mac, finds the clips, and exports the unmodified originals to a staging folder.
4. The agent copies those originals into the folder Edits imports from, then imports them into a new Edits project.

Two hard won rules about Photos:

- Always browse via the library's chronological view (Library, then All Photos, scrolled to the newest end). Never use the Photos search view to pick clips: search results can show you a clip that is not actually the file you think it is, and exports from search have silently produced the wrong media. The boring route is the reliable route.
- Export the unmodified original, every time. Edited or optimised versions degrade quality and break frame accurate work later.

### Staging and disk discipline

Editing work produces copies relentlessly: Photos exports, import copies, review renders, iteration exports. A few gigabytes a night does not sound like much until the disk fills, and a full disk does not just block exports: it stalls the photo sync the whole pipeline depends on. So storage is a designed layer of this system, not an afterthought, and it is set up once, as a choice:

- **Choose a staging workspace at setup.** If you have an external drive you can leave connected, stage there, in one named workspace folder on it. If not, stage in one named local staging folder. Either works; what matters is that staging lives in exactly one known place, never scattered. (The configuration this system was proven on uses an external drive as primary staging. Treat that as one configuration of the rule, not a requirement.)
- **Free space gate, before any export.** Any build that exports media from Photos first measures the internal disk's free space. Under a safety threshold (15 GB free is the proven figure), it parks the export heavy work and flags you, rather than filling the disk and stalling sync mid job.
- **External drives go missing; work must not stop.** An external drive can auto-eject when idle. If the primary staging drive is not mounted, the build stages in a named fallback folder on the internal disk instead, notes the fallback in the run log, and keeps going.
- **The fallback has a hard age cap.** Nothing the agent stages on the internal disk lives there beyond 3 days. Every build and every delivery run first sweeps the fallback folder and bins anything older than 3 days.
- **Log staged files, clear exactly those.** Each run records the full path of every file it stages in its run log. Delivery cleanup clears exactly those paths, so nothing is hunted for and nothing extra is touched.
- **Delete versus bin follows where the file lives.** Staging copies on the external drive are deleted outright at cleanup, never binned: a file binned from a drive sits in that drive's own trash and frees nothing until it is emptied, and these are pure copies (originals live in the photo library, finals in the cloud drive). Copies on the internal disk go to the bin as a safety net. In a local-only setup the same logic applies to your staging folder: bin at cleanup, and let the weekly audit (below) permanently clear the agent's own binned staging copies once they have sat there 3 days or more, identified from the run records. Files you binned yourself are never touched.
- **Import copies are not staging.** The copies inside the folder the editing app imports from belong to live projects, which reference them. They are cleared only together with their project, under the retention rule below, never swept as staging.
- **Project retention tiers.** Judged on the editing app's project list (size, last modified) plus the run records and your verdicts:
  - *Keep, never deleted:* live or undelivered deliverables, anything tied to a deliverable still owed, the current work, anything inside its window below.
  - *Auto-delete, no verdict needed:* closed practice projects 3 or more days after your ruling, and scout, scratch or test projects 3 or more days old. In the same sweep, each deleted project's loose import and export files are binned (and later permanently cleared under the bin rule above). A project you reopen restarts its window and is kept.
  - *Delivered brand projects:* candidates at 30 or more days after delivery, deleted only on your verdict on a named list, because clients return for revisions.
  - A weekly disk audit job runs the tiers: it measures the library and staging, performs the auto-deletions, keeps a disk log, and reports what it deleted, how much it freed, and any brand candidates awaiting your verdict. When there is nothing to report, it stays silent. Originals live in Photos and finals in the cloud drive, so a deleted project is reconstructable and never loses a deliverable.

Set up this way, the system cannot clog your filesystem whichever storage you chose: the gate stops work before the disk fills, the sweep caps staging's lifetime, and the retention tiers keep the project library from growing without bound.

### Failure modes you will meet (all learned the hard way)

- **Mac shows "offline" while it is clearly on.** The Muse desktop app was closed or dropped its connection. The laptop being awake does not mean the app is running. Fix: reopen the Muse app on the Mac and leave it running, then have the agent re-check. The app pairing and the Edits automation connection are two separate channels; one can be fine while the other is down.
- **Clips "not there yet".** You shot an hour ago and the agent cannot find the footage. The clips are still on your phone or still uploading to iCloud. The Mac library can even report itself fully synced while new items are still inserting one by one. Fix: on the phone, open Photos on Wi-Fi (charging helps) to push the upload, then wait for the Mac library's item count to hold steady across a minute or two before concluding the set is complete. A count that is still ticking up means the set is still arriving; building early means building with half the story missing.
- **Originals not downloaded.** If your Mac's Photos is set to optimise storage (common, and fine), originals live in iCloud and each clip downloads on demand when exported. That per clip fetch is a fixed cost you cannot remove; budget time for it and let the agent pre-stage tomorrow's clips while it scouts.
- **Sync paused because the disk is full.** Staging copies from earlier builds pile up quietly, a few gigabytes a night, until the internal disk fills and Photos pauses syncing to protect itself. The library then shows nothing new even though you shot all day. This is what the staging and disk discipline above exists to prevent: the free space gate parks export work before the disk fills, and the sweep and retention tiers keep staging and old projects from accumulating.
- **Mac asleep or Edits closed at build time.** The run should log the blocker, invent nothing, and tell you plainly in the morning what was missed and why. A system that quietly fakes output is worse than one that admits a miss.

## 2. The one-driver rule

Edits is one app with one timeline. Only one agent session may drive it at a time, ever. Two sessions building at once corrupt each other's timelines in ways that are hard to see until export.

The sharing rules:

- **Real jobs always outrank practice.** If you ask for a real edit while practice work is mid flight, the practice run parks at a clean checkpoint (project saved, state written down), your real job gets the driver, and practice resumes only after your job is delivered. A displaced practice item is logged as displaced, not failed.
- Before any session starts driving, it checks in writing whether another session is mid build, and finishes or parks that project cleanly first. Never build over an open timeline whose state you have not checked.
- While one build occupies Edits, other sessions can still do useful prework that does not need the app: scouting from local copies, writing plans, analysing audio and frames. A queued build should start from a finished plan, not from cold scouting.

## 3. How a job runs, end to end

### Step 1: You give selects and name the album

For real deliverables, you stay the curator: you pick the clips (or point at the album or event window) and name the job. The agent does not substitute different footage under your title, and does not go hunting unrelated material for a deliverable. (The practice system in section 5 has its own, looser sourcing rules, and the two never mix.)

### Step 2: Concept and hook first, before any cutting

The agent writes a short plan before touching the timeline: the concept in one line, the exact hook text, the beat list, the music pick, the target length. The principle: **the hook is a promise, and the cut must visibly keep it.** If the finished video does not show what the hook says, the cut is wrong, not the hook. Editing first and captioning afterwards produces videos whose text describes some other, better video. Never allow it.

Two copy rules that sound small and matter enormously:

- Every factual word in on-screen text must be true of the footage: the meal, the time of day, the activity, the product feature names. A hook that says "lunch" over brunch footage, or names a product mode that does not exist, is a defect, not a flourish.
- A label that says nothing (the equivalent of titling a clinic visit "clinic day") is worse than no text at all. If no line earns its place, ship the beat with no text.

### Step 3: Script approval before building

If the video has a voiceover, the script is shown to you and approved **before** any voice is generated and before the edit is built around it. Scripts are written to be spoken, not read: the register that works is a voice note to a friend, gossipy and present tense, starting mid thought, ending when the thought ends. No reflective poem voice, no uplift ending, no "and that's when I realised" arc pasted onto montage footage. Before generation, the script passes an anti-AI-sounding pass: vary sentence lengths, cut stock phrases and tidy three-item lists, read it aloud mentally and fix anything you would never actually say.

### Step 4: Voiceover workflow

- Default to a clone of your own voice rather than recording every take yourself; your own recording is the fallback, not the plan. (If you do record, repeated takes use the last clean take.)
- Lock the clone settings once you have a version you accept (model, stability, similarity, style) and write them down; do not re-tune per video. Generate in short blocks of two to four sentences, plain text, no pause markup tags (they make the clone sound like it is reading a script). Take a couple of takes per block and keep the better one.
- **Align on the audio before it touches the edit.** Listen to the stitched voiceover on its own first. Only when the audio is right does it go on the timeline. Then verify scene landing: every line that names a shot must actually land on that shot's cut, checked against word level timestamps, not by estimate.
- Trim dead air with a speech detector rather than by ear, with small pads (about 0.08s) so no speech gets clipped. Log how much was trimmed.
- Track your voice credits like money: log characters spent per job, and set warning thresholds (for example at 80% and 90% of your plan) so a big week never runs dry mid job.

### Step 5: The cut

- Reels land where the cut lands, roughly 18 to 30 seconds. Never pad a reel to reach a length floor; a tight 19 seconds beats a flabby 28. Stories are shorter and rawer, but keep a floor of about 7 to 8 seconds; below that a story feels like a glitch.
- Pacing: montage beats run fast, around 0.7 seconds on energetic runs, with the opening frame and the final frame holding longer. When the music has a clear drop or energy shift, the cut pace follows it: slower before, quicker after. Locate the drop with a beat analysis tool rather than guessing.
- No duplicate or near duplicate beats. Two adjacent clips of the same gesture read as a mistake, however nice each is alone.
- Never hold a frame where anyone looks wild eyed or mid grimace. Eyes closed in a genuine moment is fine; crazy eyes on a held frame is a fail.
- If the footage contains an obvious built in visual arc (a product cycling through its colours, a sky changing through sunset), structure the cut around that arc instead of imposing a generic montage order.
- If a hook asks or promises something ("help me pick"), the final beat must show the payoff: you wearing or using the thing the video lands on. A promise without a visible resolution is an unfinished video.

### Step 6: Captions and on-screen text

See the full text rules in section 4. In workflow terms: text is placed after the cut is locked, one element at a time, and every element is verified on the exported file (not the timeline preview) for placement, readability and timing before delivery.

### Step 7: Music

- Build a personal pool: the tracks and artist lanes from your own past posts that already sound like you. New picks come from that pool first. Fresh candidates (recent releases, tracks rising in your feed circle) are offered beside the pool, labelled with their source and date, never swapped in silently.
- Every pick must pass three tests, and the plan states the justification in one line: (1) the track's tone matches the copy's tone (a cheeky hook over a dramatic track is a fail), (2) it is fresh, not a dated, overused hit, (3) it actively adds something: lift, bounce, a moment. Merely "not clashing" is a fail.
- Music starts at zero: the bed is sounding from the first frame, under the hook. A silent or late first second reads as broken.
- Levels: measure loudness on the exported file, in stereo, with a proper loudness tool (a BS.1770 style measurement, for example pyloudnorm). Never trust an in-app or loop meter for the delivery figure: those read low, several dB in the case that taught this, and a mono measurement reads several dB lower still. Either error makes you mix hot. Montage and story beds sit at full level, aimed around minus 16 LUFS stereo. Under a voiceover, music drops to about 0.15 volume so the voice owns the mix.
- Rotation is enforced by a track ledger, a single file covering every piece of editing work, practice and real jobs alike. Check it before every pick, no exceptions: a track used in any delivered piece sits out original builds for 14 days from its last use. The only exceptions are a benchmark recreation mirroring the audio of your original post (fidelity is the point, logged as a mirror, not a pick), a track you name yourself for that piece, or a brand requirement written in the brief; each is logged on the ledger line with its reason. Your plan for each set states the pick, its source, and its last-used date from the ledger (or "never used"), the reviewer verifies the pick against the ledger, and the build appends the night's uses to the ledger on delivery. Why so strict: approval is per-use. Approving a piece approves that use of the track, not the track in general, and without a ledger a liked track drifts back week after week, each approval quietly making it more likely. The ledger is what stops the drift.
- Identifying a track in footage or a reference: use the platform's own audio metadata first (for a reel, the audio credit attached to the post is authoritative), an audio recognition tool as the fallback for original-audio reels, and the recognition tool as the default for stories, which carry no metadata. If the recognition tool returns no match, report the track as unidentified. Never guess a track from how it sounds.

### Step 8: The mandatory second pass

Nothing is delivered off the first export. The agent watches the export end to end once, checking: frame count exact, text timing and completion (an animated line must finish and hold, not get cut off), face clearance on held frames, loudness figures, music from frame one, and that every voice line landed on its scene. Most embarrassing defects are invisible in the timeline and obvious in the export. This pass is never skipped to save time; it is where the time is earned back.

### Step 9: Export spec and delivery

- Export at 2K (1440 x 2560 for 9:16 vertical) at the project's native frame rate, HDR off, SDR colour. Verify the exported frame count matches the timeline exactly.
- Deliver as cloud drive links, never as video files dumped into chat. Only the delivered version is ever uploaded: iteration exports (first versions, review copies, fix-pass candidates) stay local and never reach the drive at all. Finals live in one named exports folder in your cloud drive, never loose in the drive root. Verify the uploaded file's size matches the local export byte for byte before sharing the link. When an approved fix changes a delivered piece, update the same drive file in place AND rename it to the new true version in the same step: the link never changes under you, and a filename that understates the content (a "v2" name on what is really v8) is a defect, because the name is how everyone, including future you, reads what the file is.
- Delivery format: an approval table, one row per item, with the item name, its status and version (for review, approved, changes requested, in progress, did not pass), the drive link, and a feedback column that collects your notes. The table shows only items currently in play; once you approve or close something it drops off, and the full list is produced only when you ask for it. Where the review board is in use (section 5), the board is the review surface: the items sit on their cards there, and the chat delivery is a short pointer to the board rather than the full table.
- Every delivered set also carries a ready-to-post caption, written in your posting voice, so posting is a ten second decision rather than a fresh writing task. If you rewrite a supplied caption, that rewrite is gold: it gets logged as taste data.
- The agent never posts anything itself. You post, always.

## 4. The editing rules (the taste layer)

These are the standing rules the agent checks every build against. Write yours down the same way; an unwritten taste rule gets relearned at your expense every week.

Where yours come from at setup: the agent derives them from you, two ways. It interviews you about what you post, what you love and hate, how your captions sound and what music feels like you. And, with your permission, it studies your own past posts and stories: your hooks, your pacing, where your text sits and how big it is, your caption habits, the music you return to, how your videos end. Every setting below (lengths, sizes, levels, fonts) is one creator's measured habits, shown as a worked example. Replace each with your own measured habits and stated preferences, and correct the profile your agent drafts before it becomes rules.

### Text and captions

- Pick ONE caption font family that is native to the platform (the classic in-app caption family, not a fashionable display font that only exists inside the editing app; text that looks imported reads as imported). Use it for everything, hooks and captions alike.
- Size: hooks and captions both sit at font size 14 in the Edits app. Smaller sizes (11 for hooks, 10 for captions, tried earlier) proved too small to read comfortably on a finished reel. Keep the measurement habit anyway: the hook's letter height lands around 1.5% of frame height, and you verify text size by measuring the letter band on the exported frames, because timeline previews lie about final size. If you recalibrate, judge exported stills at a few candidate sizes, pick by eye, then set one size for both hooks and captions and keep it.
- Lowercase by default, but the word "I" is always capitalised, and brand and product names keep their correct case. No full stops in captions.
- At most two lines per caption, broken by meaning so bound phrases stay together. One text element on screen at a time, ever. Captions never repeat the hook.
- Placement is verified three ways on the export: (1) never over anyone's face, proven with face detection on the actual frames the text occupies, including the exact held frame for static text; (2) readable against the actual background behind the letters, not just inside a nominal safe zone (the same white text that sings over sky dies over white water foam); (3) fully inside one visual zone, never straddling a strong boundary like a mirror's edge or a horizon line.
- Check placement against BOTH platforms' interface overlays (top bands, right side action rail, bottom caption zone) on Instagram and TikTok, not just the clean frame. A location tag sitting under the platform's top bar is invisible in practice however correct it looks in the editor.
- Animated typewriter openers: size the animation span so the full line completes with at least about 0.8 seconds of hold afterwards, verified frame by frame on the export. Pair every typewriter opener with a subtle typing sound effect, spanning only the typing itself, at a texture level: working level 0.5, with 0.6 as a ceiling, never a target. Present, not noticeable. Your ear outranks any analysis graph here; if it sounds loud, it is loud.
- One narrow exception format worth knowing: a single long reflective line, shown once, completely static, no animation, works beautifully on dreamy, aspirational footage (travel, beach, genuinely happy moments). It is poison anywhere else: never on treatments, procedures or anything clinical, never layered over a mismatched voiceover, and never animated. A narrow lane, respected narrowly.

### Pacing and takes

- Beats around 0.7 seconds on fast montage runs; hook and final frames hold longer.
- Repeated takes: last clean take wins.
- No two adjacent beats showing the same gesture.
- Reel length is an outcome, not a target: roughly 18 to 30 seconds, wherever the cut lands.

### Music rules

- Pool first, fresh candidates beside it with sources and dates.
- Three test justification for every pick: tone match, freshness, adds something.
- Starts at frame one. Beat matched to drops. Loudness measured on the export file in stereo, beds around minus 16 LUFS, music at 0.15 under voiceover.
- Rotation is checked against the track ledger before every pick: 14 day cooldown from a track's last use in any delivered piece, exceptions logged (recreation mirror, a track you named, a written brand requirement). The plan states the pick, its source and its last-used date. Approval is per-use and never makes a track more likely next time.

### What a file level audit of your own posts teaches

At some point, have your agent audit your own posted reels and stories at file level: text presence and placement, shot lengths, loudness, what the first and last frames show. A few hundred of your own posts is a better style guide than any trend report, because it measures what your audience actually rewarded. The rules below are what one such audit produced; treat them as the worked example, and re-derive your own numbers rather than copying these.

**Reels:**

- Open on yourself: face or body filling a bright first frame. In the audit, face-first openings earned roughly three times the median likes of place-first openings.
- Hook text is optional, never the engine. Fewer than half the audited reels carried any text in the first two seconds, and the top performers were not the text-heavy ones. If you use a hook line, keep it to one short line, present from the first frame.
- Typing openers do not outperform static ones. If you use one, keep the text pinned for the full opening segment rather than letting it vanish.
- Holds beat cuts. The top group's median first shot ran about five seconds and its last shot about six; do not chop a strong take to hit a beat count.
- Montage around yourself, with scale changes (close, wide, detail) rather than a run of same-scale shots.
- Close on a held frame of yourself, or a brand end card. No outros, no calls to action, anywhere.
- Never: text stacks of three or more lines, all-caps meme text pinned for a whole reel, letterboxed openings, graphics covering the lower frame through the hook window, an opener where you are unidentifiable for the first seconds, any mid-reel black frame.

**Stories:**

- Restraint is the style. In the audit, about half the stories carried no added text at all, and the large majority carried nothing beyond a tiny corner tag. Default to no text; add only a corner tag or one short line when it earns its place. No polls, questions, countdowns, GIFs or hashtags appeared anywhere in the audited set.
- You are the subject in about half of stories; the rest are the place, the food, the moment.
- Text, when present, is one idea, in your caption case rules, roughly 1 to 1.6% of frame height, parked at an edge, with at most a couple of emoji at the line's end.
- Sequencing is diary order: the day as it happened. Do not impose a narrative arc on stories.
- Duration sat around eight seconds median, most stories a single take; montage beats, when used, ran about 0.8 seconds.

### Never do these

- Never build over someone else's open timeline, and never let two sessions drive Edits at once.
- Never substitute footage under a named title, and never fake a set that did not come together. Deliver what is real and say why the rest is missing.
- Never ship text you have not verified on the export itself.
- Never spend voice credits before the script is approved.
- Never ask for detailed feedback on output that still carries known headline defects. Fix the big problems, state plainly which known defects remain, and only then invite detailed notes.
- Never post, send or publish anything on the creator's behalf without their explicit approval of the exact final version.

## 5. The nightly practice system

This is the engine that makes the agent better instead of just busy. It borrows the logic of deliberate practice: reps, a coach who did not do the work, a scorecard, and rules that get written down.

### The daily output

Three sets a night, built overnight, delivered in the morning. Each set is one reel plus one story, distinct from each other. The three slots have different jobs:

1. **Benchmark set.** A recreation of one of your own posted stories, rebuilt from the raw footage, plus a fresh reel cut from the same window of footage. Your finished posts are the answer key: the agent builds its version first, then diffs it against your actual cut (which shots you picked, your hook, your beat order and durations, what you left out). The diff is the lesson.
2. **Fresh set.** A new cut, faithful to your style, from footage you have never posted.
3. **Experiment set.** One new hook structure, format or technique, clearly flagged as the experiment in the delivery, so its result can be judged as an experiment.

If the benchmark hunt honestly fails one night (raws were deleted, or the window cannot be verified), the night runs three fresh and experiment sets, the miss is named in the delivery, and a make up benchmark is built once a window verifies. Nothing is faked to fill the slot.

Voiceover discipline: only ONE of the three reels carries a voiceover; the other two are text hook montages. Concentrating the script effort on one reel produces one good script instead of three thin ones, and it conserves voice credits.

### Sourcing for practice

Practice has looser sourcing than deliverables, by the creator's explicit choice: the agent may scout the camera roll and albums freely, unused and unposted footage first, reuse allowed and logged. A weekly pool index (albums and recents categorised, with clip counts and a used log) stops the agent reusing the same clips and makes hunting faster over time.

One discipline sits on top of the used log, learned the hard way: **unused clips are not the same as fresh content.** An event or session window that anchored a set may not anchor another set the next night, and should not repeat within the same week unless the creator asks. At least two of the three sets each night must come from windows the practice has never built from. A night assembled from the leftovers of last night's window reads as a rerun to the creator even when every clip is technically unused, and it teaches the agent nothing new. When a benchmark hunt blocks honestly, the fallback is a fresh window hunt, never the leftovers. One practical tip that pays for itself: your posted archive's timestamps point at your densest shooting windows, which is exactly where unused footage clusters. Hunt there first. And a habit worth adopting: keep raw clips for at least 90 days after posting, then archive to a drive rather than deleting; creators who purge raws starve their own benchmark slot, and food footage is always the first to go.

### The independent reviewer

Every set is built as a v1, then scored by a **separate agent that did not build it** and never touches the editing app. The reviewer judges the plan, the script, preview frames and the export, cold, against the rubric and your style rules, and writes scored feedback with specific catches. The builder fixes the catches into a v2, the reviewer re-scores v2 (blind to its own v1 comments where practical), and **only v2 is presented to you**. Your verdict outranks the reviewer's score; where you disagree, the disagreement is logged and the rubric is recalibrated toward you.

### The rubric and ship gate

Score out of 100: quality 90 plus speed 10.

Quality criteria (90): hook in the first 3 seconds (13), pacing and take picks (18), story arc and distinctness (9), voiceover script, spoken not written (13), voice clone delivery (9), music and mix (9), framing variety and polish (9), technical clean (10). For videos without voiceover, the voice criteria drop out and the total is scaled and stated plainly.

Speed (10) is computed from logged times, never judged by eye, and it is measured against the size of the job: clips handled, minutes of footage scouted, beats in the final cut, voice blocks, export passes. Only the editing phase earns speed points; file transfer time is infrastructure, not skill, and is logged separately. Speed points only count if quality clears about 80% of its marks, so rushing never pays.

Pass mark: 80 of 100. If a v2 still scores under 80 it is presented anyway, with its score and the reviewer's top reason. Hiding weak work teaches the system nothing.

**The goalpost moves.** When any set scores 100, or the rolling 7 day v2 average reaches 95, the rubric tightens within a day: raise the pass mark, demand more inside existing criteria, promote proven experiment criteria into the main rubric, tighten speed bands. A perfect score means the test got too easy, never that the work is done.

### The scorecard and feedback files

Four living documents run the loop:

- **A system file**: the single home for how the practice runs (slots, rubric, schedule, rules). One home per fact; when a rule changes, it changes there.
- **A run log** per day: per set scores (v1 and v2, with rubric version), the daily v1 average, a rolling 7 day average for v1 and v2 separately, build times, voice credits spent, clips used, and a root cause note for any score dip. The trajectory that matters is v1 rising and the v2 minus v1 gap shrinking; that is the measurable proof the agent is internalising lessons rather than leaning on the reviewer.
- **A feedback file**: every piece of your feedback recorded in your own words, the rule distilled from it, and where that rule now lives. Your corrections are the training data; they accumulate permanently and do not expire when the rubric version changes.
- **A learnings checklist**: the cumulative pre-build checklist the next run starts from, so each night begins from everything learned so far instead of from zero.

### How your feedback is handled

You will give feedback quick fire, note by note, as you watch. That is fine and expected. The agent batches your notes per set and applies them in ONE fix pass with ONE verification and re-export, rather than a rebuild cycle per note. There is no round cap: fix rounds continue until you approve or say stop. The delivery asks you for at most one or two specific things (a taste call, a missing fact), never an open ended "thoughts?".

### The review board: one board, wired both ways

Delivered work lives on a review board (the Edits Queue space; full build spec in `spaces/edits-queue/TEMPLATE.md`): a job queue with a one-running-job lock on top, and every delivered piece below it as a card in Ready for review, Changes requested, Passed or Did not pass, each card carrying verdict buttons and a feedback field. The board only earns its place if it is wired in both directions:

- **Builds patch it as events happen.** Job starts (including resumed builds), exports done, second pass done, each finished piece lands in Ready for review the moment it is done, and the job is cleared once both marks are set. A board updated in a morning batch is a report; a board patched live is a picture you can trust at any hour.
- **Your taps reach the agent by relay.** A tap inside a space fires no webhook and no event an agent can subscribe to, so a small scheduled job polls the board (every 15 minutes works), diffs it against a saved state file, and forwards what changed (a job started, a verdict, a feedback note, a progress mark) to the agent's chat as instructions. The state file is rewritten only after a successful send, so nothing is forwarded twice and nothing is dropped.
- **Chat is a full input channel.** When you give a verdict or feedback in chat instead, the agent writes it to the board in the same flow, verdict with your notes condensed to the actionable points as the feedback, or the note alone if there is no verdict. Then, immediately, it syncs the relay's state file for exactly the items it wrote, from a fresh board read, so the relay does not echo your own instruction back to the agent as if it were a fresh tap. You never say the same thing twice.
- **The morning delivery reads the board first.** Verdicts and notes left in the space count as your rulings: they are logged, and the delivery never re-asks for them. The chat delivery is a short pointer to the board, not the full table of links and scores.

## 6. Watchdogs and scheduling, in brief

- **Nightly build job**: a scheduled run in the small hours builds the three sets. **Morning delivery job**: a scheduled run at a fixed morning time reads the review board first, then posts the morning delivery to your chat: a short pointer to the night's items waiting on the board, not the full table. Schedules are set once and run whether or not you are awake; that is the point.
- **A handoff file** is the living state document: the builder updates it at every checkpoint (scouted, planned, v1 built, review in, v2 built, exported, delivered), so if a run is disrupted, a fresh session can pick up cold at the last completed checkpoint without rebuilding or re-spending voice credits.
- **A watchdog** checks the handoff and run log for movement while a run is active. If nothing has moved for a set threshold (around 12 to 35 minutes, tune it), the watchdog first tries to unblock the run itself (nudge or re-dispatch), and only bothers you if the unblock fails or the blocker genuinely needs you. Watchdogs should be event driven: switched on by the workstream they guard when real work starts, and switched off when it finishes, not ticking all day burning budget.
- **Failure honesty**: if the Mac was asleep, the app closed or sync failed, the morning delivery says plainly what was missed and why. No invented output, ever.

## 7. The biggest lessons and pitfalls, from day one

1. **Concept first, always.** The single most expensive habit is editing first and writing the hook afterwards. The hook is a promise; build the cut that keeps it.
2. **Your verdict is the ground truth, and scores are a proxy.** The reviewer will sometimes pass things you hate and flag things you love. Log every disagreement and recalibrate toward you; an agent trained to please its rubric instead of its creator is a well scored stranger.
3. **Verify on the export, not the timeline.** Text size, placement, timing, loudness, face clearance: all of it lies in preview and tells the truth in the exported file. Budget the second pass or pay for the defects publicly.
4. **Measure loudness in stereo, and size text by measuring letters.** Two boring measurement disciplines that each prevent an entire class of "sounds hot, looks huge" failures.
5. **Copy truth is a defect class.** Wrong meal, wrong product feature name, a label that says nothing: these read as carelessness to viewers and they are the easiest catches to prevent, because they only require checking the words against the footage before building.
6. **Music fails quietly.** A track that merely does not clash still fails. Tone match, freshness, and adds something, justified in one line per pick, or expect to redo the mix at the worst moment. And rotation fails silently too: without a ledger, a liked track drifts back week after week, and every approval makes it more likely, not less. The ledger and the 14 day cooldown exist because taste plus memory is not a rotation policy.
7. **Sync is the bottleneck nobody believes in.** The editing is fast; getting tonight's clips off the phone is not. Stable item counts before building, originals exported the boring way, and tomorrow's clips pre-staged during scouting. The same invisibility applies to disk: staging copies pile up a few gigabytes a night until the disk fills and sync itself stalls. The free space gate, the staging sweep and the retention tiers are not housekeeping garnish; they are what keeps the pipeline possible.
8. **One driver, written checkpoints, honest misses.** Most multi agent disasters are really coordination failures: two sessions in one app, a run that died silently, a set faked to fill a quota. The rules in sections 2 and 6 exist because each of those happened.
9. **Batch feedback, never drip fixes.** One fix pass per set of notes, one verification. Rebuilding per note burns hours and teaches nothing; batching turns your corrections into rules.
10. **Keep the raw footage.** The whole benchmark system, and most of the fresh system, runs on your outtakes. Ninety days minimum, then archive. Deleting raws to save space is deleting your agent's training set.
