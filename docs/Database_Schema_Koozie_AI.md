# Database Schema: Koozie AI

## 1. Introduction

### 1.1 Purpose
This document outlines the database schema for Koozie AI. As Koozie AI is built on Bubble.io, this schema describes the "Things" (data types) and their fields, which are analogous to tables and columns in a traditional relational database. It also describes the relationships between these data types.

### 1.2 Scope
This schema covers the primary data entities required to support the functionalities of Koozie AI as defined in the SRS and Technical Architecture documents.

## 2. Data Types (Tables) and Fields

Bubble.io uses "Things" as data types. Each "Thing" has several built-in fields (e.g., `Creator`, `Created Date`, `Modified Date`, `_id`). Custom fields are defined below.

### 2.1 User
Stores information about registered users.
*   **Built-in Bubble Fields:**
    *   `Email`: (Text) User's email address (also used for login).
    *   `Password`: (Password) Handled by Bubble.io.
    *   `_id`: (Text) Unique identifier for the user.
    *   `Created Date`: (Date)
    *   `Modified Date`: (Date)
*   **Custom Fields:**
    *   `Name`: (Text) User's full name or display name.
    *   `ProfilePictureURL`: (Text or Image) URL to the user's profile picture.
    *   `Role`: (Text) User role (e.g., "Standard", "Admin", "Premium"). Defaults to "Standard".
    *   `StripeCustomerID`: (Text) Stripe Customer ID for subscription management.
    *   `Subscription`: (Subscription) Link to the user's active subscription record. (One-to-one)
    *   `NotificationPreferences`: (NotificationPreference) Link to the user's notification settings. (One-to-one)
    *   `BusinessSize`: (Text) Information gathered at signup, e.g., "1-10 employees".
    *   `Turnover`: (Text) Information gathered at signup, e.g., "£0-£50k".
    *   `LastLoginDate`: (Date)
    *   `IsEmailVerified`: (Yes/No) Boolean, true if email is verified.
    *   `SocialLoginProvider`: (Text) e.g., "Google", "Facebook" if social login was used.
    *   `SocialLoginID`: (Text) User ID from the social login provider.

### 2.2 Subscription
Stores details about user subscriptions.
*   **Custom Fields:**
    *   `User`: (User) Link to the user this subscription belongs to. (One-to-one, though a User links to this)
    *   `PlanName`: (Text) Name of the subscription plan (e.g., "Free", "Premium Monthly", "Premium Yearly").
    *   `StripeSubscriptionID`: (Text) Stripe Subscription ID.
    *   `Status`: (Text) Subscription status (e.g., "active", "canceled", "past_due", "trialing").
    *   `StartDate`: (Date) Date the subscription started.
    *   `EndDate`: (Date) Date the subscription ends or ended.
    *   `CurrentPeriodEndDate`: (Date) For recurring subscriptions, when the current billing period ends.
    *   `Price`: (Number) Price of the subscription.
    *   `Currency`: (Text) Currency code (e.g., "GBP", "USD").

### 2.3 ChatSession
Represents a single chat conversation.
*   **Custom Fields:**
    *   `User`: (User) The user who initiated/owns this chat. (Many-to-one with User)
    *   `Title`: (Text) Optional title for the chat session (e.g., auto-generated from the first few messages).
    *   `StartDate`: (Date) When the chat session started.
    *   `LastActivityDate`: (Date) Timestamp of the last message in this session.
    *   `Archived`: (Yes/No) Boolean, true if the user archived this chat.

### 2.4 ChatMessage
Represents a single message within a ChatSession.
*   **Custom Fields:**
    *   `ChatSession`: (ChatSession) The chat session this message belongs to. (Many-to-one with ChatSession)
    *   `Sender`: (Text) Indicates who sent the message ("User" or "AI").
    *   `UserSender`: (User) Link to the User if `Sender` is "User".
    *   `Content`: (Text) The text content of the message.
    *   `Timestamp`: (Date) When the message was sent/received.
    *   `Feedback`: (Text) User feedback on an AI message (e.g., "thumb_up", "thumb_down").
    *   `FeedbackComment`: (Text) Optional user comment accompanying the feedback.
    *   `AssociatedDocument`: (UploadedDocument) Link if this message relates to an uploaded document. (Optional)
    *   `IsSuggestedQuestion`: (Yes/No) Boolean, true if this message represents a suggested question from the AI.

### 2.5 Goal
Represents a user-defined goal.
*   **Custom Fields:**
    *   `User`: (User) The user who owns this goal. (Many-to-one with User)
    *   `Title`: (Text) Title or description of the goal.
    *   `Description`: (Text) More detailed description of the goal (optional).
    *   `Status`: (Text) e.g., "Pending", "In Progress", "Completed", "Archived".
    *   `DueDate`: (Date) Optional due date for the goal.
    *   `CreationSource`: (Text) How the goal was created (e.g., "Manual", "AI_Chat").

