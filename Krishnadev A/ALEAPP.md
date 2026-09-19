# ALEAPP: Static and Forensic Android Analysis

## Purpose

ALEAPP, or Android Logs Events And Protobuf Parser, is an open-source tool for parsing Android forensic artifacts.

It is primarily used after data has been acquired from a device, backup, filesystem extraction, or forensic image. ALEAPP processes supported Android artifacts and converts raw files into investigator-readable tables, reports, and timelines.

The key point is that ALEAPP is an analysis and parsing tool, not a device-extraction tool.

```text
Acquisition → ALEAPP Parsing → Analysis and Reporting
```

## Understanding

Android forensic artifacts can contain highly sensitive information, including messages, contacts, location history, browser activity, account identifiers, application data, and device logs.

ALEAPP should only be used on data that the investigator is authorized to examine.

For forensic work, investigators should:

* Preserve the original evidence.
* Record acquisition details and relevant hashes.
* Work from an appropriate forensic copy whenever practical.
* Document the ALEAPP version used.
* Record observations, assumptions, and any limitations.

## What ALEAPP Does

ALEAPP supports several parts of the post-acquisition forensic workflow:

* Accepting an Android forensic extraction or artifact directory as input.
* Identifying supported Android databases, XML and JSON files, logs, protobuf-related files, and application artifacts.
* Parsing artifacts with dedicated modules.
* Converting raw records into readable tables containing timestamps, identifiers, paths, and artifact-specific fields.
* Helping correlate records into activity timelines and application-usage views.
* Generating HTML reports and structured output.
* Providing a repeatable workflow for processing acquired Android data.

## Features

Important capabilities include:

* An open-source, Python-based parsing workflow.
* A collection of parsers for Android system and third-party application artifacts.
* Support for common databases, XML preferences, logs, protobuf-related files, and filesystem structures.
* Artifact-specific parsing rather than treating every file as generic text.
* Timeline-friendly output using artifact timestamps and normalized fields.
* HTML report generation for investigation and review.
* A modular architecture that can be extended with additional parsers.
* Compatibility with acquisition sources such as ADB collections, mobile forensic tools, backups, and filesystem extractions.

## Technology Stack

|Layer|Technology or approach|Purpose|
|-|-|-|
|Input and evidence|Android extraction, filesystem dump, backup, or artifact directory|Provides the source data for analysis.|
|Parser engine|Python-based ALEAPP modules|Identifies and parses Android artifacts.|
|Artifact parsers|SQLite, XML, JSON, protobuf, and log-oriented parsers|Converts artifacts into structured records.|
|Normalization|Common field and timestamp handling|Presents different artifacts in a consistent format.|
|Reporting|HTML and structured parser output|Supports human-readable forensic review.|
|Automation|Command-line execution and repeatable scripts|Enables batch processing and repeatable analysis.|
|Extensibility|Modular parser design|Allows artifact-specific parsers to be updated or added.|
|Platform|Cross-platform Python environment|Runs on the investigator's workstation.|

### Key Distinction

ALEAPP generally does not bypass a device lock or acquire a live device by itself. It requires an acquisition source containing artifacts that its parsers can process.

```text
Acquisition → ALEAPP Parsing → Analysis and Reporting
```

## Setup

ALEAPP is an open-source project rather than a closed commercial forensic suite.

A typical setup involves:

1. Obtaining the project source.
2. Installing the required Python dependencies.
3. Preparing the Android forensic working directory.
4. Running the parser against the available evidence.

Exact installation commands and supported versions may change between project releases. The current project documentation should be consulted before using ALEAPP in an investigation or production workflow.

## Usage Workflow

A typical workflow is:

1. Preserve the original acquisition.
2. Create or identify an appropriate forensic working copy.
3. Locate the Android extraction or artifact directory.
4. Run ALEAPP with the required input and output parameters.
5. Allow the artifact-specific parsers to process the available files.
6. Review the generated HTML reports and artifact tables.
7. Correlate timestamps and records across applications and system artifacts.
8. Record the ALEAPP version, acquisition source, relevant hashes, and investigative observations.

## Project Structure

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

## Why This Architecture Is Useful

### Separation of Acquisition

Evidence can be acquired using different tools and then processed independently with ALEAPP.

### Artifact-Specific Parsing

Android applications and system components store information in different formats. Dedicated parsers can interpret those formats more effectively than a generic text-processing approach.

### Repeatability

The same evidence directory can be processed again using documented parser versions and settings. This supports consistent review and validation.

### Readable Reporting

Structured artifact tables and HTML reports make large collections of raw files easier to inspect and correlate.

### Open-Source Extensibility

Because the project is open source, investigators and developers can inspect, maintain, test, and extend parser logic.

### Non-Destructive Analysis

Working from a forensic copy helps avoid altering the original acquisition during analysis.

## Limitations

* ALEAPP can only parse artifacts that are present and supported by its available parsers.
* Missing or encrypted application data cannot be reconstructed simply by parsing the available files.
* Results depend heavily on the quality and completeness of the upstream acquisition.
* Android versions and application updates can change database schemas, file paths, timestamps, and storage formats.
* Parsed timestamps and identifiers should be interpreted in the context of the underlying artifact and acquisition metadata.
* Important findings should be validated against the original or preserved source artifacts.
* A parser result is an interpretation of an artifact and should not be treated as independent proof without reviewing its source and context.

## 

