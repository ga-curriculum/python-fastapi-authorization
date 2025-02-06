# Updating Seeding Program

**Learning objective:** By the end of this lesson, you will **practice** using a seeding program to establish relationships between different data entities.

## Overview

It will be much easier to test this through seeding, rather than making requests. So let's update our `tea_data.py`.

**data/tea_data.py**

```py
from models.tea import TeaModel
from models.comment import CommentModel
from models.user import UserModel # Import user model

teas_list = [
  TeaModel(name="chai", rating=4, in_stock=True, user_id=1),
  TeaModel(name="earl grey", rating=3, in_stock=False, user_id=2)
]

# ! Specify the tea_id on the CommentModel, to associate it with a tea
comments_list = [CommentModel(content="This is a great tea", tea_id=1)]

```

Here we've added a `user_id` for every tea generated. We've also imported the User model up top.

But we need to change one thing on the `seed.py` seeding program file:

```py
# seed.py
from sqlalchemy.orm import Session, sessionmaker
from passlib.context import CryptContext
from models.base import Base
from models.tea import TeaModel
from models.comment import CommentModel
from data.tea_data import teas_list, comments_list
from data.user_data import user_list
from config.environment import db_URI
from sqlalchemy import create_engine

engine = create_engine(db_URI)
SessionLocal = sessionmaker(bind=engine)

# ! This seed file is a separate program that can be used to "seed" our database with some initial data.
try:
  print("Recreating database..")
  Base.metadata.drop_all(bind=engine)
  Base.metadata.create_all(bind=engine)

  print("seeding our database..")
  # ! Seed teas
  db = SessionLocal()
  db.add_all(user_list) # Make sure to seed the users BEFORE the teas
  db.commit()

  db.add_all(teas_list)
  db.commit()

  db.add_all(comments_list)
  db.commit()


  db.close()

  print("bye 👋")
except Exception as e:
  print(e)

```

In our seed program, we need to make sure that we've generated user records BEFORE the teas since the user records are required to generate the teas because of the relationship we just created.

## Verifying the seeded User and Tea data

Run `pipenv run python seed.py`, and check your database, to see that teas are now being created with users. If so, then we've successfully set up the relationship.
