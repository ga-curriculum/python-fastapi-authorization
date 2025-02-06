<h1>
  <span class="headline">FastAPI Authorization</span>
  <span class="subhead">Concepts</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to tktk

# Authorization Intro - Concepts

**Learning objective:** By the end of this lesson, you will be able to **explain** how we can use authorization techniques to implement route and object level permissions.

## Authorization Overview

Now that our app is _authenticated_, we'll be moving on to _authorization_. The difference again:

**Authentication**: Are you who you say you are?

**Authorization**: Are you allowed to do what you are trying to do?

Authorization allows us to add permissions to our API in different ways. The 2 ways of authorizing that we'll be looking at:

1. **Route level permissions**. This will restrict and control who can access which routes. e.g. Only logged in users (have a valid token) can POST a tea.
2. **Object level permissions**. This will restrict and control the operations someone can perform on certain resources. e.g. Only the user who created a tea can update that tea.

We'll be looking at point 1 first. Before we jump into that, we need to briefly talk about FastAPI's **Depends**.

This will be necessary, because the `dependency injection` pattern will allow us to annotate certain functions as routes that are only accessible to logged in users. Dependency injection in a FastAPI app is a design pattern that allows you to inject dependencies (like our database connections, authentication mechanisms, or other utility functions) directly into your routes and other components of the application.

`Depends` will allow to us to run some verification code, on whichever routes we choose, _before_ the user's request ever reaches the constoller logic that access the database. If the expected dependency is missing, or invalid, an error while be thrown, thereby preventing unauthorized operations.

To learn more about `dependency injection` software design pattern, you can checkout a more technical explaination [here](https://martinfowler.com/articles/injection.html#InversionOfControl).

## Next Steps

In this module we will focus just on setting up and using `Depends` in a test API. Once we understand how FastAPI dependency injection works, we will explore how to use `Depends` to check inbound request for a JWT, an authenticated user's token.
