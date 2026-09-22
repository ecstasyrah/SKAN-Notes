<details>
<summary><strong>How to Find “Suspect” Traffic Patterns</strong></summary>

## How to Find “Suspect” Traffic Patterns

- Recognize suspicious traffic by understanding normal network behavior, learning common attack patterns, and using alerts and logs to guide packet analysis.

### Know What Normal Looks Like

- Experienced analysts identify unusual packets because they understand how legitimate traffic normally behaves.

#### Establish a Baseline

1. Capture normal, everyday traffic from your network.
2. Review it periodically.
3. Note the protocols, conversations, ports, and geographic locations commonly observed.
4. Compare suspicious captures with these normal patterns.

#### Benefit

- A familiar baseline helps you identify unexpected behavior more quickly when a monitoring system generates an alert.

### Learn Common Attack Patterns

- Understanding how common attacks appear in packets helps distinguish suspicious behavior from ordinary communication.
- Build this knowledge through repeated practice and investigation.

### Use Other Evidence

- Wireshark is one part of the investigation, not the only source of information.
- Review security alerts, firewall logs, server events, and other available records to identify relevant systems and time periods before inspecting packets.

### Step Back and Ask Questions

- Regularly consider the purpose and context of the traffic instead of focusing only on individual packet fields.

#### Investigation Questions

- What is this traffic doing?
- Should this system be communicating this way?
- Are the protocol, port, and destination expected?
- Does this traffic normally look like this?
- What evidence explains the difference?

### Start with Simple Patterns

- Focus on straightforward, familiar behavior before moving to more complicated attacks and protocols.
- Develop your understanding gradually rather than trying to recognize every possible attack immediately.

### Prepare a Wireshark Security Profile

- Keep investigation settings and reusable filters organized in a dedicated security profile.

1. Create or select your security profile.
2. Save useful filters as you learn them.
3. Give each filter a clear name.
4. Reuse and adjust the filters during future investigations.

### Main Principle

- Unexpected traffic deserves investigation, but unusual does not automatically mean malicious. Compare it with the baseline and supporting evidence before reaching a conclusion.

</details>

<details>
<summary><strong>How to Find “Suspect” Traffic Patterns</strong></summary>

## How to Find “Suspect” Traffic Patterns

- Recognize suspicious traffic by understanding normal network behavior, learning common attack patterns, and using alerts and logs to guide packet analysis.

### Know What Normal Looks Like

- Experienced analysts identify unusual packets because they understand how legitimate traffic normally behaves.

#### Establish a Baseline

1. Capture normal, everyday traffic from your network.
2. Review it periodically.
3. Note the protocols, conversations, ports, and geographic locations commonly observed.
4. Compare suspicious captures with these normal patterns.

#### Benefit

- A familiar baseline helps you identify unexpected behavior more quickly when a monitoring system generates an alert.

### Learn Common Attack Patterns

- Understanding how common attacks appear in packets helps distinguish suspicious behavior from ordinary communication.
- Build this knowledge through repeated practice and investigation.

### Use Other Evidence

- Wireshark is one part of the investigation, not the only source of information.
- Review security alerts, firewall logs, server events, and other available records to identify relevant systems and time periods before inspecting packets.

### Step Back and Ask Questions

- Regularly consider the purpose and context of the traffic instead of focusing only on individual packet fields.

#### Investigation Questions

- What is this traffic doing?
- Should this system be communicating this way?
- Are the protocol, port, and destination expected?
- Does this traffic normally look like this?
- What evidence explains the difference?

### Start with Simple Patterns

- Focus on straightforward, familiar behavior before moving to more complicated attacks and protocols.
- Develop your understanding gradually rather than trying to recognize every possible attack immediately.

### Prepare a Wireshark Security Profile

- Keep investigation settings and reusable filters organized in a dedicated security profile.

1. Create or select your security profile.
2. Save useful filters as you learn them.
3. Give each filter a clear name.
4. Reuse and adjust the filters during future investigations.

### Main Principle

- Unexpected traffic deserves investigation, but unusual does not automatically mean malicious. Compare it with the baseline and supporting evidence before reaching a conclusion.

</details>

<details>
<summary><strong>Lab 4 – Analyzing TCP SYN Attacks</strong></summary>

## Lab 4 – Analyzing TCP SYN Attacks

- Investigate a real SYN-flood capture by examining connection volume, targeted ports, and SYN-ACK responses.

### Required Capture

