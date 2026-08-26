<details>
<summary><b>Confidentiality, Integrity, and Availability (CIA)</b></summary>

## The CIA Triad
The CIA triad consists of three elements designed to ensure that data is kept private, accurate, and accessible to authorized users.

### 1. Confidentiality (Privacy)
Ensures that data is kept private and secure from unauthorized access.
*   **Encryption:** Securing data so it cannot be read by unauthorized parties.
*   **2FA/MFA (Multi-Factor Authentication):** Adding extra layers of access verification.
*   **Airgaps:** Separating a network so it is not connected to other networks or the internet to keep data completely private.

### 2. Integrity (Consistency and Accuracy)
Ensures that data remains accurate, consistent, and unaltered.
*   **File Permissions and Access Control:** Ensuring that only authorized folks have access to the data, and those who should not are restricted.
*   **Version Control:** Tracking changes to ensure you are dealing with the latest, greatest version of a file, or accurately referencing past versions.

### 3. Availability (Consistent Accessibility)
Ensures that systems and data are consistently accessible to authorized users when needed.
*   **Patches and Updates:** Keeping systems up to snuff to meet security requirements and prevent downtime caused by malware, viruses, or ransomware.
*   **Backups and DR/HA:** Utilizing Disaster Recovery (DR) and High Availability (HA) systems to ensure data and services remain available.

---

## Non-Repudiation
Non-repudiation is the assurance that someone cannot deny the validity of something (such as a document, an email, or participation in a communication).

*   **Mechanism:** This service is often provided by a **PKI (Public Key Infrastructure)** through the use of public and private keys.
*   **How it Works:** Assuming a user keeps their private key secure, any data encrypted via that private key could *only* have originated from that specific user. This creates a system where actions and communications can be definitively substantiated.

</details>

<details>
<summary><b>Authentication, Authorization, and Accounting (AAA)</b></summary>

## The AAA Framework
The AAA framework is a core concept in security that manages user access and tracks activity. It consists of three main components:

### 1. Authentication
*   **Function:** Identifies the user and either allows or denies access.
*   **Mechanism:** May challenge the user for additional credentials (such as a PIN or a rotating code) to properly verify their identity.

### 2. Authorization
*   **Function:** Determines what the user is allowed to do once they are authenticated.
*   **Mechanism:** Uses Access Control Lists (ACLs) to define exactly who can access what resources.
*   **Parameters:** Defines the length of time allowed on the network and specifies exact permission levels (e.g., read, write, and execute).

### 3. Accounting
*   **Function:** Tracks the start and stop time of each session and monitors overall resource consumption.
*   **Use Cases:**
    *   *Billing/Chargeback:* Formally charging departments or users for the resources they consume.
    *   *Showback:* Showing users or departments how much time and resources they are using without a formalized chargeback method.
    *   *Shameback:* Putting high-consuming departments on a "naughty list" and reporting this to executive management to encourage them to pony up and fund the resources they are tying up.

</details>

<details>
<summary><b>Identification vs. Authentication vs. Authorization</b></summary>

## Understanding the Differences
When discussing access control, it is important to distinguish between three core concepts: identification, authentication, and authorization.

### 1. Identification (Who you are)
*   **Definition:** The process of claiming or labeling an identity.
*   **Examples:** Providing a username, a security ID, or presenting a smart card.

### 2. Authentication (Proving who you are)
*   **Definition:** The process of verifying that you are indeed the person you claim to be.
*   **Examples:** A username and password combination, a PIN, a One-Time Password (OTP), or biometric data (such as a fingerprint or retina scan).

### 3. Authorization (What you can do)
*   **Definition:** The process of granting permissions.
*   **Function:** Determining exactly what resources, files, or systems you are allowed to access once you have been successfully authenticated.

</details>

<details>
<summary><b>Multifactor Authentication and Authentication Factors</b></summary>

## What is Multifactor Authentication (MFA)?
Multifactor Authentication involves using two or more pieces of information to authenticate a user. 

*   **The Golden Rule of MFA:** The authentication methods *must* come from different categories. 
    *   *Example:* Using a password and a PIN only counts as a single factor because both fall under the category of "something you know."

---

## Authentication Categories (The Factors)
To properly implement MFA, you pull from different categories of authentication. These include:

