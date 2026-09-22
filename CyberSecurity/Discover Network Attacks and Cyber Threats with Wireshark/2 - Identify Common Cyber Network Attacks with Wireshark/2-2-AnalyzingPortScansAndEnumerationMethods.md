<details>
<summary><strong>Network and Host Discovery Scans</strong></summary>

## Network and Host Discovery Scans
- Identify scanning activity used to discover active devices, open ports, running services, operating systems, and possible vulnerabilities.

## Purpose of Network Discovery
- Attackers need information about a network before they can identify systems that may be vulnerable to exploitation.

### Information Collected
- Active devices
- IP addresses
- Open and closed ports
- Available network services
- Operating systems
- Network infrastructure
- Possible vulnerabilities

### Investigation Goals
- Identify where the scan originated.
- Determine which systems were targeted.
- Find the systems that responded.
- Identify the ports and protocols scanned.
- Determine whether the activity was authorized.
- Look for exploitation or lateral movement after the scan.

## Initial Access
- An attacker must first gain a foothold before scanning an internal network.

### Possible Entry Methods
- Physical network access
- Phishing attacks
- Malicious email attachments
- Malware callbacks
- Stolen VPN credentials
- Compromised user accounts
- Exploitation of internet-facing services

## Lateral Movement
- After gaining access, an attacker may search for other systems that contain more valuable information or privileges.

### Discovery Before Movement
- The compromised system can scan nearby devices and services to identify possible lateral-movement targets.

## ARP Scans
- An ARP scan discovers active devices on the same local broadcast domain.

### How It Works
- The scanning system sends ARP requests for multiple local IP addresses and monitors which devices send ARP replies.

### Network Limitation
- ARP scanning normally works only within the local Layer 2 broadcast domain because routers do not forward ARP requests.

### Indicators
- One device sends ARP requests for many IP addresses.
- Requests occur quickly and sequentially.
- The scanner has no normal reason to communicate with the requested systems.
- Multiple devices respond directly to the same source.

## ICMP Ping Sweeps
- An ICMP ping sweep searches an IP range for systems that respond to ICMP Echo Requests.

### How It Works
- The scanner sends ICMP Echo Requests to several addresses and records the systems that return Echo Replies.

### Common Indicators
- One source sends ICMP Echo Requests to many destination addresses.
- Requests follow a sequential address pattern.
- A large number of requests occur within a short period.
- Several systems return ICMP Echo Replies to the same source.

### Limitation
- Many systems and firewalls block or ignore ICMP, so a device may be active even when it does not respond to a ping.

## TCP SYN Scans
- A TCP SYN scan checks whether TCP services are available on one or more systems.

### How It Works
- The scanner sends initial TCP SYN packets to selected destination ports.

### Typical Responses
- A SYN-ACK response usually indicates that the port is open.
- A TCP RST response usually indicates that the port is closed.
- No response may indicate that the traffic was filtered or dropped.

### Indicators
- One source sends SYN packets to many ports on one system.
- One source sends SYN packets to the same port on many systems.
- Many connections are attempted without completing the TCP handshake.
- Common service ports are tested in rapid succession.

## UDP Scans
- A UDP scan tests systems for available UDP-based services.

### How It Works
- The scanner sends UDP packets to several destination ports and observes the responses.

### Typical Responses
- A valid UDP response may indicate an available service.
- An ICMP Destination Unreachable or Port Unreachable message usually indicates a closed port.
- No response may mean that the port is open, filtered, or that the service simply does not reply.

### Investigation Challenge
- UDP scans can be slower and more difficult to interpret because UDP does not use a connection handshake.

## Operating-System Fingerprinting
- Attackers may examine how a device responds to specially formed packets to estimate its operating system.

### Possible Indicators
- Unusual TCP flag combinations
- Repeated probes to closed and open ports
- Abnormal ICMP requests
- Tests involving TCP options, window sizes, or response behavior

## Recommended Investigation Process
- Examine the scanning pattern and determine whether it represents legitimate or unauthorized activity.

1. Identify the source IP address.
2. Determine the scan type: ARP, ICMP, TCP, UDP, or a combination.
3. Count the number of targeted systems and ports.
4. Review the time between requests.
5. Identify which systems responded.
6. Determine which ports appeared open.
7. Check whether the source is an approved scanner or administrator system.
8. Review activity immediately after the scan.
9. Correlate the packets with IDS, firewall, endpoint, and authentication logs.

## Legitimate Scanning
- Not every network scan is malicious.

### Possible Authorized Sources
- Vulnerability scanners
- Asset-discovery systems
- Network-monitoring platforms
- Configuration-management tools
- Security assessment teams
- System administrators

### Verification
- Compare the source address, scan schedule, target range, and scanning method with approved security activities.

## Lab Capture Environment
- The course captures were created in a virtual lab using controlled sample scans.

### Simplified Traffic
- Much of the unrelated background traffic was removed so the scanning behavior is easier to recognize.

### Real-Network Difference
- Production captures contain normal user traffic, broadcasts, application sessions, retransmissions, monitoring traffic, and other background activity.

## Practice Recommendation
- Practice using captures from a network you own or are explicitly authorized to monitor.

### Safety Reminder
- Do not capture or scan networks without permission. Follow organizational policies, privacy requirements, and applicable laws.

### Key Takeaway
- ARP scans discover local devices, ICMP sweeps identify responsive IP systems, and TCP or UDP scans search for available services. Always identify the source, targets, responses, timing, and authorization before classifying the activity as malicious.