- Open the **Lab 6 – TCP SYN scans** trace file referenced in the lesson.
- Although this section is titled Lab 4, the instructor calls the exercise **Lab 6 – Detecting Unusual TCP SYN Behavior and Unusual Port Numbers**.
- The target address is abbreviated as `64.129`; use its complete IP address from the capture.

### Step 1: Read the Lab Questions

- Review the four questions embedded in the capture.

1. Open **Statistics → Capture File Properties**.
2. Read **Capture file comments**.
3. Attempt the questions before reviewing the findings.

### Step 2: Identify Abnormal SYN Activity

- Look for many sources rapidly sending initial connection requests to one target.

1. Review the packet list and timestamps.
2. Compare the source and destination addresses.
3. Apply `tcp.flags.syn == 1 && tcp.flags.ack == 0` to isolate initial SYN packets.
4. Look for repeated attempts directed at the same device.

#### Lab Findings

- A large number of SYNs arrive rapidly from many internet IP addresses.
- Most requests target the same destination.
- The lesson identifies this capture as a distributed SYN-flood attack.

### Step 3: Review the Targeted Ports

- Use conversation statistics to identify heavily targeted and unusual ports.

1. Clear the display filter to review the full capture.
2. Open **Statistics → Conversations**.
3. Select the **TCP** tab.
4. Locate conversations involving the target.
5. Sort **Port B** and inspect the endpoint addresses.

#### Observed Ports

- `80`: The main destination port targeted by the SYN flood.
- `443`: Some traffic is present, but not all involves the same target or attack.
- `1986`: Additional traffic appears to probe addresses sequentially across the subnet and deserves separate investigation.

#### Interpretation

- A flood against one service and sequential probing across a subnet are different patterns; do not automatically group them as one activity.
- An unfamiliar port warrants investigation but does not prove malicious traffic.
- Confirm endpoint roles because **Port B** is not always the server port.

### Step 4: Find the Target’s SYN-ACK Responses

- Determine whether the target responds to connection attempts.

1. Select the saved **TCP → Open Ports** filter.
2. Replace its address condition with the target’s source IP.
3. Apply `tcp.flags.syn == 1 && tcp.flags.ack == 1 && ip.src == <target_IP>`.
4. Replace `<target_IP>` with the complete target address.
5. Review the displayed packet count and source ports.

#### Lab Result

- The filter matches approximately `500` SYN-ACK packets.
- The target responds from its web service, indicating that the port was accepting connection attempts.
- This count represents packets, not necessarily 500 unique or completed connections.

### Step 5: Examine an Individual Conversation

- Check whether the capture contains the complete connection exchange.

1. Right-click a matching SYN-ACK.
2. Select **Conversation Filter → TCP**.
3. Look for the initial SYN and the client’s final ACK.

#### Lab Observation

- The demonstrated conversation contains only a SYN-ACK.
- The instructor interprets this as a delayed response to a SYN sent before capture began.
- Several examined conversations lack a complete handshake.

#### Evidence Limitation

- A missing SYN can also result from capture loss or limited network visibility.
- The capture alone cannot establish the exact response delay when the original SYN is absent.

### How SYN Flooding Affects the Target

- SYN requests can create many half-open connections when the handshake is not completed.
- Excessive requests may exhaust connection-handling capacity and prevent legitimate clients from connecting.

#### Expected Impact

- The instructor expects the overwhelmed target eventually to stop sending SYN-ACKs.
- Confirm resource exhaustion using server metrics and logs; missing replies alone do not prove it.

### Main Findings

- Many source IPs rapidly send SYNs toward a common target, primarily on port `80`.
- Port `1986` traffic shows an additional pattern requiring investigation.
- Approximately `500` SYN-ACK packets are visible, but examined conversations are incomplete.
- The lesson presents this as a real DDoS example; distinguish observed packet behavior from inferred server impact.

</details>

<details>
<summary><strong>Identifying Unusual Country Codes with GeoIP</strong></summary>

## Identifying Unusual Country Codes with GeoIP

- Investigate unexpected geographic destinations and country-code domains by examining GeoIP information and DNS queries.

### Prerequisite

- Configure Wireshark’s GeoIP databases before using geographic filters.
- Refer to the earlier **Configuring Wireshark for Cybersecurity Analysis** module for setup.

### Indicator 3: Unexpected GeoIP Locations

- GeoIP estimates the geographic location associated with an IP address.
- Use it to investigate where incoming connections originate and where internal systems send data.

