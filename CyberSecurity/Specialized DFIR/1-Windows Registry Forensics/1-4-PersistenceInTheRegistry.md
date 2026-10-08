## Introduction

- **Module: Persistence in the Registry**
- Examine how attackers maintain access to a system through the registry.
- Topics include:
  - What persistence is.
  - How attackers use it.
  - Registry locations that may contain indicators of malicious persistence.

<details>
<summary>Persistence in the Registry</summary>

## What Is Persistence?

- After gaining a foothold, attackers may install:
  - Backdoors to maintain access.
  - Malware to perform activities such as cryptocurrency mining.
- Systems reboot and users log off.
- Attackers need their tools and malware to **start again**.
- This is known as **maintaining persistence**.

## Auto-start Extension Points

- Registry locations used to start programs automatically are often called **auto-start extension points**, or **ASEPs**.
- This module focuses on three areas:

| Mechanism | Purpose |
|---|---|
| **Run keys** | Start programs automatically at user logon. |
| **Services** | Run programs in the background, potentially starting automatically. |
| **Scheduled tasks** | Run programs at specified times or when triggers occur. |

- Scheduled tasks can restart malware every few minutes, even after its process has been stopped.

</details>

<details>
<summary>Persistence Analysis Tools</summary>

## Microsoft Autoruns

- Examines hundreds of persistence locations in the **file system and registry**.
- Can be configured to:
  - Verify code-signing signatures.
  - Query VirusTotal using file hashes.
- Particularly useful for examining a **live system**.
- The course does not recommend it for this offline-hive workflow because setup and output can be limiting.

## RECmd

- **RECmd**, by Eric Zimmerman, is a command-line registry analysis tool.
- Can:
  - Extract registry keys and values.
  - Search using regular expressions.
  - Recover deleted keys, values, and data where recoverable.
  - Use batch files to extract many artifacts at once.
- Command-line operation allows analysis to be **scripted**.

## RegistryASEPs.reb

- Located in RECmd’s **`BatchExamples`** directory.
- Combines multiple ASEP extraction definitions.
- Used to extract persistence-related information from collected hives.

</details>

<details>
<summary>Demo: Run Keys Analysis</summary>

## Run Keys

- A common persistence location is:

  `Software\Microsoft\Windows\CurrentVersion\Run`

- Programs specified in Run values can start automatically when a user logs on.
- Examine both:
  - **Machine-wide entries** in the SOFTWARE hive.
  - **User-specific entries** in each user’s NTUSER.DAT hive.

## HKLM and User Run Keys

| Location | Scope |
|---|---|
| **HKLM Run key** | Applies at user logon across the system. |
| **HKCU Run key** | Applies when that specific user logs on. |

- **Correction to the transcript:** the standard HKLM Run key runs programs at logon, not simply at system boot.
- Other ASEPs have different startup conditions.

## Extracting Persistence Artifacts

- Use RECmd with:
  - The directory containing the **clean hives**.
  - An output directory, such as `Evidence\Persistence`.
  - A CSV output filename.
  - The full path to **`RegistryASEPs.reb`**.

| Option described in the course | Purpose |
|---|---|
| `-d` | Search a directory containing registry hives. |
| `-csv` | Specify the CSV output directory. |
| `-csvf` | Specify the output filename. |
| `-bn` | Specify the batch file to run. |

- Directory processing avoids specifying each hive individually.

## Demo Results

- Processing took slightly less than **five minutes**.
- RECmd extracted **78,399 keys and values**.
- These results are potential persistence-related artifacts, not 78,399 malicious entries.
- Open the CSV in **Timeline Explorer**.

## Important Columns

| Column | Meaning |
|---|---|
| **Last-write timestamp** | Last modification time of the registry key. |
| **Key path** | Full path to the key. |
| **Value name** | Name of the entry. |
| **Value data** | Program path, command, or other stored data. |
| **Hive tag/source hive** | Identifies the hive type and source file. |

- Filter for **Run keys** to reduce the results to roughly a dozen entries.
- Check the source hive to identify the associated user.

## Accounting User Findings

| Run value | Target | Finding |
|---|---|---|
| **VBCECompiler** | `System.exe` | Previously identified as malicious in the investigation. |
| **NTSystem** | `ntlhost.exe.exe` | Suspicious and requires further examination. |

