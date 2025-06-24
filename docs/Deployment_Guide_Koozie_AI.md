# Deployment & Installation Guide: Koozie AI (Bubble.io)

## 1. Introduction

### 1.1 Purpose
This document provides instructions and guidelines for deploying, managing, and maintaining the Koozie AI application, which is built on the Bubble.io platform. It covers version control, deployment to live, custom domain setup, and management of environment-specific configurations like API keys.

### 1.2 Scope
This guide is intended for developers, administrators, or technical personnel responsible for the operational management of the Koozie AI Bubble.io application.

### 1.3 Prerequisites
*   Admin access to the Koozie AI application in the Bubble.io editor.
*   Access to the domain registrar if a custom domain is used.
*   Access to accounts for third-party services (Stripe, Google Gemini, Tavily API, email service provider) to manage API keys.

## 2. Bubble.io Environment Overview

Bubble.io typically provides two main versions for an application:

*   **Development (Test) Version:**
    *   URL: `https://YourAppName.bubbleapps.io/version-test/` (or a custom test domain).
    *   This is the primary environment for development, testing, and iteration.
    *   It has its own separate database. Changes made here do not affect the live application until deployed.
    *   Workflows can be run step-by-step using the debugger.

*   **Live Version:**
    *   URL: `https://YourAppName.bubbleapps.io/` or your custom domain (e.g., `https://www.koozi.ai`).
    *   This is the production environment accessible to end-users.
    *   It has its own separate database.

## 3. Version Control and Saving Changes

*   **Saving Changes:** Bubble.io automatically saves changes made in the editor in real-time to the current version you are working on (usually Development).
*   **Deployment Points (Snapshots):** While Bubble.io saves continuously, you can create named "save points" (snapshots) of your application. This is good practice before making significant changes or deployments.
    *   Access via `App Settings > Versions` (or similar, Bubble interface may vary slightly).
*   **Reverting Changes:** You can revert your application to a previous save point if needed. This creates a new copy; it doesn't alter the history of save points.

## 4. Deployment Process (Development to Live)

Deploying changes from the Development version to the Live version is a straightforward process in Bubble.io:

1.  **Thorough Testing:** Ensure all new features and bug fixes have been thoroughly tested in the Development version (see `Testing_QA_Reports_Koozie_AI.md`).
2.  **Backup (Optional but Recommended):**
    *   Create a manual backup/snapshot of your Live database before deployment, especially for critical updates. (Bubble.io `App Data` section usually has options for this).
    *   Create a named save point of the Development version.
3.  **Navigate to Deployment:** In the Bubble.io editor, typically near the top-right, you will find a "Development" dropdown or a "Deploy" button.
4.  **Select Target:** Choose to deploy the "Current development version" to "Live".
5.  **Deployment Description:** Add a brief description of the changes being deployed. This is helpful for your deployment history.
    *(This description should align with entries in `CHANGELOG.md`)*.
6.  **Confirm Deployment:** Bubble.io will show what is being deployed (e.g., app changes, database changes if any were made directly to Live - which is generally discouraged for schema).
7.  **Execute Deployment:** Confirm the deployment. Bubble.io will then copy the current state of your Development application to the Live version. This process usually takes a few moments.
    *   **Database:** When deploying from Development to Live, you have options regarding the database:
        *   **Copy database from Development to Live:** This overwrites the Live database with the Development database. **Use with extreme caution**, as it will erase live user data. Typically only done for initial setup or major overhauls where data loss is acceptable or planned.
        *   **Deploy application changes only:** This is the most common scenario. It deploys the application logic, UI, and workflow changes but leaves the Live database untouched. Schema changes (new fields, data types) made in Development *will* be reflected in Live.
8.  **Post-Deployment Smoke Testing:** Immediately after deployment, perform smoke tests on the Live application to ensure critical functionalities are working as expected (e.g., login, chat, key features).

## 5. Custom Domain Setup

To use a custom domain (e.g., `www.koozi.ai`) for the Live version:

1.  **Bubble.io Settings:**
    *   Navigate to `Settings > Domain / Email` in the Bubble.io editor.
    *   Enter your custom domain name in the "Domain name" field.
    *   Bubble.io will provide you with DNS records (usually A records or CNAME records) that you need to configure with your domain registrar.
2.  **Domain Registrar Configuration:**
    *   Log in to your domain registrar's control panel (e.g., GoDaddy, Namecheap, Google Domains).
    *   Navigate to the DNS management section for your domain.
    *   Add or modify the DNS records as specified by Bubble.io. This typically involves pointing your domain (e.g., `www` or `@` for the root domain) to Bubble.io's servers.
    *   **TTL (Time To Live):** Set a low TTL initially if possible, so changes propagate faster.
3.  **Propagation:** DNS changes can take some time to propagate globally (from a few minutes to 48 hours, though usually much faster).
4.  **Verification:** Once Bubble.io detects the correct DNS settings, it will typically automatically provision an SSL certificate for your custom domain. The status in `Settings > Domain / Email` will update.

