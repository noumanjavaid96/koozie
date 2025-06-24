# Software Requirement Specification: Koozie AI

## 1. Introduction

### 1.1 Purpose
The purpose of this document is to outline the software requirements for Koozie AI, an AI-powered assistant designed to help users with task management, goal setting, and information retrieval. This SRS will define the scope, functionalities, system requirements, and constraints for the Koozie AI platform.

### 1.2 Intended Audience
This document is intended for project stakeholders, including the development team, project managers, QA team, and the client.

### 1.3 Overall Description
Koozie AI is a web-based application that provides users with an interactive AI chat interface to manage tasks, set goals, and receive assistance on various topics. It includes features for user account management, subscriptions, notifications, and an admin panel for system management. The system aims to provide a professional, friendly, and intuitive user experience, with a focus on British English spelling and conventions.

## 2. Overall Description

### 2.1 Product Perspective
Koozie AI is a standalone product. It interacts with external services for payment processing (Stripe) and potentially for notifications (e.g., Twilio, TextMagic, Mailgun, SendGrid) and CRM integration (e.g., coursecreator360).

### 2.2 Key Product Features
*   AI-powered chat interface for task management, goal setting, and Q&A.
*   Manual task and goal creation.
*   User account management (sign-up, login, profile, password management, account deletion).
*   Subscription management (free and premium tiers).
*   Notification system (email, potentially SMS/WhatsApp) for reminders and updates.
*   History page for user interactions.
*   Admin panel for user management, content management (FAQs, documents, videos), and viewing platform statistics.
*   User feedback mechanism (thumbs up/down on chat responses).
*   Document upload functionality (PDFs up to 50MB) for AI processing.
*   Featured videos section on user dashboard.
*   Customizable email templates.

### 2.3 User Characteristics
*   **Standard Users:** Individuals or business professionals looking for an AI assistant to help with productivity, goal tracking, and information.
*   **Admin Users:** System administrators responsible for managing the platform, users, content, and monitoring system health.
*   Users are expected to have basic web literacy.

### 2.4 Operating Environment
*   Web-based application accessible via modern web browsers.
*   Hosted environment (details TBD, currently on `bubbleapps.io`).
*   Integration with Stripe for payment processing.
*   Potential integration with notification services (Twilio, TextMagic, Mailgun, SendGrid) and CRM (coursecreator360).

### 2.5 Design and Implementation Constraints
*   **Platform:** Currently implemented on `bubbleapps.io`. Future platform decisions TBD.
*   **Language:** British English must be used for all user-facing text and AI responses.
*   **AI Model:** The primary Large Language Model (LLM) is Google Gemini. For queries requiring real-time web information, the Tavily API is used to fetch search results that augment the context provided to Gemini. The system must be configurable to follow specific instructions regarding tone (professional, friendly), style (British English), and behavior (asking clarifying questions).
*   **Security:** Standard security practices for web applications, including secure user authentication, data protection, and secure API integrations. OTP for email verification and account linking.
*   **Performance:** Responsive UI, timely AI responses. Specific metrics TBD.
*   **Usability:** Intuitive and user-friendly interface. Natural conversation flow in chat.
*   **Branding:** Consistent use of "Koozi" (not "Koozie") in all platform text and communications.

## 3. System Features

### 3.1 AI Chat Interface
*   **3.1.1 Functional Requirements:**
    *   Users shall be able to interact with an AI assistant via a chat interface.
    *   The AI shall understand natural language queries in British English.
    *   The AI shall assist with:
        *   Creating tasks and goals.
        *   Answering questions.
        *   Providing information based on uploaded documents.
        *   Generating content (e.g., LinkedIn posts).
    *   The AI shall ask clarifying questions one at a time to understand user expectations before providing a full answer.
    *   The AI shall use tables, graphics, etc., where appropriate to make answers easy to understand.
    *   The AI shall be professional and friendly in tone.
    *   The AI shall use initial user data (e.g., business size, turnover) to tailor responses.
    *   The AI shall provide definitions for new keywords introduced in responses.
    *   Users shall be able to provide feedback (thumbs up/down) on AI responses.
    *   A "thumbs down" response should trigger a clarifying interaction from the AI (e.g., "Sorry I was off the mark, let me ask a couple of messages to be clear...").
    *   Suggested follow-up questions shall be relevant and not repetitive.
    *   The AI should not confuse suggested questions with direct questions (i.e., avoid asking questions in the first person when presenting suggested questions).
    *   The AI should correctly handle requests like "create me an avatar" without getting stuck in loops.
    *   The AI should be able to process uploaded PDF documents and answer questions based on their content.
