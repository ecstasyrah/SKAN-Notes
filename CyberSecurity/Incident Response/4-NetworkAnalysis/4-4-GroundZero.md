<details>
<summary>Back to Ground Zero</summary>

## Initial Access Point Found

- You've discovered the **internal base of operations for the adversary**.
- In other words:
  - Their **initial access point**.

## Isolate the Machine or Continue Investigating?

- There must be a discussion with the **network owner**.
- **The final decision rests with them.**
- Understand the tradeoffs between:
  - **Continuing the investigation**.
  - **Isolating a compromised system**.

**In this scenario**

- The ransomware has already been deployed.
- Spending a few more hours finding **critical IOCs**:
  - May be the recommendation.

**If data is actively being exfiltrated**

- **Proprietary information** or **customer data** leaving the network:
  - May change that decision.

## Initial Compromise Found Through Host Analysis

1. A **malicious Office document**:
   - Most likely due to **phishing**.
2. An **embedded macro**.
3. The infected host reaches out to an external server:
   - For **command and control**.

## Two Tasks for the Network Analyst

1. **Validate the network traffic**:
   - Using the IOC found during host analysis.
2. **Expand the search**:
   - Find whether anything else communicated with the malicious actor.

</details>

<details>
<summary>Finding a Beacon</summary>

## 1. Validate the External IOC

- Use **Elasticsearch**:
  - View high-level information about conversations.
- Filter for:

| Filter | Value |
|---|---|
| **Source IP** | **172.31.37.10** |
| **Destination IP** | **172.31.101.111** |

**Lab addressing**

- **172.31.101.111** is a private IP address.
- In this lab:
  - It represents an **external IP**.

## 2. Examine the Ports and Services

| Port | Observation |
|---|---|
| **443** | More than **11,000 conversations** between the two hosts. |
| **443 — identified as SSL** | Approximately **8,500 conversations** identified by Zeek. |
| **8000** | Some conversations identified as **HTTP**. |

- Zeek labels the encrypted protocol as **SSL**:
  - Even though it is most likely **TLS**.
- Not all port 443 conversations were identified as SSL.
- Add **port 8000** as a filter:
  - Validate that the HTTP conversations line up.
- Remove the port filter:
  - Return to the overall communication.

## 3. Expand the Search

- Remove the **source IP** restriction.
- Keep the destination:
  - **172.31.101.111**.
- Look for other internal hosts communicating with it.

**Additional host found**

- **172.31.64.10**.
- Also reaching out to the malicious IP:
  - More conversations over **443**.

**Record these artifacts**

- The external IOC.
- Both internal hosts.
- The ports and detected services.

## Why Host and Network Analysis Work Together

- Encrypted traffic over **443**:
  - Can be difficult to identify as malicious through network analysis alone.
- Host analysis provides **IOCs**.
- Apply those IOCs to the network search.

**Result**

- Validated the known **C2 communication**.
- Found **another internal host** communicating with the same destination.

## 4. Perform Deeper Analysis

- Repeat the earlier workflow:
  1. Use **TCPDump** to filter the relevant traffic.
  2. Run **Zeek** against the filtered capture.
  3. Examine the protocol logs.

## 5. Examine ssl.log

1. Extract the relevant columns.
2. **Sort** the results.
3. Select **unique** entries.
4. Look for missing or unusual information.

**Artifact highlighted in the lesson**

- Missing **server-name information**.
- Fields marked with a **dash**:
  - No information detected by Zeek.

> **Clarification:** A missing `server_name` is a clue to investigate, not proof of malicious traffic or an incorrectly formatted certificate. This field generally reflects the client's TLS Server Name Indication (SNI), which is not present in every connection.

## Validate Through Kibana Discover

- Open the **Discover** tab.
- Inspect the **raw logs**.
- Compare the same fields visible on the command line.
- During incident response:
  - You may need to work directly with the raw logs.

## 6. Examine HTTP on Port 8000

- Open **http.log**.
- Find **three HTTP requests**:
  - From **172.31.37.10**.
  - To **172.31.101.111**.
  - Over **port 8000**.
- Inspect the **full URIs**.
- The requests end with a request for:
  - **payload.bat**.

## Connect the Download to the Office Document

- Work in concert with **host analysis**.
- Let the host analysts examine the file.
- Their analysis can determine:
  - Whether **payload.bat** is part of the **malicious Office chain**.

</details>

<details>
<summary>Another Artifact Tracked Down</summary>

## What Was Accomplished?

- **Validated the IOC** found through host analysis.
- Expanded the search.
- Found **another host communicating with the same malicious IP**.

## Why Historical Traffic Matters

- The initial compromise could be investigated because:
  - Traffic from **before the incident** was available.
- That historical traffic:
  - **Won't always be available**.

## Use the IOCs for Mitigation

- Record the discovered **IOCs**.
- Incorporate them into:
  - **Automated detection**.
  - **Prevention**.
- This is critical for the **mitigation phase**.

## Continue Scoping

- The investigation is **not finished**.
- Broaden the search.
- Record all the artifacts.
- **Make sure everything is accounted for.**

</details>