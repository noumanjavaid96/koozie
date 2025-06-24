# API Documentation: Koozie AI

## 1. Introduction

### 1.1 Purpose
This document provides details on the Application Programming Interfaces (APIs) used by and potentially exposed by Koozie AI. Since Koozie AI is primarily built on Bubble.io, which acts as both frontend and backend, this document will focus on:
1.  **Internal APIs:** Bubble.io backend workflows that function as internal API endpoints called by the frontend.
2.  **External APIs Consumed by Koozie AI:** Details of how Koozie AI interacts with third-party APIs (e.g., LLM, Stripe, Email services).
3.  **APIs Exposed by Koozie AI (Future):** If Koozie AI were to expose its own endpoints for third-party consumption (currently not a primary requirement).

### 1.2 Scope
This document will detail the conceptual structure of these APIs, request/response formats, and authentication methods. Specific Bubble.io workflow URLs or exact external API endpoint details will be referenced generally, as the primary interaction is managed within the Bubble.io environment.

## 2. Authentication
*   **Internal Bubble.io APIs:** Authenticated based on the logged-in user's session managed by Bubble.io. Admin-specific endpoints are further restricted by user roles defined in Bubble.io.
*   **External APIs Consumed:** Each external service has its own authentication mechanism, typically API keys or OAuth tokens. These keys are securely stored within Bubble.io (e.g., in API Connector configurations, server-side scripts, or environment variables).
    *   **LLM API:** API Key sent in request headers.
    *   **Stripe API:** API Key sent in request headers.
    *   **Email Service API (e.g., SendGrid):** API Key sent in request headers.
    *   **SMS/WhatsApp API (e.g., Twilio):** Account SID and Auth Token (or API Key) sent in request headers.

## 3. Internal APIs (Bubble.io Backend Workflows)

These are server-side workflows in Bubble.io triggered by frontend actions or other workflows. While not traditional REST APIs with explicit URLs for external calls (unless explicitly exposed), they function similarly for internal operations.

### 3.1 AI Chat Interaction
*   **Endpoint (Conceptual):** `POST /workflow/send_chat_message`
*   **Description:** Handles sending a user's message to the LLM and receiving a response.
*   **Authentication:** User session.
*   **Request Body (JSON example):**
    ```json
    {
      "userId": "current_user_id",
      "chatId": "optional_existing_chat_id", // for ongoing conversations
      "message": "User's typed message",
      "uploadedDocumentId": "optional_document_id_if_any" // ID of a document uploaded in this turn
    }
    ```
*   **Processing:**
    1.  Retrieve chat history for context (if `chatId` provided).
    2.  If `uploadedDocumentId` is present, retrieve document text.
    3.  Construct prompt for LLM (including system prompt, history, user message, document text).
    4.  Call External LLM API.
    5.  Save user message and AI response to Koozie DB.
    6.  Return AI response.
*   **Response Body (JSON example):**
    ```json
    {
      "status": "success",
      "aiResponse": "AI's generated response",
      "newChatId": "id_of_the_chat_session", // if a new chat was created
      "suggestedQuestions": ["Suggestion 1", "Suggestion 2"] // Optional
    }
    ```
*   **Error Response (JSON example):**
    ```json
    {
      "status": "error",
      "message": "Error processing chat message"
    }
    ```

### 3.2 User Feedback on Chat Message
*   **Endpoint (Conceptual):** `POST /workflow/submit_chat_feedback`
*   **Description:** Records user feedback (thumbs up/down) for an AI message.
*   **Authentication:** User session.
*   **Request Body (JSON example):**
    ```json
    {
      "messageId": "ai_message_id_in_db",
      "feedback": "thumb_up" | "thumb_down",
      "comment": "Optional user comment"
    }
    ```
*   **Response Body (JSON example):**
    ```json
    {
      "status": "success",
      "message": "Feedback recorded"
    }
    ```

### 3.3 User Account Management (Examples)
Bubble.io handles most of these (signup, login, password reset) through its built-in actions, but custom workflows might exist for profile updates.

*   **Endpoint (Conceptual):** `POST /workflow/update_user_profile`
*   **Authentication:** User session.
*   **Request Body (JSON example):**
    ```json
    {
      "name": "New User Name",
      "profilePictureUrl": "url_to_new_picture" // Or handle file upload directly
    }
    ```
