## Introduction

- Review the **analysis performed** and the **artifacts examined**.
- Examine the **timeline created through the analysis** and review what it tells us.

<details>
<summary>File System Analysis Summary</summary>

## Two Sets of Forensic Artifacts

| Artifact source | Information obtained |
|---|---|
| **NTFS file system** | Activity within the file system, such as which files were created or modified. |
| **Internet browsers** | Websites and files accessed through a browser, and files downloaded from the internet. |

## NTFS Metadata Files

| Artifact | Forensic value |
|---|---|
| **Master File Table — MFT** | Contains file names, timestamps and most information examined during a file system investigation. |
| **LogFile** | Tracks file system changes; can show what files existed and what was done to them. |
| **USN Journal** | Tracks changes and includes timestamps useful for creating timelines. |

- **LogFile and USN Journal have limited size**, so their records may only go back a few hours or days.

</details>

<details>
<summary>File System Analysis Timeline</summary>

## Findings

| Date and time — UTC | Artifact | Finding |
|---|---|---|
| **January 15, 2023, 14:40:42** | `winuser.exe` | Created on the system; requires recovery and closer examination to determine what it is. |
| **January 17, 2023, 19:37:02–09** | `wal`, `Exodus_wallet.pdf` and `wallet.txt` | The recap places their creation within the accounting user's Music directory at this time. |
| **January 17, 2023, 22:07:35** | Firefox installer | Created in `\Users\accounting\Downloads`; Firefox directories and executables appeared shortly afterward. |
| **January 17–18, 2023** | `SocksEscort64` | Several copies appeared in `\Users\accounting\Downloads`. |

- **Date discrepancy:** The earlier MFT demo placed the `wal` directory's creation on **January 16 at 19:37:02**, while this recap says January 17. Verify the original evidence.

## Interpretation

- Activity within the **accounting user's directories** indicates that someone was using an account that had not been legitimately used.
- Firefox program directories and executables appearing after the installer allow us to **infer that the installer was run**.
- A resident copy of **Wallet.txt** could be viewed within the MFT.
- Its contents suggested a cryptocurrency connection; **a crypto miner was not established by the file alone**.
- File system analysis alone did not establish **what SocksEscort64 was or where it came from**.
- Examine the MFT further to identify additional activity.

</details>

<details>
<summary>Browser Analysis Summary</summary>

## Browser Artifacts

- Browsers perform activities beyond simply accessing the internet.
- Artifacts examined include:

| Artifact | Information |
|---|---|
| **Browsing history** | Sites and resources accessed. |
| **Browser cache** | Stored content from browser activity. |
| **Downloads** | Files downloaded through the browser. |
| **Bookmarks** | Sites saved by the user. |
| **Website cookies** | Information associated with website activity. |
| **Saved logins** | Stored account and login information. |

- Together, these artifacts help show **what someone was doing within the browser**.

</details>

<details>
<summary>Browser Analysis Timeline</summary>

## Findings

| Date | Activity | Investigation value |
|---|---|---|
| **February 7th** | `Wallet.txt` appeared in Internet Explorer history. | Supports access to a file found throughout the system. |
| **Over several days** | Access to sites within `regions.com`. | Shows banking-site activity across at least three browsers. |
| **February 8th** | Access to `2ip.me`. | A site used to determine an IP address and apparent location. |
| **February 11th** | Opera downloaded through Google Chrome. | Identifies another browser to investigate. |
| **Shortly afterward** | Opera directory appeared in Internet Explorer history. | Suggests Opera was installed and may have been used. |

## Interpretation

- Banking activity raised the possibility of **fraud**, but the purpose of the visits was not confirmed.
- It was **unknown why multiple browsers were used**.
- Examining all browsers helped create a **more complete timeline**.
- IP-checking sites can be used by attackers, but **access alone does not prove malicious activity**.
- Combine browser history with other forensic artifacts to better understand the activity.

</details>

<details>
<summary>Combined Timeline</summary>

## File System and Browser Correlation

| Date and time — UTC | Evidence source | Activity |
|---|---|---|
| **January 17, 2023, 22:07:35** | File system | `Firefox Installer.exe` appeared in `\Users\accounting\Downloads`. |
| **January 17, 2023, 22:09:22** | Firefox history | First recorded Firefox browser activity. |
| **February 7th, 16:29:08** | Firefox history | Next recorded activity: access to `regions.com`. |

- The first Firefox activity appeared **1 minute and 47 seconds** after the installer.
- This supports the conclusion that Firefox was **installed and executed**.

## Unanswered Questions

- What happened between **January 17th and February 7th**?
- Did the attacker access anything on January 17th?
- If so, why is there no corresponding history?
- Is it possible they were using **private browsing mode**?

## Why Combine Timelines?

- Combining file system and browser artifacts provides a **bigger picture of attacker activity**.
- Gaps and unanswered questions help identify **which artifacts to examine next**.

</details>

<details>
<summary>Conclusion</summary>

- **Timestamps** are among the most powerful pieces of file system evidence.
- Examine both sets of NTFS timestamps:
  - **Standard Information — SI**
  - **File Name — FN**
- Review all **eight timestamps discussed in the course**.
- **Don't limit browser analysis to history**; other artifacts provide additional information.
- **Combine timelines from all examined artifacts** for a clearer picture of system activity.
- Practice with the supplied evidence to find activity not covered in the demonstrations.
- **Related course:** Specialized DFIR: Windows Registry Analysis examines the same compromised system through registry forensics.

</details>