<details>
<summary>Infected Host Communication</summary>

## Where to Begin

- Work with the **IOCs and host artifacts**.
- Investigate the network side **backwards**:
  - Starting with the **adversary's actions**.
- Ransomware has infected multiple hosts:
  - Start with **one of the infected hosts**.

## Examine Outbound Communications

- Look at what information is:
  - **Being sent external to your network**.
  - **Being received from outside your network**.
- Ransomware often has **external callouts**.
- These may provide:
  - Additional **indicators of compromise**.
  - Insight into the activity when the ransomware hit.

## Decision Point

- You have **packet capture**.
- Focus on the infected host's **specific conversations**.
- **Filter down to a single IP and see what you can find.**

</details>

<details>
<summary>Demo: Beacons and Webshell</summary>

## 1. Filter the Packet Capture

- Source file:
  - **incident.pcap**.
- Contains the network communication being investigated.
- Infected host:
  - **172.31.37.20**.
- Output file:
  - **ransomware-victim.pcap**.

**TCPDump components**

| Component | Purpose |
|---|---|
| **-nn** | No DNS hostname or port-name resolution. |
| **-r incident.pcap** | Read the original capture. |
| **host 172.31.37.20** | BPF filter for the infected host's traffic. |
| **-w ransomware-victim.pcap** | Write the filtered capture. |

## 2. Analyze the Filtered Capture with Zeek

1. Create a directory called **zeek-ransomware**.
2. Change into that directory.
3. Run **Zeek** against the filtered PCAP.
4. Review the generated logs.

- The logs contain:
  - **Protocol information**.
  - Conversations that Zeek detected.
- Include communication:
  - **External to the network**.
  - **Internal to the network**.

## 3. Start with conn.log

- Provides a **high-level overview**.
- Zeek produces **conversation-level statistics**:
  - Overall conversations.
  - Rather than individual packets.

**Fields to extract with zeek-cut**

| Field | Meaning |
|---|---|
| **id.orig_h** | IP address starting the conversation. |
| **id.resp_h** | IP address responding back. |
| **id.resp_p** | Responding port associated with the conversation. |
| **service** | Application protocol detected by Zeek. |

## Port-Independent Protocol Detection

- Zeek can determine the service:
  - **Regardless of the port being used**.
- This helps identify protocols operating on:
  - Unexpected or unusual ports.

## 4. Examine Conversations Started by .20

1. Extract the relevant fields using **zeek-cut**.
2. Use **awk**:
   - Filter the first column for **172.31.37.20**.
3. Use **sort** and **uniq -c**:
   - Produce a clean list.
   - Count matching conversations.

**Findings**

| Destination | Port | Observation |
|---|---|---|
| **52.251.113.157** | **80** | **35 conversations**, identified as HTTP. |
| **172.31.37.10** | **15111** | Communication with another internal host over an unusual port. |
| Other destinations | Various | DNS and network discovery information expected in this environment. |

## Why Port 15111 Is Interesting

- A little high to be a typical service port.
- A little low to be a typical ephemeral port.
- **Should .20 be talking to another internal host?**
- Investigate this conversation further.

## 5. Examine Conversations Directed to .20

- Change the **awk** filter:
  - From the first column.
  - To the **second column**.
- This shows conversations where **.20 is the responding host**.

**Finding**

- **.10 reaches out to .20**:
  - Many times.
  - Over different protocols.

## Use conn_state to Reduce Noise

- Add **conn_state** to the extracted fields.
- Many entries are marked **S0**.

**S0**

- Zeek sees the initial connection attempt.
- Does not see a response from the host.
- Typically indicates an **unsuccessful connection attempt**.

**Filtering**

- Use a reverse grep filter:
  - Exclude **S0**.
- Focus first on conversations with more activity.
- Return to the other attempts later.

## Incoming Traffic of Interest

- **73 conversations over port 8080**:
  - At least one identified as **HTTP** in the connection summary.
- **Port 445 traffic**:
  - Other **SMB conversations**.
- Record these findings in the **investigation notebook**.

## 6. Examine http.log

- Contains information specific to the **HTTP protocol**.
- Includes source and destination information.

**Important fields**

| Information | What it reveals |
|---|---|
| **Originating and responding IPs and ports** | Who is communicating. |
| **HTTP method** | Requests such as **POST** and **GET**. |
| **Host** | The hostname or IP requested by the client. |
| **URI** | The requested path. |
| **User agent** | Software acting on behalf of the client. |

## Three HTTP Artifacts to Investigate

1. Requests to **hello.iamironcat.com**.
2. Traffic over **port 8080**.
3. Traffic over **port 15111**.

- Use **zeek-cut** to extract the fields.
- Use **grep** to focus on each artifact.

## 7. External HTTP Communication

- Filter for:
  - **52.251.113.157**.
- Use **sort** and **uniq** to compare the requests.

**Findings**

