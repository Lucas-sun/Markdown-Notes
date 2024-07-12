# FastAPI
## Two modules about Pydantic and Starlette
- Starlette mainly develop web part
- Pydantic mainly develop data analyze part
## Preparing
- `pip install fastapi`
- `pip install uvicorn`
- `pip install pydantic`
## Run FastAPI
1. method 1
    ```shell
    # xxx is the python file name without suffix
    uvicorn xxx:app --reload
    ```
2. method 2
    ```python
    # main.py
    from fastapi import FastAPI
    import uvicorn

    app = FastAPI()

    # Write your code here

    if __name__ == "__main__":
        # You can look source code of uvicorn if you want to learn it
        uvicorn.run("main:app", port=8000, debug=True, reload=True)
    ```
## Swagger UI
- What is it?
  - It is a very nice debug website even can replace postman
- How to open it?
  - Open your bowser and input `127.0.0.1:8000/docs` on address bar can do it
# Start Offical Learning
## Path Operation
1. Decorators Parameters
    ```python
    from fastapi import FastAPI
    import uvicorn

    app = FastAPI()

    # Please look source code of decorators
    # Param: tags: You can see it on swagger ui
    # Param: summary:
    # Param: description:
    # Param: response_description:
    # Param: deprecated: It decides the endpoint whether user can use it
    @app.post("/items",
    tags=["This is a demo api"],
    summary="This is a summary",
    description="This is a description",
    response_description="",
    deprecated=False
    )
    async def demo():
        return ""

    if __name__ == "__main__":
        uvicorn.run("main:app", port=8000, debug=True, reload=True)
    ```
2. include_router
    ```python
    # main.py
    from fastapi import FastAPI
    import uvicorn
    import demo

    app = FastAPI()

    app.include_router(demo.router, prefix="/xxx", tags=["xxx"])

    if __name__ == "__main__":
        uvicorn.run("main:app", port=8000, debug=True, reload=True)

    # demo.py
    from fastapi import APIRouter

    router = APIRouter()

    # Write your api endpoint here
    ```
## Request And Response
1. Path Parameters
    ```python
    from fastapi import APIRouter

    router = APIRouter()

    @router.get("/demo/{id}")
    async def demo(id: int):
        return {"demo_id": id}

    # *****************************************************************************
    # **Please note the order of endpoint, the path parameters will effect router**
    # *****************************************************************************
    ```
2. Query Parameters
    ```python
    from fastapi import APIRouter
    from typing import Union, Optional

    router = APIRouter()

    # Declare parameters that not belong to path parameters will auto be query parameters
    @router.get("/demo")
    async def demo(id: int):
        # Do some database query action
        return {"demo_id": id}

    # It can not send query params if the query param has default value
    @router.get("/demo2")
    # The Union can receice more data type
    # Union[str, None] <==> Optional[str] The two parts of code is same
    async def demo(id: int=None, item: Union[str, None]):
        # Do some database query action
        return {"demo_id": id}
    ```
3. Request Body
    ```python
    # The code will do data validation and receive request
    from fastapi import APIRouter
    from pydantic import BaseModel, Field, validator
    from typing import Optional, List

    router = APIRouter()

    class demo2Model(BaseModel):
        key1: str
        key2: str

    class demoModel(BaseModel):
        # Please look source code of Field to use parameters
        key1: int = Field(default=0)
        key2: str = Field(regex=r'')
        key3: Optional[str, int] = None
        key4: List[int]
        # This can import outside validation
        key5: demo2Model

        # You can also use validator to validate data, such as
        @validator("key4")
        def demo_validation(cls, value):
            # Do some validation
            return value

    @router.post("/demo")
    async def demo(data: demoModel):
        # It will auto serialize data to dict
        return data
    ```
4. Form Data
   - You must install below library to use pydantic.Form
        `pip install python-multipart`
    ```python
    from fastapi import APIRouter, Form

    router = APIRouter()

    @router.post("/demo")
    async def demo(data: str=Form()):
        
        return {"data": data}
    ```
