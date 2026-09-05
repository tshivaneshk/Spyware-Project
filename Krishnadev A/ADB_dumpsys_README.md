# ADB + dumpsys — Dynamic / Device Analysis

> Android Forensics / Device Analysis — Project Review

## 📌 Purpose & Scope

**Android Debug Bridge (ADB)** is a command-line communication and control interface between a workstation and an Android device or emulator.

The Android **`dumpsys`** utility exposes diagnostic information from Android system services.

Together, ADB and dumpsys are useful for **dynamic/device analysis** because they can observe the device while Android is running rather than only examining an offline extraction.

## ⚠️ Privacy & Ethics

ADB access can expose device files, logs, application information, settings, and runtime state.

Depending on authorization and device configuration, ADB may also allow commands to be executed on the device.

Use ADB only on devices you own or are explicitly authorized to analyze.

For forensic work, document:
- Device state
- Authorization method
- Commands executed
- Collection timestamps
- Actions that could change device state

## 🔍 What It Does

- Establishes a workstation-to-Android communication channel through ADB.
- Provides shell access subject to device privileges and ADB authorization.
- Collects runtime information such as processes, services, packages, storage, properties, logs, and device state.
- Uses `dumpsys` to query Android system services for detailed runtime diagnostics.
- Supports dynamic observation of applications and system behavior.
- Can collect selected files or command output for later offline analysis, including with ALEAPP.

## ✨ Features

- USB and supported network-based device communication.
- Device discovery and authorization-status checking.
- Interactive Android shell through `adb shell`.
- File transfer using `adb pull` / `adb push`, subject to permissions.
- Package and application inspection through package-manager commands.
- Runtime service inspection through `dumpsys`.
- Log collection using `logcat`.
- Device properties and build-information collection.
- Process and activity/service observations.
- Support for physical devices and Android emulators.

## 🛠️ Tech Stack

| Layer | Technology / Approach | Purpose |
|---|---|---|
| Host Interface | ADB client on Windows/Linux/macOS | Communicate with Android device |
| Transport | USB or supported TCP/IP connection | Carry ADB protocol traffic |
| Device Daemon | `adbd` | Receives and executes authorized ADB requests |
| Shell | Android shell / toybox utilities | Run device-side diagnostic commands |
| System Diagnostics | `dumpsys` + Android system services | Expose runtime service state |
| Logs | `logcat` / Android logging system | Collect runtime log messages |
| Package Analysis | `pm` / package-manager interface | Inspect installed package state |
| Device Metadata | `getprop` / settings / procfs where permitted | Collect system and runtime properties |
| Analysis Output | Captured command output / files | Feed observations into later analysis |

## 🏗️ Architecture

```text
Investigator Workstation
          |
          | adb commands
          v
      ADB Client
          |
       USB / TCP
          |
          v
         adbd
          |
    +-----+------------------+
    |                        |
    v                        v
Android Shell             dumpsys
    |                  System Services
    |                 (activity, package,
    |                  battery, power...)
    |                        |
    +-----------+------------+
                |
                v
       Runtime / Device Output
                |
                v
       Capture & Forensic Notes
                |
                v
 Optional Offline Analysis
          (e.g. ALEAPP)
```

## 💻 Representative Analysis Commands

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

### Inspect Activity Manager

```bash
adb shell dumpsys activity
```

### Inspect Package Information

```bash
adb shell dumpsys package
```

### Inspect Memory

```bash
adb shell dumpsys meminfo
```

### Inspect Battery

```bash
adb shell dumpsys battery
```

