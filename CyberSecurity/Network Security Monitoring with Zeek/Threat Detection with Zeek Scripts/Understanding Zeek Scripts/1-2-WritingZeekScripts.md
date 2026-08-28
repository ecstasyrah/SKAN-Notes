<details>
<summary><b>Managing Events with Frameworks</b></summary>

## The Logging Framework Overview
An introduction to how Zeek handles log output and structure.

### 1. Framework Characteristics
*   **Flexible Structure:** Zeek utilizes a highly flexible, key-value pair-based approach for logging.
*   **Event-Driven:** Any neutral or notable event that occurs on the network typically gets logged for additional analysis, monitoring, alerting, or record keeping.
*   **Extensibility:** The framework can be easily extended through various modules and integrations to fit specific organizational needs.

---

## Core Components of the Logging Interface
The logging framework relies on three interdependent components to function: Streams, Filters, and Writers.

### 1. Log Streams
*   **Definition:** The individual logs that Zeek maintains (e.g., the DNS stream or `weird.log`).
*   **Structure:** Each stream contains definitions for the specific fields that the resulting log will consist of.

### 2. Log Filters
*   **Function:** Determine exactly *what* information gets written to the logs and *how* it is handled (e.g., output destination, log rotation intervals).
*   **Default Behavior:** Every log stream has a default filter built-in that writes everything to disk. 
*   **Dependency:** If a log stream has no filters attached to it, it will not log anything. Custom filters can be added to refine this behavior.

### 3. Log Writers
*   **Function:** Attached to filters, writers define the output format and tell Zeek how to actually write the events.
*   **Formats:** The default format is ASCII, but writers can extend output to databases or platforms like Elasticsearch.
*   **Metadata:** Writers typically include metadata describing the file path, format, and other variables, which is useful for other Zeek processes.

</details>

<details>
<summary><b>Zeek Base and Policy Scripts</b></summary>

## Comparing Base and Policy Scripts
Understanding the architectural division of responsibility between base and policy scripts.

### 1. Functional Roles
*   **Base Scripts (`base/`):** 
    - Define what Zeek sees, how it ingests data, and how it logs network activity.
    - Responsible for analyzing raw packets, tracking requests, replies, headers, and messages, and writing standard logs.
*   **Policy Scripts (`policy/`):** 
    - Define how Zeek reacts to the data analyzed by the base scripts.
    - Responsible for raising notices, triggering alerts, performing policy actions (e.g., drops), and generating detections for security events, traffic spikes, and anomalous behaviors.

---

## Signatures and Protocol Detections
How policy scripts and signatures build on base protocol analysis.

### 1. Web Application Detection Example
*   **Base Layer:** The base HTTP scripts ingest and analyze HTTP packets (tracking headers, payload bodies, and session states).
*   **Policy Layer (`detect-webapps`):** Uses signatures (`.sig` files) rather than standard scripts to inspect fields like `http-reply-body`, response headers, and metadata.
*   **Detection Matching:** Matches specific web application signatures (e.g., Redmine, TYPO3, Plesk, Moodle, Google Analytics) and generates events when matches are found.

---

## Frameworks in Base vs. Policy
How frameworks operate at both layers of the Zeek hierarchy.

### 1. Base Frameworks
*   **Spicy Framework:** Acts as a customization and parser engine.
*   **Signature Framework:** Enables custom signature-based pattern matching.
*   **Notice Framework (Base):** Defines how notice records are created, populating fields such as MIME types, file information, and UIDs.

### 2. Policy Frameworks and Actions
*   **Policy-Level Notice Framework:** Implements actionable responses (e.g., dropping packets) via hooks.
*   **Inter-Framework Integration:** Interacts with frameworks like `NetControl` to manage actions such as catch-and-release or active blocking.
*   **Miscellaneous Policies (`policy/misc/`):** Contains diverse detections that expand beyond base capabilities to capture anomalous network behaviors.

</details>

<details>
<summary><b>Using the Logging Framework</b></summary>

