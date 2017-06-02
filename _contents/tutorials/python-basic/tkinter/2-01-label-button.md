---
youku_id: XMTYwNTc3OTE3Mg
youtube_id: vMAK1oJAtkQ
description: 
chapter: 2
title: Label & Button 标签和按钮
date: 2016-11-3
---
* 学习资料:
  * [相关代码](https://github.com/MorvanZhou/tutorials/blob/master/tkinterTUT/tk2_label_button.py)


#### 窗口主体框架

每一个 tkinter 应用的主体框架都可以包含下面这部分. 定义 `window` 窗口 和 `window`的一些属性, 然后书写窗口内容, 最后执行`window.mainloop`让窗口活起来.

```python
import tkinter as tk

window = tk.Tk()
window.title('my window')
window.geometry('200x100')

# 这里是窗口的内容

window.mainloop()
```

#### 窗口内容

这次我们会建立一个用来描述的标签 `tk.Label`, 比如:

```python
l = tk.Label(window, 
    text='OMG! this is TK!',    # 标签的文字
    bg='green',     # 背景颜色
    font=('Arial', 12),     # 字体和字体大小
    width=15, height=2  # 标签长宽
    )
l.pack()    # 固定窗口位置
```

<img class="course-image" src="/static/results/tkinter/2-01-01.png">

我们也可以通过变量的形式控制标签的显示, 这时我们引入按钮 `tk.Button` 的概念, 没点一次按钮, 标签变化一次. 用一下内容替换上面的标签. 并把需要变化的文字存成变量 `var`:

```python
var = tk.StringVar()    # 这时文字变量储存器
l = tk.Label(window, 
    textvariable=var,   # 使用 textvariable 替换 text, 因为这个可以变化
    bg='green', font=('Arial', 12), width=15, height=2)
l.pack() 
```

接着我们来做 按钮 `tk.Button`: 

```python
b = tk.Button(window, 
    text='hit me',      # 显示在按钮上的文字
    width=15, height=2, 
    command=hit_me)     # 点击按钮式执行的命令
b.pack()    # 按钮位置
```

那么点击是的命令我们用 `if` `else` 语句来判断. 用 `on_hit` 来判断当前状态.

```python
on_hit = False  # 默认初始状态为 False
def hit_me():
    global on_hit
    if on_hit == False:     # 从 False 状态变成 True 状态
        on_hit = True
        var.set('you hit me')   # 设置标签的文字为 'you hit me'
    else:       # 从 True 状态变成 False 状态
        on_hit = False
        var.set('') # 设置 文字为空
```

没有点击时: 

<img class="course-image" src="/static/results/tkinter/2-01-02.png">

点击第一次:

<img class="course-image" src="/static/results/tkinter/2-01-03.png">

点击第二次:

<img class="course-image" src="/static/results/tkinter/2-01-02.png">




