<details>
<summary><strong>When to Break Out Wireshark for Threat Hunting</strong></summary>

## When to Break Out Wireshark for Threat Hunting
- Use Wireshark after broader security tools identify suspicious systems, communications, or time periods that require detailed packet-level analysis.

### Incident Scenario
- James is a cybersecurity engineer and incident-response team member at Globomantics, a multinational software development company.

#### Security Alert
- The intrusion detection system reports a possible security breach affecting several organizational systems.

#### Investigation Goals
- Determine how the attackers gained access.
- Identify which systems were compromised.
- Establish what the attackers did after gaining access.
- Determine whether any information was exfiltrated.

## Traffic Collection Questions
- Before analyzing packets, investigators must understand where the traffic came from and whether it covers the relevant activity.

### Important Questions
- What network traffic should be analyzed?
- How and where was it captured?
- Which tools collected it?
- Does the capture include the affected systems?
- Does it cover the period before, during, and after the alert?
- Was the traffic collected from the correct network location?

## Why Random Capturing Is Ineffective
- Capturing traffic from random locations and manually searching for unusual packets is slow and unreliable.

### Possible Problems
- The capture may not contain the attacker’s traffic.
- Important activity leading up to the breach may be missed.
- The investigator may collect large amounts of irrelevant data.
- The most useful traffic may disappear before the correct capture begins.
- Manual packet review can become time-consuming and tedious.

## Wireshark as a Network Microscope
- Wireshark provides a highly detailed view of individual packets and network conversations.

### Main Strength
- It helps investigators closely examine suspicious protocols, payloads, sessions, flags, files, and communication patterns.

### Main Limitation
- Wireshark may provide too much detail during the beginning of an investigation when the scope of the incident is still unknown.

#### Elephant Analogy
- Examining an entire network using only Wireshark is like trying to understand an elephant through a microscope: the details are visible, but the complete situation is difficult to see.

## Start with Broader Security Tools
- Use higher-level monitoring and detection tools to identify unusual behavior before examining individual packets.

### Examples
- Intrusion Detection Systems
- Intrusion Prevention Systems
- Security Information and Event Management systems
- Endpoint Detection and Response tools
- Firewalls and proxy logs
- DNS and authentication logs
- NetFlow or other network-flow records
- Network Detection and Response platforms

### Purpose
- These tools help identify the affected systems, suspicious IP addresses, unusual services, relevant time ranges, and possible indicators of compromise.

## Recommended Investigation Workflow
- Narrow the scope first, then use Wireshark for detailed packet inspection.

1. Receive an alert from a security or monitoring system.
2. Confirm the affected systems and accounts.
3. Identify suspicious IP addresses, ports, protocols, and domains.
4. Determine the relevant time range.
5. Locate existing packet captures or begin targeted traffic collection.
6. Apply focused Wireshark filters.
7. Analyze the suspicious conversations and payloads.
8. Document the attack method, affected systems, and possible data exfiltration.

## When to Use Wireshark
- Use Wireshark when the investigation has a specific system, conversation, indicator, or time period to examine.

### Good Use Cases
- Validating an intrusion-detection alert.
- Investigating suspicious DNS behavior.
- Examining port scans and unusual TCP flags.
- Reviewing unexpected SSH or remote-access traffic.
- Identifying traffic over non-standard ports.
- Extracting files from unencrypted traffic.
- Analyzing command-and-control communication.
- Confirming possible data exfiltration.
- Decrypting authorized TLS traffic when matching session secrets are available.

## When Wireshark May Be Too Early
- Avoid beginning with detailed packet analysis when the affected systems, relevant time period, and suspicious activity are still unknown.

### Better First Step
- Use broader monitoring tools to understand the overall network activity and narrow the investigation scope.

### Key Takeaway
- Wireshark is most effective as a focused investigation tool, not as the only threat-hunting platform. First identify suspicious behavior using broader security data, then use Wireshark to examine the relevant traffic in detail.

</details>

<details>
<summary><strong>Starting with IDS Alerts and Firewall/Server Event Logs</strong></summary>

## Starting with IDS Alerts and Firewall/Server Event Logs
- Use security alerts and system logs to identify the affected systems and time range before beginning detailed packet analysis in Wireshark.

## Avoid Random Packet Analysis
- Randomly capturing and reviewing traffic is inefficient because it provides no clear system, time, or indicator to investigate.

### Better Approach
- Start with alerts and logs that narrow the investigation before examining individual packets.

## Intrusion Detection Systems
- An IDS monitors network traffic and generates alerts when it detects suspicious behavior or known attack patterns.

### Recommended Monitoring Location
- Place network monitoring near the organization’s internet perimeter, such as immediately inside the outbound firewall.

