# pyqt5基本模板

```python
import sys
from PyQt5.QtWidgets import QApplication, QWidget

if __name__ == "__main__":
    app = QApplication(sys.argv)  # 创建一个app对象，一个pyqt程序中，QApplication对象有且只有一个

    w = QWidget()
    w.setWindowTitle("第一个pyqt程序")
    w.show()

    app.exec()  # 循环监听事件
```





# 基本UI

## 种类

QPushButton：按钮

QLabel：文本框

QLineEdit：输入框

QRadioButton:单选按钮

QGroupBox：组容器

QDigital

QSpinBox

## 基本方法

UI的基本方法：

- setParent()：设置从属关系。
- setGeometry()：设置几何属性：(x，y，w，h)
- frameGeometry()：返回几何属性（一个几何对象）

  - 几何对象的方法：

  - getRect()：得到包含几何属性的元组。
  - center()：得到几何中心点（一个中心点对象）。
    - 中心点对象的方法：
    - x()：得到中心点的x坐标值。
    - y()：得到中心点的y坐标值。
- resize()：设置UI的宽高。
- move()：移动UI的左上角点的x，y坐标。
- clicked.connect()





# 布局

## 概述

布局器**基本类别**：

- QBoxLayout：盒子布局。
- QGridLayout：网格布局。
- QFormLayout：
- QStackedLayout：

UI**设置布局**的方法：

- setLayout()

**布局**的基本方法：

- setStretch()：添加“弹簧”。

- addWidget()：向布局中添加控件。
- addLayouot()：向布局中添加其他布局。

使用方法：

- 控件可以设置布局（setLayout()）,布局可以添加其他控件（addWidget()）,其他控件也可以设置布局。
- 如此 控件、布局、控件、布局……**循环嵌套**下去，可以编排出各种外观。

注意：

- 一个控件只能有一个布局。
- 一个布局可以添加多个控件。
- 布局也可以嵌套布局，就像布局中添加控件一样。

## 盒子布局

### 分类

- QVBoxLayout：垂直布局器
- QHBoxLayout：水平布局器

### 示例

#### 示例1-垂直布局

```python
from sys import argv
from PyQt5.QtWidgets import QApplication, QWidget, QPushButton, QVBoxLayout


class MyWindow(QWidget):
    def __init__(self):
        super().__init__()
        self.setWindowTitle("垂直布局示例")
        self.resize(400, 300)

        # 创建三个按钮
        btn1 = QPushButton("按钮1")
        btn2 = QPushButton("按钮2")
        btn3 = QPushButton("按钮3")

        # 创建布局器
        layout = QVBoxLayout()

        # 向布局器中添加控件
        layout.addStretch(1)  # 添加“弹簧”
        layout.addWidget(btn1)  # 添加控件
        layout.addStretch(1)
        layout.addWidget(btn2)
        layout.addStretch(1)
        layout.addWidget(btn3)
        layout.addStretch(5)  # 添加“弹簧”

        self.setLayout(layout)


if __name__ == '__main__':
    app = QApplication(argv)

    w = MyWindow()
    w.show()

    app.exec()
```

效果：

![垂直箱子布局](.\图片\垂直箱子布局.png)

主要步骤：

- `class MyWindow(QWidget)`：自定义窗口对象，继承于QWidget。
- 在`__init__()`方法中：
  - `btn = QPushButton("按钮")`：创建三个按钮，并将其添加到布局器中。
  - `layout = QVBoxLayout()`：在自定义的窗口对象中，创建布局（布局器）。
  - `layout.addWidget(btn)`、`layout.addStretch()`：在三个按钮之间，添加“弹簧”，来控制三个控件（按钮）的间距。
  - `self.setLayout(layout)`：将自定义的窗口对象设置为刚才创建的布局。


#### 示例2-垂直与水平布局

```python
from sys import argv
from PyQt5.QtWidgets import QApplication, QWidget, QVBoxLayout, QHBoxLayout, QGroupBox, QRadioButton


class MyWindow(QWidget):
    def __init__(self):
        super().__init__()
        self.init_ui()

    def init_ui(self):
        self.setWindowTitle("垂直和水平箱子布局")
        # self.resize(400, 300)
        container = QVBoxLayout()

        hobby_box = QGroupBox("爱好")
        hobby_layout = QVBoxLayout()
        hobby_btn1 = QRadioButton("抽烟")
        hobby_btn2 = QRadioButton("喝酒")
        hobby_btn3 = QRadioButton("烫头")
        hobby_layout.addWidget(hobby_btn1)
        hobby_layout.addWidget(hobby_btn2)
        hobby_layout.addWidget(hobby_btn3)
        hobby_box.setLayout(hobby_layout)

        gender_box = QGroupBox("性别")
        gender_layout = QHBoxLayout()
        gender_btn1 = QRadioButton("男")
        gender_btn2 = QRadioButton("女")
        gender_layout.addWidget(gender_btn1)
        gender_layout.addWidget(gender_btn2)
        gender_box.setLayout(gender_layout)

        container.addWidget(hobby_box)
        container.addWidget(gender_box)

        self.setLayout(container)


if __name__ == '__main__':
    app = QApplication(argv)

    w = MyWindow()
    w.show()

    app.exec()
```

效果：

<img src=".\图片\垂直和水平箱子布局.png" alt="垂直和水平箱子布局"  />

主要步骤：

- `hobby_box = QGroupBox("爱好")`：创建**“爱好”选择框**，组控件。
  - `hobby_layout = QVLayout()`、`hobby_box.setLayout()`设置该组控件为**垂直布局**。
