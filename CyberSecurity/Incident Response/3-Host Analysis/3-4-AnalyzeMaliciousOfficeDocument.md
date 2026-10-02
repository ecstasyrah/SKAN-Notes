<details>
<summary>How Far Do You Dive In?</summary>

## Initial Access Vector Found

- You found the **initial access vector**.
- You could move to **eradication**:
  - The timing is up to the **incident manager**.
- The new artifact, the **Office macro**, could have useful information.

## Next Decision Point

- **What do you need from this Office document?**
- **What will the impact of that information be to final scoping and eradication?**

## The Incident Responder's Role

- There are people whose entire careers are to:
  - Reverse **malware**.
  - Reverse **maldocs**.
- Your job during the incident:
  - Know enough to **recognize the components of malicious activity**.
  - Perform enough analysis to support **decisions and direction**.
- **Just enough analysis to get to the information that you need to make the decisions at each phase.**

## What You Need from the Document

- Determine whether there are **external C2 IPs hiding inside the document**.
- Find the external IPs that the macro reaches out to.
- Use **network analysis** to hunt down those connections.

## Two-Stage Deployment

1. The **macro is the first stage**.
2. It reaches out to the **C2 server**:
   - Pulls down the **malware payload**.
   - The payload connects back to the **C2 server**.

## Three-Stage Deployment

1. The macro reaches out to a **false web front**.
2. That web front hosts the **payload**.
3. The payload executes and talks back to the **C2 server**.

- Keep these stages in mind when examining the macro:
  - Make sense of **what it is contacting and why**.

</details>

<details>
<summary>Demo: Analyze Office Document</summary>

## Analyze the Collected Document

- Return to the **incident response desktop**.
- The document has been pulled back for analysis.
- Use tools to dump out the **VBA macro information**.

## 1. oleid: Inspect the Document

- First tool:
  - **oleid**.
- Run it against the **Globo document** in the **Lab files** folder.
- Returns information about the document.

| Finding | Information |
|---|---|
| **Document format** | **MS Word 97–2003** |
| **Container format** | **OLE** |
| **VBA macros** | Present |
| **Risk finding** | Suspicious VBA macros |
| **Author metadata** | The instructor's name, as part of the lab |

## Document Format and Suspicious Macros

- The lesson highlights the older **Word 97–2003 format**:
  - Discussed as a way attackers may avoid protections associated with newer formats.
- The document contains **VBA macros**.
- The demonstration discusses suspicious characteristics such as:
  - **High entropy**.
  - A macro that is **too long**.

**High entropy**

- High **randomness of the characters**.
- Can help identify content that needs further analysis.

## 2. olevba: Extract and Analyze the Macro

- Next tool:
  - **olevba**.
- Gives information specifically about the **VBA macro**.
- Dumps the contents of the VBA script:
  - In a format that's easy to read.
- Also provides its own **analysis of suspicious features**.

## Auto Execution

- From the attacker's perspective:
  - The macro needs to **execute automatically** when the relevant event occurs.
  - Otherwise, someone would have to find and execute it manually.
- The trigger depends on:
  - The **document type**.
  - The **macro event or keyword**.

**Examples discussed**

- **AutoOpen**.
- Document-open events.
- New-document events.
- **Auto_Open**.
- Excel workbook-open events.

- The tool explains which triggers it finds:
  - And when they run.

## Suspicious Keyword: exec

- The tool identifies **exec** as potentially associated with shell execution.
- In this sample, it appears in:
  - **exec bypass**.
- Here, the term is part of a PowerShell execution-policy setting:
  - It is not itself a command to execute a separate program.

**Detection limitation**

- The attacker could also obfuscate **exec**:
  - Just as the word **PowerShell** was obfuscated.
- This illustrates the **cat-and-mouse game** of detection.

## Encoded Strings

- The tool also identifies:
  - **Base64-encoded strings**.
  - **Hex strings**.
- These are useful findings for further inspection.

## Automated Analysis Across the Environment

- Pull documents that may contain macros from the **entire environment**.
- An organization using automation may have:
  - Many macros.
  - Macros that are **not all bad**.
- Analyze them using these tools.
- Pull out documents marked:
  - **Suspicious**.
  - **High risk**.
- Focus manual analysis on those documents.

## 3. Copy the Macro into CyberChef

1. Copy the extracted macro.
2. Open **CyberChef**:
   - https://gchq.github.io/CyberChef/