#### Purpose
- Attackers commonly pass through the internet connection when accessing the network, receiving commands, downloading malware, or exfiltrating data.

### Open-Source Monitoring Tools
- Organizations can monitor important network links without purchasing an expensive commercial platform.

#### Suricata
- Provides signature-based detection, protocol analysis, and network security monitoring.

#### Snort
- Detects suspicious traffic using rules and known attack signatures.

#### Zeek
- Produces detailed network metadata and protocol logs that can support behavioral analysis and threat detection.

#### Important Clarification
- Zeek is mainly a network security monitoring and analysis platform rather than a traditional signature-only IDS.

## Firewall and Server Logs
- Logs from infrastructure and systems can reveal suspicious events that require packet-level investigation.

### Useful Log Sources
- Firewalls
- Servers
- Routers and switches
- Web servers
- DNS servers
- Authentication systems
- Proxy servers
- VPN gateways
- Endpoint security tools

### Suspicious Events
- Repeated failed logins
- Connections from unexpected IP addresses
- Unusual outbound traffic
- Access to unauthorized services
- Firewall rule violations
- Unexpected system or account changes
- Large or unusual data transfers

## Continuous Packet Collection
- Collect packets at important network locations before a security event occurs.

### Recommended Capture Locations
- Internet ingress and egress connections
- Links immediately inside the perimeter firewall
- Core network connections
- Server network segments
- Data-center links
- Connections to other critical systems

### Why Advance Collection Matters
- Packet data cannot normally be recovered after an event if no capture was running at the time.

#### Recommended Practice
- Maintain continuous or rolling packet capture so relevant traffic is available when an alert occurs.

### Operational Considerations
- Continuous capture requires proper planning and management.

#### Requirements
- Sufficient storage capacity
- Capture-file rotation or ring buffers
- Data-retention policies
- Accurate time synchronization
- Restricted access to packet data
- Privacy and legal approval
- Monitoring of capture-system health

## Information Provided by Alerts and Logs
- Automated security tools help identify the scope of the investigation.

### Event Time
- The timestamp indicates when investigators should begin reviewing the packet capture.

### Impacted System
- The source or destination address helps identify which computer, server, user, or network segment was involved.

### Additional Indicators
- Alerts may also provide:

  - Source and destination IP addresses
  - Source and destination ports
  - Protocols
  - Domains or URLs
  - Usernames
  - Alert signatures
  - Severity levels

## Using Packet Data
- Packet captures provide the detailed network evidence needed to reconstruct the event.

### Investigation Goals
- Determine how the attacker gained access.
- Confirm which systems communicated.
- Identify the protocols and services involved.
- Examine commands, files, or payloads when visible.
- Determine whether lateral movement occurred.
- Look for command-and-control communication.
- Identify possible data exfiltration.
- Find suspicious activity missed by automated tools.

## Recommended Investigation Workflow
- Use alerts and logs to narrow the scope, then examine the relevant traffic in Wireshark.

1. Receive an IDS alert or discover a suspicious log event.
2. Record the event timestamp.
3. Identify the affected source and destination systems.
4. Collect related IP addresses, ports, protocols, users, and domains.
5. Locate the packet capture covering the event.
6. Open the capture in Wireshark.
7. Apply targeted filters and coloring rules.
8. Examine relevant conversations and packet contents.
9. Export suspicious files when authorized and safe.
10. Document the findings and timeline.

## Important Time Requirement
- All monitoring systems should use synchronized clocks.

### Purpose
- Accurate timestamps allow investigators to match IDS alerts, firewall logs, server events, and packet captures correctly.

## Important Wireshark Features
- The investigation builds on five major Wireshark skills.

### Statistics
- Provide a high-level view of protocols, endpoints, conversations, and traffic volume.

### Filters and Coloring Rules
- Isolate suspicious traffic and make important packets easier to identify.

### Custom Columns
- Display useful information such as ports, flags, hostnames, and country data directly in the packet list.

### Export Objects
- Extract files and objects transferred through supported protocols.

### GeoIP
- Identify the approximate geographic location associated with public IP addresses.

## Key Takeaway
- IDS alerts and infrastructure logs identify when an event occurred and which systems may be affected. Continuous packet capture preserves the evidence, while Wireshark helps reconstruct the detailed network activity.

</details>

<details>
<summary><strong>Packet Analysis and the MITRE ATT&CK Framework/Cyber Kill Chain</strong></summary>

## Packet Analysis and the MITRE ATT&CK Framework/Cyber Kill Chain
- Map suspicious network activity to established attack models to understand what the attacker may be doing and what evidence should be investigated next.

## Purpose of Attack Frameworks
- Security frameworks organize attacker behavior into recognizable objectives, stages, techniques, and procedures.

