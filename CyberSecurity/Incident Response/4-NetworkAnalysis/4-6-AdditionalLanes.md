<details>
<summary>Why Can't We Pay?</summary>

## The Ransom Payment Leads to Dead Ends

- The network owner decided the investigation was taking too long:
  - Attempted to **pay the ransom**.
- Following the ransomware note:
  - Leads to **dead ends**.
  - There's **no actual way to pay**.
- This is strange:
  - Ransomware operators depend on their reputation to get paid.

## The Remaining Host to Investigate

- Communication to another host hasn't been fully investigated:
  - **172.31.64.10**.
- This is the **Dark Energy Application Server**:
  - The primary server used by Globomantics.

## Consider What's Valuable to the Business

- Bring the investigation back to:
  - **What's valuable to your customer's business**.
- The Dark Energy Server would be:
  - A valuable target for a ransomware attack.

## Ransomware Was Staged but Not Activated

- The **stager with ransomware** appears to have been set up on the server.
- But it was **never activated**.

**Questions to consider**

- Did something go wrong with that server?
- Did the adversary run out of time?
- Was there another reason to leave it running?

## Before Closing the Case

- Investigate communication from the **Dark Energy Server**.
- Look for **anything anomalous**.

</details>

<details>
<summary>Demo: DNS Entropy Analysis</summary>

## 1. Examine the Server in Kibana

- Set the **source IP** to:
  - **172.31.64.10**.
- Examine what it is communicating out to.

**Previously identified activity**

- Communication with the known bad **C2 server**.
- Connections over:
  - **Port 15111**.
  - **Port 443**.

**New observation**

- A **massive amount of DNS conversations**.

## Why the DNS Volume Stands Out

- A DNS server making requests on behalf of clients:
  - Might have many DNS conversations.
- An internal application server making this many requests:
  - Seems **anomalous**.

**Destination**

- **172.31.0.2**:
  - The **internal DNS server**.
- The conversation flow is going the right way:
  - But the DNS queries still need investigation.

## 2. Extract DNS Traffic

- Use the same traffic-filtering process as before.
- Filter for:
  - **Port 53**.
- Do not restrict the capture to the Dark Energy Server:
  - Take a broader look at **all DNS conversations on the network**.

## 3. Run Zeek with the DNS Entropy Script

1. Create a new directory for Zeek.
2. Copy the **DNS entropy script** into that directory.
3. Run Zeek against the filtered capture:
   - Include the **dnsentropy script**.

## What Is Entropy?

- A **randomization score** on queries or strings.
- The script calculates an **entropy score for each query**.

**Purpose**

- DNS logs can contain a massive amount of data.
- Entropy helps narrow the search:
  - Find queries that look **weird or anomalous**.

## Entropy Threshold Alerts

- The script can alert when a query exceeds:
  - A preset **entropy threshold**.
- A higher score can identify a query:
  - That looks more unusual than a standard DNS query.
- The alerts indicate:
  - **Something should be investigated**.

## 4. Examine dns_entropy.log

- Generated file:
  - **dns_entropy.log**.
- Contains:
  - Originating host and port.
  - Responding host and port.
  - Individual **queries**.
  - Their **entropy scores**.

## Compare Lower and Higher Scores

1. Read **dns_entropy.log**.
2. Extract:
   - **query_entropy**.
   - **query**.
3. Sort the values.
4. Reverse the sort to inspect the highest scores.

| Queries in the demonstration | Observation |
|---|---|
| **Reddit, Yahoo, and Google** | Scores around **2.5–2.8**; normal-looking strings in this dataset. |
| **Queries to dirtylitter.iamironcat.com** | Higher entropy; the suspicious queries stand out. |

- These values describe the **lab observations**:
  - They are not a universal cutoff for malicious DNS.

## 5. Identify the Suspicious Domain

- Queries go to:
  - **dirtylitter.iamironcat.com**.
- This correlates with:
  - The previously investigated **known bad domain**.
- Appended to the front:
  - **Encoded information in the subdomain**.

**What this suggests**

- Information is being sent:
  - **Encoded inside DNS requests**.

## 6. Count the Suspicious Queries

1. Extract:
   - **Originating host**.
   - **Query**.
2. Filter for strings containing:
   - **dirtylitter**.
3. Perform a **line count**.

**Finding**

- **4,260 queries** to that domain.

## 7. Identify Every Originating Host

1. Keep the **dirtylitter** filter.
2. Extract **field 1**:
   - The originating host.
3. **Sort and uniq** the values.
4. Review the complete list of hosts making the requests.

**Finding**

- **Every single request comes from the Dark Energy Server.**
- Source:
  - **172.31.64.10**.

## What the DNS Channel Is Doing

- Data is encoded into **subdomains**.
- Queries go through the **internal DNS server**.
- The encoded information is carried toward:
  - The **authoritative DNS server associated with the malicious domain**.

## Key Findings

| Artifact | Finding |
|---|---|
| **Originating host** | **172.31.64.10 — Dark Energy Application Server** |
| **Internal DNS server** | **172.31.0.2** |
| **Suspicious domain** | **dirtylitter.iamironcat.com** |
| **Query count** | **4,260** |
| **Suspicious content** | Encoded information in subdomains |
| **Behavior identified** | A DNS channel carrying data |

</details>

<details>
<summary>The Real Reason for the Attack</summary>

## Ransomware Wasn't the Only Objective

- The ransomware had its effect.
- But the noise helped hide:
  - **An additional lane of activity**.
  - Objectives being carried out on the **critical application server**.

**Distractions included**

- Ransomware.
- Internal scans.
- Noisy communication.

## Further Host Analysis

- Further host analysis finds:
  - **Critical files were exfiltrated**.
- The exfiltration used:
  - The **DNS channel** discovered during network analysis.

## Why the Investigation Mattered

- You:
  - Kept your cool.
  - Took detailed notes.
  - Used **critical thinking throughout the investigation**.
- This helped:
  - **Scope the entire incident**.
  - Find activity that most likely would have been missed.

</details>