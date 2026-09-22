<details>
<summary><strong>Detection</strong></summary>

## Detection

- Independently validate the reported activity to determine whether it is a genuine incident and guide the next response actions.

### Collect Information Without Assuming It Is Correct

- Staff may unintentionally provide inaccurate descriptions or misuse terms such as brute force and DDoS.
- Confidence does not guarantee accuracy, but even mistaken accounts can provide useful clues.
- Listen carefully, take notes, and distinguish reported claims from verified evidence.

### Validate the Initial Report

1. Review the events originally identified by Globomantics.
2. Compare staff accounts with available technical evidence.
3. Confirm whether the activity is malicious or remains unexplained.
4. Record findings, uncertainties, and information still needed.

### Key Detection Questions

| Question | Why It Matters |
|---|---|
| Is this a confirmed incident or a collection of suspicious events? | Determines whether malicious activity has been established |
| Should affected devices remain connected or be isolated? | Balances intelligence collection against continued harm and evidence loss |
| Is there network activity? | Helps identify ongoing communication and potentially affected systems |
| Is the malware recognizable? | May guide investigation and selection of a response playbook |
| What appears to be the attacker’s intent? | Helps assess likely impact and prioritize action |

### Support Decision-Makers

- Technical responders gather evidence for the **IR lead or incident manager**, who may own key response decisions.
- Each decision depends on information collected during earlier activities.
- Explain what is known, what remains uncertain, and the consequences of available actions.

### How Findings Shape the Response

- Determine which incident response playbook to use.
- Inform containment and continued monitoring decisions.
- Support strategic and operational choices, including insurance involvement and ransomware-response planning.
- Help assess whether backups can be trusted and whether further attacker activity is expected.

</details>

<details>
<summary><strong>Initial Detection</strong></summary>

## Initial Detection

- Inspect an affected device to validate the reported ransomware activity and record initial indicators of compromise (IOCs).
- The lab intentionally makes some background behavior visible through pop-ups; real malware may show no such interface.

### 1. Examine the Ransom Note

- Record distinctive names, locations, instructions, and strings for later investigation.

| Observation | Investigative Value |
|---|---|
| `theinvincibleironcat` | Ransom-note title and potential IOC |
| Two copies of the note on the desktop | Possible repeated file creation; does not alone prove two executions |
| `C:\Users\Administrator\Desktop` | Confirmed location of affected files |
| `hello.iamironcat.com` | Advertised payment domain and potential network indicator |
| “I'm Ironcat,” “Jarvis,” and “Scorched Earth Protocol” | Distinctive strings for later intelligence searches |

- Preserve the domain as an indicator; its appearance in the note does not prove that the device contacted it.
- Themes and branding can help correlate reports but do not establish attacker identity.

### 2. Assess the Affected Files

1. Record changed filenames and extensions.
2. Check whether affected files remain readable using an appropriate viewer.
3. Document the affected directory and user account.
4. Correlate these observations with the ransom demand.

- The instructor demonstrates files presented as encrypted and no longer readable normally.
- An unfamiliar extension or unreadable content alone does not prove encryption; combined evidence supports the ransomware assessment.

### 3. Preserve Possible Device Identifiers

- The note contains a mixed-case alphabetic string associated with the word `Windows`.
- It may identify the victim, encode information, or relate to the environment, but its purpose is not yet confirmed.

1. Copy the exact string into investigation notes.
2. Preserve capitalization and surrounding context.
3. Analyze its format and purpose later.

- Mentioning Windows does not establish whether the malware also supports Linux.

### 4. Investigate Recurring Pop-Ups

- A pop-up appears around logon and returns after being dismissed.
- This suggests recurring execution and possible persistence.

1. Record when the pop-up appears.
2. Note whether it returns after dismissal or logon.
3. Preserve its text and any changing values.
4. Later investigate the process and startup mechanism responsible.

- Reappearance alone does not identify the persistence mechanism.

### 5. Record Suspected Base64 Content

- Another displayed string contains letters and numbers and is saved for later decoding.

- Base64 may include uppercase and lowercase letters, numbers, `+`, `/`, and trailing `=` padding.
- A string containing only letters can still be Base64.
- Digits or `=` padding are clues, not proof; preserve and test the complete value before classifying it.

### 6. Note Possible Beaconing

- The repeated activity occurs at a similar interval, suggesting a task running periodically.
- Periodic malware activity may involve communication with an external server, but the visual behavior alone does not confirm network traffic.

