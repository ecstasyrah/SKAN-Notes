<details>
<summary><b>Scripting with Zeek</b></summary>

## Introduction to Zeek and Scripting
An overview of Zeek's core functionalities and how scripting enhances its threat detection capabilities.

### 1. Core Capabilities
*   **Broad Visibility:** Passively monitors and analyzes network traffic out of the box, including HTTP sessions, DNS conversations, and web applications.
*   **Advanced Detection:** Functions as both an Intrusion Detection System (IDS) and a general network traffic analyzer.
*   **Beyond Signatures:** Leverages behavioral analysis and pattern recognition rather than relying solely on traditional signature-based detection.

### 2. The Power of Scripting
*   **Granular Customization:** Allows administrators to fully customize deployments by toggling specific detection methods and analysis types on or off.
*   **Extensibility:** Expands original capabilities by allowing Zeek to call upon external data sources or systems for added context.
*   **Behavioral Tracking:** Empowers defenders to actively hunt for irregular patterns and unnatural behaviors that do not trigger standard signatures.

---

## Architecture and Data Processing
How Zeek processes network traffic through its internal engines to generate security intelligence.

### 1. Internal Architecture
*   **Event Engine:** Uses various subcomponents to convert raw network packets into higher-level, actionable events.
*   **Subcomponents:** Includes dedicated modules for input sources, packet analysis, session analysis, and file analysis.
*   **Plug-in Architecture:** Processes core protocols like SSH and HTTP while allowing users to build and integrate custom components.

### 2. Network Security Data Types
*   **The Four Data Types:** Network monitoring consists of full content (recording traffic), transaction data (summarizing traffic), extracted content, and alert data (judging traffic).
*   **Zeek's Focus:** While capable of collecting extracted content and alert data, Zeek does not natively record full packet content (PCAPs) out of the box. Its primary strength and focus reside in processing and analyzing transaction data.

</details>

<details>
<summary><b>Learning About Zeek Scripts</b></summary>

### Zeek Script Resources
https://docs.zeek.org/en/current/script-reference/scripts.html
https://docs.zeek.org/en/current/script-reference/index.html
https://docs.zeek.org/en/current/frameworks/index.html


## Script Files and Resources
Understanding file extensions, script loading methods, and core documentation resources.

### 1. Script Types and Loading
*   **File Extensions:** 
    - Signatures use the `.sig` extension.
    - Scripts use the `.zeek` extension.
*   **Loading Methods:** 
    - Scripts can be loaded individually or as entire directories (which automatically processes the `__load__.zeek` file inside that folder).

### 2. Key Documentation Resources
*   **Script Index:** 
    - Catalogs information about every built-in script available in Zeek.
*   **Script Reference:** 
    - Details all options, variables, attributes, analyzers, and log files.
*   **Framework Section (Book of Zeek):** 
    - Provides in-depth explanations and operational guidance for each built-in framework.

---

## Anatomy of a Zeek Script
Breaking down the core sections of a Zeek script using the traceroute script structure.

### 1. Load Directives and Namespace
*   **`@load` Statements:** 
    - Directives placed at the top to import necessary libraries, frameworks, and supporting scripts.
*   **Module Definition:** 
    - Establishes the namespace for the script to prevent naming collisions.

### 2. The Export Block (`export`)
*   **Log Record Types:** 
    - Defines log fields and types (using `&log`) to structure the resulting log records.
*   **Framework Constraints:** 
    - Defines variables and settings for interacting with frameworks like the Notice framework.
*   **Constants:** 
    - Establishes configuration constants and thresholds (e.g., ICMP time-exceeded limits and time intervals).

### 3. Event Handlers
*   **Event Instructions:** 
    - Contains the logic and execution instructions that trigger when specific network events occur.
*   **Initialization (`zeek_init`):** 
    - Sets up initial parameters, links tokens to declared constants, and creates new log streams.
*   **Specific Triggers:** 
    - Handles detections such as signature matches for low TTL packets or ICMP time-exceeded events.

</details>

<details>
<summary><b>Event Queues and Handling</b></summary>

## Event-Driven Architecture and Built-in Functions
Zeek operates on an event-driven scripting language where network traffic is converted into events based on protocol analyzers and active scripts.

### 1. The Role of Events and `.bif` Files
*   **Event Generation:** 
    - As Zeek processes packets, protocol analyzers generate events for connections, requests, responses, and server flags.
