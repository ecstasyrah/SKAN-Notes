# Information and Cyber Security Literacy: Governance, Risk, and Compliance

# Cybersecurity Study Notes: Vulnerability Management Lifecycle

## 1. What Is Vulnerability Management?

**Vulnerability management** is the continuous process of finding, evaluating, prioritizing, fixing, and verifying security weaknesses in an organization's systems.

### Why is vulnerability management continuous?

New vulnerabilities are discovered constantly by:

* Security researchers
* Attackers
* Software vendors
* Other organizations

Software vendors also regularly release security patches.

Therefore:

> **Vulnerability management is not a one-time activity. It is a continuous cycle.**

---

# 2. The Six Phases of the Vulnerability Management Lifecycle

The vulnerability management lifecycle can be remembered as:

**Discovery → Prioritization → Assessment → Reporting → Remediation → Verification**

| Phase                 | Main Question                                |
| --------------------- | -------------------------------------------- |
| **1. Discovery**      | What systems and assets do we have?          |
| **2. Prioritization** | Which systems are most important?            |
| **3. Assessment**     | What vulnerabilities exist?                  |
| **4. Reporting**      | What needs attention and how are we doing?   |
| **5. Remediation**    | How do we fix or manage the vulnerabilities? |
| **6. Verification**   | Did the fix actually work?                   |

---

# Vulnerability Lifecycle

## Overview

Vulnerability management is a **continuous process** because new vulnerabilities are constantly being discovered by attackers and software vendors.

Most vendors regularly release patches, so organizations need to continuously:

1. Discover their systems and assets
2. Prioritize them
3. Assess vulnerabilities
4. Report findings
5. Remediate vulnerabilities
6. Verify that the remediation worked

---

# 1. Discovery

The first phase is to make sure the organization knows about **all of its systems and assets**.

This is important because every:

* System
* Application
* Device

could potentially contain vulnerabilities.

### Ways organizations discover assets

Organizations can build an inventory using:

* Network traffic
* Network scanning
* Configuration Management Databases (CMDBs)
* Agents installed on workstations

### Key Point

> You need to know what systems and assets exist before you can properly manage their vulnerabilities.

---

# 2. Prioritization

After discovering the organization's systems and assets, the next step is to determine their **importance to the business**.

Systems can be prioritized based on:

* How they support the business
* The type of data they process
* The potential impact if they are compromised

Organizations often have **more vulnerabilities than they have the ability to fix**.

Therefore, they need to determine which vulnerabilities could have the **largest impact on the organization**.

### Role of GRC

Information from **GRC (Governance, Risk, and Compliance)** is important during prioritization.

GRC can provide information about the potential impact of:

* Confidentiality being affected
* Integrity being affected
* Availability being affected
* Compliance violations

This information helps vulnerability managers determine which vulnerabilities should receive priority.

---

# 3. Assessment

The assessment phase is where the organization's **current vulnerabilities are discovered**.

A vulnerability manager typically uses a:

## Vulnerability Scanner

A vulnerability scanner is a software application that communicates with systems and devices on the network.

It determines whether vulnerabilities exist and builds a database containing:

* Systems
* Devices
* Their vulnerabilities

The scanner also attempts to rank vulnerabilities based on how serious they are.

## CVSS

The industry uses a standard method for describing the seriousness of vulnerabilities called:

**CVSS — Common Vulnerability Scoring System**

CVSS gives vulnerabilities a **base score between 1 and 10**.

The score considers factors such as:

* How easy the vulnerability is to exploit
* How serious the vulnerability is

### Important Limitation of CVSS

The CVSS score is **theoretical**.

It does not automatically consider:

* The business impact of an attack
* How likely the vulnerability is to be exploited within the organization's specific environment
* Where the affected system is located within the organization's network

Therefore, the vulnerability manager combines the CVSS score with information from the **prioritization phase** and knowledge of the organization's environment.

This allows vulnerabilities to be ranked based on their **potential impact to the organization**.

---

# 4. Reporting

The reporting phase involves creating vulnerability reports that can be reviewed by the **Information Security Committee**.

Vulnerability management is important enough that organizations often include vulnerability requirements in their **information security policies**.

### Example policies from the discussion

An organization might have requirements such as:

* Vulnerabilities with a CVSS above 6 on internet-connected systems must be fixed within 7 days.
* Vulnerabilities with a CVSS above 8 on end-user systems must be fixed within 30 days.

These are examples of organizational policy requirements.

### What does GRC look for?

The GRC team looks for:

#### Policy violations

Have vulnerability remediation requirements been exceeded?

#### Trends

Are the number of vulnerabilities:

* Increasing?
* Decreasing?

Trends are important because they can show whether the organization has enough operational resources to keep up with vulnerability remediation.

If vulnerabilities continue to increase, management may need to address the lack of resources.

