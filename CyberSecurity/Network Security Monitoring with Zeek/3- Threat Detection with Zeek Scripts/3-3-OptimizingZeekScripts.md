<details>
<summary><b>General Zeek Practices</b></summary>

## Best Practices for Customization
Core operational rules to maintain system stability and preserve custom logic during updates.

### 1. File Modification Rules
*   **Do Not Edit Base Scripts:** Never modify files directly inside the `base/` directory. System updates and patches can overwrite these files, wiping out custom changes.
*   **Use the Site Directory:** Place all custom scripts and configurations in the `site/` folder and load or redefine them via `local.zeek`.
*   **Policy Scripts are Situational:** Scripts in the `policy/` directory (e.g., detecting TCP scans) are intended for specific use cases and are not all loaded by default; they must be explicitly loaded via `local.zeek` or manual execution.

---

## The Connection Record Data Type
The foundational data structure for Zeek network analysis and state tracking.

### 1. The Backbone of Zeek Scripts
*   **Core Role:** The `connection` record is passed into many major event handlers (e.g., `c: connection`) across protocols.
*   **Lifecycle Stages:** Defined across three key connection states:
    - When a new connection is established/created.
    - When a connection times out.
    - When a connection record is closed and removed.
*   **Usage:** Essential for identifying metadata such as source/destination IP addresses, ports, protocols, and connection durations.

---

## Key Initialization and Configuration Scripts
Understanding the script hierarchy and boot profiles within Zeek.

### 1. `init-bare.zeek`
*   **Function:** Declares core types, variables, vectors, and the fundamental `connection` record definitions.
*   **Bare Mode:** Loaded when Zeek is invoked with the `-b` (bare mode) command-line flag, stripping away higher-level analysis features for lightweight execution.

### 2. `init-default.zeek`
*   **Function:** Loaded by default under standard operations.
*   **Scope:** Activates primary frameworks, utilities, standard protocol analyzers (HTTP, DNS, SSH), and file analysis capabilities.

### 3. `local.zeek`
*   **Function:** The primary aggregation and entry point for custom operational settings and script management.
*   **Usage:** Uses `@load` directives to enable specific policy scripts and site-specific custom detection modules.
*   **Documentation:** Best practice is to document and comment within `local.zeek` explaining which scripts are loaded and their organizational purpose.

</details>

<details>
<summary><b>Optimizing Built-in Scripts</b></summary>

## Understanding Base Script Functionality
Zeek relies heavily on its built-in base scripts to provide out-of-the-box value. These scripts handle everything from packet protocol processing to defining events.

### 1. The `base/` Directory and Core Scripts
*   **Essential Components:** The `base/` directory contains critical files like `init-bare.zeek`, which imports telemetry functions, binary functions, protocol analyzers, and definitions for anomalous ("weird") network events.
*   **Built-in Functions (BIFs) and Frameworks:** This directory also houses the necessary frameworks and BIFs required for core detection and logging capabilities.

---

## Customizing and Tuning Frameworks
While base scripts are foundational, their default behaviors can be optimized to suit specific environments without editing the source files directly.

### 1. Utilizing Redefinitions (`redef`)
*   **Modifying Behavior:** You can use the `redef` attribute to change how logging and notices operate. 
*   **Practical Examples:** Redefinitions can be used to disable printing hooks (stopping Zeek from logging directly to the terminal), rename informational records, alter data types, or completely turn off specific logging streams.
*   **The Weird Framework:** By inspecting scripts like the weird notice framework, you can see how tables define various anomalous events and use redefinitions via policy hooks to alter how Zeek handles them.

### 2. Event Tuning and Automation
*   **Initialization Events:** You can write custom scripts in `local.zeek` that hook into initialization events (e.g., `zeek_init()`). This allows you to verify and print exactly which optimized configurations are loaded when Zeek starts.
*   **Expanded Use Cases:** This tuning can be expanded to specific security events. For instance, if an invalid TCP checksum or SSH brute-force activity is detected, an event handler can trigger an immediate alert to a SIEM, a monitoring dashboard, or a terminal for real-time visibility.

</details>

<details>
<summary><b>Script Writing Best Practices</b></summary>

## Writing Explicit and Resilient Scripts
Adhering to script design standards ensures reliable detections and prevents unexpected crashes.

### 1. Specificity and Clear Naming
*   **Be Explicit:** Avoid overly broad rules that create excessive alert noise or accidentally tune out critical events. 
*   **Descriptive Identifiers:** Use clear, descriptive labels for notice types (e.g., `SSH_Brute_Force` instead of a vague `Brute_Force`) so the reason for an alert is immediately obvious.
*   **Preserving Global Defaults:** When overriding settings (such as failure threshold counts) in custom scripts, ensure you preserve the default fallback values.

### 2. Handling Missing Fields and Preventing Crashes
*   **Defensive Checking:** Zeek can crash or throw runtime errors when attempting to access unpopulated record fields.
*   **Conditional Guards:** Use `if/else` statements to verify a field exists before performing operations on it, allowing Zeek to gracefully move on and continue processing.

---

## Performance Optimization and Resource Management
Techniques to streamline script execution and reduce unnecessary overhead on production systems.

### 1. Analyzer and State Management
*   **Disable Unused Analyzers:** Because Zeek is event-driven, analyzing protocols not present or relevant in your environment wastes valuable resources. For instance, disable the SIP analyzer if your organization does not use SIP.
*   **Connection State Cleanup:** Use commands like `connection_state_remove` to manage resources and avoid loading unnecessary state tracking or analyzers.

### 2. Debugging Hygiene
*   **Clean Up Debug Code:** Temporary print or debug statements consume resources that could be used for deeper traffic analysis. Always remove or comment out debug statements once testing and troubleshooting are complete.

