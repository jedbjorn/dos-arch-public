---
title: dos-arch
tags: [ai, substrate, agents, shells, meeting-intelligence]
date: 2026-06-20
project: dos-arch
purpose: A substrate for AI workflows — human and AI, doing more together
---

# dos-arch

[![Open in md-converter](https://img.shields.io/badge/Open%20in-md--converter-6b46c1?style=flat-square)](https://md-converter.designs-os.com/?url=https://github.com/jedbjorn/dos-arch-public/blob/main/README.md)

## What it is

dos-arch is a substrate for building AI workflows — a stack where models, agents, and human operators compose into something that does more than any of them alone.

The dominant conversation about AI in 2026 has narrowed to "can AI replace human workers." dos-arch is built around a different question: what do these workflows want to become when you take the material seriously?

> [!class1]
> This is the public home for dos-arch. The substrate is under active development — open releases will follow as the platform matures. Watch this repo.

### Four primitives

The substrate is built on four composable layers:

- **Model** — the material. dos-arch is model-agnostic: Anthropic, OpenAI, or local Ollama, hot-swapped per conversation.
- **Harness** — the software layer around the model: data records, output shape, automation, human-in-the-loop review.
- **Agent** — an AI instance with a specific directive, tool grant, and output shape. Named, testable, invokable on schedule or by a shell.
- **Shell** — an AI instance with *persistent identity the instance itself manages and revises* against the ongoing work.

### The shell concept

Memory systems exist. Identity-as-instance-curated against an ongoing directive is a different unit of abstraction — and the difference is load-bearing. A shell isn't "AI with memory." It's an agent whose identity — lessons, stances, a core seed — is managed by the agent itself, against its directive and the work it's doing. The substrate handles authentication, session management, and the memory store; the shell handles itself.

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
