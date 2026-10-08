## Introduction

- **Module: Evidence of Access**
- Explore registry keys that tell us what files and folders were **accessed, opened, or existed** on a Windows system.
- Topics include:
  - User activity recorded in the registry.
  - Quickly extracting forensically valuable information.
  - Specific registry keys that provide evidence of access.

<details>
<summary>Evidence of Access</summary>

## What Is Evidence of Access?

- Evidence showing files and folders a user **accessed or opened**.
- These could include:
  - Their own tools.
  - Documents already on the system.
  - Downloaded programs.
- Helps build a **timeline of what the attacker did**.
- Even if an attacker deletes a file or directory, evidence that it existed may remain.

## Access Is Different from Execution

- Evidence that a file was accessed or existed is **not necessarily evidence that a program executed**.
- Different artifacts are needed to establish execution.

## User Activity Recorded in the Registry

| Activity | Forensic value |
|---|---|
| **Files opened through applications or Explorer** | May appear in Most Recently Used, or MRU, records. |
| **Directories viewed in Explorer** | Identify local, removable-media, and network folders accessed. |
| **Connected devices** | May help identify USB devices, serial numbers, and connection activity. |
| **Typed paths and search terms** | Reveal locations or information the user was looking for. |
| **Application-specific activity** | Help reconstruct actions within a particular program. |

- This module examines a few valuable keys, not every possible source of user activity.

</details>

<details>
<summary>RegRipper</summary>

## Purpose

- **RegRipper**, by Harlan Carvey, extracts specific, forensically valuable registry information.
- Uses plugins to retrieve relevant **keys, values, and data**.
- Decodes supported encoded data automatically.
- In the demonstrated workflow, identifies the hive and applies the appropriate plugins.

## Limitations

- Does **not extract every possible artifact**.
- Valuable information may still require manual examination.
- Does **not turn a dirty hive into a clean hive**.
- Can report that a hive is dirty and still extract its data.
- Prepare recovered hives with their transaction logs before analysis.

## Extensibility

- RegRipper is **open source** and **plugin-based**.
- Additional plugins can extend its functionality.

</details>

<details>
<summary>Demo: Extracting Registry Artifacts</summary>

## Parsing the Hives

1. Use the previously prepared **clean hives**.
2. Open RegRipper.
3. Select a hive, starting with **SAM**.
4. Specify an output file, such as `output\sam.txt`.
5. Click **Rip**.
6. Repeat for the remaining hives.

## Processing Time

- Larger hives, such as **SOFTWARE**, may take longer.
- RegRipper may appear unresponsive while plugins run.
- Allow processing to finish.

## Output Files

| File | Contents |
|---|---|
| **Log file** | Information about the plugins that ran. |
| **Text report** | Extracted results from the plugins. |

- Search the text reports for the artifacts being investigated.

</details>

<details>
<summary>Demo: RecentDocs Key Analysis</summary>

## Location and Purpose

- **Hive:** User’s `NTUSER.DAT`
- **Key:**

  `Software\Microsoft\Windows\CurrentVersion\Explorer\RecentDocs`

- Tracks files and folders opened through **Windows Explorer**.
- Files opened only from the command line are less likely to appear.
- Uses **Most Recently Used (MRU)** ordering.

## RecentDocs Sections

| Section | Capacity described in the course |
|---|---|
| **Main RecentDocs key** | Last **150 files and folders**. |
| **Extension subkeys**, such as `.txt` and `.pdf` | Last **20 files** for each extension. |
| **Folder subkey** | Last **30 folders**. |

- Numeric value names are identifiers, not the opening order themselves.
- The MRU information determines the order.

## Timestamp Limitations

- RecentDocs does not keep a separate opening timestamp for every entry.
- Key last-write timestamps can help narrow the timing of activity.
- Extension subkeys may provide more specific timing clues than the main key.
- Correlate with other evidence; file system timestamps do not automatically equal the time a user opened a file.

## Accounting User Findings

- Search the Accounting user’s RegRipper report for **`recentdocs`**.
- The main key contains about **11 entries**.

| Artifact | Why it matters |
|---|---|
| `Exodus_Wallet.pdf` | A document worth locating and examining. |
| `Wallet.txt` | Another wallet-related file. |
| `threat` | A directory requiring investigation. |
| `start.vbs` | A script that could be relevant to malicious activity. |

- Scripts and executables should be located and examined; their presence alone does not establish malware.

## Timing Clues

| Artifact or key | Recorded last-write time | Interpretation |
|---|---|---|
| **Main RecentDocs** | February 11, 2023, at 18:45 | Latest recorded modification of the main key. |
| **`.pdf` subkey** | February 7, 2023, at 02:30:57 UTC | Contains only `Exodus_Wallet.pdf`, providing a stronger timing clue for that entry. |
| **`.vbs` subkey** | January 16, 2023, at 19:37:16 | Contains only `start.vbs`, providing a timing clue for that entry. |

