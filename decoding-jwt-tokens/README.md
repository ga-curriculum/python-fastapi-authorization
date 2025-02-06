<h1>
  <span class="headline">FastAPI Authorization</span>
  <span class="subhead">Decoding JWT tokens</span>
</h1>

**Learning objective:** By the end of this lesson, you will be able to **implement** JWT tokens decoding to authenticate user's API requests.

## Securing FastAPI routes

In this lesson, we will create the `get_current_user` function as a dependency for our application.

This function will be used to secure FastAPI routes. It checks that every request to protected routes comes from an authenticated user by verifying their JWT token.

The `get_current_user` function does the following:

1. Decodes the provided JWT token to extract the payload.
2. Validates the token to ensure it hasn’t expired and that it’s from a trusted source.
3. Retrieves the corresponding user data from the database.

If everything is valid, the function allows the request to continue. If not, it raises an error.

### Creating the directory structure

In your application folder, create a new directory to store your dependencies:

```sh
mkdir dependencies
```

### Defining the dependency

Now, let’s define the `get_current_user` function that will be used as a dependency. This function will handle the token decoding and user validation process.

Create a new file called `get_current_user.py` inside the `dependencies` folder.

```python

# dependencies/get_current_user.py

from fastapi import Depends, HTTPException, status
from fastapi.security import HTTPBearer
from sqlalchemy.orm import Session
from models.user import UserModel
from database import get_db
import jwt
from jwt import DecodeError, ExpiredSignatureError # We import specific exceptions to handle them explicitly
from config.environment import secret

# We're using the HTTP Bearer scheme for the Authorization header
http_bearer = HTTPBearer()

# This function takes the database session and the JWT token from the request header
def get_current_user(db: Session = Depends(get_db), token: str = Depends(http_bearer)):

    try:
        # Decode the token using the secret key
        payload = jwt.decode(token.credentials, secret, algorithms=["HS256"])

        # Query the database to find the user with the ID from the token's payload
        user = db.query(UserModel).filter(UserModel.id == payload.get("sub")).first()

        # If no user is found, raise an HTTP 401 Unauthorized error
        if not user:
            raise HTTPException(status_code=status.HTTP_401_UNAUTHORIZED,
                                 detail="Invalid username or password")

    # Handle decoding errors (invalid token)
    except DecodeError as e:
        raise HTTPException(status_code=status.HTTP_403_FORBIDDEN,
                             detail=f'Could not decode token: {str(e)}')

    # Handle expired token errors
    except ExpiredSignatureError:
        raise HTTPException(status_code=status.HTTP_403_FORBIDDEN,
                             detail='Token has expired')

    # Return the user if the token is valid
    return user

```

### How `get_current_user` will be used in our tea API

We can now use the `get_current_user` function in routes that require user authentication. By using FastAPI’s `Depends` function, we’ll ensure that only authenticated users can access these routes.

The following routes will now be protected:

- `POST /teas`: Only authenticated users can create new teas.
- `PUT /teas/:id`: Only authenticated users can update an existing tea.
- `DELETE /teas/:id`: Only authenticated users can remove an existing tea.

With the `get_current_user` function, FastAPI will automatically check the validity of the token before allowing access to these routes.