*   **Built-in Functions (`.bif`):** 
    - Files where developers document and define how specific events are detected and generated for each protocol or analyzer (e.g., RDP session events).
    - Custom event definitions can also be added.

---

## Event Queues and Event Handlers
The core components that manage the flow and processing of generated events.

### 1. Queuing and Execution
*   **Event Queue:** 
    - Holds all observed events in the order they occur (First Come, First Served / first seen to last seen).
*   **Event Handlers:** 
    - Functions defined in Zeek scripts that consume events from the queue and execute specific actions or logic when an event triggers.

---

## Base Scripts vs. Policy Scripts
Understanding the structural organization within `/opt/zeek/share/zeek/`.

### 1. Base Scripts (`base/`)
*   **Purpose:** 
    - Focuses on foundational data processing, loading default frameworks, handling certificate/file analysis, and logging baseline protocol states (e.g., logging HTTP traffic if a state is pending).
*   **File Extension:** 
    - Uses the `.zeek` extension across all script files.

### 2. Policy Scripts (`policy/`)
*   **Purpose:** 
    - Focuses on taking action, performing detections, and extracting deeper context from the data processed by the base scripts.
*   **Example Application:** 
    - Parsing HTTP headers to identify specific software types or hardware details.

</details>

<details>
<summary><b>Zeek Script Manipulation</b></summary>

## Variables and Constants
Zeek extracts intelligence from network traffic and uses specific data structures to manipulate scripts and process that information.

### 1. Variable Types
*   **Global Variables:** 
    - Can traverse different scripts and are used globally across the deployment. 
    - Must be called using the module name defined in the first section of the script.
*   **Local Variables:** 
    - Only available within their local scope (such as within an individual event body or function). 
    - They are deleted once they are no longer in use.
*   **Constants:** 
    - Can be set and altered when Zeek is parsing information, but become immutable (cannot be changed) during runtime.
    - Can be redefined globally in the `local.zeek` script or locally within individual scripts.

---

## Data Types and Structures
Zeek uses a variety of data types to define network elements and establish logic.

### 1. Atomic Data Types
*   Common programming data types such as **integers**, **floating-point numbers**, and **booleans** (true/false statements).
*   Network-specific data types, including **ports**, **subnets**, and **times**.
*   **Regular Expressions (Regex)** can also be utilized as a core data type.

### 2. Complex Data Structures
*   **Sets:** 
    - Used as containers for elements of a specific data type (like a set of interesting IP addresses or ports).
    - Formatted as `variable_name: set[type]`.
    - Frequently used within `if/then` statements to trigger events or print information.
*   **Tables and Vectors:** 
    - Other primary complex structures used for organizing Zeek data (though not covered in depth in this specific module).

---

## Demo: Building a Custom Notice Script
Creating a custom script (`notice_http.zeek`) to alert administrators whenever an HTTP request contains "/admin/" in the URI.

### 1. Script Logic and Construction
*   **Load Statements:** 
    - The script calls on necessary base scripts, specifically the Notice framework and the base HTTP protocol analyzer.
*   **Event Definition:** 
    - Redefines a custom notice event labeled `Custom_HTTP_Admin_Seen`.
*   **Detection Criteria:** 
    - Uses an `if` statement to look for the string `/admin/` within any detected HTTP request URI.
*   **Alert Generation:** 
    - If detected, it sends an informational notice to the framework with the message: *"An admin URI was detected."*

### 2. Deployment and Testing
*   **Loading the Script:** 
    - The new script is added to `local.zeek` using a `@load notice_http` statement (the `.zeek` extension is not required).
*   **Verification:** 
    - Running a `curl` command with "/admin/" in the URL successfully triggers the rule.
*   **Log Output:** 
    - Checking the `notice.log` (parsed with `jq`) confirms the detection, displaying the custom note, the message, and the associated ports, protocols, and IP addresses.

![](./img/image.png)

### Configurations
```bash
#after adding the script above 
sudo nano /opt/zeek/share/zeek/site/local.zeek
# add
@load ./notice_http #save and quit

#then check if there are no errors
sudo zeekctl check
sudo zeekctl deploy

curl http://10.98.2.107/admin/test
#check logs
jq . /opt/zeek/logs/current/notice.log

```

</details>

<details>
<summary><b>Zeek Scripting Keys</b></summary>

