# FastAPI Course
## The offical document
- [document](https://fastapi.tiangolo.com/)
## How to start a FastAPI app
- Run the script to start
    ```shell
    # uvicorn is the web server we use to start FastAPI app.
    uvicorn xxx:app --reload
    ```
## Learning Start
### Base Knowledge
- GET
  1. The first app
      ```python
      from fastapi import FastAPI

      app = FastAPI() # This allows uvicorn to identify that key

      @app.get("/")
      async def first_api():
          return {"message": "Hello World!"}
      ```
  2. We can see all of the endpoint by used Swagger UI.
      > 127.0.0.1:8000\docs
  3. Path Parameters
      ```python
      # static path
      @app.get("/books/book_one")

      # dynamic path
      @app.get("/books/{dynamic_param}")
      async def xxx(dynamic_param):
          return ...
      ```
      - One thing to note is that order matters with path parameters.
          ```python
          @app.get("/books/{dynamic_param}")
              async def xxx(dynamic_param):
                  return ...

          @app.get("/books/mybook") # This func will never be get called.
              async def xxx():
                  return ...
          ```
  4. Query Parameters
        ```python
        @app.get("/books/")
            async def xxx(parameter: str):
                return ...
        ```
- POST
  1. The first app
      ```python
      from fastapi import Body, FastAPI

      app = FastAPI() # This allows uvicorn to identify that key

      @app.post("/book/create_book")
      async def create_book(new_book=Body()):
          print(new_book)
      ```
- PUT
  1. The first app
        ```python
        from fastapi import Body, FastAPI

        app = FastAPI() # This allows uvicorn to identify that key

        @app.put("/book/update_book")
        async def update_book(update_book=Body()):
            print(update_book)
        ```
- DELETE
  1. The first app
        ```python
        @app.delete("/books/delete_book/{book_title}")
        async def delete_book(book_title: str)
            return {"book_title": book_title}
        ```
### Review Request Method
- Demo
    ```python
    from fastapi import FastAPI, Body

    app = FastAPI()

    class Book:
        id: int
        title: str
        author: str
        description: str
        rating: int

        def __init__(self, id, title, author, description, rating):
            self.id = id
            self.title = title
            self.author = author
            self.description = description
            self.rating = rating

    BOOKS = [
        Book(1, "Computer Science Pro", "codingwithLucas", "A Very nice book", 5),
    ]

    @app.get("/books")
    async def read_all_books():
        return BOOKS

    @app.post("/create-book")
    async def create_book(book_request=Body()): # This method doesn't have data validation
        BOOKS.append(book_request)
    ```
- Data Validation
    ```python
    from fastapi import FastAPI, Body, Path, Query
    import pydantics # It is used for data modeling, data parsing and has efficient error handling
    from pydantics import BaseModel, Field
    from typing import Optional

    app = FastAPI()

    class Book:
        id: int
        title: str
        author: str
        description: str
        rating: int

        def __init__(self, id, title, author, description, rating):
            self.id = id
            self.title = title
            self.author = author
            self.description = description
            self.rating = rating

    BOOKS = [
        Book(1, "Computer Science Pro", "codingwithLucas", "A Very nice book", 5),
    ]

    @app.get("/books")
    async def read_all_books():
        return BOOKS

    @app.get("/books/query")
    async def read_books_by_rating(book_rating: int = Query(gt=0, lt=6)): # This will do query parameters validation.
        books_to _return = []
        for book in BOOKS:
            if book.rating == book_rating:
                books_to_return.append(book)
        return books_to_return

    class BookRequest(BaseModel):
        id: Optional[int] # It's mean this item is optional now.
        # id: Optional[int] = Field(title="id is not needed") # This can config swagger, it will show on swagger UI.
        # If pydantics2 change -> id: Optional[int] = None
        title: str = Field(min_length=3) # We want the minimal length of a title to be three
        author: str
        description: str
        rating: int = Field(gt=0, lt=6) # The rating can only be between 1 and 5
        
        class Config: # This also can config swagger
            # if pydantics2 change -> json_schema_extra = {}
            schema_extra = {
                'example': {
                    'title': 'A new book',
                    'author': 'Lucas',
                    'description': 'xxxxxxxx',
                    'rating': 5
                }
            }
            

    @app.post("/create-book")
    async def create_book(book_request: BookRequest):
        new_book = Book(**book_request.dict()) # converting the request to Book object
        # if '.dict()' get some of error, please try to use '.model_dump()' because the version of pydantics is different.
        BOOKS.append(new_book)

    @app.put("/boons/update_book")
    async def update_book(book: BookRequest):
        for i in range(len(BOOKS)):
            if BOOKS[i].id == book.id:
                BOOKS[i] = book

    @app.delete("/books/{book_id}")
    async def delete_book(book_id: int = Path(gt=0)): # This will do path parameters validation
        for i in range(len(BOOKS)):
            if BOOKS[i].id == book_id:
                BOOKS.pop(i)
                break
    ```
- Status Code
  1. 1xx -> Information responses that a request is processing.
  2. 2xx -> Success:Request Successfully complete
  3. 3xx -> Redirection:Further action must be complete
  4. 4xx -> Client Errors: An error was caused by the client
  5. 5xx -> Server Errors: An error occurred on the server
- HTTP Exceptions
    ```python
    from fastapi import FastAPI, HTTPException
    from starlette import status

    app = FastAPI()

    @app.get("/book/{book_id}", status_code=status.HTTP_200_OK) # This define the status code when all action are successful
    async def read_book(book_id: int):
        for book in BOOKS:
            if book.id == book_id:
                return book
        return HTTPException(status_code=404, detail='Item not found')
    ```
- Full SQL Database
    ```shell
    # Firstly, we need download some python library for achieve orm framework, Ex.
    pip install sqlalchemy
    ```
    ```python
    # database.py for sqlite
    from sqlalchemy import create_engine
    from sqlalchemy.orm import sessionmaker
    from sqlalchemy.ext.declarative import declarative_base

    SQLALCHEMY_DATABASE_URL = "sqlite:///./todos.db"

    # The engine can open up a connection and use our database
    engine = create_engine(SQLALCHEMY_DATABASE_URL, connect_args={"check_same_thread": False}) # The connect_args is to prevent any kind of sharing of the same connection for different kind of requests

    SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

    Base = declarative_base()
    ```
    ```python
    # model.py
    from database import Base
    from sqlalchemy import Column, Integer, String, Boolean

    class Todos(Base):
        __tablename__ = "todos"

        id = Column(Integer, primary_key=True, index=True) # The parameter 'index' tell our database table that this is indexable, which means it's going to be unique.
        title = Column(String)
        description = Column(String)
        priority = Column(Integer)
        complete = Column(Boolean, default=False)
    ```
    ```python
    # main.py
    from typing import Annotated, Optional

    from pydantics import BaseModel, Field
    from sqlalchemy.orm import Session
    from fastapi import FastAPI, Depends, HTTPException
    from starlette import status
    import models
    from models import Todos
    from database import engine, SessionLocal

    app = FastAPI()

    models.Base.metadata.create_all(bind=engine) # This will only be run if the sqlite does not exist

    def get_db():
        db = SessionLocal()
        try:
            yield db
        finally:
            db.close()

    db_dependency = Annotated[Session, Depends(get_db)]
    
    @app.get("/")
    async def read_all(db: db_dependency):
        return db.query(Todos).all()
    
    @app.get("/todo/{todo_id}", status_code=status.HTTP_200_OK)
    async def read_todo(db: db_dependency, todo_id: int):
        to_model = db.query(Todos).filter(Todos.id==todo_id).first()
        if todo_model is not None:
            return todo_model
        raise HTTPException(status_code=404, detail="Todo not found.")
    
    class TodoRequest(BaseModel):
        title: str = Field(min_length=3)
        description: str = Field(min_length=3, max_length=100)
        priority: int = Field(gt=0, lt=6)
        complete: bool

    @app.post("/todo", status_code=status.HTTP_201_CREATED)
    async def create_todo(db: db_dependency, todo_request: TodoRequest):
        todo_model = Tdodos(**todo_request.dict())

        db.add(todo_model)
        db.commit()

    @app.put("/todo/{todo_id}", status_code=status.HTTP_204_NO_CONTENT)
    async def update_todo(db: db_dependency, todo_id: int, todo_request: TodoRequest):
        todo_model = db.query(Todos).filter(Todos.ID==todo_id).first()
        if todo_model is None:
            reise HTTPException(status_code=404, detail="Todo not found.")
        todo_model.title = todo_request.title
        todo_model.description = todo_request.description
        todo_model.priority = todo_request.priority
        todo_model.complete = todo_request.complete

        db.add(todo_model)
        db.commit()

    @app.delete("/todo/{todo_id}", status_code=status.HTTP_204_NO_CONTENT)
    async def delete_todo(db:db_dependency, todo_id: int):
        todo_model = db.query(Todos).filter(Todods.id==todo_id).first()
        if todo_model is None:
            raise HTTPException(status_code=404, detail="Todo not found")
        db.query(Todos).filter(Todos.id==todo_id).delete()
        db.commit()
    ```
    ```shell
    uvicorn main:app --reload
    ```
- Authentication
    ```python
    # auth.py
    # If you want to use the auth and main at the same time
    # Please move auth or other to package named routers(optional)
    # Next please import APIRouter
    # In the end, please write below in main
    # from routers import auth
    # app.include_router(auth.router)


    from fastapi import FastAPI, APIRouter

    # app = FastAPI()

    # @app.get("/auth")
    # async def get_user():
    #     return {'user': 'authenticated'}

    router = APIRouter()
    @router.get("/auth")
    async def get_user():
        return {'user': 'authenticated'}
    ```
    ```python
    # The foreignkey
    from database import Base
    from sqlalchemy import Column, Integer, String, Boolean, ForeignKey

    class Users(Base):
        __tablename__ = "users"

        id = Column(Integer, primary_key=True, index=True)
        email = Column(String, unique=True)
        username = Column(String, unique=True)
        hashed_password = Column(String)
        is_active = Column(Boolean, default=True)
        role = Column(String)

    class Todos(Base):
        __tablename__ = "todos"

        id = Column(Integer, primary_key=True, index=True) # The parameter 'index' tell our database table that this is indexable, which means it's going to be unique.
        title = Column(String)
        description = Column(String)
        priority = Column(Integer)
        complete = Column(Boolean, default=False)
        owner_id = Column(Interger, ForeignKey("users.id"))
    ```
    ```python
    # hash this password
    # Firstly, you need install python library
    # pip install "passlib[bcrypt]"
    from fastapi import APIRouter
    from models import Users
    from pydantic import BaseModel
    from passlib.context import CryptContext
    from fastapi.security import OAuth2PasswrodRequestForm


    router = APIRouter()

    bcrypt_context = CryptContext(schemes=['bcrypt'], deprecated="auto")

    class Create_User(BaseModel):
        username: str
        email: str
        password: str
        role: str

    @router.post("/create_user")
    async def create_user(db: db_dependency, create_user_request: Create_User):
        create_user_model = Users(
            email = create_user_request.email,
            username = create_user_request.username,
            hashed_password = bcrypt_context.hash(create_user_request.password),
            is_active = True,
            role = "admin"
        )
        db.create(create_user_model)
        db.commit()
    
    # we need install python-multpart
    # pip install python-multipart
    def authenticate_user(username: str, password: str, db):
        user = db.query(Users).filter(Users.username==username).first()
        if not user:
            return False
        if not bcrypt_context.verify(password, user.hashed_password):
            return False
        return user

    # pip install "python-jose[cryptography]"
    from jose import jwt
    
    # You can use cmd 'openssl rand -hex 32' to generate it.
    SECRET_KEY = '8b31ad97538cda7025035a8733f6c4ff642e317925c36f07fb61658c42d5a875'
    ALGORITHM = 'HS256'

    def create_access_token(username: str, user_id: int, expires_delta: timedelta):
        encode = {
            'sub': username,
            'id': user_id
        }
        expires = datetime.utcnow() + expires_delta
        encode.update({"exp": expires})
        return jwt.encode(encode, SECRET_KEY, algorithm=ALGORITHM)

    @router.post("/token")
    async def login_for_access_token(form_data: Annotated[OAuth2PasswrodRequestForm, Depends()], db: db_dependency):
        user = authenticate_user(form_data.username, form_data.password, db)
        if not user:
            return "Failed"
        token = create_access_token(user.username, user.id, timedelta(minutes=20))
        return token

    ```
- Hashing Passwords
    ```python
    # pip install "passlib[bcrypt]"
    from passlib.context import CryptContext

    bcrypt_context = CryptContext(schemes=['bcrypt'], deprecated="auto")

    hashed_password = bcrypt_context.hash(password)

    bcrypt_context.verify(password, hashed_password) # This can verify what is it same between password and hashed_password.
    ```
- Web Token(JWT)
You need watch videos from P62 to P64
- Authorization
    ```python
    # admin.py
    ...

    router = APIRouter(
        prefix='/admin',
        tags=['admin']
    )

    def get_db():
        db = SessionLocal()
        try:
            yield db
        finally:
            db.close()

    db_dependency = Annotated[Session, Depends(get_db)]
    user_dependency = Annotated[dict, Depends(get_current_user)]

    @router.get("/todo", status_code=status.HTTP_200_OK)
    async def read_all(user: user_dependency, db: db_dependency):
        if user is None or user.get("user_role") != admin:
            raise HTTPException(status_code=401, detail='Authentication Failed')
        return db.query(Todos).all()

    ```
- MySQL
    ```python
    # pip install pymysql

    # from typing import Annotated
    from typing_extensions import Annotated

    from fastapi import Depends
    from sqlalchemy import create_engine
    from sqlalchemy.orm import sessionmaker, Session
    from sqlalchemy.ext.declarative import declarative_base

    SQLALCHEMY_DATABASE_URL = "mysql+pymysql://root:root@10.76.2.208:4866/kqsql_demo"

    # The engine can open up a connection and use our database
    engine = create_engine(SQLALCHEMY_DATABASE_URL)

    SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

    Base = declarative_base()
    ```