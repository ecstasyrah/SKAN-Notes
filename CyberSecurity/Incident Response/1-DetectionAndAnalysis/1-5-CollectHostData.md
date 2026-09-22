<details>
<summary><strong>Collection</strong></summary>

## Collection

- Collect a **triage image**: a focused set of evidence from a live system for later analysis.
- Full disk imaging may take too long for immediate response, and shutting down critical systems can interrupt operations and destroy volatile evidence.

### Preserve Malware Samples

- Collect all relevant samples, including alternate versions, scripts, and supporting files.
- Package samples in password-protected archives and transfer them through approved channels to malware analysts.
- The instructor describes finding six ransomware variants, including a debug build that revealed useful operational details.

### Follow the Order of Volatility

- Volatile evidence includes data lost at shutdown and information that changes rapidly during normal operation.

| General Collection Priority | Examples |
|---|---|
| Network activity | Traffic passing through the network |
| Memory | Running processes and other RAM contents |
| Live operating-system state | Connections, routes, sessions, and active configuration |
| Filesystem and registry | Temporary files, malware artifacts, and relevant registry data |
| External service logs | Centralized logging, application logs, and directory-service records |
| Full disk image | Broader evidence for detailed forensic analysis |

- This order is a guide, not a rigid sequence; collect in parallel when possible and prioritize evidence at immediate risk.
- External logs may rotate quickly and need prompt preservation.
- Live disk imaging is possible with suitable tools, but changing data and system impact must be considered.

### Scope the Collection

1. Use initial scoping results to identify affected systems.
2. Collect relevant evidence from as many of those systems as feasible.
3. Automate repeatable collection tasks.
4. Record actions, errors, and collection times.
5. Preserve evidence for deeper analysis.

</details>

<details>
<summary><strong>Collect Host Data</strong></summary>

## Collect Host Data

- Collect host artifacts for later analysis without investigating every result immediately.
- The demonstration covers host collection first and addresses network capture separately.

### 1. Resume Documentation

1. Reestablish `$datapath` if the previous PowerShell session was closed.
2. Confirm that it points to the correct device-specific evidence folder.
3. Start or resume console transcription.
4. Add a `captains_log` entry recording the collection start time and time zone.

- `captains_log` is the lab’s custom logging function.
- Save important outputs separately as well as recording terminal activity.

### 2. Collect System Information

- Use `Get-ComputerInfo` or the command-line alternative `systeminfo`.
- Preserve host identity, operating-system details, architecture, and configuration.

### 3. Preserve DNS, Neighbor, and Routing Data

| Evidence | Example Tool | Investigative Value |
|---|---|---|
| DNS cache | `Get-DnsClientCache` | Recent cached hostname-to-address information |
| ARP/neighbor cache | `arp -a` or `Get-NetNeighbor` | Nearby address mappings |
| Routing table | `Get-NetRoute` or `route print` | Active routes and possible traffic redirection |

1. Run the supported collection command.
2. Review enough output to confirm collection worked.
3. Save it under `$datapath`.
4. Record unavailable commands and use a tested alternative.

- The lab’s DNS cache links the Ironcat domain to an IP address.
- DNS cache entries describe names, not complete URLs, and do not alone prove that a connection occurred.
- Command availability depends on the operating system, installed modules, and environment—not only the PowerShell version.
- Temporary routes may disappear after restart, making early collection useful.

### 4. Export Firewall Configuration

- Preserve local firewall rules and relevant configuration.

- Attackers may modify rules to support a web shell, remote access, or traffic forwarding.
- Save the output for later review rather than analyzing a large ruleset during collection.

### 5. Collect Accounts and Logon Sessions

| Tool or View | Purpose |
|---|---|
| Local Users and Groups (`lusrmgr.msc`) | Inspect local accounts and group membership where supported |
| `net localgroup Administrators` | List local administrator-group members |
| PsLoggedOn | Identify logged-on users |
| LogonSessions | Record detailed logon-session information |
| `Get-ADUser -Filter *` | Retrieve AD user records when authorized and AD tooling is available |

