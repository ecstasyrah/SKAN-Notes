
<details>
<summary><b>Business Processes Impacting Security Operations</b></summary>

## Overview of Change Management Processes
To make security operations as repeatable and standard as possible, organizations must take several business processes into account regarding change management.

### 1. The Approval Process
*   **Function:** 
    - Ensures changes go through defined approval channels (e.g., a manager, director, VP, or a Change Management Board).
*   **Benefit:** 
    - Creates a standard, repeatable process so nothing gets missed.

### 2. Ownership
*   **Function:** 
    - Clearly defines who owns the specific change and who owns the application or platform.
*   **Benefit:** 
    - Ensures that application owners (who might be otherwise unaware of underlying infrastructure, network, or data center changes) are properly notified.

### 3. Stakeholders
*   **Function:** 
    - Identifies the business personnel who fund the application or change.
*   **Benefit:** 
    - Keeps those who are financially or operationally invested in the loop, even if they do not manage the day-to-day infrastructure.

### 4. Impact Analysis
*   **Function:** 
    - Understanding the full implications of a change before it happens (avoiding the "scream test" where you pull a cord and see who complains).
*   **Considerations:** 
    - Requires documenting downstream impacts, subcomponents, dependencies, and what feeds into or out of the application. 

### 5. Testing
*   **Function:** 
    - Testing changes in a lower environment (like a QA or test environment) before pushing them to production.
*   **Benefit:** 
    - Test results can be included in the change management ticket to communicate risk to the proper people during the approval process.

### 6. Maintenance Windows
*   **Function:** 
    - Establishing clearly defined timeframes when changes are permitted to take place.
*   **Decision Making:** 
    - If a change is failing or taking longer than expected and risks exceeding the window, the team must decide whether to roll back or press on based on the organization's specific tolerance.

### 7. Standard Operating Procedures (SOPs)
*   **Function:** 
    - The overarching organizational rules that define all of the requirements above.
*   **Method of Procedure (MOP):** 
    - The specific documentation stating exactly *what* is going to be done during the individual change.
*   **Benefit:** 
    - Standardization ensures everyone knows exactly what to do and who to notify, which reduces risk and increases overall security.

---

## Change Management Meetings
A critical part of the process is bringing all relevant teams together (in-person or remote) to review upcoming changes on a regular cadence.

*   **Visibility:** 
    - Everyone discusses their planned changes so all groups are aware of what others are doing.
*   **Conflict Prevention:** 
    - Significantly reduces the chance of teams "stepping on each other's toes" or causing overlapping operational conflicts.

</details>

<details>
<summary><b>Approval Process</b></summary>

## Change Management Approvals
When establishing an approval process for changes, several key factors and procedures must be considered to ensure smooth execution and avoid conflicts.

### 1. Manual vs. Automated Approvals
*   **Approval Chain:** 
    - Determine if the routing of a change ticket to the appropriate approver is a manual process or an automated one.
*   **Notifications:** 
    - Ensure that the necessary people are automatically notified when a ticket requires their review or approval.

### 2. Ticket Details
*   **Required Information:** 
    - Whether the workflow is manual or automated, comprehensive details about the change and the notification process must be included directly within the ticket.

### 3. Global Change Review & Collisions
*   **Function:** 
    - Reviewing all scheduled changes across the organization to identify potential "change collisions."
*   **Benefit:** 
    - Understanding exactly who is doing what is critical to ensure teams are not stepping on each other's toes.
*   **Collision Example:** 
    - A team is in the middle of a massive storage or server upgrade while the data center team is conducting a power upgrade at the exact same time. If the power gets cut halfway through the server patch, it causes a major crisis.

### 4. Following Standard Operating Procedures (SOPs)
*   **Function:** 
    - Ensuring that every team follows the exact same standard operating procedures.
*   **Benefit:** 
    - Greatly reduces the risk of change collisions and catastrophic operational overlaps.

