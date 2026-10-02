<details>
<summary>It's Getting Hot in Here!</summary>

## Another Compromised Device

- Additional **IPs and ports from the macro for C2** revealed:
  - Another previously unidentified compromised device.
  - The **Dark Energy control server**.
- A plan for **eradication** is being put together with management.
- Globomantics has one more **host-based analysis task**.

## Ransomware as a Distraction

- Their data was encrypted, but:
  - **There's no ransom to be paid.**
- It appears to be:
  - **Destruction of data**.
  - A cover for an **entirely different lane of activity**.
- Likely target:
  - The research facility's **crown jewels**.
  - The **Dark Energy control server**.

## What Management Needs to Know

- **What data was accessed?**
- **What was the impact?**
  - On the **Dark Energy intellectual property**.
  - On the **services**.
- **What data might have been stolen or changed?**
- **Is there anything that you can do to fix it?**

## Why This Analysis Cannot Wait

- Management needs to understand the **nature of the breach**.
- This supports:
  - **Reporting requirements**.
  - Their **public response**.
  - Decisions about the affected services.

</details>

<details>
<summary>Demo: Edited Executables and Forensic Timeline</summary>

## Inspect the Dark Energy Control Server

- The server was **not hit with ransomware**.
- No hallmark traits:
  - No encrypted icons.
  - No recurring pop-up.
- A service is still running:
  - But it **isn't running as intended**.

**Signs of tampering in the lab**

- **Orbital Satellite Trajectory**:
  - Goes down, hits the ground, and flatlines.
- **Global heat absorption**:
  - At **18%**.
- An unprofessional message from **Ironcat**.
- The service display is **red**.

## Check Whether Logs Survived

- The ransomware cleared logs on affected devices.
- Devices not hit by ransomware:
  - May still have their logs.
- On this server:
  - **The logs were not cleared.**

## Security Logs and Audit Policy

- Start with the **Security** logs.
- Look for:
  - **Audit Failure**.
- Not everything is audited automatically.
- Different types of activity may require auditing to be enabled:
  - **Logon events**.
  - **Service access**.
  - **System events**.
- Check the local audit policy or **Group Policy**.

**Important**

- Missing logs may mean:
  - Logging was not enabled.
  - The activity was not being audited.
- **That doesn't confirm that there's no activity.**

## Rapid Logon Failures

- Multiple account names.
- Many failures happening **very rapidly**.
- XML view shows names such as:
  - **administrator**.
  - **admin**.
  - **server**.
- In the demonstration, this points to:
  - A **brute-force attempt to log in over RDP**.
- External IPs are attempting the logins:
  - RDP is exposed externally.

## Is the Brute Force Related to the Incident?

- The attacker already had access through:
  - A **C2 server**.
  - A **reverse shell**.
- Continuing to brute-force the administrator password:
  - Does not line up with the activity already being investigated.
- The lesson treats this as:
  - **Another set of attacks happening in parallel**.

**Response**

- Make a note in the **incident response report**.
- Tell the **incident manager**.
- Flag the externally exposed **RDP** service.
- Continue investigating the existing compromise.

## Remote Desktop Services Logs

**Location**

- **Applications and Services Logs**
- **Microsoft**
- **Windows**
- **RemoteDesktopServices**
- Relevant **Operational** logs

**What they reveal**

- Connections being made.
- Disconnections.
- IPs associated with RDP connection attempts:
  - Even when those attempts failed.

**Why they matter**

- Identify IPs that are supposed to log in.
- Identify IPs that aren't.
- Support blocking decisions.
- Investigate the attacker's other **operational IPs**.

## SMB Logs

- There are also logs associated with **SMB**.
- These can reveal:
  - Connections over **port 445**.
  - Connections to other IPs.

| Log category | Connection direction |
|---|---|
| **SMB Server** | Receiving the connection. |
| **SMB Client** | Creating the connection. |

**Finding on this device**

- No relevant SMB activity for this part of the incident.
- The last entry shown is **9/18**:
  - Does not line up with the activity being investigated.
- Other devices with known SMB activity:
  - May have useful evidence in these logs.

## Investigate the Changed Service

- The service is running:
  - But **something has changed**.
- Next question:
  - **Has the executable changed?**
- Use **Velociraptor** to create a:
  - **Forensic timeline based on the MFT**.

## MFT Forensic Timeline

- Windows uses the **MFT** to record information about files.
- Extract that information from the drive.
- Create a timeline to investigate:
  - File activity.
  - Changes.
  - What the attacker may have done.

**Velociraptor artifact**

- **Windows.Timeline.MFT**

## Filter for Executables

- Filter the **FullPath** column:
  - Look for files ending in **.exe**.
- The demonstration uses a **tilde-based matching expression**.

**Why filter?**

- Many files change frequently.
- Changes are not limited to:
  - Deleting files.
  - Moving files.
- Cache and disk-backed memory activity can create noise.
- Filtering helps focus on:
  - **Executable files relevant to the service**.

## Suspicious File Location

