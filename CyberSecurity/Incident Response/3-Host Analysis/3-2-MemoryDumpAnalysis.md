<details>
<summary>Synthesized Antibodies and Threat Actors</summary>

## Understand What Caused the Compromise

- You need to know **what caused them to get compromised by the threat actor in the first place**.
- Part of the collection included:
  - **Dumping memory images from the affected devices**.
- Memory captures from only affected devices:
  - Don't give much of a **baseline**.
  - Need to understand the difference between an **unaffected device** and those **hit with ransomware**.

## Two Paths of Activity

| Path | What you are investigating |
|---|---|
| **Ransomware** | Track back the ransomware kill chain to its origin. |
| **Separate communication channel** | A channel the attackers could be using to laterally communicate. |

- Heavy indications point to the device found through:
  - **Host-based network connection analysis**.
- Two separate sets of activity:
  - Likely from the **same root cause or initial access point**.
  - Still **two different paths of behavior**.

## Why Look for the Process?

- **No communication comes without a process.**
- That process is generating the **internal communications**.
- Find the process to develop this activity into **IOCs**.

## Why More IOCs Are Needed

- There is **zero guarantee** that the only actions the attackers took were:
  - Drop ransomware.
  - Leave.
- Devices may have this **communication channel active**:
  - Without being affected by the ransomware.
- Finding the processes and other IOCs associated with the channel:
  - Allows you to find other places the attackers **were or are currently operating**.

## Ransomware as a Cover

- Ransomware could be a cover to:
  - **Steal intellectual property**.
  - Cause **covert harm**.
- Smart attackers could exclude targets of their clandestine activity:
  - From the devices hit with ransomware.
- Ransomware indicators are:
  - **Loud**.
  - **In your face**.
  - Easy to find.
- The company puts pressure on everyone:
  - To **get back to normal**.

## Separate Internal C2 and Lateral Movement

- Systems such as the **secret dark energy satellite system**:
  - May not be hit with the same ransomware.
  - May not have the same IOCs.
- Instead:
  - A **fully separate internal C2**.
  - **Lateral movement**.
- Some **behavioral TTPs** will be the same:
  - That's what we're counting on.

</details>

<details>
<summary>Demo: Identify Running Process, Services, and Executable</summary>

## Start with the Memory Capture

- The very first thing done on each device during initial triage:
  - **Pull down a memory capture**.
- Victim 1's memory capture:
  - **victim1.raw**.
- Raw memory was collected:
  - **Before you started taking actions on that device**.
- Known suspicious activity:
  - Network connections to a specific device.
  - **Port 15111**.

**Questions to answer**

- What process is connected to those network connections?
- What was that process doing?

## Use Volatility

- The tool used in the demonstration:
  - **Volatility 3**.
- The instructor uses version 3 because:
  - Version 2 doesn't support all the newer Windows memory layouts used in the lab.
- Incorrect memory parsing can produce:
  - **Weird characters**.
  - Information that doesn't line up with what it expects to see.
- Version 3 works for the lab's:
  - **Windows 10**.
  - **Windows Server 2019**.

## 1. Netscan: Connect Network Activity to a Process

- **netscan**:
  - Like doing a **netstat** against memory.
  - Shows connections from the time the memory was captured.
- The previously collected network connections:
  - Didn't have processes associated with them.
- Volatility also prints:
  - **Process IDs associated with that traffic**.

**Finding on victim 1**

| Evidence | Finding |
|---|---|
| **Remote IP** | **172.31.37.10** |
| **Port** | **15111** |
| **Process ID — PID** | **5632** |
| **Process name** | **PowerShell** |

- Put these findings in your **notes**.
- Use the process ID to:
  - Look up the individual process.
  - Identify what it was doing.

## PowerShell and the Existing Ransomware IOC

**PowerShell connection**

- PowerShell methodology suggests looking for:
  - **Encoded commands**.
  - A **callback**.
- Continue investigating:
  - The separate connection over **15111**.

**Existing ransomware activity**

- **Port 8080**.
- **ironcatwuzhere** process.
- The web shell hosted by the ransomware.
- Already seen during initial triage:
  - An existing **indicator of compromise**.

## 2. Pslist: Identify the Process and Its Parent

- **pslist**:
  - List processes in memory.
- In Bash, use **grep**:
  - Look for **5632**.
  - Avoid searching through the entire output manually.

**Finding**

| Process information | Value |
|---|---|
| **Process** | **PowerShell** |
| **PID** | **5632** |
| **Parent process ID** | **5840** |

- Now you have the parent process ID:
  - To investigate **what launched PowerShell**.
- The PowerShell process connects over **15111**:
  - To a device that doesn't have ransomware.
- This tracks the **separate C2 channel**.

## 3. Cmdline: Examine How PowerShell Was Launched

- **cmdline**:
  - Prints command lines used to run processes in memory.
- Command line information is still in memory:
  - Dump it out and examine it.

**Questions to answer**

- Was this PowerShell running normally?
- Did a user run it?
- Were they running normal system administration commands?
- Is this something actually interesting?

**Finding**

- A **massive Base64 string**.
- Associated with:
  - **PowerShell**.
  - **PID 5632**.
  - The connection over the unusual port.
- Encoding PowerShell commands:
  - Common activity for threat actors.

## PowerShell Command Components