#### Accuracy Limitation

- GeoIP is approximate. Hosting providers, VPNs, proxies, and outdated records can affect the result.
- An IP’s location does not establish the attacker’s actual location or identity.

### Filter by Country Name

- Use `ip.geoip.country == "Russia"`.
- This matches IPv4 packets whose source or destination maps to Russia.

### Filter by Two-Letter Country Code

- Use `ip.geoip.country_iso == "RU"`.
- Use the `_iso` field for country codes and the `country` field for full country names.

### Exclude a Country

- Use `ip && !(ip.geoip.country_iso == "US")` to exclude IPv4 packets with either endpoint mapped to the United States.

#### Important Limitation

- This also hides traffic between a US endpoint and a foreign endpoint.
- It may include packets without GeoIP information.

#### Focus on Foreign Destinations

- Use `ip.geoip.dst_country_iso && !(ip.geoip.dst_country_iso == "US")` to show known destination countries outside the United States.

### Indicator 4: Unexpected Country-Code Domains

- Review DNS queries for country-code endings that are unusual for the organization, such as `.ru` or `.cn`.

#### Investigation Context

- Evaluate the complete domain name and the requesting host.
- An unfamiliar country-code domain is an investigation lead, not proof of malware.

### Filter DNS Queries by Domain Ending

- Use `dns.flags.response == 0 && dns.qry.name matches r"(?i)\.(us|mx|cr)\.?$"`.

#### Filter Meaning

- `dns.flags.response == 0` limits results to DNS queries.
- `matches` performs a regular-expression search.
- `(?i)` makes the match case-insensitive.
- `\.` requires a literal dot before the suffix.
- `(us|mx|cr)` matches `.us`, `.mx`, or `.cr`.
- `\.?$` allows an optional trailing dot and requires the suffix at the end of the name.

#### Example Country Codes

- `.us` – United States
- `.mx` – Mexico
- `.cr` – Costa Rica

#### Important Distinction

- This filter identifies requested domain names, not traffic physically traveling to those countries.
- A country-code domain can be hosted in another country.

### Investigation Workflow

- Compare geographic and DNS findings with normal business activity.

1. Identify the countries and domain endings normally used by the organization.
2. Apply the relevant GeoIP or DNS filter.
3. Identify the internal host and external address or domain.
4. Review the related conversations and data transfers.
5. Correlate unexpected activity with security alerts and host logs.

</details>

<details>
<summary><strong>Lab 7 – Spotting Suspect Country Codes with Wireshark</strong></summary>

## Lab 7 – Spotting Suspect Country Codes with Wireshark

- Use GeoIP filters, endpoint statistics, and mapping to investigate the geographic distribution of network traffic.

### Required Capture and Setup

- Open the **Lab 7 – Country Codes** PCAP, which uses the same traffic as Lab 6 with updated investigation questions.
- MaxMind GeoIP databases must already be configured under **Preferences → Name Resolution → MaxMind database directories**.

### Step 1: Read the Lab Questions

- Review the questions before examining the traffic.

1. Open **Statistics → Capture File Properties**.
2. Read **Capture file comments**.
3. Investigate traffic originating from Russia and China and whether traffic is sent back to those countries.

### Step 2: Filter Traffic from Russia

- Use the source GeoIP country to identify packets originating from addresses mapped to Russia.

1. Select an IPv4 packet.
2. Expand **Internet Protocol Version 4 → Source GeoIP**.
3. Right-click the country field.
4. Select **Prepare as Filter → Selected**.
5. Change the filter to `ip.geoip.src_country == "Russia"`.
6. Apply it.

#### Lab Result

- The instructor reports `369` matching packets.

### Step 3: Filter Traffic from China

- Change the source-country condition to examine Chinese IP locations.

1. Apply `ip.geoip.src_country == "China"`.
2. Review the displayed count and packet behavior.

#### Lab Result

- The instructor initially reports `323` matching packets.

### Counting Packets, Conversations, and Endpoints

- These measurements are different and should not be used interchangeably.

#### Packets

- The displayed count represents individual packets matching the filter.

#### Unique IP Conversations

- Open **Statistics → Conversations → IPv4** and enable **Limit to display filter** to count matching IP-address pairs.

#### Unique Endpoints

- Open **Statistics → Endpoints → IPv4** to examine individual IP addresses.
- Check each endpoint’s country because a filtered packet includes both its source and destination.

