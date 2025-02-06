<h1>
  <span class="headline">FastAPI Authorization</span>
  <span class="subhead">Verifying Permissions for PUT and DELETE</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to implement controller logic to verify data permissions based on the currently logged-in user.

In this lesson, we will add permission checks to the `PUT` and `DELETE` routes of our application. The goal is to ensure that only the user who created a specific tea can update or delete it. We will compare the `user_id` of the tea record with the `id` of the currently logged-in user. If the user IDs do not match, we will prevent the operation and return a `403 Forbidden` error.

## Verifying object permissions and error handling

In this step, we will add logic to check the user's permissions before allowing updates or deletions of a tea record.

### Implementing permission checks for `update`

To check if the user is allowed to update a tea record, we compare the `user_id` from the tea record with the `id` of the logged-in user. If they don't match, we return a `403 Forbidden` response:

```py
# controllers/teas.py

@router.put("/teas/{tea_id}", response_model=TeaSchema)
def update_tea(tea_id: int, tea: TeaSchema, db: Session = Depends(get_db), current_user: UserModel = Depends(get_current_user)):
    db_tea = db.query(TeaModel).filter(TeaModel.id == tea_id).first()
    if not db_tea:
        raise HTTPException(status_code=404, detail="Tea not found")

    # Check if the current user is the creator of the tea
    if db_tea.user_id != current_user.id:
        raise HTTPException(status_code=403, detail="Operation forbidden")

    tea_data = tea.dict(exclude_unset=True)
    for key, value in tea_data.items():
        setattr(db_tea, key, value)

    db.commit()
    db.refresh(db_tea)
    return db_tea
```

## Implementing permission checks for `delete`

Similar to the update logic, we need to check that the logged-in user is the one who created the tea before allowing deletion:

```py
# controllers/teas.py

@router.delete("/teas/{tea_id}")
def delete_tea(tea_id: int, db: Session = Depends(get_db), current_user: UserModel = Depends(get_current_user)):
    db_tea = db.query(TeaModel).filter(TeaModel.id == tea_id).first()
    if not db_tea:
        raise HTTPException(status_code=404, detail="Tea not found")

    # Check if the current user is the creator of the tea
    if db_tea.user_id != current_user.id:
        raise HTTPException(status_code=403, detail="Operation forbidden")

    db.delete(db_tea)
    db.commit()
    return {"message": f"Tea with ID {tea_id} has been deleted"}
```

## Testing final code

Now that we've added final permission checks, it's important to test that the logic works as expected.

### Test unauthorized access

- Try to update or delete a tea that does not belong to the currently logged-in user. You should receive a `403 Forbidden` response.

### Test authorized access

- Now, try to update or delete a tea record that belongs to the logged-in user. The operation should succeed.

**Congratulations!** You've now secured your routes and implemented object-level permissioning.

Enjoy your work and make your own cup of tea!
