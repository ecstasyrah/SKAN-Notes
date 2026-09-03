<details>
<summary><b>Enriching Zeek’s Data</b></summary>

## The Value of Context in Security Operations
Understanding why raw network data isn't enough and how threat intelligence enriches incident response.

### 1. The Problem with Raw Data
*   **Limited Visibility:** Relying solely on basic network telemetry (Layer 3 and 4 data like IPs, ports, and protocols) forces analysts to make critical block/allow decisions with incomplete information.
*   **False Positives:** Without context, benign administrative tasks can easily look like malicious activity, leading analysts down time-consuming rabbit holes.

### 2. Building the Full Picture (The ICMP Example)
A standard ICMP ping request looks suspicious without context. By layering intelligence, the true narrative emerges:
*   **Network Context:** `Host A` is pinging `Host B` exactly once per second.
*   **Endpoint Context:** Both assets are organization-owned, providing EDR (Endpoint Detection and Response) telemetry.
*   **Identity Context:** The ping was initiated by a known user ("Joe") using a standard terminal application.
*   **Process Intelligence:** The hash of the terminal application matches a clean, known-good executable (verifying it is not a rogue process masquerading as a terminal).
*   **Situational Context:** `Host B` is a Domain Controller currently undergoing a scheduled maintenance window.
*   **The Conclusion:** Instead of a network scanning attack, the context reveals an administrator simply waiting for a server to reboot. 

## The Zeek Intel Framework
*   **Function:** The Intel Framework automates the integration of this critical context. 
*   **Goal:** By utilizing threat intelligence feeds and contextual data, Zeek helps analysts skip the manual correlation process and immediately understand the intent behind network traffic.

</details>

<details>
<summary><b>General Intelligence vs. Threat Intelligence</b></summary>

## Defining the Terms
Understanding the distinction between broad data enrichment and security-specific context.

### 1. General Intelligence
*   **Purpose:** Provides broad data enrichment and context to help analysts understand network traffic.
*   **Examples:** Fully Qualified Domain Names (FQDNs), IP address ownership, and geolocation data.
*   **Value:** Helps build a baseline understanding of "who" and "where," but does not inherently indicate a security risk.

### 2. Threat Intelligence
*   **Purpose:** A specialized subset of intelligence designed specifically to help mitigate cyber attacks and malicious activity.
*   **Examples:** Known malicious file hashes, Command and Control (C2) server IP addresses, or infrastructure associated with specific Advanced Persistent Threat (APT) groups.
*   **Value:** Provides evidence-based indicators of risks, threats, and vulnerabilities, directly driving security actions (like blocking traffic or triggering incident response).

---

## Practical Security Applications
How threat intelligence changes the way organizations defend their networks.

### 1. Adding Context to Telemetry
*   **Scenario:** Observing a network connection to a foreign IP address might look suspicious but could be benign. 
*   **With Threat Intelligence:** If intelligence feeds identify that specific IP as a known C2 server in a region associated with an APT group, the traffic is instantly categorized as a high-priority threat rather than just anomalous behavior.

### 2. Automated Defense
*   **Next-Gen Firewalls:** Modern firewalls continuously ingest threat intelligence from large research teams to automatically block known threats in real-time.
*   **Efficient Operations:** Threat intelligence allows security teams to focus their investigations on verified indicators of compromise (IoCs) rather than hunting down false positives based on basic network telemetry.

---

## Integration with Zeek
Applying intelligence directly to Zeek's network monitoring.

### 1. The Intel Framework
*   **Function:** Zeek's Intel Framework allows the system to consume external threat intelligence platforms and streams.
*   **Outcome:** Instead of viewing a connection log containing raw IP addresses, Zeek cross-references those IPs against its loaded intelligence. If a match occurs, Zeek can dynamically append a field indicating the IP is malicious or suspicious, instantly enriching the log output.

</details>

<details>
<summary><b>The Zeek Intel Framework</b></summary>

## Overview of the Intel Framework
A look at the foundational components of Zeek's Intelligence Framework and how to configure it to ingest threat data.

### 1. Framework Mechanics
*   **The Documentation:** *The Book of Zeek* is the primary resource for exploring the specific functions and events of the Intelligence Framework.
*   **Intelligence Indicators:** Intel data typically consists of the actual indicator (like a domain name, e.g., `reddit.com`), the indicator type (e.g., `Intel::DOMAIN`), and the source of the metadata.
*   **Loading Intel Data:** To ingest plaintext intelligence files, you must specify the file path to Zeek using a `redef` statement in your configuration.

### 2. The "Seen" Scripts
For Zeek to compare network traffic against your loaded intelligence, it must be told what to look for.
*   **Function:** The `seen` scripts observe specific data points in the traffic (like file hashes, DNS queries, or HTTP URLs) and pass them to the Intel Framework for comparison.
*   **Loading the Scripts:** You must load these scripts into your configuration (typically by adding `@load frameworks/intel/seen` to your local script).
*   **Logging Matches:** Any time a piece of "seen" data matches an indicator in your loaded intelligence, Zeek logs the event in a dedicated `intel.log` file.

---

## Testing and Sandboxing
How to safely experiment with Zeek scripts and intelligence feeds.

