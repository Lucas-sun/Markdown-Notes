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
- How to config it?
  - Config your decorators parameters for showing on swagger ui, you can ask chatgpt to learn it or look source code.
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

    # It not must send query params if the query param has default value
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
   - You must install below library to use fastapi.Form
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
8. The Response Model
    ```python
    from fastapi import APIRouter
    from pydantic import BaseModel, EmailStr

    router = APIRouter()

    class Demo(BaseModel):
        key1: str
        key2: str
        key3: EmailStr

    items = {
        "one": {"key1": "123"}
        "two": {"key2": "123", "key3": "123"}
        "three": {"key1": "123", "key2": "123", "key3": "123"}
    }

    # param: response_model: Do serialization for showing to client
    # param: response_model_exclude_unset: If the key is unset in database, it will not serialize that key when true.
    # param: response_model_exclude_none
    # param: response_model_exclude_default
    # param: response_model_exclude
    # param: response_model_include
    @router.post("/xxx", response_model=Demo, response_model_exclude_unset=True)
    async def demo():
        return items["one"]
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
            # add_exception_handlers=True, # It maybe discloses debug information when runing with production mode.
        )

        if __name__ == "__main__":
            uvicorn.run("main:app", port=8000, debug=True, reload=True)
        ```
        > Now we need do some database migration
        > The aerich is an orm migration tool, It needs to be used with tortoise, please use below cmd to install to your pc
        ```shell
        pip install aerich

        # Firstly, you must add "aerich.models" to your settings.TORTOISE_ORM.apps.models.models, and then you can just run it.
        # It needs to be used one time because of initing config.
        aerich init -t settings.TORTOISE_ORM # the position of TORTOISE_ORM config
        # It will generate a new file named pyproject.toml and a new dir named migrations when the command finished.
        # It only generates something and not operates anything with database.

        aerich init-db
        # It will generate tables base on class created by ourself when the command finished.

        aerich migrate --name xxxx
        # When we update our tables, we need do migration for save our old data, the command can generate a migration file.
        # The parameter "name" is for naming this migrate
        aerich upgrade
        aerich downgrade
        # We can update or downdate the tables when migration files have created
        ```
### Apply Database object
```python
from fastapi import APIRouter
from Models import *

router = APIRouter(
    prefix="/demo"
)

# Read All
@router.get("/")
async def getAllDemo():
    instances = await database1.all() # QuerySet

    for instance in instances:
        pass

    # One To Many Query
    for instance in instances:
        await instance.database2.values("key1") # one object query
    
    await instances.values("database2__key1") # more objects query

    # Many To Many Query
    for instance in instances:
        await instance.database3.all() # one object query

    await instances.values("database3__key1") # more objects query

    return {}

# Read Some
@router.get("/{id}")
async def getSomeDemo(id: int):
    instances = await database1.filter(id=id)
    instance = instances.first() # <====> await database1.get(id=id)

    # instance_infos = instances.values("key1", "key2") # It gets keys of data or not objects
    return {}

# Add New
from pydantic import BaseModel, Field
from typing import Optional, List

class demoModel(BaseModel):
    # Please look source code of Field to use parameters
    key1: int = Field(default=0)
    key2: str = Field(regex=r'')
    key3: Optional[str, int] = None
    key4: List[int]
    database3: List[int]

@router.post("/")
async def addDemo(demo: demoModel):
    instance = database1(**demo.dict())
    await instance.save() # <==> instance = await database1.create(**demo)

    # Many To Many relation
    data3s = await database3.filter(id__in=demo.database3)
    await instance.database3.add(*data3s)

    return {}

# Update One
@router.put("/{id}")
async def updateDemo(id: int, demo: demoModel):
    demo = demo.dict()
    database3 = demo.pop("database3")

    await database1.filter(id=id).update(**demo)

    # ManyToMany key operation
    instance = await database1.get(id=id)
    await instance.database3.clear()
    data3s = await database3.filter(id__in=database3)
    await instance.database3.add(*data3s)

    return {}

# Delete One
@router.delete("/{id}")
async def deleteDemo(id: int):
    deleteCount = await database1.filter(id=id).delete()
    if not deleteCount:
        raise HTTPException(status_code=404, detail="Not Found!")
    return {}
```
## Middleware And CORS
> The middleware just is a function in FastAPI.
> A complete middleware has request code block and response code block in the function
1. middleware demo
    ```python
    from fastapi import FastAPI, Request
    from fastapi.response import Response
    import uvicorn

    app = FastAPI()

    # The order of function execution is middleware2 -> middleware1, that means reversing the normal order.

    @app.middleware("http")
    async def middleware1(request: Request, call_next):
        # request code block

        response = await call_next(request)
        # response code block
        return response

    @app.middleware("http")
    async def middleware2(request: Request, call_next):
        # request code block
        if False:
            # Please look the source code of Response
            return Response(status_code=403, content="Forbidden")
        
        response = await call_next(request)
        # response code block
        return response

    if __name__ == "__main__":
        # You can look source code of uvicorn if you want to learn it
        uvicorn.run("main:app", port=8000, debug=True, reload=True)

    ```
