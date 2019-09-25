---
title: 用python从0开始写2048小游戏
date: 2019-09-25 10:54:40
tags:
---



最近想自己写个小游戏练练手，研究了一下也就2048和俄罗斯方块最简单了，所以就拿2048开刀了。

环境是windows10下的python3
研究了一下2048对编程语言的要求，挺低的，就下面2点要求：
1、可以绘制界面，可以绘制图案和显示文字
2、可以捕捉用户输入（上下左右按键，或者对应的滑动）
很多语言都可以满足这两点要求，刚好最近写了几个python脚本，就顺便学习一下python吧。
调研了一下，开发程序一般使用wxPython包，那就它了。
wxPython基础知识学习

第一步，先学习写一个最简单的wxPython应用程序：
```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
import wx

app = wx.App()
frame = wx.Frame(None, -1, "Hello World")
frame.Show()
app.MainLoop()
```
这个程序能看出一个wxPython程序至少要实现这几点
1、导入wx包
2、实例化一个App
3、实例话一个Frame
4、显示这个Frame
5、app进入主循环
这个应用只弹出一个标题为Hello World的空界面，看起来没有任何扩展的功能。

然后扩展一下这个最简单的应用，学习一下wxPython的用法
```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
import wx


class MyFrame(wx.Frame):
    def __init__(self, title):
        super(MyFrame, self).__init__(None, title=title, size=(250, 250))
        self.Bind(wx.EVT_PAINT, self.on_paint)
        self.Bind(wx.EVT_KEY_DOWN, self.on_key)
        self.Centre()
        self.Show()

    def on_paint(self, event):
        dc = wx.PaintDC(self)
        dc.SetBackground(wx.Brush(wx.LIGHT_GREY))
        dc.Clear()
        dc.DrawRoundedRectangle(30, 30, 100, 100, 2)
        dc.DrawLine(25, 150, 190, 150)
        dc.DrawText("hello world", 35, 160)

    def on_key(self, event):
        key_code = event.GetKeyCode()
        if key_code == wx.WXK_UP:
            print("UP")
        elif key_code == wx.WXK_DOWN:
            print("down")
        elif key_code == wx.WXK_LEFT:
            print("left")
        elif key_code == wx.WXK_RIGHT:
            print("right")

class MyApp(wx.App):
    def OnInit(self):
        frame = MyFrame('appStudy')
        frame.Show(True)
        return True


if __name__ == "__main__":
    app = MyApp()
    app.MainLoop()
```
这个应用有一点点样子了，通过继承App和Frame，定义了MyApp和MyFrame，然后就可以在MyFrame里面加点功能进去了。在这个appStudy应用中，做了下面几件事情

绑定了EVT_PAINT事件和on_paint函数，即app一旦触发EVT_PAINT事件，就会调用on_paint函数。而这个事件在初始化界面的时候是会被调用的。所以我们可以把一些绘制图案的功能放在这个函数里面。在on_paint函数中
• 设置背景色为淡灰色
• 画了一个圆角矩形
• 画了一条线
• 写了hello world这几个字
绑定了EVT_KEY_DOWN事件和on_key函数，即有按键事件发生时，就会调用on_key函数。on_key函数中的可以检测到按的是哪个按键

运行这个应用，结果如下
这里写图片描述
Good，绘制界面和捕捉用户输入两个功能都会了，下面就可以开始开发2048这个小游戏了。
绘制游戏背景

