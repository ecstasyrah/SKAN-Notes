Specialized Digital Forensics and Incident Response: Windows Registry Forensics

## Course Introduction

- **Instructor:** Tyler Hudak, an incident responder with years of experience investigating cybersecurity incidents.
- **Registry Forensics** is one of the easiest and most valuable Windows Forensics techniques.
- Helps build a story about **what happened on the system** being analyzed.

## Course Goal

- Examine the Windows registry of a compromised system to figure out **what an attacker did after they broke into the system**.

## Major Topics

- **Finding and preparing the registry** on a Windows system for analysis.
- **Parsing the registry** to get information quickly.
- Locating important registry keys that provide information about:
  - **What files were accessed.**
  - **What programs were executed.**
  - **What backdoors were configured to be persistent.**

## Learning Outcome

- Know where key pieces of forensic data are within the registry.
- Understand **what information they provide**.

## Prerequisites

- Basic Windows functionality.
- Digital forensics best practices.

# Introduction

## Windows Registry Analysis Concepts

- The registry is a **primary source of information** during a Windows forensic investigation.
- This module covers:
  - Why investigators examine the registry.
  - Where registry files are located.
  - Common registry terminology.
  - Preparing the registry for analysis.

<details>
<summary>Windows Registry Concepts</summary>

## Why Examine the Registry?

- The Windows registry is a **configuration database** for the Windows operating system.
- Keeps information about the operating system, networking, and applications.
- Also contains artifacts of user and program activity.

| Information | Forensic value |
|---|---|
| **Files accessed** | Clues about what users were doing and what information they accessed. |
| **Programs executed** | Clues about an attacker’s actions and where to focus further analysis. |
| **Persistence** | Identify backdoors configured to start after a reboot or user logon. |

- Registry artifacts can provide evidence of activity, but they are not a complete record of every file access or program execution.

## Registry Terminology

| Term | Meaning | File system comparison |
|---|---|---|
| **Root key** | Top-level registry location. | Base of the file system. |
| **Key** | Contains subkeys and values. | Directory. |
| **Subkey** | A key beneath another key. | Subdirectory. |
| **Value** | A named entry containing data. | File. |
| **Data** | Information stored in a value. | File contents. |
| **Hive** | A collection of registry data, commonly backed by a file. | Storage containing part of the registry. |

## Five Root Keys

| Root key | Abbreviation | Contents |
|---|---|---|
| **HKEY_CLASSES_ROOT** | `HKCR` | File extension associations, COM registrations, and related information. |
| **HKEY_LOCAL_MACHINE** | `HKLM` | System-wide information, including local users, services, and applications. |
| **HKEY_USERS** | `HKU` | Loaded user hives, with subkeys identified by user SIDs. |
| **HKEY_CURRENT_USER** | `HKCU` | Settings for the current user. |
| **HKEY_CURRENT_CONFIG** | `HKCC` | A logical link to the current hardware profile under HKLM. |

- User registries contain information specific to the user and their applications.

## System Hive Locations

- Normally located in:

  `C:\Windows\System32\config`

- Main hives to collect:
  - **SAM**
  - **SECURITY**
  - **SOFTWARE**
  - **SYSTEM**
- The course recommends collecting **everything in this directory**, including associated logs.

## User Hive Locations

| Hive | Location |
|---|---|
| **NTUSER.DAT** | `%USERPROFILE%\NTUSER.DAT` |
| **UsrClass.dat** | `%USERPROFILE%\AppData\Local\Microsoft\Windows\UsrClass.dat` |

- Collect these hives for each user account.
- Also collect files beginning with **`NTUSER.DAT`** and **`UsrClass.dat`** so associated logs are included.

## Amcache

- An additional hive used when investigating program-related activity.
- Located at:

  `%SystemRoot%\AppCompat\Programs\Amcache.hve`

- Its contents and interpretation are covered later in the course.

</details>

<details>
<summary>Last Write Timestamp</summary>

## What Is Recorded?

- Each registry key has a **last-write timestamp**.
- Registry Explorer displays this timestamp for the selected key.
- The timestamp is updated when the key or its values are modified.

## Key Limitation

- Last-write timestamps belong to **keys**, not individual values.
- They show that something within the key changed.
- They do **not identify which specific value changed** at that time.

