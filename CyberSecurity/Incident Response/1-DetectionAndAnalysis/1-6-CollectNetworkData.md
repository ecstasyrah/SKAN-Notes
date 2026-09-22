<details>
<summary><strong>Collect Network</strong></summary>

## Collect Network

- Capture network traffic to investigate malware communication and correlate it with host activity during the same period.
- Network data is highly volatile: traffic that was not captured generally cannot be recovered later without another recording source.

### Volatility Versus Practicality

- The **order of volatility** prioritizes evidence that disappears or changes quickly.
- The instructor’s informal **“order of plausibility”** means considering what collection is immediately practical.
- Host artifacts are often available first, while network changes require access, planning, and approval.
- This explains the course’s host-first demonstration; it does not make network evidence less important.

### Network Collection Methods

| Method | How It Works | Main Considerations |
|---|---|---|
| SPAN / Port mirroring | Copies traffic from selected ports or VLANs to a monitoring port | Requires device configuration, permissions, and a collection connection |
| Network TAP | Provides monitoring copies of traffic crossing a link | Inserting an inline TAP may interrupt service |
| Endpoint capture | Records traffic visible to an affected host’s interface | Quick to deploy but provides a limited view of the wider network |

### SPAN / Port Mirroring

- Configure the network appliance to copy traffic from a port, VLAN, or multiple VLANs to a destination port.

1. Identify the traffic and interfaces to monitor.
2. Obtain administrative access and change approval.
3. Configure the mirror destination.
4. Connect the capture device.
5. Verify that the required traffic is visible.

- Implementation varies by vendor.
- Mirroring consumes device resources and may drop copied packets when oversubscribed.

### Network TAP

- Place a TAP between two devices to obtain a copy of the traffic crossing that link.

1. Identify the link carrying relevant traffic.
2. Coordinate any required service interruption.
3. Insert the TAP into the link.
4. Connect the monitoring output to the collector.
5. Verify capture visibility.

- The lesson also mentions a **vampire tap**, which physically contacts cable conductors using probes.
- Vampire taps are associated with legacy coaxial Ethernet; they are not a general solution for modern Ethernet or fiber networks.

### Endpoint Capture During Triage

- Capture locally when infrastructure-level monitoring cannot be arranged immediately.
- Save packets as a PCAP for later analysis.
- Match packet timestamps with process, connection, and other host evidence.
- A local capture does not automatically reveal traffic between other devices.

### Exam Focus

- **Why collect host evidence first despite network volatility?** It may be immediately accessible while network capture requires approvals and setup.
- **SPAN versus TAP:** SPAN copies traffic through appliance configuration; a TAP observes traffic at the link.
- **Why capture on the endpoint?** It provides a practical early view of the affected host’s communication.

</details>

<details>
<summary><strong>Demo: Network Capture</strong></summary>

## Demo: Network Capture

- Capture the web-shell traffic and recurring communication associated with Ironcat.
- The demonstration uses Wireshark for inspection and RawCap for portable collection.

### Wireshark and RawCap

| Tool | Role in the Demonstration |
|---|---|
| Wireshark | Capture and interactively inspect packets |
| RawCap | Collect a PCAP without installing a separate Npcap-style capture driver |

- Typical Windows Wireshark packet capture requires a capture component such as Npcap.
- RawCap is portable, but its visibility depends on the operating system and selected interface.
- Installing tools changes the affected system; the lab installs Wireshark for teaching purposes.

### 1. Capture Web-Shell Traffic

1. Start Wireshark capture on the relevant Ethernet interface.
2. Apply the display filter `tcp.port == 8080`.
3. Observe communication involving the identified web shell.
4. Inspect HTTP requests, responses, and text content.

- In the controlled lab, the instructor submits `dir` through the shell to generate visible traffic.
- Document responder-generated traffic so it is not mistaken for attacker activity.
- The captured content may provide additional IOCs or patterns for network detection signatures.

### 2. Capture Communication with the Suspected Server

