# ADB and dumpsys: Dynamic Android Tool

## Purpose and Scope

Android Debug Bridge (ADB) is a command-line interface that allows a workstation to communicate with an Android device or emulator.

The Android `dumpsys` utility provides diagnostic information from Android system services. Together, ADB and `dumpsys` are useful for observing a device while Android is running, rather than only examining an offline extraction.

This report describes their purpose, architecture, common commands, practical workflow, and forensic limitations.

## Privacy and Ethics

ADB access can expose device files, logs, application information, settings, and runtime state. Depending on the device configuration and available privileges, it may also allow commands to be executed on the device.

ADB should only be used on devices that you own or are explicitly authorized to analyze.

For forensic or investigative work, document:

* The device state.
* The authorization method.
* The commands executed.
* Collection timestamps.
* Any actions that could change device state.

## What ADB and dumpsys Do

ADB and `dumpsys` support several types of device and runtime analysis:

* Establishing communication between a workstation and an Android device.
* Providing shell access subject to device privileges and authorization.
* Collecting information about processes, services, packages, storage, logs, properties, and device state.
* Querying Android system services through `dumpsys`.
* Observing applications and system behavior while the device is running.
* Collecting selected files and command output for later offline analysis, including analysis with tools such as ALEAPP.

## Features

Important capabilities include:

* Communication over USB and supported network-based connections.
* Device discovery and authorization-status checking.
* Interactive Android shell access through `adb shell`.
* File transfer with `adb pull` and `adb push`, where permitted.
* Package and application inspection through package-manager commands.
* Runtime service inspection through `dumpsys`.
* Log collection with `logcat`.
* Device property and build-information collection.
* Process, activity, and service observation.
* Support for physical Android devices and emulators.

## Technology Stack

|Layer|Technology or approach|Purpose|
|-|-|-|
|Host interface|ADB client on Windows, Linux, or macOS|Communicates with the Android device.|
|Transport|USB or supported TCP/IP connection|Carries ADB protocol traffic.|
|Device daemon|`adbd`|Receives and processes authorized ADB requests.|
|Shell|Android shell and toybox utilities|Runs device-side diagnostic commands.|
|System diagnostics|`dumpsys` and Android system services|Exposes runtime service state.|
|Logs|`logcat` and the Android logging system|Collects runtime log messages.|
|Package analysis|`pm` and the package-manager interface|Inspects installed package state.|
|Device metadata|`getprop`, settings, and permitted procfs data|Collects system and runtime properties.|
|Analysis output|Captured command output and files|Provides material for later review or parsing.|

## Commands

### Check Connected Devices

```bash
adb devices
```

### Get Device Properties

```bash
adb shell getprop
```

### List Installed Packages

```bash
adb shell pm list packages
```

### Inspect the Activity Manager

```bash
adb shell dumpsys activity
```

### Inspect Package Information

```bash
adb shell dumpsys package
```

### Inspect Memory Usage

```bash
adb shell dumpsys meminfo
```

### Inspect Battery State

```bash
adb shell dumpsys battery
```

### Inspect Power State

```bash
adb shell dumpsys power
```

### Inspect Windows

```bash
adb shell dumpsys window
```

### Collect Logs

```bash
adb logcat -d
```

### Pull an Authorized File

```bash
adb pull <authorized\_path> <local\_output>
```

The commands available and the information they return depend on the Android version, device configuration, ADB authorization, application sandboxing, and available privileges.

## Setup and Access

A typical setup process is:

1. Install an appropriate Android Platform Tools package.
2. Enable the required developer and ADB settings on an authorized device.
3. Connect the device to the workstation.
4. Confirm that the device appears in the ADB device list.
5. Accept the authorization prompt if one appears.
6. Confirm the device identity and record relevant metadata.
7. Document whether the device is rooted, unlocked, encrypted, or otherwise restricted.

### Initial Verification

```bash
adb devices
adb shell getprop ro.product.model
adb shell getprop ro.build.version.release
```

These commands confirm that the device is visible and provide basic model and Android-version information.

## Suggested Analysis Workflow

A practical workflow is:

1. Identify and authorize the target device.
2. Record basic device and build information.
3. Collect relevant `dumpsys` service output.
4. Collect `logcat` output when runtime logging is relevant.
5. Inspect package, process, activity, storage, power, and battery state as required.
6. Save outputs in a case directory using descriptive filenames and timestamps.
7. Transfer authorized files to the workstation where appropriate.
8. Use ALEAPP or another forensic parser for deeper offline analysis when suitable artifacts are available.

## Folder Structure

```text
Android Platform Tools
├── adb client
│   ├── device discovery
│   ├── shell execution
│   ├── file transfer
│   └── log collection
│
└── Android Device
    ├── adbd
    ├── shell / toybox
    ├── logcat / logging system
    ├── package manager (pm)
    └── dumpsys
        ├── activity
        ├── package
        ├── battery
        ├── power
        ├── meminfo
        ├── window
        └── other Android services
```

## Dynamic and Static Roles

|Aspect|ADB and dumpsys|ALEAPP|
|-|-|-|
|Analysis mode|Dynamic or live-device analysis.|Static or offline artifact analysis.|
|Primary input|A running Android device or emulator.|Acquired Android files and artifacts.|
|Main function|Collects runtime state and communicates with the device.|Parses and interprets stored artifacts.|
|Timing|Observes the current state of the device.|Examines historical data present in the evidence.|
|Typical output|Command output, logs, service dumps, and copied files.|Structured artifact tables and reports.|
|Device interaction|Direct interaction with an authorized device.|Normally does not interact with a live device.|
|Best use|Runtime state, processes, services, logs, and configuration.|Historical application and system artifacts.|
|Relationship|Can provide material for later parsing.|Can analyze data collected upstream.|

## Why This Architecture Is Useful

### Visibility into Live State

`dumpsys` exposes runtime information that may not exist as one standalone file on the device.

### Android's System-Service Model

Many parts of Android maintain their state through framework services. `dumpsys` provides a diagnostic view of those services.

### Repeatable Command-Line Collection

Investigators can record the exact commands used and preserve their output for review, comparison, or later analysis.

### Separation of Responsibilities

ADB provides communication, while Android system services provide the runtime information being inspected.

### Complementary Analysis

ADB and `dumpsys` can collect live observations and selected artifacts. ALEAPP can then parse suitable offline data and help organize it into a more structured report.

### Broad Diagnostic Coverage

Different `dumpsys` services provide information about activities, packages, memory, power, battery, windows, and other Android components.

## Limitations

* ADB is not automatically a complete forensic acquisition method.
* Access is limited by device authorization, Android security controls, permissions, encryption, and debug configuration.
* Interacting with a live device can change volatile state.
* Processes, logs, timestamps, caches, and other runtime information may change during analysis.
* `dumpsys` output varies by Android version, device implementation, and system service.
* Non-rooted devices generally restrict access to protected application data and system areas.
* Network-based ADB should only be used in a controlled and authorized environment.
* Raw command output should be preserved whenever possible.
* Analysts should record the device state, command sequence, collection time, and actions taken.
* Dynamic observations should be distinguished from historical artifacts and interpreted in context.

## 

