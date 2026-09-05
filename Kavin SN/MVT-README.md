<div align="center">

# MVT — Mobile Verification Toolkit
### Technical Review & Architecture Report

_Open-source forensic toolkit for detecting mobile spyware compromise_

![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?style=flat-square&logo=python&logoColor=white)
![License](https://img.shields.io/badge/License-MVT%20(MPL%202.0%20Modified)-informational?style=flat-square)
![Platform](https://img.shields.io/badge/Platform-Linux%20%7C%20macOS-lightgrey?style=flat-square)
![Interface](https://img.shields.io/badge/Interface-CLI-black?style=flat-square)
![Maintainer](https://img.shields.io/badge/Maintained%20by-Amnesty%20International%20Security%20Lab-red?style=flat-square)

[Overview](#overview) • [Features](#features) • [Tech Stack](#tech-stack) • [Architecture](#architecture) • [Setup & Installation](#setup--installation) • [Usage](#usage) • [Project Structure](#project-structure) • [Privacy & Ethics](#privacy--ethics) • [Limitations & Roadmap](#limitations--roadmap) • [Contributing](#contributing) • [License](#license)

</div>

---

## Overview

**MVT (Mobile Verification Toolkit)** is a command-line forensic toolkit released by the **Amnesty International Security Lab** in July 2021, developed in the context of the **Pegasus Project** investigation. It is designed to help technologists, investigators, and civil-society organizations determine whether a mobile device shows signs of compromise by known commercial spyware.

MVT does not exploit, root, or jailbreak a device. Instead, it analyzes forensic artifacts that have **already been extracted** through standard, non-invasive methods — an Android ADB backup/bugreport, or an iOS iTunes backup, filesystem dump, or sysdiagnose bundle — and cross-references them against published Indicators of Compromise (IOCs) for known spyware campaigns.

This document is a technical review of MVT's architecture, capabilities, and limitations, produced for academic/project evaluation purposes.

---

## Features

- Parses and normalizes forensic artifacts from **Android** (ADB backup, bugreport, AndroidQF collections) and **iOS** (iTunes backup, full filesystem dump, sysdiagnose)
- Matches extracted data (URLs, domains, process names, file paths) against **STIX2-format IOC feeds** using a high-performance multi-pattern matching engine
- Detects known spyware persistence techniques — malicious configuration profiles (iOS), Accessibility Service abuse (Android), suspicious background receivers and processes
- Generates a unified, chronological **timeline** of device activity for manual correlation
- Supports encrypted iOS backup decryption when the backup password is known
- Extensible **plugin architecture** — new artifact parsers can be added without modifying the core engine
- Optional VirusTotal integration for secondary hash/URL reputation checks
- Fully open-source and auditable — every detection decision can be traced back to source

---

## Tech Stack

| Layer | Technology | Purpose |
|---|---|---|
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
                 │   Android acquisition       │        │    iOS acquisition          │
                 │  bugreport · backup · qf    │        │ backup · filesystem · sysdiag│
                 └──────────────┬─────────────┘        └──────────────┬─────────────┘
                                │  raw extracted files                │
                                ▼                                     ▼
                 ┌────────────────────────────┐        ┌────────────────────────────┐
                 │     Android modules         │        │       iOS modules           │
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
                               │  Indicators engine           │
                               │  (Aho-Corasick automaton      │
                               │   vs. STIX2 IOC feeds)        │
                               └───────────────┬─────────────┘
                                                ▼
                               ┌─────────────────────────────┐
                               │  Alerts · Timeline · JSON     │
                               │  report output                │
                               └─────────────────────────────┘
```

**What flows between layers:** raw device dumps → platform-specific structured records → a shared plugin engine that orchestrates every module → a single pattern-matching pass against known-bad indicators → a human-readable report.

---

## Setup & Installation

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

> MVT officially supports Linux and macOS. Windows users are directed to the Docker image, as native Windows support is not provided.

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

## Privacy & Ethics

MVT is released under a **modified Mozilla Public License 2.0**, which adds a **Consensual Use Restriction** clause explicitly prohibiting use of the tool for non-consensual, adversarial forensics. It is designed exclusively for individuals or organizations examining a device they own or have explicit authorization to examine — not for surveillance of third parties.

Public IOC feeds alone are **not sufficient** to certify a device as "clean." A negative result only means no *known* indicator was matched — it does not rule out compromise by an undocumented or novel threat.

---

## Limitations & Roadmap

| Limitation | Notes | Suggested direction |
|---|---|---|
| Signature-based detection only | Cannot detect spyware with no published IOC | Add an optional statistical/anomaly-scoring layer as a supplement, not a replacement |
| No live/runtime scanning | Fully offline, post-hoc analysis | Out of scope by design — intentional for safety and auditability |
| iOS visibility gap | Full filesystem access needs jailbreak/forensic extraction tools | Continue expanding `mixed/` and `sysdiagnose` coverage, which need no jailbreak |
| Parser fragility | OS updates can silently change database schemas MVT depends on | Add schema-version detection and graceful degradation per parser |
| Pure-Python hot paths | Large dataset parsing can be slow outside the C-backed IOC matcher | Selectively port hot loops to Rust via `pyo3` bindings |

---

## Contributing

This repository section documents review findings and is maintained for project evaluation purposes. For contributions to the actual MVT project, refer to the upstream repository's `CONTRIBUTING.md` at [github.com/mvt-project/mvt](https://github.com/mvt-project/mvt).

---

## License

MVT is distributed under a modified Mozilla Public License 2.0 with a Consensual Use Restriction clause. See the upstream project's `LICENSE` file for full terms.

<div align="center">

_Reviewed and documented as part of an academic mobile forensics project evaluation._

</div>
