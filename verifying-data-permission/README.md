# Verifying Data Permission

**Learning objective:** **Learning objective:** By the end of this lesson, you will be able to **implement** controller logic to verify data permissions based on the current logged-in user.

## Overview

Now onto our last step! We'll now need to build in logic into our PUT and DELETE routes, where we check the current logged in user against the user assigned to the tea record that's being referenced in the route. If the user_ids do not match, we return a 402 response and do not perform the operation. If not, we complete the operation. See below for revised PUT and DELETE routes:

## Verifying Object Permissions and Error Handling

```py
@router.put("/teas/{tea_id}", response_model=TeaSchema)
def update_tea(tea_id: int, tea: TeaCreateSchema, db: Session = Depends(get_db), current_user: UserModel = Depends(get_current_user)):
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
    return {"message": f"Tea {tea_id} deleted successfully"}
```

## After Implementation

Always review and test serialization behaviors to ensure they align with your application's requirements and expectations. After implementing the updated PUT and DELETE routes permissioning, test them in FastAPI's interactive console.

- _Attempt_ to update or delete a tea that does not belong to the current logged in user and you should see the a 403 Forbidden response.

Once you're able to confirm our permissions are preventing unauthorized access, we can now proceed to the final check:

- _Attempt_ to update or delete a tea record that _does_ belong to the current user that is logged in. You should see the operation go through successfully.

**Congratulations!** You've now secured your routes and implemented object-level permissioning.

Make your own cup of tea!