1. Record the timing of repeated activity.
2. Compare it later with running processes, connections, and packet captures.
3. Check for contact with the recorded domain or other destinations.

### Initial Findings and Next Steps

- The observed ransom demand and file changes support the reported ransomware incident.
- Initial evidence includes affected paths, note titles, a payment domain, distinctive strings, and possible victim identifiers.
- Persistence and network communication remain leads requiring verification.
- Search other systems for the collected indicators; affected user files on one host do not prove that other devices are infected.
- Stop this initial visual review once the findings are documented and use them to guide deeper analysis.

</details>

<details>
<summary><strong>Dig Deeper</strong></summary>

## Dig Deeper

- The initial review confirms malicious activity consistent with ransomware, but recurring activity after encryption requires further investigation.

### Current Findings

- User files on the initially examined device appear encrypted.
- The payment destination references **uPlexa Coin**.
- Notes about Ironcat provide leads for later threat-intelligence research.
- The full scope of the compromise remains unknown.

### Investigate Post-Encryption Activity

- Encryption is often a late-stage attacker objective, but it does not mean the attack has ended.
- Recurring pop-up activity may represent communication with attacker-controlled infrastructure.
- Network evidence is needed to determine what the malware continues to do.

### Decision Point: Isolate or Observe?

| Consideration | Why It Matters |
|---|---|
| Continued harm | Connected systems may still support data theft, further damage, or spread |
| Evidence collection | Brief observation may reveal destinations, timing, and other IOCs |
| Attacker awareness | Loss of communication could alert attackers to intervention |
| Ransom pressure | Attackers may respond with additional messages, deadlines, or demands |

- The instructor suggests that a short delay may not change already encrypted files, but this is a scenario-specific judgment—not assurance that waiting is safe.
- Decisions should account for ongoing activity, affected assets, and business impact.

### Scenario Decision

- The responder and incident response manager choose a temporary **watch-and-learn** approach until the next decision point.

1. Collect network connection information.
2. Identify destinations and recurring communication patterns.
3. Develop IOCs for the malware’s follow-on activity.
4. Search for matching indicators across the enterprise.
5. Reassess containment as new evidence becomes available.

</details>

<details>
<summary><strong>Triage Questions</strong></summary>

## Triage Questions

- Initial triage gathers the evidence needed for immediate response decisions, rather than a complete forensic explanation.
- In this scenario, responders are approximately **30 minutes to 2 hours** into the engagement, affected devices remain connected, and the threat has not been contained.

### Immediate Context

- The ransomware payment portal prompts management discussions about insurance and possible payment.
- Technical responders focus on understanding the active threat and its impact on Dark Energy satellite operations.

### Priority Questions

| Question | Purpose |
|---|---|
| Which initial IOCs identify this activity? | Search across the enterprise and estimate the scope |
| What network activity belongs to the malware? | Identify malicious connections and destinations |
| Is that activity ongoing? | Determine whether communication or attacker activity continues |
| Are backdoors or remote-access capabilities present? | Assess whether attackers can execute code or monitor the device |
| What else can be learned quickly from the live system? | Identify behavior that affects the next response decision |

### Keep the Investigation Focused

1. Deploy the prepared triage tools to the affected system.
2. Collect evidence relevant to the priority questions.
3. Record findings, uncertainties, and initial IOCs.
4. Provide the IR lead or incident manager with information needed for the next decision.
5. Preserve material for deeper analysis later.

### Questions for the Broader Investigation

- What was the root cause?
- How did the attackers gain access?
- Did they perform actions beyond encryption?
- Were servers also compromised?

- These questions remain important, but exhaustive investigation should not delay urgent decisions.
- Investigate them immediately when the findings could change containment or recovery priorities.

### Time Is the Main Constraint

- Detailed forensic analysis takes time; prioritize evidence with the greatest immediate decision value.
- The instructor’s “2% of the data tells 90% of the story” is an illustrative estimate, not a validated statistic.
- Deep analysis of forensic images comes later; initial triage should produce actionable findings quickly.

</details>

<details>
<summary><strong>Demo: Detect Initial Event</strong></summary>

## Demo: Detect Initial Event

- Perform focused live triage to preserve evidence, identify ransomware behavior, and investigate ongoing communication and possible backdoors.
- The supplied `Run-Initial-Triage.ps1` is a guided worksheet, not a script intended to run unattended from beginning to end.

