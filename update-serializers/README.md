# Update Base Model Serializer

**Learning objective:** By the end of this lesson, you will be able to **update** a base model serializer to reflect changes in data model relationships.

## Overview

Let's update our tea serializer so that the user association is displayed in the docs.

The serializer refactor shown below in the `TeaSchema` accomplishes the following:

**Display User Association**: By including user: `UserSchema` in `TeaSchema`, the serializer includes information about the user associated with each tea.

**Usage in the Application**: This updated schema will also be used to serialize tea objects into JSON format that includes user details. For example, when retrieving tea data through API endpoints, the response will now include information about the user who owns each tea, automatically populating that user's information, with each tea's JSON response.

## Updating the Tea Schema

```py
# serializers/tea.py
from pydantic import BaseModel
from typing import Optional, List
from .comment import CommentSchema
from .user import UserSchema # Import the User schema

class TeaSchema(BaseModel):
  id: Optional[int] = True # This makes sure you don't have to explicitly add an id when sending json data
  name: str
  in_stock: bool
  rating: int
  user: UserSchema # Each tea belongs to a user

  comments: List[CommentSchema] = []

  class Config:
    orm_mode = True
```

So we've just implemented the relationship. We should test the implementation now to make sure we did it correctly.
