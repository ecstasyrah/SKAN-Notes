<details>
<summary><strong>Setting the Stage</strong></summary>

## Setting the Stage

- Assume the role of a network security engineer joining the **Globomantics network security team**, with foundational knowledge of network security.

### Course Focus

- Use Wireshark to perform common network security tasks:
  - Test firewall rule sets.
  - Investigate insecure traffic.
  - Extract transferred objects and files.
  - Perform basic command-line analysis.

### Why Wireshark?

- Wireshark is widely used for packet analysis by network engineers and security engineers.
- Its capabilities also support security investigations and verification of network controls.

### Previous Course Knowledge

- This course builds on Chris Greer’s coverage in:
  - **Wireshark Configuration for Cybersecurity Analysis**
  - **Identify Common Cyber Network Attacks with Wireshark**

### Current Module

- Use Wireshark to test firewall capabilities, focusing on whether configured rules behave as intended.
- The next section explains how to build the demonstration environment.

</details>

<details>
<summary><strong>Creating a Learning Environment</strong></summary>

## Creating a Learning Environment

- Build a virtual lab with three Kali Linux machines and one OPNsense firewall to test traffic between trusted and untrusted networks.

### Software and Hardware Used in the Course

| Component | Course Setup |
|---|---|
| Virtualization platform | VMware Workstation Professional |
| Host memory | 16 GB RAM |
| Kali Linux | Version 2022.4 |
| OPNsense | Version 22.7 |
| Wireshark | Version 4.0.2 |
| Analysis and scanning tools | Wireshark and Nmap from the standard Kali installation |

- These versions describe the demonstration environment.
- Adjust CPU and memory allocations to your available hardware; 16 GB RAM is not stated as a minimum requirement.

### 1. Download the Software

1. Visit https://www.kali.org/get-kali/ and choose the appropriate Kali Linux image.
2. Visit https://opnsense.org/download/ and choose the appropriate firewall image.
3. Ensure Wireshark and Nmap are available in the Kali installation.

### 2. Create the Virtual Machines

- Configure four devices with the following roles:

| Device | Network | Role |
|---|---|---|
| Kali machine 1 | LAN1 | Inside/trusted host |
| Kali machine 2 | LAN1 | Inside/trusted host |
| Kali machine 3 | LAN2 | Outside/untrusted host |
| OPNsense firewall | LAN1, LAN2, and update connection | Connect and filter traffic between networks |

### 3. Configure Firewall Interfaces

1. Assign one interface to **LAN1**, the inside network.
2. Assign another interface to **LAN2**, the outside/untrusted lab network.
3. Add a third interface for public internet access used for system and package updates.

- The firewall requires at least two interfaces for the inside and outside networks.
- The demonstration uses a separate third interface for internet access; LAN2 represents the untrusted lab segment.

### 4. Update the Environment

1. Start the lab and verify connectivity.
2. Update all operating systems and installed packages using their appropriate repositories.
3. Use the relevant parts of the topology for each course module.

### Next Topic

- Review the simulated attacks that will target the firewall itself or pass through it.

</details>

<details>
<summary><strong>Reviewing Common Attack Types</strong></summary>

## Reviewing Common Attack Types

- Test how OPNsense firewall rules handle host discovery, port scanning, unusual TCP flags, and denial-of-service traffic.
- The module covers common examples, not every attack type.

### Lab Observation Method

- Capture traffic with Wireshark on both the outside Kali machine and an inside Kali machine.

1. Generate test traffic from the outside network.
2. Examine the firewall’s rules and console.
3. Compare captures on both sides to determine which traffic passes through.
4. Evaluate default rules, common configurations, and misconfigurations.

### ARP Discovery

- Address Resolution Protocol maps IPv4 addresses to MAC addresses on a local network.
- ARP discovery can identify active local devices, but ARP broadcasts normally do not cross routers or routed firewalls.
- This module therefore focuses on other methods for discovering hosts across the firewall.