---

# 5. Remediation

The next phase is to **remediate vulnerabilities**.

Because organizations usually have more vulnerabilities than they can immediately fix, the first step is **triage**.

## Vulnerability Triage

Vulnerabilities are divided into three categories:

### 1. Fix

Vulnerabilities that need to be fixed.

### 2. Acknowledge

Vulnerabilities that will not be fixed because the organization's policy allows them to remain unresolved.

### 3. Investigate

Vulnerabilities that require more investigation before a decision can be made.

Ideally, vulnerabilities that fall outside the organization's security policy requirements are fixed, while those that do not require fixing are acknowledged.

---

# Ways to Remediate a Vulnerability

The method used depends on the cause of the vulnerability.

## Configuration Error

If the vulnerability is caused by an incorrect configuration:

> **Configure the system properly.**

## Software Vulnerability With a Patch

If the software vendor has provided a patch:

> **Apply the patch.**

## Upgrade the Software

If applying the patch is not an option, the organization may be able to:

> **Upgrade to a non-vulnerable version of the software.**

This may be considered when applying the patch could be too dangerous, particularly on a production system.

## Compensating Controls

If upgrading is also not possible, security engineering may develop **compensating controls**.

These controls reduce the likelihood of the vulnerability being exploited.

---

# When a Vulnerability Cannot Be Remediated

Sometimes a vulnerability cannot be fixed even though the organization's policy says it should be.

Reasons can include:

* No patch is available.
* Deploying the patch could cause the system to stop working.

In this situation, the vulnerability manager works with the **risk and compliance teams** to create an **exception**.

The exception is then managed through the organization's **exception process**.

---

# 6. Verification

The final phase is to verify that the remediation actually worked.

This can involve verifying:

* Patches
* Configuration changes
* Compensating controls

The goal is to confirm that the vulnerability **no longer exists**.

### How is this normally done?

The vulnerability scanner can be used again.

If the scanner no longer reports the vulnerability on the:

* System
* Application
* Device

then the remediation has been successfully verified.

---

# Penetration Testing

Vulnerability scanning is **not the only way** organizations discover vulnerabilities.

Organizations can also conduct **penetration tests**.

A penetration test simulates the activities of a real threat actor.

It can discover vulnerabilities that a vulnerability scanner may not find.

---

# Vulnerability Management and GRC

Vulnerability management is an interesting area of information security because it requires an understanding of:

* Different types of systems
* Vulnerabilities
* Potential business impact

It connects the **technical side of information security** with **GRC**.

---

# GRC — Final Concepts

The course explains that GRC provides answers to:

> **What should an organization do about information security, and why?**

GRC also:

* Checks whether the organization is actually doing what it is supposed to do
* Provides mechanisms to detect problems
* Helps rectify problems before they become business problems

Without a GRC function, information security may depend too much on the interests of individual managers.

The organization may then lack assurance that:

* Appropriate security is in place according to its risk tolerance
* Applicable laws are being followed
* Regulations are being followed
* Contractual obligations are being met

Therefore:

> **GRC is an important part of an organization's information security and overall success.**

---

# Quick Review

## Six Phases

**1. Discovery**
Know what systems and assets exist.

**2. Prioritization**
Determine which systems and vulnerabilities are most important to the business.

**3. Assessment**
Use vulnerability scanners to discover and rank vulnerabilities.

**4. Reporting**
Report vulnerabilities, policy violations, and trends.

**5. Remediation**
Fix, acknowledge, or investigate vulnerabilities.

**6. Verification**
Confirm that the vulnerability has actually been removed or addressed.

---

## Important Terms

| Term                         | Meaning                                                                                             |
| ---------------------------- | --------------------------------------------------------------------------------------------------- |
| **Vulnerability**            | A weakness that could potentially be exploited                                                      |
| **Vulnerability Management** | The continuous process of managing vulnerabilities                                                  |
| **Vulnerability Scanner**    | Tool used to discover vulnerabilities                                                               |
| **CVSS**                     | Standard system for describing vulnerability severity                                               |
| **GRC**                      | Governance, Risk, and Compliance                                                                    |
| **Triage**                   | Deciding whether vulnerabilities should be fixed, acknowledged, or investigated                     |
| **Remediation**              | Addressing a vulnerability                                                                          |
| **Compensating Control**     | A control that reduces the likelihood of exploitation when a vulnerability cannot be directly fixed |
| **Exception**                | A formal way of managing a vulnerability that cannot be remediated according to policy              |
| **Penetration Test**         | A test that simulates the activities of a real threat actor                                         |

---

# Main Idea to Remember

**Discover → Prioritize → Assess → Report → Remediate → Verify**

The overall goal is to continuously identify vulnerabilities, determine which ones matter most to the organization, address them appropriately, and confirm that the problem has been resolved.




![alt text](image.png)