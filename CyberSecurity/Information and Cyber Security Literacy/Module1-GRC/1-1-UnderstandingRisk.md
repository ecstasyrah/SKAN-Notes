<details>
<summary>Essential GRC</summary>

## What is GRC?

**GRC** stands for:

* **Governance**
* **Risk**
* **Compliance**

GRC answers two important questions about an organization's information security:

1. **What information security should the organization do, and why?**
2. **How does the organization know it is actually doing what it wants to do?**

---

## What Information Security Should an Organization Do?

The information security an organization decides to implement is mainly determined by **two factors**.

### Factor 1: What bad things could happen?

The organization needs to understand:

> **What could happen if it did not have information security?**

For example, the organization needs to consider the potential negative effects of security incidents on its systems and business.

### Factor 2: What must the organization do?

The organization must also determine whether there are:

* Laws
* Regulations
* Contractual obligations

that require certain information security measures.

### Together

These two factors determine what information security the organization decides to implement.

This is typically documented in an:

**Information Security Policy**
or
**Cyber Security Policy**

---

## Information Security Policy

The information security policy describes what different information security functions should do.

It can define what the organization's:

* Information security architecture
* Information security operations
* Information security administration
* Information security engineering

should do.

### Important Point

Having a policy does **not** automatically mean that people are actually following it.

This leads to the second major question of GRC:

> **Is the organization actually doing what its information security policy says it should do?**

---

## Making Sure the Policy Is Being Followed

GRC helps determine whether what the organization **intends to happen** is actually happening.

GRC activities include:

* Making sure security activities are being performed as intended
* Reviewing incidents
* Reviewing exceptions
* Understanding what incidents and exceptions mean for the policy
* Producing reports

These activities help determine whether the organization's information security policy is actually working.

---

## Reviewing and Improving the Policy

Information security will not always work perfectly.

Therefore, organizations should **regularly review and evaluate** how things are going.

The purpose is to determine:

> **How can the information security policy be improved?**

This means GRC is not only about creating policies.

It is also about:

**Reviewing → Evaluating → Improving**

the organization's information security approach.

---

## Risk in Information Security

One of the important parts of GRC is understanding:

> **What bad things could happen, and what would their effects be on the organization?**

In information security, the three major concerns are breaches of:

* **Confidentiality**
* **Integrity**
* **Availability**

These are the three types of impact discussed in the course.

---

## Confidentiality

A breach of **confidentiality** occurs when information is exposed to people who are **not authorized to see it**.

### Key Question

> Could a cyber event expose data to unauthorized people?

If yes, this is a **confidentiality breach**.

---

## Integrity

A breach of **integrity** occurs when information is altered and the organization can no longer rely on the data.

### Key Question

> Could a cyber event change data so that the organization could no longer trust it?

If yes, this is an **integrity breach**.

---

## Availability

A breach of **availability** occurs when a system becomes unavailable to the people who need to use it.

### Key Question

> Could a cyber event make a system unavailable to its users?

If yes, this is an **availability breach**.

---

## Confidentiality, Integrity, and Availability

The three can be remembered as:

| Security Concern    | What Happens?                                       |
| ------------------- | --------------------------------------------------- |
| **Confidentiality** | Unauthorized people can see information             |
| **Integrity**       | Information is changed and can no longer be trusted |
| **Availability**    | A system becomes unavailable to its users           |

---

## From Availability to Business Risk

When a system becomes unavailable, the organization may experience a **business impact**.

This is where information security connects to **risk**.

For example:

> A system becomes unavailable → people cannot use it → the organization experiences a business impact → this represents a risk to the organization.

Therefore, information security risk is not only about the technology itself.

It is about **how a security problem can affect the organization**.

---

## Organizations Manage Many Types of Risk

Organizations do not only manage information security or technology risks.

They manage many different types of risk.

The speaker gives examples such as:

* Setting the price of a product too high, causing nobody to buy it
* Paying workers too little, causing them to leave
* Running out of money and being unable to pay bills

These examples show that **risk exists throughout the organization**, not just within IT.

