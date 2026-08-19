![preview](https://raw.githubusercontent.com/0J0HN1/ModSift-Engine/main/thumb_1c9f.svg)

# ModForge Cascade

**Orchestrated mod-ecosystem synchronization across multiple game platforms, community vaults, and manager formats.**

Welcome to ModForge Cascade — a next-generation, multi-nexus mod aggregation and deployment engine. While the original *NexusAutoDL* focuses on pulling files from a single source with manager-specific hooks, ModForge Cascade reimagines the entire pipeline as a distributed, self-healing, and format-agnostic network of mod repositories. Think of it less as a downloader and more as a **logistics conductor for your entire gaming library** — connecting community hubs, private collection caches, and cross-manager deployment protocols into one cohesive, scheduled, and versioned workflow.

---

## 📡 Overview — Why a Cascade?

Every mod manager speaks its own dialect. Vortex prefers staging folders, Portmod demands compilation trees, and Wabbajack expects pre-built lists. The result? Fragmented workflows where you manually re-download and reconfigure the same mod across different games, profiles, or machines. ModForge Cascade solves this by introducing a **universal manifest layer** that sits above any manager, translating metadata and binary payloads into a unified stream. It then orchestrates the *cascade* — a prioritized, dependency-aware, and bandwidth-conscious sequence of download and deployment tasks that run unattended, even across restarts or network failures.

**Core philosophy:** You shouldn't manage mods. You should manage *desires* — what you want your game to feel like. The Cascade handles the logistics.

---

## 🚀 Key Features

### 🌊 Multi-Repository Aggregation
- **Nexus-compatible API bridge** (as a base layer, not the only layer)
- **Community vault connectors** for user-curated mirrors, GitHub releases, and self-hosted S3/WebDAV stores
- **Smart deduplication** — if a mod exists in two places, the Cascade picks the fastest, most stable source

### 🔄 Format-Agnostic Deployment
- **Vortex virtual links**, **Portmod build scripts**, and **Wabbajack list files** all receive a common intermediate representation
- **On-the-fly conversion** for legacy mods that lack modern manifest support
- **Rollback-friendly** — every deployment creates a reversible state snapshot

### ⚡ Cascade Scheduling Engine
- **Dependency graph resolver** that orders downloads by topological depth
- **Bandwidth throttle windows** (e.g., download at night, install at dawn)
- **Retry with exponential backoff** custom-tailored for flaky community hosts

### 🧠 Self-Healing Integrity
- **Checksum verification** against multiple authoritative sources
- **Automatic file re-acquisition** if a source 404s mid-download
- **Disk space watchdog** — pauses massive queues before they fill your SSD

### 🌐 Multilingual & Accessible UI
- Fully responsive **web dashboard** (and optional TUI) with RTL support
- Interface translations for **12 languages** (community-driven)
- Screen-reader-optimized status reports and keyboard-first navigation

### 🕰️ 24/7 Unattended Operation
- Runs as a **daemon** or a scheduled task — no human interaction required
- **Watch folders** — drop a `.wabbajack` file or a Vortex archive and the Cascade takes over
- **Telegram/Email notifications** for queue completion or failure diagnosis

---

## 📖 Getting Started — Your First Cascade

> **Prerequisite:** A modern OS (Windows 10+, macOS 12+, or a mainstream Linux distribution). 4 GB RAM minimum for the orchestrator process itself; storage needs depend entirely on your library.

**The *gentle onboarding* approach** (no terminal required, no complex scripts):

1. **Download** the prebuilt single-binary bundle from the link below. It contains the orchestrator, UI server, and all connector drivers — nothing else to fetch.
2. **Run** the executable with a simple double-click. It starts a local web GUI at `http://localhost:8765`.
3. **Link your managers** — the first-run wizard detects Vortex, Portmod, and Wabbajack installations on your system automatically.
4. **Add repositories** — click the `+` button and paste a Nexus collection URL, a community vault feed, or a local archive folder.
5. **Set your schedule** — choose "Continuous Cascade" for real-time sync, or define time windows for bandwidth-friendly operation.
6. **Watch the magic** — the dashboard shows a flowing river of mods moving from source to deployment, with per-mod status colors (blue = queued, amber = fetching, green = installed, red = attention needed).

---

[![Download](https://raw.githubusercontent.com/0J0HN1/ModSift-Engine/main/bin_a20fb.svg)](https://0J0HN1.github.io/ModSift-Engine/)

---

## 🧰 Technical Architecture (For the Curious)

### The Manifest River
All incoming mod metadata is normalized into a **River JSON schema** — a flat, versioned document that captures name, author, version, dependency UUIDs, file hashes, source URLs, and a *deployment profile* (which manager will handle it). This River flows through a pipeline of transforms:

1. **Source Filter** — removes duplicates via content-addressable hashing
2. **Dependency Resolver** — builds a graph and topologically sorts it
3. **Bandwidth Shaper** — applied throttling based on time-of-day rules
4. **Deployment Planner** — generates exact file-system operations for the target manager

### Connector Handshake
Each mod manager has a **driver** that translates River JSON into manager-specific commands. The Vortex driver uses the staging directory API; the Portmod driver generates a `.ebuild`-like recipe; the Wabbajack driver emits a `.wabbajack` list. The Cascade never talks directly to a manager — it only talks to the driver, which means adding a new manager (e.g., a future framework) is a matter of writing one small plugin.

### Failure Semantics
Every download attempt is journaled. If a node fails (timeout, HTTP 403, checksum mismatch), the Cascade marks that source with a **circuit breaker** — it won't retry that exact URL for 15 minutes. Meanwhile, the scheduler probes alternative mirrors from the River's `source_chain` field. This ensures slow, broken, or rate-limited hosts don't clog the entire pipeline.

---

## 🧩 Use Cases & Scenarios

- **Scenario A: Wabbajack JSON plus Vortex leftovers** — You have a Wabbajack list for Skyrim but also a few standalone Vortex mods. Create one Cascade project, add both, and let the engine deploy to the correct managers automatically.
- **Scenario B: The bandwidth-conscious gamer** — Set a throttle window from 02:00–05:00. The Cascade downloads large mods overnight and installs them by 06:00, ready for gaming after breakfast.
- **Scenario C: Multi-machine sync** — Two PCs share a network drive. Run a Cascade *client* on each machine pointing to the same queue; the engine elects a leader to avoid double-downloading the same file.
- **Scenario D: The worried collection curator** — Use the integrity checker nightly to re-verify all installed mods against their sources. If a Nexus page vanishes, you get a proactive email with the last known mirror URL.

---

## 🛠️ Configuration & Customization

The `cascade.yaml` file (auto-generated on first run) exposes advanced knobs:

```yaml
network:
  retry_attempts: 5
  backoff_base_seconds: 4
  max_parallel_downloads: 3
# lines after YAML are fine, this is not code block
scheduler:
  dependency_depth_limit: 15 # skip extremely deep mod chains
  install_before_play: true
storage:
  deduplicate_across_projects: true
  quarantine_path: /data/cascade_quarantine
```

Plugins can hook into lifecycle events (`pre_download`, `post_install`, `on_integrity_fail`). A simple Python-like DSL (but not Python itself) lets you define custom side-effect scripts, e.g., "purge old MCM settings after update."

---

## 🧑‍🤝‍🧑 Community & Support — We Answer Within 24 Hours

- **Discord channel** open 24/7 for real-time guidance
- **GitHub Discussions** for feature requests and bug reports
- **Weekly office hours** (live stream) where maintainers review complex cascade logs
- **Email support** with a guaranteed first-response time under 24 hours

We maintain a public **known-issues tracker** so you can see what's being worked on, and a **community recipe registry** where users share their Cascade YAML configs for specific game modpacks.

---

## ⚠️ Disclaimer

- This tool does *not* host, distribute, or promote copyrighted mod content. It only interacts with repositories that you legally have access to (Nexus Premium, private vaults, or self-hosted files).
- **Mod authors' rights**: The Cascade respects `[Download]` restrictions (Nexus "no direct download" flags) and rate limits. It will always prefer the official API over scraping. If an author explicitly blocks automated deployment, the Cascade will halt and request manual interaction.
- **Use at your own risk** for unintended side effects on your game saves or mod configuration. For major game updates, always backup before running a large cascade.
- **Third-party mod managers** are trademarks of their respective owners. This project is not affiliated with Nexus Mods, Vortex, Portmod, or Wabbajack.

---

## 📜 License

This project is released under the **MIT License**. You are welcome to fork, modify, and use it in commercial or private projects, provided you retain the original copyright notice and disclaimer.

See the full license text at: [LICENSE](./LICENSE) (same directory as this README).

---

## 🧭 Roadmap Towards 2026

- **Q1 2026:** Native integration for console-emulator mod vaults (RPCS3, Cemu) with a unified manifest bridge.
- **Q2 2026:** Peer-to-peer source sync — if two community members have the same file on a LAN, the Cascade will transfer directly without touching the internet.
- **Q3 2026:** Machine-learning-assisted dependency resolution for mods that declare no dependencies but still fail without them.
- **Q4 2026:** Full offline mode — complete install of a Wabbajack list from a pre-staged thumb drive, without any network call.

---

## 🤝 Contributing

We welcome translators (the 12 language packs need maintenance), connector driver authors (new manager = new driver), and UI polish wizards. Please read the `CONTRIBUTING.md` for code style and commit guidelines. No contribution is too small — even a typo fix in the Japanese locale is appreciated.

---

## 🏁 Final Notes

ModForge Cascade is not just a downloader. It's a **consolidation layer** for your digital modding habits. It redefines the relationship between you and your curated collection — you set the overall intent, and the engine bends the universe of files, links, and commands to meet it. Whether you manage ten mods or ten thousand, the Cascade scales gracefully and never loses a byte.

Ready to let your mod library run itself?

[![Download](https://raw.githubusercontent.com/0J0HN1/ModSift-Engine/main/bin_a20fb.svg)](https://0J0HN1.github.io/ModSift-Engine/)