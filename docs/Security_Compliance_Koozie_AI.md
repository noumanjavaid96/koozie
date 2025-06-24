# Security & Compliance Documentation: Koozie AI

## 1. Introduction

### 1.1 Purpose
This document outlines the security measures implemented within the Koozie AI application (built on Bubble.io) and discusses relevant compliance considerations, particularly concerning data protection.

### 1.2 Scope
This document covers security aspects related to the Bubble.io platform, data handling, user authentication, API integrations, and general compliance notes. It is not an exhaustive security audit but provides an overview of the security posture and practices.

## 2. Platform Security (Bubble.io)

Koozie AI leverages the Bubble.io platform, which provides a foundational layer of security:

*   **Infrastructure:** Bubble.io manages the underlying cloud infrastructure (typically AWS), including server maintenance, patching, and baseline security configurations.
*   **SSL/TLS Encryption:** All data in transit between the user's browser and the Bubble.io servers is encrypted using HTTPS by default. Bubble.io manages SSL certificate provisioning and renewal.
*   **DDoS Protection:** Bubble.io provides protection against common DDoS attacks.
*   **Regular Backups:** Bubble.io performs regular backups of the application database. Specific backup frequency and retention policies are managed by Bubble.io (details can be found in their terms of service or documentation). Users can also create manual snapshots.

## 3. Data Protection and Privacy

### 3.1 Data Storage
*   All application data (user profiles, chat messages, goals, etc., as defined in `Database_Schema_Koozie_AI.md`) is stored in Bubble.io's built-in database, hosted on AWS servers.
*   Uploaded files are stored in Bubble.io's S3-compatible storage.

### 3.2 Data Privacy Rules (Bubble.io)
Bubble.io's privacy rules are the primary mechanism for controlling data access at a granular level. These rules are configured in the `Data > Privacy` tab for each Data Type ("Thing").
*   **Principle of Least Privilege:** Privacy rules are configured to ensure users can only access and modify data they own or are explicitly permitted to access.
    *   **Example for `Goal` Data Type:**
        *   A user can only find, view, and modify `Goal` records where `This Goal's User is Current User`.
        *   Admin users (`Current User's Role is "Admin"`) may have broader read access for administrative purposes.
        *   Default permissions (`Everyone else`) are set to deny access to fields and search capabilities unless explicitly allowed.
*   **Sensitive Fields:** Fields containing sensitive information (e.g., hashed passwords - managed by Bubble; Stripe Customer IDs) are protected by restrictive privacy rules. Direct display of extremely sensitive raw data on the UI is avoided.

### 3.3 User Data Management
*   **User Account Deletion:** Users have the option to delete their accounts (as per SRS). The workflow associated with account deletion should:
    *   Delete the User "Thing".
    *   Delete associated data "Things" (e.g., Goals, Tasks, ChatSessions, ChatMessages, UploadedDocuments owned by the user) to comply with "right to be forgotten" principles. This requires careful workflow design to find and delete all related records.
    *   Instruct Stripe (via API) to delete the corresponding Customer object if applicable (and if desired by the client).
*   **Data Export:** While not explicitly requested in chat logs, for GDPR compliance, a mechanism for users to request an export of their data should be considered for future implementation if not already present. Bubble.io allows CSV export of data by admins, which could be a manual fulfillment step.

### 3.4 Content of Chat Messages and Uploaded Documents
*   Chat messages and the content of uploaded documents may contain personal or sensitive information entered by the user.
*   This data is stored in the Bubble.io database and is subject to the same privacy rules.
*   The LLM API provider's data usage and privacy policies are also relevant here. Koozie AI should use LLM providers that offer data privacy assurances (e.g., not using submitted data for training their public models without explicit consent or via specific enterprise agreements). This needs to be confirmed with the chosen LLM provider.

## 4. Authentication and Authorization

### 4.1 User Authentication
*   **Password Management:** User passwords are managed by Bubble.io's built-in authentication system. Passwords are automatically hashed and salted. Plaintext passwords are never stored.
*   **Email Verification:** New user sign-ups require email verification via a One-Time Password (OTP) sent to their email address to ensure the validity of the email and prevent unauthorized account creation.
    *   OTPs are stored temporarily (`EmailVerificationOTP` Thing) with an expiry timestamp and marked as used.
*   **Social Logins:** If social logins (e.g., Google) are implemented, authentication is delegated to the respective OAuth providers. Secure handling of tokens and user information from these providers is managed by the relevant Bubble.io plugins or API configurations.
*   **Session Management:** Bubble.io manages user sessions securely using cookies.

### 4.2 Authorization
*   **Role-Based Access Control (RBAC):** A `Role` field on the `User` "Thing" (e.g., "Standard", "Admin", "Premium") is used to control access to features and data.
*   **Workflow Conditions:** Workflows that perform sensitive actions or navigate to restricted pages include conditions to check `Current User's Role`.
*   **Page Access:** Page load workflows can redirect users if they do not have the appropriate role for a specific page (e.g., redirect non-admins from the `/admin` page).
*   **Element Visibility/Editability:** Conditional logic in the Design tab controls the visibility and editability of UI elements based on user role or data ownership.
*   **Privacy Rules:** As mentioned in 3.2, privacy rules enforce data access restrictions at the database level, providing a critical layer of authorization.