*   **3.1.2 User Interactions:**
    *   Typing messages in a chat input field.
    *   Clicking on suggested questions/responses.
    *   Clicking thumbs up/down icons.
    *   Uploading documents via a file upload interface.
*   **3.1.3 Input/Output Specifications:**
    *   Input: Text queries, file uploads (PDF), clicks on interactive elements.
    *   Output: Text responses, suggested questions, interactive elements, display of generated content.

### 3.2 User Account Management
*   **3.2.1 Functional Requirements:**
    *   Users shall be able to sign up for a new account using email and password.
    *   Users shall be able to sign up/log in using social login (details of providers TBD).
    *   If a user signs up with email and later uses the same email for social login, the system shall allow account linking via OTP verification.
    *   Users shall be able to log in with their credentials.
    *   Users shall undergo email verification using an OTP.
    *   OTP verification screen shall include a resend functionality and a timer for OTP expiry.
    *   Users shall be able to view and edit their profile information (Name, Profile Picture).
        *   Profile picture upload limit: 5MB.
        *   Allowed profile picture formats: JPG, GIF, PNG. Alert for incorrect formats.
        *   Loader to be implemented for profile picture upload.
        *   "Save Changes" button in Edit Profile should be enabled when data is entered.
    *   Users shall be able to change their password.
        *   Requires current password. Alert for incorrect current password.
        *   Loader on "Update Password" button.
        *   Password field placeholder text as per Figma design.
    *   Users shall be able to delete their account.
        *   Confirmation pop-up ("Are you sure you want to delete this Account?") with "Cancel" or "Delete" options. Pop-up should be center-aligned and styled as per Figma.
*   **3.2.2 UI/UX:**
    *   Forms for sign-up, login, profile editing, password change.
    *   Alerts for errors and confirmations.
    *   UI issues in Edit Profile to be fixed as per Figma.

### 3.3 Task and Goal Management
*   **3.3.1 Functional Requirements:**
    *   Users shall be able to manually create tasks/goals via a UI (e.g., popup).
    *   The AI shall be able to create tasks/goals based on chat interactions.
    *   Users shall be able to view their tasks and goals (e.g., on a "Goals" page).
    *   Users shall be able to edit tasks/subgoals (e.g., via a pencil icon).
    *   The system shall prompt users to create goals (e.g., via buttons or suggested questions from AI).
    *   When the AI sets a goal, it should not immediately ask if the goal is complete but allow for further interaction or task breakdown.
*   **3.3.2 UI/UX:**
    *   Dedicated page/section for viewing goals.
    *   UI for manual goal/task creation.
    *   Editable fields for task/goal details.

### 3.4 Subscription Management
*   **3.4.1 Functional Requirements:**
    *   The system shall support different subscription tiers (e.g., Free, Premium).
    *   Users shall be able to subscribe to a premium tier.
    *   Payment processing shall be handled via Stripe integration.
    *   Users shall be able to manage their subscription (view current plan, upgrade, cancel - TBD).
    *   Subscription status should be reflected in user profiles/dashboards.
*   **3.4.2 UI/UX:**
    *   Subscription page UI for users to select plans and manage subscriptions.
    *   Clear display of plan features and pricing.