</details>

<details>
<summary><strong>Lab 1 – Detecting Network Discovery Scans with Wireshark</strong></summary>

## Lab 1 – Detecting Network Discovery Scans with Wireshark
- Identify an ARP-based network discovery scan, determine its source, and find the systems that responded.

### Required Capture File
- Open the `Lab1_NetworkScan` trace file.

### Step 1: Confirm the Correct PCAP
- Verify that your capture matches the file used in the lab.

1. Check the filename shown in the lower-left corner.
2. Confirm that it displays `Lab1_NetworkScan`.
3. Compare the total packet count with the lab example.

#### Packet Count
- The packet count is a useful way to confirm that two users have opened the same PCAP.

## Wireshark Security Profile
- Use a dedicated security profile containing the columns, filters, and preferences needed for forensic analysis.

### Profile Features
- GeoIP databases
- TCP port-name resolution
- Security-focused custom columns
- Saved display filters
- Coloring rules
- Consistent packet-pane layout

#### Profile Selection
- The active profile appears in the lower-right corner of Wireshark.

## Configuring the Time Format
- Display timestamps in UTC so investigators in different time zones see the same packet times.

### Step 2: Set UTC Time
- Change the packet-list timestamps to UTC Time of Day.

1. Select **View**.
2. Select **Time Display Format**.
3. Choose **UTC Time of Day**.

#### Forensic Benefit
- UTC provides a common worldwide time reference for correlating packet captures, alerts, and logs.

## Identifying the ARP Scan
- Look for one source sending many ARP broadcasts for sequential IP addresses.

### Step 3: Review the Packet List
- Examine the ARP packets at the beginning of the capture.

1. Locate packets identified as **ARP**.
2. Review the source MAC address.
3. Review the destination address.
4. Examine the target IP address in each ARP request.
5. Compare the addresses across consecutive packets.

#### Observed Behavior
- The same source MAC address repeatedly sends broadcast ARP requests.
- The requests target sequential local addresses such as `.2`, `.3`, `.4`, and `.5`.
- The source continues through the subnet’s IP-address range.

#### Finding
- Sequential ARP requests from one source are consistent with an ARP discovery scan.

### Lab Scope
- Ignore the TCP SYN packets and ICMP error temporarily and focus on the ARP activity.

## Understanding ARP Scanning
- An ARP scanner searches the local broadcast domain for devices that are currently active.

### How It Works
- The scanner broadcasts a request asking which device owns each local IP address.

### Successful Discovery
- A device that owns a requested address normally sends an ARP reply containing its MAC address.

### Network Limitation
- ARP discovery normally remains inside the local Layer 2 broadcast domain because routers do not forward ARP broadcasts.

## Comparing ARP Frame Lengths
- The capture contains ARP packets displayed with different captured lengths.

### Observed Lengths
- Packet `13` is displayed as `42` bytes.
- Another ARP packet is displayed as `60` bytes.

### Step 4: Inspect the 60-Byte Packet
- Examine the additional Ethernet padding.

1. Select the 60-byte ARP packet.
2. Expand **Ethernet II**.
3. Locate **Padding**.
4. Select the padding field.
5. Review the zero bytes in the packet-bytes pane.

#### Ethernet Padding
- Padding is added when necessary to meet Ethernet’s minimum frame-size requirement.

### Step 5: Inspect the 42-Byte Packet
- Compare the shorter captured packet with the 60-byte packet.

1. Select the 42-byte ARP packet.
2. Expand **Ethernet II**.
3. Check whether Wireshark displays a padding field.
4. Compare it with the 60-byte packet.

#### Important Correction
- A 42-byte capture does not necessarily mean that a physically undersized Ethernet frame traveled across the wire.

#### Capture Behavior
- The network adapter, driver, or capture point may omit Ethernet padding from the captured data even though padding existed on the wire.

#### FCS
- Wireshark commonly displays a 60-byte Ethernet frame when the 4-byte Frame Check Sequence is not included in the capture. Its actual size on the wire would be 64 bytes.

## Finding ARP Replies
- Filter for ARP replies to determine which systems responded to the scan.

### ARP Opcode Values
- Opcode `1` represents an ARP request.
- Opcode `2` represents an ARP reply.

### Step 6: Prepare the Opcode Filter
- Build the filter from an ARP request packet.

1. Select an ARP request.
2. Expand **Address Resolution Protocol**.
3. Locate **Opcode**.
4. Confirm that its value is `1`.
5. Right-click **Opcode**.
6. Select **Prepare as Filter → Selected**.
7. Change the value from `1` to `2`.
8. Apply the filter.

#### ARP Request Filter
- Use `arp.opcode == 1`.

#### ARP Reply Filter
- Use `arp.opcode == 2`.

### Result
- Two systems respond to the ARP scan.

#### Responsive Addresses
- `56.1`
- `56.100`

#### Attacker’s Next Step
- The scanner can record these responsive systems and perform further port or service discovery against them.

## Investigating the Scanner
- Determine whether the source MAC address belongs to an approved device.

### Investigation Questions
- Which device owns the source MAC address?
- Is it an authorized vulnerability or asset scanner?
- Is the activity occurring during an approved maintenance window?
- Does the device normally perform network discovery?
- Which systems responded?
- Did the source perform TCP or UDP scans afterward?

## Establishing a Baseline
- Compare the scan with the normal amount and pattern of ARP activity on the network.

