<details>
<summary><b>Customizing Zeek Logging</b></summary>

## Alternative Logging Formats
While Zeek uses a TSV (ASCII) format natively, logs can be converted into other formats like XML, SQLite, or JSON to better suit different environments.

### 1. Format Comparisons
*   **JSON (Recommended):** 
    - Highly extensible and widely used across many applications.
    - Easier to read and filter due to its structured nature.
    - Easily ingested by SIEMs, SOAR platforms, and other automation tools.
*   **SQLite:** 
    - Stores data in a database format.
    - Beneficial for recalling specific information, including field-value pairs.
*   **XML:** 
    - A structured data format, though it can be more difficult to parse compared to the others.

---

## Tooling and Configuration
How to manage and view customized log formats effectively.

### 1. The jq Tool
*   **Purpose:** 
    - A command-line JSON processor used to format and print JSON data in a much more human-readable way.
    - Helps analysts explore Zeek's data without getting lost in unformatted text strings.

### 2. Configuration Options
*   **Changing Formats:** 
    - Modify the `local.zeek` script or enable the specific policy tuning script for JSON logging.
*   **Flexibility vs. Consistency:** 
    - Formats can be configured on a script-by-script basis, but sticking to a single format across all logs is recommended unless a specific use case requires otherwise.

### 3. Log Streaming
*   **Centralized Logging:** 
    - Organizations commonly stream logs to a centralized server for processing.
*   **Zeek and Kafka:** 
    - Kafka is a tool designed to handle streaming telemetry pipelines centrally.
    - The `zeek-kafka` package easily integrates Zeek with Kafka to streamline log shipping.

</details>

<details>
<summary><b>Configuring JSON Logging</b></summary>

## Enabling JSON Logs in Zeek
Converting the default TSV logging format globally to JSON format via local site configurations.

### 1. Site Scripts and `local.zeek`
*   **Directory Location:** 
    - Located in `/opt/zeek/share/zeek/site` (or the equivalent installation share path).
    - Scripts stored in this directory are persistent and will not be overwritten during Zeek upgrades.
*   **Configurable Options:** 
    - Enable or disable specific protocol analyzers, script directories, threat detection scripts (e.g., Heartbleed), file hashing (SHA-1), and geolocation data.
    - Added configurations like `mac-logging` and `vlan-logging`.

### 2. Enabling the JSON Policy
*   **Policy Import:** 
    - Add `@load policy/tuning/json-logs.zeek` to `local.zeek`.
*   **How It Works:** 
    - Redefines the standard ASCII log writer to output JSON key-value pairs instead of tab-separated values.
*   **Redeploying Zeek:** 
    - Apply the changes by running `zeekctl deploy`.
    - Redeploying automatically archives existing logs and begins writing new events in JSON format.

---

## Viewing and Parsing JSON Logs
Using external tools to format and read JSON output in the command line.

### 1. The `jq` Utility
*   **Installation:** 
    - Can be installed via the package manager (e.g., `sudo apt install jq`).
*   **Usage Syntax:** 
    - Run `jq . <log_file>` (such as `jq . weird.log`) inside `/opt/zeek/logs/current/`.
*   **Benefits:** 
    - Formats condensed JSON into pretty-printed, structured key-value pairs on separate lines for easy reading and analysis.

### configurations

```bash
cd /opt/zeek/share/zeek/site/
ls # search for the local.zeek
sudo nano local.zeek
#turn on vlan logging and mac logging; add:

@load policy/tuning/json-logs.zeek
#save

cat /opt/zeek/share/zeek/policy/tuning/json-logs.zeek
#change to json format instead of zeek
sudo zeekctl deploy

cd /opt/zeel/logs/current
ls

# Download jq and activate jq formatting
sudo apt update && sudo apt install jq -y
jq . <log>

```
</details>

<details>
<summary><b>Network Analysis with Zeek</b></summary>

## Fundamentals of Network Analysis
Exploring how Zeek operates as a core network analysis tool to provide deep operational insights.

### 1. Core Capabilities
*   **Definition:** 
    - The process of identifying and documenting hosts and their activities on the network.
*   **Use Cases:** 
    - Monitoring specific ports and protocols, reviewing link utilization, checking for policy compliance, and establishing behavioral baselines.

---

## Security and Protocol Visibility
Leveraging network analysis specifically for a strong security posture.

### 1. Key Security Concepts
*   **Visibility:** 
    - You cannot secure what you cannot see; Zeek provides this necessary visibility.
*   **Consistency:** 
    - Reliable data is required to build accurate baselines and detect anomalies.
*   **Protocol Understanding:** 
    - Knowing how devices and protocols are supposed to communicate is critical to differentiating normal activity from potential threats.

### 2. Identifying Protocol Anomalies
*   **Legitimate Variations:** 
    - Standard HTTPS uses TCP 443, but QUIC (HTTP/3) uses UDP 443. An untuned system might classify this as a mismatch, highlighting the need to understand modern protocols to avoid blocking valid traffic.