### 3.5 Notification System
*   **3.5.1 Functional Requirements:**
    *   Users shall receive notifications for various events (e.g., task reminders, updates).
    *   Users shall be able to opt for notification preferences (e.g., weekly, workdays, just before due date).
    *   Notifications can be delivered via email.
    *   Potential for WhatsApp/SMS notifications (pending decisions on Twilio/TextMagic).
    *   Email templates shall be customizable and use approved designs.
    *   Transactional emails (e.g., OTP, verification) shall be sent using a designated email service (e.g., SendGrid, Mailgun via Mailtrap for testing).
*   **3.5.2 UI/UX:**
    *   UI for users to set notification preferences.

### 3.6 History Page
*   **3.6.1 Functional Requirements:**
    *   Users shall be able to view a history of their interactions with the AI (e.g., past chats).
    *   The history page shall be revamped for better usability.
    *   Sidebar shall have separate tabs for "Recent chat" & "Conversations".
    *   A "No Chat found" placeholder text shall be shown if search yields no results.
    *   Pop-up for deleting a chat should be center-aligned.
*   **3.6.2 UI/UX:**
    *   Clear layout for displaying past conversations.
    *   Search/filter functionality for history (TBD).

### 3.7 Admin Panel
*   **3.7.1 Functional Requirements:**
    *   Admins shall be able to log in to a separate admin interface.
    *   **Dashboard/Stats:**
        *   Display basic platform statistics:
            *   Total users (with dropdown for 7-day period and 1-month period).
            *   User feedback counts (likes, dislikes).
        *   Sub-headings and logo should be appropriately sized.
    *   **User Management:**
        *   View a list of users.
        *   User types (Free, Premium, Admin) in the subscription list should reflect in the user list.
    *   **Content Management:**
        *   Manage FAQs: Add, edit, delete FAQs.
            *   "Save Changes" button should be disabled until an answer is entered.
            *   Loader on "Save Changes" button.
            *   User shouldn't be able to enter a DOB greater than the current date (if DOB is part of FAQ admin, this needs clarification).
            *   Question mark should be added at the end of questions.
            *   Missing questions in FAQ list to be added.
        *   Manage Documents for AI:
            *   Upload documents (PDFs) to be used by the AI. Label: "Add document to platform/Koozi AI".
        *   Manage Featured Videos:
            *   Add, edit, delete YouTube video links to be displayed on the user dashboard.
            *   Video links should not be restricted by needing to be shortened; the system should handle full YouTube links.
            *   Option to "feature video on user dashboard" for videos.
    *   **Feedback Management:**
        *   View user feedback (thumbs up/down with associated comments).
*   **3.7.2 UI/UX:**
    *   Clear and organized admin interface.
    *   Admin link: `https://koozie-ai.bubbleapps.io/admin` (or similar).

### 3.8 Document Upload
*   **3.8.1 Functional Requirements:**
    *   Users shall be able to upload PDF documents in the chat.
    *   The AI shall be able to process these documents and provide information based on their content.
    *   File type restriction: Only PDF files allowed.
    *   File size limit: 50MB. Proper alert for exceeding limit: "Apologies, but the file is too large. Users cannot upload PDFs exceeding 50 MB."
*   **3.8.2 UI/UX:**
    *   Clear interface for file selection and upload.
    *   Progress indicators for uploads (TBD).

### 3.9 Featured Content (Downloads & Videos)
*   **3.9.1 Functional Requirements:**
    *   **Downloads:**
        *   A section for "Downloads" shall be available to users (content TBD, from client-provided folder).
    *   **Featured Videos:**
        *   A section for "Featured Videos" shall display videos managed from the admin panel.
        *   Client has 50+ videos on YouTube; a basic intro to "Grow" can be featured, others in a home section.
*   **3.9.2 UI/UX:**
    *   Accessible sections on the user dashboard or relevant pages.

## 4. External Interface Requirements

### 4.1 User Interfaces
*   Clean, modern, and intuitive user interface.
*   Consistent branding and styling (Koozi, not Koozie).
*   Responsive design for various screen sizes (desktop primary, mobile/tablet TBD).
*   Adherence to Figma designs where provided.
*   Scrolling issues to be fixed.

### 4.2 Hardware Interfaces
*   None specified.

