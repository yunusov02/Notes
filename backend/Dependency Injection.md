

### what problem does `Depends` solve

```python

@app.get("/users")
def get_users():
	session = Session(engine)
	
	users = session.query(User).all()
	
	session.close()
	
	return users
```


Another endpoints
```python
@app.post("/users")
def create_user():
    session = Session(engine)

    ...

    session.close()
    

@app.delete("/users/{id}")
def delete_user():
    session = Session(engine)

    ...

    session.close()
```


Every endpoint repeats 
- create session
- close session

Whenever an endpoint needs a session call this function this function called dependency

```python

from sqlalchemy.orm import Session

def get_db():

	db = Session(engine)
	
	try:
		yield db
		
	finally:
		db.close()
		
```


### Where does `Depends` come in

```python
from fastapi import Depends

@app.get("/users")
def get_users(
	db: Session = Depends(get_db)
):
	pass
```

Before I call this endpoint I need to call `get_db()`

Modern FastAPI style

many projects now use Annotated


```python
from typing import Annotated
from fastapi import Depends

SessionDependency = Annotated[Session, Depends(get_db)]

```