1. Retrieve the remote address from the previously saved `netconn.txt`.
2. Apply `ip.addr == 52.251.113.157`.
3. Wait for recurring traffic.
4. Preserve the capture for later analysis.
5. Correlate it with the process and connection evidence collected during triage.

- This lesson identifies the destination as **`52.251.113.157`**, correcting the earlier transcript’s inconsistent address.
- `ip.addr` matches packets with that address as either source or destination.
- The destination is associated with `hello.iamironcat.com` in the lab.
- A packet capture alone does not normally identify the owning Windows process; use host evidence for that association.

### 3. Collect with RawCap

1. Record the collection action and time in `captains_log`.
2. Run `RawCap.exe` to identify available interfaces.
3. Select the relevant interface.
4. Specify a PCAP output path in the device’s evidence folder.
5. Start capture and verify that the packet count increases.
6. Capture for the required observation period.
7. Stop collection and preserve the PCAP.

- The demonstrated interface index is `0`; check the actual interface list on each system.
- Open the saved PCAP in Wireshark or another network-analysis tool later.

### 4. Use the Provided Worksheet

- The course supplies a line-by-line triage worksheet in the course files and lab environment.
- It is a guided collection aid, not an unattended script ready to run unchanged.
- It can be adapted and tested for automation.

### Exam Focus

| Item | Meaning |
|---|---|
| `tcp.port == 8080` | Display traffic with source or destination TCP port 8080 |
| `ip.addr == 52.251.113.157` | Display IPv4 traffic to or from the specified address |
| PCAP | Saved packet evidence for later analysis |
| RawCap | Portable packet collection used in the demonstration |
| Host/network correlation | Connect observed traffic with processes and activity at matching times |

- These Wireshark **display filters** change what is shown; they do not limit which packets are captured.

</details>

<details>
<summary><strong>Summary</strong></summary>

## Summary

- Initial response established actionable findings, supported urgent decisions, and preserved evidence for deeper investigation.
- Broader analysis may continue for days or weeks across hosts, networks, services, and applications.

### What Is Known

| Finding | Significance |
|---|---|
| Ransomware affected Globomantics | Confirms a cybersecurity incident |
| Persistence was identified or indicated | Access may survive beyond the initial execution |
| Communication involves `iamironcat.com` | Provides a network investigation lead |
| A web shell listens on port `8080` | Provides an additional remote-access mechanism |
| The demonstrated sample targets `C:\Users` | Helps assess user-data impact and focus searches |
| Other systems show matching activity | Establishes an initial scope beyond one device |
| Host and network evidence was collected | Supports detailed analysis and timeline development |

### What Is Still Unknown

- How did the attacker first gain access?
- Was the entry point an email, macro, exposed web server, or another method?
- How did the malware reach additional systems?
- Are other backdoors or persistence mechanisms still active?
- Was data stolen or another objective pursued?
- Was ransomware the primary objective or a distraction?
- What is the complete extent of compromise?

### Investigate Possible Active Directory Compromise

- The lesson raises the possibility of a **Golden Ticket**: a forged Kerberos ticket-granting ticket.
- Creating one requires the domain’s `krbtgt` key material; local administrator access alone does not establish that capability.
- Resetting an ordinary user’s password does not invalidate a forged ticket created with compromised domain signing keys.
- Investigate domain-level credential compromise rather than assuming it occurred solely because endpoint administrator access was observed.

### Next Steps

1. Identify missing stages of the attack chain.
2. Analyze collected host and network evidence.
3. Refine IOCs and expand scoping.
4. Investigate the root cause and remaining attacker access.
5. Use the findings to guide containment, eradication, and recovery.

### Exam Focus

- **Initial scope is not final scope:** Early indicators may miss different tools or earlier attacker activity.
- **Encryption is not the whole incident:** Backdoors and other objectives may remain.
- **Collection supports analysis:** Preserve evidence now for questions that require deeper investigation.
- **Root-cause analysis matters:** Removing visible ransomware alone may leave the original access path intact.

</details>