#### Transcript Discrepancy

- The lesson later describes `369` Russian stations and `318` Chinese stations.
- These figures do not establish the requested conversation counts. Verify the appropriate statistics instead of treating packet counts as unique hosts or conversations.

### Step 4: Check Traffic Going to These Countries

- Change the GeoIP field from source to destination.

1. Apply `ip.geoip.dst_country == "China"`.
2. Review the matching packets.
3. Repeat with `ip.geoip.dst_country == "Russia"`.

#### Lab Observation

- SYN-ACK packets are visible going to China.
- These are replies from the device receiving the incoming SYN traffic, not evidence that it initiated new outbound connections.

#### Both Destination Countries

- Use `ip.geoip.dst_country == "Russia" || ip.geoip.dst_country == "China"`.

### Step 5: Save Country Filters

- Group reusable geographic filters under a Countries menu.

1. Enter the desired filter.
2. Click **+**.
3. Use a label such as `Countries//China`.
4. Save the filter.

#### Either-Direction Filter

- Use `ip.geoip.country == "China"` to match packets with either endpoint mapped to China.
- Use `ip.geoip.country == "Russia"` for Russia.
- Label source-only and destination-only filters clearly if saving them separately.

### Step 6: Review GeoIP Endpoint Details

- Examine the locations and network organizations associated with public IP addresses.

1. Clear the display filter to review the complete capture.
2. Open **Statistics → Endpoints**.
3. Select **IPv4**.
4. Review the geographic and network-information columns.

#### Available Information

- Country
- City
- Autonomous System Number (ASN)
- Autonomous System Organization

#### Private Addresses

- Local private IP addresses generally do not have public GeoIP locations.

### Step 7: Display Endpoints on a Map

- Visualize the geographic distribution of endpoints with available location data.

1. In the Endpoints window, locate the map control or drop-down.
2. Select **Open in browser**, as shown in the lesson.
3. Review the plotted locations.
4. Inspect grouped markers to identify areas containing multiple endpoints.

#### Interpretation

- Map markers help reveal geographic concentrations.
- Endpoint counts on a map are different from packet counts and conversation counts.

### Investigation Reminder

- GeoIP estimates an IP address’s location, not the attacker’s identity or physical location.
- Country alone does not make traffic malicious. Consider the SYN-flood behavior, expected business activity, and other evidence together.

</details>

<details>
<summary><strong>Lab 8 – Filtering for Unusual Domain Name Lookups</strong></summary>

## Lab 8 – Filtering for Unusual Domain Name Lookups

- Examine DNS names, identify unexpected country-code endings, and save a reusable domain filter.

### Required Capture

- Open the Lab 8 trace file.
- This capture contains only DNS traffic to make the lookup patterns easier to identify.

### Step 1: Read the Lab Questions

- Determine how many country-code variations of `hackersden` appear and build a filter for those domain endings.

1. Open **Statistics → Capture File Properties**.
2. Read the questions in the capture comments.
3. Attempt the investigation before reviewing the findings.

### Step 2: Filter for Hackersden Lookups

- Find DNS packets containing the name `hackersden`.

1. Select a DNS packet, such as packet `36`.
2. Expand **Domain Name System → Queries**.
3. Locate **Name**.
4. Right-click **Prepare as Filter → Selected**.
5. Replace the exact comparison with `dns.qry.name contains "hackersden"`.
6. Apply the filter.

#### Field Correction

- The correct field is `dns.qry.name`, not `dns.query.name`.

#### Filter Behavior

- `contains` performs a case-sensitive substring search within the specified field.
- The filter can match both queries and responses because responses commonly repeat the queried name.

### Step 3: Add the Queried Name as a Column

- Display domain names directly in the packet list.

1. Right-click the DNS **Name** field.
2. Select **Apply as Column**.
3. Review the domain endings across the matching packets.

#### Lab Findings

- `hackersden.ru` – Russia
- `hackersden.cn` – China
- `hackersden.jp` – Japan
- `hackersden.uk` – United Kingdom
- Four different country-code endings appear.

### Step 4: Filter for Multiple Country-Code Endings

- Use a regular expression to match any domain ending in `.ru`, `.cn`, `.jp`, or `.uk`.

1. Replace `contains` with `matches`.
2. Apply `dns.qry.name matches r"(?i)\.(ru|cn|jp|uk)\.?$"`.

#### Pattern Meaning

