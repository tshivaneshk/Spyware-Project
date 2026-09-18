Androguard: Android Static Analysis Tool Report
Executive Summary
I explored Androguard 4.1.4 to understand how it can be used for Android static analysis and reverse engineering.
For the understanding, I analyzed the Moglog (Show tracking application I developed) Android APK. I examined its APK and Android Binary XML (AXML) structures, inspected DEX files, reviewed classes and methods, identified application components and third-party libraries, and used cross-reference information to understand relationships between code elements.
The results showed that Androguard is useful for moving from basic APK information to a more structured understanding of an application's code, architecture, and dependencies.
Objectives
The objectives were:
Understand what Androguard is and where it can be used.
Learn how Androguard processes an Android APK.
Examine Android Manifest information through AXML parsing.
Inspect DEX files, classes, methods, fields, and strings.
Explore cross-reference (XREF) relationships between methods and classes.
Understand limitations of static analysis.
Tool Overview
Androguard is a Python-based framework for analyzing and reverse engineering Android applications.
An APK has compiled application code and Android-specific resources. Androguard parses these structures and makes them available through Python objects and analysis APIs.
The main areas I went throgh were:
APK parsing.
Android Binary XML (AXML) parsing.
DEX parsing.
Class and method analysis.
Fields and strings.
Cross-reference analysis.
Application structure identification.
Relationships between code elements.
Androguard also supports capabilities such as disassembly and decompilation. However, this study focused mainly on structural analysis and XREF relationships.
Analysis Workflow
The exploration followed this general workflow:
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
The APK analyzed during the exploration belonged to the following package:
```text
com.skech.moglog
```
APK and AXML Analysis
APK Structure
An APK is the packaged form of an Android application. It commonly contains files and directories such as:
```text
AndroidManifest.xml
classes.dex
resources
other application files
```
Androguard provided programmatic access to these components, making it possible to inspect the internal structure of an APK.
Android Binary XML
The `AndroidManifest.xml` file inside an APK is stored in Android Binary XML (AXML) format rather than ordinary readable XML.
Androguard can parse this format and expose useful Manifest information, including:
Package information.
Application components.
Permissions.
SDK information.
Activities.
Services.
Broadcast receivers.
Content providers.
This makes AXML analysis useful for understanding the declared structure and capabilities of an Android application.
DEX Analysis
Android application code is commonly stored in DEX (Dalvik Executable) files.
During the exploration, Androguard reported the following information for one DEX file:
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
These figures show that the APK contained a large amount of compiled code. This included both application-specific code and code from packaged libraries and frameworks.
The DEX analysis made it possible to inspect:
Classes.
Methods.
Fields.
Strings.
Code-related information.
Relationships between code elements.
The Analysis Layer and XREFs
One of the most important areas examined was:
```text
androguard/core/analysis/
```
The analysis layer provides a higher-level view of the relationships within the parsed DEX data.
The distinction between parsing and analysis can be described as follows:
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
Cross-References
XREF stands for cross-reference. XREF information describes how different code elements are connected.
It can help identify relationships such as:
Methods that reference other methods.
Methods referenced by other methods.
Classes connected through method calls.
References to fields and other code elements.
It helped to provide context around individual classes and methods.
Findings from the Moglog APK
Application Components
The analysis output contained recognizable components associated with ViewModels, repositories, models, cloud or data handling, dependency injection, and user-interface frameworks.
Some examples included:
```text
WatchViewModel
ShortcutRepository
CloudSyncRepository
ApiKeyManager
DramaRepository
```
These names provided info about the general organization of the application and helped distinguish likely application logic from framework code.
Cloud Synchronization
The analysis identified the following methods and class:
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
These results suggested the presence of functionality related to cloud synchronization and cloud-hosted application data.
API-Key-Related Code
The analysis also identified an `ApiKeyManager` class with methods such as:
```text
ApiKeyManager
    getApiKey()
    getSyncFrequency()
```
This provided a useful starting point for further investigation into how API keys are obtained, stored, and used.
However, the presence of an API-key-related method does not by itself indicate a security vulnerability. It only identifies an area that may require additional analysis.
Application Logic
The analysis contained methods such as:
```text
WatchViewModel->saveShortcut(...)
DramaRepository->saveDrama(...)
```
It also identified model methods including:
```text
Drama->getId()
Drama->getStatus()
Drama->getCompletedAt()
Drama->getAddedAt()
```
These results helped to know application's business logic and data-handling structure.
Frameworks and Libraries
The output also contained references to several common Android and Kotlin technologies, including:
```text
Kotlin
AndroidX
Jetpack Compose
Material3
Kotlin Coroutines / Flow
Dagger / Hilt
Firebase
```
Identifying these technologies was useful for separating application-specific code from third-party libraries, generated code, and framework components.
Practical Value
The information obtained from Androguard can support several stages of Android application analysis:
Area	Information obtained
Application identification	Package and APK information
Manifest analysis	Components, permissions, and SDK information
Code discovery	Classes and methods
Data discovery	Fields and strings
Code relationships	XREF information
Architecture understanding	ViewModels, repositories, and models
Technology identification	Framework and library classes
Investigation planning	Security- or functionality-related code locations
The main practical value observed was the ability to trace relationships between code elements rather than viewing class and method names in isolation.
Limitations
Static Analysis Does Not Prove Runtime Execution
The presence of a method in an APK does not prove that the method executes during normal application use. Runtime behavior may depend on user actions, configuration, permissions, network responses, or other conditions.
Large Analysis Output
The APK produced the following results:
```text
27,853 classes
196,824 analyzed methods
```
With this much code, manually reviewing every class and method would be impractical. Filtering, searching, and targeted investigation are necessary.
Generated and Framework Code
Kotlin, Jetpack Compose, AndroidX, Firebase, and other libraries contributed significant amounts of code. As a result, application-specific logic must be separated from generated code and third-party components.
Obfuscation
Obfuscation can make classes and methods difficult to understand by replacing meaningful names with short or unclear identifiers. This can limit the usefulness of name-based searches and require analysis of method behavior and relationships instead.
Interpretation Is Required
Androguard provides structured information, but the analyst still needs to interpret the results correctly. A discovered class, method, string, or permission should not automatically be treated as a vulnerability or security finding.
References
Androguard GitHub
Androguard Documentation
