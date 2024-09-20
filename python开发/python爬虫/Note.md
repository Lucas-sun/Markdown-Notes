# 爬虫
## 啥是爬虫
- 代码模拟浏览器或手机去执行某些动作
## 爬虫的本质是啥
1. 分析正常的网络请求
2. 用代码去模拟正常的网络请求
### 请求分析(1/2)
- 安装谷歌浏览器
- 用无痕模式去查看网页（去除干扰）
- 抓包分析
  - Network分析
    - 请求头-remote access：就是该域名对应的ip网址和端口号
    - User-Agent：表示当前的设备
  - 重要参数
    1. URL地址
    2. 请求头
    3. 响应体
    4. 响应头
  - 分析思路
    - 请求方式：GET or POST
    - 请求地址
    - 请求头
    - 请求体
### 请求模拟(2/2)
```python
# 发送GET请求
import requests
res = requests.get(url=xxx,headers={xxx:xxx})

print(res.text) # 响应体
print(res.headers) # 响应头

# 发送POST请求
import requests
res = requests.post(url=xxx,headers={xxx:xxx},data={xxx:xxx})

print(res.text) # 响应体
print(res.headers) # 响应头
```
### 案例：花瓣网
### 案例：豆瓣网
- 比花瓣网多个一层防护，需要带入user-agent
### 案例：腾讯课堂

- 模拟发送时
```python
requests.post(
    url=xxx,
    json={xxx:xxx}
)
requests.post(
    url=xxx,
    data={xxx:xxx}
)
# 上述两种方式用哪种具体要看Payload中的格式
```
- Referer指的是你从哪里跳转到这个网页的，也就是**上一次**你浏览的网页
## URL参数
```python
import requests

requests.get(url=xxx?xxx)
requests.get(
    url=xxx,
    params={
        xxx:xxx
    }
)
# 上述两种方式等价，不过第一种方式需要对中文转移
```
## 请求体格式
```
name=wupeiqi&age=18&size=99
请求头：Content-Type:application/x-www-form-urlencoded; charset-UTF-8

{"name":"wupeiqi","age":18,"size":99}
请求头：Content-Type:application/json; charset=UTF-8
```
```python
import requests

request.post(
    url=xxx,
    data="xxx=xxx&xxx=xxx",
    headers={
        "Content-Type":"application/x-www-form-urlencoded; charset=UTF-8"
    }
)
request.post(
    url=xxx,
    data={
        xxx:xxx,
        xxx:xxx
    },
    headers={
        "Content-Type":"application/x-www-form-urlencoded; charset=UTF-8"
    }
)
# 上述两种方式等效

requests.post(
    url=xxx,
    json={
        xxx:xxx,
    }
)
requests.post(
    url=xxx,
    data=json.dumps({
        xxx:xxx,
    })
    headers={
        "Content-Type":"application/json; charset=UTF-8"
    }
)
# 上述两种方式等效
```
## Cookie
- 通常用于用户凭证的保存
- 读取返回的cookie
```python
import requests

res = requests.get(
    url=xxx
)
cookie_dict = res.cookies.get_dict()
print(cookie_dict)
```
- 发送时携带cookie
```python
import requests

res = requests.get(
    url=xxx
    headers={
        "Cookie":"xxxxxxxxxx",
    }
)

requests.get(
    url=xxx,
    cookies={
        xxx:xxx,
        xxx:xxx,
    }
)
# 上述两种方式等价
```
## 响应体格式
```python
import requests
import json

res = requests.get(
    url=xxx
)
res.content # 原始响应体（字节类型）

res.text # 原始文本，字节转成字符串形式

# 如果返回的是json格式，可以自动转成json格式，即data=json.loads(res.text),注意如果不能被json.loads会报错
data = res.json()
```
## 提取HTML格式的文本信息
- 基于bs4模块
```python
# beautifulsoup4
from bs4 import BeautifulSoup

html = "xxxx"
soup = BeautifulSoup(html_string,features="html.parser")

tag = soup.find(attrs={"id":"xxx"}) # 属性
print(tag)
print(tag.name)
print(tag.text)
print(tag.attrs)

tag = soup.find(name="h1") # 标签
print(tag)
print(tag.name)
print(tag.text)
print(tag.attrs)

# 找孩子
parent_tag = soup.find(attrs={xxx:xxx})
children_tag = parent_tag.find()

# 找到所有的元素
soup.find_all(recursive=False) # 参数是是否递归的寻找
```
- 基于xpath模块
## 军事网-自动登录
- 表单请求和ajax请求
  - 表单刷新，提交数据页面刷新
  - ajax提交，提交页面不刷新，请求类型是xhr(XMLHttpRequest)