- `(?i)` makes the match case-insensitive.
- `\.` requires a literal dot before the country code.
- `(ru|cn|jp|uk)` matches any one of the listed endings.
- `|` means OR within the regular expression.
- `\.?` allows an optional trailing dot.
- `$` requires the match to occur at the end of the name.

#### Avoiding False Matches

- The lesson’s broad pattern `(ru|cn|jp|uk)` can match those letters anywhere in a domain.
- Requiring the dot and end of the name limits matches to the intended domain endings.

#### Queries Only

- Use `dns.flags.response == 0 && dns.qry.name matches r"(?i)\.(ru|cn|jp|uk)\.?$"` to exclude DNS responses.

### Step 5: Save the Filter

- Store the expression under a Naming menu for quick reuse.

1. Keep the completed filter in the display filter bar.
2. Click **+**.
3. Enter `Naming//Strange Domain Country` as the label.
4. Save the filter.

### Interpretation Limits

- These filters identify DNS names, not all subsequent connections to the resolved servers.
- A country-code domain does not prove that its server is physically located in that country.
- The capture contains readable DNS; encrypted DNS cannot be inspected this way unless decrypted.
- An unusual domain name or country-code ending is a lead to investigate, not proof of malicious activity.

</details>

<details>
<summary><strong>Analyzing HTTP Traffic and File Transfers</strong></summary>

## Analyzing HTTP Traffic and File Transfers

- Investigate unencrypted transfers, outdated TLS versions, and unusual HTTP User-Agent strings as possible indicators of suspicious activity.

### Indicator 5: Unencrypted Web Traffic and File Transfers

- Malware may use HTTP or other unencrypted protocols for communication, downloading files, or transferring data.
- The lesson cites Hancitor as an example.
- Cleartext traffic allows analysts to inspect requested filenames, communicating endpoints, headers, and visible payloads.

#### Investigation Context

- Compare unencrypted activity with the organization’s normal traffic.
- Legitimate applications may use HTTP, and malware can also use modern TLS. Encryption status alone does not determine whether traffic is malicious.

### Filter Requests for Selected File Extensions

- Use `http.request.uri.path matches r"(?i)\.(bin|exe|php)$"`.

#### Filter Meaning

- `matches` performs a regular-expression search.
- `(?i)` makes the match case-insensitive.
- `\.` matches the literal period before the extension.
- `(bin|exe|php)` allows any of the three extensions.
- `$` requires the extension to appear at the end of the URI path.

#### Syntax Correction

- `matches` is an operator separated by spaces, not part of a field named `http.request.uri.matches`.

#### Interpretation Limits

- `.exe` commonly identifies a Windows executable.
- `.bin` indicates binary data but is not necessarily executable.
- `.php` commonly identifies a server-side application endpoint; requesting it does not necessarily download PHP source code or malware.
- File extensions alone cannot establish the actual file type or whether it is malicious.

### FTP Transfers

- Investigate unexpected FTP sessions for possible file downloads or data exfiltration.
- Review the communicating systems, transfer direction, filenames, and data volume.

#### FTP Filters

- Use `ftp` to inspect recognized FTP control traffic.
- Use `ftp-data` to inspect recognized FTP data traffic.

### Search for Visible Strings

- Use `frame contains "torrent"` or `frame contains "attack"` to locate those exact strings in captured frame bytes.
- Replace the example text with a relevant investigation term.

#### Limitations

- The search is case-sensitive and does not establish that a match is malicious.
- It may miss text split across packets, compressed content, or encrypted data.
- Follow the relevant stream when packet-level inspection does not provide enough context.

### Indicator 6: Outdated TLS and Unusual User-Agents

- Older TLS versions and unexpected client identifiers may reveal legacy software, automated tools, or suspicious applications.

### Outdated TLS Versions

- The lesson highlights TLS `1.0` and `1.1` as versions to investigate, compared with TLS `1.2` and `1.3`.

#### Interpretation

- An old TLS version may indicate outdated software or configuration, but does not itself prove malware or identify the application’s patch level.
- Distinguish the versions a client offers from the version actually negotiated.
- Do not rely only on the Supported Versions extension: older handshakes may omit it, and TLS 1.3 uses legacy version values in some fields.

### Unusual HTTP User-Agent Strings

- The User-Agent identifies the client software as reported by the application.
- Unexpected tool names or unusual formats may help identify bots and automated activity.

#### Example Filter