---

## Where Does the GRC Team Belong?

Because GRC deals with **organizational risk**, the GRC team does not always report directly to the:

* Chief Information Security Officer (**CISO**)
* Chief Information Officer (**CIO**)

The GRC team may instead belong to a **separate organizational risk department**.

Where GRC belongs depends on factors such as:

* Industry
* Organization size
* Organization maturity

---

## Different Organizational Structures

There are different ways organizations can structure GRC.

### Structure 1

The head of GRC may report into:

* IT
* Information Security

### Structure 2

GRC may be completely **independent** from IT and information security.

However, it can still work closely with:

* CISO
* CIO

### Important Point

There is no single structure that applies to every organization.

The responsibility for information security risk can be placed differently depending on the organization.

---

## GRC and Information Security Risk

Regardless of where GRC sits within the organization, one important responsibility is determining the risk associated with a breach of:

* Confidentiality
* Integrity
* Availability

The important question is:

> **What is the risk to the organization if an IT system or application experiences a breach of confidentiality, integrity, or availability?**

This connects technical security problems to their **impact on the organization**.

---

## Starting the GRC Journey

The course explains that understanding **risk** is where the GRC journey begins.

The organization needs to understand:

**What could go wrong?**

↓

**What would happen if it did?**

↓

**What would be the impact on the organization?**

Understanding this risk helps the organization determine what information security it should have in place.

---

# Main Idea to Remember

> **GRC determines what information security an organization should have, why it needs it, and whether the organization is actually doing what its policies require.**

The starting point of the GRC journey is:

## **Understanding Risk**

**What could go wrong? → What would happen? → What would the impact be on the organization?**
</details>

<details>
<summary>What is Risk?</summary>


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

</details>

<details>
<summary>Measuring Risk</summary>

## 1. Why Do We Measure Risk?

The purpose of analyzing risk is to help an organization decide **where to allocate resources**.

When an organization has multiple risks, it needs to understand:

* Which risk is the most serious?
* Which could have the highest impact?
* Which is most likely to happen?

Logically, the risks with the **highest impact and likelihood** are the ones the organization needs to worry about most.

Therefore:

> **Risk needs to be measured so organizations can compare risks and decide which ones should be addressed first.**

---

# 2. Cybersecurity Risk Measurement

Risk management is an established profession.

For example, insurance companies use risk management to determine how likely someone is to make an insurance claim.

They can use:

* Previous experience
* Historical information
* Experience with similar customers
* Experience with similar events

Cybersecurity is different because it does not have as much historical data.

The cybersecurity field is still building its history and experience.

Therefore:

> **Cybersecurity risk measurement is not as advanced or as precise as some traditional forms of risk management.**

---

# 3. Two Approaches to Measuring Cyber Risk

Organizations generally use one of two approaches:

1. **Qualitative Risk Assessment**
2. **Quantitative Risk Assessment**

---

# 4. Qualitative Risk Assessment

A **qualitative approach** uses:

* Subject matter expert opinions
* Current knowledge of information security

to estimate:

* Impact
* Likelihood

Instead of using exact numbers, organizations use **relative scales**.

---

# 5. Qualitative Impact and Likelihood

### Impact

Impact could be classified using levels such as:

* Very High
* High
* Medium
* Low
* Very Low

### Likelihood

Likelihood could be classified using levels such as:

* Almost Certain
* Likely
* Possible
* Unlikely

The exact classifications depend on the organization.

---

# 6. Risk Heat Map

Organizations often display qualitative risks using a:

## Heat Map

A heat map plots:

**Likelihood** against **Impact**

Risks toward the top-right of the heat map represent events that are:

* Very likely to happen
* Very high impact

These risks are generally the ones the organization should manage first.

---

# 7. Impact Depends on the Organization

There is no universal definition of what counts as **high**, **medium**, or **low** impact.

The classification depends on the organization.

### Financial Impact Example

For one organization:

> More than $1 million might be considered a very high financial impact.

For a much larger organization:

> $1 million might be considered a low impact.