### 1. Access the Response Kit

- Encrypted shortcuts may fail even when their target executables remain usable.

1. Open an accessible folder or use the Recycle Bin to reach File Explorer.
2. Navigate to the prepared `initial-triage` toolkit.
3. Launch intact executables directly if shortcuts fail.
4. Open the trusted lab folder and triage worksheet in VS Code.
5. Open a PowerShell terminal and run selected sections as needed.

- The lab treats the toolkit directory as removable media.
- Folder access, Recycle Bin access, and context menus remain functional in this example; this is not guaranteed for every ransomware infection.
- In VS Code, **Terminal → Run Selected Text** executes highlighted commands; **View → Word Wrap** improves readability.

### 2. Create a Device-Specific Evidence Folder

- Keep each host’s evidence separate, especially when several responders investigate multiple devices.

1. Record the computer name and domain.
2. Combine them into a meaningful folder name.
3. Create the collection directory.
4. Store its location in `$datapath`.
5. Verify that the directory exists before saving output.

- `$env:COMPUTERNAME` reads the computer-name environment variable.
- In PowerShell, use `Get-ChildItem Env:` to list environment variables.
- The command-prompt equivalent can be invoked as `cmd /c set`; bare `set` has a different meaning in PowerShell.

### 3. Establish an Activity Log

- The custom `captains_log` function writes actions, timestamps, and time-zone information to a **master-station-log**.

1. Load the logging function.
2. Write a test entry.
3. Confirm that the entry appears in the evidence folder.
4. Record each collection action, finding, and relevant error.

- This function is supplied by the lab; it is not a built-in PowerShell command.

### 4. Record Host Identity and Time

- Collect enough context to associate evidence with the correct device and correlate its timeline.

| Information | Purpose |
|---|---|
| System date and time | Establish the collection timeline |
| Time zone | Interpret timestamps accurately |
| Computer name and domain | Identify the host |
| Network interfaces and IPs | Identify physical, wireless, and virtual connectivity |
| OS information and architecture | Select compatible collection tools |

- The demonstrated host address is `172.31.37.30`.
- Multiple interfaces may indicate a dual-homed system or virtual networking; an interface alone does not establish what software is running.
- Preserve original timestamps and time zones, then normalize copies consistently during analysis.
- Do not change the affected system’s clock merely to standardize reporting.

### 5. Start Console Transcription

- Record terminal commands and output for later review.

1. Start `Start-Transcript`, saving the transcript under `$datapath`.
2. Run collection commands such as `Get-ComputerInfo`.
3. Confirm that output is being recorded.
4. Keep the session open during collection.
5. End transcription with `Stop-Transcript` when finished.

- A transcript supplements the activity log; it does not capture every GUI action or guarantee complete output from every external tool.
- The instructor also demonstrates Windows Steps Recorder (`psr`) for screenshots and interaction records, where available.

### 6. Capture Physical Memory Early

- RAM is volatile: it changes rapidly and is normally lost when power is removed.

1. Determine the system architecture.
2. Select a compatible WinPmem build from the toolkit.
3. Capture memory to the evidence location.
4. Record the acquisition time, tool, and output path.
5. Preserve the dump for later analysis with tools such as Volatility.

- Memoryze and Redline are mentioned as alternative tools; compatibility must be checked.
- Acquisition preserves memory from the collection period, not the original state when malware launched.
- Tool execution and memory acquisition themselves affect the live system; document those changes.

### 7. Preserve the Ransom Note

- The note provides content, metadata, and potential indicators for enterprise searches.

1. Record its full path, initially under `C:\Users\Administrator\Desktop`.
2. Collect file properties and timestamps.
3. Save the metadata separately.
4. Calculate and record a file hash.
5. Copy the note into the evidence folder.

- File timestamps help build a timeline but do not conclusively establish when ransomware first executed.
- The demo records MD5 for matching purposes; SHA-256 can also be recorded for stronger integrity checking.
- Identical file content produces the same hash regardless of filename; victim-specific changes can produce different hashes.

### 8. Search for Other Copies

- Search recursively to identify where the ransom note was dropped.

1. Search the filesystem for the note’s filename using `-Recurse`.
2. Record matching paths and access errors.
3. Note whether the search completed or was interrupted.
4. Use the observed locations to focus searches on other hosts.