| Detail | Value |
|---|---|
| **Originating host** | **172.31.37.20** |
| **External destination** | **52.251.113.157** |
| **Requested host** | **hello.iamironcat.com** |
| **URI** | **/key/** |
| **User agent** | **Go-http-client** |
| **Conversations** | **35** |

- **Go-http-client**:
  - The default user agent used in **Golang**.
- In this investigation:
  - The user agent and destination warrant further inspection.
- HTTP is unencrypted here:
  - Inspect the contents using **Wireshark**.

## 8. HTTP Traffic over Port 8080

- The HTTP log shows **five conversations**.
- Direction:
  - **172.31.37.10 → 172.31.37.20**.
- The **Host** field contains an IP address.
- Requests go to the **root address** on .20.
- The user agent looks normal:
  - But the overall behavior is unusual.
- Investigate it in **Wireshark**.

## 9. HTTP Traffic over Port 15111

- Direction:
  - **172.31.37.20 → 172.31.37.10**.
- The **Host** field again contains an IP.
- Each URI has:
  - A first part that looks semi-normal.
  - Information that may be **encoded**.
  - A key at the end that is consistent across requests.
- Sorting and deduplicating is less useful:
  - Each request contains a unique artifact.
- Review the individual requests.

## 10. Inspect the External Requests in Wireshark

1. Open the same filtered PCAP.
2. Filter for the **external IP**.
3. Add **http** to the display filter.
4. Inspect the requests to **/key/**.

**Finding**

- The requests use **POST**:
  - The client sent information to the external web server.

## A 404 Response Does Not Mean Nothing Was Sent

- The server replies:
  - **404 Not Found**.
- This is the server's response to the request.
- Even though the URI was not found:
  - **The client still sent the information**.
- Do not interpret the 404 response as proof that:
  - No data left the host.

## Inspect the POST Data

- The request contains **URL-encoded form data**.

| Field | Observation |
|---|---|
| **forwarder** | Hostname of the infected machine. |
| **ID** | **isb-cs21** |
| **key** | An encoded value to investigate. |

## Decode the Key with CyberChef

1. Copy the **key** value.
2. Paste it into **CyberChef**.
3. Use the suggested **magic** operation.
4. Apply **From Base64**.
5. Inspect the decoded string.

**Finding**

- The decoded content appears associated with:
  - The **iamironcat** domain.

## Compare the Key Values

- Add the key field as a **Wireshark column**.
- Review the POST requests.
- The demonstration identifies:
  - **Two total key values**.
- Decode the second value with CyberChef.

**Decoded text**

- **“I am an iron cat, and this computer is my litter box.”**

**Meaning**

- An artifact related to the **ransomware**.
- The infected .20 host is sending information:
  - To the **iamironcat domain**.

## Share the Artifacts with Malware Analysts

- Pass the extracted values to the **malware analysis team**.
- They could be critical in:
  - Helping recover data affected by ransomware.
- Their usefulness for decryption still needs:
  - **Malware analysis and validation**.

## 11. Follow the Port 8080 HTTP Stream

1. Filter for **port 8080**.
2. Focus on **HTTP**.
3. Right-click a conversation.
4. Select **Follow → HTTP Stream**.

**Direction shown in the demonstration**

| Stream content | Direction |
|---|---|
| **Request — red** | **.10 → .20** |
| **Response — blue** | **.20 → .10** |

## Web Shell Evidence

- HTML title:
  - **goshell**.
- Other strings:
  - **Reverse Shell**.
  - **Go**.
  - **py pty**.
- The page contains components consistent with:
  - A **web shell**.

## Commands Submitted Through the Web Shell

- Commands are submitted from **.10 to .20**.
- One command:
  - **whoami**.
- Response:
  - **hostname\administrator**.
- Further commands produce:
  - A directory listing of **Windows\system32**.

**What this establishes**

- The web shell was not merely present.
- It was being used for:
  - **Basic enumeration**.
- The shell was operating with:
  - **Administrator privileges**.

## Web Shell Timeline

- The activity occurs **after the ransomware hit**.
- The adversary may have further goals:
  - Separate from encrypting files.
- This expands the investigation beyond:
  - Ransomware alone.

## 12. Inspect Port 15111 in Wireshark

1. Filter for **port 15111**.
2. Add **http**.
3. Examine the **GET requests**.
4. Follow an **HTTP stream**.

**Findings**

- A normal-looking URI followed by:
  - Encoded-looking information.
  - A repeated key.
- A request and a **successful response**.
- Little additional readable information.

## Limits of Decoding

- CyberChef does not reveal useful content from these strings.
- Possible explanations discussed:
  - An encoding not yet identified.
  - **Randomized strings**.
- Pass these artifacts to:
  - The **malware analysis team**.

## Possible Internal Command and Control

- Communication flows:
  - **.20 → .10**.
- Repeated back-and-forth communication.
- This activity starts **before the ransomware hit**.
- The behavior and timeline suggest:
  - **Internal command and control traffic**.

</details>

<details>
<summary>What Did We Find?</summary>

## Main Artifacts

| Artifact | Finding |
|---|---|
| **Internal communication** | Infected hosts communicate with **172.31.37.10**. |
| **External HTTP requests** | Keys submitted to the **iamironcat domain**. |
| **Port 8080 web shell** | Commands from **.10 to .20**, including enumeration. |
| **Port 15111 traffic** | Possible internal C2 beginning before the ransomware. |

## Investigate 172.31.37.10 Next

- Other ransomware-infected machines show a similar pattern:
  - Multiple devices talking to **172.31.37.10**.
- This host needs further investigation.

## Malicious Domain

- The **iamironcat domain** is associated with the attack.
- Use it as an indicator to:
  - **Validate across the environment**.

## Potential Recovery Information

- The submitted key values could help:
  - **Unlock or decrypt the ransomware-affected data**.
- Give the evidence to the:
  - **Malware analysis team**.

## More Than Ransomware

- A web shell with **administrator privileges** is being used.
- The adversary has:
  - More access.
  - Potentially other intentions.
- The web shell activity happened **after the ransomware**.

## Next Decision Point

- **How did the adversary get onto each host to begin with?**
- In a typical attack:
  1. Gain initial access to a **single endpoint**.
  2. **Move laterally** from there.
- The next step:
  - Investigate that **lateral movement**.

</details>