That organization might consider an impact above $100 million to be very high.

Therefore:

> **Risk scales are dependent on the organization.**

---

# 8. Reputation Impact

The same idea applies to reputational impact.

For example:

### Low Impact

A small number of customers discussing an event on social media.

### Very High Impact

The organization becomes the lead story on television news.

The organization determines how these levels are classified.

---

# 9. Quantitative Risk Assessment

The alternative to qualitative risk assessment is:

## Quantitative Risk Assessment

Quantitative risk assessment uses **absolute figures** to produce a financial risk value.

Three important terms are:

* **SLE**
* **ARO**
* **ALE**

---

# 10. Single Loss Exposure (SLE)

**SLE = Single Loss Exposure**

SLE represents:

> **The financial impact on the organization if the event happens once.**

In other words:

**One occurrence → How much financial loss would it cause?**

---

# 11. Annualized Rate of Occurrence (ARO)

**ARO = Annualized Rate of Occurrence**

ARO represents:

> **How frequently the event is expected to occur each year.**

It estimates the number of times the event could happen within one year.

---

# 12. Annual Loss Exposure (ALE)

**ALE = Annual Loss Exposure**

ALE represents:

> **The potential financial impact to the organization in one year.**

The formula discussed is:

## `ALE = SLE × ARO`

### Example

If:

* SLE = $100,000
* ARO = 2

Then:

**ALE = $100,000 × 2**

**ALE = $200,000**

The potential annual financial impact would therefore be **$200,000**.

---

# 13. Using ALE for Decisions

ALE can help an organization decide whether it is worth spending money to prevent a risk.

The basic idea is:

> If the annual cost of preventing an event is greater than the ALE, the organization may decide that it is worth accepting the risk of the event happening.

ALE is only **one method** of quantitative risk assessment.

There are other quantitative risk assessment methods as well.

---

# 14. Other Risk Assessment Methods

The speaker mentions several other methods:

* **OCTAVE**
* **IRAM2**
* **FAIR**

Different organizations and industry sectors may use different methods.

---

# 15. Risk Rankings

Regardless of which method is used, the result is usually a set of **risk rankings**.

These rankings can be created for:

* Individual systems
* Groups of systems

The results may be displayed as:

### Heat Map

Shows risks based on their impact and likelihood.

### Ranked List

Lists risks from higher priority to lower priority.

---

# 16. Measuring Risk Is Not the End

Measuring risk does not actually change what the organization does by itself.

The risk manager or analyst has identified and ranked the risks.

The next step is:

> **Managing the risks that have been identified.**

This is the next important part of the risk management process.

---

# Risk Measurement Process

```text
Identify Risks
      ↓
Measure Risk
      ↓
Determine Impact
and Likelihood
      ↓
Qualitative
or
Quantitative
Assessment
      ↓
Rank the Risks
      ↓
Manage the Identified Risks
```

---

# Key Concept

> **The purpose of measuring risk is to help the organization compare risks, prioritize them, and decide where resources should be allocated.**

The two main approaches are:

**Qualitative → Relative ratings and expert judgment**

**Quantitative → Absolute figures and financial values**

For quantitative assessment, remember:

**SLE → Financial impact of one event**

**ARO → Expected frequency per year**

**ALE → Annual financial impact**

### `ALE = SLE × ARO`

</details>

<details>
<summary>Managing Risk</summary>

## 1. What Is Risk Tolerance?

Organizations constantly make decisions about risk.

They need to determine:

> **How much risk are we willing to accept?**

This is called **risk tolerance**.

### Risk Tolerance

**Risk tolerance** is the amount of risk an organization is willing to tolerate before the risk becomes unacceptable.

Risk measurement is important because it helps an organization identify:

* Which risks are within its tolerance
* Which risks are outside its tolerance
* Which risks need to be actively managed

---

# 2. Who Defines Risk Tolerance?

Risk tolerance is usually determined by the organization's:

**Enterprise or Organizational Risk Management function**

This information is typically passed to the:

**Information Security Risk Management team**