- Preserve enabled accounts, privileged memberships, session identifiers, and authentication information.
- Domain membership does not eliminate local accounts on ordinary member systems; domain controllers handle accounts differently.
- Session identifiers can help correlate authentication and activity across logs.

### 6. Record Network Share Connections

- Run `net use` to list network resource connections visible in the current context.
- Use `net share` when collecting shares hosted by the local machine.

- Ransomware may encrypt accessible shared files or use network access during propagation.
- An empty `net use` result does not prove that the machine has no hosted shares or that other users have no connections.

### 7. Collect Services

1. Run `sc.exe query` or `cmd /c sc query`.
2. Collect additional service properties through WMI/CIM where needed.
3. Save service names, statuses, executable paths, and other available configuration.

- In Windows PowerShell, bare `sc` can resolve to `Set-Content`; specify `sc.exe` to avoid the alias.
- Services are a common persistence mechanism.
- Different collection methods may expose different useful properties.

### 8. Preserve Persistence Information

- Use **Autoruns** to collect many startup and persistence-related locations, including services, drivers, print monitors, and scheduled tasks.

1. Launch Autoruns.
2. Let enumeration finish.
3. Save its results into the evidence folder.
4. Collect scheduled-task details separately using the command line.

- The saved Autoruns data can be reviewed later.
- Autoruns provides broad coverage but does not guarantee identification of every persistence mechanism.

### 9. Collect Disk and Filesystem Information

| Tool or Evidence | Purpose |
|---|---|
| NTFSInfo | Report NTFS volume information and metadata layout |
| DiskMon | Observe disk read/write activity during collection |
| Volume information | Identify the relevant disks and volumes |

- Specify the correct drive, such as `C:`, when required.
- Save any captured disk activity and metadata output.
- NTFS volume information is not a substitute for collecting the MFT or a forensic image.
- VolumeID is a utility capable of changing volume serial numbers; do not use it to alter evidence during collection.

### 10. Collect Loaded Modules and Handles

- Use **ListDLLs** to record modules loaded by processes.
- Collect process handles to identify referenced resources, such as files, registry keys, and other system objects.
- These artifacts can help explain malware behavior during later host analysis.

### 11. Preserve Windows Event Logs

1. Collect relevant event information using `Get-WinEvent` or the command-line tool `wevtutil`.
2. Create a dedicated event-log subfolder under `$datapath`.
3. Export available logs in their native `.evtx` format.
4. Record logs that could not be collected.

- Windows event-log files are commonly stored in `C:\Windows\System32\winevt\Logs`.
- Directly copying active files may fail because they are in use; supported export methods are preferable.
- Preserve logs beyond Security because other channels may retain evidence after an attacker clears selected logs.

### 12. Collect Additional Text Logs

- Review relevant files under `C:\Windows\System32\LogFiles` and other application-specific locations.
- Preserve available logs and document access errors.

**Firewall logs:**

- When logging is configured, firewall logs may show permitted or dropped traffic.
- Their contents depend on logging settings and retention; enabling the firewall does not automatically log every connection.
- Compare collected logs across hosts to investigate communication between systems.

- System32 is not guaranteed to be untouched by ransomware.
- `-ErrorAction Continue` continues execution while displaying errors; it does not suppress them.

### 13. Plan Deeper Acquisition

- After focused collection, determine whether full disk imaging and additional artifact acquisition are needed.
- Potential follow-up evidence includes registry hives, ShimCache, and filesystem metadata.
- The transcript’s “NTFS.dit” likely refers to **`NTDS.dit`**, the Active Directory database found on domain controllers, not a general NTFS artifact.

### Collection Outcome

- Preserve the gathered host evidence, activity log, transcript, and any collection limitations.
- Use this dataset for deeper analysis of persistence, attacker activity, and affected systems.

</details>