第一步，先把游戏背景给画出来，这个简单，就是在on_paint里面
• 设置个背景色
• 画一个大矩形
• 大矩形里面画16个小矩形
如下图：
这里写图片描述
唯一不知道的是矩形颜色怎么设置，查了一下SetBrush函数可以设置填充色，setPen可以设置边框。我们不要边框，直接设置成透明就可以了。Bingo，很容易就画出来了。
```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
import wx


class MyFrame(wx.Frame):
    PANEL_ORIG_POINT = wx.Point(15, 15)

    def __init__(self, title):
        super(MyFrame, self).__init__(None, title=title, size=(500, 550))
        self.Bind(wx.EVT_PAINT, self.on_paint)
        self.Centre()
        self.SetFocus()
        self.Show()

    def on_paint(self, e):
        self.draw_tiles()

    def draw_tiles(self):
        dc = wx.ClientDC(self)
        dc.SetBackground(wx.Brush("#FAF8EF"))
        dc.Clear()
        dc.SetBrush(wx.Brush("#C0B0A0"))
        dc.SetPen(wx.Pen("", 1, wx.TRANSPARENT))
        dc.DrawRoundedRectangle(self.PANEL_ORIG_POINT.x, self.PANEL_ORIG_POINT.y, 450, 450, 5)
        for row in range(4):
            for column in range(4):
                dc.SetBrush(wx.Brush("#CCC0B3"))
                dc.DrawRoundedRectangle(self.PANEL_ORIG_POINT.x + 110 * column + 10,
                                        self.PANEL_ORIG_POINT.y + 110 * row + 10, 100, 100, 5)


class MyApp(wx.App):
    def OnInit(self):
        frame = MyFrame('2048')
        frame.Show(True)
        return True

if __name__ == "__main__":
    app=MyApp()
    app.MainLoop()
```
进一步绘制界面，按键事件处理

背景界面画完之后，就需要考虑实现这几个功能：

处理按键事件
在小方块上写数字，以及根据方块的不同数字刷新不同的颜色。

功能1简单，功能2就写个测试函数，在按下空格键的时候触发吧。
颜色嘛，就写个VALUE_COLOR_DEF的map好了，key就是2、4、8……这些数字，value就是颜色。
看起来也不难，得加一个全局性质的类变量tile_values，用二维数组的方法来记录每个小方块当前的值；然后在空格键的时候调用test_update_tiles函数给每个小方块设置个不同的值，然后刷新一下界面，这一步很容易就写好了。
实现的过程中遇到个小问题，发现EVT_PAINT经常会触发，简单，写个变量 is_inited来控制让背景只画1次。
```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
import wx
class MyFrame(wx.Frame):
    PANEL_ORIG_POINT = wx.Point(15, 15)
    VALUE_COLOR_DEF = {
        0: "#CCC0B3",
        2: "#EEE4DA",
        4: "#EDE0C8",
        8: "#F2B179",
        16: "#F59563",
        32: "#F67C5F",
        64: "#F65E3B",
        128: "#EDCF72",
        256: "#EDCF72",
        512: "#EDCF72",
        1024: "#EDCF72",
        2048: "#EDCF72",
        4096: "#EDCF72",
        8192: "#EDCF72",
        16384: "#EDCF72",
        32768: "#EDCF72"
    }
    tile_values = [[0, 0, 0, 0], [0, 0, 0, 0], [0, 0, 0, 0], [0, 0, 0, 0]]
    is_inited = False

    def __init__(self, title):
        super(MyFrame, self).__init__(None, title=title, size=(500, 550))
        self.Bind(wx.EVT_PAINT, self.on_paint)
        self.Bind(wx.EVT_KEY_DOWN, self.on_key)
        self.Centre()
        self.SetFocus()
        self.Show()

    def on_paint(self, e):
        if not self.is_inited:
            self.draw_tiles()
            self.is_inited = True

    def draw_tiles(self):
        dc = wx.ClientDC(self)
        dc.SetBackground(wx.Brush("#FAF8EF"))
        dc.Clear()
        dc.SetBrush(wx.Brush("#C0B0A0"))
        dc.SetPen(wx.Pen("", 1, wx.TRANSPARENT))
        dc.DrawRoundedRectangle(self.PANEL_ORIG_POINT.x, self.PANEL_ORIG_POINT.y, 450, 450, 5)
        for row in range(4):
            for column in range(4):
                tile_value = self.tile_values[row][column]
                tile_color = self.VALUE_COLOR_DEF[tile_value]
                dc.SetBrush(wx.Brush(tile_color))
                dc.DrawRoundedRectangle(self.PANEL_ORIG_POINT.x + 110 * column + 10,
                                        self.PANEL_ORIG_POINT.y + 110 * row + 10, 100, 100, 5)
                dc.SetTextForeground("#707070")
                text_font = wx.Font(30, wx.SWISS, wx.NORMAL, wx.BOLD, face=u"Roboto")
                dc.SetFont(text_font)
                if tile_value != 0:
                    size = dc.GetTextExtent(str(tile_value))
                    if size[0] > 100:
                        text_font = wx.Font(24, wx.SWISS, wx.NORMAL, wx.BOLD, face=u"Roboto")
                        dc.SetFont(text_font)
                        size = dc.GetTextExtent(str(tile_value))
                    dc.DrawText(str(tile_value), self.PANEL_ORIG_POINT.x + 110 * column + 10 + (100 - size[0]) / 2,
                                self.PANEL_ORIG_POINT.y + 110 * row + 10 + (100 - size[1]) / 2)

    def on_key(self, event):
        key_code = event.GetKeyCode()
        if key_code == wx.WXK_UP:
            print("UP")
        elif key_code == wx.WXK_DOWN:
            print("down")
        elif key_code == wx.WXK_LEFT:
            print("left")
        elif key_code == wx.WXK_RIGHT:
            print("right")
        elif key_code == wx.WXK_SPACE:
            self.test_update_tiles()

    def test_update_tiles(self):
        self.tile_values = [[0, 2, 4, 8], [16, 32, 64, 128], [256, 512, 1024, 2048], [4096, 8192, 16384, 32768]]
        self.draw_tiles()


class MyApp(wx.App):
    def OnInit(self):
        frame = MyFrame('2048')
        frame.Show(True)
        return True


if __name__ == "__main__":
    app = MyApp()
    app.MainLoop()
```
运行一下，按一下空格键，不错，就是我想要的。不好意思的是，这里的颜色是下了个2048的小游戏，玩出了所有颜色之后截屏，然后用画板的拾色器取出来的颜色。
这里写图片描述
游戏算法的实现

