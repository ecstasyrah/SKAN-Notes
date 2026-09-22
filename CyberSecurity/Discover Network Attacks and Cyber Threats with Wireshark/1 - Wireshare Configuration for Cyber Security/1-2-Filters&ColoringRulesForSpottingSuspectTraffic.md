<details>
<summary><strong>Analyzing Unusual DNS Activity</strong></summary>

## Analyzing Unusual DNS Activity
- Identify suspicious DNS behavior in Wireshark that may indicate malware communication or data exfiltration.

### Malware and DNS
- Malware may use DNS traffic to determine the infected computer’s location, communicate with external servers, or secretly transfer data.

### External IP Discovery
- Malware may connect to services such as `whatismyip.com` to discover the victim’s public IP address and approximate location.

### Normal DNS Behavior
- A computer normally sends DNS requests to its configured local or organizational DNS server.

#### Typical Communication
- A standard DNS lookup usually consists of a small request and response, often requiring only two or three packets.

### Suspicious External DNS
- Direct DNS communication with an unexpected external server may be suspicious if the computer is expected to use only its local DNS server.

#### Important Reminder
- External DNS traffic is not automatically malicious. It should be compared with the network’s normal configuration and behavior.

### DNS Tunneling
- Malware can disguise commands or stolen data as DNS queries and responses sent to an attacker-controlled server.

#### Why It May Work
- Some firewalls allow DNS traffic without deeply inspecting its contents, making DNS a possible channel for bypassing security controls.

### Indicators of Unusual DNS Activity
- Look for DNS traffic sent directly to unexpected external servers.
- Look for unusually large DNS queries or responses.
- Look for a high number of DNS packets between the same two systems.
- Look for repeated requests to unusual or randomly generated domain names.
- Look for long or encoded-looking values inside DNS queries.
- Look for a user device transmitting more DNS data than expected.

### Investigation Approach
- Establish the device’s normal DNS server and typical traffic pattern before deciding whether activity is suspicious.

### Key Takeaway
- Unexpected DNS servers, excessive requests, large packets, and encoded-looking queries may indicate malware or DNS tunneling, but they require further investigation before being classified as malicious.

</details>

<details>
<summary><strong>Lab 7 – Part 1 – Filtering for Unusual DNS Activity</strong></summary>

## Lab 7 – Part 1 – Filtering for Unusual DNS Activity
- Use Wireshark filters, statistics, custom columns, and packet payloads to identify possible DNS tunneling or data exfiltration.

### Lab Focus
- Focus on the investigation techniques because DNS-based attacks can appear in many different forms.

### Step 1: View the Lab Questions
- Use the capture file comments as a guide for the investigation.

1. Open the Lab 7 PCAP in Wireshark.
2. Select **Statistics**.
3. Select **Capture File Properties**.
4. Read the questions under **Capture file comments**.
5. Attempt the questions before reviewing the answers.

### Investigation Questions
1. How many DNS conversations do you see in this pcap? 
    - Open Statistics > Conversations - check the UDP conversations (there are 997)
    - multiple dns conversaations from a specific IP address which makes it suspicious
2. How many DNS responses do you see?
    - check the DNS response on a specific conversation > its zero so it is a query not a respons
    - 1 = response
    - to filter right click response > apply as filter > selected
    - There are no response from this filter so it is weird
3. Do any of the responses have more than 5 answers? 
    - No official dns responses
    - Look for "Answer RRs" > select as filter just like with the dns response and it should be > 0 to know if there are packets that has more than 0 answered RRs Resource Records; This makes it weird if there are too much RRs
    - Healthy response is more or less 10 RRs 
    - To investigate suspicious behavior the filter should be dns.count.answers > 10
Can you set and save a filter for this activity?
    - To set up the filter there could be different types of filter for DNS so just add "//" after "DNS"
    - Example: DNS // High answers count
4. What is the packet with the most Answer RR's?
    - Right click "Answer RRs" > add it as a column then sort it. Check the lowest
5. There seems like alot of malformed DNS. Is the client trying to send data out to another device? How can we tell?
    - Sending out data or data are exfiltrated from a client to another 
### Step 2: Count the DNS Conversations
- Review the UDP conversations to determine the volume and direction of the DNS traffic.

1. Select **Statistics**.
2. Select **Conversations**.
3. Open the **UDP** tab.
4. Review the total number of conversations.
5. Examine the values under **Port B**.

#### Result
- The capture contains `997` UDP conversations.
- All conversations use port `53` because the capture contains filtered DNS traffic.
- Most of the traffic comes from one workstation and is sent to another DNS server.

#### Finding
- Nearly 1,000 DNS conversations between a small number of systems is unusual and requires further investigation.

### Step 3: Filter for DNS Responses
- Use the DNS response flag to identify packets officially marked as responses.

1. Select a DNS packet, such as packet `17`.
2. Expand **Domain Name System**.
3. Expand **Flags**.
4. Locate **Response** or `dns.flags.response`.
5. Right-click the field.
6. Select **Prepare as Filter**.
7. Select **Selected**.
8. Change the value from `0` to `1`.
9. Apply the filter `dns.flags.response == 1`.

#### Flag Values
- `0` means the packet is a DNS query.
- `1` means the packet is a DNS response.

#### Result
- No packets match the filter, meaning the capture contains no packets officially marked as DNS responses.

#### Important Reminder
- Traffic returning from a server is not necessarily a valid DNS response. Confirm it using the DNS response flag.

