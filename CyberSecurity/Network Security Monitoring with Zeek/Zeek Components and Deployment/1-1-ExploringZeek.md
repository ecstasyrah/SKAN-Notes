<details>
<summary><b>Learning About Zeek</b></summary>

## Understanding Zeek
Zeek is a network monitoring tool that alerts you on threats or suspicious activity in the network.

### 1. Key Characteristics
*   **Tool Comparison:** 
    - Similar to a Network Detection and Response (NDR) tool.
*   **Features:** 
    - Open source, extremely customizable, and has the ability to automate many aspects of its processes.

---

## History and Evolution
Zeek was initially developed in the 1990s under the name Bro.

### 1. Timeline
*   **1995:** 
    - Vern Paxson began working on the code and laying out the foundation.
*   **2003:** 
    - The National Science Foundation supported more research and development.
*   **Development Factors:** 
    - Academic research projects were the main contributing factor to its current capabilities.
*   **Modern Era:** 
    - Later renamed from Bro to Zeek.
    - Currently using version 7.0.

---

## Architecture and Deployment
Zeek operates on a two-layer architecture and can be deployed in multiple ways depending on network needs.

### 1. Two-Layer Architecture
*   **Event Engine:** 
    - Processes network traffic.
*   **Policy Scripts:** 
    - Define the logic for interpreting and responding to that traffic.

### 2. Deployment Methods
*   **Standalone deployment:** 
    - Suitable for small networks or specific segments within a larger network; offers simplicity but lacks scalability.
*   **Cluster-based deployment:** 
    - Ideal for large network environments; utilizes multiple Zeek instances distributed across various nodes to handle high volumes of traffic.
*   **Cloud-integrated:** 
    - Leverages cloud infrastructures to provide scalability and flexibility; suitable for organizations with dynamic network needs.

</details>

<details>
<summary><b>Zeek’s Core Components</b></summary>

## Core Functionality
Zeek can be deployed in multiple ways and utilizes various frameworks, plug-ins, and extensions to accomplish its use cases. 

### 1. Zeek's Core Engine
*   **Traffic Processing:** 
    - Processes incoming network traffic and generates events based on pre-defined patterns to capture and tag traffic.

### 2. The Processing Model
*   **Event Generation:** 
    - Captures and analyzes network packets to create events that correspond to network activities.
*   **Event Handling:** 
    - Utilizes policy scripts to handle generated events, allowing for customer responses, alerting, and data interpretation.
*   **Logging:** 
    - Documents information based on the events seen, providing valuable insights into network activities.

---

## Management and Component Interaction
Understanding how Zeek's pieces work together to process and act on network traffic.

### 1. ZeekControl
*   **Centralized Management:** 
    - Provides an interactive shell to operate and manage Zeek installations, preventing the need to manually start/stop nodes or pull stats individually.
*   **Deployment Support:** 
    - Works for both standalone and cluster deployments and can be used to install the system and scripts.

### 2. The Event Engine
*   **Packet Analysis:** 
    - Takes raw packets and uses several analyzers to identify occurring events. It strictly performs analysis and does not make decisions on what to do with the events.
*   **Analyzers:** 
    - Utilizes transport layer analyzers, application layer analyzers (with subsets to examine specific protocol behavior), and infrastructure analyzers.
*   **Connection Tracking:** 
    - Has the ability to track connections to identify suspicious behavior with protocols that require processes like the three-way handshake.

### 3. Policy Script Interpreter
*   **Policy Definition:** 
    - Defines the actual policies Zeek uses to determine what to do with the events received from the event engine.
*   **Event Handlers:** 
    - Uses scripts to identify events and generate alerts. Allows for the customization of policies on top of the default ones.
*   **Scripting Language:** 
    - Zeek's scripting language is event-driven. After receiving events from the Event Engine, the interpreter typically generates a notification or alert for investigation.

</details>

<details>
<summary><b>Understanding Zeek’s Frameworks</b></summary>

## Framework Overview
Zeek provides several frameworks that enhance its functionality and ease of use by pulling in additional data to manipulate or use in various ways.

### 1. Data and Configuration Frameworks
*   **Input Framework:** 
    - Helps read data from sources into Zeek, allowing data to be read into tables or actual events for Zeek to use.
*   **Configuration Framework:** 
    - Acts as an alternative to using constraints in scripts by providing the ability to change any configurations at runtime.
    - Offers options to run Zeek with different configurations or tweaks without modifying the scripts themselves.
    - Useful for turning certain features or scripts on or off for testing.

### 2. Integration and Response
*   **NetControl Framework:** 
    - Helps with integrations by acting as Zeek's API to connect to external devices.
    - Allows passing rules to network devices (like routers, switches, and firewalls) after detecting certain behaviors.
    - Can be used effectively as a response mechanism.