- 常见登录流程
  1. 方式1
     - 正常请求流程
       - 第一次访问，后台会返回内容+Cookie，在cookie中保存当前用户凭证
       - 第二次访问，输入用户名+密码提交，此时浏览器会自动将第一次返回的凭证携带到后台，后台校验成功，此时凭证赋予登录授权
       - 第n次访问，携带Cookie中的凭证去访问，后台就会根据凭证返回用户的相关信息
  2. 方式2
     - 正常请求流程
       - 第一次访问，后台仅返回页面
       - 第二次访问，输入用户名+密码提交，后台校验成功后，在**响应体**或**Cookie**返回登录凭证，【网页一般在Cookie中居多】
       - 第n次访问，携带之前返回的凭证去访问，后台就会根据凭证返回用户的相关信息
- 案例
  1. 抓包分析
  2. 尝试实现
     - 请求头："X-Requested-With":"XMLHttpRequest" 表示ajax请求的标志
## js逆向
- 有些发送到服务器的数据包含一些js本地生成的数据，需要逆向将其算法解出来用
可以使用这个用来做算法验证 [在线加密平台](https://icyberchef.com)
- 注意登陆页面的Cookie的校验字符串
- 断点调试，在浏览器的source上做js断点调试
- 注意：登陆时需要考虑三个因素**Cookie**和**请求头**以及**请求体**
## 图片验证码识别
1. 识别
```python
# pip install ddddocr -i https://mirrors.aliyun.com/pypi/simple
# pip install Pillow
# 需要先下载这两个库

import ddddocr

ocr = ddddocr.Ddddocr(show_ad=False)
with open("xxxx", mode="rb") as f:
    body = f.read()
code = ocr.classification(body)
print(code)
```
2. 图片编码
   - 如果图片的存在格式是base64
```python
import base64
import ddddocr

content = base64.b64decode("xxxxxxxxxxxxxxx")
with open("x.png", mode="wb") as f:
    f.write(content)
ocr = ddddocr.Ddddocr(show_ad=False)
code = ocr.classification(content)
print(code)
```
3. 获取在线验证码图片
   - 可能需要一些密钥来获取验证码图片，有可能存在某些标签的属性中
   - 可能需要和服务器通信来获取密钥
## 无限反调试
- 可以在debugger语句右键点击never pause ...
## MP3下载
- 状态码为301 Moved Permanently表示重定向，重定向地址为响应头里面的location
- request默认会自动重定向，如不需要可以使用`allow_redirects=False`参数去除
## selenium
[谷歌浏览器最新selenium驱动网址](https://googlechromelabs.github.io/chrome-for-testing)

[114及之前版本](http://chromedriver.storage.googleapis.com/index.html)

[xpath寻找元素方法](https://blog.csdn.net/huiseqiutian/article/details/73739707)
- 比较熟练，无需笔记
- 需要注意的是有些登录页面有验证码操作以及js执行
- 让selenium执行js
```python
driver.execute_script("xxxxxxx")
```
- 延时等待处理
```python
from selenium.webdriver.support.wait import WebDriverWait

WebDriverWait(driver,30,0.5).until(lambda dv:dv.find_element(By.XPATH, "xxxxxxxxxxxx")).click()

def exec():
    if true:
        return "xxx"
    return
WebDriverWait(driver,30,0.5).until(func).click()

# 全局配置
driver.implicitly_wait(10)
# 后续找元素时，没找到则等待10s
```
- 获取文本和属性
```python
# 普通标签
print(tag.text)
print(tag.get_attribute["xxx"])

# input标签
print(tag.get_attribute["placeholder"])
print(tag.get_attribute["value"])

# 选择标签（单选）
print(tag.get_property("checked"))
```
- selenium + bs4
```python
driver.page_source # 网页源代码
# 之后用bs4解析
```
- 携带Cookie
```python
# 一定要先访问再添加cookie
driver.get("xxxxxxxxxxxxxxxxxxx")
driver.add_cookie({xxx:xxx})
```
- IP检测和代理
  - 买代理
  - 设置白名单
  - 生成API链接
```python
opt = webdriver.ChromeOptions()
opt.add_argument(f'--proxy-server={xxxxxx}')
```
- 特征检测和绕过
```python
opt.add_argument("--disable-infobars") # 去除浏览器被自动化软件控制提示
opt.add_experimental_option("excludeSwitches", ["enable-automation"]) # 不同版本可能有不同的方式
opt.add_experimental_option("useAutomationExtension", False) # 三个都填上，确保万无一失
```
- 无头模式
```python
opt.add_argument("--headless") # 无头模式
opt.add_argument("blink-settings=imageEnabled=false")
```
- 截屏
```python
tag = driver.find_element()
tag.screenshot("xxxx.jpg")
```
## 滑块验证和绕过
1. 获取验证码图片
2. 识别图片，计算轨迹距离
   - ddddocr
    ```python
    import ddddocr
    import requests

    slice_bytes = requests.get("xxxxx").content
    bg_bytes = requests.get("xxxx").content

    slide = ddddocr.DdddOcr(det=False,ocr=False,show_ad=False)
    res = slide.slide_match(slide_bytes,bg_bytes,simple_target=True)
    x1,y1,x2,y2 = res["target"]
    print(x1,x2,y1,y2)
    ```
   - opencv
    ```python
    import cv2
    import numpy as np
    import requests

    def get_distance(bg_bytes, slice_bytes):
        def get_image_object(byte_image):
            img_buffer_np = np.frombuffer(byte_image,dtype=np.uint8)
            img_np = cv2.imdecode(img_buffer_np,1)
            bg_img = cv2.cvtColor(img_np, cv2.COLOR_BGR2GRAY)
            return bg_img
        bg_image_object = get_image_object(bg_bytes)
        slice_image_object = get_image_object(slice_bytes)
        # 边缘检测
        bg_edge = cv2.Canny(bg_image_object,255,255)
        tp_edge = cv2.Canny(slice_image_object,255,255)

        bg_pic = cv2.cvtColor(bg_edge,cv2.COLOR_GRAY2RGB)
        tp_pic = cv2.cvtColor(tp_edge,cv2.COLOR_GRAY2RGB)

        res = cv2.matchTemplate(bg_pic, tp_pic, cv2.TM_CCOEFF_NORMED)
        
        min_val, max_val, min_loc, mac_loc = cv2.minMaxLoc(res) # 寻找最优匹配
        x = max_loc[0]
        return x
    ```
   - 接码平台
     - [打码平台](http://www.ttshitu.com)
3. 寻找滑块，控制滑动
```python
from selenium.webdriver import ActionChains

tag = driver.find_element(...)
ActionChains(driver).click_and_hold(tag).perform() # 点击并抓住标签
ActionChains(driver).move_by_offset(xoffset=114,yoffset=0).perform() # 向右滑动114像素（向左是负数）
ActionChains(driver).release().perform() # 释放
```
## 文字点选验证
1. 获取图片
2. 识别
   - ddddocr[^1]
    ```python
    # 目标识别
    ocr = ddddocr.DdddOcr(show_ad=False)
    word = ocr.classification(tag.screenshot_as_png)
    print(word)

    # 文字坐标识别
    # ddddocr
    ocr = ddddocr.DdddOcr(show_ad=False,det=True)
    poses = ocr.detection(content)

    bg_word_dict = {}
    img = Image.open(BytesIO(content))
    for box in poses:
        x1,y1,x2,y2 = box
        corp = img.crop(box)
        img_byte = BytesIO()
        corp.save(img_byte,"png")
        # 识别文字
        ocr2 = ddddocr.DdddOcr(show_ad=False)
        word = ocr2.classification(img_byte.getvalue())
        # 获取每个字的坐标
        bg_word_dict[word] = [int((x1+x2)/2), int((y1+y2)/2)]
    print(bg_word_dict)
    ```
   - 打码平台
     - [超级鹰](https://www.chaojiying.com/)
     - 更适合识别异形文字
[^1]:可以在 [搭建pytorch环境对模型进行训练](https://github.com/sml2h3/dddd_trainer)
3. 坐标点击文字
```python
# 最最重要的是坐标转换
# 假设上述获得的坐标为x、y
x = int(x)-int(img.size["width"]/2)
y = int(y)-int(img.size["height"]/2)
ActionChains(driver).move_to_element_with_offset(标签对象,xoffset=x,yoffset=y).click().perform()
```
## 自动化VS逆向

## 必备知识点
1. python执行javascript
   - 逆向某个网站，例如：md5/aes/rsa 【js】 [python还原]
   - 拿到JavaScript -> python运行
     1. 安装node.js
        - [python] -> [node.js] -> [javascript]
     2. node.js执行js`node xxx.js`
     3. python运行js代码
        ```python
        import subprocess
        res = subprocess.check_output('node demo.js "武沛齐"', shell=True)
        data_string = res.decode('utf-8')
        print(data_string)
        ```
2. python执行javascript(方法2)
   - 仍需要node.js环境
   - 安装pyexecjs `pip install pyexecjs`
3. 图片验证码的识别

## 滑动轨迹snapshot
1. 必备知识点
   - 浏览器环境
     - python -> node.js -> jsdom -> js + 代码
     - 以上可以让node.js拥有浏览器环境
     - `npm install jsdom -g` -g参数指的是全局安装
```python
# 安装命令
npm install -g jsdom
npm install -g node-gyp
npm install -g canvas --canvas_binary_host_mirror=https://npm.taobao.org/mirrors/canvas/

查看npm安装目录
npm root -g

配置环境变量
NODE_PATH = "xxxxx" 路径
```
```javascript
const jsdom = require("jsdom")
const {JSDOM} = jsdom;

const dom = new JSDOM()
document = dom.window.document
```
   - 补环境
     - 遇到什么搜什么
2. 逆向轨迹
   - AES加密 `pip install pycryptodome`
3. 逆向提交sendLoginCode

## 补环境失败
- 找关键字
- 硬核分析
  - 逆向分析算法，全部基于Python

！！！！！有时间再看88-99

## 爬虫，TLS指纹校验原理和绕过
1. 现象导入
   - 浏览器或者手机可以正常访问
   - 代码请求失败
2. 什么是TLS指纹校验？
   - http不存在TLS/SSL校验
   - https=TLS/SSL + http
   - 怎么判断是浏览器还是代码呢
   - 绕过TLS校验思路
     - 修改指纹信息=>md5
     - 模拟浏览器的指纹
3. 查看TLS
   - 网站
   - 安装抓包工具，如 [wireshark](https://www.wireshark.org/download.html)
     - 浏览器发送请求
     - 代码发送请求
4. 如何绕过TLS
   1. requests -> urllib3 -> util -> ssl_.py -> 大概在110行位置有DEFAULT_CIPHERS;
    ```python
    import urllib3
    urllib3.util.ssl_.DEFAULT_CIPHERS = xxxxxx
    ```
   2. curl-cffi
      - `pip install curl-cffi`
      - 底层使用>>>curl 伪造发送请求 => 二次开发（常见浏览器指纹）=> 发送请求
        ```python
        from curl_cffi import requests

        res = requests.get(xxxxxx,xxxxxxx,impersonate="chrome101")
        ```
   3. Go语言（先去学习go语言）
## mitmproxy
- 一个代理工具（软件安装或python模块安装）
- 安装
```shell
python -m venv xxx
source
pip install mitmproxy
# 如果报错，显示缺少c++类库，可以在一下网址中安装visual studio
https://my.visualstudio.com/Downloads?q=Visual%20Studio%202015%20update%203
# 安装完成后在script目录下拥有mitmdump.exe和mitmproxy.exe以及mitmweb.exe三个启动文件
# 一般使用mitmdump.exe，其他的web提供了界面，但dump是在命令行执行
```
- 使用
  - 如果要让mitmproxy支持，http和https请求，**https需要安装证书**
    ```python
    from mitmproxy import http
    from mitmproxy.http import Request

    def request(flow: http.HTTPFlow):
        print("请求",flow.request.path)

    def response(flow: http.HTTPFlow):
        pass
    ```
  - 启动 `mitmdump -q -p 8888 -s v1.py`
    - -q: 静默运行，无log输出
    - -p: 指定端口号
    - -s: 指定运行文件
  - 在本机配置好代理服务器，然后在浏览器打开 [http://mitm.it/](http://mitm.it/)下载证书，之后**安装证书**
- 请求拦截或修改
```python
from mitmproxy import http
from mitmproxy.http import Request

def request(flow: http.HTTPFlow):
    # 修改
    flow.request.url = "xxxxxxxxxxxxxxxxxx"
    # 拦截1
    if flow.request.url.startswith("xxxxxxx"):
        flow.response = Response.make(
            200,
            b"xxxxxx",
            {
                "Content-Type":"text/html",
            }
        )
    # 拦截2
    if flow.request.url.startswith("xxxxxxx"):
        flow.kill()

def response(flow: http.HTTPFlow):
    pass
```
- 响应
  - 和上述类似，亦可修改

## 案例：艺龙
- 必备知识
  1. 动态js的调试(每次刷新js都会重新加载，且网址不一致)
     - 解决方案：将加载js时的网址替换为固定
- 分析请求
- 逐一逆向实现



 