到了这里，最重要的来了，需要实现按键之后的算法了。需要做这几件事情：

用户按键之后，要计算出每个小方块中新的数字
在初始化游戏时或者按键处理之后，需要随机生成2或者4的数字，填充到空的方块中。

对于算法1，以按左键为例，我发现每一行都各自独立的，所以针对每一行进行计算就行了。所以我设计出来的算法分两步走：

把相邻需要合并的数字合并
把所有非0的格子向左移动，最后的格子填充0

举例如下：
[2, 2, 4, 4 ] -> [4, 0, 8, 0] -> [4, 8, 0, 0]
如果是按右键，就把这一行逆序之后，再按照向左移动处理，处理完之后再逆序一下就行了，如下：
[2, 2, 4, 4 ] -> [4, 4, 2, 2] -> [8, 0, 4, 0] -> [8, 4, 0, 0] -> [0, 0, 4, 8 ]
如果是按上下键，就把整个tile_values矩阵行列转换一下，然后就可以按照左右按键处理了，处理完之后再行列转换回来
对于功能2的实现，添加一个add_random_tile的函数，在所有空(值为0)的小方块中随机挑一个，然后再随机生成2或者4填充进去就可以了。

功能更复杂了，顺便调整了一下代码结构，于是现在的代码变成了下面这个样子：
```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
import wx
import copy
import random

class MyFrame(wx.Frame):
    PANEL_ORIG_POINT = wx.Point(15, 15)
    VALUE_COLOR_DEF = {
        0: "#CCC0B3",
        2: "#EEE4DA",
        4: "#EDE0C8",
        8: "#F2B179",
        16: "#F59563",
        32: "#F67C5F",
        64: "#F65E3B",
        128: "#EDCF72",
        256: "#EDCF72",
        512: "#EDCF72",
        1024: "#EDCF72",
        2048: "#EDCF72",
        4096: "#EDCF72",
        8192: "#EDCF72",
        16384: "#EDCF72",
        32768: "#EDCF72"
    }
    tile_values = [[0, 0, 0, 0], [0, 0, 0, 0], [0, 0, 0, 0], [0, 0, 0, 0]]
    is_inited = False
    def __init__(self, title):
        super(MyFrame,self).__init__(None, title=title, size=(500, 550))
        self.Bind(wx.EVT_PAINT, self.on_paint)
        self.Bind(wx.EVT_KEY_DOWN, self.on_key)
        self.Centre()
        self.SetFocus()
        self.Show()
    def on_paint(self, e):
        if not self.is_inited:
            self.start_game()
            self.is_inited = True
    def add_random_tile(self):
        empty_tiles = [(row, col) for row in range(len(self.tile_values)) for col in range(len(self.tile_values[0]))
                       if self.tile_values[row][col] == 0]
        if len(empty_tiles) != 0:
            row, col = empty_tiles[random.randint(0, len(empty_tiles) - 1)]
            # value should be 2 or 4
            self.tile_values[row][col] = 2 ** random.randint(1, 2)
            return True
        else:
            return False
    def on_key(self, event):
        key_code = event.GetKeyCode()
        temp_tile_values = copy.deepcopy(self.tile_values)
        if key_code == wx.WXK_UP:
            self.on_key_up()
        elif key_code == wx.WXK_DOWN:
            self.on_key_down()
        elif key_code == wx.WXK_LEFT:
            self.on_key_left()
        elif key_code == wx.WXK_RIGHT:
            self.on_key_right()
        elif key_code == wx.WXK_SPACE:
            self.test_update_tiles()
            return
        self.add_random_tile()
        self.draw_tiles()
    def update_single_row_value(self, row_value, positive):
        num_cols = len(row_value)
        if not positive:
            temp_data = copy.deepcopy(row_value)
            row_value = [temp_data[num_cols - 1 - i] for i in range(num_cols)]
        for i in range(num_cols):
            if row_value[i] == 0:
                continue
            for j in range(i + 1, num_cols):
                if row_value[j] == row_value[i]:
                    row_value[i] *= 2
                    row_value[j] = 0
                    break
                elif row_value[j] > row_value[i]:
                    break
        for i in range(num_cols):
            if row_value[i] != 0:
                continue
            for j in range(i + 1, num_cols):
                if row_value[j] != 0:
                    row_value[i] = row_value[j]
                    row_value[j] = 0
                    break
        if not positive:
            temp_data = copy.deepcopy(row_value)
            row_value = [temp_data[num_cols - 1 - i] for i in range(num_cols)]
        return row_value
    def on_key_up(self):
        temp_tile_values = [[row[i] for row in self.tile_values] for i in range(len(self.tile_values[0]))]
        for row in range(len(self.tile_values)):
            temp_tile_values[row] = self.update_single_row_value(temp_tile_values[row], True)
        self.tile_values = [[row[i] for row in temp_tile_values] for i in range(len(temp_tile_values[0]))]
    def on_key_down(self):
        temp_tile_values = [[row[i] for row in self.tile_values] for i in range(len(self.tile_values[0]))]
        for row in range(len(self.tile_values)):
            temp_tile_values[row] = self.update_single_row_value(temp_tile_values[row], False)
        self.tile_values = [[row[i] for row in temp_tile_values] for i in range(len(temp_tile_values[0]))]
    def on_key_left(self):
        for row in range(len(self.tile_values)):
            self.tile_values[row] = self.update_single_row_value(self.tile_values[row], True)
    def on_key_right(self):
        for row in range(len(self.tile_values)):
            self.tile_values[row] = self.update_single_row_value(self.tile_values[row], False)
    def init_screen(self):
        dc = wx.ClientDC(self)
        dc.SetBackground(wx.Brush("#FAF8EF"))
        dc.Clear()
        dc.SetBrush(wx.Brush("#C0B0A0"))
        dc.SetPen(wx.Pen("", 1, wx.TRANSPARENT))
        dc.DrawRoundedRectangle(self.PANEL_ORIG_POINT.x, self.PANEL_ORIG_POINT.y, 450, 450, 5)
    def draw_tiles(self):
        dc = wx.ClientDC(self)
        dc.SetBackground(wx.Brush("#F0F0E0"))
        dc.SetBrush(wx.Brush("#C0B0A0"))
        dc.SetPen(wx.Pen("", 1, wx.TRANSPARENT))
        for row in range(4):
            for column in range(4):
                tile_value = self.tile_values[row][column]
                tile_color = self.VALUE_COLOR_DEF[tile_value]
                dc.SetBrush(wx.Brush(tile_color))
                dc.DrawRoundedRectangle(self.PANEL_ORIG_POINT.x + 110 * column + 10,
                                        self.PANEL_ORIG_POINT.y + 110 * row + 10, 100, 100, 5)
                dc.SetTextForeground("#707070")
                text_font = wx.Font(30, wx.SWISS, wx.NORMAL, wx.BOLD, face=u"Roboto")
                dc.SetFont(text_font)
                if tile_value != 0:
                    size = dc.GetTextExtent(str(tile_value))
                    if size[0] > 100:
                        text_font = wx.Font(24, wx.SWISS, wx.NORMAL, wx.BOLD, face=u"Roboto")
                        dc.SetFont(text_font)
                        size = dc.GetTextExtent(str(tile_value))
                    dc.DrawText(str(tile_value), self.PANEL_ORIG_POINT.x + 110 * column + 10 + (100 - size[0]) / 2,
                                self.PANEL_ORIG_POINT.y + 110 * row + 10 + (100 - size[1]) / 2)
    def start_game(self):
        self.tile_values = [[0, 0, 0, 0], [0, 0, 0, 0], [0, 0, 0, 0], [0, 0, 0, 0]]
        self.init_screen()
        self.add_random_tile()
        self.add_random_tile()
        self.draw_tiles()
    def test_update_tiles(self):
        self.tile_values = [[0, 2, 4, 8], [16, 32, 64, 128], [256, 512, 1024, 2048], [4096, 8192, 16384, 32768]]
        self.draw_tiles()
class MyApp(wx.App):
    def OnInit(self):
        frame = MyFrame('2048')
        frame.Show(True)
        return True
if __name__ == "__main__":
    app = MyApp()
    app.MainLoop()
```
做到这里，游戏已经可以玩了，好开心^_^。可是游戏还不会判断是否结束。
结束判断