</details>

<details>
<summary><b>Additional Change Management Considerations</b></summary>

## Key Elements for Successful Changes
Building on the change management processes, here are several deeper considerations to ensure smooth and secure operations.

### 1. Ownership and Key Stakeholders
*   **Involvement:** 
    - Must be involved in the review and approval process.
*   **Continuous Updates:** 
    - Communication should be sent out at the start of the maintenance window, periodically throughout (e.g., every 30 to 60 minutes), and at the conclusion.
*   **Outcome Notification:** 
    - Must explicitly state if the change was successful, or if issues occurred that require a rollback or next steps to restore functionality.

### 2. Impact Analysis
*   **Scope Identification:** 
    - Identifying exactly which systems or environments could potentially be impacted by the change.
*   **Levels of Impact:** 
    - Assessing both the best-case scenario (no impact) and the worst-case scenario (system loss, X hours of downtime, cascading failure of interrelated upstream/downstream applications).
*   **Risk Assessment:** 
    - Documenting these risks (even if they are a 1-in-1,000 chance) allows owners and stakeholders to make an informed "go/no-go" decision.

### 3. Test Results
*   **Lower Environments:** 
    - Whenever possible, test changes in a lower or test environment before production.
*   **Metrics Gathered:** 
    - Helps teams understand the actual time required to implement, the level of difficulty, and the viability of rollback procedures.