### Step 4: Find Packets with Answer Records
- Search for packets that claim to contain DNS answer resource records.

1. Clear the current display filter.
2. Select a DNS packet.
3. Expand **Domain Name System**.
4. Locate **Answer RRs** or `dns.count.answers`.
5. Right-click the field.
6. Select **Prepare as Filter**.
7. Select **Selected**.
8. Change the comparison to greater than `0`.
9. Apply the filter `dns.count.answers > 0`.

#### Result
- Some packets contain alleged answer records even though they are marked as DNS queries.
- Packet `18` is a query that claims to contain `29,285` answer resource records.

#### Finding
- A client query containing thousands of answer records is abnormal and suggests malformed or disguised traffic.

### Step 5: Filter for More Than Five Answers
- Identify packets containing an unusually high number of answer records.

1. Enter the filter `dns.count.answers > 5`.
2. Apply the filter.
3. Review the matching packets.

#### Optional Higher Threshold
- Use `dns.count.answers > 10` to focus on more extreme answer counts.

#### Normal Comparison
- A legitimate DNS response commonly contains only a small number of answers, often approximately one to five.

### Step 6: Save the DNS Filter
- Save the filter as a button so it can be reused quickly.

1. Enter the desired filter in the display filter bar.
2. Click the **+** button.
3. Enter a label beginning with `dns//`.
4. Add a descriptive filter name.
5. Save the filter.

#### Example Label
- Use `dns//High Answer Count` as the label.

#### Filter Organization
- The `dns//` prefix groups related filters under a DNS menu instead of creating a separate button for every filter.

### Step 7: Find the Highest Answer Count
- Add the answer count as a column and sort it to locate the largest value.

1. Select a packet containing **Answer RRs**.
2. Right-click `dns.count.answers`.
3. Select **Apply as Column**.
4. Sort the new column from lowest to highest.
5. Examine the packet with the largest value.

#### Result
- Packet `552` contains the highest alleged count with `40,600` answer resource records.

#### Finding
- This value is not realistic for normal DNS traffic and strongly suggests malformed or disguised data.

### Step 8: Inspect the Packet Payload
- Examine the packet bytes to determine whether DNS is carrying other information.

1. Clear the display filter.
2. Sort the packets by frame number.
3. Return to the beginning of the capture.
4. Review packets marked **Malformed Packet**.
5. Select suspicious packets.
6. Examine the hexadecimal and ASCII views.
7. Compare the payloads across consecutive packets.

#### Result
- Readable text appears in the ASCII view and increases across consecutive packets.
- One visible value appears to contain `martianlol.com`.
- Wireshark repeatedly identifies the traffic as malformed.

### Likely Activity
- DNS appears to be carrying data from the client to another system.

#### Possible DNS Exfiltration
- Information may be leaving the device through port `53`, which may indicate DNS tunneling.

### Why It May Bypass Detection
- A firewall may only see a client communicating with a server through port `53` using reasonably sized packets.

#### Deeper Inspection
- The unusual flags, answer counts, malformed-packet warnings, and readable payload reveal that the traffic does not behave like normal DNS.

### Key Findings
- The capture contains `997` UDP conversations using port `53`.
- No packets are officially marked as DNS responses.
- Packet `18` claims to contain `29,285` answer records.
- Packet `552` contains the highest alleged count at `40,600`.
- Multiple packets are marked as malformed.
- Readable data appears inside the DNS payload.
- The traffic likely demonstrates DNS tunneling or data exfiltration.

### Key Takeaway
- High DNS volume, missing response flags, impossible answer counts, malformed packets, and readable payload data are strong indicators of suspicious DNS activity.

</details>

<details>
<summary><strong>Lab 7 – Part 2 – Filtering for Unusual DNS Activity</strong></summary>

## Lab 7 – Part 2 – Filtering for Unusual DNS Activity
- Create and save Wireshark filters that detect external DNS queries and unusually large DNS responses.

### Filter 1: External DNS Requests
- Display DNS queries sent to destinations outside the approved local DNS network.

#### Example Network
- Assume all approved local DNS servers are located within the `10.0.0.0/24` subnet.

#### Step 1: Filter for DNS Queries
- Use the DNS response flag to display query packets.

1. Enter `dns.flags.response == 0`.
2. Remember that a response flag of `0` means the packet is a DNS query.

#### Step 2: Exclude the Local DNS Network
- Add a condition that removes queries sent to the approved DNS subnet.

1. Add `ip.dst == 10.0.0.0/24` to represent the local DNS network.
2. Place the condition inside parentheses.
3. Apply the `!` operator to exclude matching destinations.
4. Combine both conditions using `&&`.

#### Complete Filter
- Use `dns.flags.response == 0 && !(ip.dst == 10.0.0.0/24)`.

#### Result
- The filter displays DNS queries sent to devices outside the approved local DNS subnet, including possible external DNS resolvers.

#### Customization
- Replace `10.0.0.0/24` with the actual IP address or subnet containing the organization’s approved DNS servers.

### Step 3: Save the External DNS Filter
- Save the filter under the DNS drop-down menu for quick access.

1. Enter the completed filter in the display filter bar.
2. Click the **+** button.
3. Enter `dns//External DNS Requests` as the label.
4. Save the filter.

#### Filter Organization
- The `dns//` prefix places the saved filter inside the existing DNS filter menu.

### Filter 2: Large DNS Responses
- Display DNS responses with an IP packet length greater than 500 bytes.

#### Step 1: Identify DNS Responses
- Set the DNS response flag to `1`.

