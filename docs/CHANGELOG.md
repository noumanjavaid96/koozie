# Koozie AI - Changelog

This document records all notable changes to the Koozie AI application.
The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to Semantic Versioning (though Bubble.io's versioning is primarily date/deployment based, we can assign semantic versions to major deployments).

## [Unreleased] - YYYY-MM-DD

### Added
-   Feature X was implemented.
-   New admin panel section for Y.

### Changed
-   Updated UI for the user dashboard.
-   Modified the prompt for Gemini LLM for Z behavior.

### Fixed
-   Resolved bug where users could not reset passwords.
-   Corrected spelling errors on the subscriptions page.

### Removed
-   Old feature A was deprecated and removed.

---

## [1.0.0] - YYYY-MM-DD - Initial Production Release

### Added
-   Initial set of features including:
    -   User authentication (Signup, Login, Email OTP verification).
    -   AI Chat Interface with Google Gemini and Tavily API integration.
    -   Goal and Task Management (manual and AI-assisted).
    -   Subscription Management with Stripe (Free & Premium tiers - details TBD).
    -   User Profile Management.
    -   Admin Panel (User list, Content Management for FAQs, Videos, Documents).
    -   History Page for chat sessions.
    -   Basic Notification System (Email).
    -   Document Upload (PDFs up to 50MB) for AI processing.
    -   Featured Videos and Downloads sections.

### Changed
-   (If any changes from initial internal versions to first production release)

### Fixed
-   (Any bugs fixed before first production release)

---

**How to Use This Changelog:**

*   **[Unreleased]:** This section is for changes made since the last release. Update this as you develop. When you're ready to release, change `[Unreleased]` to the new version number (e.g., `[1.0.1]`) and add the release date. Then, create a new `[Unreleased]` section at the top.
*   **Version Numbers:** While Bubble.io uses deployment dates/times, for significant releases, assign a semantic version number (MAJOR.MINOR.PATCH).
    *   MAJOR version when you make incompatible API changes (if APIs are exposed) or fundamental application changes.
    *   MINOR version when you add functionality in a backward-compatible manner.
    *   PATCH version when you make backward-compatible bug fixes.
*   **Sections:** Use `Added`, `Changed`, `Fixed`, `Removed`, `Deprecated`, `Security` as appropriate.
*   **Clarity:** Write entries that are clear and understandable to both technical and non-technical stakeholders where possible. Link to issue trackers if applicable.
```
