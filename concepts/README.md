<h1>
  <span class="headline">FastAPI Authorization</span>
  <span class="subhead">Concepts</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to explain how authorization helps control access to API routes and resources, focusing on route-level and object-level permissions.

## What is Authorization?

In this lesson, we will focus on **authorization**, which is different from **authentication**.

| Concept            | Definition                                                                                      |
| ------------------ | ----------------------------------------------------------------------------------------------- |
| **Authentication** | Confirms _who_ the user is. For example, logging in with a username and password.               |
| **Authorization**  | Determines _what_ the user is allowed to do. For example, restricting access to certain routes. |

There are two main types of authorization we will cover:

1. **Route-level permissions** – Restrict access to specific API routes.
   - Example: Only logged-in users can create a new tea entry using the `POST /teas` route.
2. **Object-level permissions** – Restrict access to specific resources based on ownership.
   - Example: Only the user who created a tea entry can update or delete it.

In this lesson, we will first focus on **route-level permissions** and then move on to object-level permissions in later lessons.

## Using FastAPI's `Depends` for authorization

Before we can implement route-level permissions, we need to understand how FastAPI’s `Depends` function works.

### What is `Depends`?

FastAPI uses a design pattern called **dependency injection**, which allows us to **inject functions** (like authentication checks) into our routes.

The `Depends` function helps us **run checks before the route is executed**. This means we can:

- Make sure users are logged in before they can access certain routes.
- Validate authentication tokens before running the route’s actions.
- Automatically block unauthorized requests and send an error message.

### How Does `Depends` Work?

1. We create a function that checks if the user is logged in or has a valid token (authentication check).
2. We use the `Depends` function in our route to **require** that this check is done before executing the route.
3. If the check fails (for example, the user is not logged in), FastAPI will **block the request** and send a `403 Forbidden` error.