1. Enter `dns.flags.response == 1`.
2. Remember that a response flag of `1` means the packet is a DNS response.

#### Step 2: Add the Packet-Length Condition
- Limit the results to IPv4 packets larger than 500 bytes.

1. Add the condition `ip.len > 500`.
2. Combine both conditions using `&&`.

#### Complete Filter
- Use `dns.flags.response == 1 && ip.len > 500`.

#### Important Correction
- The correct Wireshark IPv4 field is `ip.len`, not `ip.length`.

#### Result
- The filter displays DNS responses carried in IPv4 packets larger than 500 bytes.

#### Finding
- No packets in this specific capture match the filter, but it can be saved and used in future investigations.

#### Investigation Reminder
- A large DNS response is not automatically malicious. It should be examined to confirm whether its size and contents are legitimate.

### Step 3: Save the Large Response Filter
- Save the filter under the DNS drop-down menu.

1. Enter the completed filter in the display filter bar.
2. Click the **+** button.
3. Enter `dns//Large DNS Responses` as the label.
4. Save the filter.

### Saved DNS Filters
- `dns//External DNS Requests` identifies queries sent outside the approved DNS network.
- `dns//Large DNS Responses` identifies DNS responses larger than 500 bytes.

### Key Takeaway
- External DNS queries and unusually large DNS responses can indicate suspicious activity. Saving these filters under the `dns//` menu makes them easier to reuse during future Wireshark investigations.

</details>

<details>
<summary><strong>Lab 8 – Filtering for Traffic Based on Country Location</strong></summary>

## Lab 8 – Filtering for Traffic Based on Country Location
- Use Wireshark GeoIP data to identify and filter network traffic based on its country of origin or destination.

### Required Capture File
- Open the **Lab 3 and 4 – GeoIP and Columns PCAP** file.

### Investigation Scenario
- The organization normally communicates with systems in the United States and several European countries.

#### Expected Countries
- Common traffic includes the United States, France, Canada, Ireland, and Germany.

#### Unusual Country
- Traffic involving Russia is unusual for this organization and should be investigated further.

#### Important Reminder
- Traffic from a particular country is not automatically malicious. Compare it with the organization’s normal business locations and communication patterns.

### Step 1: Review Countries in the Capture
- Use the Endpoints window to see the countries communicating within the PCAP.

1. Open the PCAP in Wireshark.
2. Select **Statistics**.
3. Select **Endpoints**.
4. Open the appropriate endpoint tab.
5. Review the available country information.
6. Look for countries outside the organization’s normal locations.

### Step 2: Apply a Country Filter from Endpoints
- Create a filter directly from a selected country in the Endpoints window.

1. Locate the unusual country.
2. Right-click the country entry.
3. Select **Apply as Filter**.
4. Select **Selected**.
5. Close the Endpoints window.

#### Result
- Wireshark displays only the traffic associated with the selected country.

### Step 3: Inspect the GeoIP Information
- Review the packet details to confirm the country and its GeoIP information.

1. Select one of the filtered packets.
2. Expand the **Internet Protocol** details.
3. Expand **Destination GeoIP**.
4. Locate the destination country and country code.

### Step 4: Prepare a Destination-Country Filter
- Create a reusable filter using the destination country field.

1. Right-click the destination country field.
2. Select **Prepare as Filter**.
3. Select **Selected**.
4. Review the generated filter in the display filter bar.
5. Apply the filter.

#### Example Filter
- Use `ip.geoip.dst_country == "Russia"` to display IPv4 traffic with Russia as its GeoIP destination.

#### Important Correction
- The correct field name is `ip.geoip.dst_country`. The underscore should not contain a Markdown escape character.

### Step 5: Save the Country Filter
- Save the filter under a country-based drop-down menu for quick access.

1. Enter the completed filter in the display filter bar.
2. Click the **+** button.
3. Enter `Country//Russia` as the label.
4. Save the filter.

#### Filter Organization
- The `Country//` prefix creates a drop-down menu containing saved filters for different countries.

### Filter Limitation
- `ip.geoip.dst_country` filters only by the destination country.

#### Source-Country Traffic
- Use the corresponding source GeoIP country field when you need to examine traffic originating from a particular country.

### Key Takeaway
- GeoIP filters help identify traffic involving unexpected countries. Always evaluate the results using the organization’s normal business activity before deciding that the traffic is suspicious.

</details>

<details>
<summary><strong>Analyzing Suspect TCP Behavior and Flags</strong></summary>

## Analyzing Suspect TCP Behavior and Flags
- Examine TCP SYN activity, unusual TCP flag combinations, and unexpected SSH traffic that may indicate scanning, firewall evasion, or unauthorized access.

### TCP SYN Scanning
- A TCP SYN scan sends connection requests to different ports or systems to discover available devices and open services.

#### Possible Attacker Activity
- A compromised computer may scan the network to identify systems and services that can be exploited or infected.

#### Normal Client Behavior
- Clients commonly connect to known services on servers, printers, and approved IoT devices.

#### Suspicious Client Behavior
- A client may be suspicious when it attempts to connect to more ports or devices than normally expected.

### Indicators of a Possible SYN Scan
- Look for one source sending SYN packets to many destination ports.
- Look for one source sending SYN packets to many destination IP addresses.
- Look for repeated connection attempts without completed TCP handshakes.
- Look for clients attempting to enumerate routers, switches, adjacent hosts, or unrelated systems.
- Look for connection attempts involving services the client does not normally use.