2. CORS demo
    > A middleware for cross-origin requests
    ```python
    from fastapi import FastAPI, Request
    import uvicorn

    app = FastAPI()

    @app.middleware("http")
    async def CORSMiddleware(request: Request, call_next):
        response = await call_next(request)
        response.headers["Access-Control-Allow-Origin"] = "*"
        return response

    if __name__ == "__main__":
        # You can look source code of uvicorn if you want to learn it
        uvicorn.run("main:app", port=8000, debug=True, reload=True)
    ```
    ```python
    from fastapi import FastAPI
    from fastapi.middleware.cors import CORSMiddleware
    import uvicorn

    app = FastAPI()

    origin = [
        "http://localhost:64574"
    ]

    @app.add_middleware(
        CORSMiddleware,
        allow_origin=origin, # On behalf of clients
        allow_credentials=True,
        allow_method=["GET"],
        allow_headers=["*"],
    )

    if __name__ == "__main__":
        # You can look source code of uvicorn if you want to learn it
        uvicorn.run("main:app", port=8000, debug=True, reload=True)
    ```
## Dependency Injection
1. demo
    ```python
    from fastapi import Depends
    from typing import Optional

    async def demo2(param1: Optional[int]=1, param2: Optional[int]=10):
        return {"param1": param1, "param2": param2}

    @xxx.get("/")
    async def demo1(demo2: dict=Depends(demo2)):
        return demo2

    # Here is the execution flow.
    # exec demo1 -> exec demo2 -> assign param:demo2 -> continue demo1

    # Parameters in func demo2 get from such as query params.

    # We can reuse this dependency
    @xxx.get("/xxx")
    async def demo3(demo2: dict=Depends(demo2)):
        return demo2
    ```
    > We can also use class as dependency, such as..
    ```python
    from fastapi import Depends
    from typing import Optional

    class Demo2:
        def __init__(self, param1: Optional[int]=1, param2: Optional[int]=10)
        self.param1 = param1
        self.param2 = param2

    @xxx.get("/xxx")
    async def demo1(demo2: Demo2=Depends(Demo2)):
        return demo2
    # Two code blocks func are same.
    @xxx.get("/xxxx")
    async def demo1(demo2: Demo2=Depends()):
        return demo2
    ```
    > If we don't use the dependency, It can be written in decorators, such as...
    > We can use it to verify auth What just raise HTTPException in dependency.
    ```python
    @xxx.get("/", dependencies=[Depends(demo2),])
    ```
    > If we need to reuse the dependency, We can do it such as...
    ```python
    from fastapi import FastAPI

    async def demo2():
        if False:
            raise HTTPException(status_code=status.HTTP_404_NOT_FOUND, detail="xxxxx")
    
    app = FastAPI(dependencies=[Depends(demo2),])
    ```
## Authentication and Authorization
```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/demo")
async def demo():
    
````