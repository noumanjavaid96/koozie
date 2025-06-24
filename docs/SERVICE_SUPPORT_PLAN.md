# Koozie AI - Service Support Plan (Template)

## 1. Introduction

### 1.1 Purpose
This document outlines the agreed-upon service support structure for the Koozie AI application following its delivery and launch. It defines the scope of support, service level objectives (SLOs/SLAs), contact procedures, and responsibilities of both the support provider (e.g., Rensis Technologies) and the Client.

**This document is a template and must be reviewed, customized, and formally agreed upon by all parties.**

### 1.2 Scope of Support
This Service Support Plan covers:
*   The Koozie AI application hosted on the Bubble.io platform.
*   Integrations with third-party services explicitly listed as part of the Koozie AI core functionality (e.g., Stripe, Google Gemini, Tavily API, configured Email Service Provider).
*   Bug fixes and issue resolution related to the application's defined features as per the Software Requirement Specification (SRS).

This plan **does not** cover:
*   New feature development or enhancements beyond the agreed scope (these would require a separate change request or project).
*   Issues arising from misuse of the application by users.
*   Problems originating solely within third-party services that are outside the control of the support provider (e.g., a global Stripe outage). The support provider will assist in diagnosing such issues.
*   User training beyond initial handover documentation (unless specified).
*   Direct support for the Client's end-users (unless a separate agreement for Tier 1 support is in place). Typically, the Client will provide Tier 1 support to their end-users and escalate to the support provider as Tier 2/3.
*   Management of the Client's accounts with third-party service providers (e.g., Stripe account management, LLM API billing).

### 1.3 Term of Support
*   **Start Date:** [YYYY-MM-DD] (e.g., Date of final UAT sign-off or official launch)
*   **End Date/Review Period:** [YYYY-MM-DD / e.g., 3 months post-launch, then renewable annually]
    *   *(This section needs to be defined by the contract or agreement)*

## 2. Support Hours and Availability

*   **Standard Support Hours:**
    *   [e.g., Monday to Friday, 9:00 AM - 5:00 PM UK Time (excluding public holidays)]
*   **After-Hours Support:**
    *   [e.g., Only for Critical Issues, or On-call basis - specify terms and potential additional costs]
*   **Public Holidays Observed:**
    *   [List UK public holidays or relevant regional holidays]

## 3. Issue Classification and Service Level Objectives (SLOs)

Issues will be classified based on their severity and impact on the application's functionality and the Client's business operations.

| Severity Level | Description                                                                                                | Example                                                                      | Target Response Time | Target Resolution Time (Objective) |
|----------------|------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|----------------------|------------------------------------|
| **Critical (S1)** | Application is completely down or a core feature (e.g., chat, login, payment) is unusable for all users. No workaround available. Significant business impact. | Users cannot log in. AI chat not responding globally. Stripe payments failing. | [e.g., 1 Business Hour]  | [e.g., 4-8 Business Hours]         |
| **High (S2)**     | A core feature is significantly impaired or unavailable for a subset of users, or a non-critical feature is unusable for all users. Workaround may exist but is difficult. Moderate business impact. | Admin panel inaccessible. Document upload failing intermittently. User profile page errors. | [e.g., 2-4 Business Hours] | [e.g., 1-2 Business Days]          |
| **Medium (S3)**   | Minor feature impairment, or cosmetic issue with moderate impact. Workaround is available. Low business impact.                 | Spelling errors on a page. UI element misaligned. A non-critical admin report is slow. | [e.g., 1 Business Day]   | [e.g., 3-5 Business Days]          |
| **Low (S4)**      | Cosmetic issue with minimal impact, or general inquiry. No significant impact on functionality.                               | Request for information about a feature. Minor UI tweak suggestion.          | [e.g., 1-2 Business Days]  | [e.g., Next maintenance cycle / As scheduled] |

*   **Response Time:** Time taken to acknowledge the reported issue and begin investigation.
*   **Resolution Time:** Time taken to implement a fix or provide a satisfactory workaround. These are objectives and may vary based on issue complexity. For S1/S2 issues, continuous effort will be applied until resolved or downgraded.

## 4. Support Request and Escalation Procedure

