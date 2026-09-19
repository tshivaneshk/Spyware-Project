# MVT — Mobile Verification Toolkit 🔍

**Cyber Forensics & Spyware Compromise Detection Toolkit**
Built for civil-society investigators, journalists, and technologists analyzing suspected spyware targeting.

![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?style=flat-square&logo=python&logoColor=white)
![License](https://img.shields.io/badge/License-MVT%20(MPL%202.0%20Modified)-informational?style=flat-square)
![Platform](https://img.shields.io/badge/Platform-Linux%20%7C%20macOS-lightgrey?style=flat-square)
![Interface](https://img.shields.io/badge/Interface-CLI-black?style=flat-square)
![Maintainer](https://img.shields.io/badge/Maintained%20by-Amnesty%20International%20Security%20Lab-red?style=flat-square)

---

## ⚠️ Privacy & Ethics

MVT is released under a **modified Mozilla Public License 2.0**, which adds a **Consensual Use Restriction** clause. It is intended **exclusively for examining a device the user owns or is explicitly authorized to examine** — non-consensual, adversarial forensic use is prohibited by license.

Public Indicators of Compromise (IOCs) alone are **not sufficient to certify a device as "clean."** A scan returning no matches means no *known* spyware indicator was found — it does not rule out an undocumented or novel threat.

---

## What It Does

MVT never touches a live phone invasively — it never exploits, roots, or jailbreaks a device. Instead:

1. Someone extracts device data through **standard, built-in methods** — an Android ADB backup/bugreport, or an iOS iTunes backup, full filesystem dump, or Apple sysdiagnose bundle
2. MVT **reads that already-extracted data** (SQLite databases, plists, logs, config files) — the same way any script reads a file
3. Every parsed artifact (URLs, process names, file paths, domains) is **checked against known-bad indicators** published by threat-intel researchers (Amnesty, Citizen Lab, etc.) in the STIX2 format
4. Any match is raised as an **alert**, and everything is placed on a unified **timeline** for manual review
5. Findings are exported as a structured **JSON report**

---

## Features

- 📱 **Dual-platform support** — dedicated module families for both Android and iOS
- 🧬 **STIX2 IOC matching** — cross-references extracted artifacts against published spyware indicator feeds
- 🕵️ **Persistence-technique detection** — malicious iOS configuration profiles, Android Accessibility Service abuse, suspicious background receivers
- 🕒 **Unified timeline** — chronological view of all extracted events across every module
- 🔓 **Encrypted backup handling** — decrypts password-protected iOS backups when the password is known
- 🧩 **Plugin architecture** — new artifact parsers can be added without touching the core engine
- 🔍 **VirusTotal integration** — optional secondary hash/URL reputation lookups
- 🔎 **Fully auditable** — open-source; every detection decision traces back to readable source code

---

## Tech Stack

| Layer | Technology | Purpose |
|-------|-----------|---------|
| Core language | Python 3.10+ | Orchestration, parsing, CLI |
| CLI framework | Click 8.4 | Command structure (`mvt-ios`, `mvt-android`) |
| Terminal output | Rich 15.0 | Formatted tables, progress bars |
| IOC matching engine | pyahocorasick 2.3 (C extension) | Multi-pattern string matching (Aho-Corasick automaton) |
| Threat-intel format | STIX 2 | Industry-standard indicator exchange format |
| Data validation | Pydantic 2.13 / pydantic-settings | Typed config and structured module output |
| Android communication | adb-shell, libusb1 | Pure-Python ADB protocol, no Android SDK dependency |
| iOS backup handling | iOSbackup, cryptography 50.0 | Encrypted backup key derivation and decryption |
| Protobuf decoding | betterproto2 | Parses Android's binary bugreport format |
| Packaging | uv (lockfile-based) | Fast, reproducible dependency resolution |
| Containerization | Docker (3 images: general, Android, iOS) | Consistent extraction environment across OSes |

---

## Architecture

```
                 ┌────────────────────────────┐        ┌────────────────────────────┐
                 │   Android acquisition       │        │    iOS acquisition           │
                 │  bugreport · backup · qf    │        │ backup · filesystem · sysdiag│
                 └──────────────┬─────────────┘        └──────────────┬─────────────┘
                                │  raw extracted files                │
                                ▼                                     ▼
                 ┌────────────────────────────┐        ┌────────────────────────────┐
                 │     Android modules         │        │       iOS modules            │
                 │  dumpsys artifact parsers   │        │  mixed / fs / backup parsers │
                 └──────────────┬─────────────┘        └──────────────┬─────────────┘
                                │  structured results                 │
                                └───────────────┬─────────────────────┘
                                                ▼
                               ┌─────────────────────────────┐
                               │   MVTModule plugin engine    │
                               │  (module_loader, dependency  │
                               │   resolution, orchestration) │
                               └───────────────┬─────────────┘
                                                ▼
                               ┌─────────────────────────────┐
                               │  Indicators engine            │
                               │  (Aho-Corasick automaton      │
                               │   vs. STIX2 IOC feeds)         │
                               └───────────────┬─────────────┘
                                                ▼
                               ┌─────────────────────────────┐
                               │  Alerts · Timeline · JSON     │
                               │  report output                 │
                               └─────────────────────────────┘
```

**What flows between layers:** raw device dumps → platform-specific structured records → a shared plugin engine that orchestrates every module → a single pattern-matching pass against known-bad indicators → a human-readable report.

---

## Installation & Setup

```bash
# Install from PyPI
pip3 install mvt

# Or install from source
git clone https://github.com/mvt-project/mvt.git
cd mvt
pip3 install .

# Or run via Docker (recommended for Windows users)
docker build -t mvt .
```

> MVT officially supports Linux and macOS. Windows users are directed to the Docker image.

---

## Usage

```bash
# iOS: analyze a full filesystem dump against known IOCs
mvt-ios check-fs --iocs indicators.stix2 /path/to/filesystem_dump

# iOS: analyze an iTunes backup
mvt-ios check-backup --iocs indicators.stix2 /path/to/backup

# Android: analyze an ADB backup
mvt-android check-backup --iocs indicators.stix2 backup.ab

# Download and update public IOC feeds
mvt-ios download-iocs
```

---

## Project Structure

```
mvt/
├── src/mvt/
│   ├── common/         # Platform-agnostic engine: module base class,
│   │                   # module loader, IOC matching, alerts, CLI
│   ├── android/
│   │   ├── modules/    # androidqf, backup, bugreport, intrusion_logs
│   │   ├── artifacts/  # dumpsys_* structured parsers
│   │   └── parsers/    # raw backup / protobuf decoders
│   └── ios/
│       ├── modules/    # mixed, fs, backup, sysdiagnose parsers
│       └── data/       # static reference/baseline JSON data
├── tests/              # Test suite, mirrors src/ structure
├── docs/               # MkDocs documentation source
├── Dockerfile*         # General, Android, and iOS container definitions
└── pyproject.toml      # Dependency manifest
```

---

## Why This Architecture

MVT's design centers on **one core decision**: keep the detection engine completely ignorant of *which platform* it's analyzing, and push all platform-specific knowledge into small, disposable modules.

- **Plugin pattern over monolith** — every artifact parser (SMS, WhatsApp, dumpsys battery, Safari history) is a subclass of the same `MVTModule` base class. This means Amnesty's team can add support for a brand-new artifact type by writing one small file — the engine, IOC matcher, and CLI never need to change. This matters enormously for a project maintained by a small non-profit team that has to keep pace with constantly-changing phone OS internals.

- **Aho-Corasick for IOC matching** — with thousands of published indicators and potentially millions of extracted strings per scan, naive substring search would be far too slow. Building the indicator list into a single automaton once, then scanning each string in one pass, keeps scan time proportional to the data size — not the indicator list size. This was the correct algorithmic choice given the scale threat-intel feeds operate at.

- **Read-only, no-exploit design** — MVT deliberately never requires root, jailbreak, or any exploit to function. This is not a technical limitation but an intentional architectural boundary: it's what makes the tool legally distributable, safe for civil-society use, and auditable, at the cost of reduced visibility on non-jailbroken iOS devices.

- **Python orchestration + native hot paths** — the application layer is pure Python for readability and auditability (critical for a human-rights tool where trust matters as much as function), while the one performance-critical component (IOC matching) is offloaded to a C extension. This is a deliberate "readable where it matters, fast where it matters" trade-off rather than a full native rewrite.

- **STIX2 as the indicator format** — rather than inventing a proprietary indicator format, MVT consumes the same STIX2 feeds published by the wider threat-intelligence community, so it benefits from research done outside the project entirely.
