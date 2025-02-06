<h1>
  <span class="headline">FastAPI dependencies</span>
  <span class="subhead">FastAPI's `Depends` Function</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to explain the purpose and advantages of FastAPI's `Depends` function in handling dependencies for routing.

## Understanding FastAPI's `Depends`

Before we secure our routes, we need to understand `Depends`. You may have seen it used when obtaining a database session for routes, but now we will take a deeper look.

FastAPI's `Depends` is a core feature of the framework. It allows you to define dependencies in your API, making your code cleaner and more reusable.

### What is `Depends`?

`Depends` is a function that declares dependencies for your route functions.

A dependency is anything your route needs to work correctly. Examples include:

| Dependency type    | Example use case                              |
| ------------------ | --------------------------------------------- |
| Database session   | Ensures routes can interact with the database |
| Authentication     | Checks if a user is logged in                 |
| Security policies  | Restricts access based on user roles          |
| Request parameters | Reuses common query parameters                |

When you use `Depends`, FastAPI automatically resolves the dependency and passes it to your route function. It also handles errors and returns helpful messages if something goes wrong.

### Why use FastAPI's `Depends`?

Using `Depends` makes your code more modular and easier to maintain. Here are its key benefits:

| Benefit            | Explanation                                                               |
| ------------------ | ------------------------------------------------------------------------- |
| **Readability**    | Clearly shows what each route depends on                                  |
| **Reusability**    | Allows you to reuse dependencies across multiple routes                   |
| **Error handling** | Automatically manages dependency errors and provides meaningful responses |
| **Testing**        | Makes it easier to replace dependencies in tests                          |

## How `Depends` works

### Declaring a dependency

You use `Depends` inside your route function’s parameters and pass it a dependency function. FastAPI then runs the dependency function and provides its result to the route.

Here’s a simple example:

```python
from fastapi import FastAPI, Depends
from typing import Optional

app = FastAPI()

def common_parameters(q: Optional[str] = None, skip: int = 0, limit: int = 100):
    return {"q": q, "skip": skip, "limit": limit}

@app.get("/items/")
def read_items(commons: dict = Depends(common_parameters)):
    return commons
```

### What happens in this example?

1. When the `/items/` route is called, FastAPI first calls `common_parameters`.
2. The result of `common_parameters` is passed as an argument to `read_items`.
3. The route function uses the dependency output to handle the request.

This approach keeps your route functions clean by moving logic to separate dependency functions.

## Using dependencies in real applications

Dependencies become more useful in larger applications. A common use case is requiring a database session in multiple routes.

Example:

```python
from fastapi import FastAPI, Depends
from sqlalchemy.orm import Session
from database import get_db  # Import a function that provides a database session

app = FastAPI()

@app.get("/users/")
def read_users(db: Session = Depends(get_db)):
    return db.query(User).all()
```

Here, FastAPI will:

1. Call `get_db()` to get a database session.
2. Pass the session to the `read_users` function.
3. Close the session after the request is completed.

This ensures all routes using `get_db()` receive a valid database session without repeating code.