### ICMP Ping Sweeps

- Send ICMP Echo Requests across the inside subnet to look for responding hosts.
- Unlike ARP, ICMP can travel across routed networks when permitted.
- Allowing ping can help network management but also enables discovery.
- No reply does not necessarily mean a host is offline; filtering may prevent a response.

### TCP and UDP Port Scanning

- Scan ports to identify reachable services on a target.
- These scans can follow ICMP discovery or independently probe hosts that do not answer ping.

**Service probing:**

- Examine responding services for software names, versions, and enabled features.
- Attackers may use this information to select service-specific exploits.

### TCP Flag-Based Scans

- Unusual flag combinations test how hosts and firewalls respond and may expose gaps in filtering.

| Scan | TCP Flags |
|---|---|
| FIN | FIN only |
| NULL | No flags set |
| Xmas | FIN, PSH, and URG set together |

- Some firewall configurations may pass these probes even when other discovery methods are blocked.
- Responses depend on the target’s TCP implementation and filtering; these scans do not reliably bypass every firewall.

### Denial-of-Service Attacks

- DoS attacks attempt to disrupt availability by overwhelming resources.
- Firewall protection depends on its implementation, configuration, and the attack type.
- Dedicated anti-DoS systems may provide protections beyond ordinary firewall rules.

**TCP SYN cookies:**

- Reduce resource consumption associated with incomplete TCP handshakes during SYN floods.
- The instructor states that this feature was disabled by default in the OPNsense version used when the course was developed.

**Lab objective:**

- Send DoS test traffic toward an inside host and determine whether firewall rules allow it through.
- Blocking or passing the test traffic alone does not establish the firewall’s overall resistance to DoS attacks.

### Next Topic

- Examine common firewall rule configurations and misconfigurations.

</details>

<details>
<summary><strong>Common Firewall Rule Misconfigurations</strong></summary>

## Common Firewall Rule Misconfigurations

- Firewall audits verify that rules match intended access policies while preserving required functionality.
- Common problems include obsolete rules, excessive permissions, duplicates, and incorrect rule order.

### 1. Obsolete or Unused Rules

- Rules may remain after services are retired, staff responsibilities change, projects finish, or policies are updated.
- Forgotten access, such as temporary remote access, may expose unintended devices or services.

**Audit steps:**

1. Compare rules with current device, service, project, and policy documentation.
2. Confirm whether each access requirement still exists.
3. Remove rules that are no longer needed.

### 2. Overly Permissive Rules

- Broad rules may improve convenience while allowing more access than necessary.
- Apply least privilege: permit only the required devices, destinations, protocols, and ports.

**Audit steps:**

1. Identify rules covering large groups of devices or broad traffic types.
2. Confirm the actual business requirement.
3. Narrow each rule to the minimum access needed.

### 3. Duplicate Rules

- Duplicate rules may not immediately change traffic handling, but they complicate maintenance.
- Removing only one copy can leave access enabled through another matching rule.

**Audit steps:**

1. Identify rules granting the same access.
2. Consolidate unnecessary duplicates.
3. When revoking access, check for other rules that still permit it.

### 4. Incorrect Rule Order

- Many firewalls evaluate rules from top to bottom and use the first matching rule.
- An earlier broad permit can prevent a later specific deny from taking effect.

| Order | Example Rule | Effect |
|---|---|---|
| 1 | Allow all traffic to a server | Matches first and permits traffic |
| 2 | Deny SSH to that server | Never reached for traffic already allowed |

**Audit steps:**

1. Check the firewall’s rule-processing behavior.
2. Look for broad rules near the top that hide the effect of later rules.
3. Place specific rules before broader rules where required by the intended policy.
4. Test that allowed and denied traffic behave as expected.

### Implicit Deny

