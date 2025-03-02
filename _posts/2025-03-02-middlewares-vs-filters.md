---
title: Middlewares vs Filters
date: 2025-03-02
categories: []
tags: [filter, middleware, .net]     # TAG names should always be lowercase
---

# Rewrite:
## Middleware vs. Filters: Tradeoffs

Both middleware and filters can handle cross-cutting concerns like authentication and authorization. However, they have different scopes and purposes.

### Middleware:
**Scope:** Applies to all HTTP requests in the pipeline. <br />
**Use case:** Ideal for global concerns that affect the entire app, such as authentication or logging. <br />
**Tradeoffs:** <br />
- **Pros:** It is executed early in the pipeline, ensuring that authentication is checked before any other logic (like routing or controllers) is executed. <br />
- **Cons:** Because it's global, it can be less flexible if you need to apply different logic to different parts of your app. Also, it runs for every request, which could be inefficient if you're only concerned with a subset of requests (e.g., for a specific API route). <br />


### Filters:
**Scope:** Applied to specific actions or controllers. <br />
**Use case:** Filters are ideal for more granular control, such as applying authentication/authorization to certain endpoints or actions. <br />
**Tradeoffs:** <br />
- **Pros:** More flexible, as you can apply them to specific controllers or actions only. It allows for fine-grained control over which parts of the app require authentication or authorization. <br />
- **Cons:** Since filters run after middleware, they won’t stop requests as early as middleware would, so unauthorized requests might still reach your controllers. <br />
<br><br>
# Resource Filter vs Action Filters: Comparison

## 1. **Action Filters**

### **Scope**
- Action filters are executed after routing but before the action is executed.
- They are typically used for **authentication**, **authorization**, **logging**, or modifying the action’s parameters or result.

### **Use Case**
- **Authentication and Authorization**: Ensuring a user is authenticated before accessing a specific action.
- **Logging**: You can log request details before the action or log response details after the action.
- **Action Modifications**: Modifying action parameters before they reach the action method or modifying the result after the action method executes.

### **When it Runs**
- **Before** the action method is executed (e.g., logging request data or checking authorization).
- **After** the action method is executed (e.g., modifying the response).

### **Pros and Cons**
#### **Pros**:
- Can be applied specifically to controllers or actions.
- Good for handling concerns specific to a controller or action, like authentication, logging, or modifying action parameters.
- Runs after routing, but before the action executes, so it's ideal for pre-execution tasks.

#### **Cons**:
- Not ideal for global concerns (e.g., caching or cross-cutting concerns that affect multiple actions) – use middleware for that.
- Can be more granular than necessary for global tasks.

### **Example**:
```csharp
[Authorize] // This is an action-level filter
public class MyController : Controller
{
    public IActionResult SecureAction()
    {
        return View();
    }
}
```

## 2. **Resource Filters**


### **Scope**
- Resource filters are executed before action filters, even before the action method executes.
- Used for early-stage concerns like caching, request transformation, or modifying the response before the action executes.

### **Use Case**
- Caching: If you want to apply caching to multiple actions or controllers, you can use a resource filter to cache responses.
- Global Resource Management: You can manipulate the request or response at a very early stage in the pipeline.
- Request/Response Transformation: Transforming or modifying the request data before it reaches the action, or modifying the response after the action is executed.

### **When it Runs**
- Before the action method is executed.
- This means you can modify request headers, inspect or change the HTTP context, or implement logic that needs to run before the action itself.


### **Pros and Cons**
#### **Pros**:
- Runs before action filters and action methods, making it ideal for modifying global requests or setting up resources like caching.
- Great for handling global resource concerns, such as data transformation or caching, across multiple actions or controllers.

#### **Cons**:
- Not ideal for tasks that are specific to a controller or action, as it’s applied earlier in the pipeline.
- Less flexibility than action filters for tasks that need to interact with the action method’s parameters or result.

### **Example**:
```csharp
public class MyResourceFilter : IResourceFilter
{
    public void OnResourceExecuting(ResourceExecutingContext context)
    {
        // Code to run before the action is executed (e.g., modify request)
    }

    public void OnResourceExecuted(ResourceExecutedContext context)
    {
        // Code to run after the action executes (e.g., modify response)
    }
}
```


# Data Available for Each Filter
## Action Filter:
- Before Model Binding: You can access the HttpContext, query parameters, or route data before the model binding happens.
- After Model Binding: You can access the HttpContext, and the model (bound data) after the model binding, before the action executes.
- HTTP Context: Available throughout the filter lifecycle, providing access to things like headers, cookies, and user info.
## Resource Filter:
- Before Action Execution: Access the HTTP context, request headers, and query parameters before the action executes. You can modify the request if needed.
- After Action Execution: You can modify the response, headers, or handle any final transformation before returning the result to the client.
- No Model Binding Available: Resource filters do not interact with model binding, so they do not have direct access to the model state or bound data.