同时满足下面2个条件，就可以判定游戏结束了：

所有格子的值全都是非0
没有任意两个相邻的格子值相同，相邻包括上下左右

于是加一个函数is_game_over来实现这个功能，如果确定已经结束了，那就弹出个提示框来让用户重新开始。两个函数设计如下：
```
def is_game_over(self):
        # exist 0 or there is a neighbour with the same value
        print ("is_game_over")
        num_rows = len(self.tile_values)
        num_cols = len(self.tile_values[0])
        for i in range(num_rows):
            for j in range(num_cols):
                if self.tile_values[i][j] == 0 or \
                        (j < num_cols-1 and self.tile_values[i][j] == self.tile_values[i][j + 1]) or \
                        (i < num_rows-1 and self.tile_values[i][j] == self.tile_values[i + 1][j]):
                    return False
        return True

    def game_over(self):
        if wx.MessageBox(u"游戏结束，是否再来一局？", u"Game Over", wx.YES_NO) == wx.YES:
            self.start_game()
```
增加计分和最高记录功能

没有分数这游戏可不好玩，得把这个加上。分数的计算，就每次把被合并了的数字加到总分上去好了。界面设计成如下：
这里写图片描述
直接画文字也可以，不过我发现个更好的工具：StaticText控件。用这个控件之后，更新分数就只要修改控件内容就可以。这一步要做下面几件事：