### Investigation Questions
- Is the source system authorized to perform network scans?
- Is it connecting only to expected servers and services?
- Is it scanning devices outside its normal responsibilities?
- Are the TCP handshakes completed?
- Is the activity part of legitimate monitoring or security testing?

### Important Reminder
- SYN activity is not automatically malicious. Vulnerability scanners, monitoring systems, and administrative tools can generate similar traffic.

## Unusual TCP Flags
- TCP packets with missing or abnormal flag combinations may indicate scanning, operating-system fingerprinting, firewall evasion, or malformed traffic.

### Null Flags
- A TCP Null packet has none of the TCP control flags enabled.

#### Possible Purpose
- Attackers may use Null packets to scan systems or test how devices and firewalls respond to unusual traffic.

### FIN Flags
- A FIN scan sends packets with only the FIN flag enabled instead of beginning with a normal SYN request.

#### Possible Purpose
- Attackers may use FIN packets to identify open or closed ports without performing a standard TCP handshake.

### Christmas Tree Flags
- A Christmas Tree or Xmas packet commonly has the FIN, PSH, and URG flags enabled together.

#### Name Origin
- The packet is described as “lit up like a Christmas tree” because several TCP flags are enabled simultaneously.

### Why Attackers Use Unusual Flags
- Some stateless firewalls and packet-filtering routers may handle unusual TCP flags differently from standard SYN packets.

#### Possible Evasion
- A filtering device may assume that the packet belongs to an existing conversation and allow it without properly checking the connection state.

### Possible Signs of Abnormal Flag Activity
- TCP packets with no flags enabled.
- Packets containing only the FIN flag.
- Packets with FIN, PSH, and URG enabled together.
- Flag combinations that do not match the normal TCP connection process.
- Unusual packets sent repeatedly to several ports or systems.

### Important Reminder
- Unusual flag combinations are investigation indicators, not automatic proof of malware. They may also result from testing tools, damaged packets, or unusual network software.

## SSH Traffic
- Secure Shell provides encrypted remote command-line access and is commonly used to administer servers and network devices.

### Standard Port
- SSH normally uses TCP port `22`.

### Non-Standard Ports
- SSH can be configured to use other ports, so filtering only for port `22` may not identify every SSH connection.

### Legitimate SSH Activity
- System administrators and support engineers may use SSH to manage authorized servers, services, and network devices.

### Suspicious SSH Activity
- SSH should be investigated when it comes from an unexpected user, workstation, network segment, or location.

#### Example
- SSH access from an engineer’s administrative computer may be normal, while SSH access from a front-desk receptionist’s computer may be suspicious.

### SSH Investigation Questions
- Is the source computer authorized to use SSH?
- Is the user responsible for managing the destination system?
- Is the destination an approved server or network device?
- Is the connection occurring at an expected time?
- Is SSH using an approved port?
- Are there repeated failed connection attempts?
- Is an internal system initiating SSH connections to an unusual external address?

### Key Takeaway
- Repeated SYN packets, abnormal TCP flags, and unexpected SSH sessions may indicate scanning, firewall evasion, lateral movement, or unauthorized remote access. Compare the activity with the organization’s normal network behavior before classifying it as malicious.

</details>

<details>
<summary><strong>Lab 9 – Part 1 – Filtering for Suspect TCP Behavior</strong></summary>

## Lab 9 – Part 1 – Filtering for Suspect TCP Behavior
- Create filters and I/O graphs that help identify TCP SYN scans and Christmas Tree scans in Wireshark.

### Required Capture File
- Open the Module 2 trace file previously used to examine the port-scanning activity.

#### Course File Reminder
- The lesson refers to this as the Lab 1 and 2 or Lab 2 and 3 trace file. Use the capture containing the large number of TCP SYN packets.

### Observed Port-Scanning Activity
- One device attempts to connect to common ports across several systems.

#### Targeted Ports
- Port `21` – FTP
- Port `22` – SSH
- Port `23` – Telnet
- Port `25` – SMTP
- Port `443` – HTTPS

#### Finding
- The repeated connection attempts to several ports and systems indicate port-scanning activity.

## Filtering for TCP SYN Packets
- Create a filter that displays initial SYN packets without including SYN-ACK responses.

### Basic SYN Filter
- Use `tcp.flags.syn == 1` to display packets with the SYN flag enabled.

#### Limitation
- The basic filter displays both initial SYN packets and SYN-ACK responses because both packet types have the SYN flag set.

### Step 1: Create a SYN-Only Filter
- Combine the SYN and ACK fields to display only initial connection requests.

1. Enter `tcp.flags.syn == 1` in the display filter.
2. Select one of the matching SYN packets.
3. Expand **Transmission Control Protocol**.
4. Expand **Flags**.
5. Locate the acknowledgement flag or `tcp.flags.ack`.
6. Confirm that its value is `0`.
7. Right-click the acknowledgement field.
8. Select **Prepare as Filter → …and Selected**.
9. Apply the completed filter.

#### Complete SYN-Only Filter
- Use `tcp.flags.syn == 1 && tcp.flags.ack == 0`.

#### Filter Meaning
- `tcp.flags.syn == 1` requires the SYN flag to be enabled.
- `tcp.flags.ack == 0` requires the ACK flag to be disabled.

#### Result
- The filter displays initial TCP SYN packets without displaying SYN-ACK responses.

### Step 2: Examine the SYN Frequency
- Review the Time column to determine how quickly the source sends SYN packets.