*   **Something you know:** Information committed to memory, such as a password, a PIN, or a secret.
*   **Something you are:** Biometric data, such as a fingerprint, a retina scan, or facial recognition.
*   **Something you have:** A physical item you possess, such as a smart card, a hardware token, or an authenticator app that generates rotating codes.
*   **Somewhere you are:** Location-based data, such as a specific IP address or geolocation.
*   **Something you do:** A specific action or pattern of behavior, such as a written signature, typing rhythm, language, or slang.
*   **Something you exhibit:** A physical pattern, personality, or neurological trait, such as gait analysis (the unique way you walk).
*   **Someone you know:** Social proof, where a trusted friend or colleague vouches for the user (often via some type of token generation).

</details>

<details>
<summary><b>Gap Analysis</b></summary>

## What is a Gap Analysis?
A gap analysis is the process of identifying and evaluating the discrepancies (or "gaps") between an organization's current security measures and its desired or required security posture. 

*   **Goal:** To identify vulnerabilities, weaknesses, and areas for improvement.
*   **Outcome:** Enables the development of a targeted strategy to enhance cybersecurity defenses, reduce risk, and prioritize resource investments based on ranked risks.

## The Building Perimeter Analogy
Think of a gap analysis like walking around the perimeter of a physical building:
*   You check the doors and windows to see if they have proper locks.
*   If you find a window on the side of the building that is broken or wide open, you have identified a *gap* in that physical defense. 

## Applying Gap Analysis to a Network
When applying this concept to an IT environment, we swap physical doors and windows for networking components, servers, and software. Questions asked during this process include:
*   **System Status:** What are our current patch levels and OS levels? Are there known vulnerabilities that have not been patched?
*   **Tooling Capabilities:** Do we have the tools in place to identify vulnerabilities or push out patches quickly across the entire environment? 
*   **Incident Readiness:** If a zero-day incident occurs, can we push critical patches immediately?

If the organization lacks these capabilities, it is recorded on a gap analysis report. The team then ranks these risks to determine where to invest time and resources, tackling issues from most important to least important.

</details>

<details>
<summary><b>Zero Trust</b></summary>

## What is Zero Trust?
Zero Trust is a paradigm shift from traditional perimeter-based security. 
*   **The Old Model:** Assumed that anything outside the network perimeter was untrusted (bad actors) and anything inside was automatically trusted.
*   **The Zero Trust Model:** Driven by the shift to remote and hybrid work, this model operates on the fundamental principle to **never automatically trust any user, device, or network component, regardless of its location**. Everything must be authenticated, whether it is inside or outside the corporate network.

---

## The Eight Tenets of Zero Trust
A Zero Trust environment is built upon eight main tenets:

1.  **Least Privileged Access:** Only granting the minimum permissions necessary to access a resource (e.g., giving read-only access if write access is not needed).
2.  **Microsegmentation:** Dividing the network and its resources into smaller, firewalled groups. This creates stopgaps that contain breaches and significantly reduce the "blast radius" of an attack.
3.  **Continuous Monitoring:** Continuously monitoring all activity, regardless of whether the user is already inside the network and authenticated.
4.  **Multifactor Authentication (MFA):** Requiring multiple factors of authentication for all access, even for internal resources.
5.  **Device and User Identity Verification:** Verifying and authenticating the identity of every user and device attempting to access resources.
6.  **Encryption:** Encrypting data both in transit and at rest to make it inaccessible even if a system or device is compromised.
7.  **Strict Access Control:** Keeping access as slimmed down and tightly controlled as possible.
8.  **Assume a Breach:** Operating under the assumption that everyone is a bad actor and everything is already compromised, applying the highest level of due diligence everywhere.

---

## Zero Trust Architecture
A Zero Trust network is structurally divided into two primary planes:

### 1. The Control Plane
This is where policies are created and access decisions are made. It houses the **Policy Decision Point (PDP)**, which consists of:
*   **Policy Administrator:** Creates the policies and rules.
*   **Policy Engine:** Evaluates requests against the established rules to make access decisions.

### 2. The Data Plane
This is where the actual data transfer and access occurs. It houses the **Policy Enforcement Point (PEP)**.
*   **PEP (The Gatekeeper):** Sits between the subject and the resource.

### The Access Workflow
1.  An **untrusted subject** attempts to access a resource.
2.  The request hits the **Policy Enforcement Point (PEP)**.
3.  The PEP reaches out to the **Policy Decision Point (PDP)**, providing context (user, system, etc.).
4.  The **Policy Engine** evaluates the criteria. 
5.  If approved, the PDP signals the PEP to allow access.
6.  The subject is now **trusted** and can access the enterprise resource.

---