- Use `http.user_agent contains "gobuster"` to find that tool name in the header.

#### Limitation

- User-Agent values can be customized or spoofed. A browser-like value does not guarantee legitimate traffic, and an unusual value does not prove an attack.

### Investigation Approach

- Combine protocol visibility, requested paths, transfer behavior, TLS negotiation, and client headers with the network baseline and other security evidence.

</details>

<details>
<summary><strong>Lab 9 – Analyzing HTTP Traffic and Unencrypted File Transfers</strong></summary>

## Lab 9 – Analyzing HTTP Traffic and Unencrypted File Transfers

- Examine HTTP requests, downloads, and outbound POST traffic to investigate a malware infection chain.

### Required Capture

- Reuse the Hancitor malware capture from Lab 3: `AnalyzinganAttack.pcapng`.
- Review **Statistics → Capture File Properties** for the lab notes.
- Do not extract and execute binary files from this capture.

### Step 1: Focus on HTTP Traffic

- Display readable HTTP requests and responses.

1. Apply `http`.
2. Review the GET requests, POST requests, and response codes.
3. If the packet list is crowded, right-click unnecessary column headers, such as **DNS Name** or **Time to Live**.
4. Hide the columns temporarily or select **Remove this Column**.

#### Alternative Filter

- `tcp.port == 80` displays all TCP traffic involving port 80.
- `http` displays traffic Wireshark recognizes as HTTP, including HTTP on other ports.

### Step 2: Inspect Requested Resources

- Identify paths and file extensions associated with the suspicious communication.

1. Select an HTTP GET request.
2. Expand **Hypertext Transfer Protocol**.
3. Locate **Request URI**.
4. Review requests such as `swaging.php`, `.bin` files, and an `.exe` file.

#### Interpretation

- A request to a `.php` endpoint usually retrieves its generated response, not the PHP source code.
- A successful HTTP response alone does not establish whether the returned content is malicious.

### Step 3: Filter Selected File Extensions

- Display requests for paths ending in `.php`, `.exe`, `.bin`, or `.zip`.

1. Right-click **Request URI**.
2. Select **Prepare as Filter → Selected**.
3. Replace the exact comparison with `http.request.uri.path matches r"(?i)\.(php|exe|bin|zip)$"`.
4. Apply the filter.

#### Filter Meaning

- `matches` performs a regular-expression search.
- `(?i)` makes it case-insensitive.
- `\.` requires a literal period before the extension.
- `(php|exe|bin|zip)` matches any listed extension.
- `$` requires the extension at the end of the URI path.

#### Important Scope

- This filter identifies matching requests, not every response or transferred file.
- Using the path field allows requests with query parameters to match.
- The lesson’s broader pattern can match extension-like text elsewhere in a URI.

### Step 4: Investigate the Executable Download

- Identify the server involved in the suspicious download.

1. Select the GET request for the `.exe` file.
2. Expand **Internet Protocol Version 4**.
3. Review the destination address and destination GeoIP information.
4. Inspect the related HTTP response to confirm the transfer.

#### Lab Finding

- The lesson identifies the download server as located in Germany.
- The client retrieves an executable and additional binary files from this server.
- GeoIP is an approximate location indicator, not proof of the server operator’s identity.

### Step 5: Examine Subsequent POST Requests

- Investigate data sent outward after the downloads.

1. Review the later requests in timestamp order.
2. Apply `http.request.method == "POST"` to examine all recognized HTTP POST requests.
3. Compare their source, destination, URI, and visible request bodies.
4. Determine what data is being submitted.

#### Lab Finding

- The infected host begins sending POST requests to other servers after the downloads.
- The instructor identifies several destinations as being in Russia.

#### Interpretation

- POST requests may carry routine application data, malware check-ins, commands, or stolen information.
- Their timing and destination are clues; inspect the content and related evidence before concluding that exfiltration occurred.

### Step 6: Review Endpoint Locations

- Use endpoint statistics to summarize the systems involved in the filtered traffic.

1. Apply the extension filter or POST filter, depending on the activity being investigated.
2. Open **Statistics → Endpoints**.
3. Select **IPv4**.
4. Enable **Limit to display filter**.
5. Review the country information for the public IP addresses.

#### Scope Reminder

- The endpoint list reflects the current filter. An extension filter may omit POST requests to paths without those extensions.

### Step 7: Save the Extension Filter

- Keep the reusable expression under the Signatures menu.

