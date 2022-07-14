# Continuous Threat Modelling

## Introduction

Threat modeling is a structured approach of identifying and prioritizing potential threats to a system, and determining the value that potential mitigations would have in reducing or neutralizing those threats. This guide provides an approach to creating a threat model for both existing systems or applications as well as new systems, and then explains how to keep the model current with continuous threat modelling (CTM).

## Who Performs Threat Modelling

Practically everyone in your development team has a stake in a threat model. You don't need to be a security expert to apply the techniques covered in this guide. All developers, software and system designers, and architects should strive to include threat modeling in their software development life cycle. Optimally, you will create your threat models and decide which you need mitigations during an early stage of the development of a new system, application, or feature. Assessing potential threats during the design phase of your project can save significant resources rquired to refactor the project to include risk mitigations during a later phase of the project.

### Mission Statement

The full threat modelling service that a security department typically provides for development teams is a valuable but scarce resource. Furthermore, those security engineers will never have the intimate knowledge of the system that the developers do. The growing pace and volume of new features and systems dictates the need to scale the role of threat modlling, which will be reasonably acomplished by transferring know-how to development teams. The approach outlined in this guide provides a structure for development teams to translate their product knowledge into security findings, following a guided approach to questioning its security posture. The goal of this approach is to support and strengthen the development team’s security capabilities with repeated practice to the point where the quality of the threat model executed by the development team will require minimal involvement of your security team.

### When to Threat Model

The modern emphasis on agile development doesn't allow development to stop while a team of experts gather into a room to perform some kind of official threat modelling ceremony.

The threat model is a living document and needs to be treated as such, evolving together with the system being developed. Ideally it starts at design time, and evolves together with the system, but most commonly it starts with an effort to model what already exists and then follows the life of the product.

