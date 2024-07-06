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
  2. We can see all of the endpoint by uesd Swagger UI.
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
from fastapi import FastAPI, Body
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
```