### Normal ARP Activity
- ARP requests and replies are required for ordinary IPv4 communication on Ethernet networks.

### Suspicious ARP Activity
- One source rapidly requests sequential addresses across an entire subnet.
- ARP activity suddenly increases beyond the normal baseline.
- An ordinary user workstation begins enumerating nearby systems.
- The discovery activity is followed by port scans or connection attempts.

### Important Reminder
- ARP traffic alone is not malicious. Its source, frequency, sequence, timing, and purpose determine whether it should be investigated.

### Key Filters
- ARP traffic: `arp`
- ARP requests: `arp.opcode == 1`
- ARP replies: `arp.opcode == 2`

### Key Findings
- One MAC address sends sequential ARP requests across the subnet.
- The activity represents a textbook ARP discovery scan.
- Two systems respond at `.1` and `.100`.
- The source device should be identified and checked for authorization.

### Key Takeaway
- Detect ARP discovery scans by looking for one source broadcasting requests for many sequential IP addresses. Filter with `arp.opcode == 2` to identify the systems that responded.

</details>

<details>
<summary><strong>Lab 2 – Identifying Port Scans with Wireshark</strong></summary>

## Lab 2 – Identifying Port Scans with Wireshark

- Detect ICMP discovery sweeps and TCP port scans, highlight SYN packets, and examine unusual TCP options.

### Required Capture

- Open `Network_PortScan.pcapng`, which contains just over `5,000` packets.
- Addresses such as `2.15` and `0.8` below are abbreviated as spoken in the lesson, not complete IP addresses.

### Step 1: Identify the ICMP Ping Sweep

- Look for one source sending Echo Requests to sequential destination addresses.

1. Review the beginning of the capture.
2. Locate the source ending in `2.15`.
3. Observe requests to addresses ending in `0.1`, `0.2`, `0.3`, and onward.
4. Locate the request sent to `0.8`.
5. Check packet `11` for the corresponding Echo Reply.

#### Finding

- The sequential requests indicate a Layer 3 discovery sweep.
- The reply from `0.8` confirms a responsive host, not an open TCP port.
- Wireshark’s arrows beside the frame numbers visually link related requests and replies.

### Step 2: Prepare a TCP SYN Filter

- Isolate packets with the SYN flag enabled.

1. Select a TCP SYN packet.
2. Expand **Transmission Control Protocol → Flags**.
3. Right-click **SYN**.
4. Select **Prepare as Filter → Selected**.
5. Review and apply `tcp.flags.syn == 1`.

#### Filter Scope

- `tcp.flags.syn == 1` matches both SYN and SYN-ACK packets.
- To show only initial connection attempts, use `tcp.flags.syn == 1 && tcp.flags.ack == 0`.

### Step 3: Highlight SYN Packets

- Create a coloring rule so SYN traffic stands out without requiring a display filter.

1. Copy the SYN filter.
2. Open **View → Coloring Rules**.
3. Add a new rule named `TCP SYN`.
4. Paste the filter.
5. Choose a bright color, such as green.
6. Enable the rule and click **OK**.

#### Import the Course Rules

- In **Coloring Rules**, click **Import** and select the supplied `chriscoloringrules` file to use the instructor’s rules.

### Step 4: Add Packet Comments

- Document suspicious behavior directly in the capture.

1. Right-click the relevant packet.
2. Select **Packet Comment**.
3. Record the observation and save the comment.
4. Save the capture as `.pcapng` to preserve packet comments.

### Step 5: Examine TCP Options and Window Size

- Inspect the SYN packet’s characteristics for clues that it was generated by a scanning tool.

1. Select the initial SYN discussed in the lab.
2. Expand **Transmission Control Protocol → Options**.
3. Note the **Maximum Segment Size (MSS)** of `1460` bytes.
4. Review the advertised TCP window of `1024` bytes.
5. Check for other TCP options.

#### Observed Characteristics

- MSS is the only TCP option shown.
- Window scaling, timestamps, and SACK-permitted options are absent.
- Similar SYN packets target addresses ending in `0.13`, `0.14`, `0.17`, and `0.18`.

#### Important Clarification

- MSS limits the TCP data size per segment; the receive window limits how much unacknowledged data the receiver currently accepts.
- A window smaller than MSS is valid, and missing optional TCP features does not prove malicious activity.
- These characteristics become useful clues when combined with repeated probes across many hosts and ports.

### Step 6: Review TCP Conversations

- Use conversation statistics to identify the scanner’s targets and tested ports.

1. Clear the display filter to review the full capture.
2. Open **Statistics → Conversations**.
3. Select the **TCP** tab.
4. Locate the source ending in `2.15`.
5. Sort **Address B** to group destination systems.
6. Review **Port B** for the ports tested on each system.

#### Targeted Ports

- `443` – HTTPS
- `80` – HTTP
- `110` – POP3
- `135` – Microsoft RPC Endpoint Mapper

#### Finding

- One source probes several common ports across multiple destination systems, supporting the identification of a TCP port scan.
- Address B and Port B identify endpoint B; confirm the connection direction before assuming it is the server.

### Key Takeaway

- Identify discovery sweeps through sequential ICMP requests, highlight SYN traffic with coloring rules, and use TCP Conversations to reveal the scan’s scope. Combine packet characteristics with traffic patterns and authorization checks before classifying activity as malicious.

</details>

<details>
<summary><strong>Lab 3 – Analyzing Malware for Network and Port Scans</strong></summary>

