- Kivy 可以运行于 Windows， Linux， MacOS， Android， iOS 等当前绝大部分主流桌面/移动端操作系统。
- Kivy 基于 Python，界面文件和程序文件相互分离的设计思路，设计简洁优雅，语法易学，适合新人入门。

# Kivy

## 安装

Kivy 可以通过 pip 安装：

```
pip install kivy
```

## 入门

```python
from kivy.app import App
from kivy.core.window import Window
from kivy.uix.button import Button

Window.size = (300, 400)  # 设置打开窗口的大小


class MainApp(App):
    def build(self):  # 重写build方法
        return Button(text='Hello World', on_press=lambda x: print('Hello World'))


if __name__ == '__main__':
    MainApp().run()

# 以上程序会出现一个满窗口均是按钮的窗口，点击按钮会弹出Hello World的提示。
```
- Kivy设置界面时注意和别的框架不一样，他是以左下角为原点，和数学坐标系一致

### canvas

Kivy的界面绘制是通过canvas实现的，canvas是一个画布，可以用来绘制各种图形，包括线条、矩形、圆形、椭圆、多边形、位图、文本等。

```python
from kivy.app import App
from kivy.core.window import Window
from kivy.uix.label import Label
from kivy.graphics import Color, Rectangle
from kivy.uix.widget import Widget

Window.size = (400, 400)  # 设置窗口大小为400x400


class MainApp(App):
    def build(self):
        w = Widget()
        with w.canvas:
            Color(1, 0, 0)  # 设置画笔颜色为红色
            Rectangle(pos=(100, 100), size=(100, 100))  # 绘制一个红色矩形
            Color(0, 1, 0)  # 设置画笔颜色为绿色
            Rectangle(pos=(200, 200), size=(100, 100))  # 绘制一个绿色矩形
        return w


if __name__ == '__main__':
    MainApp().run()

# 以上程序会在屏幕上绘制两个矩形，颜色分别为红色和绿色。
```

### 布局

- FloatLayout：默认的布局，可以容纳其他控件，且控件会自动调整大小以适应布局。