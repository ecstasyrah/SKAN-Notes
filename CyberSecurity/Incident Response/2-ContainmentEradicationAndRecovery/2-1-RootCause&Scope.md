<details>
<summary><strong>This Journey Is About the End</strong></summary>

## This Journey Is About the End

- Focus: completing containment, eradication, and recovery after investigating the Globomantics intrusion.
- These activities are iterative. New findings may require additional scoping, containment, or analysis.

### What Happened?

1. Ransomware infected devices and installed additional persistence, including web shells.
2. Initial triage identified affected systems and preserved forensic evidence.
3. Host analysis uncovered a malicious Office macro and additional command-and-control infrastructure.
4. Network analysis revealed lateral movement and connections from the satellite control server to the attacker.
5. Investigators restored the legitimate satellite application, resolving the immediate operational crisis.

### What Remains?

- Restoring the application did not remove the attacker.
- Responders must identify and address:
  - Active command-and-control connections.
  - Domains used to download additional payloads.
  - Internal web shells and other persistence.
  - Malicious files on affected endpoints.

### Exam Focus

- **Containment:** restrict the attacker's access and limit further damage.
- **Eradication:** remove malicious code, persistence, and causes of compromise.
- **Recovery:** restore trustworthy systems and business operations.
- Resolving an immediate business problem does not mean the incident is fully resolved.
- Coordinate containment and eradication to reduce opportunities for the attacker to change access methods.

</details>

<details>
<summary><strong>Demo: Scoping with Network Indicators</strong></summary>

## Demo: Scoping with Network Indicators

- Objective: identify devices communicating with known attacker infrastructure and devices hosting the known backdoor.

### Analyze Captured Traffic

- The lab uses `ext_traffic.pcap` from the `lab_ir_eandr` directory.
- `tcpdump` can read the capture and filter traffic directed toward the known C2 address.

| Option or Tool | Purpose |
|---|---|
| `tcpdump -r` | Read packets from an existing capture |
| `tcpdump -n` | Disable address-to-name resolution |
| `dst host` | Select packets going to a destination |
| `cut` | Extract selected fields |
| `sort` | Arrange values before deduplication |
| `uniq` | Remove adjacent duplicate values |

### Identify Unique Internal Sources

1. Filter traffic destined for the known C2 address.
2. Extract the source address field.
3. Remove the appended source port.
4. Sort and deduplicate the addresses.
5. Correlate the resulting hosts with asset and investigation records.

- The lab’s field extraction depends on tcpdump’s displayed IPv4 format.
- Outbound connections can permit return traffic through a stateful firewall.
- Traffic on port `443` is not automatically legitimate or necessarily HTTPS.

### Find Listening Web Shells

- The known ransomware backdoor listens on TCP port `8080`.
- Scan the authorized endpoint subnet to identify additional candidates.

| Nmap Option | Meaning |
|---|---|
| `-sS` | TCP SYN scan |
| `-Pn` | Skip host discovery and attempt the scan |
| `-p 8080` | Scan TCP port 8080 |
| `-oA` | Save normal, XML, and grepable output |

- SYN scanning typically requires elevated privileges.
- `-Pn` is useful when discovery probes are blocked.
- A `/24` specifies the network prefix length.
- The `.gnmap` file provides grepable output for extracting hosts with open ports.

### Lab Findings and Limits

- C2 traffic identified the initial foothold and satellite server.
- Port scanning identified the `.20` and `.30` devices associated with the ransomware web shells.
- **An open port alone does not confirm malware.** Validate the service and correlate other indicators.
- Passive captures can miss idle backdoors or activity outside the capture window.

</details>

<details>
<summary><strong>Understanding Final Scope Requirements</strong></summary>

## Understanding Final Scope Requirements

- Final scoping combines network and endpoint evidence to prepare coordinated eradication.

### Three Complementary Views