5. File Upload
    ```python
    # It just suitable for uploading small files
    from fastapi import APIRouter, File
    from typing import List

    router = APIRouter()

    @router.post("/file")
    async def demo(file: bytes=File()):
        print("file", file)
        return {"file": file}

    # The below codes is suitable for uploading more files
    @router.post("/files")
    async def demo(files: List[bytes]=File()):
        for file in files:
            print(file)
        return {"files": files}
    ```
    ```python
    # It is suitable for uploading bigger files
    from fastapi import APIRouter, UploadFile
    from os.path import join
    from typing import List

    router = APIRouter()

    @router.post("/uploadFile")
    async def demo(file: UploadFile):
        path = ""
        path = join(path, file.filename)
        with open(path, "wb") as f:
            for line in file.file:
                f.write(line)
        return {"file": file.filename}

    @router.post("/uploadFiles")
    async def demo(files: List[UploadFile]):
        path = ""
        for file in files:
            path = join(path, file.filename)
            with open(path, "wb") as f:
                for line in file.file:
                    f.write(line)
        return {"file": files}
    ```
6. Request Object
    ```python
    from fastapi import APIRouter, Request
    from typing import List

    router = APIRouter()

    @router.post("/demo")
    async def demo(request: Request):
        print("URL", request.url)
        print("Client IP", request.client.host)
        print("Headers:", request.headers)
        print("cookies", request.cookies)
        return {...}
    ```
7. Request Static Files
   - Static files just be built by developer
   - Dynamic files just be generated by web server
    ```python
    from fastapi import FastAPI
    import uvicorn
    from fastapi import StaticFiles

    app = FastAPI()

    # It sets a static web url to provide for user to get it
    app.mount("/demo", StaticFiles(directory="statics_file_path"))

    if __name__ == "__main__":
        uvicorn.run("main:app", port=8000, debug=True, reload=True)
    ```
## Database
### ORM Library
> **Tortoise** ORM is an easy-to-use asyncio ORM inspired by Django
- [Tortoise Introduction](https://www.cnblogs.com/liuzhongkun/p/16777043.html)
  1. Installing
        `pip install tortoise-orm`
  2. Create Model Class
        ```python
        # Models.py
        from tortoise.models import Model
        from tortoise import fields


        class database1(Model):
            id = fields.IntFields(pk=True)
            key1 = fields.CharField(max_length=32, description="xxx")
            key2 = fields.CharField(max_length=32, description="xxx")
            key3 = fields.IntField(description="xxx")

            # One to Many
            # The related_name parameter defines the name of the reverse relation from the related model back to the model that defines the foreign key
            database2 = fields.ForeignKeyField("Models.database2", related_name="students")

            # Many to Many
            database3 = fields.ManyToManyField("Models.database3", related_name="students")

        class database2(Model):
            key1 = fields.CharField(max_length=32, description="xxx")

        class database3(Model):
            key1 = fields.CharField(max_length=32, description="xxx")
        ```
  3. Create New Tables in database
        ```python
        # settings.py
        TORTOISE_ORM = {
            'connections': {
                'default': {
                    # 'engine': 'tortoise.backends.asyncpg', # PostgreSQL
                    'engine': 'tortoise.backends.mysql', # MySQL or Mariadb
                    'credentials': {
                        'host': '127.0.0.1',
                        'port': '3306',
                        'user': 'xxxx',
                        'password': 'xxxx',
                        'database': 'xxxx',
                        'minsize': xx,
                        'maxsize': xx,
                        'charset': 'utf8m64',
                        'echo': True
                    }
                }
            },
            'apps': {
                'models': {
                    'models': ['xxx.models', 'xxx.models'], # The models python file
                    'default_connection': 'default'
                }
            },
            'use_tz': False,
            'timezone': 'Asia/Shanghai'
        }
        ```
```python
# main.py
from fastapi import FastAPI
import uvicorn
from tortoise.contrib.fastapi import register_tortoise
from settings import TORTOISE_ORM

app = FastAPI()

# Please look the source code of register_tortoise
register_tortoise(
    app=app,
    config=TORTOISE_ORM,
    # generate_schemas=True, # It will auto generate form if the database is blank. Don't open it when running with production mode.
    # add_exception_handlers=True, # It maybe discloses debug information when ruuning with production mode.
)

if __name__ == "__main__":
    uvicorn.run("main:app", port=8000, debug=True, reload=True)
```