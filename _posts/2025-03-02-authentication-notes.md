---
title: Authentication Notes
date: 2025-03-02
categories: []
tags: [authorization, authentication, .net]     # TAG names should always be lowercase
---

## Middleware vs. Filters: Tradeoffs

### Middleware:
- **Scope**: Applies to all HTTP requests in the pipeline.
- **Use case**: Best for global concerns, such as authentication and logging.
- **Tradeoffs**:
  - **Pros**: Executes early in the pipeline, ensuring authentication is checked before any other logic (like routing or controllers).
  - **Cons**: Less flexible for targeting specific routes or actions; runs for every request, which might be inefficient if you only need authentication for specific endpoints.

### Filters:
- **Scope**: Applied to specific actions or controllers.
- **Use case**: Best for finer control, such as applying authentication/authorization only to specific endpoints.
- **Tradeoffs**:
  - **Pros**: More flexible, allowing you to apply authentication only to certain routes.
  - **Cons**: Executes after middleware, meaning unauthorized requests might still reach controllers before they're rejected.

### Will Middleware or Filters Be Applied to All Requests?
- **Middleware**: Yes, middleware applies to **all requests** unless configured to target specific routes.
- **Filters**: No, filters are only applied to **specific actions** or **controllers** that you configure.

## Stateless vs. Stateful Authentication

### **Stateless Authentication**:

- **Definition**: The server does **not store any session information** about the user. All required information (like the user's identity) is included in the **token** (e.g., JWT) sent with each request.
- **Example**: A JWT token is sent in the `Authorization` header with each request, and the server validates the token without storing session data between requests.
- **Pros**:
  - Scalable (no need for server-side session storage).
  - Makes the server "stateless," meaning it doesn't need to remember previous requests.
  - Ideal for APIs, especially with distributed or mobile clients.
- **Cons**:
  - Tokens can get large and must be validated with every request (increasing computational overhead).
  - If the token is compromised, it remains valid until it expires or is revoked, as there's no centralized state to check.

### **Stateful Authentication**:

- **Definition**: The server **stores session information** about the user. A session ID (typically in a cookie) is sent with each request, and the server looks up session details to authenticate the user.
- **Example**: With cookie-based authentication, the server creates a session and stores session data in memory or a database.
- **Pros**:
  - Easier to revoke or modify the session since the server controls the session state.
  - The server can directly invalidate sessions or manage their expiration.
- **Cons**:
  - Less scalable since session data is stored server-side, creating storage overhead.
  - Maintaining session state between requests can be problematic in distributed environments unless using sticky sessions or a centralized session store.