- Many firewall rulesets end with an implicit deny that blocks traffic not explicitly permitted.
- This supports a restrictive policy: allow required access and deny everything else.
- An implicit deny cannot protect against traffic already permitted by an overly broad rule.

### Next Topic

- Review Wireshark features used to evaluate whether configured firewall rules work as intended.

</details>

<details>
<summary><strong>Briefing Wireshark Features for Analysis</strong></summary>

## Briefing Wireshark Features for Analysis

- Use packet panes, custom columns, and statistics to examine traffic and verify whether firewall rules behave as intended.

### Main Packet Panes

| Pane | Purpose |
|---|---|
| Packet List | Summarizes each packet: number, time, source, destination, protocol, length, and Info. |
| Packet Details | Expands protocol headers to show individual fields and their values. |
| Packet Bytes | Shows raw packet bytes in hexadecimal and their text representation. |

- The **Info** column summarizes dissected fields, which may include ports, TCP flags, and application commands.
- Selecting a field in Packet Details highlights its corresponding bytes in Packet Bytes.
- Raw bytes help investigate traffic that Wireshark cannot identify or has decoded incorrectly.
- The lesson’s example examines a TCP handshake packet with destination port `21`, commonly used for FTP control traffic.

### Customize the Packet List

- Add columns that make connections easier to identify and compare.

| Column | Benefit |
|---|---|
| Source Port | Shows the sending endpoint’s port |
| Destination Port | Shows the receiving endpoint’s port |
| Stream ID | Groups packets belonging to the same identified stream |

1. Select a packet containing the required field.
2. Expand its protocol details.
3. Right-click the field → **Apply as Column**.
4. Arrange the columns to suit your analysis.

### Use Consistent Timestamps

- The default relative time starts at the first captured packet.
- The instructor prefers UTC capture time with millisecond precision to simplify correlation across systems.

1. Open **View → Time Display Format**.
2. Select **UTC Time of Day**.
3. Set the display precision to **Milliseconds**.

- Consistent time formatting helps comparison; accurate correlation also requires synchronized system clocks.

### Protocol Hierarchy

- Open **Statistics → Protocol Hierarchy** for a high-level breakdown of the protocols identified in the capture.
- Use this as an initial overview before investigating individual conversations.

### Conversations

- Open **Statistics → Conversations** to examine communication between endpoints.
- Sort the results according to the investigation, such as by addresses, ports, packet counts, or bytes.

| Tab | What It Shows |
|---|---|
| Ethernet | Conversations between MAC addresses |
| IPv4 | Conversations between IPv4 addresses |
| TCP | TCP conversations, including endpoint ports |
| UDP | UDP conversations, including endpoint ports |

- TCP and UDP tabs help reveal hosts probing multiple ports or communicating with many destinations.
- In the example, the capture host is `192.168.1.95`, so it appears as an endpoint in most conversations.

### Investigate ICMP Discovery Scans

- Combine a display filter with conversation statistics to focus on ICMP activity.

1. Apply the display filter `icmp`.
2. Open **Statistics → Conversations**.
3. Enable **Limit to display filter**.
4. Select the **IPv4** tab.
5. Look for one source contacting many addresses and inspect the associated requests and replies.

- The filter includes all ICMP traffic, so verify that the packets are discovery probes rather than unrelated ICMP messages.

### Applying These Features

- Start with protocol and conversation statistics, then inspect specific packets and fields.
- Compare captures from the relevant sides of the firewall to assess whether configured rules permit or block the test traffic.

</details>

<details>
<summary><strong>Demo: Basic Wireshark and Firewall Displays</strong></summary>

## Demo: Basic Wireshark and Firewall Displays

- Review Wireshark’s packet panes and the lab’s initial OPNsense firewall rules before analyzing scan traffic.

### Demonstration Overview

- The demonstrations use prerecorded captures supplied with the course for repeatable analysis.

