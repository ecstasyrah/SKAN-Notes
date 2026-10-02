<details>
<summary>Developing Accurate Tests for Infection</summary>

## Current Situation

- The attackers were and are likely still active in the environment.
- A **C2 channel clearly separate from the ransomware execution**:
  - Unrelated to the **web shell back door**.
  - Does not appear on all affected devices.
- Connections point back to:
  - A **separate attack path**.
  - A **peer endpoint device**.
- The ransomware appears to be a **false front**.

## Main Goal

- Get the information required to:
  - **Cut off the attackers successfully**.
  - Proceed to **eradication**.
- There is no simple **“pay them and move on”** solution.

## Initial IOCs vs. New IOCs

| Evidence | What was found |
|---|---|
| **Initial ransomware IOCs** | Ransomware on **victim 1 and victim 2**. |
| **Host network connection analysis** | A nexus point of an additional internal C2 process on the **.10 device**. |
| **Process analysis** | Validated the process activity to develop additional IOCs. |

**Search for:**

- Additional connections to **port 15111**.
- Preceding connections over **SMB or port 445**.
- Additional connections to the **internal IP**.
- Associated **C2 connections and processes**.

## Rescope the Intrusion

- Initial triage provided **initial scoping**.
- As you find new threat actor activity:
  - **Fully scope the intrusion before fully eradicating the adversary**.
- Iterate on this **scoping component of containment**.
- The internal C2 IOCs have not yet been used to:
  - **Actively rescope the infection**.

## Host Analysis and Network Analysis

- Host analysis takes place:
  - During the **analysis phase**.
  - **Prior to eradication**.
  - In parallel with **network analysis**.
- Network analysis helps:
  - Confirm IOCs.
  - Identify more details.
  - Fill gaps in analysis done solely on the host.

## Why the Initial Actions Would Not Be Enough

Simply doing the following would not properly contain the event:

- Blocking **hello.iamironcat.com**.
- Deleting the **initial executable** and associated processes.

**Why?**

- Additional ransomware activity.
- **Secondary connections unrelated to that process**.

## Decision Point

- **Are we ready to properly contain the event and cut off access to the threat actor?**
- **Do we have the IOCs required to do so?**

## Approval to Deploy Agents

- Accounts and access have been sorted out.
- Globomantics has approved deploying **agents onto the network**.
- Purpose:
  - **Real-time scoping** with the new IOCs.
  - Follow the trail for **root cause**.

</details>

<details>
<summary>Demo: User Analysis</summary>

## Start on Victim 2

- Ransomware is still running.
- An additional executable is:
  - Hosting the **web shell**.
  - Reaching out over the network.
- Deploy a tool that gives access to:
  - **Live information on the device**.

## Velociraptor

- A **self-contained executable**.
- A **portable executable** that does not require installation.
- Can run as:
  - A **server**.
  - A **client**.
- In this demonstration:
  - Launched directly on the device being inspected.

## Launch the Local Interface

1. Open the **command line**.
2. Go to the folder containing the executable:
   - **LAB_FILES** in the demonstration.
3. Use **dir** to see the files.
4. Run the Velociraptor executable with:
   - **gui**
5. Browse to:
   - **localhost:8889**

- The demonstration uses the local lab login.
- The interface allows you to launch **hunts**:
  - Looking for different kinds of **artifacts**.

## Create a Hunt

1. Click the **plus button under Hunt**.
2. Name the hunt:
   - **another cat hunt**.
3. Select the **artifacts**.
4. Configure the **parameters**.
5. Launch the hunt.

**Artifact categories**

- **Generic**.
- **Linux**.
- **Windows-specific modules**.

- You can select **multiple artifacts**.

## Evidence of Download

- Example artifact:
  - **Windows.Analysis.EvidenceOfDownload**
- Looks under:
  - User **Downloads** folders.
- Uses:
  - **ZoneId=[34]**

**Purpose**

- Identify files sourced from the **internet**.
- Zone information helps distinguish:
  - Files downloaded from outside sources.
  - Files sourced locally.

## Artifacts Used in the Hunt

- **Evidence of execution**.
- **Evidence of download**.
- **Sessions**.
- **Task Scheduler**.
- **Autoruns**.
- **Untrusted binaries**.

**Why Task Scheduler and Autoruns?**

- A pop-up **continues to appear**.
- Identify **why it keeps running**.

**Untrusted binaries**

- A binary that isn't trusted by the operating system:
  - That is running.

## Review the Results

- Click **Notebook**.
- Sort through the results.
- Run **queries against the results**.
- Sort by executable and examine activity over time.

**Remember**

- Not all modules will return useful information.
- Some modules may have **no information**.

## Evidence of Execution

**SysinternalsSuite**

- Appears in the results.
- This is what was run during **initial triage**.
- Your own response activity was captured too.

**daisypayload.bat**

- Ran **after you left**.
- Associated with the internal connection:
  - **Daisy-chaining**.
- The lab uses an obvious name:
  - In another case, it could be named something more stealthy.

## Shim Cache and Timeline

- The hunt also returns **shim cache** information.
- The demonstration examines a **registry key** for executable activity.
- Velociraptor can create a **timeline** from the collected information.

## Inspect Task Scheduler

- Look for tasks you **don't expect to see**.
- Consider the environment:
  - This is an **Amazon instance**.
  - Some Amazon-related PowerShell activity is expected.
