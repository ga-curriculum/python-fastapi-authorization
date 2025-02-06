<h1>
  <span class="headline">FastAPI Authorization</span>
  <span class="subhead">Managing Model Relationships</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to update data models in a FastAPI application to define and enforce relationships between different models.

## Securing data with object permissions

In the previous lesson, we used FastAPI’s `Depends` function to restrict certain actions to logged-in users. However, this approach alone is not enough to fully secure our application.

We need to go a step further and ensure that **users can only modify the data they own**. This level of control is known as **Object Permissions**.

## What are object permissions?

Object permissions determine if a user has the right to perform actions (such as updating or deleting) on specific records in a database. Even after verifying a user's authentication token, our application must still check whether the user has permission to modify a particular resource.

### Examples of object permissions

| Application Type              | Permission Example                                                        |
| ----------------------------- | ------------------------------------------------------------------------- |
| **Content Management System** | Authors can edit their own articles but not those of others.              |
| **Project Management Tool**   | Users can update tasks assigned to them but not tasks assigned to others. |
| **E-commerce Platform**       | Customers can view their own orders but not other customers' orders.      |

## Adding permissions to teas

To restrict updates and deletions to only the user who created a tea entry, we must **create a relationship** between teas and users.

While this requires some restructuring, it ensures that only the correct user can modify their own data.

## Next steps to implement object permissions

Before our application is fully secured we will need to do the following:

1. **Update models** – Link each `tea` to a specific `user` using a foreign key.
2. **Update serializers** – Include a `user` field in the `tea` schema.
3. **Seed related data** – Modify the seed module to create users and associate teas with them.
4. **Update controllers** – Ensure that when a tea is created, it is linked to the logged-in user.
5. **Enforce permissions** – Modify routes to check if the logged-in user owns a tea before allowing updates or deletions.

## Updating the tea model

We previously used relationships for `comments`. Now, we will apply the same approach to connect `teas` to `users`.

```py
# models/tea.py

from sqlalchemy import Column, Integer, String, Boolean, ForeignKey # add foreign key
from sqlalchemy.orm import relationship
from .base import BaseModel
from .comment import CommentModel
from .user import UserModel # add user model

class TeaModel(BaseModel):

    __tablename__ = "teas"

    id = Column(Integer, primary_key=True, index=True)

    name = Column(String, unique=True)
    in_stock = Column(Boolean)
    rating = Column(Integer)

    # NEW: Foreign key linking tea to user
    user_id = Column(Integer, ForeignKey('users.id'))

    # NEW: Relationship - a tea belongs to one user
    user = relationship('UserModel', back_populates='teas')

    comments = relationship("CommentModel", back_populates="tea")
```

## Updating the user model

Now, we modify the `UserModel` to include a relationship with `TeaModel`.

```py
# models/user.py

from sqlalchemy import Column, Integer, String
from .base import BaseModel
from passlib.context import CryptContext
from datetime import datetime, timezone, timedelta
import jwt
from config.environment import secret
from sqlalchemy.orm import relationship # add relationship


pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")

class UserModel(BaseModel):

    __tablename__ = "users"

    id = Column(Integer, primary_key=True, index=True)
    username = Column(String, nullable=False, unique=True)
    email = Column(String, nullable=False, unique=True)
    password_hash = Column(String, nullable=True)

    # NEW: Relationship - a user can have multiple teas
    teas = relationship('TeaModel', back_populates='user')

    def set_password(self, password: str):
        self.password_hash = pwd_context.hash(password)

    def verify_password(self, password: str) -> bool:
        return pwd_context.verify(password, self.password_hash)

    def generate_token(self):
        payload = {
            "exp": datetime.now(timezone.utc) + timedelta(days=1),
            "iat": datetime.now(timezone.utc),
            "sub": self.id,
        }

        token = jwt.encode(payload, secret, algorithm="HS256")

        return token
```

With these changes, each tea will now be linked to a user, allowing us to enforce object permissions in our application. Next, we'll update our serializers.
