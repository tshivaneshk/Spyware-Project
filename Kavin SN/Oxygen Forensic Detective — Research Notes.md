# Oxygen Forensic Detective – Research Notes

## 1. Company Background

Oxygen Forensics was founded in 2000. It was originally known as **Oxygen Software** and was mainly involved in PC-to-mobile synchronization software. Later, the company moved into the digital forensics field.

While researching the company, I also came across a few sources mentioning that Oxygen Software was originally based in Russia and later established its headquarters in Alexandria, Virginia, USA. I am not completely sure how relevant this is to the actual technical research, but I have included it because the information appeared in more than one source.

The company has worked with a number of government and law-enforcement organisations. Some of the publicly mentioned customers include the IRS, US Army, US Department of Justice, US Department of Homeland Security, European Commission and various police organisations.

## 2. Main Products

Oxygen Forensics has several products rather than only one application.

- **Oxygen Forensic Detective** – the main software used for extraction and forensic analysis.
- **Oxygen Forensic Kit** – a hardware and software package intended mainly for field investigations.
- **Oxygen Forensic Extractor** – an extraction solution that can also be provided to hardware manufacturers.

This also suggests that their extraction technology can be separated from the main analysis application, although I could not find enough public information to confirm exactly how the internal architecture works.

## 3. Data Extraction

From what I found, Oxygen mainly uses three types of extraction.

### Physical Extraction

This works at a lower level and can support different mobile chipsets, including Qualcomm, MediaTek, Samsung Exynos and Kirin.

### Logical Extraction

Oxygen uses an agent called **OxyAgent** for some logical acquisitions. The device generally needs to be accessible/unlocked, and information such as contacts, calls, messages and application data can be collected.

### Cloud Extraction

The software also supports extraction from a large number of cloud services. Examples include iCloud, Google services and WhatsApp backups.

There are also password recovery, brute-force and decryption capabilities for supported devices.

Another tool called **KeyScout** is designed for portable extraction during field investigations.

## 4. Programming Language / Technology

This was probably the hardest part to confirm.

Oxygen Forensics is closed-source, and I could not find an official document that clearly states the programming languages used to develop Oxygen Forensic Detective.

The application is a Windows desktop application and some of its functions need to communicate with USB devices and low-level mobile hardware.

C or C++ would be a reasonable assumption for some of these components, but I don't want to list that as a confirmed fact without a source. Similar forensic products use C++, Qt and C#/.NET, but that does not necessarily mean Oxygen uses the same technologies.

So, for now, the exact technology stack should be treated as **not publicly confirmed**.

## 5. Analysis Features

The software is not limited to acquiring data. It also provides different tools for analysing the collected information.

Some of the features I found are:

- Timeline analysis
- Social Graph
- Facial recognition and image categorisation
- Offline maps
- CDR analysis
- SQLite database viewer
- OCR
- Cross-case searching

The main idea is to take the large amount of data collected from a device and make it easier for an investigator to search, organise and connect the information.

## 6. CVE / NVD Research

I searched the NVD for vulnerabilities specifically associated with Oxygen Forensics, but I could not find a CVE assigned to the Oxygen software itself.

Something more interesting came up during the research. Oxygen's release information mentions using vulnerabilities in Android as part of some extraction techniques.

Two examples I found were:

- **CVE-2024-31317** – an Android vulnerability involving unsafe deserialization. Oxygen mentions the vulnerability in connection with selective application-data acquisition during certain Full File System extraction methods.
- **CVE-2025-0072** – another Android vulnerability that is mentioned in relation to Full File System extraction.

There are also MediaTek bootloader/BROM techniques used for some physical acquisition methods.

The important point here is that these CVEs are **vulnerabilities in Android or the device platform**, not vulnerabilities discovered in Oxygen Forensic Detective itself.

## 7. Other Findings

A few other things I came across during the research:

- Oxygen has its own certification called **OFC (Oxygen Forensic Certification)**.
- The software is regularly updated with new extraction, decryption and analysis capabilities.
- I also found academic work discussing Oxygen Forensic Detective and its use in digital investigations.

Overall, Oxygen Forensic Detective is more than an extraction program. It combines device/cloud acquisition with a large set of analysis and correlation features, which is probably why it is used mainly in professional forensic investigations.