- A large number of scheduled tasks is normal:
  - Task Scheduler is used by the operating system.
  - Tasks are **not malicious by nature**.

## Do Not Mistake the Display Limit for All Results

- The Notebook initially returns **50 results**.
- Edit the query:
  - Change **LIMIT** to **100**.
  - Save and rerun the query.
- But 100 still does not show everything:
  - The lab has **136 tasks**.

**To inspect the full results**

1. Click **Results**.
2. Choose the artifact.
3. Select **TaskScheduler**.
4. Review all the available entries.

## Persistence Finding

| Detail | Finding |
|---|---|
| **Scheduled task** | **IAMNOTACAT** |
| **Folder** | **SNAP** |
| **File executed** | **potts.bat** |
| **Observed behavior** | Runs the recurring pop-up. |

- This is a **version of persistence**.
- Find it before moving to **eradication**.
- Otherwise, containment will not be effective.

## Investigate 172.31.37.10

- Next device:
  - **172.31.37.10**.
- Other devices are connecting to it.
- On this device:
  - **None of the desktop files are encrypted**.
  - It was not hit by the ransomware.
- It was not found through the initial ransomware IOCs.

## Launch Velociraptor on the New Device

- Launch it the same way as on victim 2.
- The home page shows:
  - The current device's status.
  - Connected clients.
- In this local setup:
  - It is both **server and client**.
  - Only one local client is shown.

## Think About the Initial Access Vector

**If the device were in the DMZ or publicly exposed:**

- Investigate:
  - A **web shell**.
  - A **web server vulnerability**.

**What was actually found:**

- An **internal endpoint**.
- A device where users **check their email**.

**What to look for next:**

- **Evidence of phishing**.
- A **potentially malicious document**.

## Hunt: Cats in Documents

1. Start a new hunt:
   - **Cats in Documents**.
2. Select artifacts.
3. Look for:
   - **OfficeMacros within files**.
   - Evidence that a **macro was enabled**.

**Distinguish the evidence**

| Evidence | Question |
|---|---|
| **A file containing a macro** | Does the document have a macro? |
| **Macro-enabled activity** | Was the document opened and were macros enabled? |

- Relevant evidence can also be found in the **registry**.

## Suspicious Document Found

- **Globomantics Dark Energy Absorption Technology Formula** document.
- Located in:
  - The **Administrator's Downloads folder**.
- Contains a:
  - **VBA script**.
- Looks similar to:
  - The payload previously found in memory.

**Administrator account**

- Should they be using the Administrator account to check email?
  - **Absolutely not.**
- This is part of the lab setup:
  - Allows the demonstration without focusing on privilege escalation.

## Obfuscation: Small Strings

- The payload is not placed in one block.
- It uses **tiny strings**:
  - Described in the demonstration as being under a detection threshold.
- The strings are combined:
  - **One set at a time**.
  - Until the completed string is run.

## Base64 Padding

- Not all Base64 strings end with an **equal sign**.
- An equal sign at the end can be an indicator of:
  - **Base64 padding**.
- A string followed by an equal sign inside a VBA macro:
  - Could help identify encoded content.
- Data can be sized so that:
  - Base64 does **not require equal-sign padding**.

**Why this matters**

- A detection that depends only on the equal sign:
  - Could miss other encoded strings.
- Consider why a malicious macro was not detected.

## Obfuscation: PowerShell One Letter at a Time

- The macro adds letters to a variable.
- Those letters spell:
  - **PowerShell**.
- It then executes the completed command.
- This avoids a simple search for:
  - The complete word **PowerShell**.

## Likely Root Cause

1. Started with devices affected by ransomware.
2. Found additional activity pointing to **172.31.37.10**.
3. Inspected that endpoint.
4. Found a document containing a suspicious **VBA macro**.
5. The payload resembles what was found in memory.

- The evidence lines up with:
  - The existing host findings.
  - Further network analysis.
- **Patient zero has likely been found.**

</details>

<details>
<summary>It's All a Ruse</summary>

## How the Investigation Developed

- Started with **ransomware executable IOCs**.
- Connected the findings through analysis.
- The evidence led to:
  - **172.31.37.10**.
- Inspecting that device paid off.

## The Attackers' Mistake

- Intended to use ransomware for **cover**.
- Maintained a **separate channel** to important servers.
- But also used the same device as a:
  - **Pivot point to distribute the ransomware**.
- **That's how we found them.**

## Why Behavioral IOCs Matter

- The attackers also used **similar TTPs**.
- **That's why behavioral IOCs are so effective.**
- Attackers are humans:
  - They also make mistakes.

## Value of Incident Response Agents

- Purpose-built agents deployed onto a system:
  - Help collect information for **analysis**.
  - Make investigation of live activity more effective.
- The demonstration shows how useful that capability can be.

## Root Cause and Initial Foothold

- Found **patient zero** and the associated document.
- The document was **likely from phishing**.
- This explains the nexus of events around that endpoint.
- It appears to be the **initial foothold**.

## The Sample to Analyze

- The file that was found:
  - Carries the **malicious payload**.
- Information from the document helps:
  - Investigate the assumed root cause.
  - Develop indicators for eradication.

## Final Scoping Before Eradication

- Combine:
  - Previously collected **IOCs**.
  - The **root-cause findings**.
  - Information from the **malicious document**.
- Complete **final scoping**.
- Work with the **incident manager**:
  - Provide the information required for eradication.
  - **Eradicate the attacker from the network**.

</details>