<h1>
  <span class="headline">FastAPI Authorization</span>
  <span class="subhead">Updating the Seed File</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to update the seed file to create and link related data entities in a database.

To efficiently test our database relationships, we will update our `tea_data.py` and modify our `seed.py` file to ensure that users are created before assigning them to teas. This approach makes testing easier than manually sending API requests.

## Updating `tea` data with user relationships

We need to update our `tea_data.py` to associate each tea with a user using `user_id`.

```py
# data/tea_data.py

from models.tea import TeaModel
from models.comment import CommentModel

# Add a user_id to link each tea to a specific user
teas_list = [
    TeaModel(name="chai", rating=4, in_stock=True, user_id=1),
    TeaModel(name="earl grey", rating=3, in_stock=False, user_id=2),
    TeaModel(name="matcha", rating=3, in_stock=True, user_id=4),
    TeaModel(name="green tea", rating=5, in_stock=True, user_id=1),
    TeaModel(name="black tea", rating=4, in_stock=True, user_id=5),
    TeaModel(name="oolong", rating=4, in_stock=False, user_id=4),
    TeaModel(name="hibiscus", rating=4, in_stock=True, user_id=3),
    TeaModel(name="peppermint", rating=5, in_stock=True, user_id=3),
    TeaModel(name="jasmine", rating=3, in_stock=True, user_id=5)
]

comments_list = [
    CommentModel(content="This is a great tea", tea_id=1),
    CommentModel(content="Perfect for relaxing evenings", tea_id=2),
    CommentModel(content="I love the vibrant green color!", tea_id=3),
    CommentModel(content="So refreshing and healthy!", tea_id=4),
    CommentModel(content="A classic choice for any time of day", tea_id=5)
]
```

Here’s what we changed:

- Each tea now has a `user_id` that links it to a specific user.
- The `comments_list` remains unchanged but ensures comments are properly linked to teas.

### Updating the seeding script

Next, we need to update our `seed.py` to ensure that users are created **before** teas and comments. Since teas now have a `user_id` dependency, we must make sure users exist before assigning them to teas.

```py
# seed.py

from sqlalchemy.orm import sessionmaker
from models.base import Base
from data.tea_data import teas_list, comments_list
from data.user_data import user_list
from config.environment import db_URI
from sqlalchemy import create_engine

engine = create_engine(db_URI)
SessionLocal = sessionmaker(bind=engine)

try:
    print("Recreating database...")
    Base.metadata.drop_all(bind=engine)
    Base.metadata.create_all(bind=engine)

    print("Seeding the database...")
    db = SessionLocal()

    # NEW: Seed users first to establish relationships
    db.add_all(user_list)
    db.commit()

    db.add_all(teas_list)
    db.commit()

    db.add_all(comments_list)
    db.commit()

    db.close()

    print("Database seeding complete! 👋")
except Exception as e:
    print("An error occurred:", e)
```

### Verifying the seeded data

To check that our database seeding worked correctly, run:

```sh
pipenv run python seed.py
```

Then, inspect your database to confirm:

- Users were created first.
- Teas now include `user_id` values linking them to users.
- Comments are still properly associated with teas.

Once everything is correctly set up, your database will now contain teas that are linked to users, making it easier to test user-based queries in your API.