初始化界面的时候绘制4个StaticText控件
用一个文件来加下“记录”的分数，开始游戏的时候要读出分数，结束游戏的时候，如果破纪录了要更新文件
在每次按键的时候，要计算当前分数。如果分数有变化，需要更新界面上的“积分”

因为在最上方增加了4个控件，所以整个游戏界面也往下移
增加重新开始功能

对比我手机上下载的2048的游戏，还缺少一个重新开始按钮，那就把这个功能加上吧，于是界面就变成了下面这个样子。
这里写图片描述
按钮使用button控件实现。最终代码变成了：
```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
import wx
import copy
import random

'''
    1. 绘制空界面，即学习编程语言的基本功能
    2. 绘制4×4的方块
    3. 捕捉上下左右按键，测试是否可以更新方块颜色和数字
    4. 初始化界面（random 两个位置，再random两个数字2或者4放入位置），在按键事件中增加算法，更新对应方块的数字和颜色
    5. 增加结束条件
    6. 增加计分和最高记录功能
    7、增加重新开始按钮
'''


class MyFrame(wx.Frame):
    PANEL_ORIG_POINT = wx.Point(15, 100)
    VALUE_COLOR_DEF = {
        0: "#CCC0B3",
        2: "#EEE4DA",
        4: "#EDE0C8",
        8: "#F2B179",
        16: "#F59563",
        32: "#F67C5F",
        64: "#F65E3B",
        128: "#EDCF72",
        256: "#EDCF72",
        512: "#EDCF72",
        1024: "#EDCF72",
        2048: "#EDCF72",
        4096: "#EDCF72",
        8192: "#EDCF72",
        16384: "#EDCF72",
        32768: "#EDCF72"
    }
    tile_values = [[0, 0, 0, 0], [0, 0, 0, 0], [0, 0, 0, 0], [0, 0, 0, 0]]
    is_inited = False
    score = 0
    record = 0

    def __init__(self, title):
        super(MyFrame, self).__init__(None, title=title, size=(500, 600))
        self.init_widgets()
        self.Bind(wx.EVT_PAINT, self.on_paint)
        self.Bind(wx.EVT_KEY_DOWN, self.on_key)
        self.Centre()
        self.SetFocus()
        self.Show()

    def on_paint(self, e):
        if not self.is_inited:
            self.start_game()
            self.is_inited = True

    def add_random_tile(self):
        empty_tiles = [(row, col) for row in range(len(self.tile_values)) for col in range(len(self.tile_values[0]))
                       if self.tile_values[row][col] == 0]
        if len(empty_tiles) != 0:
            row, col = empty_tiles[random.randint(0, len(empty_tiles) - 1)]
            # value should be 2 or 4
            self.tile_values[row][col] = 2 ** random.randint(1, 2)
            return True
        else:
            return False

    def on_key(self, event):
        key_code = event.GetKeyCode()
        temp_tile_values = copy.deepcopy(self.tile_values)
        if key_code == wx.WXK_UP:
            self.on_key_up()
        elif key_code == wx.WXK_DOWN:
            self.on_key_down()
        elif key_code == wx.WXK_LEFT:
            self.on_key_left()
        elif key_code == wx.WXK_RIGHT:
            self.on_key_right()
        elif key_code == wx.WXK_SPACE:
            self.test_update_tiles()
            return
        if temp_tile_values == self.tile_values:
            if self.is_game_over():
                self.game_over()
        else:
            self.draw_score()
            self.add_random_tile()
            self.draw_tiles()

    def update_single_row_value(self, row_value, positive):
        num_cols = len(row_value)
        if not positive:
            temp_data = copy.deepcopy(row_value)
            row_value = [temp_data[num_cols - 1 - i] for i in range(num_cols)]
        for i in range(num_cols):
            if row_value[i] == 0:
                continue
            for j in range(i + 1, num_cols):
                if row_value[j] == row_value[i]:
                    self.score += row_value[j]
                    row_value[i] *= 2
                    row_value[j] = 0
                    break
                elif row_value[j] > row_value[i]:
                    break
        for i in range(num_cols):
            if row_value[i] != 0:
                continue
            for j in range(i + 1, num_cols):
                if row_value[j] != 0:
                    row_value[i] = row_value[j]
                    row_value[j] = 0
                    break
        if not positive:
            temp_data = copy.deepcopy(row_value)
            row_value = [temp_data[num_cols - 1 - i] for i in range(num_cols)]
        return row_value

    def on_key_up(self):
        temp_tile_values = [[row[i] for row in self.tile_values] for i in range(len(self.tile_values[0]))]
        for row in range(len(self.tile_values)):
            temp_tile_values[row] = self.update_single_row_value(temp_tile_values[row], True)
        self.tile_values = [[row[i] for row in temp_tile_values] for i in range(len(temp_tile_values[0]))]

    def on_key_down(self):
        temp_tile_values = [[row[i] for row in self.tile_values] for i in range(len(self.tile_values[0]))]
        for row in range(len(self.tile_values)):
            temp_tile_values[row] = self.update_single_row_value(temp_tile_values[row], False)
        self.tile_values = [[row[i] for row in temp_tile_values] for i in range(len(temp_tile_values[0]))]

    def on_key_left(self):
        for row in range(len(self.tile_values)):
            self.tile_values[row] = self.update_single_row_value(self.tile_values[row], True)

    def on_key_right(self):
        for row in range(len(self.tile_values)):
            self.tile_values[row] = self.update_single_row_value(self.tile_values[row], False)

    def on_btn_restart(self, e):
        self.game_over()

    def init_widgets(self):
        self.label_score_text = wx.StaticText(self, -1, u"积分", (50, 15), (100, 30), wx.ALIGN_CENTER)
        self.label_score_text.Font = wx.Font(24, wx.SWISS, wx.NORMAL, wx.BOLD, faceName=u"Roboto")
        self.label_score_text.SetForegroundColour("#0000A0")
        self.label_score_text.SetBackgroundColour("#FAF8EF")

        self.label_record_text = wx.StaticText(self, -1, u"记录", (200, 15), (100, 30), wx.ALIGN_CENTER)
        self.label_record_text.Font = wx.Font(24, wx.SWISS, wx.NORMAL, wx.BOLD, faceName=u"Roboto")
        self.label_record_text.SetForegroundColour("#0000A0")
        self.label_record_text.SetBackgroundColour("#FAF8EF")

        self.score_text = wx.StaticText(self, -1, "0", (50, 50), (100, 30), wx.ALIGN_CENTER)
        self.score_text.Font = wx.Font(24, wx.SWISS, wx.NORMAL, wx.BOLD, faceName=u"Roboto")
        self.score_text.SetForegroundColour("#0000A0")

        self.record_text = wx.StaticText(self, -1, str(self.record), (200, 50), (100, 30), wx.ALIGN_CENTER)
        self.record_text.Font = wx.Font(24, wx.SWISS, wx.NORMAL, wx.BOLD, faceName=u"Roboto")
        self.record_text.SetForegroundColour("#0000A0")

        self.restart_btn = wx.Button(self, -1, u"重新\n开始", (350, 10), (80, 80), wx.ALIGN_CENTER)
        self.restart_btn.Font = wx.Font(24, wx.SWISS, wx.NORMAL, wx.BOLD, faceName=u"Roboto")
        self.restart_btn.SetForegroundColour("#0000A0")
        self.restart_btn.Bind(wx.EVT_BUTTON, self.on_btn_restart)

    def init_screen(self):
        dc = wx.ClientDC(self)
        dc.SetBackground(wx.Brush("#FAF8EF"))
        dc.Clear()
        dc.SetBrush(wx.Brush("#C0B0A0"))
        dc.SetPen(wx.Pen("", 1, wx.TRANSPARENT))
        dc.DrawRoundedRectangle(self.PANEL_ORIG_POINT.x, self.PANEL_ORIG_POINT.y, 450, 450, 5)

        self.score_text.SetLabel("0")
        self.record_text.SetLabel(str(self.record))

    def draw_score(self):
        self.score_text.SetLabel(str(self.score))

    def draw_tiles(self):
        dc = wx.ClientDC(self)
        dc.SetBackground(wx.Brush("#F0F0E0"))
        dc.SetBrush(wx.Brush("#C0B0A0"))
        dc.SetPen(wx.Pen("", 1, wx.TRANSPARENT))
        for row in range(4):
            for column in range(4):
                tile_value = self.tile_values[row][column]
                tile_color = self.VALUE_COLOR_DEF[tile_value]
                dc.SetBrush(wx.Brush(tile_color))
                dc.DrawRoundedRectangle(self.PANEL_ORIG_POINT.x + 110 * column + 10,
                                        self.PANEL_ORIG_POINT.y + 110 * row + 10, 100, 100, 5)
                dc.SetTextForeground("#707070")
                text_font = wx.Font(30, wx.SWISS, wx.NORMAL, wx.BOLD, faceName=u"Roboto")
                dc.SetFont(text_font)
                if tile_value != 0:
                    size = dc.GetTextExtent(str(tile_value))
                    if size[0] > 100:
                        text_font = wx.Font(24, wx.SWISS, wx.NORMAL, wx.BOLD, faceName=u"Roboto")
                        dc.SetFont(text_font)
                        size = dc.GetTextExtent(str(tile_value))
                    dc.DrawText(str(tile_value), self.PANEL_ORIG_POINT.x + 110 * column + 10 + (100 - size[0]) / 2,
                                self.PANEL_ORIG_POINT.y + 110 * row + 10 + (100 - size[1]) / 2)

    def start_game(self):
        self.tile_values = [[0, 0, 0, 0], [0, 0, 0, 0], [0, 0, 0, 0], [0, 0, 0, 0]]
        self.score = 0
        try:
            with open("record.txt") as fp:
                self.record = int(fp.read())
                # print("read record: %d" % self.record)
        except (IOError, ValueError) as err:
            print("read record error: %s" % err)
            self.record = 0

        self.init_screen()
        self.add_random_tile()
        self.add_random_tile()
        self.draw_tiles()

    def is_game_over(self):
        # exist 0 or there is a neighbour with the same value
        # print ("is_game_over")
        num_rows = len(self.tile_values)
        num_cols = len(self.tile_values[0])
        for i in range(num_rows):
            for j in range(num_cols):
                if self.tile_values[i][j] == 0 or \
                        (j < num_cols - 1 and self.tile_values[i][j] == self.tile_values[i][j + 1]) or \
                        (i < num_rows - 1 and self.tile_values[i][j] == self.tile_values[i + 1][j]):
                    return False
        return True

    def game_over(self):
        if self.score > self.record:
            self.record = self.score
            try:
                with open("record.txt", "w") as fp:
                    fp.write(str(self.score))
            except IOError as err:
                print(err)
        if wx.MessageBox(u"游戏结束，是否再来一局？", u"Game Over", wx.YES_NO) == wx.YES:
            self.start_game()

    def test_update_tiles(self):
        self.tile_values = [[0, 2, 4, 8], [16, 32, 64, 128], [256, 512, 1024, 2048], [4096, 8192, 16384, 32768]]
        self.draw_tiles()


class MyApp(wx.App):
    def OnInit(self):
        frame = MyFrame('2048')
        frame.Show(True)
        return True


if __name__ == "__main__":
    app = MyApp()
    app.MainLoop()
```
去掉注释和空行，一共还不到200行，原来做个游戏这么简单啊！
