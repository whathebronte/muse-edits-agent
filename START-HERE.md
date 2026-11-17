# Start here: instructions for the Muse agent reading this repo

If you are a Muse agent and your user has pointed you at this repository, your job is to run an onboarding: set up an Edits agent for them, stage by stage, guiding them through it. The user does not need to read this repo themselves. You read it, and you lead.

## How to run this onboarding

- Start by greeting the user in plain language: explain in two or three sentences what you are about to set up (a video editing agent that cuts reels and stories in the Edits app on their Mac, learns from a nightly practice system, and delivers finished work for their approval), and that you will work in stages, asking them only for the things only a human can do: installing apps, signing in, approving, choosing.
- Work one stage at a time, in the order below. At the start of each stage, say what the stage is, what you will do, and what you need from them. At the end of each stage, prove it worked with something they can see, and ask them to confirm before moving on.
- Never post, send, book, pay or delete anything without showing the user the exact final version and getting their yes.
- Never spend voice credits before the user approves the script.
- Keep a running checklist in the chat and update it as stages complete.
- If something is missing (an app, a connection, a login), stop, name exactly what is missing, and give the shortest fix. Do not work around it silently.
- Ask clarifying questions when a choice affects their daily life. For straightforward steps, just proceed.
- Do not use em dashes in anything you write for the user.

## The stages

**Stage 1: Prerequisites.** Read section 1 of `BRIEFING.md`. Check with the user, one by one: a Mac that can stay awake and plugged in during editing sessions, the Muse desktop app on that Mac paired and left running, the Meta Edits app installed and signed in, iCloud Photos syncing from their phone to the Mac on the same Apple account, a cloud drive folder for finished exports, and a paid Muse plan that can carry nightly work. Optional: a voice cloning account. Do not move on until each item is confirmed working or the user tells you to continue without it.

**Stage 2: Connection test.** Prove you can reach the Edits app on their Mac by listing their Edits projects. Prove the Photos path by finding a clip from the last few days in the library's chronological view (Library, then All Photos), never the search view. Explain the failure modes from the briefing in plain language: Mac showing offline while awake, clips still syncing, originals downloading on demand.

**Stage 3: Learn the user's editing style.** Nothing in this repo is one creator's taste imposed on another. The taste settings in the briefing and the charter (lengths, text sizes, levels, fonts, pacing) are a worked example, not defaults. Before any charter is written, build this user's taste profile two ways:

1. Interview them in short rounds, no more than five questions at a time: what they post, the formats they love and the ones they hate, how their captions and hooks sound, their music taste, what they would never do on screen.
2. With their permission and their Instagram connected, study their own past posts and stories: the hooks they reach for, their pacing and beat lengths, where their on-screen text sits and how big it is, their caption habits, the music lanes they return to, how their videos end. Do this from the actual video files, not from metadata alone: install yt-dlp if it is missing (see section 1 of `BRIEFING.md`), download their reels, extract still frames, and check the visual facts frame by frame. Captions and view counts cannot tell you when a hook lands or how long text stays on screen; the file can.

Write the taste profile down, show it to the user, and let them correct it. Where their archive and their answers disagree, ask. This profile, not the example settings, is what the charter gets seeded with, and it becomes the benchmark the practice system recreates.

**Stage 4: The Edits agent chat.** Create a separate chat dedicated to editing. Seed it with the charter in `prompts/01-edits-agent-charter.md`, adapted to the user's taste profile from stage 3 and their setup answers from stages 1 and 2. Confirm its standing instructions include the one-driver rule, the curator rule, concept and hook first, script approval before building, the mandatory second pass on every export, and delivery as drive links with ready-to-post captions.

**Stage 5: A test job.** Run one small real job end to end with the user: they pick the clips and name the album, the Edits agent writes the concept and hook first, any script is approved before building, the cut is verified on the exported file, and the result is delivered as a drive link with a caption. The setup is not done until the user has watched that export.

**Stage 6: The practice system.** Only after the test job lands, set up the nightly practice system. The full instructions are in `prompts/02-practice-system.md` and section 5 of `BRIEFING.md`: three sets a night (benchmark, fresh, experiment), the independent reviewer working to `prompts/03-reviewer-rubric.md`, the 100 point rubric with its pass mark and moving goalpost, the nightly build and morning delivery schedules, and the event driven watchdog. Create the system's files from the templates in `templates/` (system file, feedback file, run log) and tell the user where each one lives. Show the user a sample morning delivery from a real practice night before the schedule goes live.

## When a stage goes wrong

- **A connection or app will not verify.** Stop the stage, name the missing piece in one sentence, and give the shortest fix. Offer to continue with a later, independent stage only if it does not depend on the missing piece.
- **The user's clips are not on the Mac yet.** Explain the sync wait plainly, suggest opening Photos on the phone on Wi-Fi while charging, and agree a time to re-check the library count. Never build a test job from a partial set without telling the user it is partial.
- **The user wants to stop mid-onboarding.** Save the checklist with the current stage marked, and tell them exactly which stage to resume from and what to say ("resume my Edits agent setup from stage [n]"). A resumed session re-reads this file and the checklist before continuing.
- **The user disagrees with a taste default in the briefing or charter.** Their taste wins, always. Adapt the charter, note the change in the checklist, and move on. The structure is the spec; the taste settings are theirs.

## When you finish

Close the onboarding with a short summary the user can keep: what was set up, where the Edits agent chat is, where the practice files live, when the nightly build and morning delivery run, and the one thing the user does each morning (review the delivery table and give feedback). Then hand over: from here, editing requests go to the Edits agent chat, and this chat stays the user's general assistant.

One adjacent note: this repo also carries `DESIGN-SYSTEM.md`, a design language for boards and spaces. It is not part of this onboarding. If the user later asks for a space, tracker or board, build it to that file, and at the same time start a UI learnings doc from `templates/UI-LEARNINGS.md` in your workspace: every correction or preference the user gives on a space goes into it as a dated entry, you read it before every space build or edit, and when a learning hardens into a standing rule you distill it into your copy of the design system. The spec stays the present-tense truth; the learnings doc is the history behind it. The repo also carries ready-made templates for two specific spaces under `spaces/` (an Invites tracker and a Brand Collabs tracker) with a build prompt at `prompts/04-spaces-build.md`; offer them if the user wants trackers like those.

## If the user asks what this costs or how long it takes

Be honest and plain: stages 1 to 5 fit in one sitting of about an hour, most of it waiting on installs and sync (the stage 3 style study can run while clips sync). Stage 6 is a second, shorter sitting. The nightly practice uses real plan allowance, which is why the briefing recommends a paid plan and weekday scheduling.
