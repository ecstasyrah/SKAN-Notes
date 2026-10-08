## Introduction

- **Windows File System and Browser Forensics:** The file system and internet browser are two of the primary sources of forensic information.
- **Windows NTFS Analysis** will cover:
  - What is the NTFS file system and why is it important?
  - What useful metadata does NTFS contain for a forensic investigation?
  - How can we analyze NTFS file systems?

<details>
<summary>NTFS File System</summary>

## Purpose and Metadata

- **File system analysis** provides a lot of useful information during an investigation.
- **NTFS** stores information or metadata on the files and directories found within an NTFS volume.
- Examining this information allows investigators to **infer what happened in the file system** during periods of interest.

| Useful metadata | Information |
|---|---|
| **File and directory names** | Names of files and directories. |
| **Multiple timestamps** | Timestamps kept for every file and directory. |
| **Security information** | The owner of the file and its permissions. |
| **File size** | The size of the file. |
| **Disk blocks** | Where the file contents are located. |
| **File contents** | Potentially stored within the NTFS metadata itself. |

## Resident Files

- If a file is **less than around 700 bytes**, the file contents may be stored within the NTFS metadata.
- A file stored in this way is called a **resident file**.

## Master File Table — MFT

- The most important NTFS metadata artifact is the **master file table, or MFT**.
- The MFT contains the majority of the data examined in this and the next module.
- When mounting an NTFS drive with forensic software, the MFT appears as **`$MFT` in the root of the drive**.

## LogFile and USN Journal

- NTFS is a **journaling file system**: it keeps track of changes made to files and directories to support recovery after a file system crash.

| Artifact | Forensic information |
|---|---|
| **LogFile** | Information such as the names of files that were created, modified or deleted within the volume. |
| **USN Journal** | Similar change information, with timestamps. |

- These artifacts help determine **what files once existed and what may have been done to them**.
- Both have **limited space**.
- The sooner you get to them, the more likely they'll have relevant information.

</details>

<details>
<summary>NTFS Timestamps</summary>

## Two Sets of Timestamps

- File and directory timestamps are extremely important when generating **forensic timelines**.
- The MFT stores two sets of timestamps:

| Structure | Characteristics |
|---|---|
| **`$STANDARD_INFORMATION` — SI** | Timestamps typically seen within Windows Explorer; changed more often and modifiable by the user. |
| **`$FILE_NAME` — FN** | Contains the same types of timestamps; modified much less and not easily modified by the user. |

## MACB Timestamps

| Timestamp | Meaning | Generally records |
|---|---|---|
| **M** | Modification | When the file was last modified. |
| **A** | Access | When the file was last accessed. |
| **C** | Change | When the MFT entry or metadata of the file was last changed. |
| **B** | Born | When the file was created or born. |

## Timestamp Updates Discussed in the Course

- Each timestamp can be updated during different file system operations.
- **When timestamps are updated can change depending on the version of Windows and the configuration of the system.**
- Treat the following as the course's descriptions, rather than rules guaranteed for every system.

| Timestamp | Operations described |
|---|---|
| **Modification — M** | Both SI and FN when a file is created or modified; FN when copied to another location within the same volume or moved to a different volume. |
| **Access — A** | Both SI and FN when a file is created, modified, copied or moved between volumes; only SI when the file is opened. |
| **Change — C** | Both SI and FN when a file is created; only SI when modified, moved locally or renamed; FN when copied or moved between volumes. |
| **Born — B** | Both SI and FN when a file is created, copied or moved between volumes. |

## Interpretation

- **Access timestamp updates may be disabled**, depending on the system configuration.
- If disabled, the A time may only be set when the file is first created.
- Files typically have **multiple operations performed on them**.
- Understanding the different ways timestamps can change helps you figure out what happened when generating an analysis timeline.

</details>

<details>
<summary>File System Analysis Tips</summary>

## Create a Timeline

- Create a timeline of **all the files and directories from the MFT**.
- Focus on periods in which you **know or suspect malicious activity occurred**.
- Look for files that may have been:
  - **Modified**
  - **Created**
  - **Deleted**
- Infer what those files are based on **file name, size or other artifacts on the system**.

## Look for Suspicious Files and Locations

- Attackers commonly download files into a user's **Downloads directory**.
- Look for suspicious executables in:
  - **Temporary directories**
  - **Windows**
  - **Windows\System32**

## Examine Both SI and FN Timestamps

- Examine both the **standard information and filename timestamps**.
- FN timestamps are not modified as often and may provide useful information about when a file was created.
- A **large discrepancy between SI and FN timestamps** could indicate timestamp modification by the attacker.

## Examine LogFile and USN Journal

- Examine these files for information on:
  - **What files existed on the system**
  - **What was done to them**
- They contain a **limited amount of data**, so grab them as soon as you can.
- These files are included in the course evidence files, although they are not examined in the course.

</details>

<details>
<summary>Summary</summary>

- **MFT:** The most important metadata file in an NTFS forensic investigation.
- **MACB:** Modification, Access, Change and Born timestamps.
- **Timeline:** Focus on specific periods of interest.
- **SI and FN:** Examine both to get a more complete view of what happened.
- **LogFile and USN Journal:** Get additional information on actions that took place in the file system.
- **Next module:** Generate a timeline for an NTFS file system and perform analysis.

</details>