## Lab 3 – Analyzing Malware for Network and Port Scans

- Investigate a real malware capture to identify ARP discovery, ICMP scanning, and responding systems.

### Required Capture

- Open `AnalyzinganAttack.pcapng`.
- Use the supplied password `Infected` to unlock the lab download.
- The capture was provided by Brad Duncan of malware-traffic-analysis.net and contains Hancitor malware traffic.

### Malware Handling

- Keep this exercise focused on packet inspection.
- Do not use **File → Export Objects → HTTP** to save and run embedded executables.
- Analyze the capture in an isolated lab; viewing packets is different from executing their contents, but no software-based analysis is entirely risk-free.

### Step 1: Read the Lab Questions

- Review the investigation questions embedded in the capture before checking the answers.

1. Open **Statistics → Capture File Properties**.
2. Read the **Capture file comments**.
3. Attempt the questions using the techniques from Labs 1 and 2.

### Step 2: Identify the ARP Scanner

- Find the device requesting addresses throughout the `10.6.1.x` subnet.

1. Apply the filter `arp`.
2. Look near packet `17403`, where the sweep begins.
3. Select an ARP request from the repeatedly appearing source.
4. Expand **Address Resolution Protocol**.
5. Read the **Sender IP address**.

#### Finding

- The infected scanner is `10.6.1.101`.
- Wireshark resolves its MAC prefix to Hewlett Packard.
- It sends ARP requests for many addresses within the local subnet.
- The transcript’s shortened `10.6.101` refers to `10.6.1.101`, as clarified later in the lesson.

### Step 3: Identify ARP Responders

- Filter replies to identify addresses represented in the captured ARP responses.

1. Apply `arp.opcode == 2`.
2. Review the sender addresses in the replies.
3. Sort the **Info** column if useful.
4. Count distinct responding addresses rather than counting every reply packet.

#### Observed Addresses

- `10.6.1.1`
- `10.6.1.6`
- `10.6.1.101`

#### Interpretation

- Three addresses appear in ARP replies across the capture.
- Because the filter includes all ARP replies, including those from the scanner itself, correlate requests and replies before treating them all as responses to its sweep.

### Step 4: Examine the ICMP Sweep

- Identify the infected device’s later attempts to discover hosts using ping.

1. Replace the ARP filter with `icmp`.
2. Look near packet `60284`.
3. Review requests from `10.6.1.101` toward addresses in the `10.0.0.x` range.
4. Compare the destination addresses in packet order.

#### Scan Order

- The scan begins with `.1`, jumps to `.31`, then counts backward through `.30`, `.29`, and onward to `.2`.
- It next probes `.0`, then proceeds upward from `.32`.
- The scan is partly sequential but does not follow one continuous ascending sequence.

### Step 5: Review IPv4 Conversations

- Use conversation statistics to inspect the direction and packet count of the probes.

1. Keep the ICMP filter applied.
2. Open **Statistics → Conversations**.
3. Select **IPv4**.
4. Enable **Limit to display filter** if needed.
5. Review the addresses and directional packet counts.

#### Finding

- Many conversations contain only one packet sent by the infected device.
- In the lab’s displayed ordering, those probes appear under **Packets B → A**.
- Endpoint A or B does not automatically identify the attacker; check the actual addresses.

#### Limitation

- One packet indicates no reply within that displayed conversation.
- Packet counts alone cannot conclusively identify Echo Replies; verify the ICMP message type.

### Step 6: Filter for Ping Replies

- Find ICMP Echo Replies and determine whether they were sent to the infected host.

1. Close the Conversations window.
2. Select an Echo Reply packet.
3. Expand **Internet Control Message Protocol**.
4. Right-click **Type: 0**.
5. Select **Prepare as Filter → Selected**.
6. Apply `icmp.type == 0`.

#### Focused Reply Filter

- Use `icmp.type == 0 && ip.dst == 10.6.1.101` to show only Echo Replies addressed to the infected device.

#### Findings

- `10.6.1.1` and `10.6.1.6` returned Echo Replies.
- These replies demonstrate responsive hosts in the `10.6.1.x` network; they do not establish that the later `10.0.0.x` sweep received replies.

### Key Filters

- All ARP traffic: `arp`
- ARP replies: `arp.opcode == 2`
- All ICMP traffic: `icmp`
- ICMP Echo Replies: `icmp.type == 0`
- Echo Replies to the infected host: `icmp.type == 0 && ip.dst == 10.6.1.101`

### Key Takeaway

- The infected host `10.6.1.101` performs ARP discovery followed by ICMP probing. Use reply-specific filters and conversation direction to distinguish attempted discovery from confirmed responses.

</details>

<details>
<summary><strong>Lab 3 – Part 2 – Analyzing Malware for Network and Port Scans</strong></summary>

## Lab 3 – Part 2 – Analyzing Malware for Network and Port Scans

- Examine the infected machine’s TCP connections to discovered hosts and identify which services accepted connection attempts.

### Required Capture

- Continue using `AnalyzinganAttack.pcapng`.
- Infected device: `10.6.1.101`.
- Previously discovered hosts: `10.6.1.1` and `10.6.1.6`.

### Step 1: Examine Connections to 10.6.1.1

- Determine whether the infected device successfully contacted the first discovered host.

1. Clear the previous filter.
2. Apply `tcp && ip.addr == 10.6.1.1`.
3. Review the destination ports and connection responses.