| Method | What It Finds | Main Limitation |
|---|---|---|
| Boundary traffic analysis | Active communication with known attacker infrastructure | Misses idle or unseen connections |
| Internal port scanning | Listening services such as the known web shell | An open port does not establish maliciousness |
| Endpoint signature hunting | Malicious files and related artifacts | Signatures may miss modified or unrelated payloads |

### Why File Hunting Is Necessary

- Ransomware may copy itself to additional locations.
- Renaming a file defeats filename-only searches.
- Changing file contents changes its cryptographic hash.
- Internal content signatures may still detect modified or renamed copies when the matching content remains.

### Exam Focus

- Network scoping alone does not establish complete endpoint scope.
- File presence, active execution, and network communication are different findings.
- “Final scope” is the best supported assessment at that point, not proof that every attacker artifact has been found.

</details>

<details>
<summary><strong>Demo: Scoping with File Signatures</strong></summary>

## Demo: Scoping with File Signatures

- Objective: develop content-based signatures and hunt for ransomware across endpoints.

### Extract Strings Without Executing the Malware

- `strings` extracts readable character sequences from a binary.
- This is a basic static-analysis technique.

| Command or Pattern | Purpose |
|---|---|
| `strings FILE` | Display readable strings |
| `strings -n 10 FILE` | Show strings at least 10 characters long |
| `strings -n 100 FILE` | Show strings at least 100 characters long |
| Piping to `more` | Review output one screen at a time |

### Select Useful Indicators

- Candidate strings in the lab include:
  - A Go build identifier.
  - The function name `friendlyLetter`.
  - Ransom-note text: `I'm not a trash panda`.

- Prefer distinctive strings over common content such as HTTP-related function names or the Windows DOS-stub message.
- A Go build identifier can help identify a particular build, but can change when software is rebuilt.
- No single string is guaranteed to survive an attacker’s modifications.

### YARA Rule Components

| Component | Purpose |
|---|---|
| Rule name | Identifies the rule that matched |
| `meta` | Descriptive information |
| `strings` | Patterns to search for |
| `condition` | Logic determining whether the rule matches |

- Matching **any** listed string increases coverage but can increase false positives.
- Requiring multiple distinctive strings can improve confidence but may miss modified samples.
- Validate detections before remediation.

### Deploy with Velociraptor

1. Create a hunt.
2. Select the Windows NTFS YARA detection artifact.
3. Configure the drive, paths, and file filters.
4. Insert the YARA rule.
5. Launch the hunt across the intended endpoints.
6. Review matching files and locations.

### Findings and Tradeoffs

- The lab identifies both the original executable and a renamed copy in `SysWOW64`.
- Restricting searches to `.exe` files reduces workload but misses other payload types or executables with different extensions.
- Narrow time or path filters improve speed but can exclude relevant evidence.

### Exam Focus

- **Filename search:** depends on the name remaining unchanged.
- **Hash search:** identifies exact file content.
- **YARA search:** identifies specified content patterns that may occur across multiple variants.

</details>

<details>
<summary><strong>Preparing Eradication Plans</strong></summary>

## Preparing Eradication Plans

- Objective: translate the scope assessment into a coordinated plan to remove attacker access and persistence.

### Planning Requirements

- Identify affected devices, access paths, malicious files, and persistence mechanisms.
- Block existing C2 channels and prevent initial-access payloads from reconnecting or downloading replacements.
- Coordinate network controls with endpoint remediation.
- Account for critical services, acceptable downtime, dependencies, and recovery requirements.

### Why Coordination Matters

- Partial remediation can alert the attacker while leaving alternative access available.
- Untreated endpoints may continue executing malicious tasks or communicating with attacker infrastructure.
- The goal is comprehensive, closely coordinated action across the affected environment.

### Roles

- **Responder:** provide technical findings, explain risks, and recommend actions.
- **Incident manager:** coordinate the response and decisions.
- **Organization and administrators:** authorize and implement changes according to operational needs and assigned responsibilities.

### Exam Focus

- Eradication planning balances security requirements with business continuity.
- Removing the attacker requires addressing both remote access and locally executing persistence.

</details>