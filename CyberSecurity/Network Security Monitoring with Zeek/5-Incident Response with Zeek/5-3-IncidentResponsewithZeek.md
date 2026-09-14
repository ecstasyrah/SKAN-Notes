<details>
<summary><b>Incident Response with Zeek</b></summary>

## The SANS Incident Response Process
Understanding the framework that guides effective incident response operations.

### 1. The Six SANS IR Steps
The module structures its investigation methodology around the standard SANS Incident Response process:
1.  **Preparation:** Readying the organization and tools before an incident occurs.
2.  **Identification (Detection):** Spotting the anomalous activity or security breach.
3.  **Containment:** Isolating the threat to prevent further damage.
4.  **Eradication:** Removing the root cause of the incident from the environment.
5.  **Recovery:** Restoring systems to normal, secure operations.
6.  **Lessons Learned:** Analyzing the incident to improve future defenses.

---

## Simulating an Attack Environment
Instead of providing a static PCAP, the course uses dynamic scripts to generate real-time network traffic, allowing for a more hands-on incident response simulation.

### 1. The `setup.sh` Script
This script prepares the local lab environment to run the attack simulation and detection exercises.

*   **Network Configuration:** 
    - Defines the local network parameters (e.g., `10.0.15.0/24`), the Zeek manager IP, and potential victim IPs.
*   **Threat Intelligence Definition:** 
    - Pre-populates a set of malicious IPs, domains, and PII (Personally Identifiable Information) files that Zeek will use as its threat intelligence feed during the simulation.
*   **Dependency Installation:** 
    - Installs necessary Python tools, specifically `scapy`, which is used to craft custom network packets and generate the simulated attack traffic.
*   **Fake Server Generation:** 
    - Spins up mock HTTP servers to ensure that when the attack scripts send requests, there is a responding service. This completes the connection cycle, ensuring Zeek's HTTP analysis engine kicks in properly.
*   **Documentation Output:** 
    - Generates on-screen documentation detailing the specific scenario, the theory behind the notices being triggered, and sample analysis searches to guide the investigation.

> Create scripts in main directory and run it to 

</details>

<details>
<summary><b>Running the Investigation</b></summary>

## Executing the Simulation
Running the scripts to generate the attack traffic and ensure Zeek captures the full network stream.

### 1. Generating Attack Traffic
*   **Running `run_detections.sh`:** 
    - This script automates the attack simulation by generating traffic for initial access, malicious DNS queries, data exfiltration (HTTP POST requests), and PII file uploads.
*   **Troubleshooting the C2 Server:** 
    - For Zeek's HTTP parser to effectively analyze the traffic, a full connection must be established. 
    - You must ensure the `c2_server.py` script is running in the background. If it is offline, Zeek will miss the HTTP streams and fail to generate the expected alerts.
    - After starting the server, redeploy the Zeek configurations to ensure it is actively sniffing.

---

## Log Analysis and Findings
Using command-line tools to parse Zeek's alerts and reconstruct the attack timeline from the logs.

### 1. Parsing the `notice.log`
*   **Filtering with `jq`:** 
    - The raw `notice.log` can be noisy. Using a command-line JSON processor like `jq` makes it much easier to sort through the data and identify specific alerts.
*   **Key Detections Observed:** 
    - **Initial Access:** A simulated click on a malicious email link directing the user to a known-bad IP address. *(Note: In production environments, Email Security and EDR tools act as the first line of defense before network monitors).*
    - **C2 Beaconing:** Outbound DNS queries to malicious domains and established connections to threat IPs.
    - **Data Exfiltration:** Suspicious HTTP POST requests attempting to upload sensitive personal data (PII) to the attacker's server.

### 2. The Importance of Threat Intelligence
*   **Data Dependency:** 
    - Zeek relies heavily on the threat intelligence feeds you provide. 
    - If your threat intelligence data is incomplete or outdated, Zeek will not flag the traffic, allowing malicious activity to slip through undetected. Your detections are only as good as your intel.

</details>

<details>
<summary><b>Investigating in Splunk</b></summary>

## Visualizing Logs in the SIEM
Transitioning from the command line interface to a graphical user interface using Splunk for log analysis.