#### Finding

- The infected device makes several attempts to connect to TCP port `445`.
- The attempts fail, and the lesson shows no further connection attempts to this host.

#### More Precise Filter

- Use `tcp && ip.addr == 10.6.1.101 && ip.addr == 10.6.1.1` to isolate TCP traffic specifically between these two devices.

### Step 2: Examine Connections to 10.6.1.6

- Investigate the second host, which shows substantially more communication.

1. Apply `tcp && ip.addr == 10.6.1.101 && ip.addr == 10.6.1.6`.
2. Return to the beginning of the filtered packets.
3. Review the protocols and service ports.
4. Look for Kerberos, LDAP, SMB, and DCE/RPC activity.

#### Finding

- The infected device communicates with several services on `10.6.1.6`, indicating more activity than the unsuccessful attempts against `10.6.1.1`.

### Step 3: Review Filtered Conversations

- Use conversation statistics to summarize communication between the infected device and the second host.

1. Keep the two-host TCP filter active.
2. Open **Statistics → Conversations**.
3. Select the **TCP** tab.
4. Enable **Limit to display filter**.
5. Sort by **Port B**.
6. Review the ports and number of conversations.

#### Important Setting

- **Limit to display filter** restricts the statistics to matching traffic. Without it, conversations from the entire capture may appear.

#### Observed Service Ports

- `88` – Kerberos
- `135` – Microsoft RPC Endpoint Mapper
- `389` – LDAP
- `445` – SMB

#### Protocol Clarification

- LDAP normally uses port `389`, not `135`.
- These services are consistent with Windows-domain communication, but their presence alone does not prove exploitation.

### Step 4: Identify Open Ports on 10.6.1.6

- Find SYN-ACK responses from the target back to the infected device.

1. Select the saved **TCP → Open Ports** filter, if available.
2. Keep both SYN and ACK conditions enabled.
3. Set the source address to `10.6.1.6`.
4. Set the destination address to `10.6.1.101`.
5. Apply the completed filter.

#### Open-Port Response Filter

- Use `tcp.flags.syn == 1 && tcp.flags.ack == 1 && ip.src == 10.6.1.6 && ip.dst == 10.6.1.101`.

#### Reading the Results

- The **source IP** identifies the responding host.
- The **TCP source port** identifies the port that appears open.
- A SYN-ACK shows that the target accepted the initial connection request; it does not prove that the handshake completed or that exploitation succeeded.

### Investigation Findings

- The infected host performed ARP discovery and ICMP probing, including probes of another subnet.
- Connection attempts to port `445` on `10.6.1.1` failed.
- Communication with `10.6.1.6` involved several Windows-related services.
- SYN-ACK filtering identifies which target ports responded as open.

### Continuing the Investigation

- Retain this capture because later modules examine the malware’s activity in greater detail.
- This section establishes discovery and connection behavior; further analysis is needed to determine what happened within those sessions.

</details>

<details>
<summary><strong>How OS Fingerprinting Works</strong></summary>

## How OS Fingerprinting Works

- OS fingerprinting estimates a target’s operating system by examining characteristics of its network traffic and responses.

### Why Attackers Use It

- Identifying an operating system helps attackers narrow down vulnerabilities and potential exploits.

### Active Fingerprinting

- Tools such as Nmap send specially crafted TCP, ICMP, or other probes and compare the responses with known operating-system patterns.

### TCP/IP Stack Differences

- Operating systems and their configurations can differ in how they construct packets and respond to unusual requests.

#### Fingerprinting Clues

- IP Time to Live (TTL)
- Initial TCP receive-window size
- TCP initial sequence-number patterns
- TCP flags
- TCP options and their ordering
- Responses to unusual TCP or ICMP probes

## Indicators to Investigate

### Unexpected Host-to-Host Traffic

- Direct communication between user workstations may deserve investigation when those devices normally communicate only with servers or internet services.
- Compare the activity with normal network behavior because legitimate peer-to-peer communication also occurs.

### Unusual TTL Patterns

- TTL is an IP-header field that decreases as packets pass through routers.
- Unexpected TTL values or changes may suggest crafted probes.

#### Interpretation Limitation

- Observed TTL reflects both the sender’s initial value and the network path; it does not independently reveal the exact distance traveled or operating system.

### Repeated Initial TCP Sequence Numbers

- Identical initial sequence numbers across separate SYN connection attempts may indicate a scanning tool.

#### Important Distinction

- Retransmissions of the same SYN normally reuse its sequence number.
- Wireshark’s relative sequence numbering can display initial SYNs as `0`; compare raw sequence numbers when investigating this pattern.

### Unusual TCP Flags

- Crafted flag combinations test how a target or filtering device responds.

#### Xmas Scan

- Typically sets FIN, PSH, and URG together; it does not require every TCP flag to be enabled.

#### Null Scan

- Sends TCP packets with no control flags enabled.

#### ACK Scan

- Sends ACK probes to examine filtering behavior. An ACK packet alone is normal; its connection context determines whether it is suspicious.

### Few or Unusual TCP Options

- Minimal options or unexpected combinations may suggest packets generated by a scanning tool.

#### Common SYN Options

- **Maximum Segment Size (MSS):** Advertises the maximum TCP payload size the sender can receive per segment.
- **Window Scale:** Supports larger receive windows.
- **SACK Permitted:** Indicates support for selective acknowledgments.
- **Timestamps:** May support timing measurements and protection against old duplicate segments.

