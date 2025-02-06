# Accessing Current User Data

**Learning objective:** By the end of this lesson, you will be able to **access** the current user data within FastAPI routes and use it to associate user information with newly created records.

# Overview

Now we'll need to work on the teas controller. What we'll want to do here is to make sure that when a new `tea` is created, that the `user_id` for the current user that is logged in is also recorded to the new record.

## Adding User Data to the Teas Controller

```py
# controllers/teas.py

from fastapi import APIRouter, Depends, HTTPException, Security
from sqlalchemy.orm import Session
from models.tea import TeaModel
from models.user import UserModel
from serializers.tea import TeaModel as TeaSchema, TeaCreate as TeaCreateSchema # Add a new schema for tea creation
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
    # Create a new tea instance.
    # Assign the user_id of the tea to the id of the current user.
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
    return {"message": f"Tea {tea_id} deleted successfully"}
```

You'll notice here that we are now saving the current user to the new tea record we created in the POST /teas endpoint.

Also note that we've added a new `TeaCreate` schema to our teas serailizer, The new schema will be neccessary for creating a new tea. The `TeaCreate` schema has fewer fields, limiting the amount of required data needed to create a new tea.

```py
# serializers/tea.py
from pydantic import BaseModel
from typing import Optional, List
from .comment import CommentSchema
from .user import UserSchema

class TeaSchema(BaseModel):
  id: Optional[int] = True # This makes sure you don't have to explicitly add an id when sending json data
  name: str
  in_stock: bool
  rating: int
  user: UserSchema

  comments: List[CommentSchema] = []

  class Config:
    orm_mode = True

class TeaCreate(BaseModel): #Add a new schema for tea creation
    name: str
    in_stock: bool
    rating: int
```

## Verifying Tea and User Associations

Now go ahead and create a new tea record through FastAPI's interactive console. You'll likely need to "login" again in order to perform the operations.

Once done, you can check to make sure that the tea record was successfully created along with the associated `user_id` by viewing the tea records in `psql`.
