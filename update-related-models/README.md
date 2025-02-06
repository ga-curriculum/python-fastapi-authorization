**Learning objective:** By the end of this lesson, you will be able to **update** data models in a FastAPI application to establish relationships between different models.

# Updating Base Model

## Overview

We've covered this same relationship before, with Comments. Now, let's create a relationship between teas and users.

## Updating the Tea model with User data

First we'll work on the models:

```py
# models/tea.py
from sqlalchemy import Column, Integer, String, Boolean, ForeignKey
from sqlalchemy.orm import relationship
from .base import BaseModel
from .comment import CommentModel
from .user import UserModel

class TeaModel(BaseModel):

  __tablename__ = "teas"

  id = Column(Integer, primary_key=True, index=True)

  # ! Specific columns for our Tea Table.
  name = Column(String, unique=True)
  in_stock = Column(Boolean)
  rating = Column(Integer)

  # Foreign key for User
  user_id = Column(Integer, ForeignKey('users.id'))

  # Many (TeaModel) to One (UserModel) relationship
  user = relationship('UserModel', back_populates='teas')

  comments = relationship("CommentModel", back_populates="tea")
```

## Updating the User Model with Tea relationship

```py
# models/user.py
from datetime import datetime, timedelta
import jwt
from sqlalchemy import Column, Integer, String
from sqlalchemy.orm import relationship
from .base import BaseModel

pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")

from config.environment import secret

class UserModel(BaseModel):
  __tablename__ = "users"

  id = Column(Integer, primary_key=True, index=True)
  username = Column(String, nullable=False, unique=True)
  email = Column(String, nullable=False, unique=True)

  # One (UserModel) to Many (TeaModel) relationship
  teas = relationship('TeaModel', back_populates='user')

  def generate_token(self):
    payload = {
      "exp": datetime.utcnow() + timedelta(days=1),
      "iat": datetime.utcnow(),
      "sub": self.id,
    }

    token = jwt.encode(
      payload,
      secret,
      algorithm="HS256",
    )

    return token
```

Here we've added the necessary relationships on the model.