---

## Utilizing the Zeek Script Reference
Navigating the official documentation to find exact syntax and definitions.

### 1. Key Documentation Sections
*   **Script Reference:** Contains exhaustive details on data types, analyzer types, scripting architecture, and built-in frameworks.
*   **Cross-Linked Declarations and Attributes:** Directly inspect declarations like `redef` and logging functions like `Log::create_stream` to view default values, parameter requirements, and implementation examples.


</details>

<details>
<summary><b>Zeek Tuning Scripts</b></summary>

## Built-in Tuning and Optimization
Overview of the default scripts provided by Zeek to help optimize and format output without requiring granular code modifications.

### 1. The `policy/tuning/` Directory
*   **Common Modifications:** Contains scripts designed for broad environment tuning and configuration adjustments (often utilizing `redef` statements).
*   **JSON Logs:** Scripts modifying output formats to JSON make logs easier to view or ingest into external SIEM and monitoring tools.
*   **Asset Tracking:** The `track-all-assets` script automates the discovery process by tracking known hosts, active services, and observed certificates.
*   **Threshold Management:** Other tuning scripts exist to adjust system limits, such as file extraction thresholds and packet fragmentation handling.

---

## Creating Custom Optimization Scripts
How to write tailored optimization logic to manage state and prevent Zeek from becoming overloaded.

### 1. State and Resource Management
*   **Connection Cleanup:** In an event-driven system, efficiently deleting stale connections is crucial so Zeek has the memory and processing capacity to analyze new traffic.
*   **Custom Script Example (`optimization.zeek`):** 
    - You can create scripts to monitor specific traffic metrics, such as global SSH hit counts or port hit counts.
    - Instead of letting Zeek track these indefinitely, the script can actively remove the connection state once the count is incremented.
*   **Timeout Expirations:** Scripts can enforce strict timeouts (e.g., if a connection state exceeds 3600 seconds, explicitly expire and remove it from the state table).
*   **Memory Management:** Custom functions can be written to actively expire entire tables and delete stale key-value pairs from memory databases to prevent exhaustion.

### 2. Scaling Beyond Scripts
*   **Clustering:** If software tuning, state management, and timer reductions are not enough to handle heavy traffic loads, the environment must be scaled horizontally by clustering the Zeek system and deploying additional worker nodes and analyzers.

#### Simple optimization script
```bash
event connetion_state_remove(c: connection)
{
    if ( c$id$resp_p == 22/tcp )
    {
        ssh_hits[c$id$orig_h] += 1;
    }
}
event zeek_done()
{
    local f = open("ssh_hits.log");
    print f, "# SSH connection attempt counts:";
    for(ip in ssh_hits)
    {
        print f, fmt("%s\t%d", ip, ssh_hits[ip]);
    }
    close(f);
}

event connection_state_remove(c: connection)
{
    port_hits[c$id$orig_h] += 1;

    if ( network_time() % 3600 == 0){
        expire_table(port_hits);
    }    
}

```
</details>

<details>
<summary><b>Additional Zeek Optimizations</b></summary>

## Infrastructure and Performance Tuning
Architectural methods to enhance Zeek's operational efficiency and integrate it into enterprise environments.

### 1. Host and Resource Allocation
*   **CPU Pinning (Affinity):** 
    - Bind Zeek processes to specific CPU cores when sharing hardware with other security tools to maintain predictable, high-performance monitoring.
*   **Automated Maintenance:** 
    - Schedule resource-intensive tasks (e.g., database cleanup, log archival) using timers during non-peak hours to avoid performance spikes.

### 2. Centralized Log Shipping & Integrations
*   **Avoid Manual CLI Inspections:** 
    - Forward logs directly to a centralized SIEM or log management system rather than inspecting logs manually on the sensor.
*   **Forwarding Tools:** 
    - Ingest and parse logs with utilities like `syslog-ng`, the Splunk Universal Forwarder, or the Elastic Stack (ELK).
*   **Contextual Enrichment:** 
    - Expand Zeek's utility by integrating external context: Threat Intelligence feeds, ticketing systems, and Full Packet Capture (PCAP) solutions.

---

## Code-Level and Memory Optimization
Refining script logic to reduce CPU cycles and manage memory consumption.

### 1. Regex and Pattern Matching Efficiency
*   **Precompile Regular Expressions:** 
    - Precompile recurring regex patterns to eliminate repetitive compilation overhead during active event inspection.
*   **Containment vs. Exact Match:** 
    - Use containment operators like `in` instead of complex `match` functions when checking if a pattern exists within a string.
*   **Avoid Tight Loops:** 
    - Minimize or eliminate tight loops over regex strings across high-throughput data streams.

### 2. Large Stateful Tables
*   Manage memory usage in stateful tables by implementing explicit table expiration policies and clearing out stale entries.

---

## Course Summary & Key Takeaways
A review of the primary concepts covered across the *Threat Detection with Zeek Scripts* course:

*   **Scripting Architecture:** Learned script layout, load directives, event queues/handlers, base vs. policy layers, and the difference between global and local scopes.
*   **Frameworks in Action:** Leveraged the **Logging Framework** (streams, filters, writers) and the **Notice Framework** (`Notice::Info`, actions, policy hooks).
*   **Custom Threat Detection:** Built and deployed custom detection scripts for **SSH brute-force attacks** and **DNS string/domain monitoring**.
*   **Tuning & Best Practices:** Followed non-destructive modification practices via `local.zeek`, implemented defensive coding against missing fields, and optimized analyzers and memory timers.
*   **Key Resources:** Official **Zeek Documentation** (Script Reference) and **try.zeek.org** for sandbox testing and scripting practice.

</details>