1. Review Wireshark and the base firewall rules.
2. Compare inside and outside captures during host discovery scans.
3. Analyze TCP SYN port scans with and without host discovery.
4. Examine NULL, FIN, Xmas, and DoS traffic.
5. Evaluate different rulesets, including misconfigurations.

### 1. Open the Example Capture

- Open the course’s **ftp-data** capture, which contains an FTP file transfer between two hosts.
- Later lessons examine the FTP exchange and export transferred objects.

### 2. Review the Packet Panes

| Pane | Purpose |
|---|---|
| Packet List | Provides an overview of captured packets |
| Packet Details | Displays decoded protocol fields |
| Packet Bytes | Shows raw hexadecimal bytes and their text representation |

1. Select a packet in the Packet List.
2. Expand its protocol sections in Packet Details.
3. Select individual fields to highlight the corresponding bytes in Packet Bytes.

### 3. Inspect Protocol Details

| Protocol | Fields to Examine |
|---|---|
| Ethernet | Source MAC, destination MAC, and EtherType |
| IPv4 | Source and destination IPs, flags, lengths, and other header fields |
| TCP | Ports, flags, and other TCP header fields |
| FTP | Commands, responses, and other decoded application content |

- TCP flags will be examined further during flag-based scan demonstrations.
- Raw bytes help investigate traffic that Wireshark cannot identify or has decoded incorrectly.

### 4. Review the Initial Firewall Rules

- OPNsense sits between the trusted and untrusted lab networks.

| Interface | Lab Role | Initial Rules | Expected Behavior |
|---|---|---|---|
| LAN1 | Inside/trusted | Allow-all rule enabled | Permits traffic entering the firewall from the inside for onward routing |
| LAN2 | Outside/untrusted | Displayed test rules are disabled | Implicit deny blocks unsolicited inbound traffic without a matching permit rule |

- These describe the demonstrated lab configuration.
- Interface rules apply to traffic entering the firewall through that interface.
- Disabled rules do not permit traffic.
- Because the firewall is stateful, replies belonging to an already permitted connection may pass even without a separate inbound allow rule.

### Expected Starting Behavior

- Inside hosts can initiate traffic permitted by LAN1’s rules.
- New connections initiated from LAN2 toward inside hosts should be blocked.
- Later demonstrations compare captures on both sides to verify this behavior as rules change.

</details>

<details>
<summary><strong>Demo: Discovery Scans of the Inside Network</strong></summary>

## Demo: Discovery Scans of the Inside Network

- Compare outside and inside captures to determine whether the firewall blocks discovery probes targeting `172.16.1.0/24`.
- The outside scanner is identified in the narration as `2.100`.

### 1. Examine TCP-Based Host Discovery

- The demonstrated Nmap `-sn` scan probes addresses using TCP ports `80` and `443`.
- Although called a “ping scan,” host discovery does not necessarily use only ICMP.

1. Open the outside capture.
2. Go to **Statistics → Conversations → TCP**.
3. Sort destination addresses and ports.
4. Compare packet counts in each direction.

**Observed results:**

- The scanner probes addresses throughout the inside subnet.
- Outbound probes appear, but no replies are captured.

### 2. Verify the Inside Capture

- Missing replies alone cannot establish where traffic was blocked.

1. Open the inside capture from the same time period.
2. Apply `!dns` to reduce background traffic.
3. Check for probes from the outside scanner.

- Only local ARP traffic remains in the demonstration; no matching discovery probes reach the monitored inside host.
- Together, the captures support that the firewall blocked the probes.

### 3. Examine ICMP Host Discovery

1. Open the outside ICMP scan capture.
2. Apply `icmp`.
3. Open **Statistics → Conversations → IPv4**.
4. Enable **Limit to display filter**.
5. Compare the results with the corresponding inside capture.

**Observed results:**

- Echo Requests target addresses throughout the inside subnet.
- Conversations show two packets sent and none returned.
- Wireshark indicates that no response was seen.
- The inside capture contains no matching external probes, consistent with the default deny policy.

