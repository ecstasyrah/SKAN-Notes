<details>
<summary><strong>Phases of IR</strong></summary>

## Phases of IR

- Incident response follows a structured process, although organizations may name or group its phases differently.
- This course focuses on preparation and initial triage during the first hours or days of an incident.

### Incident Response Activities

| Activity | Purpose |
|---|---|
| Preparation | Establish plans, people, tools, permissions, and access before an incident |
| Detection | Recognize activity that may indicate an incident |
| Analysis | Determine what happened, its scope, and its root cause |
| Containment | Limit the spread and impact of the compromise |
| Eradication | Remove malicious software, attacker access, and the conditions enabling the compromise |
| Recovery | Restore systems and normal business operations |

- These activities can overlap and repeat as new evidence emerges.

### Left of Bang and Right of Bang

- **Bang:** The point when the incident occurs.
- **Left of bang:** Preparation before the incident.
- **Right of bang:** Response after the incident, beginning with initial triage.
- Early decisions influence the longer investigation, threat removal, and recovery work.

### Nontechnical Preparation

- Establish how the team will coordinate, document its work, and obtain authorization.

1. Identify key contacts, responsibilities, and escalation paths.
2. Agree on communication methods that meet the organization’s requirements.
3. Prepare alternative communication methods if normal services are unavailable or compromised.
4. Use written communications where appropriate to preserve timestamps, decisions, and instructions.
5. Prepare chain-of-custody forms to track evidence collection, handling, and transfers.
6. Obtain written authorization defining which systems, networks, and information responders may access.

- Chain-of-custody records support evidence integrity and potential legal proceedings; they do not alone guarantee admissibility.

### Technical Preparation

- Permission to access systems and the practical ability to access them are separate requirements.

1. Request responder accounts before arriving or connecting remotely.
2. Confirm that the accounts have the necessary permissions.
3. Verify access to required devices, services, and investigation tools.
4. Request an updated asset inventory.
5. Ask the organization to prioritize assets by business importance, recovery urgency, and sensitivity.

### Asset-Prioritization Questions

- Which systems are essential to keep the business running?
- Which services must be restored first?
- Which assets contain the most sensitive or valuable information?
- Which systems or data are likely targets for the attacker?

### References Mentioned in the Course

- **NIST SP 800-61 Rev. 2:** The incident-handling guidance referenced by the instructor.
- **NIST SP 800-86:** Guidance on integrating forensic techniques into incident response, including evidence collection and processing.
- The instructor uses these vendor-neutral publications as foundations for response planning.

</details>

<details>
<summary><strong>Assets</strong></summary>

## Assets

- The response covers one Globomantics facility, with asset priorities supplied by the organization.

### Business Priorities

| Priority | Asset | Reason |
|---|---|---|
| Highest | Dark Energy storage servers | Protect valuable intellectual property |
| Lower in the supplied list | Dark Energy operational systems | Manage systems with potentially catastrophic effects |

- Confirm priorities directly with the organization; they may differ from a responder’s expectations.
- The scenario highlights a tension between protecting intellectual property and operational safety that should be clarified with decision-makers.

### Obtain Network Context

- An asset inventory identifies what matters; a network diagram helps determine where to investigate and monitor.

1. Request the permissions needed to deploy and operate response tools.
2. Obtain an updated diagram showing logical connections and, ideally, physical locations.
3. Identify the subnets containing critical storage and operational systems.
4. Locate suitable network TAP or SPAN points for packet capture.

### Choose the Monitoring Scope

| Capture Location | Purpose |
|---|---|
| Relevant network edge | Observe north–south traffic entering and leaving that boundary |
| Critical storage and operational subnets | Focus on the highest-priority systems |

- Capture visibility depends on the selected location and traffic paths.
- Available storage may limit how much traffic can be collected.
- A narrower scope reduces background traffic and irrelevant alerts but may miss activity elsewhere.

</details>

<details>
<summary><strong>Initial Triage</strong></summary>

## Initial Triage

- Validate the reported incident, identify initial indicators of compromise (IOCs), and establish the tools and infrastructure needed for investigation.