#### Important Clarification

- A SYN is not required to contain at least three options. Legitimate systems may omit options.
- Scanning tools may omit or deliberately vary options because their purpose is to test responses rather than establish ordinary application sessions.

### Evaluating the Evidence

- Combine unexpected communication, TTL patterns, raw sequence numbers, flags, and TCP options.
- No single characteristic proves an operating system or malicious intent; use the overall pattern and network context.

</details>

<details>
<summary><strong>Lab 4 – Detecting OS Fingerprinting with Wireshark</strong></summary>

## Lab 4 – Detecting OS Fingerprinting with Wireshark

- Identify crafted Nmap probes by comparing target responses, TCP window sizes, and unusual IP Time to Live values.

### Lab Scenario

- A station ending in `56.102` runs an Nmap fingerprinting scan against other devices.
- Addresses below are abbreviated as spoken in the lesson; use the complete addresses shown in the capture.

### How Fingerprinting Works

- Nmap sends specially crafted packets and compares the responses with a fingerprint database to estimate the target’s operating system.

### Step 1: Review the Nmap Results

- Examine the scan output stored in the capture comments.

1. Open the Lab 4 capture.
2. Select **Statistics → Capture File Properties**.
3. Read the description and embedded Nmap output.

#### Reported Findings

- `56.100`: No scanned ports were open.
- `56.101`: Nmap reports `977` closed ports and lists several open services.
- The output identifies the second target as likely running Linux `2.6.x`; fingerprinting results are estimates.

### Step 2: Hide the ARP Scan

- Remove the initial ARP traffic to focus on fingerprinting probes.

1. Move to approximately packet `1000`.
2. Apply the filter `!arp`.
3. Review the remaining TCP and ICMP traffic.

#### Initial Observations

- The scanner sends SYN probes to TCP port `113` on multiple destinations.
- The SYN packets advertise a receive window of `1024` bytes.

#### Interpretation

- A `1024`-byte window is a clue used in this lab, not a unique or conclusive Nmap signature.

### Step 3: Compare Target Responses

- Different responses to similar probes can help distinguish network-stack behavior.

1. Right-click the first SYN sent to `56.100`.
2. Select **Conversation Filter → TCP**.
3. Review the associated response.
4. Clear the filter.
5. Locate packet `1034` and repeat the conversation filtering.
6. Compare the responses.

#### First Response

- The lesson shows an **ICMP Destination Unreachable – Protocol Unreachable** response for the first target.

#### Second Response

- The other target returns a TCP reset, demonstrating different behavior.

#### Investigation Reminder

- Responses may also be influenced by firewalls or intermediate devices, so they do not identify an operating system by themselves.
- If the TCP conversation filter hides an ICMP error, clear it and inspect the related ICMP packet separately.

### Step 4: Add a TTL Column

- Display TTL values directly in the packet list to make variations easier to compare.

1. Select packet `1033`.
2. Expand **Internet Protocol Version 4**.
3. Locate **Time to Live**.
4. Right-click it.
5. Select **Apply as Column**.

### Understanding TTL

- In practice, IPv4 TTL limits how many routed hops a packet can traverse.
- Each router or Layer 3 forwarding device reduces the value.
- When the value reaches zero, the packet is discarded, preventing it from circulating indefinitely in a routing loop.

### Step 5: Compare TTL Values

- Look for inconsistent TTLs in packets sent by the same source over the same path.

1. Review the new TTL column.
2. Compare packets from the scanner to the targets.
3. Note values such as `37`, `45`, `46`, and `59`.

#### Finding

- The lab shows widely varying TTL values from the same source communicating with local targets.
- This supports the interpretation that the packets were deliberately crafted.

#### Important Limitation

- A low TTL alone is not evidence of an attack.
- The observed value depends on the sender’s initial TTL and the network path; it does not reveal an exact hop count unless the initial value is known.

### Step 6: Filter for the Lab’s TTL Range

- Isolate packets whose TTL is greater than `30` and less than `50`.

1. Enter `ip.ttl > 30 && ip.ttl < 50`.
2. Apply the filter.
3. Review the matching sources, destinations, and probe behavior.

#### Filter Scope

- The filter matches TTL values `31–49`.
- This is an investigation aid for the lab, not a universal malicious-traffic signature.

### Step 7: Save the Filter

- Store the TTL filter for future investigations.

1. Keep the completed filter in the display filter bar.
2. Click **+**.
3. Enter `Signatures//Strange TTLs` as the label.
4. Save the filter.

#### Reuse

- Select **Signatures → Strange TTLs** to apply it again.
- Adjust the range to suit the network being investigated.

### Key Findings

- The capture starts with ARP discovery, followed by TCP probing.
- Targets respond differently, including ICMP errors and TCP resets.
- SYN window sizes and varying TTLs provide additional fingerprinting clues.
- Evaluate these characteristics together with the scan pattern rather than treating any single value as proof.

</details>

<details>
<summary><strong>Lab 4 – Part 2 – Detecting OS Fingerprinting</strong></summary>

## Lab 4 – Part 2 – Detecting OS Fingerprinting

- Examine SYN-ACK responses, unusual TCP flags, and missing TCP options to identify possible OS-fingerprinting probes.

### Lab Devices

- Scanner: `192.168.56.102`
- Target: `192.168.56.101`
- Continue using the OS-fingerprinting capture from Part 1.

### Step 1: Filter the Target’s SYN-ACK Responses

