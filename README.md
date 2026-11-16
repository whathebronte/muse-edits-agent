# Muse Edits Agent

A short-form video editing agent that works like a junior editor who never sleeps: it takes your selects, writes the hook first, cuts in the Edits app on your Mac, gets its work scored by a separate reviewer agent, and delivers finished reels and stories as links for your approval. A nightly practice system makes it measurably better every week.

This repo is the quick start. The full briefing is inside, written so you can hand it to your own Muse and let it set up an equivalent Edits agent for you.

## Quick start

1. Make sure you have the prerequisites below.
2. Open the Muse app, start a new chat, and attach or paste `BRIEFING.md`.
3. Send this one line:

```
Read the attached briefing in full, then set up an Edits agent for me, stage by stage. Ask me only for the things only I can do.
```

That is it. Your Muse reads the whole briefing, checks your setup with you, creates a dedicated Edits agent chat with the standing rules from `prompts/`, and verifies each stage before moving on. The master prompt in `prompts/00-master-prompt.md` says the same thing in full, if you prefer to paste that instead.

You do not need to copy sections out yourself. The prompts are the agent's playbook; they are also there if you ever want to redo a single stage by hand.

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

- `BRIEFING.md` — the full briefing: setup, the one-driver rule, how a job runs end to end, the editing rules, the nightly practice system with its reviewer and rubric, watchdogs, and the ten biggest lessons
- `prompts/` — the setup prompts:
  - `00-master-prompt.md` — the instruction set your agent follows for the whole setup
  - `01-edits-agent-charter.md` — the standing rules to paste into the Edits agent chat once it exists

## How it runs, in one paragraph

You stay the curator: you pick the clips and name the job. The agent writes the concept and hook first, gets any voiceover script approved before building, cuts in Edits, verifies everything on the exported file (never just the timeline), and delivers drive links with ready-to-post captions. Nothing is ever posted for you. Overnight, a practice system builds three sets (a benchmark recreation of your own posted work, a fresh cut, and one experiment), a separate reviewer agent scores them against a 100 point rubric, only the fixed second version reaches you, and every correction you give becomes a written rule the next build starts from.