*   **Malicious Mismatches:** 
    - Zeek scripts and analyzers can detect when services run on non-standard ports (such as DNS on port 8000 instead of 53, or SSH on port 5555) by recognizing that the traffic behavior does not match the port's normal profile.

---

## Advanced and Custom Use Cases
Expanding Zeek's capabilities beyond traditional intrusion detection.

### 1. Expanding Functionality
*   **Custom Tooling:** 
    - Zeek can be customized with add-ons, additional analyzers, and custom signatures.
*   **Creative Applications:** 
    - Can be used to detect and alert on traffic spikes, or even trigger auto-scaling for an application based on the volume of network traffic observed.

</details>

<details>
<summary><b>Demo: Detecting Port and Protocol Mismatches</b></summary>

## Scenario Setup: Generating a Protocol Mismatch
Simulating network traffic where the protocol does not align with the standard expected port.

### 1. Generating Non-Standard Traffic
*   **Target Server:** 
    - A server in the environment is running Netcat and listening on port 21.
*   **Mismatch Generation:** 
    - A silent `curl` command is executed to connect to the server over HTTPS using port 21.
    - This creates a protocol mismatch because common ports, protocols, and services do not match up (HTTPS uses port 443, not 21).

---

## Log Analysis and Verification
Using Zeek logs and `jq` filtering to trace and verify the anomalous connection.

### 1. Identifying the Mismatch Across Logs
*   **dpd.log (Dynamic Protocol Detection):** 
    - Typically shows weird activity or protocol errors, but in this instance, it only displayed unrelated DNS failures.
*   **known_services.log:** 
    - Highlighted the anomaly by recording TCP port 21 paired with SSL as the service, which does not make sense from a protocol analysis point of view.
*   **conn.log:** 
    - Contains full connection metadata (source/destination MAC addresses, IPs, ports, packets, and the TCP protocol). 
    - A specific `jq` filter was used to extract the exact log message by targeting the destination port (using `select` where `id.resp.p` equals 21).
*   **weird.log & notice.log:** 
    - These logs did not display events or special notices for this mismatch because no custom signatures or notifications were created for it.
*   **http.log:** 
    - Remained empty for this event because an HTTP service was not detected; it was treated as a typical SSL connection.
*   **ssl.log:** 
    - Filtering this log with the exact same `jq` search for port 21 successfully displayed the SSL connections that attempted to create the mismatched connection.

### Configuration

```bash
cat /opt/zeek/logs/current/known_services.log


```

</details>

<details>
<summary><b>Long-tail Anomalies</b></summary>

## The Concept of Long-tail Anomalies
Understanding what long-tail data is and why it matters from a security perspective.

### 1. Defining the Threat
*   **Unnatural Behavior:** 
    - Focuses on detecting hosts in a network that communicate infrequently or generate very little traffic.
*   **Malicious Indicators:** 
    - Anomalies in this "long tail" of traffic can indicate data exfiltration, compromised hosts, or slow, distributed scanning designed to avoid detection.
*   **The Needle in a Haystack:** 
    - Threat actors attempt to hide this significantly weird, low-volume communication within a massive haystack of normal, expected network traffic.

---

## Detecting Anomalies in Connection Data
Using `jq` to extract, sort, and count rare occurrences in core connection logs.

### 1. Analyzing `conn.log`
*   **Destination Ports (`id.resp_p`):** 
    - Sorting by unique counts reveals baselines (e.g., 777 counts of standard port 443) alongside rare, potentially anomalous ports (e.g., only 11 counts of port 136).
*   **Services (`service`):** 
    - Filtering services can isolate rare occurrences—like a single SSH or Kerberos TCP connection—hidden among massive amounts of standard DNS or unrecognized (null) traffic.

---

## Analyzing Application and SSL Logs
Applying the same sorting methodology to application-layer logs to spot outliers.

### 1. HTTP and DNS Traffic
*   **HTTP Anomalies (`http.log`):** 
    - Checking destination ports (`id.resp_p`) exposed a single anomaly communicating over port 40,000, standing out against standard port 80 traffic.
*   **DNS Queries (`dns.log`):** 
    - Counting occurrences of the `query` field helps identify rare, potentially malicious DNS requests hidden within routine queries.

### 2. SSL Traffic
*   **SSL Logs (`ssl.log`):** 
    - Filtering by `id.resp_p` typically validates standard ports (443, 8443).
    - Sorting by `server_name` counts allows you to identify rare or highly unusual server connections.


### Configuration

``` bash
jq -e '."id.resp_p"' /opt/zeek/logs/current/conn.log | sort | uniq -c | sort
jq -e '."<field>"' /opt/zeek/logs/current/<log> |sort | uniq -c | sort -n
# ports not used are redflags
jq -e '."id.resp_p"' /opt/zeek/logs/current/conn.log | sort | uniq -c | sort

```

</details>