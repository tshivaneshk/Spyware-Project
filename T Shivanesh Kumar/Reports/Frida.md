# Frida: Dynamic Tool Report

## Overview

Frida is a dynamic instrumentation toolkit used to inspect and interact with applications while they are running. It is commonly used for reverse engineering, security testing, debugging, and runtime analysis.

I tried Frida 17.17.0 with an Android emulator to understand its architecture, deployment models, and practical process-access workflow.

The practical work focused on setting up Frida Server, verifying communication with Android, enumerating processes, identifying the Moglog application, and investigating why attaching to the application failed.

## Frida Architecture

The main components examined during the exploration were:

```text
frida-tools
     |
frida-core
     |
+----+-------------+
|                  |
frida-server     Gadget
|                  |
+--------+---------+
         |
      Agent
         |
     frida-gum
         |
 +-------+-------+
 |               |
Native        Java/Android
```

### Components

|Component|Role|
|-|-|
|`frida-tools`|Provides command-line tools such as `frida`, `frida-ps`, and `frida-trace`.|
|`frida-core`|Handles device communication, process access, sessions, and instrumentation orchestration.|
|Agent|Frida code that runs inside the target process.|
|`frida-gum`|Provides the low-level engine for native and runtime instrumentation.|
|`frida-server`|Runs as a separate process on the Android device and enables external access to target processes.|
|Gadget|An embedded Frida library that provides an alternative to external server-based access.|

## Server and Gadget

Frida supports two main deployment models:

```text
Server model:

PC → frida-tools → frida-core → frida-server → Target


Gadget model:

Target Application
       |
       └── Frida Gadget
```

`frida-server` runs separately on the Android device and allows Frida to access target processes from the host computer.

Gadget is loaded inside the target application. It can be useful when external process attachment is restricted or when instrumentation needs to be embedded directly into the application.

Gadget is an alternative deployment mechanism. It does not replace `frida-gum`, which remains part of Frida's underlying instrumentation architecture.

## Frida Setup

I installed the Frida command-line tools using:

```bash
pip install frida-tools
```

The installation was verified with:

```bash
frida --version
```

The reported version was:

```text
17.17.0
```

I then checked the Android connection using:

```bash
adb devices
```

The emulator appeared as:

```text
emulator-5554    device
```

The emulator architecture was checked with:

```bash
adb shell getprop ro.product.cpu.abi
```

The result was:

```text
x86\_64
```

## Frida Server Setup

I pushed the matching Frida Server binary to the emulator with:

```bash
adb push frida-server /data/local/tmp/frida-server
```

I then made the file executable:

```bash
adb shell chmod 755 /data/local/tmp/frida-server
```

The server was started using:

```bash
adb shell /data/local/tmp/frida-server
```

An SELinux permission message was displayed during setup, but I confirmed that the server continued running with:

```bash
adb shell ps -A | findstr frida
```

## Verifying Communication

I used the following command to list processes visible to Frida:

```bash
frida-ps -U
```

Frida successfully enumerated Android processes, including system and application processes.

This confirmed that communication between the host computer, Frida, and the Android emulator was working.



## Moglog Process Exploration

I verified that the Moglog package was installed with:

```bash
adb shell pm list packages | findstr moglog
```

The result was:

```text
package:com.skech.moglog
```

After launching Moglog, I used:

```bash
frida-ps -U -a
```

The application appeared as:

```text
6687  Moglog  com.skech.moglog
```

This confirmed that Frida could identify the running application.

## Attachment Attempt

I attempted to attach to Moglog by name:

```bash
frida -U -n Moglog
```

I also attempted to attach using its PID:

```bash
frida -U -p 6687
```

Both attempts returned:

```text
Failed to attach: unable to access process with pid 6687
```

To confirm that the PID was still valid, I ran:

```bash
adb shell pidof com.skech.moglog
```

The command returned:

```text
6687
```

This showed that the failure was not caused by an incorrect or stale PID.

## Environment

I tried the Android build type with:

```bash
adb shell getprop ro.build.type
```

The result was:

```text
user
```

ADB root access was not available on this build.

I also checked Moglog's package configuration and confirmed that the application was debuggable:

```text
DEBUGGABLE
```

The application UID was:

```text
uid=10220
```

Frida Server was running under the Android `shell` UID:

```text
uid=2000(shell)
```

I also verified access through `run-as`:

```bash
adb shell run-as com.skech.moglog id
```

This command successfully returned the Moglog application identity.

These checks indicated that the attachment problem was related to the process-access environment rather than process discovery. In particular, being able to enumerate a process does not necessarily mean that the current Android security context permits Frida to attach to it.

## Results

|Activity|Result|
|-|-|
|Frida installation|Successful|
|Frida version verification|Successful|
|Android connection|Successful|
|Frida Server setup|Successful|
|Frida process enumeration|Successful|
|Moglog identification|Successful|
|Moglog PID verification|Successful|
|Direct process attachment|Unsuccessful|
|Moglog debuggable status|Confirmed|
|`run-as` access|Successful|
|Gadget study|Theoretical|

No successful hooks or runtime method interceptions were recorded during this exploration.

## References

* [Frida Documentation](https://frida.re/docs/home/)
* [Frida Android Documentation](https://frida.re/docs/android/)
* [Frida Installation](https://frida.re/docs/installation/)
* [Frida Modes](https://frida.re/docs/modes/)
* [Frida Gadget](https://frida.re/docs/gadget/)