**Lab finding:**

- The instructor confirms that this sample encrypts files and drops notes within `C:\Users`, including public-user folders.
- This affects valuable user data such as desktop files and documents.
- A partial search or access-denied result cannot prove that no copies exist elsewhere.
- Use the specific note and path pattern as an indicator; `C:\Users` alone is a normal Windows location.

### 9. Check for Cleared Logs

- Determine whether local logs can support the investigation.

1. Review available Windows event logs.
2. Look for **Security event ID 1102**, indicating that the Security audit log was cleared.
3. Record its timestamp and available account information.
4. Request centralized logs or other retained evidence when local history is missing.

- The lab shows evidence of log clearing.
- Event `1102` does not mean every Windows log was cleared, and clearing alone does not identify the responsible attacker.
- Remaining and newly generated records may still provide useful evidence.

### 10. Check Volume Shadow Copies

- Determine whether local snapshots remain available as a potential recovery source.

`vssadmin list shadows`

- No shadow copies are found in the demonstration.
- The instructor confirms that this ransomware deleted them.
- In other cases, an empty result alone does not prove deletion; snapshots may never have existed.
- Recovery planning must therefore consider other backups and recovery options.

### 11. Collect Network Connections

- Investigate the recurring behavior observed during initial detection.

1. Collect TCP connection and listener information with `Get-NetTCPConnection`.
2. Compare it with `netstat -anob`.
3. Use Sysinternals **TCPvcon** for additional process attribution.
4. Save the results, including structured output with `Export-Clixml` where useful.

- `Import-Clixml` can later reload the saved data for PowerShell analysis.

| Port | Context in the Investigation |
|---|---|
| `445` | SMB |
| `3389` | RDP, including the responder’s session |
| `5985` | WinRM HTTP |
| `5357` | Web Services for Devices HTTP |
| High-numbered ports | May be ordinary ephemeral or service ports |
| `8080` | Unexpected listener on this endpoint; requires investigation |

- A familiar port is not automatically safe, and an unusual port is not automatically malicious.
- The suspicious `8080` listener is associated with **PID 4756** in this capture.

### 12. Identify the Owning Processes

1. Use `Get-Process` to locate PID `4756`.
2. Inspect the process name, executable path, start time, and available properties.
3. Collect command-line arguments through process-management interfaces such as WMI/CIM.
4. Save the process information.
5. Preserve a copy of the suspicious executable without running the copied sample.

**Lab findings:**

- Two processes named `ironcatwuzhere` appear with PIDs `4756` and `5988`.
- The executable runs from a location under `C:\Windows\SysWOW64`.
- Different command-line arguments suggest different roles or modes.
- PIDs are temporary identifiers; they will differ between systems and executions.

### 13. Inspect the Process Relationships

- Use **Process Explorer** to examine the process tree and properties.

1. Locate both `ironcatwuzhere` processes.
2. Record their parent-child relationship.
3. Compare their command-line arguments.
4. Inspect available TCP/IP information.

- One process launches the other in the demonstration.
- Associations with service or task infrastructure provide persistence leads, but the actual startup mechanism still needs verification.

### 14. Investigate Recurring Outbound Connections

- Saved connection output shows repeated communication from the affected host to a destination on port `80`.

1. Record the destination, owning PID, connection states, and observation times.
2. Correlate the activity with the recurring pop-up.
3. Check relevant DNS information.
4. Compare the destination’s content with the ransom-note indicators using a controlled investigation method.

**Lab observations:**

- A reverse lookup returns no hostname; this alone is not evidence of maliciousness.
- The demonstrated destination displays IAMIRONCAT branding and a uPlexa payment page.
- The transcript gives `252.251.113.157`; verify the actual address in the lab evidence before treating it as a reusable IOC.

### 15. Assess the Port 8080 Listener

- The instructor accesses the affected host’s HTTP service on port `8080` and finds a web-shell interface.

- The educational demonstration shows command execution with administrator-level access.
- Do not issue commands merely to test a suspected live backdoor without considering authorization and evidence impact.
- A listener is not necessarily reachable from the internet; firewall rules and network placement determine exposure.
- Even an internal web shell may enable lateral movement or renewed attacker access.

### Collected Evidence and Initial Findings

