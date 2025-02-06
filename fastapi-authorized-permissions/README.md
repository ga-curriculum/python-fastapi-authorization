# FastAPI Authorized Permissions - Concepts

**Learning objective:** By the end of this lesson, you will be able to **understand** the concept of permissioning (roles and ownership) in API development.

## Overview

Leveraging the power of FastAPI's `Depends` function is great, we've defined a custom function allows to us restrict many of our CRUD operations to simply logged in users.

However, to fully secure our application, we need more granularity than this.

## Securing resources

**Object Permissions** refer to the security measures that control access to specific resources. Once an authorization token is verified, our application will determine if a user's request has 'permission' to perform certain actions such creating, editing, or deleting teas from our database.

## **Object Permissions Examples**

- **Content Management Systems (CMS)**: Authors can edit their articles but cannot modify articles written by others.
- **Project Management Tools**: Team members can update tasks they are assigned to but cannot change tasks assigned to others.
- **E-Commerce Platforms**: Customers can view their orders but not the orders of other customers.

## Permissioning

If you want to limit the updating/deleting of a tea to only the user who posted that tea, you now need a **relationship** between teas and users.

This process may seem complicated and will involve restructuring our application. However, it ensures our application's security by allowing only the user who 'owns' a tea can modify that tea's data.

## Next Steps

1. **Update Models** - associate each `tea` with a specific `user` entity.

2. **Update Serializers** - add a `user` field to the `tea` serializer.

3. **Seeding Related Data** - refactor seed module to populate `users` and associated `teas` in the database.

4. **Controller Update - Create Associations** - Refactor `tea` create to store the current_user `user_id` for new `tea` records

5. **Controller Updates - Data Permission and Error Handling** - refactor routes to check if the `current_user` against the `user` associated with a single tea, and throw an error otherwise.