### 4.1 How to Request Support
*   **Primary Contact Method:** [e.g., Dedicated Support Email: support@renesistech.com]
*   **Secondary Contact Method (for Critical issues if primary is unresponsive):** [e.g., Support Phone Number: +44 XXXXX XXXXXX (During support hours)]
*   **Information to Provide:**
    *   Your Name and Contact Information.
    *   Date and Time of Issue.
    *   Detailed Description of the Issue:
        *   What were you trying to do?
        *   What happened? (Include error messages verbatim)
        *   What was the expected behavior?
    *   Steps to Reproduce the Issue.
    *   Severity Level (your assessment).
    *   Number of users affected / Business impact.
    *   Screenshots or screen recordings (if helpful).
    *   User email(s) affected (if specific to certain users).

### 4.2 Support Workflow
1.  **Issue Reported:** Client reports issue via the defined channels.
2.  **Acknowledgement & Logging:** Support provider acknowledges receipt within the "Target Response Time" for the assessed severity. Issue is logged in a tracking system [Specify system, e.g., Jira, Trello, Internal Ticketing System].
3.  **Investigation & Diagnosis:** Support provider investigates the issue.
4.  **Communication:** Regular updates provided to the Client on the status of S1/S2 issues.
5.  **Resolution/Workaround:** Support provider works to resolve the issue or provide a workaround.
6.  **Verification:** Client verifies the fix or workaround.
7.  **Closure:** Issue is closed in the tracking system.

### 4.3 Escalation Matrix
If an issue is not being addressed according to the SLOs, or if the Client is unsatisfied with the progress, the following escalation path can be used:

| Escalation Level | Contact Person      | Title                       | Contact Details (Email/Phone) | When to Escalate                                     |
|------------------|---------------------|-----------------------------|-------------------------------|------------------------------------------------------|
| **Level 1**      | [e.g., Support Lead Name] | [e.g., Technical Support Lead] | [e.g., support.lead@example.com] | If L1 response/resolution SLO is missed.             |
| **Level 2**      | [e.g., Project Manager Name] | [e.g., Project Manager]     | [e.g., pm@example.com]           | If L2 response/resolution SLO is missed, or critical unresolved issue. |
| **Level 3**      | [e.g., Senior Management Contact] | [e.g., Head of Technology]  | [e.g., director@example.com]     | For major unresolved critical issues.                |

*(This matrix needs to be populated with actual names and contact details from the support provider.)*

## 5. Client Responsibilities

*   Provide clear and detailed information when reporting issues.
*   Make reasonable efforts to diagnose and resolve issues at Tier 1 (if applicable) before escalating.
*   Provide the support team with necessary access or information (e.g., specific user accounts for testing, if safe and agreed upon) to investigate issues.
*   Respond promptly to requests for information or clarification from the support team.
*   Ensure their users are aware of how to report issues internally to the Client's point of contact.
*   Manage their own accounts and billing for third-party services (Stripe, LLM provider, etc.).

## 6. Support Provider Responsibilities (e.g., Rensis Technologies)

*   Acknowledge and respond to support requests within the agreed SLOs.
*   Use reasonable efforts to diagnose and resolve reported issues covered by this plan.
*   Provide regular updates on the status of open high-priority issues.
*   Maintain a log of support requests and resolutions.
*   Escalate issues internally as needed to ensure timely resolution.
*   Inform the Client of any planned maintenance for the Koozie AI application that might cause downtime (requires coordination if Bubble.io platform maintenance is involved).

## 7. Exclusions and Limitations
*   As stated in Section 1.2 (Scope of Support).
*   Support provider is not responsible for data loss or corruption caused by Client actions or negligence.
*   Resolution times are objectives and not strict guarantees, especially for complex issues requiring extensive investigation or dependencies on third parties.

## 8. Plan Review and Updates
This Service Support Plan will be reviewed [e.g., annually, or as part of contract renewal] by both the Client and the support provider. Amendments can be made by mutual written agreement.

## 9. Signatures

This Service Support Plan is agreed upon by:

**For [Client Company Name]:**

_______________________________
Name:
Title:
Date:

**For [Support Provider Company Name, e.g., Rensis Technologies]:**

_______________________________
Name:
Title:
Date:
```