- `gender_box = QGroupBox("性别")`：创建**“性别”选择框**，组控件。
  - `gender_layout = QHLayout()`、`gender_box.setLayout()`设置该组控件为**水平布局**。
- `container = QVBoxLayout()`：创建整体布局，该布局控制“爱好”组控件和“性别”组控件，使两者为垂直布局。

## 网格布局

```python
from sys import argv
from PyQt5.QtWidgets import QApplication, QWidget, QGridLayout, QPushButton, QVBoxLayout, QLineEdit, QSizePolicy


class MyWindow(QWidget):
    def __init__(self):
        super().__init__()
        self.init_ui()

    def init_ui(self):
        self.setWindowTitle("网格布局示例-计算器")
        # self.resize(200, 250)

        # 整体是垂直布局，包含输入/显示区 和 按键区
        layout = QVBoxLayout()
        self.setLayout(layout)

        # 输入/显示区（不是重点）
        edit = QLineEdit()
        edit.setPlaceholderText("请输入内容")
        layout.addWidget(edit)

        # 按键区（网格布局）
        grid_layout = QGridLayout()
        layout.addLayout(grid_layout)

        button_info = {
            0: ["7", "8", "9", "+"],
            1: ["4", "5", "6", "-"],
            2: ["1", "2", "3", "*"],
            3: ["/", "0", ".", "="],
        }
        # 创建一个个按键（根据button_info），并将其添加到网格布局中
        for row_num, line_data in button_info.items():
            for col_num, sign in enumerate(line_data):
                btn = QPushButton(sign)
                grid_layout.addWidget(btn, row_num, col_num)


if __name__ == '__main__':
    app = QApplication(argv)

    w = MyWindow()
    w.show()

    app.exec()

```

效果：

![网格布局](D:\destop\笔记\notes\python笔记\pyqt5笔记\图片\网格布局.png)

主要步骤：

- 创建一个主要布局：垂直布局。
  - 包含两个控件：输入/显示框 和 键盘区
- 键盘区采用网格布局，键盘区本身就是一个布局，因此主要布局要`addLayout()`，而不是`addWidget()`
  - 向网格布局一次添加按键。

## 抽屉布局

```python
from sys import argv
from PyQt5.QtWidgets import QApplication, QWidget, QPushButton, QStackedLayout, QVBoxLayout, QLabel


class Window1(QWidget):
    """
        一个普通的窗口，包含了一个label
    """
    def __init__(self):
        super().__init__()
        label = QLabel("这是抽屉1")
        label.setParent(self)
        label.setStyleSheet("background-color:green;font-size:25px")


class Window2(QWidget):
    """
        一个普通的窗口，包含了一个label
    """
    def __init__(self):
        super().__init__()
        label = QLabel("这是抽屉2")
        label.setParent(self)
        label.setStyleSheet("background-color:yellow;font-size:25px")


class MyWindow(QWidget):
    def __init__(self):
        super().__init__()
        self.create_stack_layout()
        self.init_ui()

    def create_stack_layout(self):
        # 创建一个抽屉布局
        # 并向抽屉布局中添加了两个自定义控件
        self.stack_layout = QStackedLayout()
        w1 = Window1()
        w2 = Window2()
        self.stack_layout.addWidget(w1)
        self.stack_layout.addWidget(w2)

    def init_ui(self):
        self.setWindowTitle("抽屉布局")
        self.resize(400, 300)

        # 创建一个整体布局
        layout = QVBoxLayout()
        self.setLayout(layout)

        # 创建一个控件，并让其获得抽屉布局（self.create_stack_layout中定义的抽屉布局）
        # 该控件添加到整体布局中
        w = QWidget()
        w.setLayout(self.stack_layout)
        layout.addWidget(w)

        # 创建两个按钮，并将二者添加到整体布局中
        btn1 = QPushButton("按钮1")
        btn2 = QPushButton("按钮2")
        btn1.clicked.connect(self.btn1_clicked)  # 把按下按键1的信号与对应的槽绑定
        btn2.clicked.connect(self.btn2_clicked)  # （信号与槽接下来介绍，就相当于为按钮1添加了回调函数）
        layout.addWidget(btn1)
        layout.addWidget(btn2)

    def btn1_clicked(self):
        # 按钮1按下信号的槽函数，相当于按钮1按下的回调函数
        self.stack_layout.setCurrentIndex(0)  # 设置抽屉布局的索引，通过更改索引，就能切换到不同的”抽屉“（抽屉中的Widget）

    def btn2_clicked(self):
        # 按钮2按下信号的槽函数，相当于按钮1按下的回调函数
        self.stack_layout.setCurrentIndex(1)


if __name__ == '__main__':
    app = QApplication(argv)

    w = MyWindow()
    w.show()

    app.exec()
```

效果：

按下按钮1：

![抽屉1](.\图片\抽屉1.png)

按下按钮2：

![抽屉2](.\图片\抽屉2.png)

主要步骤：

- 创建一个整体布局：垂直布局。
  - 垂直布局中包含三个控件：一个自定义的Widget，两个按钮。
- 自定义的Widget采用的布局就是**抽屉布局**。
  - 抽屉布局中有两个Widget，这两个Widget也是自定义的。
  - 抽屉布局中的两个自定义的Widget都是只包含了一个QLabel的简单对象。
- 按钮1和按钮2被按下时会触发信号（事件），调用槽函数（回调函数）。
  - 在回调函数中调用抽屉布局器的**`setCurrentIndex()`**函数，**完成**。

# 窗口

QWidget

QMainWindow

QDialog

# 信号与槽
