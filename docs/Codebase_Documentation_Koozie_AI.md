# Codebase Documentation: Koozie AI (Bubble.io)

## 1. Introduction

### 1.1 Purpose
This document provides an overview of the Koozie AI application structure as built on the Bubble.io platform. Since Bubble.io is a visual development platform, "codebase" refers to the organization of pages, reusable elements, workflows, data types ("Things"), custom states, and plugins. This documentation aims to help developers understand how the application is constructed and maintained.

### 1.2 Scope
This document covers the primary structural and logical components of the Koozie AI Bubble.io application. It is not an exhaustive list of every single element or workflow but focuses on the key architectural patterns and important areas.

## 2. Overall Application Structure (Bubble.io Editor)

The Koozie AI application in the Bubble.io editor is organized into several key tabs and sections:

*   **Design Tab:** Contains all visual elements, pages, and reusable elements.
    *   **Pages:** Individual screens or views accessible by users via URLs.
    *   **Reusable Elements:** Components (like headers, footers, custom popups) designed once and used across multiple pages.
*   **Workflow Tab:** Defines the logic and actions that occur in response to events (e.g., button clicks, page loads, custom events).
*   **Data Tab:**
    *   **Data Types:** Defines the structure of the database ("Things" like User, Goal, ChatMessage).
    *   **App Data:** Allows viewing and managing live data in the database.
    *   **Privacy:** Defines rules for data access and modification based on user roles or conditions.
*   **Styles Tab:** Manages global styling for elements (fonts, colors, borders, etc.) to ensure consistency.
*   **Plugins Tab:** Lists and configures all installed plugins that extend Bubble.io's functionality.
*   **Settings Tab:** Application-wide settings, including API keys, domain management, deployment versions, etc.

## 3. Key Pages

The application consists of several key pages, each serving a distinct purpose. Common pages include:

*   **`index` (or Landing Page):** The initial page users might see if not logged in. (Details of current landing page TBD or if app redirects directly to login/signup).
*   **`signup`:** User registration page.
    *   **Key Workflows:** Handle user input, create a new User "Thing", perform email verification (trigger OTP email).
*   **`login`:** User login page.
    *   **Key Workflows:** Authenticate user credentials, navigate to the main dashboard/chat page.
*   **`dashboard` (or `chat`, `app`):** The main application page after login.
    *   **Key Elements:** Chat interface, navigation to goals, history, settings.
    *   **Key Workflows:** Initialize chat, send/receive messages, display dynamic content.
*   **`goals`:** Page for viewing, creating, and managing user goals and tasks.
    *   **Key Elements:** Repeating groups to display goals and tasks, input forms for new goals/tasks.
    *   **Key Workflows:** CRUD (Create, Read, Update, Delete) operations for Goals and Tasks.
*   **`history`:** Displays past chat sessions.
    *   **Key Elements:** Repeating group for chat sessions, search/filter functionality.
    *   **Key Workflows:** Fetch and display chat history.
*   **`profile` / `account_settings`:** Allows users to manage their profile (name, picture), password, and notification preferences.
    *   **Key Workflows:** Update User "Thing", handle password change logic, save notification preferences.
*   **`subscription_management`:** Page for users to view their current subscription, upgrade, or cancel.
    *   **Key Workflows:** Interact with Stripe (via API Connector or plugin) to manage subscription statuses.
*   **`admin`:** Admin panel for managing users, content, and viewing stats.
    *   **Key Elements:** Sections for user lists, FAQ management, video uploads, document uploads, stats dashboards.
    *   **Key Workflows:** CRUD operations for admin-managed data types, data visualization.
*   **`email_verification`:** Page for users to enter their OTP to verify their email.
    *   **Key Workflows:** Validate OTP, update User's `IsEmailVerified` status.

## 4. Reusable Elements

Reusable elements are crucial for maintaining consistency and reducing redundancy. Key reusable elements likely include:

*   **`Header_LoggedIn` / `Header_LoggedOut`:** Navigation bar displayed across multiple pages.
    *   Contains links, user profile dropdown (if logged in), logo.
*   **`Footer`:** Standard footer content.
*   **`Popup_GenericAlert`:** A standardized popup for showing success or error messages.
    *   **Custom States:** `message_text`, `message_type` (success/error).