3. Paste the macro into the input.
4. Remove the surrounding code that does not need de-obfuscation.
5. Keep the pieces containing the **Base64 string**.

**Already understood**

- The surrounding code launches **PowerShell**.
- PowerShell executes the result of the assembled command.
- The focus is the **encoded content**.

## 4. Reassemble the Base64 String

- The macro builds its payload from multiple strings.
- Use **Find / Replace** to remove the VBA syntax.

**Clean-up steps**

1. Remove the repeated text:
   - `str = str + "`
2. Remove the remaining quotation marks.
3. Remove the initial assignment if it does not match the repeated pattern.
4. Keep the encoded fragments in their **original order**.

**Result**

- The Base64 string needed for decoding.

## 5. Decode the First Layer

1. Add **From Base64** to the CyberChef recipe.
2. Add **Decode text**.
3. Select:
   - **UTF-16LE**.
   - **Code page 1200**.

**Why Decode text is needed**

- After Base64 decoding, the text may show:
  - Dots or other unreadable characters.
- **UTF-8** does not produce the expected result in this demonstration.
- **UTF-16LE** produces readable text.

**Finding**

- The command looks very similar to:
  - The command previously found in memory.
  - The command connecting the victims back to the compromised endpoint.

## 6. Start with the Decoded Output

- Copy the readable output back into the input.
- Clear the previous recipe steps if starting from this new input.
- This is optional:
  - It makes the next stage easier to follow.
- The command contains:
  - A **GZip stream**.
  - An **I/O memory stream**.
  - Another **Base64 string**.

## 7. Decode and Decompress the Inner Payload

1. Remove the surrounding PowerShell commands.
2. Keep only the **inner Base64 string**.
3. Apply **From Base64**.
4. Apply **Gunzip**.

**Result**

- Decompresses the data.
- Reveals the **clear text** being investigated.

## CyberChef Recipe Overview

| Stage | Operations |
|---|---|
| **Clean the VBA strings** | Find / Replace; remove assignments and quotation marks. |
| **Decode the outer layer** | From Base64 → Decode text: UTF-16LE. |
| **Extract the inner payload** | Keep the Base64 string inside the GZip-related command. |
| **Decode the inner layer** | From Base64 → Gunzip. |
| **Inspect the result** | Identify the external C2 destination. |

## 8. Identify the External C2

- The decoded payload has the same format as:
  - The command previously found in memory.
  - The command that launched the **DaisyChain back to the .10 device**.
- This time:
  - The connection goes **externally** in the lab scenario.

**Lab addressing**

- The instructor treats the **101 network** as external.
- The address still begins with **172.31**:
  - It is private addressing.
  - The lab uses different subnets to represent internal and external systems.
- The transcript does **not provide the complete external C2 IP**.

## Investigation Path

1. Started with a **ransomware victim**.
2. Found **internal C2 over a different channel**.
3. Found the device likely compromised through a **macro**.
4. Analyzed the macro.
5. Identified the **external C2 IP**.

## Stop When the Required Information Is Found

- **This is all we needed from this.**
- The external IP supports:
  - Further network analysis.
  - Final scoping.
  - Eradication.
- No need to continue deeper into the macro during this decision point.

</details>

<details>
<summary>Just Enough Analysis</summary>

## What Was Accomplished?

- You don't need to be:
  - A **maldoc reverse engineer specialist**.
  - A **malware researcher**.
- With the external IP:
  - You have the final piece of the root-cause puzzle needed for further scoping and eradication.

## Use Network Analysis to Complete Scoping

- Find anything else connecting back to those IPs.
- Combine the results with the existing host findings.
- Confirm the IOCs required to:
  - **Move on to eradication**.

## Why Avoid Going Too Deep During the Incident?

- Stay focused on the **organization's needs**.
- Avoid chasing every rabbit hole.
- Perform enough analysis to support:
  - The current decision.
  - Containment.
  - Eradication.
  - Recovery.

## When to Perform Deeper Analysis

- After **eradication and recovery**.
- When the immediate incident timeline pressure has passed.

**Then investigate in greater depth**

- **Map out everything that happened**.
- Find **novel activity**.
- Validate:
  - **What was accessed**.
  - **By whom**.
- Dissect each piece of the attack.
- Sift through all the collected data.

## Host and Network Analysis Work Together

- Host findings and parallel network analysis provide:
  - The information needed to **kick out the attackers**.
- Eradication and recovery include:
  - **Removing attacker access**.
  - **Getting operations back online**.

</details>