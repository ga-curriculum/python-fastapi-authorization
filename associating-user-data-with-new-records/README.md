<h1>
  <span class="headline">FastAPI Authorization</span>
  <span class="subhead">Associating User Data with New Records</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to associate the current logged-in user's data with newly created records, such as tea entries, in FastAPI.

## Adding user data to the `teas` controller

In this lesson, we will modify the `teas` controller so that when a new tea is created, it also records the `user_id` of the current user who is logged in. This allows us to associate the newly created tea with a specific user.

In FastAPI, we can access the current user data through dependencies. Let's update the `teas` controller to include the `user_id` when creating a new tea.

Here is the updated code for the `teas` controller:

```py
# controllers/teas.py

from fastapi import APIRouter, Depends, HTTPException
from sqlalchemy.orm import Session
from models.tea import TeaModel
from models.user import UserModel
from serializers.tea import TeaModel as TeaSchema, TeaCreate as TeaCreateSchema
from typing import List
from database import get_db
from dependencies.get_current_user import get_current_user

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
def create_tea(tea: TeaCreateSchema, db: Session = Depends(get_db), current_user: UserModel = Depends(get_current_user)):
    # NEW: Create a new tea instance and associate it with the current user
    new_tea = TeaModel(**tea.dict(), user_id=current_user.id)
    db.add(new_tea)
    db.commit()
    db.refresh(new_tea)
    return new_tea

@router.put("/teas/{tea_id}", response_model=TeaSchema)
def update_tea(tea_id: int, tea: TeaSchema, db: Session = Depends(get_db), current_user: UserModel = Depends(get_current_user)):
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
def delete_tea(tea_id: int, db: Session = Depends(get_db), current_user: UserModel = Depends(get_current_user)):
    db_tea = db.query(TeaModel).filter(TeaModel.id == tea_id).first()
    if not db_tea:
        raise HTTPException(status_code=404, detail="Tea not found")

    db.delete(db_tea)
    db.commit()
    return {"message": f"Tea with ID {tea_id} has been deleted"}
```

In the `create_tea` endpoint, you'll see that we are now associating the current user's `user_id` with the new tea record. This is achieved by passing `current_user.id` as the `user_id` when creating a new tea.

## Creating a new serializer for `tea`

We also need to update the serializers to handle the creation of new tea records. The `TeaCreate` schema is used specifically for creating new tea records. It contains only the fields required to create a new tea (`name`, `rating`, and `in_stock`).

Update the following in your `tea.py` serializer:

```py
# serializers/tea.py

from pydantic import BaseModel
from typing import Optional, List
from .comment import CommentSchema
from .user import UserSchema

class TeaSchema(BaseModel):
  id: Optional[int] = True
  name: str
  in_stock: bool
  rating: int
  user: UserSchema  # Includes user data associated with the tea
  comments: List[CommentSchema] = []  # Tea's comments

  class Config:
    orm_mode = True

# NEW: add a new schema for creating teas
class TeaCreate(BaseModel):
    name: str
    in_stock: bool
    rating: int
```

The `TeaCreate` schema doesn't include the `id` or `user` fields. These fields are automatically handled by the database when the record is created.

## Verifying `tea` and `user` associations

- After implementing the changes, you can test the functionality using FastAPI's interactive console. When you create a new tea record, make sure to log in first so that the `current_user` is properly set.

- Once the new tea is created, you can check your database (ex: using `psql`) to verify that the `user_id` is correctly associated with the tea.

Now that you've successfully set up user associations with teas, you can move forward with building out more features in your application!