## 5. API Key Management & External Service Security

### 5.1 API Key Storage
*   API keys for external services (LLM, Stripe, Email services like SendGrid/Mailgun, SMS/WhatsApp services like Twilio/TextMagic) are stored securely within the Bubble.io platform:
    *   In the **API Connector** settings for manually configured APIs.
    *   In the **Plugin settings** for plugins that manage their own API connections.
*   Bubble.io allows for different API keys for the development (test) and live versions of the application, which is crucial for isolating testing from production data and transactions. These are configured in `Settings > API` and within plugin settings that support different keys per environment.
*   **Keys are configured as "Private" in the API connector where possible, ensuring they are only used server-side and not exposed to the client's browser.**

### 5.2 Secure Communication
*   All calls to external APIs are made over HTTPS to ensure data is encrypted in transit.

### 5.3 Webhook Security (Stripe)
*   The Stripe webhook handler endpoint in Bubble.io (a backend workflow exposed as an API) must verify the signature of incoming webhook events from Stripe. This ensures that the requests are genuinely from Stripe and not malicious attempts. Bubble.io's API connector or specific Stripe plugins often provide a way to implement or assist with this verification.

## 6. Compliance Considerations

### 6.1 GDPR (General Data Protection Regulation)
Given the client and potential users are based in the UK, GDPR principles are highly relevant.
*   **Lawful Basis for Processing:** User consent is obtained during signup for processing their data as per the app's functionality. A clear privacy policy is essential.
*   **Data Minimization:** Collect only necessary user data.
*   **User Rights:**
    *   **Right to Access:** Users should be able to request their data. (Manual fulfillment via admin CSV export initially).
    *   **Right to Rectification:** Users can edit their profile information.
    *   **Right to Erasure ("Right to be Forgotten"):** Account deletion should remove user data (see section 3.3).
    *   **Right to Data Portability:** Similar to right to access, data should be exportable in a common format.
*   **Data Security:** Implement appropriate technical and organizational measures (as outlined in this document).
*   **Privacy Policy:** A clear, accessible privacy policy explaining data collection, usage, storage, and user rights must be provided to users. This document should be linked from the `HANDOVER_INDEX.md` and the application itself. *(The creation of the Privacy Policy content is a legal/client responsibility but its technical implementation for display is part of the app).*

### 6.2 PCI DSS (Payment Card Industry Data Security Standard)
*   Koozie AI does not directly store or process credit card information. Payment processing is delegated to Stripe, which is PCI DSS compliant.
*   By using Stripe Checkout or Stripe Elements, the handling of sensitive card details is offloaded to Stripe, significantly reducing Koozie AI's PCI DSS scope. Ensure the integration method used (e.g., Stripe.js + Elements or Stripe Checkout) is implemented correctly according to Stripe's guidelines.

### 6.3 Other Compliance
*   **Terms of Service:** A Terms of Service document should be in place for users. *(Legal/client responsibility for content).*
*   **Third-Party Service Compliance:** Ensure that all third-party services used (LLM, email, SMS) are themselves compliant with relevant data protection and security standards. Review their terms and privacy policies.

## 7. Security Best Practices within Bubble.io Development

*   **Avoid Exposing Sensitive Data in URLs or Client-Side Elements:** Use backend workflows and privacy rules to protect data.
*   **Validate User Inputs:** Although Bubble.io helps prevent common web vulnerabilities like SQL injection due to its architecture, always validate user inputs in workflows to ensure data integrity and prevent unexpected behavior.
*   **Regularly Review Privacy Rules:** As the application evolves, periodically review and test privacy rules to ensure they remain effective.
*   **Limit Admin Access:** Provide admin access only to trusted individuals.
*   **Use Strong, Unique Passwords for Bubble.io Editor Access.**
*   **Monitor Application Logs (Bubble.io Logs):** Bubble.io provides server logs that can be used to monitor for errors and suspicious activity.
*   **Keep Plugins Updated:** Use reputable plugins and keep them updated. Be cautious with plugins requiring extensive permissions or access to sensitive data.

## 8. Incident Response (Placeholder)

A formal incident response plan should be developed by the client or operational team. This plan would outline steps to take in the event of a security breach, data loss, or other critical incidents. Key elements would include:
*   Identification of an incident.
*   Containment procedures.
*   Eradication of the threat.
*   Recovery of systems and data.
*   Post-incident analysis and lessons learned.
*   Communication plan (internal and external, including regulatory notifications if required).

## 9. Disclaimer

This document provides an overview of security and compliance measures. It is not a guarantee of perfect security. The security landscape is constantly evolving, and ongoing vigilance, regular reviews, and updates are necessary to maintain a strong security posture. For specific legal or compliance advice, consultation with legal and security professionals is recommended.
```