### 1. Prepare Responder Devices

- Use dedicated devices with a standard build that can be restored and redeployed.

1. Maintain a baseline system image.
2. Install the required investigation tools.
3. Prepare virtual machines for different operating systems.
4. Verify that the environments can analyze the expected artifacts.

- The instructor’s example uses a Linux desktop with VMware Workstation.
- A full response may require more than one laptop, including storage and supporting network equipment.

### 2. Validate the Reported Activity

- Gather accounts from administrators and security staff, then verify them against firsthand evidence.

1. Identify the events that triggered the response.
2. Collect available logs and artifacts from affected systems.
3. Access affected devices when authorized and necessary.
4. Record initial IOCs to guide further investigation.

- Direct login is one collection method, not the only way to validate an incident; live interaction can change system evidence.

### 3. Make Tools Available

- Choose a deployment method compatible with the organization’s restrictions.

| Method | Use |
|---|---|
| USB or CD | Transfer approved tools when removable media is permitted |
| Files hosted on the response laptop | Provide tools when removable media is restricted |

### 4. Establish Response Infrastructure

- Provide a central location for collected evidence, analysis, and team services.

1. Set up dedicated storage or file servers.
2. Deploy a separate response network using suitable switches or wireless equipment.
3. Provide environments for host and network analysis.
4. Support approved agents and continuous monitoring connections.

- Plan storage capacity and access controls for the evidence collected.
- A separate switch alone does not ensure isolation; separation depends on how the network is connected.

### 5. Arrange Out-of-Band Access

- Out-of-band access lets responders reach their infrastructure without depending on the organization’s potentially compromised network.

- The instructor suggests a dedicated **4G/5G hotspot** as an independent connectivity option.
- Local and remote responders can use this connection through an approved, secured access method.
- Keep response infrastructure appropriately separated from affected systems while allowing necessary collection and monitoring.

</details>

<details>
<summary><strong>Preparation Demo Tool Sets</strong></summary>

## Preparation Demo Tool Sets

- Prepare a tested incident response kit that allows responders to collect initial triage data quickly and consistently.
- The virtual lab differs from an enterprise environment, but the preparation principles remain the same.

### Make the Kit Accessible

- Store tools on removable media or host them on an accessible response system, such as through a Python HTTP server.
- The instructor prefers removable media for the demonstration.
- Choose a delivery method that works within the affected environment’s restrictions.

### Prepare Compatible Tools

- Keep tools for the operating systems and processor architectures you may encounter.

| Requirement | Examples |
|---|---|
| Windows architectures | x86/32-bit and x64/64-bit builds |
| Linux environments | Appropriate tools for Ubuntu and Red Hat-based systems |
| Alternative tools | Multiple tested options for the same collection task |

- Select tools that work reliably with the target system and your organization’s procedures.

### Tools Highlighted in the Demo

| Tool | Purpose |
|---|---|
| WinPmem | Capture Windows memory; the kit includes x86 and x64 versions |
| Memoryze | Alternative memory acquisition and analysis tool |
| Sysinternals Suite | Portable Windows utilities useful for investigation |
| RawCap | Capture network traffic without installing a separate packet-capture driver such as Npcap |
| Windows-Logs-Quick-Response | Demonstrated PowerShell script for automating triage tasks |

### Prefer Portable Tools

- Portable tools run without a conventional installation and can often run directly from removable media.
- This reduces setup time and avoids some installation-related changes.
- Portable does not mean zero impact: running tools can still change memory, create artifacts, or load components.

### Automate Initial Triage

- Initial triage happens under time pressure, so prepare scripts that carry out the collection steps in your playbook.

1. Identify the evidence and system information required.
2. Select compatible tools for each collection task.
3. Place the tools and scripts together in the response kit.
4. Automate their execution against the target device.
5. Test the workflow and verify its output before deployment.

- The demonstration uses PowerShell, but Bash, VBScript, or batch scripts may suit other environments.
- Automation reduces repetitive work, missed steps, and human error.
- Later lessons explain each tool and walk through the response script line by line.

</details>