# Androguard — Android Static Analysis Tool Exploration Report

![Androguard](https://img.shields.io/badge/Androguard-4.1.4-3776AB?style=flat-square)
![Type](https://img.shields.io/badge/Type-Android%20Static%20Analysis-informational?style=flat-square)
![Language](https://img.shields.io/badge/Language-Python-3776AB?style=flat-square&logo=python&logoColor=white)
![Target](https://img.shields.io/badge/Target-Android%20APK-3DDC84?style=flat-square&logo=android&logoColor=white)
![Analysis](https://img.shields.io/badge/Analysis-DEX%20%7C%20AXML%20%7C%20XREF-black?style=flat-square)

---

## 1. Executive Summary

I explored **Androguard 4.1.4** to understand its capabilities as an Android static-analysis and reverse-engineering framework.

My practical work focused on analyzing the **Moglog Android APK** and examining the information that Androguard can extract from a compiled application. I studied the APK/AXML and DEX structures, inspected classes and methods, examined application-related components, and used cross-reference (XREF) information to understand relationships between code elements.

The exploration demonstrated that Androguard is useful for moving from basic APK information to a structured view of an application's classes, methods, libraries, and code relationships.

---

## 2. Objective

The objectives of my exploration were to:

- Understand what Androguard is and where it is useful.
- Understand how Androguard processes an Android APK.
- Examine Android Manifest/AXML information.
- Inspect DEX files, classes, methods, fields, and strings.
- Understand the purpose of the `core/analysis` component.
- Explore XREF relationships between methods and classes.
- Identify application-specific components and third-party frameworks.
- Understand the practical value and limitations of static analysis.

---

## 3. Tool Overview

**Androguard** is a Python-based framework for analyzing and reverse engineering Android applications.

An APK contains compiled application code and Android-specific structures. Androguard parses these structures and exposes them through Python objects and analysis APIs.

The areas I explored were:

- APK parsing
- Android Binary XML (AXML) parsing
- DEX parsing
- Class and method analysis
- Fields and strings
- Cross-reference (XREF) analysis
- Application structure identification
- Relationships between code elements

Although Androguard provides additional capabilities such as disassembly and decompilation, my practical exploration was focused on static structural analysis and XREF relationships.

---

## 4. Analysis Workflow

My exploration followed this workflow:

```text
Android APK
     |
     v
APK / AXML Parsing
     |
     v
DEX Parsing
     |
     v
Classes / Methods / Fields / Strings
     |
     v
Analysis
     |
     v
XREF Relationships
     |
     v
Application Structure
```

The APK analyzed during the exploration belonged to:

```text
com.skech.moglog
```

---

## 5. APK and AXML Analysis

### 5.1 APK Structure

An APK is the packaged form of an Android application. Important components include:

```text
AndroidManifest.xml
classes.dex
resources
other application files
```

Androguard provides programmatic access to these components.

### 5.2 Android Binary XML (AXML)

The `AndroidManifest.xml` inside an APK is stored using Android Binary XML (AXML).

Androguard parses this representation so that Manifest information can be inspected.

Manifest information can reveal:

- Package information
- Application components
- Permissions
- SDK information
- Activities
- Services
- Broadcast receivers
- Content providers

This makes AXML analysis useful for understanding the declared structure of an Android application.

---

## 6. DEX Analysis

Android application code is commonly stored in **DEX (Dalvik Executable)** files.

During my exploration, Androguard reported information for a DEX file including:

```text
DEX #13
Classes: 9130
Methods: 64614
Strings: 84354
```

The overall analysis reported:

```text
Total classes: 27853
Total analyzed methods: 196824
```

These figures indicate that the APK contained a large amount of compiled code, including application code and packaged libraries/frameworks.

From the DEX analysis, I was able to inspect:

- Classes
- Methods
- Fields
- Strings
- Code-related information
- Relationships between code elements

---

## 7. `core/analysis` and XREF Analysis

One of the important areas I examined was:

```text
androguard/core/analysis/
```

The analysis layer provides a higher-level representation of relationships within the parsed DEX information.

The distinction is important:

```text
DEX Parser
    |
    +-- What classes exist?
    +-- What methods exist?
    +-- What fields exist?
    |
    v
Analysis Layer
    |
    +-- How are code elements connected?
    +-- Which methods reference other methods?
    +-- Which classes are related?
    |
    v
XREF Information
```

### XREF

XREF means **cross-reference**.

XREF information can show relationships such as:

- Methods referencing other methods
- Methods referenced by other methods
- Classes connected through methods
- References to fields or other code elements

This changes the investigation from:

> What methods are present?

to:

> How are those methods connected?

That relationship information was one of the most useful parts of my exploration.

---

## 8. Findings from the Moglog APK

### 8.1 Application Components

The analysis output contained recognizable components associated with:

- ViewModels
- Repositories
- Models
- Cloud/data handling
- Dependency injection
- UI/framework code

Examples included:

```text
WatchViewModel
ShortcutRepository
CloudSyncRepository
ApiKeyManager
DramaRepository
```

These names helped me identify the general organization of the application from its compiled code.

### 8.2 Cloud Synchronization

I identified:

```text
CloudSyncRepository
    fetchFromCloud(...)
    syncToCloud(...)
```

and:

```text
CloudData
    getApiKey()
    getDramas()
```

These results indicated code associated with cloud synchronization and cloud data handling.

### 8.3 API-Key Related Code

I identified:

```text
ApiKeyManager
    getApiKey()
    getSyncFrequency()
```

This provided a useful starting point for examining API-key-related functionality.

The presence of an API-key-related method alone does **not** establish a security vulnerability. It only identifies code that would require further investigation.

### 8.4 Application Logic

The analysis contained methods such as:

```text
WatchViewModel->saveShortcut(...)
DramaRepository->saveDrama(...)
```

It also contained model methods such as:

```text
Drama->getId()
Drama->getStatus()
Drama->getCompletedAt()
Drama->getAddedAt()
```

These results helped identify application data handling and business-logic areas.

### 8.5 Frameworks and Libraries

The output also contained references to:

```text
Kotlin
AndroidX
Jetpack Compose
Material3
Kotlin Coroutines / Flow
Dagger / Hilt
Firebase
```

This helped distinguish application-specific code from framework and library code.

---

## 9. Practical Value

The information obtained from Androguard can support several stages of Android analysis:

| Area | Information obtained |
|---|---|
| Application identification | Package and APK information |
| Manifest analysis | Components, permissions, SDK information |
| Code discovery | Classes and methods |
| Data discovery | Fields and strings |
| Code relationships | XREF information |
| Architecture understanding | ViewModels, repositories, models |
| Technology identification | Framework and library classes |
| Investigation starting points | Security- or functionality-related code |

The main practical value I observed was the ability to trace relationships between code elements instead of only viewing isolated class and method names.

---

## 10. Limitations and Considerations

### Static Analysis Does Not Prove Runtime Execution

The presence of a method in an APK does not prove that it executes during normal application use.

### Large Analysis Output

The APK produced:

```text
27,853 classes
196,824 analyzed methods
```

This makes manual inspection impractical without filtering and targeted investigation.

### Generated and Framework Code

Kotlin, Jetpack Compose, AndroidX, and other libraries contributed significant amounts of code. This means application-specific logic must be distinguished from generated and third-party code.

### Obfuscation

Obfuscation can make classes and methods difficult to understand by replacing meaningful names with short or meaningless identifiers.

### Interpretation Is Required

Androguard provides structured analysis information, but the analyst must interpret it correctly. A discovered class or method should not automatically be treated as a vulnerability or security finding.

---

## 11. Key Takeaways

From this exploration, I learned that:

1. Androguard can programmatically parse Android APK structures.
2. AXML analysis provides access to Android Manifest information.
3. DEX analysis exposes classes, methods, fields, and strings.
4. The analysis layer provides relationships between code elements.
5. XREF information is particularly useful for tracing application logic.
6. Large applications require targeted filtering and investigation.
7. Static-analysis observations require additional evidence before being treated as security findings.

---

## 12. Conclusion

My exploration showed that Androguard is a useful **programmable static-analysis framework for Android applications**.

The most valuable capability I observed was its ability to combine parsed DEX information with XREF relationships. This allowed me to move beyond identifying what classes and methods exist and begin understanding how different parts of the application are connected.

Using the Moglog APK, I identified application components such as ViewModels, repositories, models, cloud-related classes, and framework dependencies. The analysis provided a structured starting point for further Android reverse engineering and security investigation.

Overall, Androguard provided a practical way to inspect the internal structure of a compiled Android application and understand relationships within its codebase.

---

## References

- [Androguard GitHub](https://github.com/androguard/androguard)
- [Androguard Documentation](https://androguard.readthedocs.io/)
