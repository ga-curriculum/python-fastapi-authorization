# FastAPI Authorized Routing - Concepts

**Learning objective:** By the end of this lesson, you will be able to **understand** how developing a `get_current_user` module ensure the security of your API's endpoints.

# Securing FastAPI Routes

In this lesson, we will focus on building the `get_current_user` function as a dependency to our application.

This dependency module will provides a security check for your FastAPI application. It ensures that every request to protected routes comes from authenticated users by verifying their tokens.

The processes encapsulated in `get_current_user`is responsible all processes for managing the request token:

1. Decodes the provided token to extract the payload
1. validates the token to ensure it hasn't expired and that it comes from a trusted source
1. retrieves the corresponding user data from the database

If the dependency function, after running, confirms the user is authorized to make the request.
