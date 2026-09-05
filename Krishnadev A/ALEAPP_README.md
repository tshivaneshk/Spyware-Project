# ALEAPP — Static / Forensic

> Android Forensics / Device Analysis — Project Review

## 📌 Purpose & Scope

**ALEAPP (Android Logs Events And Protobuf Parser)** is an open-source, offline-oriented Android forensic artifact parser.

It is primarily used **after Android data has already been acquired** from a device, backup, file-system extraction, or forensic image. ALEAPP parses supported Android artifacts and converts raw files into investigator-readable tables, reports, and timelines.

> **Key point:** ALEAPP is an **analysis/parsing layer**, not a device-extraction tool.

## ⚠️ Privacy & Ethics

Android forensic artifacts can contain highly sensitive information such as messages, contacts, location history, browser activity, account identifiers, application data, and device logs.

ALEAPP should only be used on data that the investigator is authorized to examine.

For forensic work:
- Preserve the original evidence.
- Record acquisition details and hashes.
- Work from an appropriate forensic copy whenever practical.
- Document parser versions and investigative observations.

## 🔍 What It Does

- Accepts an Android forensic extraction or artifact directory as input.
- Identifies and parses supported Android databases, XML/JSON/protobuf files, logs, and application artifacts.
- Normalizes parsed records into investigator-readable tables containing timestamps, identifiers, paths, and artifact-specific fields.
- Correlates artifacts into useful investigative views such as activity timelines and application usage.
- Generates HTML-based forensic output and structured results.
- Provides a repeatable post-acquisition parsing workflow.

## ✨ Features

- Open-source and scriptable Python-based forensic parsing workflow.
- Large collection of artifact parsers covering Android system and third-party application data.
- Support for common Android databases, XML preferences, logs, protobuf-related artifacts, and filesystem structures.
- Artifact-specific parsers rather than treating all files as generic text.
- Timeline-friendly output using artifact timestamps and normalized fields.
- HTML report generation for investigation and review.
- Extensible parser architecture.
- Useful alongside acquisition tools such as ADB, mobile forensic suites, backups, and filesystem extractions.

## 🛠️ Tech Stack

| Layer | Technology / Approach | Purpose |
|---|---|---|
| Input / Evidence | Android extraction, filesystem dump, backup, or artifact directory | Source data for offline analysis |
| Parser Engine | Python-based ALEAPP modules | Identify and parse Android artifacts |
| Artifact Parsers | SQLite, XML, JSON, protobuf/log-oriented parsers | Convert artifacts into structured records |
| Normalization | Common field and timestamp handling | Present heterogeneous artifacts consistently |
| Reporting | HTML and structured parser output | Human-readable forensic review |
| Automation | Command-line execution / repeatable scripts | Batch and repeatable analysis |
| Extensibility | Modular parser design | Add or update artifact-specific parsers |
| Platform | Cross-platform Python environment | Investigator workstation |

## 🏗️ Architecture

```text
Android Evidence / Extraction
            |
            v
   Input & Artifact Discovery
            |
      +-----+-----+
      |           |
      v           v
System Artifact  App Artifact
   Parsers          Parsers
(SQLite/XML/     (databases/
 logs/etc.)       app files)
      |           |
      +-----+-----+
            |
            v
  Parsing & Normalization
            |
            v
   Parsed Artifact Tables
            |
            v
   HTML / Structured Output
            |
            v
     Investigator Review
```

### Key Distinction

ALEAPP generally does **not** bypass a device lock or acquire a live device by itself.

```text
Acquisition → ALEAPP Parsing → Analysis / Reporting
```

An acquisition source must provide the artifacts that ALEAPP can process.

## ⚙️ Setup & Licensing

ALEAPP is an **open-source project**, rather than a closed commercial forensic suite.

A typical setup consists of:
1. Obtain the project source.
2. Install the required Python dependencies.
3. Prepare the Android forensic working directory.
4. Run the parser against the available evidence.

> Exact installation commands and supported versions can change between project releases. Consult the current project documentation before deployment.

## 🚀 Usage

1. Preserve the original acquisition.
2. Work from an appropriate forensic copy.
3. Identify the Android extraction/artifact directory.
4. Run ALEAPP with the required input and output parameters.
5. Allow artifact-specific parsers to process the available files.
6. Review generated HTML reports and artifact tables.
7. Correlate timestamps and records across applications and system artifacts.
8. Record the parser version, acquisition source, relevant hashes, and observations.

### Conceptual Workflow

```text
ALEAPP input_directory
        ↓
Parser execution
        ↓
Output / Report directory
```

## 📁 Project Structure

```text
ALEAPP
├── Main execution / CLI
├── Artifact parsers
│   ├── Android system artifacts
│   ├── Application artifacts
│   ├── Logs / databases / preferences
│   └── Specialized artifact modules
├── Shared parsing / utility functions
├── Report / output generation
└── Tests / parser validation
```

## 🔬 Forensic Workflow Position

| Stage | Typical Tool / Method | ALEAPP's Role |
|---|---|---|
| Acquisition | ADB, backup, filesystem extraction, forensic platform | Usually upstream |
| Preservation | Evidence copy, hashes, case documentation | External forensic process |
| Parsing | ALEAPP | **Primary role** |
| Correlation | Analyst + parsed tables/timeline | Supports through structured output |
| Reporting | ALEAPP HTML output + investigator notes | Produces reviewable results |
| Validation | Independent artifact/source verification | Recommended |

## 💡 Why This Architecture?

### Separation of Acquisition and Analysis
Raw evidence can be acquired by different tools and then parsed independently.

### Artifact-Specific Parsing
Android applications and system components store information in different formats, so dedicated parsers improve interpretation.

### Repeatability
The same evidence directory can be processed again using documented parser versions and settings.

### Readable Reporting
Structured artifact tables make large collections of raw files easier to inspect and correlate.

### Open-Source Extensibility
Investigators and developers can inspect, maintain, test, and extend parser logic.

### Non-Destructive Analysis
Parsing a working copy avoids altering the original acquisition merely to inspect its contents.

## ⚠️ Limitations & Considerations

- ALEAPP can only parse artifacts that are actually present and supported.
- Unavailable or encrypted application data cannot be reconstructed merely by parsing.
- Results depend heavily on the quality and completeness of the upstream acquisition.
- Android versions and application updates can change schemas, paths, timestamps, and storage formats.
- Parsed timestamps and identifiers should be interpreted in the context of the underlying artifact and acquisition metadata.
- Important forensic findings should be validated against the underlying source artifacts.

## 🔄 Relationship With ADB + dumpsys

```text
                 Android Device
                      |
                      v
                ADB + dumpsys
             Dynamic / Live Analysis
                      |
                      v
             Collected Artifacts
                      |
                      v
                   ALEAPP
             Static / Offline Analysis
                      |
                      v
             Reports / Timelines
```

## 📚 Summary

**ALEAPP is best suited for static/offline Android forensic analysis.**

It takes previously acquired Android data, parses supported artifacts, normalizes the information, and produces structured results suitable for investigation and correlation.

Its role is therefore primarily **post-acquisition forensic analysis**.