| Component | Purpose |
|---|---|
| **-exec bypass** | Requests an execution policy bypass for that PowerShell session. |
| **-NonInteractive** | Runs without interactive user prompts. |
| **-WindowStyle Hidden** | Hides the PowerShell window. |
| **-e** | Abbreviation used here for **-EncodedCommand**; supplies a Base64-encoded command. |

- **Base64 is encoding, not encryption.**
- The encoded command must be decoded:
  - To understand **what it actually does**.

## 4. Decode the First Base64 Layer

1. Save the command line information.
2. Copy it into a **new file**.
3. Remove the PowerShell command and its switches:
   - Leave the **Base64 string**.
4. Decode the string.

**Tools mentioned**

- **PowerShell**.
- **Bash**.
- **CyberChef**.

**Bash option**

- **base64 -d**:
  - **-d** for decode.
- Paste the encoded information.
- Copy the decoded output into the analysis file.

**VS Code display**

- **Option+Z or Alt+Z**:
  - Changes text wrapping.
  - Makes the long command easier to inspect.

## 5. Examine the Additional Encoding and Compression

- After the first decoding:
  - The output is **still encoded**.
  - Additional commands become visible.
- Compression and encoding are used to:
  - **Stream information into memory**.
  - Launch the reverse shell.

**Analysis process shown in the demonstration**

1. Set the data equal to a **variable**.
2. Work through the decoding and decompression.
3. Set up a **memory stream** to capture the output.
4. Copy the information to the output.
5. Create a **byte array**.
6. Convert the bytes into readable **ASCII** text.

**Goal**

- Print the underlying code for inspection.
- Understand the actual commands being placed into memory.

## 6. Identify the Callback Destination

- The decoded code reveals the **URL used for the connection back**.
- The payload connects over:

| Connection detail | Finding |
|---|---|
| **Protocol** | **HTTP** |
| **Destination** | **172.31.37.10** |
| **Port** | **15111** |

- This matches the connection tracked throughout the investigation.
- Now investigate that device:
  - **Is this our patient zero?**
  - **Is this where everything started?**
- Look for **common behavioral techniques** on that device.

</details>

<details>
<summary>Continued Activity</summary>

## What Has Been Found?

- Recovered the original executable that spawned the ransomware.
- Found processes correlated with:
  - Network activity in the **firewall logs**.
- Confirmed a **second lane of activity**:
  - At least at the time the data was pulled.
- Indicates **continued attacker activity**.

## The Ransomware Portal Was Fake

- The incident manager reports:
  - They decided to **pay the ransom**.
  - When trying to engage the organization, they got **silence**.
- The portal appears to have been **fake**.
- This aligns with the suspicion:
  - **This isn't just about ransomware**.
  - There is additional threat actor activity to investigate.

## Stay Focused on the Business Need

- There is always **another thread to pull**.
- You can get stuck:
  - Moving from box to box.
  - Tracking the attacker through the network one piece at a time.
- The business needs to:
  - **Get to eradication**.
  - **Return to normal operations**.

## Initial Scoping Is No Longer Enough

- Initial scoping identified the extent of the **ransomware attack**.
- It was based on:
  - Initial IOCs.
  - Information collected from affected endpoints.
- The script in memory points to:
  - **172.31.37.10**.
- That IP was **not on the list of devices affected by ransomware**.

## Encoded PowerShell Alone Is Not Enough

- Some environments have unusual-looking command lines:
  - **Windows Azure Endpoint**.
  - **SolarWinds**.
  - **SentinelOne**.
- These can have similar encoding.
- But the **de-obfuscated payload** in this case:
  - Is not explained by common applications.
  - Launches a **reverse shell**.
  - Streams the payload directly into memory.
  - Creates a connection to another device for **command and control**.

## Consider the Intel Gain/Loss

- Your gut reaction may be to **kill the process**.
- Every situation is different.
- In this case, consider:
  - **Intel gain/loss**.

**If you kill the process**

- The attacker loses the connection.
- They may assume their C2 details have been exposed:
  - **Protocol**.
  - **Port**.
  - **IP**.
- They may **change tactics**.

## Internal Pivoting and Daisy-Chaining

- The connection over **15111** is to an:
  - **Internal IP**, not external.
- Indicates:
  - **Internal pivoting**.
  - Also called **daisy-chaining**.
- Cutting off this connection:
  - Does **not** mean you have cut off the attacker's access to the entire environment.

## Next Decision Point: Rescope the Incident

**Question**

- How do you identify devices compromised by:
  - **Ransomware**?
  - The additional **command and control connection**?

**Parallel investigation**

- Use the new IOC in **network analysis**.
- **Rescope the incident**.
- Identify devices not previously recognized as compromised.
- Avoid slowly following each connection one by one.

## Limits of the New Indicators

- **PowerShell**:
  - Hardly unique.
- **Port 15111**:
  - Likely effective for identifying the **internal connections**.
  - Not necessarily the **external connection**.

## Decision in This Scenario

- **Do not kill the process yet**.
- You are not ready for **eradication with high efficacy**.
- The clock is ticking:
  - Continue answering the questions needed for effective containment and eradication.

## Identify the Root Cause

- Identify the **root cause**, if possible.
- If there is an unknown **external-facing vulnerability**:
  - The adversary can simply get right back in.
  - This time with more incentive to be sneaky.

## Questions the Business Needs Answered

- **What else is being impacted?**
- **What data was compromised?**
- With ransomware decryption now considered extremely unlikely:
  - These are the questions to answer next.

</details>