### 2.6 Task
Represents a sub-task associated with a Goal.
*   **Custom Fields:**
    *   `Goal`: (Goal) The parent goal this task belongs to. (Many-to-one with Goal)
    *   `User`: (User) The user who owns this task (inherited from Goal, but can be explicit for assignment).
    *   `Title`: (Text) Title or description of the task.
    *   `Status`: (Text) e.g., "Pending", "In Progress", "Completed".
    *   `DueDate`: (Date) Optional due date for the task.
    *   `ReminderDate`: (Date) Optional reminder date for the task.

### 2.7 UploadedDocument
Stores information about documents uploaded by users or admins.
*   **CustomFields:**
    *   `Uploader`: (User) The user who uploaded the document.
    *   `FileName`: (Text) Original name of the file.
    *   `BubbleFileLink`: (File) Link to the file stored in Bubble.io's S3 storage.
    *   `FileSizeMB`: (Number) Size of the file in megabytes.
    *   `FileType`: (Text) e.g., "PDF".
    *   `UploadDate`: (Date)
    *   `ProcessedTextContent`: (Text) Extracted text content from the document (for AI processing). (Potentially very large, consider Bubble limitations or alternative storage if needed).
    *   `Purpose`: (Text) e.g., "ChatInteraction", "AdminKnowledgeBase".

### 2.8 FeaturedVideo
Information about videos featured in the application.
*   **Custom Fields:**
    *   `Title`: (Text) Title of the video.
    *   `YouTubeURL`: (Text) Full URL to the YouTube video.
    *   `VideoID`: (Text) Extracted YouTube Video ID.
    *   `Description`: (Text) Short description of the video.
    *   `IsFeaturedOnDashboard`: (Yes/No) Boolean, if true, video appears prominently on user dashboard.
    *   `AddedBy`: (User) Admin user who added the video.
    *   `DateAdded`: (Date)

### 2.9 FAQ
Frequently Asked Questions and their answers, managed by Admins.
*   **Custom Fields:**
    *   `Question`: (Text) The question.
    *   `Answer`: (Text) The answer.
    *   `Category`: (Text) Optional category for grouping FAQs.
    *   `LastUpdatedBy`: (User) Admin user who last updated this FAQ.
    *   `LastUpdatedDate`: (Date)

### 2.10 NotificationPreference
User's preferences for receiving notifications.
*   **Custom Fields:**
    *   `User`: (User) The user these preferences belong to. (One-to-one with User)
    *   `GoalReminders`: (Text) e.g., "Daily", "Weekly", "Due Date", "Off".
    *   `TaskReminders`: (Text) e.g., "Daily", "Weekly", "Due Date", "Off".
    *   `EmailNotificationsEnabled`: (Yes/No) Master switch for email notifications.
    *   `SMSNotificationsEnabled`: (Yes/No) Master switch for SMS notifications (if feature is active).
    *   `WhatsAppNotificationsEnabled`: (Yes/No) Master switch for WhatsApp notifications (if feature is active).

### 2.11 EmailVerificationOTP
Stores OTPs for email verification.
*   **Custom Fields:**
    *   `UserEmail`: (Text) The email address this OTP is for.
    *   `OTPCode`: (Text) The generated One-Time Password.
    *   `ExpiryTimestamp`: (Date) When the OTP expires.
    *   `IsUsed`: (Yes/No) Boolean, true if the OTP has been successfully used.

### 2.12 DownloadableResource (Placeholder for "Downloads" section)
Content provided by the client for users to download.
*   **Custom Fields:**
    *   `Title`: (Text) Title of the resource.
    *   `Description`: (Text)
    *   `FileLink`: (File or Text URL) Link to the downloadable file.
    *   `Category`: (Text)
    *   `AddedBy`: (User) Admin who added this.
    *   `DateAdded`: (Date)

## 3. Relationships Summary

*   **User to Subscription:** One-to-One (a User has one active Subscription record).
*   **User to NotificationPreference:** One-to-One.
*   **User to ChatSession:** One-to-Many (a User can have many ChatSessions).
*   **ChatSession to ChatMessage:** One-to-Many (a ChatSession can have many ChatMessages).
*   **User to Goal:** One-to-Many (a User can have many Goals).
*   **Goal to Task:** One-to-Many (a Goal can have many Tasks).
*   **User to UploadedDocument:** One-to-Many (a User can upload many Documents).
*   **ChatMessage to UploadedDocument:** Many-to-One (a ChatMessage can optionally reference one UploadedDocument).
*   **Admin (User) to FeaturedVideo/FAQ/DownloadableResource:** One-to-Many (an Admin can add many of these content types).

## 4. Data Integrity and Constraints
*   Bubble.io manages basic data type constraints.
*   Required fields will be enforced through Bubble.io workflows and UI validation.
*   Uniqueness (e.g., User email) is typically handled by Bubble.io's built-in user management.
*   Referential integrity (e.g., a `Task` must belong to an existing `Goal`) is managed by how data is linked and workflows are designed in Bubble.io. Deletion rules (e.g., what happens to tasks if a goal is deleted) need to be defined in workflows.

## 5. Indexing
Bubble.io automatically handles much of the indexing. When designing complex queries or searches, consider how Bubble.io searches on fields to optimize performance. For example, searching frequently on specific text fields might benefit from careful workflow design.

This schema provides a foundation. As development progresses and new features are added or existing ones refined, this schema may evolve.
```