## 6. API Key and Environment Management

Koozie AI integrates with several external services (Google Gemini, Tavily API, Stripe, Email Service). These services require API keys, which often have separate sets for testing/development and live/production environments.

### 6.1 Configuration in Bubble.io
*   **API Connector:**
    *   When configuring calls in the API Connector, Bubble.io allows you to specify different values for keys/parameters for "Development" and "Live" versions.
    *   Example: For a Stripe API key, you'd have `Stripe_Secret_Key_Dev` and `Stripe_Secret_Key_Live`. The API Connector setup would use a shared key name like `[stripe_secret_key]`, and you'd define its value for Dev and Live in the designated fields.
    *   Ensure that "Private" is checked for sensitive keys so they are not exposed to the client-side.
*   **Plugins:**
    *   Many plugins (e.g., official Stripe plugin, email plugins) have separate fields in their settings page within the Bubble editor for development and live API keys.
    *   Example: A SendGrid plugin would have fields for "SendGrid API Key (Dev)" and "SendGrid API Key (Live)".
*   **Settings Tab:**
    *   `Settings > API` in Bubble.io can also be used to manage global API keys provided by Bubble itself or for certain general settings.

### 6.2 Best Practices for API Keys
*   **Use Test Keys in Development:** Always use test/sandbox API keys from external services (Stripe, Gemini, Tavily, etc.) in the Development version of your Bubble.io app. This prevents accidental real transactions or usage against production quotas.
*   **Use Live Keys in Production:** Only use live/production API keys in the Live version of your Bubble.io app.
*   **Secure Storage:** Store API keys only within the designated secure fields in Bubble.io (API Connector, plugin settings). Do not hardcode them into workflows or client-side scripts.
*   **Restricted Permissions:** If the API provider allows, create API keys with the minimum necessary permissions required for the application's functionality.
*   **Regular Audits (Client Responsibility):** The client should periodically review and rotate API keys for external services as per their security policies.

## 7. Application "Installation" (User Perspective)

Since Koozie AI is a web application:
*   **No client-side installation is required by end-users.** They access the application via a web browser at the live URL.
*   For internal team members or developers who need to work on the application, they will need:
    *   An invitation to the Koozie AI application as a collaborator in the Bubble.io editor (requires a Bubble.io account).
    *   Appropriate permissions assigned within Bubble.io (e.g., admin, read-only).

## 8. Maintenance and Updates

*   **Bubble.io Platform Updates:** Bubble.io manages updates to its own platform. These are usually applied automatically. Major platform changes or new features are announced by Bubble.io.
*   **Plugin Updates:** Periodically check the "Plugins" tab in the Bubble.io editor for updates to installed plugins. Test updates in the Development version before deploying to Live, as plugin updates can sometimes introduce breaking changes.
*   **Application Updates:** Follow the deployment process outlined in Section 4 for rolling out new features, bug fixes, or improvements to Koozie AI.
*   **Database Management:**
    *   Regularly monitor data growth.
    *   Bubble.io provides tools for viewing and cleaning up database entries in the `App Data` section.
    *   Consider database capacity limits if on a specific Bubble.io plan and plan for upgrades if necessary.

## 9. Rollback Plan

In case a deployment to Live introduces critical issues:

1.  **Immediate Action:** If the issue is severe, consider temporarily restricting access to the application or displaying a maintenance page (can be done by changing the `index` page content or using a temporary redirect, though Bubble doesn't have a built-in "maintenance mode" button easily).
2.  **Revert to Previous Version:**
    *   Bubble.io allows you to revert the Live application to a previous deployment. Navigate to `Settings > Versions` (or deployment history).
    *   Select the last known good version and redeploy it to Live.
    *   **Database Consideration:** Reverting the application code does NOT automatically revert the Live database to its state at that time unless you explicitly restore a database backup. If the bad deployment corrupted data, a database restore might be necessary (see Section 10).
3.  **Diagnose in Development:** Copy the problematic Live version back to Development (if needed, or use the Development version that was deployed) to diagnose and fix the issue.
4.  **Communicate:** Inform stakeholders and users (if appropriate) about the issue and the rollback.

## 10. Backup and Restore (Bubble.io)

*   **Automatic Backups:** Bubble.io performs regular automated backups of your application (both app structure and database).
*   **Manual Snapshots (Save Points):** As mentioned, create named save points before major changes.
*   **Database Backups:**
    *   You can export data from your database tables (Things) as CSV files from the `App Data` section. This is useful for offsite backups or data migration.
    *   Bubble.io offers database restore capabilities to a previous point in time, often as part of their paid plans or support. Check Bubble's current features for specifics.
*   **Restoring:**
    *   **Application Structure:** Revert to a previous save point/deployment.
    *   **Database:** If data corruption occurred, you'd need to use Bubble.io's database restore features or re-import data from a CSV backup (which can be complex for relational data).

This guide provides a comprehensive overview of deploying and managing the Koozie AI application on Bubble.io. Always refer to the official Bubble.io documentation for the most current and detailed information on platform features.
```
