<details>
<summary><b>Systems Patch Management</b></summary>

## Security Administration
At this point, we have discussed **security architecture** as the blueprint or plans, and **security engineering** as the building work or implementation. Once the building is completed, we must maintain or manage its administration. 
*   **Administrator's Responsibility:** The security system has multiple software and hardware components that must be continuously updated so they can withstand attacks. 

## The Role of Change Management
The patch management process commences **only after approval is given** from change management supervision.
*   **Scope:** Change management oversees changes in the entire ecosystem of an organization's resources.
*   **ITIL Version 4:** Describes change management as a process that benefits an organization's value chains.
*   **Process:** Change management meetings discuss the services provided by the technology. Once a change to that service is approved, the decision filters down to other management areas, like configuration, vulnerability, and patch management.

## The Patch Management Process
Patch management includes the following key concerns and steps:

### 1. Vulnerability Assessment
Identifies software or system vulnerabilities. 
*   Can be done through regular vulnerability scanning or testing.
*   Involves staying informed about security advisories and patches released by software vendors.

### 2. Patch Prioritization
Once vulnerabilities are identified, they must be prioritized based on their severity. Understanding the potential impact and other factors helps determine which patches should be applied first.

### 3. Patch Acquisition
Relevant patches must be obtained from the appropriate sources. This typically involves downloading patches from software vendors' websites using patch management tools or leveraging other trusted sources.

### 4. Testing and Validation
This happens *before* deploying patches into production. It is essential to test patches in a non-production or test environment to validate:
*   Compatibility
*   Functionality
*   Potential impacts of the patch

### 5. System Backups
It is a crucial step to ensure proper backups are completed on a system *before* a patch is applied. Patches can sometimes corrupt a system and make it inaccessible.

### 6. Patch Installation (Deployment)
Once validated, the patches can be deployed into production environments. This can be done using automated patch management tools, software deployment, or other system tools.

### 7. Monitoring and Verification
After the patch has been deployed, the organization needs to continuously monitor and verify that the patch is acting as proposed and that no issues or anomalies have surfaced.

</details>

<details>
<summary><b>IAM and the IAAA Process</b></summary>

## The IAAA Flow
Identity and Access Management (IAM) solutions are closely tied to the **IAAA process**. This flow is essential for secure systems and data access. 

*   **Identification:** The profession of who or what someone or something is.
*   **Authentication:** The proof of that profession.
*   **Authorization:** Defines what resources one can get access to.
*   **Accountability:** Verifies that what has taken place is non-repudiated.

---

### 1. Identification
Identification is established by using a unique identity for users, systems, or devices. 
*   **Identifiers:** Typically achieved through unique identifiers such as usernames, email addresses, IP addresses, or digital certificates.
*   **Enrollment:** The attributes used for identification are determined during the enrollment or entitlement process.
*   **Tools Used:** User directories, databases, or repositories. These store useful information (like usernames, passwords, and other attributes) needed for the user to access resources.

---

### 2. Authentication
Authentication is the process of verifying the identity of users or systems.
*   **Credentials:** Typically done using passwords, tokens, or biometrics to prove the claimed identity is valid.
*   **Single Sign-On (SSO):** A system tool that allows users to authenticate once and then gain access to multiple systems and applications without re-authentication.
*   **Multi-Factor Authentication (MFA):** Deployed to increase security and protect high-level resources by providing additional verification beyond just a password.
    *   *Factors include:* Something you know, something you are, or something you have (e.g., fingerprint, smart card, or a one-time passcode).
    *   *Examples:* Duo Security, Google Authenticator, and RSA SecurID.

---

### 3. Authorization
Authorization is the process of granting or denying access permissions based on the authenticated user's identity and privileges.
*   **Access Control Systems:** Enforce access permissions using models like:
    *   **RBAC:** Role-Based Access Control.
    *   **ABAC:** Attribute-Based Access Control (which works closely alongside Zero-Trust architecture).

### 4. Accountability
Accounting proof that the policies implemented are working accomplished by logging and monitoring
*   **Policy Management:** A system defines and manages policies that govern access permissions based on criteria such as user roles, groups, attributes, or contextual information.
    *   *Examples:* Policy-based security frameworks provided by cloud platforms like AWS, Azure, or Google Cloud.


</details>

<details>
<summary><b>Identity as a Service (IDaaS) and Emerging IAM Capabilities</b></summary>

## Primary Roles in IAM
There are three primary roles that facilitate the IAAA process flow and Identity and Access Management (IAM):
1. **User** (or Principal)
2. **Service Provider** (or Relying Party)
3. **Identity Provider**

---

## Identity Protocols and Frameworks
These roles manifest in several protocols used for secure access:

### SAML (Security Assertion Markup Language)
*   **Function:** How a user gets access to resources on the web.
*   **Mechanism:** The service provider holds the data, and the identity provider provides a token representing authentication and authorization for those resources.
*   **Scope:** Provides **authentication and authorization**.

### OAuth 2.0
*   **Function:** How a client application accesses resources.
*   **Mechanism:** A token comes from an authorization server that gives access to a resource server.
*   **Scope:** Provides **authorization**.

### OpenID Connect
*   **Function:** An authentication protocol that works across multiple web security boundaries.
*   **Mechanism:** Uses the OAuth framework to accomplish authorization.
*   **Scope:** Provides **authentication and authorization**.

---

## Identity as a Service (IDaaS)
These services have been bundled into **Identity as a Service (IDaaS)**.
*   **Major Providers:** Okta, Microsoft Azure Active Directory (Azure AD), OneLogin, Ping Identity, Auth0, and ForgeRock.
*   **Selection Factors:** When choosing an IDaaS provider, consider security, scalability, ease of use, integration capabilities, and pricing to meet your organization's specific needs.

---

## Just-in-Time (JIT) Authentication and Authorization
JIT supports **least privilege** and **Zero-Trust environments** by allowing temporary access to resources to reduce the potential for compromise. 

Its capabilities include:
*   **Contextual Analysis:** The IAM system analyzes contextual factors (e.g., user identity, location, device information, time of day, and other attributes) to determine if granting access is appropriate.
*   **Authorization Decisions:** The IAM system grants or denies access based on the contextual analysis.
*   **Access Provisioning:** If granted, the IAM system provisions necessary privileges (issuing temporary access tokens, updating user roles/permissions, etc.) to allow access to the requested resource.
*   **Access Revocation:** The ability to revoke access once it is no longer needed.

</details>