1. Keep the SYN-only filter active.
2. Review the packet timestamps.
3. Look for many SYN packets occurring within the same second.
4. Identify periods with unusually concentrated SYN activity.

#### Finding
- The capture contains many SYN packets within very short periods, suggesting automated scanning.

## Graphing SYN Activity
- Use an I/O graph to measure and visualize the number of SYN packets over time.

### Step 3: Open the I/O Graph
- Create a graph using the SYN-only display filter.

1. Copy `tcp.flags.syn == 1 && tcp.flags.ack == 0`.
2. Select **Statistics**.
3. Select **I/O Graphs**.
4. Click **+** to add a new graph.
5. Name the graph `SYN Scan`.
6. Paste the SYN-only filter into the display-filter field.
7. Enable the graph.

### Step 4: Configure the Graph
- Adjust the graph to clearly display short bursts of SYN traffic.

1. Disable the default graph lines temporarily.
2. Set the SYN Scan line to a bright color, such as green.
3. Set the Y-axis measurement to **Packets**.
4. Set the time interval to `100 ms`.
5. Review the SYN activity across the capture.

#### Interval Meaning
- An interval of `100 ms` measures the number of matching packets every one-tenth of a second.

#### Result
- The graph shows several significant SYN spikes.
- Near the end of the capture, approximately `500` SYN packets occur within a single `100 ms` interval.

#### Finding
- This volume of initial connection requests is highly abnormal and strongly indicates automated scanning or a SYN-based attack.

### Step 5: Compare SYNs with All Traffic
- Add the default traffic graph to compare SYN packets with the complete capture.

1. Enable or recreate the **All Packets** graph.
2. Keep the SYN Scan graph enabled.
3. Use different colors for the two graph lines.
4. Compare the number of SYN packets with the total packet count.

#### Graph Comparison
- The All Packets line represents all captured packets during each interval.
- The SYN Scan line represents only packets matching the SYN-only filter.

#### Purpose
- This comparison shows how much of the network traffic consists of initial SYN requests.

## Filtering for a Christmas Tree Scan
- Create a filter for packets that have the FIN, PSH, and URG flags enabled together.

### Christmas Tree Scan
- A Christmas Tree or Xmas scan uses an unusual combination of TCP flags to test how systems and filtering devices respond.

#### Common Flags
- FIN – Finish
- PSH – Push
- URG – Urgent

### Step 6: Build the Xmas Scan Filter
- Select each required TCP flag and combine them into one display filter.

1. Select a TCP packet.
2. Expand **Transmission Control Protocol**.
3. Expand **Flags**.
4. Locate the **Urgent** flag.
5. Right-click it and select **Prepare as Filter → Selected**.
6. Locate the **Push** flag.
7. Right-click it and select **Prepare as Filter → …and Selected**.
8. Locate the **Fin** flag.
9. Right-click it and select **Prepare as Filter → …and Selected**.
10. Apply the completed filter.

#### Complete Xmas Scan Filter
- Use `tcp.flags.urg == 1 && tcp.flags.push == 1 && tcp.flags.fin == 1`.

#### Result
- The filter displays packets containing the typical Christmas Tree combination of URG, PSH, and FIN flags.

### Step 7: Save the Xmas Scan Filter
- Save the filter inside a TCP drop-down menu.

1. Enter the completed Xmas scan filter.
2. Click the **+** button.
3. Enter `TCP//Xmas Scan` as the label.
4. Save the filter.

#### Filter Organization
- The `TCP//` prefix groups the saved filter under a TCP drop-down menu.

### Why Xmas Scans Are Used
- Xmas scans may help an attacker identify open ports, examine how a system responds, or perform operating-system fingerprinting.

#### Firewall Behavior
- Unusual flag combinations may bypass some older or stateless packet filters that focus primarily on standard SYN packets.

#### Important Correction
- Modern stateful firewalls normally track TCP connection states and are more likely to detect or block packets that do not belong to a valid connection.

### Important Reminder
- Large amounts of SYN traffic or unusual TCP flags are strong investigation indicators, but authorized security scanners can produce similar traffic.

### Key Filters
- SYN and SYN-ACK packets: `tcp.flags.syn == 1`
- Initial SYN packets only: `tcp.flags.syn == 1 && tcp.flags.ack == 0`
- Christmas Tree scan: `tcp.flags.urg == 1 && tcp.flags.push == 1 && tcp.flags.fin == 1`

### Key Takeaway
- A SYN-only filter isolates initial connection attempts, while the I/O graph reveals how rapidly they occur. Filtering for URG, PSH, and FIN together helps identify possible Christmas Tree scanning activity.

</details>

<details>
<summary><strong>Lab 9 – Part 2 – Filtering for Suspect SSH Traffic</strong></summary>

## Lab 9 – Part 2 – Filtering for Suspect SSH Traffic
- Create and save Wireshark filters that identify SSH traffic originating from unauthorized users, computers, or network segments.

### SSH Traffic
- Secure Shell provides encrypted remote access to servers and network infrastructure.

#### Standard Port
- SSH normally uses TCP port `22`.

#### Non-Standard Ports
- SSH can use other ports, so the filters must be adjusted to match the organization’s configuration.

### Expected SSH Sources
- SSH traffic should normally originate from authorized administrator computers or an approved network-engineering VLAN.

#### Suspicious SSH Sources
- SSH traffic from user workstations, guest networks, reception computers, or other unauthorized VLANs should be investigated.

## Filtering for Port 22 Traffic

