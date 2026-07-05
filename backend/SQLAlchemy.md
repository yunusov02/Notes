
**SQLAlchemy actually contains two libraries**

**SQLAlchemy Core** - you describe SQL using Python
**SQLAlchemy ORM**  - you work with Python Objects



**Database Engines**

An Engine manages communications with the database

IT acts like

```bash
Python - ENGINE - Database
```

Without an Engine SQLAlchemy cannot talk to the database

```python

from sqlalchemy import create_engine

engine = create_engine(
	'sqlite:///database.db',
	echo=True
)
```

`echo=True` prints every SQL statement executed

PostgreSQL example
```python
engine=create_engine(
	'postgresql+psycopg://postgres:password@localhost/shop'
)
```

**Engine != Connection**

Many beginners confuse these
```bash
Engine
    │
    ├── Connection
    ├── Connection
    ├── Connection
```

The Engine creates and manages database connections (using a connection pool) A connection is an actual open link to the database

**ORM Mapping**

Now we create Python classes that map to database tables
```python
from sqlalchemy.orm import DeclarativeBase

class Base(DeclarativeBase):
	pass



# every model inherits from Base

class User(Base):

	__tablename__ = "users"
	
	id: Mapped[int] = mapped_column(primary_key=True)
	name: Mapped[int] = mapped_column(String(100))
	age: Mapped[int]
```

Create all tables

```python
Base.metadata.create_all(engine)
```

**Sessions**

Sessions one of the most important concepts think of it as a workspace for database operations

```bash
Database
       ▲
       │
Session
       │
Python Objects
```


### Engine

An Engine is the object that knows how to communicate with your database
It is created once when your application start and reused
The engine does not store your data and does not execute queries by itself instead it:
- Knows where your database host port database name
- knows which database driver psycopg oracledb
- manages a pool of database connections
- creates connections when they're needed

### Connection

A connection is a single active communication channel between your Python program and Database

- **Engine** - your phone contacts app
- **Connection**- The actual phone call

Creating Connection

```python

with engine.connect() as connection:
	result = connection.execute(text('select 1'))
	
	print(result.all())
```

Connections simple sends SQL to the database


### Sessions

A session is an object that manages your interaction with the database using Python objects instead of raw SQL
Visual Representation 
```bash
Your Code
    │
    ▼
 Session
    │
    ▼
 Connection
    │
    ▼
 Database
```

Sessions doesn't talk to the database directly it asks the engine for a connection then uses that connection to execute SQL


First create an engine

```python

from sqlalchemy import create_engine

engine = create_engine('sqlite:///app.db')

```
now create session factory
```python
from sqlalchemy.orm import sessionmaker

Sessoin = sessionmaker(bind=engine)
```