*   **Ticket Inclusion:** 
    - Including concrete test results in the ticket (e.g., proving a vendor's "10-minute" change actually takes 1.5 hours) replaces hypotheticals with facts and sets the team up for success.

### 4. Maintenance Windows
*   **Definition:** 
    - Determining the acceptable timeframe to perform maintenance and establishing clear notification procedures.
*   **Rollback Thresholds:** 
    - Defining the exact point or time limit at which a failing change must be rolled back.
*   **Validation and Success Criteria:** 
    - Establishing who needs to verify the change (e.g., application owners, database, storage, and server representatives jumping on a maintenance bridge). 
    - Once all representatives validate their systems are functional, the success criteria are met, final emails are sent, and the maintenance is officially closed.

</details>

<details>
<summary><b>Standard Operating Procedures</b></summary>

## The Importance of SOPs
While extensive documentation and rules might seem cumbersome, they are essential for ensuring a successful, smooth-running operation.

### 1. Defining the Procedures
*   **Function:** 
    - A set of detailed directions on exactly how tasks are to be accomplished.
*   **Coverage:** 
    - Dictates what things are to be upgraded or patched during a maintenance window, how issues are handled, and how rollbacks are executed.
*   **Benefit:** 
    - By heavily standardizing processes, everyone knows the exact acceptable way to operate and understands how other groups are operating as well.

### 2. Approval and Escalation Procedures
*   **Approval Workflows:** 
    - Defines the chain of command for ticket approvals (e.g., a peer review first, then a manager, or higher levels depending on the time of day, time of year, or special events).
*   **Escalation Paths:** 
    - Identifies exactly who to contact internally if things go wrong.
*   **Vendor Engagement:** 
    - Determines whether to proactively open tickets with vendors to have them on standby, or wait to contact them reactively if an issue occurs.

### 3. Impacted Services, Platforms, and Environments
*   **Risk Mitigation:** 
    - Even while remaining optimistic, SOPs require teams to account for the worst-case scenario.
*   **Proactive Notification:** 
    - Documenting potential downstream impacts allows teams to proactively notify other application owners to be on standby.
*   **Validation:** 
    - Ensures those standby teams join the bridge at the end of the maintenance window to validate that their applications remain functional.

</details>

<details>
<summary><b>Technical Implications</b></summary>

## Change Management Technical Implications
When planning and executing changes, several technical implications must be clearly defined and accounted for within standard operating procedures (SOPs).

### 1. Allow/Deny Lists
*   **Function:** 
    - Clearly defines what can and cannot be done.
*   **Scope:** 
    - Applies to users (who is allowed to execute tasks), resources (computers, laptops), and network traffic (what can pass through specific points).

### 2. Restricted Activities
*   **Definition:** 
    - Activities that require special authorization, such as VP approval or elevated access to specific file systems or data center areas.
*   **Benefit:** 
    - Explicitly defining these within SOPs ensures everyone is on the same page and adheres to the proper processes.

### 3. Downtime
*   **Planned Downtime:** 
    - Expected and communicated (e.g., a maintenance window). Everyone is aware the system will go down for patching or upgrades and is on standby.
*   **Unplanned Downtime:** 
    - Unexpected outages often resulting from a lack of procedure. This can severely impact revenue and business operations.

### 4. Service or Application Restarts
*   **Planning Requirement:** 
    - All required restarts must be explicitly called out in the change ticket.
*   **Enterprise Complexity:** 
    - Unlike a personal computer that reboots in seconds, large enterprise *n-tier* or clustered applications must come up in a very specific order and can take 30 to 60 minutes to become fully operational.

### 5. Legacy Applications
*   **Definition:** 
    - While typically meaning "old" or unpatched, it can also just mean an application slated for a refresh.
*   **Risk Factor:** 
    - Some older applications have been running untouched for years (often because the original maintainers retired). 
*   **Contingency:** 
    - If taken down for maintenance, there is a real risk it may not come back up. Strict contingencies must be planned.

### 6. Dependencies
*   **Scope:** 
    - Applications rarely operate completely standalone; they rely on databases, web services, customer service modules, or finance tools.
*   **Impact:** 
    - You must account for upstream (data feeding in) and downstream (data/reports feeding out) dependencies.
*   **Notification:** 
    - Even if a dependent team is not directly involved in the application change, they must be included in the communications so they are aware of the changes taking place.

</details>

<details>
<summary><b>Documentation and Version Control</b></summary>

## Documentation Updates
Keeping documentation accurate and up-to-date is a critical component of change management and security operations.

### 1. Diagrams
*   **Types:** 
    - Includes user flow, information flow, and technical diagrams (such as as-built diagrams and reference architectures).
*   **Evolution:** 
    - These naturally evolve over time as systems are added, deprecated, or new features are introduced.
*   **Best Practice:** 
    - Documentation should be updated as close to real-time as possible to reflect the actual, current state of the environment.

### 2. Policies and Procedures
*   **Lessons Learned:** 
    - Insights gained from handling issues, outages, or critical incidents should be used to identify gaps in current processes.
*   **SOP Updates:** 
    - When new processes are created to cover these gaps, the Standard Operating Procedures (SOPs) and policies must be immediately updated to reflect the new workflow.

---

## Version Control
Version control is essential for managing changes securely, particularly in coding and DevOps environments.

### 1. Core Functions
*   **Tracking:** 
    - All code changes are tracked and safely stored, typically in a database.
*   **Rollbacks and Auditing:** 
    - Enables quick rollbacks to any previous point in time if an issue occurs, and allows for detailed auditing to see exactly what changes were made and when.
*   **Branching/Forking:** 
    - Allows code to be branched out so multiple developers can safely work on different features simultaneously without causing conflicts.

### 2. Revision Numbering
*   **Minor Changes:** 
    - Typically annotated by point (or dot) releases to denote small updates (e.g., v1.1, v1.2, v1.3).
*   **Major Changes:** 
    - Annotated by incrementing the primary number to denote a significant change or overhaul (e.g., jumping from v1.3 directly to v2.0).

### 3. Version Control Systems
*   **Git:** 
    - The underlying version control engine and repository infrastructure.
*   **Platforms:** 
    - Services like GitHub, GitLab, and Bitbucket are web-based (or sometimes on-premise) management platforms that utilize Git, allowing developers to collaboratively check code in and out.

</details> 