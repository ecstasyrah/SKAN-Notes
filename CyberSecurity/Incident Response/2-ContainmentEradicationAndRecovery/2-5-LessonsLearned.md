<details>
<summary>Lessons Learned and Moving Forward</summary>

## Responsibility Before Closing the Incident

- **Phishing is going to happen.**
  - That is a forever fight at the moment.
- **Office macros** do have some decent mitigations.
- Your responsibility:
  - **Provide recommendations**.
  - Where possible, **help implement and validate improvements** to the security posture of the organization.
  - Prevent the **same TTPs used in this compromise from working again**.
- Work directly with:
  - **Globomantics system administrators**.
  - **Local security team**.

## Follow the Attack Chain

- At every point, there is a potential for a:
  - **Mitigating** measure.
  - **Preventative** measure.
  - **Detective** measure.
- Your recommendations fuel what happens during:
  - **Continuous monitoring and detection**.

## 1. Macro Inside the Office Document

**Preventative option**

- **Globally preventing the execution of macros**.
- Office macros can be globally disabled by a **Group Policy object in a Windows domain**.
- The **Office Group Policy object template**:
  - Has to be added.
  - Does not come default in the domain.

**Detective option**

- Detecting the **code executed by malicious macros** with tools like **EDR**.

## 2. Network Traffic to the C2 Server

**What happened**

- The payload for the **C2 server** was:
  - Downloaded over **HTTP**.
  - Executed.

**Detection**

- You can't really stop all the traffic to any IP.
- Have detections in place to **inspect that traffic**.
- Inspection of this traffic tuned with **current signatures** would have detected the malicious payload.

**Why earlier detection matters**

- Moved the detection of the threat actor activity:
  - From where the **ransomware was executed**.
  - To **immediately before they gained active access to the environment**.
- Allowing Globomantics to **stop the event before it even happened**.

## 3. Lateral Movement Between Devices

**What happened**

- The threat actor moved laterally between devices over **port 15111**.
- **Client-to-client traffic in itself can be odd**.

**Network detection**

- Monitoring traffic at this point enables a number of **lateral movement alerts**.

**Endpoint detection**

- A **Windows firewall modification** had to be made:
  - For that connection to be allowed on a **non-standard port**.
- This is logged in the **security logs** on those devices.
- Using a tool like **Sysmon** and **central logging in a SIEM**:
  - Would allow the security analyst to identify this activity **as soon as it happened**.

## 4. Ransomware Execution

**Antivirus limitations**

- It's just too simple to:
  - **Change signatures**.
  - Develop **different behavioral methods**.
- Not to say you shouldn't have antivirus.
- A better detection here is to have **file access monitoring**.

## File Access Monitoring

- You don't want to have that on **everything**:
  - That is way too noisy.
- Assets logging every time they were:
  - **Accessed**.
  - **Changed**.
- You don't need someone to sit and watch that activity scroll across the screen.

**Statistical regression analysis**

- Identify deviations from **standard frequency of access or change**.
- Catch the **massive spike of hundreds of thousands of files**:
  - Being accessed.
  - Being encrypted at the same time.

**Another option**

- Watch the **disk I/O, or input/output**.

## Why Detecting Ransomware Still Matters

- The ransomware was really just a **cover**:
  - For the attacker taking manual action.
  - To alter the **integrity of the dark energy satellite service**.
- An **early warning on the ransomware activity**, provided with:
  - Proper **preapproved actions for response**.
  - **Locking down those critical services**.
- Would have prevented the **nearly catastrophic impact**.

## Implement the Lessons Learned

- At every point, there is a **preventative or detective mitigation**.
- Make sure that these lessons learned from the intrusion are implemented:
  - So the organization does not have to **learn these particular lessons again**.

## Validation: Incident Responder's Responsibility

- **Validate through monitoring**:
  - There are no additional indications of **threat actor activity**.

**Example: An employee returns from vacation**

1. An employee who was on vacation comes in.
2. Opens their email.
3. Immediately opens the **old malicious macro**.

**Required detection**

- Have **network detections** in place.
- Immediately an alert on the subsequent **callout to the C2 server**:
  - **Regardless of whether it's successful**.

## Validation: Organization's Responsibility

- Implement those:
  - **Recommendations**.
  - **Security policies**.
  - **Detections**.
- But how do they know that those implementations worked?
- If we look at the **NIST cybersecurity framework**:
  - They have checked every single box.
  - **That's not good enough.**

**The Aaron Rosenmund modification to this framework**

- The responsibility of the **security team within the organization**:
  - **Emulate the threat actor's TTPs**.
  - Check and make sure that those **mitigations actually work**.

## Red Team Validation

**Example: Globally disabling macros**

1. Did Globomantics push out the **GPO for disabling the macros globally**?
2. Use a **red team action to emulate this intrusion set**.
3. See if that setting **actually works**.
4. If there are **holes or exceptions** that are easy to take advantage of:
   - They need to be **closed and revalidated**.

**Purpose**

- Validating that those security controls are **properly implemented**.
- They won't be calling you to come back in a few weeks:
  - **Compromised by the same attacker**.
- The red team can take your **outputs as an incident responder**:
  - As **inputs into their functions**.

## Incident Response and Cybersecurity Operations

- Incident response is often represented as:
  - A contained set of functions.
  - Starting and stopping with the beginning and end of the incident.
- Instead, a function of the **organization's cybersecurity operations**.

**Inputs**

- **Detection of malicious activity**.

**Outputs**

- **Recommended security posture**.
- Improvements to **security administration**.
- Improvements to:
  - **Detections**.
  - The **continuous monitoring function** of the security operation center.
- **Prioritized adversarial intrusion sets for the red team**.

## Teams Supporting Incident Response

- **Intel analysts**:
  - Helping you vector your response activity.
- **Security engineers**:
  - Developed capabilities like **Velociraptor**.
- **Malware analysts**:
  - In this case, provided you the capability to **fully recover all of the damage done to Globomantics**.

## Moving Forward Between Incidents

- **In between incidents is your time to skill up.**
- These courses are just the **starting point for the incident responder role**.

**Continue learning**

- New **blue team tools** to advance your toolkit:
  - **Blue Team Tools path**.
- Details of various:
  - **Threat actor techniques**.
  - **Red team tools**.
- More in-depth:
  - **Advanced incident response concepts**.

**Whatever your path, always be learning.**

- Take a moment to relax.

</details>