1. Restore `http.request.uri.path matches r"(?i)\.(php|exe|bin|zip)$"`.
2. Click **+**.
3. Enter `Signatures//PHP EXE BIN ZIP`.
4. Save the filter.

### Optional: Export Objects for Analysis

- Wireshark can reconstruct HTTP objects for examination in an isolated malware-analysis environment.

1. Open **File → Export Objects → HTTP**.
2. Select the relevant object.
3. Save it only when needed for controlled analysis.
4. Do not run the extracted file.

#### External Analysis

- A service such as VirusTotal may help determine whether a file is already known as malicious.
- Do not submit confidential or sensitive material to public analysis services.

### Main Finding

- The lab connects suspicious HTTP downloads with later outbound POST activity.
- Cleartext HTTP exposes paths, headers, responses, and potentially transferred data; encrypted traffic requires decryption or other evidence sources for equivalent visibility.

</details>

<details>
<summary><strong>Lab 9 – Analyzing HTTP Traffic and Unencrypted File Transfers</strong></summary>

## Lab 9 – Analyzing HTTP Traffic and Unencrypted File Transfers

- Examine HTTP requests, downloads, and outbound POST traffic to investigate a malware infection chain.

### Required Capture

- Reuse the Hancitor malware capture from Lab 3: `AnalyzinganAttack.pcapng`.
- Review **Statistics → Capture File Properties** for the lab notes.
- Do not extract and execute binary files from this capture.

### Step 1: Focus on HTTP Traffic

- Display readable HTTP requests and responses.

1. Apply `http`.
2. Review the GET requests, POST requests, and response codes.
3. If the packet list is crowded, right-click unnecessary column headers, such as **DNS Name** or **Time to Live**.
4. Hide the columns temporarily or select **Remove this Column**.

#### Alternative Filter

- `tcp.port == 80` displays all TCP traffic involving port 80.
- `http` displays traffic Wireshark recognizes as HTTP, including HTTP on other ports.

### Step 2: Inspect Requested Resources

- Identify paths and file extensions associated with the suspicious communication.

1. Select an HTTP GET request.
2. Expand **Hypertext Transfer Protocol**.
3. Locate **Request URI**.
4. Review requests such as `swaging.php`, `.bin` files, and an `.exe` file.

#### Interpretation

- A request to a `.php` endpoint usually retrieves its generated response, not the PHP source code.
- A successful HTTP response alone does not establish whether the returned content is malicious.

### Step 3: Filter Selected File Extensions

- Display requests for paths ending in `.php`, `.exe`, `.bin`, or `.zip`.

1. Right-click **Request URI**.
2. Select **Prepare as Filter → Selected**.
3. Replace the exact comparison with `http.request.uri.path matches r"(?i)\.(php|exe|bin|zip)$"`.
4. Apply the filter.

#### Filter Meaning

- `matches` performs a regular-expression search.
- `(?i)` makes it case-insensitive.
- `\.` requires a literal period before the extension.
- `(php|exe|bin|zip)` matches any listed extension.
- `$` requires the extension at the end of the URI path.

#### Important Scope

- This filter identifies matching requests, not every response or transferred file.
- Using the path field allows requests with query parameters to match.
- The lesson’s broader pattern can match extension-like text elsewhere in a URI.

### Step 4: Investigate the Executable Download

- Identify the server involved in the suspicious download.

1. Select the GET request for the `.exe` file.
2. Expand **Internet Protocol Version 4**.
3. Review the destination address and destination GeoIP information.
4. Inspect the related HTTP response to confirm the transfer.

#### Lab Finding

- The lesson identifies the download server as located in Germany.
- The client retrieves an executable and additional binary files from this server.
- GeoIP is an approximate location indicator, not proof of the server operator’s identity.

### Step 5: Examine Subsequent POST Requests

- Investigate data sent outward after the downloads.

1. Review the later requests in timestamp order.
2. Apply `http.request.method == "POST"` to examine all recognized HTTP POST requests.
3. Compare their source, destination, URI, and visible request bodies.
4. Determine what data is being submitted.

#### Lab Finding

- The infected host begins sending POST requests to other servers after the downloads.
- The instructor identifies several destinations as being in Russia.

#### Interpretation

- POST requests may carry routine application data, malware check-ins, commands, or stolen information.
- Their timing and destination are clues; inspect the content and related evidence before concluding that exfiltration occurred.

### Step 6: Review Endpoint Locations

