<details>
<summary><strong>Intel</strong></summary>

## Intel

- Threat intelligence enriches collected indicators of compromise (IOCs) with outside information to guide investigation and response decisions.
- Ideally, intelligence gathering runs alongside initial triage.

### Share Artifacts with Specialists

| Artifact | Intended Support |
|---|---|
| Malware samples and malicious code | Malware analysts |
| IP addresses, domains, hashes, filenames, and distinctive strings | Threat intelligence analysts |

- Package malware samples in password-protected archives and transfer them through approved channels.
- Teams without dedicated specialists may need responders to perform limited research.

### Keep Research Decision-Focused

- Spend enough time to answer the next response question: **Could leaving these devices infected allow further harm?**

1. Research the identified ransomware and its known behavior.
2. Look for follow-on actions, persistence, or destructive capabilities.
3. Compare intelligence with the evidence collected locally.
4. Report findings and uncertainties to the incident manager.

- Do not assume encryption was the attacker’s final action.
- Avoid prematurely wiping systems: evidence, recovery options, and additional attacker access still need consideration.

</details>

<details>
<summary><strong>Demo: Base64</strong></summary>

## Demo: Base64

- Decode suspicious strings and research related domains, files, and infrastructure to obtain useful leads quickly.
- The results below describe the course demonstration, not a current assessment of the domains.

### 1. Decode the Pop-Up String

- The instructor uses CyberChef’s **From Base64** operation.

1. Open a trusted CyberChef installation.
2. Paste the exact captured string into the input.
3. Add **From Base64** to the recipe.
4. Record the output alongside the original value.

**Lab result:**

- The string decodes to: “I'm Iron Cat and this computer is my litter box.”
- It reinforces the malware’s theme but provides little new investigative value.

### 2. Examine the Windows-Prefixed Key

- A separate string beginning with `Windows` does not produce meaningful text in the demonstrated decoding attempts.
- Preserve the original before testing a copy without its prefix or separator.

- A letters-only string can still be Base64.
- Unreadable decoded output does not prove that a value is not Base64; it may represent binary or encrypted data.
- Its purpose remains uncertain until additional evidence is found.

### 3. Check Domain Reputation

- Search for the recorded domain, `hello.iamironcat.com`, using available threat-intelligence sources.

| Source | Demonstrated Use |
|---|---|
| URLhaus | Search for reports involving malware-related URLs |
| VirusTotal | Review domain/URL detections and related artifacts |

- The initial searches return no useful direct detections in the lesson.
- No detections means no detection was reported in those results; it does not establish that the domain is safe.
- Do not upload confidential artifacts without authorization.

### 4. Explore Related Artifacts

- The instructor uses VirusTotal Graph to investigate relationships beyond the initial domain result.

1. Open the domain in the graph.
2. Expand related nodes.
3. Locate associated executable files.
4. Open their reports.
5. Review why each file is linked and what behavior was detected.

**Lab findings:**

- A related executable has **5 detections out of 67 engines** in the demonstrated report.
- The file is linked through its association with the domain.
- A behavioral rule reports **file deletion through the command line**.

- Detection counts and graph relationships are leads, not proof that the file is identical to the local sample.
- Investigate file creation, deletion, and possible self-deletion in the affected environment.

### 5. Review Domain and Infrastructure Details

| Item | Observation in the Lesson |
|---|---|
| Parent domain | `iamironcat.com` |
| Subdomain | `hello.iamironcat.com` |
| Registrar | GoDaddy |
| Registration history | Reported as dating to 2018 |
| DNS infrastructure | Azure-related nameservers |
| Hosting location | Reported as United States |
| ASN | `8075`, associated with Microsoft |
| Registrant information | Some details are unavailable or redacted |

- `.com` is the top-level domain; `iamironcat.com` is the registered domain.
- Nameservers identify DNS service infrastructure, not necessarily the domain owner or website host.
- Redacted registration details do not prove that paid privacy protection was purchased.
- Shared registrars or cloud providers are weak links between incidents, and GeoIP does not locate the attacker.

### 6. Examine the Payment Portal

- In the controlled lab, the site redirects to a ransomware payment page.

**Observed details:**

- A victim-key field resembles the Windows-prefixed string from the ransom note.
- The portal requests the victim’s or insurance agent’s email.
- A **uPlexa payment address** provides another searchable artifact.

- The matching field supports interpreting the string as a victim identifier; it does not establish how the identifier is generated.
- Preserve payment addresses, relevant text, and URLs for correlation.
- Observing the portal does not require submitting information or contacting the attacker.

### 7. Stop at the Useful Decision Point

- Record actionable findings and hand deeper research to specialists where available.
- Continue evidence collection and analysis rather than spending triage time on unrelated research paths.

</details>

<details>
<summary><strong>Keep Looking...</strong></summary>

## Keep Looking...

- Brief intelligence gathering provides additional leads, but it does not establish the attacker’s full capabilities or objectives.

### Findings to Carry Forward

- The demonstrated infrastructure is associated with Microsoft/Azure and US hosting.
- A related executable is named `ISP users.exe` in the transcript; verify the exact filename in the report.
- Behavioral reporting suggests investigating file creation and deletion.
- The previously discovered web shell shows that remote access is an additional concern beyond encryption.

### Avoid Unsupported Conclusions

- Cloud hosting or infrastructure near the victim does not, by itself, prove sophisticated attacker tradecraft.
- Infrastructure in another country does not automatically indicate poor operational security.
- Limited public reporting does not prove that the attack is targeted, uncommon, or conducted by an advanced actor.
- Treat these possibilities as hypotheses to test against local evidence.

### Next Steps

1. Consolidate the original IOCs and new intelligence leads.
2. Preserve source details and distinguish observations from assumptions.
3. Collect additional evidence from affected endpoints and relevant networks.
4. Investigate persistence, remote access, and activity beyond ransomware.
5. Refine the scope and response decisions as evidence develops.

- The immediate priority is obtaining the data needed for deeper host and network analysis.

</details>