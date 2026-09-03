<details>
<summary><b>Zeek Deployment Methodology</b></summary>

## Deployment Architectures
Overview of the two primary ways to deploy Zeek and the operational advantages of each.

### 1. Stand-Alone Deployment
*   **Structure:** 
    - A single machine handles both the traffic monitoring and the system management.
*   **Use Case:** 
    - Ideal for small environments, simple testing scenarios, or learning environments.
*   **Drawback:**
    - Lacks scalability. Managing multiple standalone nodes individually across an enterprise becomes highly inefficient.

### 2. Cluster Deployment
*   **Structure:**
    - A set of systems working together in a coordinated fashion to analyze network traffic.
*   **Scalability:** 
    - Allows administrators to deploy numerous worker nodes (sensors) across the network while managing them from a single central point.
*   **Components:** 
    1. **Manager** node (often secured in a data center) and multiple 
    2. **Workers/Loggers** distributed to tap different network segments. Because Zeek is lightweight, these worker nodes can even be containerized.


---

## Centralized Management with ZeekControl
How administrators orchestrate and maintain Zeek deployments.

### 1. The Role of ZeekControl
*   **Function:** 
    - Provides an interactive shell to operate and manage Zeek installations (supporting both standalone and cluster deployments).
*   **Operational Efficiency:** 
    - Eliminates the need to manually start or stop individual nodes, manually pull statistics from multiple boxes, or manage configurations on a per-sensor basis.
*   **Ecosystem Integration:** 
    - By leveraging clustering and ZeekControl, organizations can efficiently gather network data from across the enterprise and pipeline it into centralized analytics tools like Splunk, NetFlow analyzers, and XDR platforms.

</details>

<details>
<summary><b>Deploying a Zeek Cluster</b></summary>

## Configuring the Manager and Worker Nodes
Setting up a basic Zeek cluster involves configuring the manager node to coordinate traffic analysis across distributed worker nodes.

### 1. Modifying `node.cfg`
*   **Stand-Alone Transition:** 
    To enable clustering, you must first comment out or remove the default stand-alone configuration in the node configuration file.
*   **Manager Block:** 
    Define the central manager by creating a `[manager]` block, specifying `type=manager` and the manager's `host` IP address.
*   **Proxy Block:** 
    Define a proxy node (e.g., `[proxy-1]`) to offload data workloads. This proxy node frequently shares the same host IP address as the manager.
*   **Worker Block:** 
    Define the sensor node (e.g., `[worker-1]`) by specifying `type=worker`, its specific `host` IP address, and the `interface` it will listen on (e.g., `ens18`).

---

## Managing Deployments and Permissions
Deploying the configuration from the manager to the worker node using ZeekControl (`zeekctl`).

### 1. SSH Authentication
*   **Communication Requirement:** 
    - The manager node configures and pushes settings to worker nodes over SSH. 
*   **Key Setup:** 
    - Ensure SSH keys are copied to the worker node (e.g., using `ssh-copy-id`) so ZeekControl can authenticate without prompting for a password.
    - You can also specify the connection username in the configuration to avoid defaulting to root.

### 2. Deployment and Packet Capture Privileges
*   **Executing the Deployment:** 
    Running the `zeekctl deploy` command applies settings, checks for syntax errors, and pushes the configuration to the worker nodes to start the Zeek processes.
*   **Permission Conflicts:** 
    Running the deploy command with `sudo` on the manager can cause permission errors on the worker if the environments and user contexts don't perfectly match. 
*   **The `setcap` Command:** 
    If a worker terminates immediately upon deployment (verifiable via the `zeekctl diag` command), it likely lacks privileges to capture network traffic on its assigned interface. Using the Linux `setcap` command on the worker node grants the Zeek binary the necessary capabilities to sniff packets without requiring root or sudo execution.
*   **Verification:** 
    Once deployed successfully, inspecting logs (like `conn.log`) on the manager node will confirm it is actively receiving telemetry from the distributed worker.

</details>