This allows information security teams to understand which cybersecurity risks the organization considers acceptable or unacceptable.

---

# 3. Risk Tolerance on a Heat Map

An organization can use a risk heat map to show which risks are within or outside its tolerance.

For example, an organization might decide that:

> A risk that is very likely to occur and would have a high impact is outside its risk tolerance.

Risks outside the organization's tolerance need to be **managed as a priority**.

These risks are often shown in:

**Red**

on a risk heat map.

---

# 4. Risk Appetite

Another important term is:

## Risk Appetite

Risk appetite describes risks that the organization is **willing to accept**.

This means the organization:

* Understands that the risk may occur
* Has accepted the risk
* Believes it can afford the impact if it occurs

Risks within the organization's risk appetite are often shown as:

**Green**

on a risk heat map or risk ranking.

---

# 5. Risks Between Appetite and Tolerance

There can be risks that are:

* Not within the organization's risk appetite
* But also not outside the organization's risk tolerance

These risks still need to be **managed**, but usually with less urgency than risks outside the organization's tolerance.

These risks are often shown as:

**Amber or Yellow**

on a heat map.

---

# 6. Risk Tolerance vs. Risk Appetite

The concepts can be understood as:

### Risk Appetite

> Risk the organization is comfortable accepting.

**Usually → Green**

### Between Appetite and Tolerance

> Risk the organization does not necessarily want, but it is still within its tolerance.

**Usually → Amber/Yellow**

### Outside Risk Tolerance

> Risk that is unacceptable and needs to be managed as a priority.

**Usually → Red**

---

# 7. Risk Treatment

When an organization needs to manage a risk, the formal term is:

## Risk Treatment

There are **four risk treatments**:

1. **Avoid**
2. **Accept**
3. **Transfer**
4. **Reduce**

---

# 8. Risk Treatment #1 — Avoid

**Risk avoidance** means stopping the business activity that creates the risk.

The organization essentially decides:

> **We will stop doing this activity, so the risk no longer exists.**

This may be considered when the profit from the activity is lower than the cost of reducing the risk to an acceptable level.

---

# 9. Risk Treatment #2 — Transfer

**Risk transfer** means transferring some or all of the potential impact of a risk to another party.

This can also be called:

> **Risk sharing**

Examples include:

* Insurance
* Cyber insurance
* Outsourcing

The idea is that another entity will suffer some or all of the impact if the risk occurs.

---

# 10. Risk Treatment #3 — Accept

**Risk acceptance** means the organization decides that it is willing to allow the risk to exist in its current form.

This may happen because:

* The risk is within the organization's risk tolerance
* An appropriate level of management has approved an exception to the tolerance

### Risk Register

Accepted risks should be recorded in a formal:

**Risk Register**

The risk register should be:

* Monitored
* Reviewed regularly

This is necessary to make sure that:

* The impact has not changed
* The likelihood has not changed
* The organization is still comfortable accepting the risk

---

# 11. Risk Treatment #4 — Reduce

The most common risk treatment is to:

> **Reduce the risk.**

Risk can be reduced by decreasing:

* The **likelihood** of the risk occurring
* The **impact** if the risk occurs
* Or both

In cybersecurity, this is typically done by adding:

* Technology
* Procedures
* Controls

---

# 12. Security Controls

A **control** is a technology or procedure used to reduce information security risk.

Controls can reduce the vulnerability of a system and therefore reduce the likelihood of a risk occurring.

---

# 13. Example: Reducing Likelihood

Imagine a system uses only:

**Username + Password**

The organization determines that the likelihood of unauthorized access is high because users could accidentally give their credentials to criminals through phishing attacks.

The organization could add a:

## Second Authentication Factor

For example, the user may need to confirm the login using an application on their phone in addition to entering their password.

This reduces the likelihood that a criminal can access the system using only a stolen password.

---

# 14. Reducing Impact

Risk reduction does not only mean reducing the **likelihood**.

The organization can also reduce the **impact** if the event happens.

For example, the impact of a confidentiality breach may depend on:

> **How many records are lost or how many people are affected.**

