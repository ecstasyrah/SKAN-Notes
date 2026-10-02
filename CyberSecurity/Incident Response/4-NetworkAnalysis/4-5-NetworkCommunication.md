<details>
<summary>Looking for Internal Scans</summary>

## The Missing Question

- You've discovered:
  - **Initial access**.
  - **Lateral movement**.
- But something is still missing:
  - **How did the adversary know which hosts to deploy the ransomware on?**

## Enumeration Before Lateral Movement

- Before direct communication using **SMB**, the adversary would need to:
  - Perform **enumeration**.
  - Discover **what services are open**.

## External vs. Internal Scanning

- The external communication from the known IOCs:
  - Showed **no external scan** in this investigation.
- The environment has an **IDS**:
  - Configured to alert on a broad scan.
- **East-west traffic**:
  - Doesn't just apply to lateral movement.
  - Also includes **internal enumeration**.

## Why Scan from Inside the Network?

- Modern network enumeration techniques can use a **proxy**:
  - Perform port or service scanning **internal to a network**.

**Avoiding detection**

- A massive scan from one external IP to many internal hosts and ports:
  - Is easy to detect.
- An adversary can instead use access to a **single endpoint**:
  - Launch enumeration from inside.

**Learning about available services**

- Hosts may respond differently to **internal conversations**.
- Reveal information such as:
  - **What ports they're listening on**.
  - **What services may be available**.

**Taking advantage of monitoring gaps**

- The adversary may hope you aren't capturing **side-to-side traffic**.
- They may be noisier with their poking around.

## Test the Theory

- Focus on the **initially infected host**.
- Examine its other behaviors:
  - **Within the network**.

</details>

<details>
<summary>Demo: SYN Scan</summary>

## 1. Return to Kibana

- Filter for:
  - **Source IP: 172.31.37.10**.
  - **Destination IP: internal network addresses**.
- Focus on the **unsuccessful connection attempts**.

## Scan Patterns Found

| Observation | Finding |
|---|---|
| **Each requested port** | **256 connection attempts** seen by Zeek. |
| **Each destination host** | Around **1,900 requests** from the internal .10 address. |
| **Source of the attempts** | The **initially compromised host**. |

- This is indicative of a **SYN scan**:
  - Reaching out to available endpoints.
  - Trying to determine **what services are available**.

## Responses from Affected Hosts

- Filter down to hosts known to be affected.
- Some connection requests receive responses.
- These tell the adversary:
  - Which **ports or services are available**.
  - Where to attempt **lateral movement**.

## 2. Compare Two Connections with TCPDump

- Filter by host and **port 445**.
- Initially inspect the **first five packets**.

| Host | Purpose of comparison |
|---|---|
| **172.31.37.42** | An address known not to have a host in this lab. |
| **172.31.37.20** | The infected host with known **SMB communication**. |

## Normal TCP Three-Way Handshake

1. **Client → Server: SYN**
   - Requests a connection.
2. **Server → Client: SYN-ACK**
   - Responds to the connection request.
3. **Client → Server: ACK**
   - Completes the TCP handshake.

## 3. Unanswered SYN Requests to .42

- **.10 → .42**:
  - Sends a packet with the **SYN flag**.
- No response comes back.
- **.10 tries twice**.

**Interpretation**

- The host may not be alive.
- The port may be filtered or otherwise unreachable.
- **No response alone does not prove that a port is closed.**
- In this lab, **.42 is known not to exist**.

## 4. SYN Scan Against .20

1. **.10 → .20: SYN**
2. **.20 → .10: SYN-ACK**
3. **.10 does not complete the handshake**.

**Observed behavior**

- .20 sends **three SYN-ACKs**.
- Eventually, the attempted connection is reset in the demonstrated capture.

**What the adversary learns**

- **.20 is listening on port 445**.
- A completed handshake is not required:
  - To discover that the port responds.

## Compare the Results

| Connection | Response | What it shows |
|---|---|---|
| **.10 → .42:445** | No response to SYN requests. | No reachable responding service was observed. |
| **.10 → .20:445 — scan** | SYN-ACK, but no completed handshake. | The host responds on **445**. |
| **.10 → .20:445 — later communication** | Established communication and SMB data. | The service is subsequently used. |

## 5. Examine the Later SMB Connections

- Expand the capture beyond the first five packets.
- Find the later **successful connections over port 445**.
- Observe:
  - TCP connection setup.
  - Additional TCP flags.
  - **Data transmission over SMB**.

## TCP Flags Discussed

| Flag | Meaning |
|---|---|
| **SYN** | Starts TCP connection establishment. |
| **ACK** | Acknowledges received information. |
| **PSH** | Requests prompt delivery of data to the receiving application. |
| **RST** | Resets a connection. |
| **ECE — E in TCPDump** | Used for Explicit Congestion Notification negotiation and signaling. |
| **CWR — W in TCPDump** | Used for ECN negotiation and congestion-window-reduced signaling. |

- The demonstration includes an **ECN-capable TCP connection**.
- ECN-related flags can appear alongside:
  - **SYN**.
  - **SYN-ACK**.
- Later packets contain:
  - **ACK**.
  - **PSH and ACK**.
  - SMB data.

## Why Compare the Connections?

- Understand what a **known good TCP connection** looks like.
- Identify where traffic varies from:
  - Standard back-and-forth packet communication.
- Distinguish:
  - **Unanswered attempts**.
  - **Scanning**.
  - **Actual service communication**.

</details>

<details>
<summary>Using the Scan Data</summary>

## This Scan Was Extremely Noisy

- It won't always be this easy to detect.
- A more advanced adversary might:
  1. Use **ARP requests at Layer 2**.
  2. Identify **hosts that are alive**.
  3. Perform a more robust scan within that **limited scope**.

## Understand Client-to-Client Communication

- **Client endpoints don't typically communicate directly with other clients.**
- Always understand those conversations.
- Consider whether they match:
  - The expected behavior of the environment.

## Fill the Timeline Gap

- Internal enumeration explains:
  - How the adversary discovered available hosts and services.
  - How they selected targets for **lateral movement**.
- Identifying this gap brings the network investigation:
  - Closer to completion.

## Questions Before Closing the Case

- **Do we have enough to close this case?**
- From **initial access** through the adversary's **final actions**:
  - Are there any loose ends?
  - Does anything still need to be tied down?
- Gather your:
  - **Findings**.
  - **Thoughts**.
  - **Notes**.
- Review them before moving to the final section.

</details>