</details>

<details>
<summary><strong>Demo: Port Scans to Specific Host (Common Techniques)</strong></summary>

## Demo: Port Scans to Specific Host (Common Techniques)

- Examine SYN scans targeting ports `1–1024` on the inside host `172.16.1.133`.

### 1. Scan with Host Discovery Enabled

- Nmap first checks whether the target appears reachable before proceeding with the port scan.

1. Inspect the outside capture for probes to ports `80` and `443`.
2. Check whether the target responds.
3. Compare the matching inside capture.

**Observed results:**

- Discovery probes receive no replies.
- Nmap considers the host down and stops before scanning the full port range.
- No corresponding probes appear at the inside host.

### 2. Scan Without Host Discovery

- Skipping discovery makes Nmap attempt the port scan even when discovery probes receive no response.
- The transcript uses the older `-P0` option; the standard option is `-Pn`.

1. Open the capture labeled **No Host Discovery**.
2. Go to **Statistics → Conversations → TCP**.
3. Sort the target ports and inspect directional packet counts.
4. Compare with the inside capture.

**Observed results:**

- Probes cover ports `1–1024`, with two packets per port in the demonstrated capture.
- No replies return, and the inside capture shows no matching external scan traffic.
- Skipping discovery changes scanner behavior; it does not bypass the firewall.

### 3. Confirm the Scan Type

1. Select a probe.
2. Expand **TCP → Flags**.
3. Verify that only SYN is set.

- A normal TCP handshake uses **SYN → SYN-ACK → ACK**.
- The separate explicitly selected SYN scan produces similar results.
- The demonstrated default firewall rules block both scans.

</details>

<details>
<summary><strong>Demo: Port Scans and DoS Attack (Alternative Techniques)</strong></summary>

## Demo: Port Scans and DoS Attack (Alternative Techniques)

- Test whether unusual TCP flags and a packet flood can reach `172.16.1.133` through the firewall.

### 1. Identify Alternative TCP Scans

| Scan | Flags Set | Purpose |
|---|---|---|
| NULL | None | Test responses to a packet without TCP flags |
| FIN | FIN only | Probe using a flag normally associated with ending a connection |
| Xmas | FIN, PSH, URG | Test responses to an unusual combination of flags |

- On TCP implementations following the relevant behavior, these probes to closed ports typically receive RST responses, while open ports may remain silent.
- Filtering can also cause silence, so no response does not prove that a port is open.
- Operating systems and intermediate firewalls can respond differently.

### 2. Compare Outside and Inside Captures

1. Open each scan’s outside capture.
2. Expand **TCP → Flags** to identify the probe type.
3. Open **Statistics → Conversations → TCP**.
4. Sort destination ports to inspect coverage; probe order may be randomized.
5. Compare the corresponding inside capture, separating local DNS and management traffic.

**Observed results:**

- NULL, FIN, and Xmas probes leave the scanner.
- No replies return.
- No corresponding probes appear at the monitored inside host.
- The firewall blocks all three scan types in the demonstration.

### 3. Examine the DoS Flood Capture

- The demonstration uses `hping3` to flood TCP port `80` with randomized source addresses.

1. Inspect timestamps using UTC with millisecond precision.
2. Compare the packet volume with the capture duration.
3. Open **Statistics → Conversations → TCP**.
4. Check the matching inside capture for forwarded flood traffic.

**Observed results:**

- Approximately 100,000 packets appear within roughly 3–4 seconds.
- The instructor reports about 100,004 TCP conversation entries; conversation counts and packet counts are different measurements.
- Randomized source addresses do not establish that many real hosts participated.
- No matching flood traffic appears in the inside capture, and the firewall remains operational.

### Interpretation

- The configured rules blocked the tested traffic.
- This short test does not establish resistance to every DoS attack or to higher, sustained traffic volumes.

