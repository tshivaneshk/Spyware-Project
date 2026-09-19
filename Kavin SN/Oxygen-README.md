# Oxygen Forensic Detective 🔓

**All-in-One Mobile, Cloud & Computer Forensics Platform**
Built for law enforcement agencies, government investigators, and licensed corporate forensic teams.

![License](https://img.shields.io/badge/License-Commercial-orange?style=flat-square)
![Platform](https://img.shields.io/badge/Platform-Windows-0078D6?style=flat-square&logo=windows&logoColor=white)
![Category](https://img.shields.io/badge/Category-Digital%20Forensics-blueviolet?style=flat-square)
![Interface](https://img.shields.io/badge/Interface-GUI-black?style=flat-square)
![Vendor](https://img.shields.io/badge/Vendor-Oxygen%20Forensics%2C%20USA-red?style=flat-square)

---

## ⚠️ Privacy & Ethics

Oxygen Forensic Detective is categorized by independent forensic-tool tracking sources under the purpose **"Perpetrator prosecution,"** with law enforcement as its intended user base. Unlike consent-based forensic tools, its core capabilities — screen lock bypass, encryption brute-forcing — are designed for use **without the device owner's cooperation**, under lawful authority (e.g. a warrant).

This is a standard dual-use consideration shared by all commercial forensic-extraction platforms (Cellebrite, Magnet AXIOM included): the underlying technical capability does not itself verify legal authorization — responsible use depends entirely on the licensing organization's internal legal compliance process, not on a restriction built into the software.

---

## What It Does

Unlike open-source, read-only forensic tools, Oxygen Forensic Detective performs the extraction itself, before feeding everything into its own built-in analytics suite:

1. Connect a target device, or supply cloud account credentials/tokens
2. Choose an extraction method — **physical** (chip-level dump with lock bypass), **logical** (via the OxyAgent companion app on an unlocked device), or **cloud** (100+ supported services)
3. If the device is encrypted or locked, apply the matching **chipset-specific bypass** or **brute-force decryption** module
4. Extracted data is imported into a persistent, searchable **case database**
5. Investigators run built-in **analytics** — timeline correlation, facial/image categorization, social graphing, geolocation mapping
6. Findings are exported as a formatted **evidence report** for legal proceedings

---

## Features

- 📱 **Broad device support** — iOS, Android, Windows Phone, BlackBerry, feature phones, SIM cards, drones, and computers
- 🔓 **Chipset-specific screen lock bypass & physical acquisition** — Kirin, MediaTek (MTK), Qualcomm, Spreadtrum, Samsung Exynos
- 🔑 **Full-disk & file-based encryption decryption** (FDE/FBE), including built-in automatic passcode brute-forcing
- ☁️ **Cloud forensics** — extraction from 100+ cloud services (iCloud, Google, WhatsApp, Telegram, and more)
- 💬 **Deep app coverage** — parses ~1,000 mobile apps across 50,000+ app versions, including encrypted messengers (WhatsApp, Telegram, Signal)
- 🕵️ **OxyAgent** — companion app for logical extraction on unlocked devices, including third-party app data
- 🎒 **KeyScout** — portable field-acquisition utility
- 📊 **Built-in analytics** — cross-device Timeline, facial/image categorization (age, gender, accessories, similar-face detection), social graphing, geolocation mapping
- 🔄 **Third-party format interoperability** — imports UFED, XRY, AXIOM (.E01/.001), Berla iVe, and JTAG/Chip-off images
- 🔤 **OCR** — text recognition on extracted images and documents

---

## Tech Stack

| Layer | Technology / Approach | Purpose |
|-------|-----------------------|---------|
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
                       │        Decryption & normalization layer                 │
                       │   (FDE/FBE decrypt, brute-force, app data parsing)      │
                       └───────────────────────────┬─────────────────────────┘
                                                    ▼
                       ┌───────────────────────────────────────────────────────┐
                       │             Case database (multi-device)                │
                       └───────────────────────────┬─────────────────────────┘
                                                    ▼
                       ┌───────────────────────────────────────────────────────┐
                       │   Analytics: Timeline · Facial ID · Social Graph ·      │
                       │   Geolocation · Cross-case Search · OCR                 │
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

Installation is via a vendor-provided Windows installer, accompanied by a hardware dongle or license key. Additional instructor-led training programs (e.g. *Extraction in a Box*, *Cloud Extraction*, *Advanced Analysis*) are offered separately by the vendor.

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

## Why This Architecture

Oxygen Forensic Detective's design centers on **one core decision**: unify extraction and analysis in a single platform, so an investigator never has to leave the tool to go from "raw device" to "courtroom-ready evidence."

- **Multiple acquisition paths feeding one pipeline** — physical, logical, and cloud extraction all funnel into the same decryption/normalization layer and the same case database. This lets an investigator use whichever method the device allows (a locked Android phone might only support physical acquisition; a cooperative device might use the faster logical OxyAgent route) without switching tools or workflows.

- **Chipset-specific exploit engineering** — because Android chipsets (Kirin, MTK, Qualcomm, Spreadtrum, Exynos) each implement lock and encryption differently at the hardware level, a single generic bypass method is not possible. Oxygen invests in maintaining a growing library of chipset-specific bypass methods — this is the architectural price of supporting non-cooperative extraction across a fragmented Android hardware ecosystem, and it's why chipset support lists are versioned and continuously updated.

- **Centralized, persistent case database over one-off scans** — unlike a tool that produces a single report per run, Oxygen stores every extraction in a searchable database that spans devices and cases. This reflects its actual use context: a law-enforcement agency accumulates many devices over many investigations, and cross-referencing (e.g. "has this phone number appeared in any other case?") is a core investigative need, not an afterthought.

- **Built-in analytics rather than external tooling** — facial recognition, social graphing, and OCR are integrated directly into the platform rather than left to separate tools. This trades modularity for investigator speed and evidentiary consistency — every analytic result stays tied to the same case record and chain-of-custody trail.

- **Closed, licensed distribution model** — because the extraction engine relies on proprietary, continuously-updated exploit research (a significant ongoing R&D cost), the business and architectural model is necessarily closed-source and license-gated, unlike a community-maintained open tool. This is a direct consequence of what the product does, not merely a business preference.