The threat model should be refreshed once a year or when there are major architectural changes. Ideally, threat modelling should become part of the Definition of Done for every story with a security-notable event (this is covered in more detail in the next section, The Threat Model is a Living Document - "Threat Model Every Story".

## The Threat Modelling Approach

### First Steps

The overall exercise of threat modelling must be performed as a team. Delegate the responsibility of updating the threat model to an architect or senior developer, the *curator*. The curator is responsible for keeping the threat model current as it evolves. The curator also owns a queue in the development team’s bug tracking system where tickets are marked with a label such as, *potential-tm-update* and treated as input events for consideration in the threat model. The curator is responsible for adding the required information to tickets in the bug tracking system and updating or closing them, as appropriate.

Threat modelling creates an opportunity for informative discussion within the team and isn't limited to team members with specific responsibilities.

The following objectives will produce a representative data flow diagram (DFD) of the system:

*Define the scope*
: Are you threat modelling a full system or just a small design change. Decide which elements of the system will be part of the threat model.

*Identify all important assets*
: Include all relevant parts of the system in the threat model. If you are worried about too much detail, start with a top-level description of the system and repeat the process for more detailed views of smaller parts.

*Draw diagram(s)*
: Create diagrams of the system to be modeled, based on the scope. These should include, minimally, the actors who use the system (users, administrators, operators, etc.), the way they interact with the system, browsers, desktop clients, servers, load-balancers and firewalls, etc. You can use a picture of a diagram on a whiteboard, or a diagram created in Visio™, Lucidchart™, Draw.io, etc. Keeping the diagram up to date will be easier with an open-source tool such as [PyTM](https://github.com/izar/pytm) or [Dataflow](https://github.com/sonyxperiadev/dataflow). What matters is that the team agrees that the diagram correctly represents the pieces of the system.

*Draw dataflows*
: Picture the interactions between the pieces in terms of data flowing. Annotate it with details: protocol, authentication, etc.

*Mark where the important data lives, transits and is transformed*
: This is important, as here you’ll find out which data you are trying to protect and where it appears in the system. Continue to the section, [DFD Details](#dfd-details), below to ensure the final DFD(s) include the required information.

Notice that the format of the DFD is important. If all teams follow the same format, it's easier for everyone to locate the same information in distinct threat models, and for members of your security team to more easily absorb this information when working with multiple development teams.

### DFD Details

It may help to use some of the questions below to start adding more info such as protocols and the type of data in each request and response between the components, especially those owned by the team or anything else custom or new. If adding all of this info to the high-level DFD ("L0") gets too dense for readability, please break up the diagram into separate, more detailed DFDs ("L1").

* How do each of the components communicate? (what protocol is in use for each data flow?)
* What is exchanged in each data flow (what information does it have? what's the purpose of the request/response? What type of data is it? Example: creds, authentication, HTML).
* Who stores what and where? (for example, what's stored in the DB?)
* Show how data flows through the application (How and where are decisions made?)
* What are the authentication and authorization checks in place for each data flow? In what order do they occur?

Here are the outcomes that are needed for the DFDs. Even if this is written elsewhere in the Threat Model document, there still needs to be a complete visualization in one or more L0 and L1 DFDs:

* Provide a complete diagram of your system, including deployment.
* Label each component in the system overview DFD (L0).
* Label the direction of each data flow using arrows (to/from/bidirectional).
* Label the main actions that each arrow represents.
* Label the protocol used for each data flow.
* Label the trust boundaries and networks.
* Label the main types of data and how they flow through the application (control flow) in the detailed DFD (L1).
* Describe the personas who use the system (users, administrators, operators, etc.) and show how data flow/access differs for each person.
* Label each part of the authentication process.
* Label each part of the authorization process.
* Label the order of these actions numerically.
* Label the "crown jewels", what's the most sensitive data? How's it handled? What is the most critical application functionality?

### Performing the Threat Model

When the diagram has enough information, the team iterates over it searching for places where there are possible failures in protecting Confidentiality, Integrity and/or Availability.

Start with answering the items below in order to focus the effort. This isn't an exhaustive list and attention must be taken to integrating these with existing mechanisms, such as widely-used company authentication APIs, cloud provider resources and processes, etc.

For each finding, identify a scenario where the finding is exploited (an “attack scenario”), what would be the impact to the system and company as a whole, and examine how likely it's that this scenario would happen.

|Subject|Sample questions under that subject|
|--------|----------------------------------------|
|Authentication and Authorization|<ul><li>How do users and other actors in the system, including clients and servers, authenticate each other so that there is a guarantee against impersonation? </li><li>Do all operations in the system require authorization, and are these given to only the level necessary, and no more (for example a user accessing a database has limited access to only those tables and columns they really need access to)?</li></ul>|
|Access Control|<ul><li>Is access granted in a role-based fashion? Are all access decisions relevant at the time access is performed? (token/permissions updated with state-changing actions; token/permissions checked before access is granted).</li><li>Are all objects in the system subject to proper access control with the appropriate mechanisms (files, web pages, resources, operations on resources, etc.)?</li><li>Is access to sensitive data and secrets limited to only those who need it?</li></ul>|
|Trust boundaries |<ul><li>Can you clearly identify where the levels of trust change in your model?</li><li>Can you map those to access control, authentication and authorization?</li></ul>|
|Auditing|<ul><li>Are security-relevant operations being logged?</li><li>Are logging best practices being followed: no PII, secrets are logged. Logging to a central location, compatible with industry standards such as SIEM, RFC 5424 and 5427, and OWASP. Is Cloudtrail being properly used?</li></ul>|
|Cryptography|<ul><li>Are keys of a sufficient length and algorithms in use known to be good (no collisions, no easy brute-forcing, etc.)?</li><li>Are all implementations of crypto well-tested and up to their latest known secure patch, and is there no use of cryptography developed in house?</li><li>Can cryptography be easily configured/updated to adapt to changes? </li></ul>|
|Defense of secrets|<ul><li>What are the tokens, keys, credentials, secrets, etc. in your system?<ul><li>How are they protected?</li><li>Are any secrets distributed with the application (hard-coded)?</li></ul></li><li>Are well-established and tested systems being used to store secrets?</li><li>Are any secrets (API or SSH keys, client secrets, AWS access keys, SSL private keys, chat client tokens, etc.) stored unencrypted in repositories, document shares, container images, local storage in browser, etc.?</li><li>Are secrets passed in through environmental variables as part of any build/deploy procedures?</li><li>Are secrets and sensitive data scrubbed from memory as soon as they're used, or is there a possibility that they would be logged?</li><li>Can keys be easily rotated?</li></ul>|
|Injection|<ul><li>Are all inputs coming from outside the system being inspected for malformed or dangerous input? (this is especially relevant to systems accepting data files, input that gets displayed as part of web pages, binaries, scripts or input that gets directly incorporated into SQL queries, and to systems that embed interpreters of, among others, Lua, JavaScript, LISP, etc.).</li></ul>|
|Data encryption in transit and at rest|<ul><li>Is all the important data in the system, the *crown jewels*, protected when it's transmitted between parts of the system and when it's being stored – both from external and internal attackers?</li></ul>|
|Data retention|<ul><li>Together with the issue of data protection in transit and at rest, are we saving and retaining more data than we need?</li><li>Is data being retained for the time and in the manner required by compliance requirements?</li></ul>|
|Data minimization and privacy|<ul><li>In case we're saving personal data, are we protecting it according to all needed standards and compliance requirements?</li><li>Do we need to minimize and/or anonymize retained data?</li></ul>|
|Resiliency|<ul><li>Does the system depend on any single point of failure that could suffer a denial of service attack?</li><li>If the system is distributed among many service nodes, is it possible to isolate a part of them, degrading the service but not interrupting it, in case of a localized security breach?</li><li>Does the system provide feedback controls (monitoring) to allow it to call for help as a denial of service or system probing takes place?</li></ul>|
|Denial of Service|<ul><li>Consider multi-tenancy - can one tenant generate a computation or I/O that would preclude other tenants from working?</li><li>Consider storage - can one tenant fill up all storage and stop others from working?</li></ul>|
|Configuration management|<ul><li>Is the system set up to be managed by a centralized configuration management tool and/or process, with backed up and protected configuration files?</li></ul>|
|Third Party Libraries and Components|<ul><li>Are all dependencies (both direct and transitive):<ul><li>Updated to mitigate all known vulnerabilities?</li><li>Obtained from trusted sources (for example, published by a well known company or developer that promptly addresses security issues) and verified as originating from the same trusted source?</li><li>Code-signing for libraries and installers is highly recommended - has code-signing been implemented?</li></ul></li><li>Does the installer validate checksums for components downloaded from external sources?</li><li>Is there an embedded browser (embedded Chromium, Electron framework, and/or Gecko)? If so, please see the *API* section under "Performing Threat Model".</li></ul>|
|Hardening|<ul><li>Has the system design taken into consideration that the system must run in a hardened environment (closed egress ports, limited file system permissions, etc.)?</li><li>Do the installer and application processes require only the minimum privileges needed to run? Do they drop privileges whenever possible?</li><li>Are hardened images being used on cloud platforms</li><li>Does the app only load libraries using absolute paths?</li><li>Was isolation of the service (containerization, limiting consumption of host resources, sandboxing) considered in the system's design?</li></ul>|
|Cloud Services|<ul><li>Have the known best practices been followed in design and use of cloud services?</li><li>Role requirements and secure policies</li><li>Use of MFA where appropriate</li><li>A plan for API key rotation</li><li>Has root access (to your cloud provider management system) been correctly hardened, managed and configured?</li><li>Have permissions been tightened for each cloud service?</li><li>Is all backchannel (server-to-server, internal APIs) communication being routed internally via VPC peering (this is, backchannel traffic doesn't go over the public Internet).</li></ul>|
|Dev/Stage/Prod practices|<ul><li>Are the environments adequately protected?</li><li>For non-production testing environments (especially staging/integration), is test data sourced from production? If so, is sensitive data (for example, personally-identifiable information, customer data, etc.) scrubbed or masked before non-production use?</li><li>Is email functionality always tested using company-managed email accounts (this is, not using public email providers such as test@gmail.com)?</li><li>Are code reviews performed per commit, by a qualified person? (No direct commits to release or main branches)</li><li>Is any security feature (login, encryption, object rights management, etc.) not covered by unit/function tests?</li></ul>|
|API|<ul><li>Should you be looking into CORS if your API will be made available to browsers?</li><li>Are you using the right mode of authentication and authorization?</li><li>Are you considering impersonation? Injection?</li></ul>|

### Additional Resources

You can learn more about threat modelling methodologies in the short document [Tactical Threat Modeling - SAFECode](https://www.safecode.org/wp-content/uploads/2017/05/SAFECode_TM_Whitepaper.pdf). For teams looking for more detailed guidance on these (and more) items, we suggest the document [Avoiding The Top 10 Software Security Design Flaws](https://ieeecs-media.computer.org/media/technical-activities/CYBSI/docs/Top-10-Flaws.pdf) by the IEEE Center for Secure Design.

### The Output of a Threat Modelling Exercise

The output, a threat model, is a document (MS Word, HTML, etc.) that should be appropriately stored so that only stakeholders have access. The final threat model document must have a brief description of your product, Data Flow Diagram(s) and the threats identified (following the structure described later in this guide). A complete threat model will include the following:

1. General and detailed Data Flow Diagrams (L0 and L1 DFDs)
1. Network traffic requirements (ports in use, requirements from firewalls, etc.)
1. Questions in the [Performing the Threat Model](#performing-the-threat-model) section have been answered.
1. Findings that the development team identified with ticket links (format specified below in the section, [Documenting Findings](#documenting-findings)).
1. In the threat model document, list:
    * Location and nature of sensitive data, assets, and functionality that we want to protect.
    * Uses of cryptography.
    * The threat model curator and other stakeholders involved.

### Documenting Findings

Once a number of findings are identified and the development team can't come up with additional findings, these need to be ranked by priority.

There are many ways of ranking, and information security often uses [CVSS - Common Vulnerability Scoring System V3](https://nvd.nist.gov/vuln-metrics/cvss/v3-calculator) format, which derives a single number from 0 to 10 based on the characteristics of the flaw. CVSS is pervasive across tools like scanners and is also used for publicly-disclosed vulnerabilities, but it's sometimes difficult to adapt the metrics to the circumstances. In that case, attaching a value to the likelihood (Unlikely=1 → Highly Likely=4) and to the impact (Low Impact=1 → Critical=4) allows a simple calculation of Risk=Likelihood X Impact. With that said, it's important to be consistent, or you may end up solving problems in a sub-optimal order.

To facilitate tracking and remediation of threat model findings, include the following information in your documentation:

* ID associated with the finding in your bug tracking system. Tickets must be created for all the findings.
* Attack Scenario (how an attacker would exploit this flaw)
* Severity (what would be the impact to the system and to the company as a whole? How likely is this to happen?) An explanation of how you arrived at the CVSSv3 score or the risk must also be present.
* Use CVSSv3 or Risk Rating to score the issue.
* Express the result as a priority level for tickets in your bug tracking system: Trivial/Minor/Major/Critical.
* Mitigation (how-to, or not-mitigated with justification).
Unmitigated findings should be added to the threat model document, including their attack scenarios and CVSSv3/Risk score. Please notice that a threat model containing unmitigated flaws becomes a sensitive document, and its distribution should be restricted accordingly.

### Closing Findings

Identifying valid findings is the objective of threat modelling, but the process is only complete if these findings are mitigated. Identify the changes to the design that are needed in order to mitigate the findings, and document them in your bug tracking system. Once these changes have been implemented, update design documents and confirm that the system stands more secure after it. Keep your threat model updated to reflect what the system *is*, not what it was or could be.

### Threat Models and Definition of Done

A threat model is considered complete if the following criteria are met:

* All of the relevant diagrams are in the document.
* The findings are documented in the format described in the section, [Documenting Findings](#documenting-findings).
* A review request is created, if necessary[^1].
* Post-review, all bugs are documented in the development team’s bug tracking system.
* The threat model is stored in a central, access-controlled location.

[^1]:A threat model review from the security team should be requested for a new release, or should be made **at least once a year**.

### How is your Threat Model reviewed?

Schedule the threat model review for a period of time when one or more stakeholders have bandwidth to:

* Engage in informal Q&A with a member of your security team
* Make updates to the DFD(s)
* Discuss potential findings
* Seek out information from other teams (who manage infrastructure, provide services/data, etc. for your application), as necessary.

Once the first draft of the threat model is ready, a member of your security team should review them for completeness and support the team if more work is needed.

The main review process involves Q&A with the stakeholder(s) about threat modelling materials provided such as DFDs and guide starter questions. This process takes several rounds and may be conducted through ticket comments, in person or online via a meeting/chat client, etc.

### Asking for Assistance

Request assistance from your security team when needed, whether you are in the process of planning, performing or finishing a threat model.

For more formal instruction, SAFECode has an online training module called [Threat Modeling 101](https://safecode.org/courses/threat-modeling-101/) that takes a deeper look on questions of diagramming, annotation, and risk rating.

## The Threat Model is a Living Document - "Threat Model Every Story"

As the product evolves, so does the threat model. Make sure that the threat model constantly reflects the present situation of the product, or details and important findings may be missed.

Events that may be of interest to the threat model are generated by team members as tickets in your bug tracking system and marked with a label, such as *potential-tm-update*. Some examples of notable events when a threat modelling must be done:

* Developers and architects make changes to the product architecture: add services, expand the attack surface by adding input vectors, network ports and services, etc.
* Change deployment requirements.
* Team merges, incorporates or inherits code from other teams. In this case the incoming codebase’s threat model needs to be incorporated.
* Addition of new or updated third party components and services.
* Changes to authentication and/or authorization of users and/or services.

In order to create awareness of notable security events, complementing security training, threat model curators are asked to refer the developers on their team to the Secure Development Checklist and make sure that using it's part of the Definition of Done of all stories.

<!-- markdownlint-disable-file MD013 MD033 -->
