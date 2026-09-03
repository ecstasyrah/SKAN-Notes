<details>
<summary><b>Zeek Detections with Signatures</b></summary>

## Introduction to Zeek and Threat Intelligence
An overview of Zeek's capabilities and its role in enhancing network security through intelligence.

### 1. Zeek's Core Capabilities
*   **Passive Network Monitoring:** Zeek is an open-source, passive network security monitoring tool capable of analyzing traffic as it crosses the network.
*   **Out-of-the-Box Visibility:** It natively monitors HTTP sessions, DNS conversations, and web applications without requiring complex initial configurations.
*   **Dual Functionality:** It can operate as a traditional Intrusion Detection System (IDS) or as a broader network traffic analyzer.
*   **Beyond Signatures:** While it supports standard signature-based detection, its default configurations rely on an event-driven scripting engine that expands its capabilities to include behavioral analysis.

### 2. Extensibility and Customization
*   **Scripting Architecture:** Zeek functions similarly to a programming language; the main process can call upon other scripts, signatures, and external integrations to perform additional tasks and enrich workflows.
*   **Course Framework:** The course focuses on utilizing the signature framework for static detections, the intel framework for data enrichment, and methods for customizing Zeek's automated responses.
*   **Prerequisites:** The only requirement to implement these features is an operating system capable of supporting a standard Zeek installation.

### 3. Intelligence vs. Threat Intelligence
*   **Network Security Use Cases:** As an IDS, Zeek is commonly deployed to detect specific network attacks, such as brute-force attempts, SQL injections, and malicious file downloads.
*   **The Need for Context:** Because human analysts cannot manually process data at network speeds, intelligence frameworks are required to automatically categorize and contextualize the data stream.
*   **Defining the Difference:** 
    - **General Intelligence:** Provides interesting context or telemetry about network data, regardless of whether it is threat-related.
    - **Threat Intelligence:** Provides specific, evidence-based context and indicators regarding potentially malicious activity.

</details>

<details>
<summary><b>The Signature Framework</b></summary>

## Overview of the Signature Framework
While Zeek primarily uses an event-driven scripting engine, it also includes a Signature Framework that functions similarly to a traditional Intrusion Detection System (IDS).

### 1. Core Functionality
*   **Traditional Analysis:** Provides static, signature-based detection capabilities to complement the scripting engine.
*   **Independent Operation:** Signatures operate separately from the rest of Zeek's core behavioral scripts and analysis.
*   **Use Cases:** Highly useful for checking compliance (e.g., verifying devices only use approved internal DNS servers) or matching known malicious patterns. If all criteria in a signature match, an action is triggered.

---

## Signature Conditions
Signatures use different types of criteria to evaluate and match values within the network traffic.

### 1. Header Conditions
*   **Function:** Inspects Layer 3 packet headers.
*   **Data Matched:** Evaluates basic network telemetry like source/destination IP addresses, ports, and protocols.

### 2. Content Conditions
*   **Function:** Uses regular expressions (regex) to match specific strings.
*   **Data Matched:** Can inspect raw payload data in a packet or dive into analyzer-specific content (such as matching specific criteria within an HTTP request).

### 3. Dependency Conditions
*   **Function:** Links multiple signatures together.
*   **Data Matched:** Requires a specific signature ID; the condition will only flag if the referenced signature has also been matched.

### 4. Context Conditions
*   **Function:** Leverages other components of the Zeek environment to compare signatures against broader contexts, greatly expanding the flexibility of standard signature rules.

---

## Signature Actions
When a signature successfully matches its defined conditions, it can trigger one of two available actions.

### 1. Event Action
*   **Function:** Raises an event to take a predefined response.
*   **Outcomes:** Typically generates a log message in the Zeek streams, but can be integrated to do more advanced tasks like sending an email alert via a plugin.

### 2. Enable Action
*   **Function:** Dynamically interacts with Zeek's processing engine.
*   **Outcomes:** Instructs Zeek to activate a specific protocol analyzer on the fly exactly when needed based on the observed event.

</details>

<details>
<summary><b>Detecting HTTP Activity</b></summary>

## Writing a Custom Signature
Building a basic signature to inspect payload content on a specific port.

### 1. Signature Configuration
*   **Creating the File:** The signature is defined in a standalone file (e.g., `user-agent.sig`) within the local site directory.
*   **Signature Layout (e.g., `found_root`):**
    - **Protocol & Port:** Targets TCP traffic (`ip-proto == tcp`) destined for a specific port (e.g., `dst-port == 8686`).
    - **Payload Matching:** Uses a regular expression to search the raw packet payload for a specific string (e.g., searching for the word `root`).
    - **Event Action:** If all conditions are met, it triggers an event with a predefined custom message (e.g., `event "Found root"`).

---

## Deploying and Testing the Signature
Loading the signature into Zeek's configuration and verifying its functionality against captured traffic.

### 1. Modifying `local.zeek`
*   **Framework Activation:** The base signature framework must be explicitly loaded by adding `@load base/frameworks/signatures` to `local.zeek`.
*   **Loading the Signature:** Instruct Zeek to load the newly created custom signature file (e.g., `@load ./user-agent.sig`).