## Inputs to the Policy Decision Point
To make accurate, real-time access decisions, the PDP gathers data from numerous systems across the network, including:
*   **CDM (Continuous Diagnostics and Mitigation) systems**
*   **Industry compliance mandates** (e.g., PCI updates)
*   **Threat Intelligence** (internal and external sources)
*   **Activity logs**
*   **Data access policies**
*   **PKI (Public Key Infrastructure)**
*   **ID Management / PAM (Privileged Access Management)**
*   **SIEM Systems**

> **Further Reading:** For a deeper technical dive into these specifics, refer to the **NIST SP 800-207** whitepaper on Zero Trust Architecture.

</details>

<details>
<summary><b>Infrared, Microwave, and Ultrasonic Physical Controls</b></summary>

## Advanced Physical Security Sensors
Adding to the physical security controls discussed previously, here are three specific types of sensors used to detect motion, distance, and unauthorized access.

### 1. Infrared (IR) Sensors
*   **Function:** Uses infrared light to detect motion, which can trigger an alarm, turn on lights, or send alerts.
*   **Types:** Active and passive types are both commonly used.

### 2. Microwave Sensors
*   **Function:** Uses microwave pulses to measure movement. 
*   **Advantages:** They are more sensitive than IR sensors and can sense motion through nonmetal materials like wood, plastic, and drywall.
*   **Use Case:** Commonly used in outdoor applications.

### 3. Ultrasonic Sensors
*   **Function:** Detects objects and the distance to an object by emitting sound waves (at frequencies too high for humans to hear). The sound waves reflect off objects and bounce back to the sensor.
*   **Mechanism:** By understanding how fast the sound wave should return, the sensor can calculate how far away an object is or detect if an object has moved into its path.
*   **Configuration:** Can be made up of two separate devices (an emitter and a detector) or a single device that performs both functions.
*   **Unique Feature:** Some types of ultrasonic sensors can be used underwater to detect distance or measure depth.

</details>

<details>
<summary><b>Honeypots, Honeyfiles, and Honeynets</b></summary>

## Deception and Disruption Technology
These technologies are designed to intentionally attract bad actors to monitor their behavior, understand their methods, and analyze the tools they use.

### 1. Honeypots
*   **Definition:** 
    - A computer or host that is set up specifically to become a target of an attack.
*   **Function:** 
    - It is configured to look like it contains sensitive or valuable information to attract hackers. Security teams monitor it closely to learn the attacker's Tactics, Techniques, and Procedures (TTPs).

### 2. Honeyfiles
*   **Definition:** 
    - The same concept as a honeypot, but applied to individual files instead of an entire host or server.
*   **Function:** 
    - Designed to entice bad actors to open or engage with the file so their activities can be closely monitored.

### 3. Honeynets
*   **Definition:** 
    - A much larger-scale implementation—an entire network intentionally set up for attack.
*   **Components:** 
    - Contains network infrastructure (routers, switches) along with various servers and operating systems to give attackers a complex environment to explore.

---

## Honeynet Architecture Example
To understand how a honeynet functions in practice, consider the following architecture example utilizing a robust virtual server (a host running multiple virtual guests):

### The Setup
*   **The Honeynet:** 
    - A network comprised of various machines designed for the attacker to "chew on" and perform reconnaissance. 
    - *Linux environment:* Running services like DNS, NTP (Network Time Protocol), FTP, and web servers.
    - *Windows environment:* Running different versions (e.g., Windows 10, Windows 11) with various service pack levels.
*   **The Honeywall:** 
    - Acts as a specialized firewall sitting between the external network and the honeynet. It is responsible for data control, data capture, and data analysis (e.g., wire sniffing).
*   **Management Server:** 
    - Connected directly to the honeywall, allowing security teams to safely monitor activity and perform forensic data collection.

### The Attack Path
When an intruder (e.g., "Harry the Hacker") attempts to breach the network, their path looks like this:
1. The attacker connects from the **Internet**.
2. They pass through the external **Public Firewall**.
3. They hit the **Honeywall**, where all their actions begin being recorded.
4. They enter the **Honeynet**, where they begin digging for sensitive information.

### Levels of Packet Inspection
As the attacker interacts with the honeynet through the honeywall, the management server performs different levels of packet inspection:
*   **Shallow Packet Inspection:** 
    - Looks at basic header information, such as the source and destination IP addresses.
*   **Medium Packet Inspection:** 
    - Looks at the header plus additional metadata around the packet.
*   **Deep Packet Inspection (DPI):** 
    - Looks at all of the above, plus digs deeply into the actual *payload* of the packet to understand the exact data being sent.

> **The Ultimate Goal:** Gathering this crucial information helps identify the TTPs used by attackers, which directly informs how the organization can strengthen its actual defenses.

</details>