*   **Response Body (JSON example):**
    ```json
    {
      "status": "success",
      "message": "Profile updated"
    }
    ```

### 3.4 Goal/Task Management (Examples)
*   **Endpoint (Conceptual):** `POST /workflow/create_goal`
*   **Authentication:** User session.
*   **Request Body (JSON example):**
    ```json
    {
      "description": "New goal description",
      "tasks": [
        {"description": "Task 1 for goal"},
        {"description": "Task 2 for goal"}
      ]
    }
    ```
*   **Response Body (JSON example):**
    ```json
    {
      "status": "success",
      "goalId": "new_goal_id_in_db"
    }
    ```

### 3.5 Admin Panel Actions (Examples)
*   **Endpoint (Conceptual):** `POST /workflow/admin_add_featured_video`
*   **Authentication:** User session (Admin role required).
*   **Request Body (JSON example):**
    ```json
    {
      "videoUrl": "youtube_video_url",
      "title": "Video Title",
      "description": "Video Description"
    }
    ```
*   **Response Body (JSON example):**
    ```json
    {
      "status": "success",
      "videoId": "new_video_id_in_db"
    }
    ```

## 4. External APIs Consumed by Koozie AI

Koozie AI relies on several external APIs. The interaction is managed via Bubble.io's API Connector or custom plugins.

### 4.1 Google Gemini LLM API
*   **Purpose:** To generate AI responses for the chat, process instructions, and perform content generation tasks.
*   **Authentication:** API Key in request header (e.g., `X-Goog-Api-Key: YOUR_GEMINI_API_KEY`).
*   **Example Endpoint (Conceptual - specific endpoint depends on the Gemini model and task, e.g., generateContent):** `POST https://generativelanguage.googleapis.com/v1beta/models/gemini-pro:generateContent`
*   **Example Request Body (JSON - for `generateContent`):**
    ```json
    {
      "contents": [
        {
          "role": "user",
          "parts": [
            {"text": "System Prompt: You are Koozi AI..."}
            // Include chat history and current user message as subsequent parts or turns
          ]
        },
        {
          "role": "user", // Representing user's current message after system prompt & history
          "parts": [
            {"text": "Current user query / PDF context / Tavily search results"}
          ]
        }
      ],
      "generationConfig": {
        "temperature": 0.7,
        "maxOutputTokens": 500
      },
      "safetySettings": [ // Adjust as needed
        {"category": "HARM_CATEGORY_HARASSMENT", "threshold": "BLOCK_MEDIUM_AND_ABOVE"},
        {"category": "HARM_CATEGORY_HATE_SPEECH", "threshold": "BLOCK_MEDIUM_AND_ABOVE"}
      ]
    }
    ```
*   **Example Response Body (JSON - for `generateContent`):**
    ```json
    {
      "candidates": [
        {
          "content": {
            "parts": [
              {"text": "The AI's generated response."}
            ],
            "role": "model"
          },
          "finishReason": "STOP",
          // Other metadata like safetyRatings
        }
      ]
    }
    ```

### 4.1.1 Tavily API (for Web Search)
*   **Purpose:** To provide Google Gemini LLM with real-time web search results for up-to-date information or broader context.
*   **Authentication:** API Key in request header (e.g., `Authorization: Bearer YOUR_TAVILY_API_KEY` - or specific header like `X-Tavily-API-Key`). *Confirm exact header with Tavily docs.*
*   **Example Endpoint (Conceptual):** `POST https://api.tavily.com/search`
*   **Example Request Body (JSON):**
    ```json
    {
      "api_key": "YOUR_TAVILY_API_KEY", // Or passed in header
      "query": "User's query that requires web search",
      "search_depth": "basic", // or "advanced"
      "max_results": 5
    }
    ```
*   **Example Response Body (JSON):**
    ```json
    {
      "query": "User's query that requires web search",
      "results": [
        {
          "title": "Search Result Title 1",
          "url": "https://example.com/result1",
          "content": "Snippet of content from result 1..."
        },
        // ... more results
      ]
    }
    ```
    The `content` from these results would then be formatted and included in the prompt to the Gemini API.