### 3. Data Management and Intelligence
*   **Logging Framework:** 
    - Manages the collection and storage of data generated by Zeek logs (including network connections, HTTP requests, DNS queries, and alerts).
    - Utilizes three main subcomponents: streams, filters, and writers.
    - Extremely customizable and supports many output formats, such as JSON and ASCII.
*   **Intelligence Framework:** 
    - Designed to consume data and make it available for matching, handling both static and dynamically downloaded intelligence.
    - Utilizes static intelligence items (like domains, IP addresses, email addresses, or hashes) which can contain additional context or metadata.

### 4. Security Detection
*   **Signature Framework:** 
    - Gives the ability to use a more traditional approach to network security detection.
    - Allows for the creation of signatures or specific rule sets within Zeek.
    - Generates an event whenever the matching criteria is found in a packet or data stream.

</details>

<details>
<summary><b>Installing Zeek</b></summary>

## Prerequisites and Environment
Setting up the lab system to install Zeek.

### 1. System Requirements
*   **Operating System:** 
    - Uses an Ubuntu virtual machine.
*   **Network:** 
    - Requires internet access to download packages and files needed to compile the source code.

---

## Installation Process
Installing Zeek from source code is the best way to customize it and utilize the most current code, as repository packages are typically behind in versions.

### 1. Preparation
*   **Dependencies:** 
    - Install necessary dependencies (ensuring to use "flex" instead of the typo "glex").
*   **Cloning the Repository:** 
    - Clone the Zeek repository into the current working directory.
    - Use the recursive command during the git clone to ensure every required file, extension, and plug-in (like zeekctl) is grabbed properly.

### 2. Building from Source
*   **Directory Setup:** 
    - Navigate into the freshly cloned zeek directory.
    - Make a new directory called build and navigate into it.
*   **Compilation Commands:** 
    - Run the CMAKE command, setting the target installation directory to `/opt/zeek`.
    - Run the make command to get it set up.
    - Run the install command once the make process is complete.

---

## Verification and Troubleshooting
Validating the installation and addressing post-installation errors.

### 1. Version Verification
*   **Zeek Version Check:** 
    - Run `/opt/zeek/bin/zeek --version` (which should display a version like 7.2.0‑dev.439).

### 2. Resolving zeekctl Errors
*   **Spool Error:** 
    - Attempting to run zeekctl initially may result in a spool error.
*   **Applying the Fix:** 
    - Make a directory for the spool.
    - Change the permissions for the entire zeek directory.
    - You should then be able to successfully open and manage zeekctl.

</details>

<details>
<summary>Zeek Installation Configuration</summary>

Enter terminal
```bash
sudo apt update && sudo apt install build-essential cmake make gcc g++ flex bison libpcap-dev libssl-dev nodejs libnode-dev python3 python3-dev swig zlib1g-dev libzmq3-dev libsqlite3-dev python3-dev
# if git isnt downloaded yet
sudo apt install git -y
sudo git clone --recursive https://github.com/zeek/zeek

cd zeek
sudo mkdir build

cd build
sudo cmake .. -DCMAKE_INSTALL_PREFIX=/opt/zeek
sudo make -j$(nproc)
sudo make install

/opt/zeke/bin/zeek --version
#check the version to know if install is successful
/opt/zeke/bin/zeekctl
#if spool error appears make a directory for it 
sudo mkdir -p /opt/zeek/spool
sudo chown -R skanlog:skanlog /opt/zeek

# open zeek
/opt/zeek/bin/zeekctl

```

</details>

<summary>Zeek Plugins and Extensions</summary>

## Customizing Zeek
Zeek's functionality can be extended through the use of plug-ins and extensions, allowing for custom modules and integrations.

### 1. Plugins and Extensions
*   **Zeek Plug-ins:** 
    - Add new functionalities or enhance existing ones.
    - Examples include additional protocol analyzers, logging formats, or integration with other security tools.
*   **Extensions:** 
    - Allow users to develop custom scripts and modules tailored to specific requirements.
    - Ensure Zeek can adapt to various use cases and environments.
    - Can include additional analyzers, brokers, external integrations, additional scripts, and even tools that create or help with new parsers.

### 2. Packages
*   **Zeek Package Manager:** 
    - Allows the installation of third-party scripts and plug-ins.
    - Makes it very easy to integrate add-ons into the Zeek system for use with the tool.
*   **Package Use Cases:** 
    - Packages exist for all sorts of use cases, such as parsing IKEv2 protocol traffic or DNS traffic.

### 3. Next Steps
*   **Upcoming Module:** 
    - Focuses on configuring Zeek, validating the installation in two different ways, and wrapping up the course.

</details>