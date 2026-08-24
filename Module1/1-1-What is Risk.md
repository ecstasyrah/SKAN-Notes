# What Is Risk?

## 1. What Is Risk?

Risk is an important foundation of **GRC (Governance, Risk, and Compliance)**.

At its simplest, risk is about understanding:

> **What effect could a bad event have on an organization?**

In information security, the bad event is generally a reduction in:

* **Confidentiality**
* **Integrity**
* **Availability**

---

# 2. The Two Components of Risk

Risk has **two main components**:

1. **Likelihood** — How likely is the bad thing to happen?
2. **Impact** — What would happen to the organization if it did?

### Risk

> **Risk = Likelihood + Impact**

The exact way risk is calculated or expressed is discussed later in the course.

Both likelihood and impact can be different for every system and organization.

This is because organizations have different:

* Business activities
* Systems
* Data
* Threats
* Security environments

---

# 3. Example: Availability Risk

Consider a company's video servers that are available on the internet.

Imagine a criminal tries to disable the servers so customers cannot watch videos.

### Impact

The impact could be significant because:

* Customers would become unhappy.
* Customers might move to other companies.
* The company could lose business.

### Likelihood

The likelihood could also be reasonably high because:

* The servers are accessible from the internet.
* Attackers can attempt to attack them.
* Disabling internet-facing systems is a common criminal activity.

The attacker could use a:

## Denial-of-Service Attack

A **denial-of-service attack** attempts to make a system unavailable by overwhelming it with requests so that it cannot respond to legitimate users.

The attacker may then attempt to demand a ransom in exchange for stopping the attack.

Because both the **likelihood** and **impact** could be significant, this would be an important risk for the organization to consider.

---

# 4. Different Types of Risk for One System

A system does not have just one risk.

There are three separate areas to consider:

* **Availability risk**
* **Integrity risk**
* **Confidentiality risk**

These risks can have different:

* Impacts
* Likelihoods

### Example

Instead of making the video servers unavailable, imagine someone breaks into the servers and copies all the videos.

The impact would be different from an availability attack.

The likelihood could also be different because the type of attacker and their motivation would be different.

Therefore:

> **The risk of confidentiality, integrity, and availability breaches will not necessarily be the same for a particular system.**

---

# 5. Understanding Impact

A cybersecurity event can affect an organization in several ways.

The impact could involve:

### Physical Safety

An event could affect the physical security or safety of people.

### Financial Impact

It could affect:

* Revenue
* Profit
* Costs

### Reputation

An event could generate:

* Press attention
* Social media attention

This could harm the organization's reputation.

### Regulatory or Legal Impact

The organization could face:

* Regulatory action
* Legal action

### Privacy

A cybersecurity event could affect the privacy of:

* Customers
* Workers

Privacy is a special type of risk because the impact can affect the people whose data is being processed, rather than only the organization itself.

---

# 6. Example of a Confidentiality Breach

Consider a breach involving sensitive customer information.

A confidentiality breach could affect:

* People's privacy
* The organization's finances
* The organization's reputation
* Privacy and data protection requirements
* Potentially, the physical safety of people depending on the type of information involved

This demonstrates that **one cybersecurity event can have multiple types of impact**.

---

# 7. Understanding Likelihood

When considering the likelihood of a cybersecurity event, several factors need to be considered.

The speaker discusses four main areas:

1. **Threat**
2. **Threat actor motivation**
3. **Threat actor capabilities**
4. **System vulnerability**

---

# 8. Threat

The first consideration is the **source of the event**, or the threat.

The threats discussed are:

* Equipment failures
* Environmental events
* Errors
* Purposeful attacks

Cybersecurity is particularly concerned with **purposeful attacks**.

### Equipment failures, environmental events, and errors

These typically affect the **availability** of systems.

### Purposeful attacks

Purposeful attacks can affect:

* Confidentiality
* Integrity
* Availability

---

# 9. Threat Actor Motivation

A **threat actor** is the person or people responsible for an attack.

