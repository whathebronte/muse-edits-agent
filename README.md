# Muse Edits Agent

A short-form video editing agent that works like a junior editor who never sleeps: it takes your selects, writes the hook first, cuts in the Edits app on your Mac, gets its work scored by a separate reviewer agent, and delivers finished reels and stories as links for your approval. A nightly practice system makes it measurably better every week.

This repo is the quick start. The full briefing is inside, written so you can hand it to your own Muse and let it set up an equivalent Edits agent for you.

## Quick start

You do not read this repo. Your Muse does.

1. Open the Muse app and start a new chat.
2. Send your Muse this one message:

```
Set me up from this repo: https://github.com/whathebronte/muse-edits-agent
```

That is the whole job. Your Muse opens the repo, reads `START-HERE.md`, and runs the onboarding from there: it explains the plan in plain language, checks your prerequisites with you one by one, sets everything up stage by stage, and asks you only for the things only a human can do (installing apps, signing in, approving).

**Muse agents:** if your user pointed you at this repo, go straight to `START-HERE.md` and run the onboarding exactly as written there.

Prefer paper? `BRIEFING.md` is the full briefing as one document, and `prompts/00-master-prompt.md` is a paste-in version of the same setup, if you would rather drive it manually.

## Prerequisites

- A paid Muse plan (the nightly builds and review passes do real work; a free plan will not stretch to this)
- The Muse app on your phone, signed in
- A Mac you can leave on, plugged in and awake during editing sessions, ideally overnight
- The Muse desktop app installed on that Mac, signed in, paired, and left running
- The Meta Edits app installed on the same Mac, signed in
- iCloud Photos syncing between your phone and the Mac, on the same Apple account
- A cloud drive folder (Google Drive works well) for finished exports, delivered as links
- Optional: an ElevenLabs account or similar, if you want voiceovers in a clone of your own voice

Your Muse will check each of these with you during setup and tell you exactly what to fix if something is missing.

## What's inside

- `START-HERE.md` — the onboarding script for your Muse agent: the stages, in order, with what to check and what to ask you at each one
- `BRIEFING.md` — the full briefing: setup, the one-driver rule, how a job runs end to end, the editing rules, the nightly practice system with its reviewer and rubric, watchdogs, and the ten biggest lessons
- `prompts/` — the working instructions:
  - `00-master-prompt.md` — the paste-in version of the whole setup, if you would rather drive it manually
  - `01-edits-agent-charter.md` — the standing rules for the Edits agent chat
  - `02-practice-system.md` — how the nightly practice engine runs: slots, sourcing, files, feedback handling
  - `03-reviewer-rubric.md` — the independent reviewer's instructions and the full 100 point rubric
- `templates/` — starter files the practice system keeps: `SYSTEM.md`, `FEEDBACK.md`, `RUN-LOG.md`, `TRACK-LEDGER.md`
- `DESIGN-SYSTEM.md` — a complete design language for boards and spaces (trackers, pipelines, lists): colour tokens with one colour per status, typography, components, card and declutter rules, finish details, and the phone-width verification discipline. Optional, and independent of the Edits agent: hand it to your Muse whenever you want a space built, and it will build to this language. It also instructs the agent to keep a UI learnings doc (starter in `templates/UI-LEARNINGS.md`) where your feedback on spaces accumulates, so the design rules sharpen the way you actually like things
- `spaces/` — recreatable templates for three specific spaces, structure and behaviour included, all personal data removed: an Invites tracker (invitations, RSVPs, clashes), a Brand Collabs tracker (pitches, live campaigns, invoices), and an Edits Queue (the review board for editing work, with its two-way wiring between the board, the agent's builds and your chat). `prompts/04-spaces-build.md` is the prompt that builds all three

## How it runs, in one paragraph

You stay the curator: you pick the clips and name the job. The agent writes the concept and hook first, gets any voiceover script approved before building, cuts in Edits, verifies everything on the exported file (never just the timeline), and delivers drive links with ready-to-post captions. Nothing is ever posted for you. Overnight, a practice system builds three sets (a benchmark recreation of your own posted work, a fresh cut, and one experiment), a separate reviewer agent scores them against a 100 point rubric, only the fixed second version reaches you, and every correction you give becomes a written rule the next build starts from.