- Recent activity involves:
  - **Administrator\Downloads\Dark_Energy_Control_Service.exe**
- But the running service:
  - Is at the **root of C:**.
  - Does **not have the underscores**.

**Why this matters**

- Different locations.
- Similar names.
- Possible **renaming and replacement**.
- Use the timeline to identify changed files:
  - And understand what the attacker did.

## Inspect the Downloads Folder

**Files found**

- **PBindSharp_v4_dropper**
- **Dark_Energy_Control_Service**

**PBindSharp_v4_dropper**

- Identified in the lesson as a common dropper for:
  - **Posh C2**.
- Another **TTP** to record.

## Significance of the Downloads Folder

- When someone browses to a file and clicks download:
  - **Downloads** is the default location.
- The instructor's interpretation:
  - The attacker had access to the device.
  - Potentially through **RDP**.
  - Downloaded tools and a replacement service.
  - Swapped the existing service with a **mock service**.

**Possible sequence**

1. Download the replacement executable.
2. Rename it.
3. Put it in the expected service location.
4. Replace the legitimate service executable.

## Look for the Original Service

- If the attacker replaced the service:
  - What happened to the original file?
- It may have been deleted:
  - But not permanently.

**Recycle Bin finding**

- **Dark_Energy_Control_Service_old**

## Restore the Original File in the Lab

1. Inspect the **Recycle Bin**.
2. Find **Dark_Energy_Control_Service_old**.
3. Restore it.
4. Check where it returns.

**Result**

- Returns to the same **root location**.
- Suggests the attacker:
  - Renamed the existing service.
  - Replaced it with another executable using the expected name.
  - Left the original in the Recycle Bin.

## Executable Replacement and Persistence

- Similar to a persistence tactic:
  - **Replacing executables inside System32**.
- Example discussed:
  - **sethc.exe**.
  - The executable for **Sticky Keys**.
  - Triggered by pressing **Shift five times**.
- The lesson compares replacing that executable with:
  - Replacing a service executable that Windows expects to run.

**Main idea**

- A familiar filename or expected location:
  - Does not guarantee that the executable is the original one.

## Test Result in the Demonstration

- The instructor runs the restored executable.
- The service display becomes:
  - **Green**.
  - Apparently functioning as intended.
- The instructor also acknowledges:
  - Clicking another executable may already be **a step too far**.

## Bring the Findings to Management

- Stop and report:
  - The apparent service replacement.
  - The recovered original file.
  - The potential recovery option.
- Work with management on:
  - How to replace the altered service.
  - How to restore operations.

</details>

<details>
<summary>You Did It! What's Next?</summary>

## Follow the Evidence

- Not every host-analysis capability was used.
- The investigation used established methods effectively.
- Following the evidence revealed:
  - The **initial access vector**.
  - The **root cause**.
  - Additional attacker activity.

## 1. Analyze the Initial Triage Files

- Start with files collected during **initial triage**.
- Include network information from the **host perspective**.
- Individually, the evidence can be difficult to interpret.
- Combined information reveals:
  - **Internal C2 traffic**.
- The collected data is:
  - A snapshot from shortly after the ransomware launched.

## 2. Connect Network Activity to Memory Processes

- Raw memory contains a large amount of information.
- Use the network findings to locate:
  - Relevant **processes in memory**.
- Extract IOCs.
- Confirm:
  - **Daisy-chaining of an attacker C2 connection**.
  - A connection back to the **third victim device**.

## 3. Recognize the Broader Attack

- The option to pay the ransom was no longer a reality.
- The separate C2 activity suggested:
  - **Much more than a ransomware attack**.

## 4. Follow the Covert Channel

- Follow the channel back to its source.
- Deploy an **incident response agent**.
- Find:
  - Scheduled tasks dropped by the ransomware.
  - A **macro-enabled document**.

## 5. De-obfuscate the Payload

- Analyze the document's payload.
- Reveal the **external IP**.
- Combine all the IOCs.
- Find the remaining compromised devices:
  - Including connections to the **Dark Energy control server**.

## 6. Review the Server Logs

- Find continued threat actor activity.
- Recognize that some activity is:
  - **Likely unrelated to the current ransomware investigation**.
- Record that activity without losing focus on:
  - The incident being investigated.

## 7. Use File Timelining

- Find clues leading to:
  - The **replacement of the satellite service**.
- The attackers did not empty the **Recycle Bin**.
- Recover the original service.
- In the lab:
  - Get it back up and running.

## The Job Is Not Done

- The **immediate crisis** is avoided.
- But eradication and recovery are:
  - **Far from over**.
- The attack is broad and complicated.
- Restoring one service does not finish the response.

## Host Analysis and Network Analysis

| Host analysis | Network analysis |
|---|---|
| Examines endpoint files, processes, memory, logs, and timelines. | Examines communications and supports cutting off attacker access. |
| Reveals what happened on the devices. | Provides another perspective on the same incident. |

- **Host analysis is just one side of the story.**
- **Network analysis is pivotal**.
- Both are needed to understand the incident and:
  - **Deny the attackers access**.

</details>