## Core Building Blocks of Zeek Scripts
An overview of the essential components used to build and manipulate Zeek scripts, including operators, types, attributes, declarations, and directives.

![alt text](./img/image-2.png)
### 1. Operators
*   **Variety of Functions:** Zeek supports relational, logical, arithmetic, set, and pattern operators.
*   **Advanced Checks:** Includes conditional operators and membership tests (e.g., verifying if a specific value exists within a set).

### 2. Data Types
*   **Standard Types:** Includes numeric (integers) and non-numeric (strings) data types.
*   **Complex Types:** Features container types (records, sets), executable types (events, hooks), file types, enumerations, and pattern types (regex).
*   **Temporal Types:** The `interval` type is used for relative time (e.g., a numeric constant followed by seconds or minutes). This is highly useful for defining time windows, such as looking for an event occurring within the past 4 hours.
![alt text](./img/image-3.png)

### 3. Attributes
*   **Defining Data:** Attributes help define and modify the data Zeek interacts with, always mapping to actual data values.
*   **`redef`:** Allows the redefinition of global variables and constants, such as log file names.
*   **`log`:** Instructs Zeek to write a specific record field or container type out to the associated log stream.
![alt text](./img/image-4.png)

### 4. Declarations and Statements
*   **Declarations:** Used globally to identify variables, constants, and modules (e.g., using `redef` to alter values or types globally).
![alt text](./img/image-5.png)
*   **Statements:** Executed after global declarations to control script logic. Common statements include `local` (to define local scope variables), `add`, `delete`, `print`, and loop/conditional statements like `for`, `while`, and `if`.
![alt text](./img/image-6.png)


### 5. Directives
*   **Execution Control:** Directives dictate which scripts or frameworks are loaded and which specific lines of code are executed.
*   **Loading Resources:** Directives like `@load` and `@load-sigs` instruct Zeek to ingest specific frameworks, custom scripts, or signatures.
*   **Conditional Logic:** Directives such as `@if` and `@else` can be used to evaluate conditions before loading specific script components.
![alt text](./img/image-7.png)

</details>

<details>
<summary><b>Understanding Script Formatting</b></summary>

## Analyzing Base Protocol Scripts
Examining how Zeek’s built-in scripts process network traffic, which is highly applicable when you are managing infrastructure visibility or analyzing network states in environments like Ubuntu. 

### 1. Connection and Inactivity Tracking
*   **`conn/main.zeek` & `inactivity.zeek`:** 
    - These scripts utilize event handlers to set up connections and log them appropriately.
    - They are critical for identifying half-open or half-established TCP connections (e.g., assessing an inactivity timeout threshold). This visibility is vital for spotting DDoS attempts or misconfigurations causing unnecessary resource consumption.
*   **DNS & FTP Analysis:** 
    - **DNS:** Tracks the state of the protocol by monitoring pending queries and replies, heavily utilizing constants and definitions for key mapping.
    - **FTP:** Employs the protocol analyzer alongside a dedicated files script to log FTP activity and extract file information when triggered.

---

## Aggregation and Logging Frameworks
How Zeek manages local deployments and structures its output data.

### 1. Local Site Configurations
*   **`local.zeek`:** 
    - Acts as an aggregation point rather than doing heavy event handling.
    - Relies heavily on `@load` statements to pull in external frameworks and `redef` statements (like redefining `ignore_checksums = F`) to adjust global behaviors.

### 2. The Logging Framework
*   **Stream Management:** 
    - Controls queue sizes to prevent the system from getting overloaded during heavy traffic.
*   **Log Writers (ASCII and SQLite):** 
    - Contains module export statements, constants, and sub-statements that strictly define the data types (strings, integers) and dictate exactly how Zeek writes fields to the final logs.

---

## Deep Dive: The HTTP Script Lifecycle
A look at the extensive `http/main.zeek` script to see how Zeek builds context throughout a connection's lifecycle.

### 1. Sequential Event Handling
*   **Initialization:** The `zeek_init` event triggers first to create the HTTP log stream.
*   **Session Tracking:** When a new HTTP session begins, Zeek pulls the initial metadata and sets the state.
*   **Granular Actions:** The script uses conditional logic to evaluate if it is seeing a new request, a new response, or an original request, taking different actions (like extracting HTTP headers) based on the exact state of the transaction.

</details>