### Step 1: Display Port 22 Traffic
- Create a basic filter for packets using TCP port 22.

1. Enter `tcp.port == 22` in the display filter bar.
2. Apply the filter.
3. Review the matching packets.

#### Result
- The lab capture contains `111` packets using TCP port `22`.

#### Filter Behavior
- `tcp.port` checks both the source and destination ports, so it displays packets traveling in either direction.

## Excluding an Authorized VLAN

### Example Network
- Assume the authorized network-engineering VLAN is `10.0.10.0/24`.

#### Important Reminder
- Replace this example subnet with the actual IP address or VLAN used by authorized administrators.

### Step 2: Create the Course-Style Filter
- Exclude packets whose source address belongs to the authorized engineering VLAN.

#### Filter
- Use `tcp.port == 22 && !(ip.src == 10.0.10.0/24)`.

#### Result
- The filter displays port 22 packets whose source IP is outside the authorized VLAN.

#### Limitation
- This filter can also match response packets from SSH servers because the server becomes the source in the return direction.

## Detecting Unauthorized SSH Connection Attempts

### Recommended Filter
- Focus on initial connection requests sent to TCP port 22 from outside the authorized VLAN.

#### Filter
- Use `tcp.dstport == 22 && tcp.flags.syn == 1 && tcp.flags.ack == 0 && !(ip.src == 10.0.10.0/24)`.

#### Filter Meaning
- `tcp.dstport == 22` identifies connections being sent to the standard SSH port.
- `tcp.flags.syn == 1` requires the SYN flag to be enabled.
- `tcp.flags.ack == 0` limits the result to initial connection requests.
- `!(ip.src == 10.0.10.0/24)` excludes authorized engineering systems.

#### Result
- The filter displays initial SSH connection attempts originating outside the authorized VLAN.

### Step 3: Apply the Recommended Filter
- Search for unauthorized systems attempting to start SSH connections.

1. Replace `10.0.10.0/24` with the authorized administrator subnet.
2. Enter the completed filter.
3. Apply it.
4. Review each matching source and destination address.
5. Confirm whether the source is authorized to manage the destination system.

## Filtering Multiple SSH Ports
- Include every port approved for SSH within the organization.

### Example Ports
- Assume SSH is allowed on ports `22`, `2222`, and `22022`.

#### Multiple-Port Filter
- Use `tcp.dstport in {22, 2222, 22022} && tcp.flags.syn == 1 && tcp.flags.ack == 0 && !(ip.src == 10.0.10.0/24)`.

#### Important Reminder
- Replace the example ports with the SSH ports used in the local environment.

## Saving the Filter

### Step 4: Create a Saved Filter Button
- Save the unexpected SSH filter for future investigations.

1. Enter the completed display filter.
2. Click the **+** button.
3. Enter `SSH//Unexpected Source` as the label.
4. Save the filter.

#### Filter Organization
- The `SSH//` prefix groups related SSH filters inside an SSH drop-down menu.

## Investigating Matching Traffic
- Examine every unexpected SSH connection to determine whether it is authorized.

### Investigation Questions
- Is the source computer inside an approved administrator VLAN?
- Is the source user authorized to manage the destination?
- Is the destination an approved server or infrastructure device?
- Is the connection occurring during an expected maintenance period?
- Is SSH using an approved port?
- Are several systems or ports being scanned?
- Are repeated connection attempts occurring?

### Port-Based Detection Limitation
- Port `22` usually carries SSH, but another application could use the same port.

#### Protocol Filter
- Use `ssh` when Wireshark successfully identifies the traffic as the SSH protocol.

#### Non-Standard SSH
- SSH running on an unusual port may require **Decode As** or protocol-based inspection before Wireshark identifies it correctly.

### Encryption Limitation
- SSH encrypts its session contents, but Wireshark can still show connection metadata such as IP addresses, ports, timing, packet counts, and session duration.

### Key Filters
- All port 22 traffic: `tcp.port == 22`
- Port 22 traffic outside the authorized source VLAN: `tcp.port == 22 && !(ip.src == 10.0.10.0/24)`
- Unauthorized initial SSH attempts: `tcp.dstport == 22 && tcp.flags.syn == 1 && tcp.flags.ack == 0 && !(ip.src == 10.0.10.0/24)`
- Multiple SSH ports: `tcp.dstport in {22, 2222, 22022} && tcp.flags.syn == 1 && tcp.flags.ack == 0 && !(ip.src == 10.0.10.0/24)`

### Key Takeaway
- Compare SSH connection attempts with the organization’s approved administrator VLANs and SSH ports. Traffic from unexpected sources should be reviewed for unauthorized access or compromised systems.

</details>

<details>
<summary><strong>Lab 10 – Filtering for Executable Files</strong></summary>

## Lab 10 – Filtering for Executable Files
- Use Wireshark filters and TCP-stream analysis to identify executable files transferred through unencrypted HTTP, including executables disguised as other file types.

### Required Capture File
- Open the **Lab 3 and 4 trace file from Module 2**.

### Important Limitation
- These methods work only when Wireshark can inspect the transferred data.

#### Encrypted Traffic
- Files transferred through HTTPS or another encrypted protocol cannot normally be inspected unless the traffic is decrypted first.

## Filtering for a Specific File

### Step 1: Locate the Request URI
- Examine the filename requested by the HTTP client.

1. Select packet `14`.
2. Expand **Hypertext Transfer Protocol**.
3. Locate **Request URI**.
4. Confirm that the URI is `/connecttest.txt`.

