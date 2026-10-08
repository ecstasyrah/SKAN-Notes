## Introduction

- In this module, we're going to learn:
  - What exactly is a **timeline**, and why do we use it forensically?
  - How can we **create a timeline from an NTFS file system**?
  - How to generate a timeline of a **compromised Windows system** and perform forensic analysis.

<details>
<summary>NTFS Analysis Tools</summary>

## What Is a Timeline?

- A timeline is a **listing of events typically ordered by time**.
- In an NTFS forensic analysis, the timeline typically comes from the **various timestamps within the MFT**.
- A timeline is used to look at what files were **created, modified or removed** during periods of suspicious or malicious activity.
- This allows us to **infer what attackers were doing** and helps craft the story of what happened on the system.

## Tools

| Tool | Purpose |
|---|---|
| **MFTECmd** | Command-line tool that reads an MFT and outputs it to CSV, JSON or BODY format. |
| **MFTExplorer** | GUI tool that directly loads the MFT and provides a file system tree view. |
| **Timeline Explorer** | Loads timelines and CSVs so we can quickly sort and examine the files. |

- **MFTECmd and MFTExplorer** extract timestamps from standard information and file name attributes, giving us **eight timestamps per file** to examine.
- **MFTExplorer** also allows us to view data contained in **resident files**.

</details>

<details>
<summary>Investigation Scenario</summary>

## System Background

- You are a forensic investigator for **Globomantics**.
- On **January 13th**, a systems administrator was setting up a **Windows 10 system for the accounting team**.
- **Remote Desktop, or RDP**, was turned on and allowed connections from the internet.

| Account | Purpose |
|---|---|
| **windowsadmin** | Used to configure the system. |
| **administrator** | A local administrator account. |
| **accounting** | A user account for the accounting group. |

- To the administrator's knowledge, **only the windowsadmin account was ever used**.
- The administrator later noticed **unusual behavior** and called the incident response team.
- On **February 13th**, the team obtained forensic artifacts.

## Investigation Goal

1. Examine the system for **signs of malicious activity**.
2. Create a **timeline from the MFT**.
3. Search through it for **files of interest**.
4. Interpret the data found in the timeline.

</details>

<details>
<summary>Demo: MFT Analysis</summary>

## Process the MFT

- Use **MFTECmd** to process the MFT into readable output.
- Quote the file and directory paths.

| Option discussed in the demo | Purpose |
|---|---|
| **-f** | Points directly to the MFT file. |
| **-csv** | Points to the directory where the output will go. |
| **-csvf** | Gives the name of the CSV output file. |
| **-at** | Optional; makes sure all timestamps show up. |

- Open the resulting CSV file in **Timeline Explorer**.

## Identify Timestamp Columns

| Column identifier | Attribute |
|---|---|
| **0x10**, such as Created0x10 | Standard information — **SI** |
| **0x30**, such as Created0x30 | File name — **FN** |

## Narrow the Timeline

| Filter | Remaining entries |
|---|---:|
| All MFT entries | Approximately **164,000** |
| Timestamps after **January 13th, 2023** | Almost **40,000** |
| Executables created after January 13th | Around **250** |

- Filtering helps focus on activity **after the system was installed**.
- On **January 15th**, executables appeared within the **accounting user's directory**.
- The accounting user was **never utilized legitimately**, so this may point to attacker activity.

## Firefox Installer

- On **January 17th at 22:07:35**, the Firefox installer was created in:
  - `\Users\accounting\Downloads`
- **We don't know if it was run** from the installer entry alone.
- Firefox executables created afterward suggest that it was installed.
- SI and FN creation timestamps were the same.
- Other timestamps showed small differences:
  - Approximately **one second** for last modified.
  - Approximately **one minute** for last record change.
- There are multiple reasons these differences could occur.
- **Next investigation step:** Examine Firefox history to see whether somebody used it to access the internet.

## SocksEscort64.exe

- Multiple files named **SocksEscort64.exe**, followed by numbered copies, appeared in:
  - `\Users\accounting\Downloads`
- Investigate what the executable was and why it was there using:
  - **Registry artifacts**
  - **Event logs**
  - **Browser history**
  - **Malware analysis**, if the file can be recovered

## Accounting User Activity

- Remove the executable filter.
- Filter for activity within the **accounting user's directory**.
- On **January 16th at 19:37:02**, a directory called **wal** was created in:
  - `\Users\accounting\Music`
- Two files appeared within it:
  - **Exodus_Wallet.pdf**
  - **Wallet.txt**
- Copies of these files appeared in multiple locations.

## Recent Link Files

- The user's Recent directory contained:
  - **wal.lnk**
  - **start.lnk**
- These provide clues about items that may have been accessed.
- Analyze the link files to determine **where they point**.

## Examine Resident File Contents

- **Wallet.txt was 247 bytes**, small enough that it could potentially be a resident file.
- If resident, its contents may be available **within the MFT**.

1. Open the MFT in **MFTExplorer**.
2. Navigate to the **wal directory under Music**.
3. Examine **Wallet.txt**.
4. The first copy showed **Non Resident Data**.
5. Examine another copy one directory above.
6. This copy contained **resident data**.

- MFTExplorer displayed the contents as **hexadecimal and ASCII**.
- The readable contents included:
  - **Wallet Backup**
  - **Mnemonic phrase**
  - **A private key**
- Further research is needed to determine what the file represents.
- Its contents alone do not establish whether a cryptocurrency miner was present.

</details>

<details>
<summary>Conclusion</summary>

## Investigation Findings

| Finding | What it contributes to the investigation |
|---|---|
| **Accounting account activity** | Potential attacker use of an account that nobody was supposed to be using. |
| **Firefox installation** | Browser history can provide information about internet activity. |
| **Unexpected executables** | Files in temporary and user directories require additional analysis. |
| **Resident Wallet.txt copies** | File contents could be examined directly from the MFT. |

## Continue the Analysis

- Examine other files that were **modified, created or accessed** during the period of interest.
- Look through the evidence:
  - **MFT**
  - **LogFile**
  - **USN Journal**
- Creating a timeline helps **tell the story of what happened** and identify where to investigate next.
- **Next module:** Browser forensics.

</details>