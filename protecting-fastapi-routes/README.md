<h1>
  <span class="headline">FastAPI Authorization</span>
  <span class="subhead">Protecting FastAPI Routes</span>
</h1>

**Learning objective:** By the end of this lesson, you will be able to implement a `get_current_user` dependency that can verify login tokens for us.

## Protecting FastAPI routes in a controller

In this lesson, we will update the FastAPI controller to protect certain routes using authentication. Specifically, we will secure routes for creating, updating, and deleting tea items by adding a `get_current_user` dependency. This dependency will verify login tokens to ensure that only authenticated users can access those routes.

### What is the `get_current_user` dependency?

The `get_current_user` function is a dependency that checks the login status of a user by verifying their JWT (JSON Web Token). If the user is authenticated, the function allows the request to proceed. Otherwise, it raises an error.

### Adding the `get_current_user` dependency

Below is the updated controller code for the `/teas` routes:

```py
# controllers/teas.py

from fastapi import APIRouter, Depends, HTTPException
from sqlalchemy.orm import Session
from models.tea import TeaModel
from serializers.tea import TeaSchema
from models.user import UserModel  # Import the UserModel to verify the current user
from typing import List
from database import get_db
from dependencies.get_current_user import get_current_user  # Import the get_current_user function


router = APIRouter()

@router.get("/teas", response_model=List[TeaSchema])
def get_teas(db: Session = Depends(get_db)):
    teas = db.query(TeaModel).all()
    return teas

@router.get("/teas/{tea_id}", response_model=TeaSchema)
def get_single_tea(tea_id: int, db: Session = Depends(get_db)):
    tea = db.query(TeaModel).filter(TeaModel.id == tea_id).first()
    if not tea:
        raise HTTPException(status_code=404, detail="Tea not found")
    return tea

@router.post("/teas", response_model=TeaSchema)
def create_tea(tea: TeaSchema, db: Session = Depends(get_db), current_user: UserModel = Depends(get_current_user)):  # Add the current_user dependency
    new_tea = TeaModel(**tea.dict())
    db.add(new_tea)
    db.commit()
    db.refresh(new_tea)
    return new_tea

@router.put("/teas/{tea_id}", response_model=TeaSchema)
def update_tea(tea_id: int, tea: TeaSchema, db: Session = Depends(get_db), current_user: UserModel = Depends(get_current_user)):  # Add the current_user dependency
    db_tea = db.query(TeaModel).filter(TeaModel.id == tea_id).first()
    if not db_tea:
        raise HTTPException(status_code=404, detail="Tea not found")

    tea_data = tea.dict(exclude_unset=True)
    for key, value in tea_data.items():
        setattr(db_tea, key, value)

    db.commit()
    db.refresh(db_tea)
    return db_tea

@router.delete("/teas/{tea_id}")
def delete_tea(tea_id: int, db: Session = Depends(get_db), current_user: UserModel = Depends(get_current_user)):  # Add the current_user dependency
    db_tea = db.query(TeaModel).filter(TeaModel.id == tea_id).first()
    if not db_tea:
        raise HTTPException(status_code=404, detail="Tea not found")

    db.delete(db_tea)
    db.commit()
    return {"message": f"Tea with ID {tea_id} has been deleted"}
```

In the above code, we use FastAPI's `Depends` functionality to add authentication to the routes. Before the request is processed, the `get_current_user` function is called to verify the user's token. If the token is invalid or missing, the request is blocked.

## Testing Authorized Routes

With our controller updates in place, now we can test and verify our authorized routes.

### 1. Attempting unauthorized access

First, let's try to make a `POST /teas` request in FastAPI's interactive console [`127.0.0.1:8000/docs`](127.0.0.1:8000/docs) without being logged in. Since the controller is updated with the `get_current_user` dependency, we should expect to receive an `unauthorized` response (HTTP 403).

#### Test unauthorized `POST /teas` Request

1. Open FastAPI's interactive console at [`127.0.0.1:8000/docs`](127.0.0.1:8000/docs).
2. Try executing a `POST /teas` request without logging in.
3. **Expected Result**: You should see a 403 response with the message `Not authenticated`. This confirms that the route is protected and requires authentication.

### 2. Logging in and making an authorized request

Now, let's try submitting an authenticated request to the `POST /teas` endpoint. We need to log in and then create a new tea using the FastAPI docs.

#### Log In via `POST /login` endpoint

1. In the interactive console, navigate to the `POST /login` endpoint.
2. Enter the correct username.
3. **Expected Result**: You should receive a 200 response with a token value.
4. **Note**: The token should be a long string of numbers and letters. Copy this token without the surrounding quotes.
5. Click the `AUTHORIZE` button on the upper right-hand corner and paste the token in the popup form.

Once authorized, you should be "logged in."

Now, return to the `POST /teas` request.

#### Test authorized `POST /teas` request

1. Go back to the `POST /teas` endpoint.
2. Execute the request again.
3. **Expected Result**: You should receive a 200 response indicating success.
4. **Verification**: Check the teas table in your `psql` database to ensure the new tea entry has been saved.

If you authenticated the client and verified the data in your database, congratulations! You've successfully protected your routes.