- Identify responses from the target’s potentially open ports.

1. Select the saved **TCP → Open Ports** filter.
2. Change the source address to `192.168.56.101`.
3. Apply `tcp.flags.syn == 1 && tcp.flags.ack == 1 && ip.src == 192.168.56.101`.

#### Result

- The lab displays `31` matching packets.
- These are response packets, not necessarily 31 different open ports.

### Step 2: Inspect Fingerprinting Clues

- Nmap examines several header characteristics together to estimate the target’s operating system.

1. Select a SYN-ACK packet.
2. Expand **Internet Protocol Version 4**.
3. Review **Identification** and **Time to Live**.
4. Expand **Transmission Control Protocol**.
5. Compare sequence numbers and TCP options across responses.

#### Observed Behavior

- The original SYN advertises only Maximum Segment Size (MSS).
- The target’s SYN-ACK also contains only MSS.

#### Sequence-Number Clarification

- Sequence-number behavior can contribute to fingerprinting, but a similar numeric range alone does not identify an operating system.
- Use raw sequence numbers when comparing initial values; Wireshark’s relative numbering may display them as `0`.

### Step 3: Find the Unusual Multi-Flag Probe

- Locate packets with URG, PSH, SYN, and FIN enabled simultaneously.

1. Clear the previous filter.
2. Go to packet `7125`.
3. Expand **Transmission Control Protocol → Flags**.
4. Right-click the parent **Flags** field.
5. Select **Prepare as Filter → Selected**.
6. Apply `tcp.flags == 0x02b`.

#### Flag Combination

- URG – Urgent
- PSH – Push
- SYN – Synchronize
- FIN – Finish

#### Xmas Scan Distinction

- The lesson calls this an Xmas scan, but this probe includes SYN.
- A conventional Xmas probe uses FIN, PSH, and URG: `tcp.flags == 0x029`.
- SYN and FIN together are unusual because they signal connection establishment and termination in the same packet.

### Step 4: Examine the Response to Packet 7125

- Follow the exchange to see how the target handles the crafted flags.

1. Right-click packet `7125`.
2. Select **Conversation Filter → TCP**.
3. Review the probe, response, and reset.

#### Observed Exchange

- The scanner sends the URG/PSH/SYN/FIN probe.
- The target replies from port `21`, commonly FTP, with a SYN-ACK.
- The scanner sends a reset to end the connection attempt.
- Nmap records this behavior as a fingerprinting clue.

### Step 5: Identify a Null Probe

- A Null probe has no TCP control flags enabled.

1. Clear the conversation filter.
2. Go to packet `7121`.
3. Expand **Transmission Control Protocol → Flags**.
4. Confirm that no flags are set.
5. Right-click **Flags → Prepare as Filter → Selected**.
6. Apply `tcp.flags == 0x000`.

### Step 6: Check the Null Probe’s Response

- Determine whether the target responds to the flagless packet.

1. Right-click packet `7121`.
2. Select **Conversation Filter → TCP**.
3. Examine the conversation for responses.

#### Result

- No response is visible in the lab.
- Silence is useful context but does not independently prove an operating system or distinguish an open port from filtering.

### Step 7: Examine SYNs Without MSS

- Inspect SYN packets that omit the Maximum Segment Size option.

1. Select the course’s saved **TCP → SYNs with no MSS** filter.
2. Select the SYN targeting port `445`.
3. Expand **Transmission Control Protocol → Options**.
4. Review the listed options.

#### Observed Options

- SACK Permitted
- Timestamps
- Window Scale
- End of Option List
- No MSS option

#### Interpretation

- Missing MSS is not automatically invalid or malicious.
- Combined with the other crafted probes, it supports investigating possible OS fingerprinting.

### Step 8: Save the Filters

- Group useful filters under the TCP menu for future investigations.

1. Enter the desired filter.
2. Click **+**.
3. Enter a descriptive label beginning with `TCP//`.
4. Save the filter.

#### Suggested Saved Filters

- `TCP//SYN FIN PSH URG`: `tcp.flags == 0x02b`
- `TCP//Null Scan`: `tcp.flags == 0x000`
- `TCP//Xmas Scan`: `tcp.flags == 0x029`

### Main Finding

- The scanner deliberately varies flags and TCP options, then compares replies and silence. These responses, combined with IP and TCP header characteristics, help estimate the target’s operating system.

</details>

<details>
<summary><strong>How HTTP Path Enumeration Works</strong></summary>

## How HTTP Path Enumeration Works

- HTTP path enumeration probes a web server for files and directories, including paths that are not linked from its main pages.

### Attacker’s Objective

- Discover accessible resources, hidden application pages, and potentially vulnerable functionality.
- Attackers send many requests containing guessed paths and examine the server’s responses.

### Possible Follow-On Activity

- Discovered vulnerabilities may allow an attacker to upload a malicious script and establish a web shell.
- A web shell can provide remote command execution under the web application’s permissions; it does not automatically grant root or administrator access.

## Visibility in Packet Captures

### Unencrypted HTTP

- HTTP commonly uses port `80`, allowing Wireshark to inspect requested paths and response codes when the traffic is captured.

### Encrypted HTTPS

- Enumeration can also occur over HTTPS, commonly on port `443`.
- Encryption hides request paths and response contents unless the traffic is decrypted.
- Request-level investigation may therefore require matching TLS session secrets or web-server logs.

## Indicators to Investigate

### Numerous Guessed Paths

