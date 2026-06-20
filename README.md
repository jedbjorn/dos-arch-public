# Meeting Capture — dos-arch

The Windows capture client for **dos-arch Meeting Intelligence**. It transcribes
your video meetings **on-device** for notes — audio is never saved or uploaded,
only the transcript text is sent to your dos-arch workspace.

## Download

**[⬇ Download the latest release](https://github.com/jedbjorn/dos-arch-public/releases/latest/download/MeetingCapture.exe)**

- Windows 10 / 11, 64-bit
- Self-contained — no .NET install required

Need a specific build? The [Releases page](https://github.com/jedbjorn/dos-arch-public/releases)
lists every version, with the version in the filename (e.g. `MeetingCapture-1.1.0.exe`).

## Setup

1. In the dos-arch web app, open **Account → Meeting capture** and turn on
   "Capture my meetings".
2. Click **Pair a device** to get a one-time pairing code.
3. Launch `MeetingCapture.exe`, then enter your **server URL** and the **pairing
   code** when prompted.
4. Done — the app lives in your system tray and transcribes capturable meetings
   according to your settings.

## Privacy

- Transcription and speaker diarization run **locally** on your machine.
- **Audio is never written to disk or uploaded** — only the resulting transcript
  text is submitted to your dos-arch workspace.
- A non-suppressible "Transcribing — no audio saved" notification is shown on the
  capturing machine for the entire session.

---

The capture client's source lives in the private dos-arch repository. This repo
hosts the public download and release notes only; releases are published
automatically by the dos-arch build pipeline.