| Evidence | Investigative Value |
|---|---|
| Activity log and transcript | Record responder actions and output |
| Host details and timestamps | Identify the device and support correlation |
| Memory dump | Preserve volatile evidence for later analysis |
| Ransom note, metadata, and hash | Support timeline development and indicator searches |
| Log-clearing evidence | Identify gaps and the need for alternative logs |
| Shadow-copy results | Inform recovery planning |
| Connections and process details | Link suspicious activity to executables |
| Preserved executable | Support later malware analysis |
| Port `8080` web shell | Identify a potential remote-access mechanism |

- These findings support the next containment and scoping decisions without requiring complete forensic analysis first.

</details>

<details>
<summary><strong>Demo: IOCs - Find Other Devices</strong></summary>

## Demo: IOCs - Find Other Devices

- Use initial indicators of compromise (IOCs) to identify additional affected systems and help the organization assess business impact.
- These early indicators will become more precise as the investigation develops.

### 1. Build a List of Devices

- The lab uses the ARP cache to identify another nearby device; an enterprise search needs a broader inventory.

| Method | Use | Limitation |
|---|---|---|
| `arp -a` | View cached local IP-to-MAC mappings | Does not list every device on the network |
| `Get-ADComputer -Filter *` | Retrieve Active Directory computer records | Requires appropriate access and AD tooling; records may be stale |
| Asset inventory | Define the authorized investigation scope | Must be checked for completeness and accuracy |

- The original affected host ends in `.30`; the additional lab host ends in `.20`.
- A `foreach` loop can apply checks to a device list, but the supplied worksheet is educational and requires adaptation before automation.

### 2. Check the Known Backdoor Port

- The malware previously exposed a web shell on TCP port `8080`.

1. Select a target from the device list.
2. Run `Test-NetConnection <target-IP> -Port 8080`.
3. Review `TcpTestSucceeded`.
4. Record reachable hosts for further validation.

- This TCP connectivity check does not require authentication to the target.
- A `True` result proves that the port is reachable, not that ransomware is present; legitimate applications can also use port `8080`.

### 3. Validate the Additional Host

- In the lab, the `.20` host exposes the same web-shell interface.
- The instructor runs `ipconfig` through the shell to confirm that it belongs to the second device.
- This is an educational demonstration; avoid executing commands through a live attacker backdoor merely to test it.

### 4. Check Other Initial IOCs

| Indicator | What to Search For |
|---|---|
| Affected file pattern | Files ending in `.encrypted` under `C:\Users` |
| Ransom-note hash | Files matching the collected note’s hash |
| Process name | `ironcatwuzhere` |
| Backdoor behavior | The identified web shell listening on port `8080` |

- Correlating multiple indicators provides stronger evidence than a port match alone.
- Remote file and process checks require appropriate permissions and a supported access method.
- The demonstrated PowerShell remoting approach requires authentication and WinRM configuration.

### 5. Record Initial Scope

1. Apply the checks across the authorized device list.
2. Separate confirmed infections from suspected matches.
3. Record unreachable systems and failed checks.
4. Provide the affected-device list to the incident manager.

- The organization maps these systems to business services to determine operational impact.
- Failed or negative checks do not guarantee that a device is clean.

</details>

<details>
<summary><strong>Pop Bottles</strong></summary>

## Pop Bottles

- Initial scoping provides an early damage assessment, but it does not complete the investigation.

### Use Findings to Assess Business Impact

- Searches may identify hundreds or thousands of potentially affected devices.
- Matching ransomware indicators suggest similar damage, but encryption on each device should not be assumed from a single weak indicator.

**The initial assessment helps management:**

- Connect affected devices to disrupted business operations.
- Prioritize systems and data for recovery or possible decryption.
- Determine which systems and information may no longer be trustworthy.
- Communicate the estimated damage to the wider organization.

### Recognize the Limits

- Initial indicators are developed quickly and may not detect every infection.
- Devices may respond differently because of access restrictions, configuration differences, or different attacker activity.
- These findings establish an initial scope, not the complete extent of compromise.

### Continue the Investigation

1. Gather intelligence about the adversary and its tactics, techniques, and procedures (TTPs).
2. Collect evidence from affected devices and relevant networks.
3. Refine detections and investigate inconsistent results.
4. Identify additional attacker access and persistence.
5. Investigate the intrusion’s root cause.
6. Use the findings to guide containment and eradication.

- Urgent containment may be necessary before the full scope or root cause is known.
- The next course activity is data collection, beginning with intelligence gathering.

</details>