---
title: dos-arch
tags: [ai, substrate, agents, shells, meeting-intelligence]
date: 2026-06-20
project: dos-arch
purpose: A substrate for AI workflows — human and AI, doing more together
---

# dos-arch

[![Open in md-converter](https://img.shields.io/badge/Open%20in-md--converter-6b46c1?style=flat-square)](https://md-converter.designs-os.com/?url=https://github.com/jedbjorn/dos-arch-public/blob/main/README.md)

## Overview

dos-arch is a substrate for building AI workflows — a stack where models, agents, and human operators compose into something that does more than any of them alone.

The dominant conversation about AI in 2026 has narrowed to "can AI replace human workers." dos-arch is built around a different question: what do these workflows want to become when you take the material seriously?

> [!class1]
> This is the public home for dos-arch. The substrate is under active development — open releases will follow as the platform matures. Watch this repo.

### Four primitives

The substrate is built on four composable layers:

- **Model** — the material. dos-arch is model-agnostic: Anthropic, OpenAI, or local Ollama, hot-swapped per conversation.
- **Harness** — the software layer around the model: data records, output shape, automation, human-in-the-loop review.
- **Agent** — an AI instance with a specific directive, tool grant, and output shape. Named, testable, invokable on schedule or by a shell.
- **Shell** — an AI instance with persistent identity it manages and revises itself against the ongoing work.

Workflows are what emerges when you compose those four — event- or schedule-driven sequences of agents and shells acting on each other and on the surrounding system. They aren't a fifth primitive; they're the point.

### The shell concept

Memory systems exist. Identity-as-instance-curated against an ongoing directive is a different unit of abstraction — and that difference does real work. A shell isn't "AI with memory." It's an agent whose identity — lessons, stances, a core seed — is managed by the agent itself, against its directive and the work it's doing. The substrate handles authentication, session management, and the memory store; the shell handles itself.

## Memory

A shell's identity lives in the substrate database. Three kinds of state, each with a different rhythm:

- **Seed** — up to ten identity-forming entries the shell keeps and curates. These are the things a shell considers foundational to who it is and how it works. The cap is enforced mechanically — planting an eleventh seed requires retiring an existing one first.
- **Lessons & Stances** — up to twenty operating principles the shell accumulates from doing the job. Capped the same way: curation, not accumulation.
- **Current state** — a rolling ~500-character now/next summary. Overwritten in place, never a log. A snapshot of where things stand.
- **Session narrative** — timestamped notes the shell appends at inflection points during a session: a decision landed, a course changed, something surprising surfaced.

The shell writes its own identity back as work happens, not at session close. Nothing is batched. The caps on seed and L&S aren't suggestions — they're DB triggers that reject an insert if the count is full. That makes "curate, don't accumulate" mechanical rather than aspirational.

### Boot document

Every shell has a rendered boot document — a structured system prompt assembled fresh from its DB state each session. It carries identity, current state, seed, lessons & stances, open flags, active projects, available skills, and the memory protocol the shell follows. Most of it stays in the model provider's cache between turns; a small live tail (the time, open flag count, unread inbox) refreshes each turn from current DB state.

### Data delivery windows

Shells can answer personal and contextual questions — *what meetings do I have Thursday?*, *how many contacts does a given org have?* — by querying the substrate directly. A unified search endpoint fans out across seven domains at once: contacts, orgs, emails, calendar, projects, flags, and meetings.

Results are delivered within a character budget sized to fit the shell's context window. When a large result set exceeds the budget, the endpoint progressively strips relational detail rather than dropping entire domains — so the shell always gets the top-level answer even when the fine grain has to be trimmed. A "return full context" button in the chat UI lets users expand any trimmed result on demand.

No vector database. The substrate is the retrieval store — structured by domain, filtered by term and time window.

## Skills

Skills are the procedural layer of the substrate — guidance documents the model follows when it's working. Every skill falls into one of two categories:

### Passive skills

Passive skills are inlined directly into the shell's boot document. The model has them in context at all times and can invoke them mid-turn without any user prompt. These are the always-on behaviors: writing memory, opening flags, recording decisions, retrieving context. If the shell is supposed to notice the right moment itself — a decision just landed, the session's heading changed — the guidance has to be present at all times. Passive is how that happens.

### Modal skills

Modal skills are loaded on demand, either by the user triggering a **Run Skill** action in the UI or by the shell pulling them in when a relevant situation arises. They carry the full procedure at runtime without occupying boot doc space on every turn. These are the purposeful actions: drafting an email, preparing for a meeting, writing up a project update, applying a tone to a document.

Some skills are both. `retrieve_context` — the substrate search skill — is passive so the shell can reach for it mid-conversation, and modal so a user can run a multi-domain search directly.

> [!class2]
> Tool schemas (what the model can call) and skill guidance (when and how to call it) travel through two completely separate channels. The schema has to be present every turn; the guidance only needs to arrive when the skill fires. That separation keeps the boot document lean across the full fleet of shells.

## Connected Data

dos-arch syncs your external accounts into the substrate — read-only, privately, and only for the contacts and context the shell has earned the right to see.

### Mail sync

Gmail is connected via IMAP. The sync worker performs a six-month backfill on first connect, then maintains live folder monitoring. Threads are ingested selectively: a thread only enters the substrate when at least one participant is already a known contact.

That rule is what keeps the inbox clean. You don't get a dump of your entire mailbox — you get the threads that involve people already in your world. New contacts aren't spawned from cold inbound; an existing known contact has to introduce them first.

### Calendar sync

Private iCal feeds are enrolled by URL. The worker fetches on a rolling three-month-back / three-month-forward window, respects ETags so it only re-fetches when something changed, expands recurring events correctly, and soft-deletes occurrences that disappear from the active window. Manual sync is a single click and fires promptly.

Like mail, calendar data is read-only toward the upstream provider. Nothing is written back.

### Contacts and organizations

Contacts and their organizations are the connective tissue that makes the rest useful. Mail and calendar data is project-scoped through contacts — a message or event only surfaces in a shell's context if a participant's organization maps to a project the shell is working on. Unfiled data stays unfiled.

```stats
:::class1
value: Read-only
label: Gmail access
description: No outbound email, no provider-side changes
:::class2
value: 6 months
label: Backfill window
description: On first connect; live IDLE monitoring after
:::class3
value: Contact-gated
label: Thread ingestion
description: Only threads with known participants enter
```

## Documents

A document in dos-arch is a markdown body stored in the substrate database. The document is the source of truth; every output format — raw markdown, rendered HTML — is derived from it on demand, never stored as a second copy that can drift.

Shells author and edit documents through structured API calls, not file I/O. A shell creates a document, edits its content, and the substrate renders it to whatever format the user requests at download time.

### Live rendering

The document viewer in the UI renders authored documents with full markdown support including diagrams. Documents can be anchored to a project, to a specific meeting, or to both — so a meeting's transcript, summary, and action items all live in one place, associated with the session that produced them.

> [!class1]
> dos-arch uses md-converter as its document render engine — the same app that renders this README. Markdown in, themed HTML out. The engine is a git submodule in the substrate.

## Meeting Intelligence

The first major feature built on the substrate. On-device transcription and speaker diarization for your video meetings — audio is never saved or uploaded, only the transcript text enters your workspace.

```stats
:::class1
value: On-device
label: Transcription
description: Runs locally — no audio leaves your machine
:::class2
value: Two-channel
label: Diarization
description: System audio and mic separated and attributed
:::class3
value: Real-time
label: Processing
description: Live during the meeting, ready when it ends
```

### How it works

- Captures system audio and microphone as separate channels during any video call
- On-device speaker diarization attributes who said what — no cloud service involved
- Transcript syncs to your dos-arch workspace for summarization, notes, and search
- A system-tray widget shows capture state; a non-suppressible notification runs during every session so the room knows

### Privacy

Audio is never written to disk or uploaded. The only thing that leaves your machine is the resulting transcript text, sent to your own dos-arch workspace. The non-suppressible notification is a hard design requirement, not a setting.

## Coming Soon

dos-arch is being built in the open as the platform matures.

```linear
Windows Capture :::class1 -> iOS + macOS Capture :::class2 -> Processing Pipeline :::class3 -> Full Platform :::class4
```

- **Windows capture** — shipping now via this repo's releases
- **iOS + macOS** — native capture clients in development; mic-only on iOS, dual-channel ScreenCaptureKit on Mac
- **Android** — manual capture client, planned
- **Processing pipeline** — auto-summaries, action items, and searchable transcripts from every meeting
- **Open platform** — once the substrate stabilizes, the deployment guide and shell tooling will follow

> [!class2]
> The private dos-arch repository is where active development happens. Public access to the full platform is on the roadmap — this repo is the first step out.