*   **`Popup_ConfirmDelete`:** A reusable confirmation dialog for delete actions.
*   **`ChatBubble_User` / `ChatBubble_AI`:** Reusable elements for displaying individual chat messages with appropriate styling.
*   **`Task_Item_Display`:** Reusable element for displaying a single task within a list.

## 5. Key Workflows & Logic

Workflows are the heart of Bubble.io's logic.

### 5.1 User Authentication
*   **Sign Up:**
    1.  User fills form on `signup` page.
    2.  Workflow: Validate inputs.
    3.  Workflow: "Sign the user up" action (Bubble native).
    4.  Workflow: Create an `EmailVerificationOTP` record.
    5.  Workflow: Trigger an email (via Email Service Plugin/API) with the OTP and a link to the `email_verification` page.
*   **Login:**
    1.  User fills form on `login` page.
    2.  Workflow: "Log the user in" action (Bubble native).
    3.  Workflow: Navigate to the main dashboard page.
*   **Social Login:**
    1.  User clicks social login button.
    2.  Workflow: Uses a plugin (e.g., Bubble's Google plugin, or generic OAuth2) to handle the social login flow.
    3.  Workflow: On successful social login, check if a User "Thing" exists with this social ID.
        *   If yes, log them in.
        *   If no, create a new User "Thing", mark email as verified (if provided by social provider), and log them in.
        *   Handle account linking if an email already exists (as per SRS).
*   **Logout:**
    1.  User clicks logout button.
    2.  Workflow: "Log the user out" action (Bubble native).
    3.  Workflow: Navigate to login page or landing page.

### 5.2 Chat Logic
*   **Sending a Message:**
    1.  User types in input field on `dashboard` page and hits send.
    2.  Workflow: Create `ChatMessage` "Thing" for user's message, linked to current `ChatSession` (or create new session).
    3.  Workflow: Trigger a custom event or backend workflow (`send_chat_message` as per API docs).
    4.  Backend Workflow:
        a.  Assemble prompt (system prompt, history, new message, PDF context if any).
        b.  Call LLM API via API Connector.
        c.  On response, create `ChatMessage` "Thing" for AI's response.
        d.  Update `ChatSession` with `LastActivityDate`.
        e.  Return AI response to the frontend to be displayed.
*   **Handling Feedback:**
    1.  User clicks thumbs up/down on an AI message.
    2.  Workflow: Update the corresponding `ChatMessage` "Thing" with the feedback and any comment.

### 5.3 Stripe Integration (Subscription Management)
*   **Subscribing to a Plan:**
    1.  User selects a plan on `subscription_management` page.
    2.  Workflow: Call Stripe API (via API Connector or Stripe plugin) to create a Stripe Checkout session or directly create a subscription for the user (associating with their `StripeCustomerID`).
    3.  Workflow: Redirect user to Stripe Checkout if used, or handle payment details directly (requires PCI compliance considerations, hence Checkout is preferred).
    4.  Backend Workflow (Webhook Handler - `stripe_webhook_handler`):
        a.  Receives events from Stripe (e.g., `invoice.payment_succeeded`, `customer.subscription.created`).
        b.  Verify webhook signature.
        c.  Update Koozie's `Subscription` "Thing" and `User`'s role based on the event.
*   **Managing Subscriptions (Cancel, Update):**
    *   Similar workflows calling appropriate Stripe API endpoints and updating local database via webhooks or direct API response.

### 5.4 Admin Panel Logic
*   Workflows primarily involve CRUD operations on data types like `FAQ`, `FeaturedVideo`, `UploadedDocument` (for AI knowledge base).
*   Access to admin pages and workflows is restricted based on `User`'s `Role` field using workflow conditions and privacy rules.

## 6. Custom States

Custom states are heavily used in Bubble.io for managing temporary UI states, holding data for display, and controlling visibility of elements without needing to write to the database.

*   **Examples:**
    *   `Page`: `loading_status` (yes/no) to show/hide a loading spinner.
    *   `Group`: `current_tab` (text) to control which content tab is visible within a group.
    *   `RepeatingGroup`: `selected_item_id` (text) to store the ID of a selected item for further actions.
    *   `Popup`: `is_visible` (yes/no) - often handled by Bubble, but custom states might be used for more complex popup logic.
    *   Input elements might have custom states to hold temporary values or validation status.

## 7. Key Plugins

The functionality of Koozie AI is extended by several Bubble.io plugins. Common and likely essential plugins include:

*   **API Connector:** Generic tool to connect to any REST API (essential for LLM, Stripe if not using a dedicated Stripe plugin, custom email services).
*   **Stripe Plugin (Official or Third-Party):** Simplifies Stripe integration if a comprehensive one is available and preferred over manual API Connector setup. (e.g., Stripe.js + Elements by Bubble).
*   **Email Service Plugin (e.g., SendGrid, Postmark):** For sending emails reliably.
*   **File Upload Plugins:** For handling file uploads, potentially with more advanced features than native uploader (e.g., progress bars, image compression). (Bubble's native uploader is often sufficient).
*   **Date/Time Picker Plugins:** For more user-friendly date selection.
*   **Rich Text Editor:** For allowing formatted text input (e.g., in admin panel for FAQ answers).
*   **Icon Plugins (e.g., Font Awesome):** For a wider variety of icons.
*   **PDF Generation/Viewing Plugins (Optional):** If PDFs need to be generated or displayed with more control within the app.
*   **Social Login Plugins (e.g., Sign in with Google, Facebook):** To enable social authentication.

*(A complete list of installed plugins and their purpose should be reviewed directly in the Bubble.io editor's "Plugins" tab for the most accurate, up-to-date information.)*

## 8. Backend Workflows (API Workflows)

Backend workflows are crucial for:
*   **Scheduled tasks:** (e.g., sending daily reminder emails - though this wasn't explicitly in chat logs, it's a common use).
*   **Processing data triggered by external webhooks:** (e.g., Stripe webhooks).
*   **Running complex logic that doesn't need to immediately update the UI or can run asynchronously.**
*   **Exposing API endpoints if Koozie AI needs to be called by other systems.**

Key backend workflows identified:
*   `send_chat_message` (conceptual, for handling LLM calls).
*   `stripe_webhook_handler` (essential for Stripe).
*   Workflows for sending OTP emails, notification emails.

## 9. Database Structure and Privacy Rules

*   **Data Types ("Things"):** As detailed in the `Database_Schema_Koozie_AI.md`.
*   **Privacy Rules:** These are critical for security. Located in `Data > Privacy`. Rules define who can find specific "Things" in searches, view fields, modify fields, upload files, etc.
    *   **Example Rule for `Goal`:**
        *   `When: Current User's Role is "Admin" -> Allow find in searches, view all fields.`
        *   `When: This Goal's User is Current User -> Allow find in searches, view all fields, allow auto-binding on modifiable fields.`
        *   `Everyone Else (Default): Deny all.`
    *   Careful consideration must be given to each data type to ensure users can only access their own data, and admins have appropriate privileges.

## 10. Option Sets

Option sets are used for predefined sets of choices that are static or change infrequently. They are more performant than using a "Thing" for simple dropdowns.

*   **Possible Option Sets:**
    *   `User Roles`: (Admin, Standard, Premium)
    *   `Subscription Status`: (Active, Canceled, Past Due, Trialing)
    *   `Goal/Task Status`: (Pending, In Progress, Completed, Archived)
    *   `Feedback Types`: (Thumb Up, Thumb Down)
    *   `Notification Frequencies`: (Daily, Weekly, Due Date, Workdays, Off)

## 11. Environment Variables / API Keys

*   API keys for external services (LLM, Stripe, Email, etc.) are managed in the Bubble.io editor, typically in the **Plugins** section (for plugin-specific keys) or in the **API Connector** (for manually configured APIs).
*   Bubble allows for different keys for Development (Test) and Live versions. These are configured in `Settings > API`.
*   **It is crucial that these keys are kept confidential and not exposed on the client-side.** Bubble.io backend workflows and the API Connector are designed to use these keys securely on the server-side.

## 12. Navigating and Understanding the App

*   **Start with Pages:** Understand the purpose of each page and the main UI elements on it.
*   **Inspect Elements:** Use the Bubble editor to select elements and see their properties, conditional logic, and associated workflows.
*   **Trace Workflows:** Start from a user action (e.g., button click) and follow the workflow steps. Use the Bubble debugger to step through workflows in test mode.
*   **Examine Reusable Elements:** Understand their functionality as they are used in multiple places.
*   **Review Data Types and Privacy Rules:** Understand how data is stored and secured.
*   **Check Plugins:** See which plugins are installed and how they are configured.

This document provides a high-level guide. The Bubble.io editor itself is the ultimate source of truth for the application's structure and logic. Regular exploration and use of the debugger are key to deeply understanding the application.
```