### Step 2: Prepare an Exact Filter
- Create a filter that displays requests for this specific file.

1. Right-click **Request URI**.
2. Select **Prepare as Filter → Selected**.
3. Apply the generated filter.

#### Exact URI Filter
- Use `http.request.uri == "/connecttest.txt"`.

#### Limitation
- This filter matches only `/connecttest.txt` and does not display other text files.

## Filtering by File Extension

### Step 3: Filter for All Text Files
- Use a regular expression to find URI paths ending in `.txt`.

#### Text File Filter
- Use `http.request.uri.path matches r"(?i)\.txt$"`.

#### Filter Meaning
- `matches` performs a regular-expression search.
- `(?i)` makes the search case-insensitive.
- `\.` matches the period before the extension.
- `$` requires `.txt` to appear at the end of the path.

#### Important Correction
- `http.request.uri == "/*.txt"` does not work because the equality operator searches for that exact value rather than treating `*` as a wildcard.

### Step 4: Save the Text File Filter
- Store the filter under a Files drop-down menu.

1. Enter the text-file filter.
2. Click the **+** button.
3. Enter `Files//All TXTs` as the label.
4. Save the filter.

#### Filter Organization
- The `Files//` prefix groups file-related filters inside one Files menu.

## Filtering for Executable Extensions

### Step 5: Search for EXE and BIN Files
- Create filters for common executable file extensions.

#### EXE Filter
- Use `http.request.uri.path matches r"(?i)\.exe$"`.

#### EXE and BIN Filter
- Use `http.request.uri.path matches r"(?i)\.(exe|bin)$"`.

#### False-Positive Warning
- Searching only for the text `exe` may match unrelated names such as `mt_exem`.

#### Better Practice
- Include the period and end-of-path marker to ensure that the filter matches an actual file extension.

## Detecting Disguised Executables

### File Extension Limitation
- Attackers may disguise executable files by assigning extensions such as `.png`, `.jpg`, or `.txt`.

#### Example
- A transferred file may claim to be a PNG image even though its contents belong to a Windows executable.

### Step 6: Search for DOS Text
- Search the HTTP data for text commonly found in Windows executable files.

1. Clear the current URI filter.
2. Enter `http contains "DOS"`.
3. Apply the filter.
4. Select the matching packet.

#### DOS Filter
- Use `http contains "DOS"`.

#### Result
- One packet matches even though the transferred file claims to be a PNG image.

#### Limitation
- This is only an investigation technique. It may produce false positives or miss executables when the text is absent, split across packets, compressed, or encrypted.

### Step 7: Follow the TCP Stream
- Reassemble and inspect the complete HTTP conversation.

1. Right-click the matching packet.
2. Select **Follow**.
3. Select **TCP Stream**.
4. Review the HTTP request and transferred file data.

### Executable Indicators
- Look for content that identifies the file as a Windows executable.

#### MZ Header
- `MZ` at the beginning of the file is the traditional signature of a Windows DOS or Portable Executable file.

#### DOS Stub Message
- Windows executables commonly contain `This program cannot be run in DOS mode`.

#### Finding
- Although the file claims to be a PNG image, the `MZ` header and DOS stub show that it is actually an executable.

### Optional MZ Signature Filter
- Check whether the HTTP file data begins with the hexadecimal `MZ` signature.

#### Filter
- Use `http.file_data[0:2] == 4d:5a`.

#### Limitation
- This works only when Wireshark exposes the beginning of the transferred file through `http.file_data`.

### Step 8: Save the Executable Filter
- Store the DOS-content filter for future investigations.

1. Apply `http contains "DOS"`.
2. Click the **+** button.
3. Enter `Files//EXE Files` as the label.
4. Save the filter.

### Security Warning
- Do not open or execute files extracted from suspicious traffic on your main computer.

#### Safe Analysis
- Examine suspicious files only in an isolated virtual machine, sandbox, or approved malware-analysis environment.

### Key Filters
- Specific file: `http.request.uri == "/connecttest.txt"`
- Text files: `http.request.uri.path matches r"(?i)\.txt$"`
- EXE files: `http.request.uri.path matches r"(?i)\.exe$"`
- EXE or BIN files: `http.request.uri.path matches r"(?i)\.(exe|bin)$"`
- DOS text: `http contains "DOS"`
- MZ signature: `http.file_data[0:2] == 4d:5a`

### Key Takeaway
- File extensions do not prove a file’s actual type. Inspect the transferred content for signatures such as `MZ` and the DOS stub, especially when a harmless-looking image or document produces suspicious results.

</details>

<details>
<summary><strong>Lab 11 – Analyzing Traffic over Non-Standard Ports</strong></summary>

## Lab 11 – Analyzing Traffic over Non-Standard Ports
- Use Wireshark conversations, protocol dissection, TCP streams, and **Decode As** to investigate traffic using unexpected ports.

### Required Capture File
- Open the **Lab 3 and 4 trace file** used in the previous exercises.

### Non-Standard Port Traffic
- Attackers may use unusual ports to hide command-and-control communication, transfer data, or avoid basic monitoring rules.

#### Important Reminder
- A high or unusual port is not automatically malicious. Applications commonly use dynamic, proprietary, or alternate ports.

## Finding Unusual Conversations

### Step 1: Open the Conversations Window
- Review the capture from a high-level view to find unusual ports carrying significant traffic.

1. Select **Statistics**.
2. Select **Conversations**.
3. Open the **TCP** tab.
4. Locate the **Port B** column.
5. Click the column twice to sort it from highest to lowest.
6. Look for unusual ports with significant packet or byte counts.

