## Introduction

- **Module: Execution Analysis**
- Examine registry artifacts that help identify **which programs were executed**.
- Understand why evidence of execution matters.
- Analyze the compromised system to investigate what the attacker ran.

<details>
<summary>Evidence of Execution</summary>

## What Is Evidence of Execution?

- **Proof that a program ran on the system.**
- Finding a program on disk does not automatically mean it was executed.
- Execution artifacts may identify the program and when it ran.

## Why It Matters

- Correlate execution evidence with the time the attacker was on the system.
- Build a **timeline of activities**.
- Infer the attacker’s behavior and goals.
- Identify their **tactics, techniques, and procedures (TTPs)**.
- Use these findings to search for similar activity on other systems.

</details>

<details>
<summary>Demo: AmCache Analysis</summary>

## Location and Purpose

- **Hive:** `Amcache.hve`
- **Location:**

  `C:\Windows\AppCompat\Programs\Amcache.hve`

- Stores application inventory and compatibility information that can be useful in forensic investigations.
- Historical entries may remain after a program has been deleted.

## Important Interpretation

- The transcript treats AmCache entries as definitive proof of execution.
- **AmCache presence alone does not prove execution**; interpretation depends on the Windows version and how the entry was populated.
- Correlate findings with other execution artifacts.
- Do not automatically interpret every AmCache timestamp as an execution time.

## Information Available

| Information | Forensic value |
|---|---|
| **Program name and path** | Identify the recorded executable and its location. |
| **File size** | Compare with other copies or samples. |
| **Last-modified time** | Provide file metadata for timeline analysis. |
| **SHA-1-related hash** | Help identify the file through external analysis sources. |

- AmCache hash calculation can differ from a full-file SHA-1, particularly for large files.
- Confirm the hash format before interpreting a lookup result.

## Findings in the Demonstration

| Program | Timestamp highlighted in the transcript, UTC | Location or context |
|---|---|---|
| `Exodus.exe` | February 7, 2023, at 06:26:15–16 | Previously identified in ShimCache. |
| `ntlhost.exe.exe` | February 9, 2023, at 04:14:05 | Under `AppData\Roaming\ntsystem`. |
| `system.exe` | January 18, 2023, at 01:05:24 | Accounting user’s Documents directory. |

- These are useful leads within the investigation period.
- A file in a user’s directory does **not establish which user executed it**.
- Use additional evidence to validate execution and timing.

## VirusTotal Result

- The instructor searches the recorded hash for `system.exe`.
- In the demonstration:
  - Many antivirus vendors detect the file.
  - It had been uploaded under the name **`VBCECompiler.exe`**.
- This supports investigating the file as potentially malicious.
- A different filename does not necessarily mean different file contents.

</details>

<details>
<summary>Demo: UserAssist Analysis</summary>

## Purpose and Location

- **UserAssist** records GUI-related program and shortcut activity.
- Stored in the user’s **`NTUSER.DAT`** hive.
- Useful for connecting activity to a particular user profile.

## Important Subkeys

| GUID prefix | Information |
|---|---|
| **`CEBFF5CD`** | Program execution records. |
| **`F4E57C4B`** | Shortcut activity records. |

## Information Recorded

- File name and path.
- Run count.
- Last execution time.

## ROT13 Encoding

- UserAssist value names are encoded using **ROT13**.
- They appear encoded when viewed directly in tools such as Registry Editor.
- **RegRipper automatically decodes them**.

## Accounting User Findings

| Program | Recorded time, UTC | Location |
|---|---|---|
| `sysinfo.exe` | February 8, 2023, at 18:05:01 | Desktop |
| `Everything.exe` | February 8, 2023, at 18:15:23 | Accounting user’s Pictures directory |
| Several executables | January 20, 2023, at 18:39:48–55 | `\\tsclient\b\BUG\` |
| `csrss.exe`, `System.exe`, and `c.exe` | January 16, 2023 | Includes `csrss.exe` in the Music directory |

- Some programs were not identified in the other artifacts examined.
- Combining artifacts helps build a more complete account of activity.

## What Does tsclient Mean?

- **`\\tsclient`** provides access to client drives redirected through a Remote Desktop session.
- When someone connects through **RDP**, they can share drives from their computer with the remote system.
- Programs accessed through these paths can therefore originate from the connecting computer.

## Connecting UserAssist and ShellBags

- Earlier ShellBags findings showed unusual paths ending in:
  - `wal`
  - `BUG`
- UserAssist now shows executions from:

  `\\tsclient\b\BUG\`

- Together, these findings support:
  - Use of an **RDP session**.
  - Access to redirected client storage.
  - Program execution from that storage.
- This supports RDP use during the incident, but does not alone prove it was the initial entry method.

## Limits of the tsclient Evidence

- The records provide filenames and execution timing.
- They do not provide the contents or hashes of those remote files.
- Further evidence is needed to determine exactly what each executable did.

## Suspicious Program Locations

- `csrss.exe` appears in the **Music directory**.
- This is inconsistent with the normal location of the legitimate Windows component.
- Familiar Windows filenames in unusual directories deserve investigation.
- A filename alone does not establish a file’s identity.

</details>

<details>
<summary>Summary</summary>

## Main Artifacts

| Artifact | Main value | Key limitation |
|---|---|---|
| **AmCache** | Program metadata, paths, and hashes that help identify files. | Presence alone is not definitive proof of execution. |
| **UserAssist** | GUI-related execution records, run counts, and last execution times. | Does not cover every way a program can execute. |

## Investigation Findings

- Identified additional suspicious programs and locations.
- Connected `\\tsclient` paths with **RDP drive redirection**.
- Correlated UserAssist with earlier ShellBags findings.
- Used a recorded hash to locate an existing VirusTotal result.

## Key Study Points

- **File presence and program execution are different claims.**
- Combine multiple artifacts to establish activity and timing.
- A file path alone does not prove who executed it.
- Other registry artifacts may reveal additional programs.

## Next Module

- Examine registry evidence of **backdoors and persistence** on the compromised system.

</details>