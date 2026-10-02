<details>
<summary>Where Are We?</summary>

## Your Role

- You are the **third-party incident response analyst** acting on behalf of **Globomantics**.
- Researching a **ransomware attack on their research facility**.
- Either:
  - You have already discovered important artifacts from a **host perspective**.
  - Your **host counterpart is discovering artifacts alongside you**.

## Your Responsibility

- **Focus on the network**.
- **Gather evidence**.
- Put together the story of **what took place**.
- Take a **rational approach** while getting to the bottom of the incident.

## Why Investigate Network Artifacts?

- Take a **broader look at the incident in your environment as a whole**.
- Discover something that was missed:
  - In the scope of the **individual endpoints**.
- Fixing issues found on endpoints is extremely important.
- It is just as important to discover:
  - **Remediation actions required on the network**.

</details>

<details>
<summary>Understanding Our Data Sources</summary>

## Understand What Data Is Available

- Before investigating, understand:
  - **What data sources you have access to**.
- During the **preparation phase**, you may have requested:
  - Certain **tools**.
  - Certain **configurations**.
- Purpose:
  - Find the necessary artifacts.
  - Gather all the important evidence.

## Full PCAP and Storage Limitations

- Optimally, you would have **full PCAP for every communication that took place**.
- But we don't have **petabytes of storage** just laying around.
- Keeping all that traffic:
  - **Isn't always feasible**.

## Three Main Categories of Network Data

1. **Full PCAP**.
2. **Network alerts generated from an IDS or IPS**.
3. **Network session data**.

## Focus of This Investigation

- **Session data**.
- **Full PCAP**.
- In this scenario, you won't focus on IDS alerts:
  - You already know **an incident has taken place**.

## Tools and Their Purposes

| Tool | Purpose |
|---|---|
| **Zeek** | **Session and protocol analysis**. |
| **TCPDump** | Filter and focus on **specific conversations**. |
| **Wireshark** | Filter and focus on **specific conversations**. |
| **Elasticsearch and Kibana** | Serve as the **correlation database**, a SIEM of sorts. |

## Zeek

- Provides **high-level information** about the conversations seen.
- Helps focus on:
  - The **artifacts you're looking for**.

## TCPDump and Wireshark

- **Filter** network traffic.
- Focus on **specific conversations**.

## Elasticsearch and Kibana

- Correlate the collected information.
- Analyze datasets using **visualizations in Kibana**.
- Help find the **necessary artifacts** in the investigation.

</details>