The motivation of the threat actor affects the likelihood of an attack being successful.

Important questions include:

* Does someone have a reason to attack the organization?
* How valuable would the attack be to them?
* How persistent would they be?

The motivation depends on what the attacker could gain from affecting the organization's:

* Confidentiality
* Integrity
* Availability

This can depend on:

* What the system does
* What data the system contains

---

# 10. Different Threat Actors Have Different Goals

Different attackers may target different systems depending on what they want.

### Criminal seeking ransom

A criminal looking for ransom may target a system where affecting **availability** would have the most immediate effect on operations.

### Foreign spy agency

A foreign intelligence agency may target a system containing information that is most useful to them.

Therefore:

> **The motivation of the threat actor affects which systems they are likely to target.**

---

# 11. Threat Actor Capabilities

Another factor is how capable the threat actor is at carrying out cyber attacks.

The organization needs to consider the attacker's **cyber capabilities**.

Possible threat actors mentioned include:

* Spy agencies of other countries
* Organized criminals
* Contract hackers working for competitors
* Activists

Different threat actors can have different levels of capability and resources.

---

# 12. System Vulnerability

The final factor discussed when considering likelihood is:

> **How vulnerable is the system to an attack?**

Questions to consider include:

* Is the system accessible from the internet?
* Is it deeply inside the internal network?
* Is it running supported software?
* Is the software receiving updates from the vendor?
* Is the system properly secured?
* Does the organization still have people who understand the system?

These factors help determine how vulnerable the system is.

---

# 13. Risk Assessment

A **risk manager** or **risk analyst** is typically responsible for understanding risk.

For each system or application, they need to determine:

### Impact

What would be the impact of a breach of:

* Confidentiality
* Integrity
* Availability

### Likelihood

How likely is each type of breach to occur?

---

# 14. Understanding the System

Before assessing risk, the risk manager or analyst needs to understand the system itself.

They may consider:

* What role does the system play in the organization?
* What data does it contain?
* Where is the system located?
* Is it connected to the internet?
* Is it deeply inside the organization's network?

Understanding the system provides the context needed for assessing its risk.

---

# 15. Assessing the Impact

The risk manager or analyst considers the potential:

* Safety impact
* Financial impact
* Reputation impact
* Regulatory impact
* Privacy impact

They consider these impacts for breaches of:

* Confidentiality
* Integrity
* Availability

---

# 16. Assessing the Likelihood

The risk manager or analyst then considers how likely each type of breach is.

Important questions include:

### Threat Actor

Who could attack the system?

### Motivation

Why would they attack it?

### Capabilities

How capable are they of carrying out the attack?

### Vulnerability

How vulnerable is the system to the attack?

---

# 17. From System Risk to Organizational Risk

Risk is assessed for **each individual system and application** in the organization.

The risk manager or analyst then combines these individual assessments to create an:

> **Overall cybersecurity risk assessment for the organization**

This means the organization can understand risk at two levels:

**Individual system/application risk**

↓

**Overall organizational cybersecurity risk**

---

# 18. Risk Assessment Process

The process discussed in the lesson can be summarized as:

```text
Understand the System
        ↓
What does it do?
What data does it contain?
Where is it?
Is it internet-connected?
        ↓
Assess Impact
        ↓
Confidentiality
Integrity
Availability
        ↓
Assess Likelihood
        ↓
Who is the threat actor?
What motivates them?
What are their capabilities?
How vulnerable is the system?
        ↓
Determine System Risk
        ↓
Combine Individual Risks
        ↓
Overall Cybersecurity Risk
```

---

# Main Idea

> **Risk is about understanding how likely a bad event is to happen and what impact it would have on the organization.**

For information security, risk is assessed by considering the potential breach of:

**Confidentiality + Integrity + Availability**

while also considering:

**Threat + Threat Actor Motivation + Threat Actor Capabilities + System Vulnerability**

Risk managers and analysts use these factors to assess individual systems and applications and eventually build an overall cybersecurity risk assessment for the organization.