### Investigation Benefits
- Provide structure for incident investigations.
- Help connect separate security events.
- Show how an attack may have progressed.
- Identify possible gaps in security monitoring.
- Guide investigators toward additional evidence.
- Create a common language for reporting findings.

## MITRE ATT&CK Framework
- MITRE ATT&CK organizes real-world attacker behavior into tactics and techniques.

### Tactics
- Tactics describe the attacker’s objective, such as gaining access, discovering systems, moving laterally, communicating with compromised devices, or stealing data.

### Techniques
- Techniques describe how the attacker attempts to achieve an objective.

### Important Clarification
- MITRE ATT&CK is not a strictly linear attack sequence. Attackers may use, repeat, skip, or combine different tactics and techniques.

## Cyber Kill Chain
- The Cyber Kill Chain describes an attack as a sequence of stages from preparation to achieving the attacker’s objective.

### Common Stages
- Reconnaissance
- Weaponization
- Delivery
- Exploitation
- Installation
- Command and Control
- Actions on Objectives

### Important Clarification
- Real attacks do not always follow every stage in a fixed order.

## Network-Visible Attack Activity
- Some attacker actions create network traffic that can be captured and analyzed in Wireshark.

### Reconnaissance and Discovery
- Packet analysis may reveal port scans, host enumeration, service discovery, and unusual DNS requests.

### Initial Access and Delivery
- Wireshark may show malicious downloads, phishing-related connections, transferred executables, or access to suspicious websites.

### Exploitation
- Network evidence may include unusual requests, malformed packets, exploit payloads, or unexpected responses from a vulnerable service.

### Lateral Movement
- Packet captures may reveal unexpected SMB, RDP, SSH, remote administration, or authentication traffic between internal systems.

### Command and Control
- Investigators may identify periodic connections, unusual DNS activity, traffic over non-standard ports, or communication with suspicious external systems.

### Collection and Exfiltration
- Packet analysis may reveal large transfers, unusual upload patterns, DNS tunneling, FTP transfers, or data sent to unexpected destinations.

### Impact
- Network traffic may show service disruption, destructive commands, denial-of-service activity, or communication associated with ransomware.

## Activity Not Always Visible in Packets
- Wireshark cannot directly observe every action performed on a compromised system.

### Examples
- Local privilege escalation
- Changes to files or registry settings
- Local credential theft
- Processes executed without network communication
- Persistence mechanisms
- Data collected but not yet transmitted
- Malware actions performed only in memory

### Additional Evidence Sources
- Endpoint Detection and Response logs
- Operating-system event logs
- Authentication logs
- Application logs
- File-system evidence
- Memory captures
- Malware-analysis results
- IDS and firewall alerts

## Mapping Packet Evidence

### Step 1: Identify the Network Behavior
- Determine what happened in the captured traffic.

#### Examples
- A device scanned several ports.
- An executable was downloaded through HTTP.
- A workstation contacted an unexpected external server.
- A large amount of data left the network.

### Step 2: Determine the Attacker’s Objective
- Decide which ATT&CK tactic or Kill Chain stage best explains the observed behavior.

### Step 3: Record the Supporting Evidence
- Document the relevant packets, IP addresses, ports, protocols, timestamps, domains, files, and conversations.

### Step 4: Correlate Other Data Sources
- Compare the packet evidence with IDS alerts, endpoint events, firewall logs, authentication records, and server logs.

### Step 5: Investigate Related Activity
- Search for evidence of earlier or later attacker behavior associated with the same systems.

## Avoiding Static Assumptions
- Attackers constantly change their tools, infrastructure, ports, protocols, and traffic patterns.

### Unreliable Assumptions
- A particular attack always uses the same port.
- Malware always communicates through one protocol.
- Every attack produces the same packet pattern.
- Traffic using a standard port is automatically legitimate.
- Traffic using a non-standard port is automatically malicious.

### Better Approach
- Understand how attacks work in principle, establish normal network behavior, and investigate meaningful deviations from that baseline.

## Investigation Reminder
- A packet pattern is normally an indicator that requires context and supporting evidence.

### Questions to Ask
- Is this behavior normal for the source device?
- Is the destination expected and trusted?
- Does the protocol match the port and payload?
- Does the timing correspond with another security alert?
- Are related systems showing similar activity?
- Which ATT&CK tactic or Kill Chain stage could explain it?

## Keeping Threat Knowledge Current
- Review updated threat intelligence and MITRE ATT&CK information because attacker techniques continually evolve.

### Key Takeaway
- Use MITRE ATT&CK and the Cyber Kill Chain to provide context for packet evidence. Wireshark can reveal several network-visible attack stages, but complete incident analysis requires correlation with endpoint, server, firewall, and security-monitoring data.

</details>