- These are **registry key timestamps**, not independent timestamps stored on each file entry.

## Recorded Folders

- `The Internet`
- `Startup`
- `wal`

</details>

<details>
<summary>Demo: ShellBags Key Analysis</summary>

## Location

- **Hive:** User’s `UsrClass.dat`
- Relevant keys:

  `Local Settings\Software\Microsoft\Windows\Shell\Bags`

  `Local Settings\Software\Microsoft\Windows\Shell\BagMRU`

## What ShellBags Reveal

- Evidence of folders interacted with through **Windows Explorer**.
- Can include:
  - Local folders.
  - Folders on removable media.
  - Network folders.
- Artifacts may remain even after a folder has been deleted.

## RegRipper Output

| Column | Information |
|---|---|
| **MRU Time** | Registry-derived timing associated with the folder entry. |
| **Modified** | Recorded file system modification timestamp. |
| **Accessed** | Recorded file system access timestamp. |
| **Created** | Recorded file system creation timestamp. |
| **Resource** | Folder or shell location represented by the entry. |

- Times in the demonstration are **UTC**.
- Stored file system timestamps reflect recorded metadata and may not represent the folder’s current state.
- Interpret MRU and key timestamps carefully; they are not a complete history of every folder visit.

## Accounting User Findings

| Folder or resource | Time shown in the demonstration |
|---|---|
| `My Computer\<GUID>\wal` | January 16, 2023, at 19:37:13 UTC |
| `My Computer\<GUID>\bug` | January 20, 2023, at 18:37:19 UTC |
| `Startup` | January 22, 2023, at 23:40:55 UTC |

- Some locations contain **GUIDs** that require further interpretation.
- The `wal` and `bug` directories deserve investigation.

## Why Startup Matters

- The Windows Start menu’s **Startup folder** can be used for persistence.
- Programs placed there may start when the user logs on.
- Access to the folder is a clue; it does not by itself prove that persistence was configured.

</details>

<details>
<summary>Demo: ShimCache Analysis</summary>

## Purpose of ShimCache

- **ShimCache**, also called **AppCompatCache**, is stored in the **SYSTEM hive**.
- Supports Windows application compatibility, including legacy programs.
- Can contain evidence that executables **exist or previously existed** on the system.

## Presence Does Not Prove Execution

- How entries are populated depends on the Windows version.
- An executable may be recorded after its directory is viewed in Windows Explorer.
- **On Windows 10, an executable’s presence in ShimCache does not prove it executed.**
- Use other artifacts to establish execution.

## Memory and Disk

- Recent ShimCache information may remain in **memory** before being written to the SYSTEM hive.
- The course describes shutdown as a point when the cache is written to disk.
- A hive collected from a running system may not contain the latest entries.
- Memory forensics may recover additional cache information.

## Information Available

- Full executable path.
- File last-modified timestamp.
- Other metadata, such as size or flags, depending on the Windows version.
- **The file’s last-modified time is not its execution or download time.**

## AppCompatCacheParser

- The RegRipper output is difficult to review in this demonstration.
- Use **AppCompatCacheParser**, by Eric Zimmerman, to export ShimCache as CSV.
- Specify:
  - The clean **SYSTEM hive**.
  - An output directory.
  - An output filename, such as **`shimcache.csv`**.
- The demonstration extracts **641 entries**.

## Timeline Explorer

- **Timeline Explorer**, also by Eric Zimmerman, provides an easy way to review CSV files.
- Open the exported CSV.
- Sort by **last-modified time**.
- Examine suspicious names and locations.

## Findings

| Artifact | Observation |
|---|---|
| `SocksEscort64.exe` | Appears multiple times in Downloads-related entries. |
| Exodus-related files | May relate to the previously identified `Exodus_Wallet.pdf`. |
| `Update.exe` | Requires further investigation. |
| `netlibs.exe` | Found under `AppData\Local\Temp`. |
| `wins.exe` | Found under `AppData\Local\Temp`. |

- Entries around January 17 and January 27 become interesting when sorted by file modification time.
- Multiple entries may suggest repeated copies or downloads, but do not establish that by themselves.
- Executables in **`AppData\Local\Temp`** deserve attention during an attacker’s activity period.
- None of these ShimCache entries alone confirms execution.

</details>

<details>
<summary>Summary</summary>

## Three Main Artifacts

| Artifact | Main forensic value |
|---|---|
| **RecentDocs** | Files and folders recorded as recently opened by a user. |
| **ShellBags** | Folder interactions, including local, removable-media, and network locations. |
| **ShimCache** | Evidence of executables present or previously present on the system. |

## Key Points

- **Evidence of access is different from evidence of execution.**
- Deleted files and folders may leave registry artifacts.
- Use MRU ordering and timestamps carefully when building a timeline.
- RegRipper extracts valuable information, but does not replace all manual analysis.
- These artifacts represent only part of the user activity available in the registry.

## Next Module

- Examine registry artifacts that provide evidence of **program execution**.

</details>