- One source requests many different files or directories within a short period.
- Requests resemble automated guessing rather than ordinary navigation through the application.
- Paths may follow a wordlist or appear unrelated; they do not have to be random.

### Repeated HTTP 404 Responses

- `404 Not Found` indicates that the requested resource was not found.
- A few 404 responses are normal, but a spike associated with many guessed paths can indicate enumeration.

### Unexpected HTTP Activity

- Unencrypted requests may stand out in an environment where web applications normally use HTTPS.
- Port `80` traffic alone does not establish malicious activity.

## Investigation Focus

- Identify the requesting source and targeted web server.
- Compare the request rate and path pattern with normal activity.
- Review response codes to distinguish unsuccessful guesses from potentially accessible resources.
- Correlate suspicious requests with server logs and subsequent uploads or command activity.

### Important Reminder

- Broken links, automated crawlers, and authorized security testing can produce similar patterns. Evaluate the source, timing, and purpose before classifying the activity as an attack.

</details>

<details>
<summary><strong>Lab 5 – Analyzing HTTP Path Enumeration with Wireshark</strong></summary>

## Lab 5 – Analyzing HTTP Path Enumeration with Wireshark

- Analyze a dictionary-based web scan by examining HTTP GET requests, response codes, and the User-Agent header.

### Lab Capture

- Open the Lab 5 web-server enumeration capture.
- The traffic uses unencrypted HTTP over port `80`, allowing inspection of requested paths, headers, and responses.

### Step 1: Read the Lab Questions

- Determine what the scanner requested and which requests succeeded.

1. Open **Statistics → Capture File Properties**.
2. Read the capture comments.
3. Investigate how many paths were requested, which were available, and which response codes appeared.

### Step 2: Inspect the Initial Request

- Examine packet `4` to identify the requested resource and scanning tool.

1. Select packet `4`.
2. Expand **Hypertext Transfer Protocol**.
3. Review **Request Method**, **Request URI**, **Host**, and **User-Agent**.

#### Observations

- Method: `GET`
- URI: `/`, requesting the root resource.
- Host: Identifies the target web server.
- User-Agent: Contains `gobuster`, the tool used for enumeration.

### Step 3: Filter HTTP GET Requests

- Display the paths that the scanner attempted to access.

1. Right-click **Request Method**.
2. Select **Prepare as Filter → Selected**.
3. Apply `http.request.method == "GET"`.
4. Review the requested paths in the **Info** column.
5. Check the displayed packet count.

#### Lab Result

- The lesson reports `607` GET requests, with different paths being tested.
- Examples include `/index`, `/crack`, `/serial`, `/warez`, `/full`, `/news`, and `/images`.

#### Interpretation

- Requests for many guessed paths indicate dictionary-based enumeration.
- A URI does not necessarily identify a physical directory; it may refer to a file or application route.

### Step 4: Identify Successful Responses

- Find requests that received HTTP `200 OK`.

1. Right-click the initial GET packet.
2. Select **Conversation Filter → TCP**.
3. Locate the corresponding **HTTP/1.1 200 OK** response.
4. Expand its HTTP details.
5. Right-click **Status Code → Prepare as Filter → Selected**.
6. Apply `http.response.code == 200`.

#### Lab Result

- There are `2` responses with status `200`.
- They correspond to `/` and `/index`.

#### Verify Each Request

- Right-click each successful response and select **Conversation Filter → TCP** to inspect its associated request.
- A `200` response indicates successful HTTP handling, but does not by itself confirm a physical directory or vulnerability.

### Step 5: Count Not-Found Responses

- Identify unsuccessful guesses using HTTP `404 Not Found`.

1. Apply `http.response.code == 404`.
2. Review the displayed packet count.
3. Compare the responses with the requested paths.

#### Lab Result

- The lesson reports `595` responses with status `404`.
- A large number of missing resources supports the enumeration finding.

#### Count Discrepancy

- `2` successful responses plus `595` not-found responses total `597`, not `607`.
- The transcript does not explain the remaining difference. Inspect all HTTP responses and request-response links before concluding that every other request returned `404`.

### Step 6: Create a Gobuster Signature Filter

- Match the tool name without requiring an exact User-Agent version.

1. Clear the current filter.
2. Return to the GET request in packet `4`.
3. Expand HTTP and locate **User-Agent**.
4. Right-click **Prepare as Filter → Selected**.
5. Replace the exact comparison with `http.user_agent contains "gobuster"`.
6. Apply the filter.

#### Why Use Contains?

- It matches the lowercase text `gobuster` even when other text or version numbers appear before or after it.
- It will not identify every Gobuster scan because User-Agent values can be changed or omitted.

### Step 7: Save the Signature

- Store the filter for future investigations.

1. Keep the Gobuster filter in the display filter bar.
2. Click **+**.
3. Enter `Signatures//Gobuster` as the label.
4. Save it.

### Compare with Normal Browser Traffic

- Examine the User-Agent in visible HTTP traffic from a browser and compare it with the lab’s scanner header.
- Unusual User-Agents are clues, not proof: legitimate tools can use custom headers, and attackers can imitate browsers.

### Main Findings

- The scanner sends many guessed HTTP paths using a Gobuster User-Agent.
- `/` and `/index` receive `200 OK`.
- Numerous `404` responses indicate unsuccessful guesses.
- Evaluate the request pattern, response codes, source, and authorization together when identifying suspicious enumeration.

</details>