#### Observed Ports
- Port `445` – SMB
- Port `465` – Secure SMTP
- Port `587` – SMTP submission
- Port `993` – Secure IMAP
- Port `5228` – Commonly associated with Google services
- Port `51595` – High-numbered port with significant traffic

#### Important Clarification
- **Port B** represents the port used by endpoint B. It is not automatically the server port.

#### Verification
- Examine the initial SYN packet to determine which system initiated the connection and which port is acting as the service port.

## Investigating Port 5228

### Step 2: Apply the Conversation as a Filter
- Isolate the conversation using port `5228`.

1. Select the conversation containing port `5228`.
2. Right-click the conversation.
3. Select **Apply as Filter → Selected**.
4. Close the Conversations window.
5. Review the filtered packets.

### Automatic Protocol Detection
- Wireshark may recognize a protocol even when it uses a non-standard port.

#### Result
- Wireshark identifies the port `5228` conversation as TLS 1.3 without requiring manual configuration.

### Step 3: Inspect the TLS Client Hello
- Examine the Server Name Indication to identify the requested server.

1. Select the TLS **Client Hello** packet.
2. Expand **Transport Layer Security**.
3. Expand the TLS extensions.
4. Locate **Server Name Indication**.
5. Review the server name.

#### Result
- The Server Name Indication contains `mtalk.google.com`.

#### Finding
- The device is communicating with a Google service through TLS on port `5228` instead of the usual HTTPS port `443`.

#### Important Reminder
- Using port `5228` is not automatically suspicious because some legitimate Google services use this port.

## Investigating Port 51595

### Step 4: Isolate the High-Port Conversation
- Examine the conversation using port `51595`.

1. Clear the current display filter.
2. Return to **Statistics → Conversations**.
3. Open the **TCP** tab.
4. Sort the **Port B** column from highest to lowest.
5. Select the conversation using port `51595`.
6. Right-click it.
7. Select **Apply as Filter → Selected**.
8. Close the Conversations window.

### Unknown Application Data
- Wireshark recognizes the traffic as TCP but does not automatically identify its application-layer protocol.

#### Layer Information
- Wireshark can decode the traffic through Layer 4 because it recognizes TCP.
- The remaining payload appears as general or undecoded data.

#### Possible Reasons
- The protocol may not have a Wireshark dissector.
- The protocol may be using a non-standard port.
- The traffic may use a proprietary format.
- The data may be encrypted, encoded, or malformed.
- The channel may be used for command-and-control communication or data exfiltration.

## Inspecting the TCP Stream

### Step 5: Follow the Conversation
- Reassemble the TCP stream and examine its readable contents.

1. Right-click one of the packets.
2. Select **Follow**.
3. Select **TCP Stream**.
4. Review the exchanged data.

#### Visible Text
- The stream contains readable references such as:

  - `POP3 Server`
  - `esc2.net`
  - `IMail`
  - `CommuniGate Pro`

#### Initial Possibilities
- The traffic might involve POP3, IMAP, SMTP, or another email-related application.

#### Important Reminder
- Readable product names and protocol text provide investigation clues but do not prove which protocol is being used.

## Manually Selecting a Dissector

### Step 6: Try Decode As
- Force Wireshark to interpret the traffic using a suspected protocol dissector.

1. Close the TCP Stream window.
2. Select a packet involving port `51595`.
3. Right-click the packet.
4. Select **Decode As**.
5. Locate the port `51595` entry.
6. Select the suspected protocol, such as **POP**.
7. Click **OK**.
8. Review the newly decoded packet details.

### Evaluating the Result
- Determine whether the selected dissector produces meaningful protocol information.

#### Successful Dissection
- A correct dissector should show recognizable commands, responses, fields, and protocol structure.

#### Unsuccessful Dissection
- Meaningless or incorrectly structured results suggest that the selected dissector is wrong.

#### Lab Result
- Decoding the traffic as POP does not produce useful commands or responses, so the traffic is probably not normal POP traffic.

### Step 7: Try Other Possible Protocols
- Test another dissector only when the stream contents provide a reasonable clue.

1. Open **Decode As** again.
2. Select another likely protocol, such as IMAP or SMTP.
3. Apply the dissector.
4. Review the decoded fields.
5. Stop when no protocol produces a meaningful structure.

### Reset Decode As
- Remove an incorrect manual protocol assignment when the investigation is complete.

1. Open **Analyze → Decode As**.
2. Locate the manually assigned port.
3. restore or clear the default assignment.
4. Click **OK**.

## Possible Malicious Activity
- An unidentified high-port conversation may require deeper investigation for command-and-control activity or data exfiltration.

### Indicators to Review
- Large amounts of data transferred through an unusual port.
- Repeated connections to an unfamiliar external address.
- Cleartext commands or system information.
- Payloads that do not match the claimed protocol.
- Long-running or regularly repeating connections.
- Connections initiated by systems that do not normally use the service.

### High-Port Warning
- Port `51595` may be a temporary client port rather than a server service port.

#### Determine the Connection Roles
- Inspect the first SYN packet to identify the client, server, source port, and destination port before treating the high-numbered port as suspicious.

### Key Takeaway
- Start with **Statistics → Conversations** to find unusual ports with meaningful traffic. Allow Wireshark to detect the protocol automatically, inspect the TCP stream when it cannot, and use **Decode As** only when the payload provides evidence for a likely protocol.

</details>