### 1. Try.Zeek.org
*   **The Platform:** A web-based sandbox environment that allows you to test custom scripts, upload PCAP files, and run Zeek without needing a local installation.
*   **Practical Use:** You can use this platform to test custom intelligence files against sample traffic and immediately verify the output in the browser (such as checking the generated `intel.log` for successful matches).
*   **Extensibility Testing:** Because Zeek is event-driven, you can use the sandbox to practice writing automation logic—such as triggering a deeper packet capture or alerting an incident response workflow the moment an intelligence match occurs.

</details>

<details>
<summary><b>Creating Custom Data Files for Intel</b></summary>

## Structuring the Intelligence Data File
How to format a plaintext data file (`.dat`) so Zeek can ingest custom indicators of compromise (IoCs) like IP addresses and file hashes.

### 1. The `.dat` File Format
*   **Location:** Can be created anywhere, but typically stored in the local site directory (e.g., `ip_intel.dat`).
*   **Strict Syntax Rule:** The Zeek Intel Framework requires precise formatting. Every field within the data file must be separated by a **single tab character**, not spaces. Using spaces will cause the ingestion to fail.
*   **Content:** The file defines the indicator itself (like an IP address or SHA-1 hash), the indicator type, and contextual metadata (such as the source of the intelligence).

---

## Configuring Zeek to Ingest the Data
Updating Zeek's configuration to actively monitor traffic against the custom intelligence file.

### 1. Modifying `local.zeek`
*   **Enable Frameworks:** Add load directives for the base Intel Framework and the necessary `seen` scripts (e.g., `@load frameworks/intel/seen`).
*   **Point to the File:** Use a `redef` statement to tell Zeek exactly where the data file is located using the `Intel::read_files` method.
*   **Define Event Triggers:** While you can script this directly in `local.zeek`, best practice dictates creating separate scripts for event modifications (like hooking into `new_connection` events to check originator and responder IPs) to keep the main configuration clean.

---

## The Automated Detection Workflow
Testing the configuration and understanding how Zeek processes the data from end to end.

### 1. Testing with PCAPs
*   **Execution:** Run Zeek against a packet capture containing known malicious traffic (e.g., `zeek -r hash_intel.pcap local.zeek`).
*   **The `intel.log` Output:** If a match occurs, Zeek generates an `intel.log` file. This log details the matched indicator, the indicator type, exactly where it was seen in the traffic (e.g., the responding IP of a connection), and the manual source metadata you defined in the `.dat` file.

### 2. Zeek's Automated Pipeline
When a user downloads a malicious file, Zeek automates what would otherwise be a highly manual incident response process:
1.  **Traffic Observation:** Zeek analyzes the raw HTTP stream.
2.  **File Hashing:** It detects the file download and passes it to the File Analyzer, which automatically computes the file's hash (and can extract the file if configured).
3.  **Intelligence Matching:** The hash and connection metadata are handed to the Intel Framework, which instantly compares them against the loaded threat intelligence data.
4.  **Alerting:** Upon a match, it generates the `intel.log` entry and can simultaneously trigger an alert via the Notice Framework.

</details>

<details>
<summary><b>Adding Domain Data</b></summary>

## Monitoring Domain and URL Indicators
Expanding the threat intelligence scope beyond IPs and hashes to track malicious or suspicious domains.

### 1. The `Intel::DOMAIN` Indicator
*   **File Modification:** By commenting out previous IP and hash indicators in the `.dat` file, you can introduce domain indicators (e.g., `popcash.net`).
*   **Protocol Granularity:** Using `Intel::DOMAIN` tracks the domain name, whereas using `Intel::URL` can track the full URI/URL path the client is requesting.

### 2. Analyzer Dependencies
To successfully match intelligence data against network traffic, Zeek must have the appropriate analyzers enabled to actually "see" the traffic.
*   **DNS Traffic:** Requires the DNS analyzer to observe domain requests.
*   **HTTP Traffic:** Requires the HTTP analyzer to observe domains in HTTP Host headers.
*   **Encrypted Traffic (HTTPS):** Full URL visibility often requires an encrypted visibility engine or a decryption mechanism, though domain names can sometimes still be observed in DNS queries or TLS SNI fields.

---

## Log Analysis and Fallback Mechanisms
Understanding how Zeek captures intel matches across different protocol streams.

### 1. Multi-Protocol Detection
*   **Testing with `domain.pcap`:** Running Zeek against this capture file populates both `dns.log` and `intel.log`.
*   **The `intel.log` Output:** When inspecting the intel log, you may see the same domain flagged multiple times under different contexts. 
    - **HTTP Host Header:** The domain is flagged during the HTTP request.
    - **DNS Request:** The domain is flagged during the initial name resolution.
*   **The Fallback Advantage:** This multi-vector detection acts as a safety net. If HTTP traffic is encrypted and Zeek cannot inspect the Host header, it will still flag the malicious domain during the unencrypted DNS request phase, assuming the DNS analyzer is active.

---

## Scalability and Automation
Moving from manual entry to dynamic threat intelligence operations.

### 1. Dynamic Intel Integration
*   **Automated Ingestion:** While manually editing `.dat` files is useful for testing, production environments leverage the Intel Framework to automatically download and ingest dynamic threat intelligence from commercial or open-source providers (such as Cisco Talos or ThreatConnect).
*   **Operational Value:** Automating this context sharing reduces manual analysis, increases system robustness, and ensures the network monitoring system is always operating with the most up-to-date threat indicators.
*   **Testing Platform:** Continue leveraging `try.zeek.org` or your local sandbox to experiment with different indicator types (IP, Hash, Domain, URL) to fully grasp the framework's capabilities.

</details>