### Inspect Power

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
adb pull <authorized_path> <local_output>
```

> Exact command availability and output depend on Android version, device configuration, ADB authorization, application sandboxing, and privileges.

## ⚙️ Setup & Access

Typical setup:

1. Install an appropriate Android Platform Tools package.
2. Enable the required developer/ADB configuration on an authorized device.
3. Connect the device.
4. Verify that the device appears in the ADB device list.
5. Accept the device authorization prompt if required.
6. Confirm device identity and record relevant metadata.
7. Document whether the device is rooted, unlocked, encrypted, or otherwise restricted.

### Initial Verification

```bash
adb devices
adb shell getprop ro.product.model
adb shell getprop ro.build.version.release
```

## 🚀 Usage

1. Identify and authorize the target device.
2. Record basic device/build information.
3. Collect relevant `dumpsys` services.
4. Collect `logcat` output when runtime logging is relevant.
5. Inspect package, process, activity, storage, and power/battery state as required.
6. Capture outputs into a case directory with command names and timestamps.
7. Transfer authorized artifacts to the workstation where appropriate.
8. Use ALEAPP or another forensic parser for deeper offline analysis.

## 📁 Project / Component Structure

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

## 🔄 Dynamic vs Static Role

| Aspect | ADB + dumpsys | ALEAPP |
|---|---|---|
| Analysis Mode | **Dynamic / live device analysis** | **Static / offline artifact analysis** |
| Primary Input | Running Android device/emulator | Acquired Android files/artifacts |
| Main Function | Collect runtime state and communicate with device | Parse and interpret stored artifacts |
| Timing | Observes current/live state | Analyzes historical data present in evidence |
| Typical Output | Command output, logs, service dumps, copied files | Structured artifact tables and HTML report |
| Device Interaction | Direct interaction with authorized device | Normally no live-device interaction |
| Best Use | Runtime state, processes, services, logs, configuration | Historical application/system artifacts and correlation |
| Relationship | Can provide material for later parsing | Can analyze data collected upstream |

## 💡 Why This Architecture?

### Live-State Visibility
`dumpsys` exposes information that may not exist as a single static file.

### System-Service Model
Android framework services maintain runtime state, while `dumpsys` provides a diagnostic view of many services.

### Command-Line Repeatability
Investigators can document exactly which commands were executed and capture their output.

### Layer Separation
ADB provides communication while Android services provide the actual device state being inspected.

### Complementary Workflow
ADB/dumpsys can collect live evidence, while ALEAPP can later parse suitable offline artifacts.

### Broad Diagnostic Coverage
Different `dumpsys` services provide perspectives on activities, packages, memory, power, battery, windows, and other Android system components.

## ⚠️ Forensic Considerations & Limitations

- ADB is not automatically a full forensic acquisition mechanism.
- Access is constrained by device authorization, Android security controls, permissions, encryption, and debug configuration.
- Live interaction can change volatile device state.
- Process state, logs, timestamps, caches, and other runtime information may change during analysis.
- `dumpsys` output is version- and service-dependent.
- Non-rooted devices generally restrict access to protected application data and system areas.
- Network ADB should only be used in a controlled, authorized environment.
- Preserve raw command output and record the device state, command sequence, collection time, and analyst actions.
- Dynamic observations should be distinguished from historical artifacts and interpreted with appropriate context.

## 🔗 Relationship With ALEAPP

```text
┌───────────────────────────┐
│       Android Device      │
└─────────────┬─────────────┘
              │
              v
┌───────────────────────────┐
│       ADB + dumpsys       │
│ Dynamic / Device Analysis │
└─────────────┬─────────────┘
              │
              v
┌───────────────────────────┐
│     Collected Evidence    │
│   Files / Logs / Output   │
└─────────────┬─────────────┘
              │
              v
┌───────────────────────────┐
│          ALEAPP           │
│ Static / Forensic Parsing │
└─────────────┬─────────────┘
              │
              v
┌───────────────────────────┐
│ Reports / Tables / Events  │
└───────────────────────────┘
```

## 📚 Summary

**ADB + dumpsys is best suited for dynamic Android device analysis.**

It provides communication with an authorized Android device and allows investigators to inspect runtime system information, services, packages, logs, and other device state.

Its role complements ALEAPP:

> **ADB + dumpsys → collect/observe live device state**  
> **ALEAPP → parse/analyze acquired Android artifacts**
