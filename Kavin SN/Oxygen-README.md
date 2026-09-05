<div align="center">

# Oxygen Forensic Detective
### Technical Review & Architecture Report

_Commercial all-in-one mobile, cloud, and computer forensics platform_

![License](https://img.shields.io/badge/License-Commercial-orange?style=flat-square)
![Platform](https://img.shields.io/badge/Platform-Windows-0078D6?style=flat-square&logo=windows&logoColor=white)
![Category](https://img.shields.io/badge/Category-Digital%20Forensics-blueviolet?style=flat-square)
![Interface](https://img.shields.io/badge/Interface-GUI-black?style=flat-square)
![Vendor](https://img.shields.io/badge/Vendor-Oxygen%20Forensics%2C%20USA-red?style=flat-square)

[Overview](#overview) • [Features](#features) • [Tech Stack](#tech-stack) • [Architecture](#architecture) • [Setup & Licensing](#setup--licensing) • [Usage](#usage) • [Project Structure](#project-structure) • [Privacy & Ethics](#privacy--ethics) • [Limitations & Roadmap](#limitations--roadmap) • [Contributing](#contributing) • [License](#license)

</div>

---

## Overview

**Oxygen Forensic Detective** is a commercial, closed-source digital forensics platform developed by **Oxygen Forensics (USA)**, positioned as an alternative to Cellebrite and Magnet AXIOM. It is built for law enforcement agencies, government investigators, and corporate security/legal teams, providing end-to-end capability from **device extraction** through **evidence analysis** in a single environment.

Unlike open-source, read-only forensic tools, Oxygen Forensic Detective performs the extraction itself — including bypassing screen locks, decrypting encrypted storage, and pulling data from cloud accounts — before feeding everything into its own built-in analytics suite.

This document is a technical review of Oxygen Forensic Detective's capabilities and positioning, produced for academic/project evaluation purposes.

---

## Features

- **Device extraction** from iOS, Android, Windows Phone, BlackBerry, feature phones, SIM cards, drones, and computers
- **Chipset-specific screen lock bypass and physical acquisition** across Kirin, MediaTek (MTK), Qualcomm, Spreadtrum, and Samsung Exynos chipsets
- **Full-disk and file-based encryption decryption** (FDE/FBE), including automatic passcode brute-forcing at no additional charge
- **Cloud forensics** — extraction from 100+ cloud services (iCloud, Google, WhatsApp, Telegram, and more)
- Parses nearly **1,000 mobile applications** across more than 50,000 app versions, including encrypted messaging apps (WhatsApp, Telegram, Signal)
- **OxyAgent** — a companion app for logical extraction on unlocked Android devices, including third-party app data
- **KeyScout** — a portable field-acquisition utility
- Built-in analytics: cross-device/cross-case **Timeline**, **image and facial categorization** (age, gender, accessories, similar-face detection), **social graphing**, geolocation mapping
- **Third-party format interoperability** — imports UFED, XRY, AXIOM (.E01/.001), Berla iVe, and other forensic tool outputs
- Optical Character Recognition (OCR) for extracted images and documents

---

## Tech Stack

| Layer | Technology / Approach | Purpose |
|---|---|---|
| Extraction engine | Proprietary chipset-level exploits (Kirin, MTK, Qualcomm, Spreadtrum, Exynos) | Physical acquisition and screen lock bypass |
| Companion agent | OxyAgent (installable Android/iOS app) | Logical extraction from unlocked devices |
| Field acquisition | KeyScout (portable utility) | On-site device imaging |
| Decryption | Proprietary brute-force / dictionary / attack engine | FDE/FBE decryption, passcode recovery |
| Cloud acquisition | Proprietary connectors (100+ services) | Cloud account data extraction |
| Analytics | Proprietary computer vision, graph analysis, search indexing | Facial recognition, social graphing, cross-case search |
| Platform | Windows desktop application | Primary investigator workstation environment |
| Interoperability | Format importers (UFED, XRY, AXIOM, JTAG/Chip-off images) | Ingesting evidence acquired by other forensic tools |

> Oxygen Forensic Detective is closed-source. Internal implementation details beyond vendor-published documentation are not publicly available or independently auditable.

---

## Architecture

```
        ┌───────────────────────────┐   ┌───────────────────────────┐   ┌───────────────────────────┐
        │   Physical acquisition    │   │   Logical acquisition      │   │    Cloud acquisition        │
        │  chip-level dump + lock   │   │  OxyAgent on unlocked      │   │  100+ cloud service          │
        │  bypass + key extraction  │   │  device (apps, contacts)   │   │  connectors                  │
        └─────────────┬────────────┘   └─────────────┬──────────────┘   └─────────────┬────────────────┘
                       │                              │                                │
                       └──────────────┬───────────────┴───────────────┬────────────────┘
                                      ▼                               ▼
                       ┌───────────────────────────────────────────────────────┐
                       │        Decryption & normalization layer                │
                       │   (FDE/FBE decrypt, brute-force, app data parsing)     │
                       └───────────────────────────┬─────────────────────────┘
                                                    ▼
                       ┌───────────────────────────────────────────────────────┐
                       │             Case database (multi-device)               │
                       └───────────────────────────┬─────────────────────────┘
                                                    ▼
                       ┌───────────────────────────────────────────────────────┐
                       │   Analytics: Timeline · Facial ID · Social Graph ·     │
                       │   Geolocation · Cross-case Search · OCR                │
                       └───────────────────────────┬─────────────────────────┘
                                                    ▼
                                        Investigative report / evidence export
```

**What flows between layers:** raw device/cloud data (via exploit-based, logical, or cloud acquisition) → decryption and normalization → a persistent multi-device case database → a built-in analytics suite → exportable evidence for legal proceedings.

---

## Setup & Licensing

Oxygen Forensic Detective is **not open-source or freely distributable**. Access requires a commercial license from Oxygen Forensics, typically issued to:

- Law enforcement agencies
- Government investigative bodies
- Licensed corporate forensic/legal teams

Installation is via a vendor-provided Windows installer, accompanied by a hardware dongle or license key, and additional instructor-led training programs (e.g. *Extraction in a Box*, *Cloud Extraction*, *Advanced Analysis*) are offered separately by the vendor.

> No self-service installation or public download is available; access is gated by vendor licensing and verification of end-user eligibility.

---

## Usage

Typical investigator workflow (GUI-driven, not command-line):

1. Connect the target device or provide cloud account credentials/tokens
2. Select an extraction method: **Physical**, **Logical (OxyAgent)**, or **Cloud**
3. If locked/encrypted, apply the appropriate chipset lock-bypass or brute-force module
4. Import the resulting extraction into the case database
5. Run analytics: Timeline correlation, facial/image categorization, social graphing
6. Search across the current case or the entire historical case database
7. Export findings as a formatted evidence report

---

## Project Structure

As a closed-source commercial product, Oxygen Forensic Detective has no publicly inspectable source repository. Its documented functional structure is organized as:

```
Oxygen Forensic Detective (proprietary)
├── Oxygen Forensic Extractor      # Physical / logical / OxyAgent extraction engine
├── KeyScout                       # Portable field-acquisition module
├── Cloud Extractor                # 100+ cloud service connectors
├── Decryption Engine              # Brute-force, dictionary, FDE/FBE decryption
├── Analytics Suite                # Timeline, facial ID, social graph, OCR, geolocation
└── Case Database                  # Multi-device, cross-case storage and search
```

---

## Privacy & Ethics

Oxygen Forensic Detective is categorized by independent forensic-tool tracking sources under the purpose **"Perpetrator prosecution"**, with law enforcement as its intended user base. Unlike consent-based forensic tools, its core capabilities — screen lock bypass, encryption brute-forcing — are designed for use **without the device owner's cooperation**, under lawful authority (e.g. a warrant).

This is a standard dual-use consideration shared by all commercial forensic-extraction platforms (Cellebrite, Magnet AXIOM included): the underlying technical capability does not itself verify legal authorization, so responsible use depends on the licensing organization's internal legal compliance processes rather than a restriction built into the software itself.

---

## Limitations & Roadmap

| Limitation | Notes |
|---|---|
| Closed-source | No independent code audit possible; capabilities are vendor-reported |
| Cost barrier | Enterprise/law-enforcement licensing, not accessible to individuals or small civil-society organizations |
| Chipset dependency | Physical acquisition/lock-bypass coverage is tied to specific, continuously updated chipset support lists |
| No built-in consent safeguard | Legal/ethical use relies entirely on the licensing organization's own authorization process |
| Windows-only | No native macOS or Linux client |
| Rapid device churn | Requires continuous vendor engineering to keep pace with new phone models and OS security patches |

---

## Contributing

This repository section documents review findings for academic evaluation purposes only. Oxygen Forensic Detective is proprietary software; there is no public contribution pathway. Feature requests or support are handled directly through Oxygen Forensics' vendor support channels.

---

## License

Oxygen Forensic Detective is proprietary, commercially licensed software. All rights are held by Oxygen Forensics. This review references only publicly available vendor documentation and third-party reporting.

<div align="center">

_Reviewed and documented as part of an academic mobile forensics project evaluation._

</div>
