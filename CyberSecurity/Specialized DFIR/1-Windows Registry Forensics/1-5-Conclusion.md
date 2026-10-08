## Introduction

- Review the investigation and what the registry revealed about the compromise.
- Three types of artifacts were examined:

| Artifact type | Investigative value |
|---|---|
| **File access or existence** | Clues about what files were opened and what information was accessed. |
| **Program execution** | Understand the attacker’s actions and potential risk to the organization. |
| **Persistence** | Identify programs configured to restart automatically. |

<details>
<summary>Investigation Recap</summary>

## RecentDocs: start.vbs

- The Accounting user’s **RecentDocs** key recorded `start.vbs`.
- This was a **Visual Basic Script** that appeared suspicious.
- Registry examination alone did not reveal its contents.

## ShellBags: Startup Directory

- The Accounting user’s **UsrClass.dat** contained ShellBags evidence of access to the **Startup directory**.
- Programs or shortcuts placed in this directory can run when the user logs on.
- The registry did not directly connect `start.vbs` to that directory.

## Correlating the Timestamps

| Artifact | Timestamp highlighted in the course |
|---|---|
| **start.vbs access-related record** | January 16, 2023, at **19:37:16 UTC** |
| **Startup directory’s recorded last-modified time** | January 16, 2023, at **19:38:10 UTC** |

- The timestamps are **54 seconds apart**.
- This suggests `start.vbs` may have been placed in the Startup directory.
- It is **not concrete proof**.
- File system analysis would be needed to investigate the connection.

## ShimCache: Executables Present

- ShimCache contained entries for:
  - **SocksEscort64**
  - A **Firefox installer**
  - **Exodus-related update executables**
- On Windows 10, **ShimCache presence does not prove execution**.

## Possible Timeline

| Date | Findings discussed |
|---|---|
| **January 16, 2023** | `start.vbs` activity and a closely timed Startup directory modification. |
| **January 17, 2023** | Firefox installer and SocksEscort entries. |
| **January 27, 2023** | Exodus-related entries. |

- These findings help develop an investigative timeline.
- File modification timestamps do **not independently establish when a file was downloaded or executed**.

## AmCache Findings

- AmCache contained records for:
  - Exodus-related programs.
  - `ntlhost.exe.exe`, also referred to as `ntlhost.exe` in the recap.
  - `system.exe`.
- The timestamps discussed were **file last-modified times**, not execution times.
- The SHA-1 recorded for `system.exe` led to a VirusTotal result identifying it as malicious.

## AmCache Interpretation

- The transcript treats AmCache records as proof of execution.
- **AmCache presence alone does not establish execution.**
- Correlate its program metadata with other execution artifacts.

## UserAssist: Remote Desktop Activity

- The Accounting user’s **NTUSER.DAT** contained UserAssist records for four files executed from the **BUG directory**.
- The paths used:

  `\\tsclient\...`

- These paths indicate **Remote Desktop drive redirection**.
- The findings support:
  - Use of an RDP session.
  - Access to storage shared from the connecting computer.
  - Execution of programs from that shared storage.
- This establishes RDP use during the activity, but does not by itself establish the initial entry method.

## Run Key: Persistence

- The Accounting user’s Run key contained an entry for **`ntlhost.exe.exe`**.
- It was configured to start when the **Accounting user logged on**.

## Last-Write Timestamp Limitation

- The Run key’s last-write timestamp was:

  **February 11, 2023, at 18:45:03 UTC**

- The key contained **five values**.
- The timestamp belongs to the **key**, not the individual `ntlhost.exe.exe` entry.
- We cannot determine which value was last changed from that timestamp alone.
- The entry establishes configured persistence, but not its exact installation time.

</details>

<details>
<summary>Summary</summary>

## What Registry Analysis Revealed

- Files accessed or previously present.
- Evidence relating to program execution.
- Programs configured for persistence.
- Connections between artifacts that help explain attacker activity.

## The Registry Is Only Part of the Story

- Examine additional evidence, including:
  - **File system artifacts**
  - **Event logs**
- Correlate findings to strengthen conclusions and resolve uncertainties.

## Further Analysis Opportunities

| Area | What to examine |
|---|---|
| **Other registry locations** | Evidence not covered in the demonstrations. |
| **Original dirty hives and recovered copies** | Differences that may reveal additional information. |
| **Other RegRipper output** | Research the forensic value of keys not yet reviewed. |
| **Last-write timestamps** | Identify keys modified around known compromise times. |
| **RECmd searches** | Extract and investigate related keys and values. |

- Dirty hives may retain older information, but are not necessarily complete or consistent historical snapshots.
- Preserve the original evidence when comparing hive versions.

## Practice

- Use the demo files to perform additional registry analysis.
- Investigate unexplained artifacts and correlate them with other evidence.
- Document new findings and distinguish **observations, inferences, and confirmed conclusions**.

</details>