### 2. Execution and Log Analysis
*   **Running the PCAP:** Execute Zeek against a saved packet capture file using the `-r` flag (e.g., `zeek -r sig1.pcap local.zeek`).
*   **Generated Logs:**
    - **`signatures.log`:** Records the matched signature event, including the source and destination IP addresses, ports, and the actual payload snippet that triggered the match.
    - **`notice.log`:** The signature framework automatically sends an alert notice to this log when a signature fires.
    - **`conn.log`:** Highly useful for cross-referencing broader connection details (such as byte counts, packet totals, and layer 2 addresses) for the flagged session.

---

## Practical Application
*   **User-Agent Auditing:** The same payload inspection logic can be used to scan HTTP traffic for specific User-Agent strings. This allows network administrators to detect when users are browsing with unauthorized software (like an unapproved version of Chrome or Firefox) that falls outside the enterprise baseline.

</details>

<details>
<summary><b>Detecting File Downloads via Signatures</b></summary>

## Creating a Broad Protocol Signature
Building a generalized signature to detect specific file type transfers (like PDFs) across any TCP port.

### 1. The PDF Download Signature (`http_pdf.sig`)
*   **Protocol Requirement:** Targets TCP traffic (`ip-proto == tcp`).
*   **Port Agnostic:** Unlike previous signatures, this intentionally omits a specific `dst-port`. This demonstrates Zeek's flexibility, allowing it to detect PDF transfers over *any* TCP port rather than just standard HTTP ports.
*   **Payload Matching:** Uses pattern matching to detect `.pdf` within the payload.
*   **Event Action:** Triggers the custom event message `"PDF was Downloaded"`.

---

## Deployment and Log Analysis
Loading the new signature and extracting context from the generated logs.

### 1. Updating `local.zeek`
*   **Script Management:** Add `@load ./http_pdf.sig` to your `local.zeek` file. 
*   **Noise Reduction:** To avoid log clutter, you can disable previously loaded signatures (like `found_root`) by commenting them out in `local.zeek`.
*   **Execution:** Replay the traffic using `zeek -r htp_pdf.pcap local.zeek`.

### 2. Log Verification and Insights
*   **`signatures.log`:** Confirms the `pdf_download` signature fired and displays the payload snippet containing the match.
*   **`notice.log`:** Records the alert alongside connection metadata (source/destination IPs and ports).
*   **Extracting Passive Metadata:** Inspecting the payload snippets in these logs often reveals additional environment context—such as identifying an Apache server running on Ubuntu. This passive intelligence can be used to write highly targeted regex signatures later.

---

## Broader Integrations and Use Cases
*   **File Analysis Framework:** While regex signatures are great for spotting extensions in a payload, Zeek's native File Analysis Framework can be used in tandem to actually extract those files and compute hashes for deeper inspection.
*   **Traffic Triage:** Signatures can act as a lightweight triage point, flagging interesting or suspicious traffic so it can be passed off to more resource-intensive protocol analyzers or Zeek modules for behavioral analysis.

</details>

<details>
<summary><b>Detecting Known Malware</b></summary>

## Transitioning to File-Based Detection
Moving beyond static payload string matching to detect known malware using file hashes and Zeek's scripting engine.

### 1. Scripting vs. Basic Signatures
*   **A Scripted Approach:** Instead of using the standard signature framework (`.sig` files), this method uses a custom Zeek script (`.zeek`) to leverage Zeek's built-in file analysis capabilities.
*   **Static Indicator, Dynamic Analysis:** While the detection remains static (triggering on a predefined, known malicious SHA-1 hash), the script dynamically hands off the payload to other Zeek analyzers to calculate that hash on the fly.

---

## Building the Malware Detection Script
Writing a script to extract files from HTTP traffic, compute their hashes, and alert on matches.

### 1. Script Logic and Construction
*   **Loading Dependencies:** The script must load the Notice Framework and the `hash-all-files` script from the File Analysis Framework.
*   **Defining the Target:** A constant is declared containing the specific SHA-1 hash of the known malware payload.
*   **Handling `file_new`:** When a new file is detected (specifically looking at the HTTP source), this event handler triggers the file analyzer to generate a SHA-1 hash for the transferred file.
*   **Handling `file_hash`:** Once the hash computation is complete, this event compares the newly calculated hash against the predefined target hash. If they match, a custom Notice (e.g., `Malware_Detected`) is triggered.

---

## Deployment and Incident Response
Executing the script and extracting actionable data from the resulting logs.

### 1. Configuration and Execution
*   **Updating `local.zeek`:** Add the `@load malware_detection.zeek` directive. To isolate testing and reduce log noise, disable previously loaded signatures (like `http_pdf`).
*   **Running Zeek:** Replay the traffic against the new configuration using `zeek -r htp_pdf.pcap local.zeek`.

### 2. Log Analysis
*   **`notice.log`:** Captures the `Malware_Detected` alert, confirming that the malicious hash was observed in transit.
*   **`http.log`:** Provides essential context for the alert, detailing the source/destination IPs, ports, and bytes transferred during the download.
*   **Extensibility for Incident Response:** While this basic script successfully alerts on the hash, it can be easily modified to inject specific HTTP connection data and file metadata directly into the final notice message, drastically speeding up triage and response times.

</details>