## Forensic Use

- Identify keys modified during a **time of interest**.
- Combine the timestamp with the key’s contents and other evidence.

</details>

<details>
<summary>Transaction Logs</summary>

## Why Collect Transaction Logs?

- Registry changes may be recorded in transaction logs before they are fully reflected in the hive file.
- Think of transaction logs as a **journal used to recover pending changes**.
- Collecting only the hive can leave out recent information.

## Log Locations

- Transaction logs are stored in the **same directory as their hive**.
- Common extensions:
  - `.LOG`
  - `.LOG1`
  - `.LOG2`
- Collect the hive **and its associated transaction logs**.

## Dirty and Clean Hives

| Hive state | Meaning |
|---|---|
| **Dirty hive** | May require transaction-log replay to recover changes not fully written to the hive. |
| **Clean hive** | Does not require that recovery step. |

- Comparing an unrecovered hive with a recovered copy may reveal differences.
- A dirty hive is not necessarily a complete or consistent historical snapshot.
- Keep the original evidence and create a separate recovered copy for analysis.

## RLA

- **RLA**, by Eric Zimmerman, is a command-line tool for replaying registry transaction logs.
- Combines a hive with its associated logs to produce a recovered hive.

| Option discussed in the course | Purpose |
|---|---|
| `-f` | Specify an individual hive file. |
| `-d` | Specify a directory containing hives; search subdirectories recursively. |
| `-out` | Specify the output directory. |
| `-cn False` | Disable name compression for user hives. |

- Keep transaction logs in the **same directory as the corresponding hive**.
- The instructor uses `-cn False` to avoid issues encountered with some user hives.
- By default, RLA also copies already-clean hives into the output directory.

</details>

<details>
<summary>Investigation Scenario</summary>

## Globomantics Investigation

- You are a forensic investigator examining a potentially compromised **Windows 10 system**.
- The system was intended for the **accounting team**.

## Timeline

| Date | Event |
|---|---|
| **January 13, 2023** | A systems administrator configured the system and connected it to the internet. |
| **January 13, 2023** | Remote Desktop, or RDP, was enabled and exposed to the internet. |
| **February 13, 2023** | The administrator tried to reconnect and received a message that another user was signed in. |
| **February 13, 2023** | The Accounting user denied the administrator’s connection request. |
| **February 13, 2023** | Incident response was called, and forensic artifacts were collected. |

## Accounts on the System

| Account | Intended use | Administrator’s understanding |
|---|---|---|
| **Windowsadmin** | Configure the system. | The only account known to have been used. |
| **Administrator** | Local administrator account. | Had never been logged into. |
| **Accounting** | Account for the accounting group. | Had not yet been used by the organization. |

## Why This Was Suspicious

- Nobody from the organization was expected to be using the system.
- The **Accounting** account was apparently active and denied the connection request.

## Investigation Goal

- Examine the system for **signs of malicious activity**.
- This course focuses specifically on **registry evidence**.

</details>

<details>
<summary>Demo: Creating Clean Hives</summary>

## Preparing the Evidence

- The course files contain collected **registry hives and transaction logs**.
- Place the original files in the dirty-evidence directory.
- Use a separate output directory for the recovered hives.

## Recovery Procedure

1. Run **RLA**.
2. Use the directory option to select the base directory containing the hives.
3. Let RLA search the subdirectories for hives and associated logs.
4. Set the output directory to:

   `C:\evidence\clean`

5. Disable name compression with the course’s **`-cn False`** setting.
6. Review the recovered hives in the output directory.

## Demo Result

- Processing took a little over **one second**.
- The output directory contains recovered hive files.
- Transaction-log changes have been incorporated into those output hives.

</details>

<details>
<summary>Summary</summary>

## Key Study Points

- Understand **root keys, keys, subkeys, values, data, and hives**.
- Collect three main categories:
  - **System hives**
  - **User hives**
  - **Amcache**
- Use **last-write timestamps** to identify keys modified during a relevant period.
- Collect associated **transaction logs** to avoid missing recent changes.
- Use **RLA** to create recovered copies for analysis.

## Next Module

- Determine **what files were opened by the attacker** using registry artifacts.

</details>