- Use endpoint statistics to summarize the systems involved in the filtered traffic.

1. Apply the extension filter or POST filter, depending on the activity being investigated.
2. Open **Statistics → Endpoints**.
3. Select **IPv4**.
4. Enable **Limit to display filter**.
5. Review the country information for the public IP addresses.

#### Scope Reminder

- The endpoint list reflects the current filter. An extension filter may omit POST requests to paths without those extensions.

### Step 7: Save the Extension Filter

- Keep the reusable expression under the Signatures menu.

1. Restore `http.request.uri.path matches r"(?i)\.(php|exe|bin|zip)$"`.
2. Click **+**.
3. Enter `Signatures//PHP EXE BIN ZIP`.
4. Save the filter.

### Optional: Export Objects for Analysis

- Wireshark can reconstruct HTTP objects for examination in an isolated malware-analysis environment.

1. Open **File → Export Objects → HTTP**.
2. Select the relevant object.
3. Save it only when needed for controlled analysis.
4. Do not run the extracted file.

#### External Analysis

- A service such as VirusTotal may help determine whether a file is already known as malicious.
- Do not submit confidential or sensitive material to public analysis services.

### Main Finding

- The lab connects suspicious HTTP downloads with later outbound POST activity.
- Cleartext HTTP exposes paths, headers, responses, and potentially transferred data; encrypted traffic requires decryption or other evidence sources for equivalent visibility.

</details>

<details>
<summary><strong>Lab 10 - Analysis of a Brute Force Attack</strong></summary>

## Lab 10 - Analysis of a Brute Force Attack

- Investigate repeated FTP login attempts, identify successful credentials, and create filters for successful and failed logins.
- Unencrypted FTP exposes usernames, passwords, and server responses in the capture.

### 1. Review the Lab Questions

- Open **Statistics → Capture File Properties** and identify:
  - Which usernames were attempted?
  - Which credentials worked?
  - Which filter identifies successful logins and can be reused?

### 2. Isolate an FTP Conversation

- The client opens several TCP connections to port `21`, spreading login attempts across multiple sessions.

1. Select **packet 49**.
2. Right-click → **Conversation Filter → TCP**.
3. Review the `USER` and `PASS` commands and the server responses.

### 3. Add an FTP Response Code Column

- A response code column makes login outcomes easier to scan.

1. Select an FTP response packet.
2. Expand **File Transfer Protocol** in the packet details.
3. Right-click **Response code → Apply as Column**.

| Response Code | Meaning |
|---|---|
| `220` | Service ready for a new user |
| `331` | Username received; password required |
| `230` | User successfully logged in |
| `530` | Not logged in; shown as “Login incorrect” in this capture |

- A password request (`331`) does not establish that authentication succeeded.

### 4. Examine Failed Login Attempts

- The lab shows these unsuccessful credential combinations:

| Username | Password | Result |
|---|---|---|
| `ADMIN` | `ADMIN` | Login incorrect |
| `FTPUSER` | `LOGIN` | Login incorrect |
| `msfadmin` | `ADMIN` | Login incorrect |

- Repeated credential guesses, frequent `530` responses, and many short or simultaneous FTP connections suggest brute force activity.

### 5. Find the Successful Login

- Filter for FTP responses reporting successful authentication.

1. Clear the conversation filter and apply:
   `ftp.response.code == 230`
2. Select a matching packet.
3. Right-click → **Follow → TCP Stream**.
4. Locate the preceding `USER` and `PASS` commands.

**Successful credentials in this lab:**

- Username: `msfadmin`
- Password: `msfadmin`
- Result: `230` — login successful.
- A later server error does not change the fact that authentication succeeded.

### 6. Save Reusable Login Filters

- Use these filters to investigate future FTP captures:

| Purpose | Display Filter |
|---|---|
| Successful logins | `ftp.response.code == 230` |
| Unsuccessful authentication responses | `ftp.response.code == 530` |

1. Select the FTP response code.
2. Right-click → **Prepare as Filter → Selected**.
3. Adjust the value to `230` or `530` and apply it.
4. Click **+** to save each filter, using labels such as `FTP//Successful Logins` and `FTP//Failed Logins`.

### Investigation Follow-Up

- Identify the client generating the attempts and whether the activity is authorized.
- Review successful sessions for subsequent commands, accessed files, and transfers.
- Repeated failures followed by success deserve investigation, but individual failed logins alone do not prove an attack.

</details>