- Both entries are associated with the **Accounting user’s NTUSER.DAT**.
- These establish a way for the programs to start automatically at logon.
- A configured auto-start entry does not by itself prove that it successfully ran.

</details>

<details>
<summary>Demo: Services Analysis</summary>

## Services as Persistence

- A Windows service is a program that works in the background.
- Legitimate examples include:
  - Windows Update.
  - IIS web servers.
  - Windows Defender.
- Malware and backdoors can also run as services.

## Registry Location

- On a running system:

  `HKLM\SYSTEM\CurrentControlSet\Services`

- Each service has a subkey named after it.
- In an offline SYSTEM hive, examine the relevant numbered control set, such as `ControlSet001`.

## Important Service Values

| Value or subkey | Purpose |
|---|---|
| **DisplayName** | Human-readable service name. |
| **Start** | Startup configuration, including whether the service is disabled. |
| **ImagePath** | Path and command line used to launch the service. |
| **Parameters** | Additional settings, potentially including the service DLL. |

- Focus on **ImagePath** to identify the program being launched.

## svchost.exe

- **`svchost.exe`** hosts Windows services implemented in DLLs.
- Multiple svchost processes can be normal.
- Legitimate copies are normally found in:
  - `C:\Windows\System32`
  - `C:\Windows\SysWOW64`
- Malware may use the same filename to blend in.
- The filename alone does not establish legitimacy.

## svchost Groups

- Group definitions are stored at:

  `HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Svchost`

- Each group lists the services it hosts.
- Examples discussed under `netsvcs` include:
  - `CertPropSvc`
  - `SCPolicySvc`
  - `lanmanserver`

## Finding the Actual Service DLL

1. Identify the service in the normal **Services** key.
2. Examine its **ImagePath**.
3. If it points to `svchost.exe -k <group>`, inspect the service’s **Parameters** subkey.
4. Locate **`ServiceDll`**.
5. Examine the DLL path.

- An attacker may configure a malicious DLL to load into a legitimate svchost process.
- A process listing alone may not reveal the malicious DLL.
- The course leaves examination of the scenario’s services as an exercise.

</details>

<details>
<summary>Demo: Scheduled Tasks Analysis</summary>

## Scheduled Tasks as Persistence

- Windows tasks run programs at specified times or in response to triggers.
- Attackers can use them to repeatedly execute malware or backdoors.
- Stopping a process may not stop a scheduled task from launching it again.

## Registry Location

- **Hive:** SOFTWARE
- **Key:**

  `HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Schedule\TaskCache`

## Linking Tree and Tasks

| Subkey | Contents |
|---|---|
| **Tree** | Task names and folders in a tree-like structure. |
| **Tasks** | Task information organized by GUID. |

1. Find the task under **`TaskCache\Tree`**.
2. Read its **`Id`** value.
3. Copy the GUID.
4. Locate the matching GUID under **`TaskCache\Tasks`**.
5. Examine the task’s stored data.

## Important Values

| Value | Purpose |
|---|---|
| **Description** | Optional description of the task. |
| **Actions** | Binary data describing what the task executes, including executable paths and parameters where applicable. |

- **Actions** requires parsing; it is not simply a plain-text path.
- Review the RegRipper output for tasks configured on the compromised system.
- The course leaves analysis of specific scheduled tasks as an exercise.

</details>

<details>
<summary>Summary</summary>

## Main Persistence Mechanisms

| Mechanism | What to investigate |
|---|---|
| **Run keys** | Auto-start values and their target programs. |
| **Services** | ImagePath, startup configuration, and ServiceDll. |
| **Scheduled tasks** | Task identifiers, actions, paths, and parameters. |

## Key Findings and Tools

- Attackers use persistence to **maintain access after logoff, restart, or process termination**.
- **Autoruns** helps examine persistence on live systems.
- **RECmd** and `RegistryASEPs.reb` extract persistence-related registry artifacts.
- The Accounting user’s Run entries point to:
  - `System.exe`
  - `ntlhost.exe.exe`
- These mechanisms are only a fraction of the possible persistence locations.

## Next Module

- Review the findings from the investigation and finish the course.

</details>