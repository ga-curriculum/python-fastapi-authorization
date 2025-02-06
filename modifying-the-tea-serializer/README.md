<h1>
  <span class="headline">FastAPI Authorization</span>
  <span class="subhead">Modifying the Tea Serializer</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to update a serializer to reflect changes in data model relationships and display user associations in API responses.

## Why update the serializer?

In this lesson, we will update the **tea serializer** so that it includes user information when returning tea data from our API.

Right now, our API does not include user details when retrieving tea data. We need to modify the serializer to:

| Improvement                  | Benefit                                                                          |
| ---------------------------- | -------------------------------------------------------------------------------- |
| **Display user association** | Ensure each tea object includes information about the user who created it.       |
| **Improve API responses**    | When retrieving tea data, responses will now include user details automatically. |

This update will allow the API to return more complete data, making it easier for frontend applications to use.

## Updating the tea schema

We need to modify the `TeaSchema` to include the user information.

```py
# serializers/tea.py

from pydantic import BaseModel
from typing import Optional, List
from .comment import CommentSchema
from .user import UserSchema  # Import the User schema

class TeaSchema(BaseModel):
    id: Optional[int] = True
    name: str
    in_stock: bool
    rating: int
    user: UserSchema  # New: Each tea belongs to a user
    comments: List[CommentSchema] = []

    class Config:
        orm_mode = True

```

### What does this update do?

- **Includes the user data**: Each tea object will now show details about the user who created it.
- **Uses `orm_mode = True`**: This allows SQLAlchemy models to be converted into Pydantic schemas correctly.
- **Automatically populates user details**: When retrieving tea data, the API response will now include the associated user's information.

Now that we've updated the serializer, we should test our changes to ensure the user association appears correctly in API responses.
