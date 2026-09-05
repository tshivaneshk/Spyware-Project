# Frida — Dynamic Instrumentation & Android Exploration Report

![Frida](https://img.shields.io/badge/Frida-17.17.0-3F51B5?style=flat-square)
![Type](https://img.shields.io/badge/Type-Dynamic%20Instrumentation-informational?style=flat-square)
![Platform](https://img.shields.io/badge/Platform-Android-3DDC84?style=flat-square&logo=android&logoColor=white)
![Interface](https://img.shields.io/badge/Interface-CLI-black?style=flat-square)

---

## 1. Overview

Frida is a **dynamic instrumentation toolkit** used to inspect and interact with applications while they are running. It is commonly used for reverse engineering, security testing, debugging, and runtime analysis.

I explored Frida 17.17.0 with an Android emulator to understand its architecture, deployment models, and practical process-access workflow.

My practical work focused on setting up Frida Server, verifying communication with Android, enumerating processes, identifying the Moglog application, and investigating an attachment failure.

---

## 2. Frida Architecture

The main components I studied are:

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

| Component | Role |
|---|---|
| **frida-tools** | Command-line interface such as `frida`, `frida-ps`, and `frida-trace` |
| **frida-core** | Handles device communication, process access, sessions, and orchestration |
| **Agent** | Frida code running inside the target process |
| **frida-gum** | Low-level instrumentation engine for runtime/native instrumentation |
| **frida-server** | Separate process that enables external access to target processes |
| **Gadget** | Embedded Frida library that provides an alternative to external server-based access |

---

## 3. Server vs Gadget

The two deployment models I studied are:

```text
Server model:

PC → frida-tools → frida-core → frida-server → Target


Gadget model:

Target Application
       |
       └── Frida Gadget
```

**frida-server** runs separately on the Android device and allows Frida to access target processes externally.

**Gadget** is loaded inside the target application. It is useful when external process attachment is restricted or when embedded instrumentation is required.

Gadget is an alternative deployment mechanism; it does not replace `frida-gum`.

---

## 4. Practical Environment

| Component | Value |
|---|---|
| Frida | 17.17.0 |
| Android | 16 |
| SDK | 36 |
| Emulator ABI | x86_64 |
| Target | Moglog |
| Package | `com.skech.moglog` |
| Host | Windows |

---

## 5. Frida Setup

I installed the Frida CLI tools with:

```bash
pip install frida-tools
```

I verified the installation:

```bash
frida --version
```

Result:

```text
17.17.0
```

I verified the Android connection:

```bash
adb devices
```

The emulator was available as:

```text
emulator-5554    device
```

I confirmed the emulator architecture:

```bash
adb shell getprop ro.product.cpu.abi
```

Result:

```text
x86_64
```

---

## 6. Frida Server Setup

I pushed the matching Frida Server to the emulator:

```bash
adb push frida-server /data/local/tmp/frida-server
```

Then I made it executable:

```bash
adb shell chmod 755 /data/local/tmp/frida-server
```

I started it with:

```bash
adb shell /data/local/tmp/frida-server
```

Although an SELinux permission message was displayed, I verified that the server remained running using:

```bash
adb shell ps -A | findstr frida
```

---

## 7. Verifying Frida Communication

I used:

```bash
frida-ps -U
```

Frida successfully enumerated Android processes, including system and application processes.

This confirmed that:

```text
PC
 ↓
Frida
 ↓
Android Emulator
```

communication was working.

---

## 8. Moglog Process Exploration

I verified the Moglog package:

```bash
adb shell pm list packages | findstr moglog
```

Result:

```text
package:com.skech.moglog
```

After launching Moglog, I used:

```bash
frida-ps -U -a
```

Moglog appeared as:

```text
6687  Moglog  com.skech.moglog
```

This confirmed that Frida could identify the running application.

---

## 9. Attachment Attempt

I attempted to attach by name:

```bash
frida -U -n Moglog
```

and by PID:

```bash
frida -U -p 6687
```

Both attempts returned:

```text
Failed to attach: unable to access process with pid 6687
```

I verified that the PID was valid using:

```bash
adb shell pidof com.skech.moglog
```

which returned:

```text
6687
```

Therefore, the failure was not caused by an incorrect or stale PID.

---

## 10. Environment Investigation

I checked the Android build:

```bash
adb shell getprop ro.build.type
```

Result:

```text
user
```

ADB root access was unavailable on this build.

I also checked Moglog's package configuration and confirmed that it was **debuggable**:

```text
DEBUGGABLE
```

Its application UID was:

```text
uid=10220
```

Frida Server was running under the Android `shell` UID:

```text
uid=2000(shell)
```

I also verified:

```bash
adb shell run-as com.skech.moglog id
```

which successfully returned the Moglog application identity.

These checks helped establish that the attachment problem was related to the process-access environment rather than process discovery.

---

## 11. Results

| Activity | Result |
|---|---|
| Frida installation | Successful |
| Frida version verification | Successful |
| Android connection | Successful |
| Frida Server setup | Successful |
| Frida process enumeration | Successful |
| Moglog identification | Successful |
| Moglog PID verification | Successful |
| Direct process attachment | Unsuccessful |
| Moglog debuggable status | Confirmed |
| `run-as` access | Successful |
| Gadget study | Theoretical |

I did **not** record successful hooks or runtime method interception during this exploration.

---

## 12. Key Takeaways

- Frida provides runtime instrumentation rather than static APK analysis.
- `frida-tools` provides the CLI.
- `frida-core` manages communication and process sessions.
- The Agent operates inside the target process.
- `frida-gum` provides the underlying instrumentation capabilities.
- `frida-server` enables external process access.
- Gadget provides an embedded alternative.
- Android permissions and process identity can affect Frida attachment.
- Process enumeration does not guarantee successful instrumentation.

---

## 13. Conclusion

I successfully set up Frida 17.17.0 with an Android emulator and verified communication between Frida and Android. I was able to enumerate processes and identify the Moglog application.

Direct attachment to Moglog was unsuccessful in the given Android user-build environment. I investigated the process ID, application UID, Frida Server UID, debuggable status, and root availability to understand the limitation.

I also studied Frida Gadget as an alternative deployment model for environments where external process attachment is restricted.

Overall, this exploration gave me a practical understanding of **Frida's architecture, Android deployment, process discovery, and the factors affecting runtime instrumentation**.

---

## References

- [Frida Documentation](https://frida.re/docs/home/)
- [Frida Android Documentation](https://frida.re/docs/android/)
- [Frida Installation](https://frida.re/docs/installation/)
- [Frida Modes](https://frida.re/docs/modes/)
- [Frida Gadget](https://frida.re/docs/gadget/)
