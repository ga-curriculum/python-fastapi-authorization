<h1>
  <span class="prefix">Python</span>
  <span class="headline">FastAPI Authorization</span>
</h1>

## About

This module covers the fundamentals of **authorization** in FastAPI, ensuring that users can only access and modify the data they are allowed to. Students will learn how to secure API routes using **route-level permissions** and enforce **object-level permissions** based on data ownership.By the end of this module, students will be able to implement authentication checks, manage user access, and restrict actions on protected resources.

## Content

| Lesson                                                                                               | Est. Delivery Time | Skills                                                                                                                |
| ---------------------------------------------------------------------------------------------------- | :----------------: | --------------------------------------------------------------------------------------------------------------------- |
| [Setup](./setup/README.md)                                                                           |       5 min        | Set up the development environment.                                                                                   |
| [Concepts](./concepts/README.md)                                                                     |       5 min        | Learn how authorization techniques control access to API routes using route-level and object-level permissions.       |
| [FastAPI's `Depends` Function](./fastapis-depends-function/README.md)                                |       10 min       | Understand FastAPI's `Depends` function for handling dependencies in route processing.                                |
| [Decoding JWT Tokens](./decoding-jwt-tokens/README.md)                                               |       10 min       | Implement JWT token decoding to authenticate API requests.                                                            |
| [Securing FastAPI Routes](./securing-fastapi-routes/README.md)                                       |       15 min       | Implement the `get_current_user` dependency to verify login tokens and protect routes.                                |
| [Managing Model Relationships](./managing-model-relationships/README.md)                             |       15 min       | Update FastAPI data models to define and enforce relationships between different entities.                            |
| [Modifying the Tea Serializer](./modifying-the-tea-serializer/README.md)                             |       10 min       | Modify the `teas` serializer to reflect changes in relationships with `users`.                                        |
| [Updating the Seed File](./updating-the-seed-file/README.md)                                         |       10 min       | Update the seed file to create and link related data entities in the database.                                        |
| [Associating User Data with New Records](./associating-user-data-with-new-records/README.md)         |       15 min       | Associate the current logged-in user's data with newly created records in FastAPI.                                    |
| [Verifying Permissions for `PUT` and `DELETE`](./verifying-permissions-for-put-and-delete/README.md) |       15 min       | Implement controller logic to verify data permissions for `PUT` and `DELETE` routes based on the current user's data. |
| **Total content**                                                                                    |      110 min       |                                                                                                                       |

## References

📖 [Reference Materials](./references/README.md)

## Internal

### Prerequisites

- FastAPI Authentication

### Starter code

🟢 [Starter code](https://git.generalassemb.ly/modular-curriculum-all-courses/python-fastapi-authentication-solution)

### Solution code

🏁 [Solution code](https://git.generalassemb.ly/modular-curriculum-all-courses/python-fastapi-authorization-solution)

### Course landing pages

- [APD - Advanced Python for Developers](https://ga-curriculum.github.io/python-fastapi-authorization/canvas-landing-pages/apd.html)
- [Fallback](https://ga-curriculum.github.io/python-fastapi-authorization/canvas-landing-pages/fallback.html)

### Resources

✏️ [Instructor Guide](./internal-resources/instructor-guide.md)

🎥 [Video Hub](./internal-resources/video-hub.md)

🏗️ [Release Notes](./internal-resources/release-notes.md)

---

**Find a 👾 bug 👾 or have suggestions? [Let us know](https://ga-curriculum.github.io/universal-resources-internal/module-feedback.html)!**