### 4.3 Software Interfaces
*   **Stripe API:** For processing payments and managing subscriptions.
*   **Email Service API (SendGrid/Mailgun/etc.):** For sending transactional emails and notifications. Credentials via Mailtrap for testing.
*   **SMS/WhatsApp Service API (Twilio/TextMagic - TBD):** For sending SMS/WhatsApp notifications if implemented.
*   **LLM API:** Interface with the chosen Large Language Model for AI chat functionality.
*   **CRM Integration (coursecreator360 - TBD):** Potential integration for customer data synchronization. Client uses `coursecreator360` which uses Mailgun as SMTP.

### 4.4 Communications Interfaces
*   HTTPS for all web communications.

## 5. Non-functional Requirements

### 5.1 Performance Requirements
*   Chat responses should be timely. Specific target: TBD.
*   Page load times should be acceptable. Specific target: TBD.
*   System should handle concurrent users effectively (target number TBD).
*   Loaders should be implemented for long-running operations (e.g., file uploads, password updates).

### 5.2 Security Requirements
*   Secure user authentication (password hashing, OTP).
*   Protection against common web vulnerabilities (XSS, CSRF, SQLi - if applicable).
*   Data encryption for sensitive user data (at rest and in transit).
*   Secure handling of API keys and credentials.
*   Regular security audits (TBD).

### 5.3 Scalability Requirements
*   The system should be designed to accommodate a growing number of users and data. (Specifics TBD)

### 5.4 Usability Requirements
*   Intuitive navigation and user flow.
*   Clear error messages and user feedback.
*   Consistent UI/UX across the platform.
*   AI responses should be natural, easy to understand, and avoid jargon where possible, or explain it.
*   Accessibility considerations (TBD).

### 5.5 Reliability Requirements
*   The system should be available and operational with minimal downtime (target uptime TBD).
*   Data integrity must be maintained.
*   Robust error handling.

### 5.6 Maintainability Requirements
*   Codebase should be well-documented and organized.
*   Modular design to facilitate updates and bug fixes.
*   Configuration management for different environments (dev, test, prod).

### 5.7 Localization Requirements
*   Primary language: British English.
*   Correct spelling ("Koozi") to be used consistently.

## 6. Other Requirements

### 6.1 Legal, Regulatory, and Compliance Requirements
*   Data privacy regulations (e.g., GDPR if applicable to UK users) must be adhered to concerning user data.
*   Compliance with Stripe's terms of service for payment processing.
*   Compliance with terms of service for any third-party notification or AI services used.

### 6.2 Data Management Requirements
*   User data (profile, tasks, goals, chat history) must be stored securely.
*   Mechanism for users to request their data or delete their account (and associated data).
*   Backup and recovery strategy (TBD).
*   User feedback data (likes, dislikes, comments) should be stored for analysis and potential AI training/improvement.

### 6.3 Client Onboarding/Initial Setup
*   The system should utilize initial data collected during user sign-up (e.g., business size, turnover) to tailor AI prompts and responses.
*   A default AI prompt should be configurable in the backend to set the AI's persona and behavior (e.g., "You are the world’s best LLM... Always use British / English...").

## 7. Assumptions and Dependencies
*   Client will provide access to necessary accounts (Stripe, email services, potentially CRM).
*   Client will provide content for "Downloads" section.
*   Client will provide access to YouTube channel/videos for "Featured Videos".
*   The `bubbleapps.io` platform capabilities and limitations will influence implementation.
*   Availability and reliability of third-party services (Stripe, LLM API, notification services).

## 8. Future Considerations / To Be Determined (TBD)
*   Specific performance metrics.
*   Detailed scalability targets.
*   Accessibility standards to be met.
*   Specifics of CRM integration with `coursecreator360`.
*   Decision on SMS/WhatsApp notification provider (Twilio vs. TextMagic).
*   Detailed backup and recovery plan.
*   Advanced AI training/feedback loop using user feedback.
*   Mobile application development.
*   Full scope of "Downloads" content.
*   Specific social login providers.
*   Search/filter functionality for chat history.
*   Target uptime.
*   Security audit schedule.
```