</details>

<details>
<summary><strong>Demo: Show Common Ruleset Problems</strong></summary>

## Demo: Show Common Ruleset Problems

- Change firewall rules and compare captures to verify which traffic reaches the inside host `172.16.1.133`.

### 1. Allow Only FTP Control Traffic

- Enable a rule permitting TCP destination port `21` to the inside host.

1. Run the demonstrated SYN scan.
2. Inspect **Statistics → Conversations → TCP**.
3. Filter for `tcp.port == 21`.
4. Compare outside and inside captures.

**Observed results:**

- Port `21` probes pass through; other scanned ports remain blocked.
- The target returns **RST-ACK**, indicating that the connection is rejected rather than accepted.
- An open, accepting TCP service would normally respond with **SYN-ACK**.
- The rule permits access to port `21`, but it does not make an FTP service available.

### 2. Allow All IP Traffic to the Host

- Disable the FTP-only rule and enable a rule allowing all IP traffic to `172.16.1.133`.

**Observed results:**

- Many probes now reach the inside host.
- Numerous RST-ACK responses return from the host’s closed ports.
- Inside captures confirm that the firewall forwards the scan.

**Configuration concern:**

- A broad permit exposes more of the host than a narrowly scoped service rule.
- Prefer specific required permissions followed by an implicit deny.
- If exceptions are needed before a broad rule, their order must match the firewall’s processing behavior.

### 3. Allow All TCP and UDP Traffic

- Replace the all-IP rule with one permitting TCP and UDP to the host.

**Observed results:**

- The TCP scan still passes through and looks similar on both sides.
- Restricting protocol types does not restrict the accessible TCP or UDP ports.
- A TCP-only test does not verify how UDP or other protocols are handled.

### 4. Examine Duplicate SSH Rules

- Enable two rules that both permit TCP port `22`.

1. Inspect the outside scan.
2. Compare the inside capture.
3. Identify the sender of each SYN, SYN-ACK, and RST.

**Observed sequence:**

- The outside scanner sends a SYN.
- The inside host returns a SYN-ACK, indicating an accepting SSH port.
- The outside system sends a RST, ending the attempt without completing a normal connection.

- The instructor attributes the reset to the scanner host’s kernel; the capture directly establishes its source and flags.
- A scanner-originated RST does not mean the target port is closed.

### 5. Disable Only One Duplicate Rule

- Disabling one SSH permit leaves the other rule active.

**Observed results:**

- Port `22` remains reachable.
- Captures look similar even though the administrator intended to revoke access.
- Duplicates are harder to notice when separated within a large ruleset.

**Audit steps:**

1. Check for duplicate or overlapping permissions.
2. Remove or disable every rule that unintentionally preserves the access.
3. Apply the changes.
4. Retest and compare captures to verify the intended result.

</details>

<details>
<summary><strong>Summary</strong></summary>

## Summary

- The **Validating Firewall Rules** module demonstrates how packet evidence can confirm whether firewall behavior matches the intended policy.

### Topics Covered

1. **Security role:** Work as a Globomantics network security engineer.
2. **Learning environment:** Use Kali Linux, OPNsense, and Wireshark across inside and outside networks.
3. **Attack patterns:** Examine discovery scans, port scans, unusual TCP flags, and DoS traffic.
4. **Rule problems:** Identify obsolete, overly broad, duplicate, and incorrectly ordered rules.
5. **Wireshark features:** Use packet panes, custom columns, filters, and statistics.
6. **Demonstrations:** Compare captures under default and modified firewall configurations.

### Main Lessons

- Compare both sides of the firewall; missing replies alone do not prove that probes were blocked.
- Check packet direction and flags to distinguish blocked traffic, closed ports, and accepting services.
- Permitting traffic does not mean the destination service is running.
- Broad or duplicate rules can preserve unintended access.
- Retest rule changes to confirm actual behavior.

</details>