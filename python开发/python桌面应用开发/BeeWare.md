# BeeWare

BeeWare 并不是一个单独的产品、工具或库 (library)，它是一系列工具和库的集合：每个工具和库都能协同工作，帮助您编写跨平台、具有本地图形用户界面的 Python 应用程序。它包括:

- Toga，一个跨平台的 widget (小部件，控件) 工具包;
- Briefcase，一个用于将Python项目打包为可分发的成品，可以发送给最终用户的工具;
- 库（如 Rubicon ObjC），用于访问平台原生库的库;
- 预编译的Python构建版本，可在官方Python安装程序不可用的平台上使用。

作为用户，只需要与前两个（Toga和Briefcase）互动。然而，每个工具也可以单独使用 - 例如，你可以使用Briefcase部署应用程序，而不使用Toga作为GUI工具包。

BeeWare套件可用于：macOS、Windows、Linux（使用GTK）；在移动平台如Android和iOS；以及Web上。

## 开始第一个应用程序

1. 创建一个python虚拟环境
`python -m venv myenv`

2. 安装 BeeWare 套件
`pip install briefcase`

3. 创建一个新app
`briefcase new`
   - 接下来会需要提供一些信息
     - 正式名称 - 接受默认值：你好，世界。
     - App Name - 接受默认值：helloworld。
     - 捆绑 - 如果您拥有自己的域名，请按相反顺序输入该域名。(例如，如果您拥有域名 “cupcakes.com”，则输入 com.cupcakes 作为捆绑）。如果您没有自己的域名，请接受默认的捆绑包（com.example）。
     - 项目名称 - 接受默认值：你好世界。
     - Description - 接受默认值（或者，如果您想发挥自己的创造力，也可以提出自己的描述！）。
     - 作者 - 在此处输入您自己的姓名。
     - 作者电子邮件 - 输入您自己的电子邮件地址。这将用于配置文件、帮助文本以及向应用程序商店提交应用程序时需要电子邮件的任何地方。
     - URL - 应用程序登陆页面的 URL。同样，如果您拥有自己的域名，请输入该域名的 URL（包括 https://）。否则，只需接受默认 URL (https://example.com/helloworld)。此 URL 不需要实际存在（暂时）；只有在您将应用程序发布到应用程序商店时才会使用。
     - 许可证 - 接受默认许可证（BSD）。但这不会影响本教程的任何操作，因此，如果您对许可证选择有特别强烈的意见，请随意选择其他许可证。
     - 图形用户界面框架 - 接受默认选项 Toga（BeeWare 自己的图形用户界面工具包）。
   - 然后就会看到一个项目骨架
      ```
      beeware-tutorial/
      ├── beeware-venv/
      │   └── ...
      └── helloworld/
         ├── CHANGELOG
         ├── LICENSE
         ├── pyproject.toml    # 描述了如何打包发布应用程序
         ├── README.rst
         ├── src/    # 包含应用程序的所有代码
         │   └── helloworld/
         │       ├── app.py
         │       ├── __init__.py
         │       ├── __main__.py
         │       └── resources/
         │           └── README
         └── tests/  # 包含初始测试套件
            ├── helloworld.py
            ├── __init__.py
            └── test_app.py
      ```

4. 以开发者模式运行应用程序
`briefcase dev`

## 项目架构分析

在`src/helloworld`目录下，有三个文件：

- `__init__.py`：将 `helloworld` 目录标记为可导入的 Python 模块。这是一个空文件；它的存在告诉 Python 解释器 `helloworld` 目录定义了一个模块

- `__main__.py`：将 `helloworld` 模块标记为一种特殊的模块 - 可执行模块。如果你尝试使用 `python -m helloworld` 试图运行 `helloworld` 模块，Python 将从``__main__.py`` 文件开始执行。`__main__.py` 的内容相对简单。
   ```python
   from helloworld.app import main

   if __name__ == "__main__":
      main().main_loop()
   ```

- `app.py`：应用程序的主要逻辑。
   ```python
   import toga
   from toga.style import Pack
   from toga.style.pack import COLUMN, ROW

   class HelloWorld(toga.App):
      def startup(self):
         # startup 方法的第一件事是定义一个主盒子 (main box)。Toga 的布局方案类似于 HTML。
         # 你通过构造一系列盒子 (box) 来构建应用程序，每个盒子包含其他盒子或实际的小部件 (widgets)。
         # 然后，你对这些盒子应用样式 (styles)，以定义它们将如何消耗可用的窗口空间 (window space)。
         main_box = toga.Box() 
         
         # 这将创建一个 toga.MainWindow 的实例，它的标题 (title) 将与应用程序的名称 (self.formal_name) 匹配。
         # 主窗口是 Toga 中的一种特殊窗口——它是与应用程序的生命周期 (life cycle) 密切绑定的窗口。当主窗口关闭时，应用程序退出。
         # 主窗口也是具有应用程序菜单的窗口（如果你在像 Windows 这样的平台上，菜单栏是窗口的一部分；
         # 如果你没有进行任何菜单栏的增删操作，你将看到默认的 file (文件) 和 help (帮助) 这两个菜单栏选项）
         self.main_window = toga.MainWindow(title=self.formal_name)

         # 然后，我们将空盒子作为主窗口的内容，并指示应用程序显示我们的窗口:
         self.main_window.content = main_box
         self.main_window.show()

   def main():
      return HelloWorld()
   ```

在我们开发的时候，修改Helloworld类即可
```python
class HelloWorld(toga.App):
    def startup(self):
        main_box = toga.Box(style=Pack(direction=COLUMN))

        name_label = toga.Label(
            "Your name: ",
            style=Pack(padding=(0, 5)),
        )
        self.name_input = toga.TextInput(style=Pack(flex=1))

        name_box = toga.Box(style=Pack(direction=ROW, padding=5))
        name_box.add(name_label)
        name_box.add(self.name_input)

        button = toga.Button(
            "Say Hello!",
            on_press=self.say_hello,
            style=Pack(padding=5),
        )

        main_box.add(name_box)
        main_box.add(button)

        self.main_window = toga.MainWindow(title=self.formal_name)
        self.main_window.content = main_box
        self.main_window.show()

    def say_hello(self, widget):
        print(f"Hello, {self.name_input.value}")
```
Toga 的内置布局系统称为 “Pack” (包)。它的行为很像 CSS (Cascading Style Sheets 层叠样式表)。你可以在一个层次结构中定义对象–在 HTML 中，对象是 \<div> (division 块级容器)、\<span> (inline span 内联容器) 和其他 DOM 元素 (Document Object Model 文档对象模型)；在 Toga 中，对象是部件 (widgets ) 和盒子 (boxes)。然后，您可以为各个元素指定样式。在本例中，我们表示这是一个 COLUMN (垂直) 框，也就是说，它是一个将占用所有可用宽度 (width) 的框，并会随着内容的添加而扩大高度 (height)，但会尽量使高度更短。

Briefcase可以检测到应用程序已经运行过，为了节省时间，它只会运行应用程序。如果你在应用程序中添加了新的依赖项，你可以在运行 `briefcase dev` 时通过 `-r` 选项来确保它们被安装。

## 打包发布应用程序

我们不能一直用开发者模式来运行应用，不能让用户做安装python，下载库，用命令启动这样子的方式，所以需要打包应用。

### 创建应用程序脚手架

#### 准备工作

如果是第一次打包应用程序，需要创建一些配置文件和其他脚手架来支持打包过程，执行
`briefcase create`

这个命令做了如下动作：
1. 它**生成一个应用程序模板**。除了实际应用程序的代码之外，构建本机安装程序还需要许多文件和配置。这些额外的脚手架对于同一平台上的每个应用程序几乎都是一样的，除了正在构建的实际应用程序的名称–因此，Briefcase 为其支持的每个平台提供了一个应用程序模板。这一步将推出模板，替换应用程序名称、捆绑 ID 和配置文件中的其他属性，以支持正在构建的平台。
如果您对 Briefcase 提供的模板不满意，可以提供自己的模板。不过，在使用 Briefcase 的默认模板获得更多经验之前，您可能不想这样做。

2. 它**下载并安装了一个支持包**。公文包所采用的打包方法被描述为 “最简单可行的方法”–它将一个完整、独立的 Python 解释器作为其构建的每个应用程序的一部分。这种打包方式在空间利用上略显不足–如果有 5 个应用程序打包到 Briefcase 中，那么就会有 5 份 Python 解释器副本。但是，这种方法保证了每个应用程序都是完全独立的，使用的 Python 都是已知的能与应用程序一起工作的特定版本。
同样，Briefcase 为每个平台提供了默认的支持包；如果需要，您可以提供自己的支持包，并将其作为构建过程的一部分。如果您需要启用 Python 解释器中的特定选项，或者如果您想从标准库中剥离运行时不需要的模块，您可能需要这样做。
公文包维护支持包的本地缓存，因此一旦您下载了特定的支持包，该缓存副本将用于未来的构建。
As noted above, when Briefcase packages an app as a native Linux system package (the default package format for Linux), a support package is not included with the app. Instead, the app will use the Python that is provided by the distribution of Linux being targeted.

3. 它**安装应用程序要求**。您的应用程序可以指定运行时所需的任何第三方模块。这些模块将使用 pip 安装到应用程序的安装程序中。

4. 它**安装你的应用程序代码**。您的应用程序将有自己的代码和资源（如运行时需要的图像）；这些文件会被复制到安装程序中。

5. 最后，它还会添加安装程序本身所需的其他资源。这包括需要附加到最终应用程序的图标和闪屏图像等。

### 构建应用

执行
`briefcase build`

构建完成后可以在`build\helloworld\windows\app\src\Hello World.exe`找到打包好的应用。

### 运行应用

执行
`briefcase run`

运行完成后会打开一个窗口，显示你刚才打包的应用。

### 创建安装程序

执行
`briefcase package`

打包命令会执行将脚手架项目转换为最终可发布产品所需的编译工作。根据平台的不同，这可能涉及编译安装程序、执行代码签名或执行其他发布前任务。

## 更新应用程序

简易开发包 “通过就地运行您的代码来运行–它试图为您的代码提供尽可能逼真的运行环境，但并不提供或使用任何平台基础架构来将您的代码包装成应用程序。打包应用程序的部分过程包括将代码复制到应用程序捆绑包中，如果之前打包过应用程序，则仍然是旧代码。
因此，我们需要告诉公文包更新应用程序，复制新版本的代码。我们可以删除旧的平台目录，然后从头开始。不过，Briefcase 提供了一种更简单的方法–您可以更新现有捆绑应用程序的代码：
`briefcase update`

如果 Briefcase 找不到脚手架模板，它会自动调用 create 生成一个新的脚手架
更新了安装程序代码后，我们就可以运行 `briefcase build` 来重新编译应用程序，运行 `briefcase run` 来运行更新后的应用程序，并运行 `briefcase package` 来重新打包应用程序以便分发。

### 更新和运行一步到位

为了简化更新和运行打包应用程序的过程，Briefcase 提供了支持这种使用模式的快捷方式 `- run`命令上的 `-u`（或 `--update`）选项
`briefcase run -u`

package 命令也接受 -u 参数，因此如果你修改了应用程序代码并希望立即重新打包，可以运行 `briefcase package -u`。

## 移动应用程序

### Android

#### 创建并编译 Android 应用程序

首先，运行 create 命令。这会下载一个 Android 应用程序模板，并在其中添加您的 Python 代码
`briefcase create android`

首次运行 `briefcase create android` 时，Briefcase 会下载 Java JDK 和 Android SDK。文件大小和下载时间可能相当长；这可能需要一段时间（10 分钟或更长，取决于您的互联网连接速度）。下载完成后，系统将提示您接受 Google 的 Android SDK 许可证。
完成后，我们的项目中就会有一个`build/helloworld/android/gradle`目录，其中包含一个带有 Gradle 构建配置的 Android 项目。这个项目将包含你的应用代码，以及一个包含 Python 解释器的支持包。
然后，我们可以使用 Briefcase 的 build 命令将其编译为 Android APK 应用程序文件。
`briefcase build android`

#### 在虚拟设备上运行应用程序

可以使用 Briefcase 的 run 命令在 Android 设备上运行应用程序。让我们从在 Android 模拟器上运行开始
要运行应用程序，请运行 `briefcase run android`。运行时，系统会提示你可以在哪些设备上运行应用程序。最后一项总是创建新安卓模拟器的选项。
`briefcase run android`

## 调用第三方库

- 以开发者模式运行，只需要安装第三方库即可
- 打包发布，需要将第三方库打包进安装包，这时候需要更新依赖项`pyproject.toml`
   ```python
   [tool.briefcase.app.helloworld]
   formal_name = "Hello World"
   description = "A Tutorial app"
   long_description = """More details about the app should go here.
   """
   sources = ["src/helloworld"]
   requires = [] # 选项描述了应用程序的依赖关系。它是一个字符串列表，其中指定了您希望应用程序包含的库（以及可选的版本）
   ```
在 pyproject.toml 中的更下面部分，你会注意到与操作系统相关的其他部分，如 `[tool.briefcase.app.helloworld.macOS]` 和 `[tool.briefcase.app.helloworld.windows]`。这些部分也有一个 `requires` 设置。这些设置允许你定义额外的特定平台依赖关系，例如，如果你需要一个特定平台的库来处理应用程序的某些方面，你可以在特定平台的 `requires` 部分中指定该库，而该设置将仅用于该平台。你会注意到，所有的 toga 库都是在特定平台的 requires 部分中指定的，这是因为显示用户界面所需的库都是特定平台的。

## 异步编程

我们需要的是一种方法，让处于长期事件处理程序中间的应用程序知道，只要我们能从上次中断的地方继续运行，就可以暂时将控制权释放回事件循环。应用程序可以自行决定何时释放控制权；但如果应用程序定期向事件循环释放控制权，我们就可以拥有一个长期运行的事件处理程序，并保持响应式用户界面。

我们可以通过使用`异步编程`来实现这一点。异步编程是一种描述程序的方法，它允许解释器同时运行多个函数，在所有并发运行的函数之间共享资源。

异步函数（称为 `协同例程`）需要明确声明为异步函数。它们还需要在内部声明何时有机会将上下文切换到另一个共同例程。

在 Python 中，异步编程是通过 `async` 和 `await` 关键字以及标准库中的 `asyncio` 模块来实现的。`async` 关键字允许我们声明一个函数是异步协程。关键字 `await` 提供了一种方法来声明何时有机会将上下文切换到另一个协程。`asyncio` 模块为异步编码提供了一些其他有用的工具和原语。

```python
async def say_hello(self, widget):
    async with httpx.AsyncClient() as client:
        response = await client.get("https://jsonplaceholder.typicode.com/posts/42")

    payload = response.json()

    self.main_window.info_dialog(
        greeting(self.name_input.value),
        payload["body"],
    )
```
- 方法定义为 `async def`，而不只是 `def`。这告诉 `Python` 该方法是一个异步协程。

- 创建的客户端是异步的 AsyncClient() 而不是同步的 `Client()`。这就告诉 `httpx` 应在异步模式而非同步模式下运行。

- 用于创建客户端的上下文管理器被标记为 `async`。这就告诉 Python，当上下文管理器进入和退出时，有机会释放控制。

- `get` 调用带有 `await` 关键字。这指示应用程序在等待网络响应时，可以将控制权释放给事件循环。

## 自定义图标

编辑您的`pyproject.toml`，在配置部分中定义上方 添加一个新的icon配置项 ：`[tool.briefcase.app.helloworld]sources`
```python
icon = "icons/helloworld"
```
此图标定义未指定任何文件扩展名。该值将用作前缀；每个平台都会向此前缀添加其他项，以构建每个平台所需的文件。某些平台需要多个图标文件；此前缀将与文件大小和变体修饰符相结合，以生成要使用的图标文件列表。

我们现在可以再次运行 - 但这一次，我们传递标志 ，告诉 `Briefcase` 我们想要安装新的应用程序资源（即图标）：`briefcase update--update-resources`

当您在 iOS 或 Android 上运行该应用程序时，除了图标更改之外，您还应该注意到启动画面采用了新图标。但是，图标的浅蓝色背景与启动画面的白色背景相比看起来有点不协调。我们可以通过自定义启动画面的背景颜色来解决这个问题。 pyproject.toml在icon定义之后将以下定义添加到您的 中：
```python
splash_background_color = "#D3E6F5"
```