### 4.2 Stripe API
*   **Purpose:** Payment processing and subscription management.
*   **Authentication:** API Key in request header (e.g., `Authorization: Bearer YOUR_STRIPE_SK_KEY`).
*   **Key Endpoints Used (Conceptual):**
    *   `POST /v1/customers`: Create a customer.
    *   `POST /v1/subscriptions`: Create a subscription.
    *   `GET /v1/subscriptions/{id}`: Retrieve subscription details.
    *   `POST /v1/payment_intents`: Create a payment intent.
    *   `POST /v1/setup_intents`: Create a setup intent (for saving payment methods).
    *   `POST /v1/checkout/sessions`: Create a Stripe Checkout session.
*   **Webhooks:** Koozie AI will need an endpoint (exposed Bubble.io workflow) to receive webhooks from Stripe for events like `invoice.payment_succeeded`, `customer.subscription.updated`, `customer.subscription.deleted`.
    *   **Endpoint (Conceptual, exposed by Bubble):** `POST /workflow/stripe_webhook_handler`
    *   **Authentication:** Stripe webhook signature verification.
    *   **Request Body:** Stripe event object (JSON).

### 4.3 Email Service API (e.g., SendGrid, Mailgun)
*   **Purpose:** Sending transactional emails (OTP, notifications).
*   **Authentication:** API Key in request header (e.g., `Authorization: Bearer YOUR_EMAIL_SERVICE_API_KEY`).
*   **Example Endpoint (Conceptual - SendGrid):** `POST https://api.sendgrid.com/v3/mail/send`
*   **Example Request Body (JSON - SendGrid):**
    ```json
    {
      "personalizations": [{"to": [{"email": "user@example.com"}]}],
      "from": {"email": "noreply@koozi.ai", "name": "Koozi AI"},
      "subject": "Your Subject Here",
      "content": [{"type": "text/html", "value": "<p>HTML email content</p>"}]
      // Or using template IDs
    }
    ```

### 4.4 SMS/WhatsApp API (e.g., Twilio - if implemented)
*   **Purpose:** Sending SMS or WhatsApp notifications.
*   **Authentication:** Account SID and Auth Token (Basic Auth) or API Key.
*   **Example Endpoint (Conceptual - Twilio SMS):** `POST https://api.twilio.com/2010-04-01/Accounts/{AccountSid}/Messages.json`
*   **Example Request Body (URL-encoded form data - Twilio):**
    `To=+1234567890&From=+0987654321&Body=Your notification message.`

## 5. APIs Exposed by Koozie AI (Future Potential)

Currently, Koozie AI does not expose public APIs for third-party consumption. If this were a requirement, Bubble.io allows exposing backend workflows as REST APIs.

*   **Authentication:** Would likely use API keys managed within Bubble.io for external clients.
*   **Example Endpoint (Conceptual):** `GET /api/1.1/obj/goal/{goal_id}` (Bubble.io Data API style) or `POST /api/1.1/wf/create_task_externally` (Bubble.io Workflow API style).
*   **Request/Response Formats:** Would follow JSON standards.

## 6. API Versioning
*   **Internal APIs:** Versioning is managed implicitly through Bubble.io's deployment versions (development, live).
*   **External APIs Consumed:** Koozie AI will use the versions of external APIs as specified by the providers. Care must be taken to manage updates or deprecations from these providers.
*   **Exposed APIs (Future):** If APIs are exposed, versioning (e.g., `/v1/endpoint`) would be crucial.

## 7. Rate Limiting
*   **Internal APIs:** Bubble.io has its own platform capacity and workload limits.
*   **External APIs Consumed:** Koozie AI must respect the rate limits of external API providers. Implement retry mechanisms with backoff for transient errors.
*   **Exposed APIs (Future):** If APIs are exposed, rate limiting per client/API key would need to be implemented within Bubble.io.

## 8. Error Handling
*   API responses (both internal and external calls) should use standard HTTP status codes.
    *   `200 OK`: Successful request.
    *   `201 Created`: Successful creation of a resource.
    *   `400 Bad Request`: Invalid input from the client.
    *   `401 Unauthorized`: Missing or invalid authentication.
    *   `403 Forbidden`: Authenticated but not authorized to access the resource.
    *   `404 Not Found`: Resource not found.
    *   `500 Internal Server Error`: Server-side error.
*   Error responses should include a meaningful message in JSON format where possible.
    ```json
    {
      "status": "error",
      "errorCode": "SPECIFIC_ERROR_CODE", // Optional
      "message": "Descriptive error message"
    }
    ```
```