If an organization reviews how long it needs to retain data, it may be able to delete unnecessary data.

If a breach occurs afterward, there would be less data available for an attacker to obtain.

This can reduce the **impact** of the breach.

---

# 15. Reducing Both Likelihood and Impact

An organization can use multiple measures at the same time.

For example:

### Control

Add a second authentication factor.

**Result → Reduces likelihood**

### Data Retention

Delete data that no longer needs to be retained.

**Result → Reduces impact**

Together:

> **Both the likelihood and impact of the risk are reduced.**

---

# 16. Selecting Security Controls

The final stage of risk management involves looking at systems where the organization has decided to reduce risk.

The organization then selects appropriate **technical controls** to:

* Reduce likelihood
* Potentially reduce impact

Controls do not always need to be selected separately for every individual system.

---

# 17. Grouping Systems

Organizations will often group systems that have **similar levels of risk**.

They can then determine which controls should be applied:

* Across a group of systems
* Across the entire organization

This can make security management simpler.

---

# 18. Security Standards and Frameworks

Organizations can adopt internationally agreed standards or frameworks to provide a common set of controls.

The speaker gives examples including:

### NIST Cybersecurity Framework

A framework that can be applied at the organizational level to help manage cybersecurity risk.

### ISO 27001

An information security standard that organizations can use to establish security requirements and controls.

---

# 19. Securing Individual Systems

Organizations may also use configuration standards to ensure individual systems are securely configured.

Examples mentioned include:

### STIGs

**Security Technical Implementation Guides**

These are configuration standards from the **Department of Defense**.

### CIS Benchmarks

Benchmarks from the **Center for Internet Security** that can be used to help securely configure individual systems.

---

# 20. Risk Management Is Continuous

Risk management is **not something an organization does only once**.

It is a:

> **Continuous process**

Organizations constantly change.

For example:

* Business activities change
* New systems are added
* Old systems are retired
* The threat landscape changes

Because of these changes, the risk associated with systems and applications needs to be **re-evaluated regularly**.

Risks outside the organization's appetite or tolerance may then require management.

---

# 21. The Result of Risk Management

The end result of risk management is a collection of:

* Technical controls
* Operational controls

that the organization has decided to implement to keep cybersecurity risk within its:

**Risk tolerance**

Ideally, risks should also be kept within the organization's:

**Risk appetite**

---

# 22. What Information Security Should an Organization Do?

This connects back to the first GRC question:

> **What information security should an organization do?**

The answer is:

> **An organization should implement sufficient information security controls to keep its risk levels within its risk tolerance, and ideally within its risk appetite.**

The required controls are defined in the organization's:

* Information Security Policy
* Cyber Security Policy

---

# 23. Role of Information Security Teams

The organization's information security policy is used by:

* Information security architecture teams
* Information security engineering teams

These teams use the policy to design **technical controls** that meet the organization's security requirements.

The process can be viewed as:

```text
Risk Assessment
      ↓
Risk Tolerance & Risk Appetite
      ↓
Determine Which Risks Need Management
      ↓
Risk Treatment
      ↓
Select Security Controls
      ↓
Information Security Policy
      ↓
Security Architecture & Engineering
      ↓
Technical Controls
```

---

# 24. Risk Management Is Only Part of GRC

Managing risk answers an important part of the question:

> **What information security does the organization need?**

However, it is only **half of the story**.

Organizations also exist within an external environment.

That external environment can require organizations to implement certain security measures through:

* Laws
* Regulations
* Contracts

These external requirements are the next part of the GRC discussion.

---

# Main Idea

> **Risk management helps an organization determine which risks are acceptable and which need to be treated.**

The organization can:

**Avoid → Accept → Transfer → Reduce**

the risk.

For cybersecurity, reducing risk commonly involves implementing **controls** that lower the likelihood or impact of a security event.

Risk management is **continuous**, because the organization's systems, business activities, and threat landscape constantly change.

Ultimately:

> **The organization should implement enough security controls to keep cybersecurity risk within its risk tolerance, ideally within its risk appetite.**

</details>