## Logging Framework Components
The logging framework is built on three core components: writers, filters, and streams. Understanding how they interact is essential for manipulating Zeek's logging infrastructure.

### 1. Log Writers
*   **Function:** Determine the actual output format of the logs.
*   **Supported Formats:** The **ASCII writer** is the most common (and can be converted to JSON). Zeek also natively supports **SQLite** and integrations for platforms like **Elasticsearch**.
*   **Customization:** Writers are rarely modified directly unless a specific output format is required. However, they offer built-in options (detailed in the *Book of Zeek*) for formatting timestamps, writing natively in JSON, and detecting un-rotated logs.

---

### 2. Log Filters
*   **Function:** Define the exact parameters of what a stream will log and how it will be handled.
*   **Dependency:** A stream can have multiple filters attached, but if a stream has **no filters**, it will not write any output.
*   **Default Behavior:** Upon creation, streams are assigned a default filter (simply named `default`) that logs everything.
*   **Capabilities:** Filters can be customized globally or individually to:
    - Rename logs or change the output file path.
    - Send log output to multiple files simultaneously.
    - Define and manage log rotation processes.
    - Add new fields, or rewrite/rename existing columns in the final Zeek log output.

---

### 3. Log Streams
*   **Function:** The primary component used to create entirely new logs or modify the output of built-in logs.
*   **Requirements:** Creating a new stream requires defining a **record type** (which explicitly lists all the fields that will be logged) and a **log ID** (which uniquely identifies the stream).
*   **Execution:** Streams use attributes like `create_stream` to initialize the log and `write` to actively record the data.
*   **Event Driven:** To function properly, streams must be tied to specific events (either built-in global events or custom ones) to trigger the log generation.
*   **Optimization:** Streams can be entirely disabled if they are no longer needed, which can help optimize system performance.

</details>

<details>
<summary><b>Interacting with the Zeek Notice Framework</b></summary>

## Notice Framework Actions
The Notice Framework allows you to define specific actions to take when Zeek flags an event, bridging the gap between detection and reporting. 
*   **ACTION_LOG:** Writes the notice directly into the `Notice::LOG` logging stream (typically `notice.log`).
*   **ACTION_ALARM:** Writes to the `Notice::ALARM_LOG` stream (which rotates hourly) and emails the contents to the configured destination.
*   **ACTION_EMAIL:** Sends the notice immediately in an email to the configured addresses.
*   **ACTION_PAGE:** Sends a high-priority alert page or email to designated recipients.

## Customizing with Hooks and Policies
You can tailor the framework's behavior using policies, hooks, and specific record types to ensure you only receive the alerts that matter.
*   **Notice Policies:** Act as filters to determine which flagged situations are actionable and what specific actions to apply to them.
*   **Policy Hooks:** Multi-bodied functions used to modify notices—such as appending payload data or connection IDs—before they are sent onward to the action plugins.
*   **Prioritization:** Multiple policy hooks can have priorities applied to dictate their execution order (greater values execute first).
*   **The `Notice::Info` Record:** The primary record type that contains the context of the event (like message strings and sources) and is passed to the `NOTICE` function.
*   **Suppression:** Notices can be suppressed utilizing the `identifier` field in the `Notice::Info` record to prevent alert fatigue from repetitive events.

## Scripting Demo: SSH Brute Force Detection
A custom script can leverage the Notice Framework to actively track and alert on SSH brute-force attempts across the network.
1.  **Load Dependencies:** Import the Notice framework and the base SSH protocol analyzer at the top of the script.
2.  **Define Notice Type:** Create a custom `Notice::Type` (e.g., `SSH_Brute_Force`).
3.  **Track Attempts:** Build an event handler that increments an attempt counter for both successful and failed SSH authentications originating from a specific source IP.
4.  **Trigger Notice:** Use an `if` statement to evaluate the count; if the attempt counter exceeds a threshold (like 5), trigger the custom notice.
5.  **Deploy:** Append the `@load` statement to `local.zeek`, verify the configuration with `zeekctl check`, and apply the changes with `zeekctl deploy`.

</details>