### 1. Filtering Zeek Alerts
*   **Targeting the Notice Log:** 
    To focus solely on alerting information, analysts filter the search by specifying the `notice` log source type.
*   **Timeboxing the Search:** 
    Narrowing the search window (e.g., to the "last 30 minutes") helps isolate the simulated attack traffic from historical noise.

### 2. The Splunk Common Information Model (CIM)
*   **Field Normalization:** 
    Splunk automatically extracts fields (like destinations, event types, and originating hosts) and maps them to standard terms.
*   **Why it Matters:** 
    The Common Information Model (CIM) ensures that data from different sources uses a shared naming convention. This allows analysts to easily search, correlate, and visualize data across the enterprise without worrying about how different vendors label their logs. 

### 3. Troubleshooting Parsing Issues
*   **Bundled Events:** 
    Sometimes, external add-ons (like the Corelight or Zeek add-on) may improperly parse the incoming logs, grouping multiple distinct events into a single entry instead of splitting them.
*   **The Fix:** 
    To troubleshoot, analysts may need to temporarily disable the problematic add-on, restart the Splunk Universal Forwarder, redeploy Zeek, and regenerate the traffic to see if events split properly.
*   **Moving Forward:** 
    Even if events are temporarily bundled due to parsing errors, the raw JSON text remains accessible within the message fields, allowing the manual investigation to continue.

</details>

<details>
<summary><b>Wrapping up Incident Response</b></summary>

## Reconstructing the Attack Timeline
Building the full story of an incident requires pivoting from the initial alert through various data sources to identify the root cause.

### 1. Tracing the Alert Chain
*   **The Initial Detection:** 
    Investigations often begin with a high-severity alert, such as a Command-and-Control (C2) beacon being detected on the network.
*   **Pivoting to DNS Logs:** 
    By filtering the DNS analyzer logs for the originating host, analysts can review A or AAAA records to see the exact domains queried immediately preceding the C2 connection.
*   **Identifying Initial Access:** 
    Working backward through the network telemetry helps trace the activity to the very beginning, such as the initial HTTP request triggered by clicking a malicious URL in a phishing email.

---

## Navigating SIEM Data and Noise
Operating in a realistic SOC environment means dealing with large volumes of data and occasional ingestion errors.

### 1. Overcoming Data Parsing Issues
*   **Bundled Events:** 
    Sometimes data normalization fails, causing multiple distinct JSON events to be bundled together into a single log entry within the SIEM.
*   **Extracting Context:** 
    Despite parsing errors, analysts can still read the raw text of the logs or use command-line tools like `jq` to understand the field-value pairs and identify the attack criteria.
*   **Effective Filtering:** 
    To cut through the noise, always filter searches using specific detection names, originating host IPs, or known threat intelligence indicators.

---

## Mapping to the MITRE ATT&CK Framework
Contextualizing network detections against known adversary behaviors helps strengthen future defenses.

### 1. Applying the Framework
*   **Understanding the Tactics:** 
    SIEM applications often map alerts to the MITRE ATT&CK framework. For example, the simulated attack maps directly to Initial Access via Phishing with a malicious link (Technique T1566).
*   **Mitigation and Detection:** 
    The framework highlights how different layers of defense—such as Email Security gateways, endpoint EDR, and Network IPS—work together. Zeek's role focuses heavily on the network traffic detection phase, observing the behaviors that slip past the initial perimeter.

---

## Course Conclusion
A summary of the core concepts covered throughout the Zeek Incident Response path.

### 1. Key Takeaways
*   **Deployment Versatility:** 
    Zeek can be deployed as a standalone instance, scaled as a centralized cluster for large environments, or integrated natively into cloud platforms like Microsoft Defender.
*   **Deep Protocol Analysis:** 
    Zeek provides comprehensive visibility into critical domain protocols (LDAP, SMB, Kerberos) and enriches SIEM data to streamline investigations.
*   **Practical Incident Response:** 
    By leveraging custom scripts and threat intelligence, analysts can rely on Zeek to track, investigate, and build a narrative around complex network intrusions.

</details>