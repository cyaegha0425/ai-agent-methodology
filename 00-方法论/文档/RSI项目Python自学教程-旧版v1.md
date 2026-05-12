# RSI项目Python自学教程

**教程说明**：这是一个完全基于真实项目的Python入门教程。你会看到真实的代码、真实的报错、真实的思考过程。每一行代码都来自实际项目，不是老师编出来教学用的。

**适用读者**：会操作电脑、会用鼠标键盘，但对编程一窍不通的产品经理。Python代码对你来说就像天书，但你想知道它到底在干嘛——或者你想自己动手改一改。

**项目背景**：这是一个RSI（星际公民）游戏飞船的自动抢购工具。原理很简单——定好时间，脚本自动帮你点网页、加购物车、付款。但实现起来涉及浏览器自动化、API调用、图形界面等好几个技术领域。

**项目路径**：`/root/Projects/RSI_MIEKCIK_BUY/`

---


> 📖 **这一章为零基础读者准备，先把后面会用到的专业术语用大白话讲清楚。**

# 第0章 电脑基础与术语扫盲

> **本章说明**：这一章专门为零基础读者准备。后面章节会提到一些"听起来很专业"的术语（命令行、路径、环境变量、URL、API等），本章先用大白话讲清楚。你不需要记住所有细节，只需要"听过、知道大概是什么"。遇到不懂的术语，随时回来翻一翻。

### 0.1 命令行是什么

你平时用电脑：打开文件夹 → 双击文件 → 点点鼠标操作。

**命令行**（Command Line）是一种"用文字命令操作电脑"的方式。弹出来的是一个黑色窗口，里面只能输入文字，不能用鼠标。

**Windows上打开命令行有几种方式**：

1. **开始菜单搜索**：点击Windows开始菜单 → 搜索 `cmd` 或 `PowerShell` → 回车
2. **右键开始菜单**：右键点击开始菜单 → 选择"终端"或"PowerShell"
3. **快捷键**：按 `Win + X` 弹出快捷菜单 → 选择"终端"

打开后，你会看到类似这样的界面：


```bash
PS C:\Users\你的用户名>
```


`>` 前面是你的当前位置（当前文件夹）。你可以在后面输入命令。

> 💡 **生活比喻**：命令行就像微信对话框，只不过"对方"是你的电脑。你输入文字命令，电脑执行并返回结果。

### 0.2 文件夹路径是什么

**路径**（Path）就是"文件或文件夹在电脑里的地址"。


```bash
C:\Users\张三\Documents\项目\RSI_MIEKCIK_BUY
```


这段文字表示：从C盘 → 用户文件夹 → 张三文件夹 → 文档文件夹 → 项目文件夹 → RSI项目

路径有两种形式：

| 类型 | 例子 | 说明 |
|------|------|------|
| **绝对路径** | `C:\Users\张三\Desktop\test.py` | 从盘符开始的完整地址，无论在哪里都能找到 |
| **相对路径** | `./modules/config.py` | 相对于"当前位置"的地址，`./` 表示当前文件夹 |

**Windows路径 vs Linux路径**：
- Windows 用反斜杠 `\`，如 `C:\Users\张三`
- Linux/Mac 用正斜杠 `/`，如 `/home/zhangsan`

Python代码里通常用 `/`（正斜杠），Python会自动处理兼容问题。

### 0.3 什么是环境变量

当你打开命令行，输入 `python`，电脑怎么知道 `python` 在哪里？

**环境变量**（Environment Variable）是操作系统给程序提供的一些"全局配置信息"。

其中最重要的一个叫 **PATH**（路径环境变量）。它记录了"去哪里找可执行程序"。

举个例子，你的电脑里安装了Python，路径是 `D:\Python\python.exe`。把这个路径加到 PATH 里，之后在任何位置输入 `python`，系统就知道去 `D:\Python` 找这个程序了。

**为什么安装Python要勾选"Add to PATH"？**
- 勾选了 → 命令行里输入 `python` 就能用
- 没勾选 → 必须输入完整路径 `D:\Python\python.exe` 才能用

> 💡 **PM桥梁**：环境变量就像"公共通讯录"。程序需要找某些东西时，会先去通讯录里查地址。PATH 就是告诉系统"去哪里找可执行程序"的通讯录。

### 0.4 URL是什么

**URL**（Uniform Resource Locator）就是"网页地址"。


```bash
https://robertsspaceindustries.com/en/store/pledge
```


分解一下：
- `https://` — 使用什么协议（安全版HTTP）
- `robertsspaceindustries.com` — 服务器地址（域名）
- `/en/store/pledge` — 服务器上的具体路径

> 💡 **生活比喻**：URL就像"详细收货地址"。域名是"小区名称"，路径是"楼号-单元-门牌号"。

### 0.5 HTTP是什么

**HTTP**（HyperText Transfer Protocol）是"超文本传输协议"。简单说：它是客户端（浏览器/你的程序）和服务器（网站后台）之间通信的规则。

常见的HTTP请求：

| 请求类型 | 含义 | 例子 |
|----------|------|------|
| **GET** | 获取数据 | 打开网页、查商品信息 |
| **POST** | 提交数据 | 登录、提交表单、创建订单 |

当你访问一个网页时：
1. 你的浏览器发送 GET 请求给服务器
2. 服务器返回网页内容（HTML）
3. 浏览器渲染显示

### 0.6 API是什么

**API**（Application Programming Interface）是"应用程序接口"。

听起来很抽象，但其实很好理解：

> **API = 接口文档 + 调用方式**

就像你和开发对需求时，你会说"帮我对接一下XX系统的接口"。开发会去读接口文档，然后按文档规定的方式去调用对方的功能。

**生活中的API例子**：
- 天气API：你传城市名，返回天气数据
- 支付API：你传金额，返回支付链接
- 地图API：你传坐标，返回位置信息

> 💡 **PM桥梁**：你理解"对接第三方SDK"吗？API就是那个需要对接的东西的说明书+通道。

### 0.7 本章小结

这一章讲的都是"听起来专业但其实很简单"的概念：

| 术语 | 一句话理解 |
|------|-----------|
| 命令行 | 用文字命令操作电脑的黑窗口 |
| 路径 | 文件在电脑里的地址 |
| 环境变量/PATH | 系统级别的"通讯录"，告诉程序去哪找东西 |
| URL | 网页地址 |
| HTTP | 浏览器和服务器通信的规则 |
| API | 接口文档+调用方式，别人提供功能的入口 |

现在你已经具备了阅读后续章节的"基础词汇量"。遇到不懂的术语，随时回来查！

---

# 第一部分 · 准备出发


> 📖 **学会搭建Python开发环境，这是运行任何Python代码的前提。**

## 第1章 从零搭建开发环境

### 1.1 场景引入

你拿到了一份Python代码，想跑起来看看效果。双击 `.py` 文件——弹出一个黑色窗口，一闪而过，什么都没发生。

（内心OS：说好的运行程序呢？？？）

这不是程序坏了，而是Python解释器没安装。就像你买了个日本电器但家里插座是国标的一样，`.py` 文件需要"翻译"才能被电脑执行，这个翻译官就是Python解释器。

### 1.2 概念讲解

**Python** 是一种编程语言，写出来的代码是给人看的文本。但电脑只懂0和1，所以需要**解释器**（Interpreter）把Python代码翻译成电脑能懂的指令。

你可以把Python解释器理解为：
- 程序员：写Python代码（人类的语言）
- 翻译官：Python解释器（翻译成人和电脑都能懂的中间语言）
- 监工：操作系统（调度一切）

**pip** 是Python的包管理器（Package Manager）。你可以理解为App Store——你需要什么功能，就用pip去"下载安装"别人写好的代码包。

### 1.3 安装步骤

**第一步：下载Python**

打开浏览器，访问 https://www.python.org/downloads/

下载最新的Python 3.x版本（教程用的是3.10+，但3.8以上基本都兼容）。

安装时注意：
- 勾选 **Add Python to PATH**（这是坑最多的一步，没勾选的话命令行找不到python）
- 点击 Customze Installation 可以改安装路径，建议装在D盘或E盘，C盘容易权限问题

**第二步：验证安装**

按 Win+R，输入 `cmd`，回车。弹出黑色命令行窗口，输入：


```bash
python --version
pip --version
```


如果看到类似 `Python 3.12.0` 和 `pip 24.0 from ...` 的字样，恭喜你，安装成功！

如果提示"不是内部命令"，大概率是第一步没勾Add to PATH。解决方法：重装Python，或者手动配置环境变量。

**第三步：安装第一个包**

我们来装一个叫 `requests` 的包，它能让Python发送HTTP请求（就是模拟浏览器访问网页）。


```bash
pip install requests
```


看到 `Successfully installed requests` 就说明装好了。

### 1.4 项目环境准备

回到我们的RSI项目。根据项目文档，需要安装的依赖有：


```bash
pip install playwright
python -m playwright install chromium
```


`playwright` 是浏览器自动化框架，可以理解为"让Python帮你控制浏览器"。`chromium` 是它支持的浏览器之一（Chrome的开源兄弟）。

> 💡 **PM桥梁**：你跟开发说"给我做个网页自动化脚本"，开发第一反应就是用什么框架。Selenium、Playwright、Puppeteer这些都是干这事的。就像你说"给我找个靠谱的配送员"，骑手可以用美团、饿了么、达达一样。

### 1.5 动手试一下

1. 打开命令行，输入 `python`，回车。你会看到 `>>>` 提示符——这是Python的交互模式
2. 输入 `print("Hello World")`，回车。看到了吗？"Hello World"被打印出来了
3. 输入 `exit()` 退出

恭喜你，你刚才写的第一行Python代码成功了！

### 1.6 常见问题

**Q：pip install 很慢怎么办？**

国内访问国外服务器慢，可以换国内镜像源：

```bash
pip install requests -i https://pypi.tuna.tsinghua.edu.cn/simple
```


**Q：装了多个Python版本冲突了？**

可以用 `py -3` 或 `python3` 指定版本，或者用虚拟环境（下一节讲）。

---


> 📖 **选择一个好用的IDE，让写代码变成一种享受。**

## 第2章 IDE工具链

### 2.1 场景引入

你用记事本写了几行Python代码：

```python
name = "咩咩"
print("Hello" + name)
```


保存成 `test.py`，双击运行。黑色窗口一闪而过，你都不知道有没有报错。

这是因为记事本没有代码高亮、没有错误提示、没有代码补全——它只是个"写字板"，不是"写代码的工具"。

你需要**IDE**（Integrated Development Environment，集成开发环境）。

### 2.2 概念讲解

**IDE** 是专门用来写代码的软件。就像Photoshop是处理图片的专业工具，IDE是处理代码的专业工具。

好的IDE能做到：
- 代码高亮：用不同颜色显示关键字、变量、字符串，一眼看出代码结构
- 语法提示：敲几个字母弹出完整单词，减少拼写错误
- 错误提示：写完就告诉你哪里可能有错，不用等运行
- 调试功能：让代码"暂停"在某个位置，看看这时候变量是多少
- 终端集成：写代码的旁边就能运行程序

**VS Code**（Visual Studio Code）是微软出的免费IDE，轻量、插件丰富、对Python支持好。新手友好度极高。

### 2.3 安装配置VS Code

**第一步：下载安装**

访问 https://code.visualstudio.com/ ，点击大绿色Download按钮。

安装过程没什么坑，一路Next就行。

**第二步：安装Python插件**

打开VS Code，按 Ctrl+Shift+X 打开扩展面板，搜索 "Python"，找到Microsoft出品的那个，点击Install。

**第三步：打开项目文件夹**

VS Code打开后，点击"Open Folder"，找到RSI项目所在的文件夹。

左边会出现文件列表，像这样：


```bash
📁 RSI_MIEKCIK_BUY/
    📄 RSI_MIEKCIK_BUY.py  # 主入口文件
    📁 modules/
        📄 config.py
        📄 main.py
        ...
```


**第四步：认识界面**

- 左边：文件浏览器（Explorer）
- 中间：代码编辑器（写代码的地方）
- 右边：有时候会弹出一些辅助面板
- 底部：终端面板（Terminal，输出程序运行结果）

### 2.4 运行代码

打开 `modules/config.py`，你会看到代码。

按 **F5** 或者点击右上角的绿色三角形 ▶️ 按钮，VS Code会问你用什么配置运行。选"Python File"就行。

底部终端会显示程序输出。如果有报错，VS Code会用红色波浪线标出错误位置，鼠标悬停能看到错误信息。

### 2.5 调试入门

调试是编程最重要的技能之一。简单说就是"让程序停下来，看看这时候发生了什么"。

**打断点**：在代码行号左边点一下，会出现一个红点。这就是断点——程序运行到这里会暂停。

**单步执行**：按 F10 可以"走一步"，看这一步执行完后变量变成了什么。

**查看变量**：程序暂停时，把鼠标悬停在变量上，或者按 Ctrl+Shift+D 打开调试面板，能看到所有变量的当前值。

> 💡 **PM桥梁**：调试就像你设计的产品出问题了，你去找开发对质。开发说"来来来，我给你复现一遍"，然后一步步操作给你看——这就是调试。区别是开发对程序做这个动作叫"单步调试"。

### 2.6 动手试一下

1. 用VS Code打开RSI项目
2. 打开 `modules/config.py`
3. 在文件中搜索关键词 `CFG = {` 定位到配置定义处
4. 在 `print("Hello")` 下面（没有就自己加一行）输入 `print(CFG["TARGET_TIME"])`

5. 按 F5 运行

你应该在终端看到类似 `2026-05-15 00:00:00` 的输出——这是从配置文件里读取的目标时间。

---


> 📖 **从整体上了解项目的结构，知道各个文件是干什么的。**

## 第3章 项目全景图

### 3.1 场景引入

你打开RSI项目文件夹，看到一堆 `.py` 文件：


```bash
RSI_MIEKCIK_BUY.py    # 主入口
modules/
    __init__.py
    config.py         # 配置
    main.py           # 主逻辑
    gui_config.py     # 配置界面
    gui_progress.py   # 进度界面
    browser.py        # 浏览器操作
    api_client.py     # API调用
```


作为PM，你可能会问：为什么要拆这么多文件？能不能写一个超级长的文件搞定所有功能？

答案是：可以，但不推荐。就像你的产品文档可以写100页不分章节，但谁看谁疯。

### 3.2 架构图解

用大白话说，这个项目的流程是这样的：


```bash
用户看到配置界面（gui_config.py）
    ↓ 填写：时间、关键词、是否测试模式
用户点"开始抢购"
    ↓
程序打开浏览器，登录RSI（browser.py）
    ↓
到点自动刷新页面（main.py控制）
    ↓
在列表页找到目标商品（api_client.py）
    ↓
点击"加入购物车"（browser.py + api_client.py）
    ↓
自动填信用点、点继续、确认付款
    ↓
进度界面（gui_progress.py）实时显示：登录✓ 预热✓ 加购✓ 付款✓
    ↓
弹出结果：🎉成功！or ⚠️请检查
```


### 3.3 模块关系

用PM能理解的话说：

| 模块 | 类比 | 职责 |
|------|------|------|
| config.py | 产品配置文件 | 存放所有固定参数：目标时间、搜索关键词、API地址 |
| gui_config.py | 设置页面 | 用户输入配置的地方：选日期、输关键词、点开始 |
| gui_progress.py | 订单跟踪页 | 实时显示抢购进度：现在到哪一步了 |
| browser.py | 自动化操作员 | 控制浏览器：打开网页、点击按钮、截图 |
| api_client.py | 后端API调用 | 直接和RSI服务器通信：查购物车、扣款 |
| main.py | 产品经理 | 协调所有模块：按什么顺序做什么事 |

### 3.4 数据流向


```bash
用户输入 → gui_config.py → config.py（保存配置）
                                      ↓
                              main.py（读取配置，启动流程）
                                      ↓
        ┌─────────────────────────────┼─────────────────────────────┐
        ↓                             ↓                             ↓
   browser.py                    api_client.py               gui_progress.py
   (控制浏览器)                  (调用API)                    (更新界面)
        ↓                             ↓                             ↓
   页面操作：                    GraphQL请求：                实时显示：
   打开页面、点击按钮            查购物车、扣款                登录✓、预热✓...
        ↓                             ↓                             ↓
        └─────────────────────────────┼─────────────────────────────┘
                                      ↓
                              main.py（汇总结果）
                                      ↓
                              gui_progress.py（显示成功/失败）
```


### 3.5 代码入口

看 `RSI_MIEKCIK_BUY.py`（这是项目的主入口文件）：


```python
# 来自 RSI_MIEKCIK_BUY/RSI_MIEKCIK_BUY.py
from modules.main import run, test, help
from modules.config import get_args
```


这就是Python的 **import** 语法——从 `modules` 文件夹里，把需要的模块"拿进来"用。

> 💡 **PM桥梁**：import 就像你在写PRD时引用别人的文档。"详见竞品分析章节"、"参考埋点规范"——import干的是同样的事：把别人写好的代码拿过来用。

### 3.6 动手试一下

1. 用VS Code打开项目
2. 打开 `modules/__init__.py`

你会看到：


```python
# 来自 RSI_MIEKCIK_BUY/modules/__init__.py
from . import config
from . import api_client
from . import browser
from . import gui_config
from . import gui_progress
from . import main
from . import sku_interceptor
from . import calibration
from .latency_test import show_latency_dialog

__all__ = ['config', 'api_client', 'browser', 'gui_config', 'gui_progress', 'main', 'sku_interceptor', 'calibration']
```


这是Python模块的入口文件。`*` 表示"导出所有模块给别人用"。你不需要完全理解这在干什么，只要知道：`from modules import config` 这种写法能找到 `config.py`，就是因为有 `__init__.py`。

---

# 第二部分 · 语言基础


> 📖 **学习Python的基本语法，包括变量、数据类型、字符串和运算符。**

## 第4章 基础语法（上）

### 4.1 场景引入

打开 `modules/config.py`，你看到很多这样的代码：


```python
# 来自 RSI_MIEKCIK_BUY/modules/config.py 第50-65行
CFG = {
    "TARGET_TIME": "2026-05-15 00:00:00",
    "TIMEZONE": "Asia/Shanghai",
    "TIME_OFFSET": -0.2,
    "BROWSE_URL_PREFIX": "https://robertsspaceindustries.com/en/store/pledge/browse/extras/standalone-ships?page=1&sortField=price&sortDir=desc&keywords=",
    "SEARCH_KEYWORDS": "Kraken",
    "TEST_MODE": False,
    "UA": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36...",
}
```


这些代码在说什么？我们来拆解。

### 4.2 变量

**变量**（Variable）是什么？你可以理解为"给数据贴个标签"。


```python
name = "咩咩"      # 把字符串"咩咩"贴上name的标签
age = 18          # 把数字18贴上age的标签
is_member = True  # 把布尔值True贴上is_member的标签
```


等号 `=` 在编程里不表示"等于"，而是"赋值"——把右边的值塞进左边的变量里。

在 `config.py` 里：


```python
GUI_BG_COLOR = "#A8B4D4"  # 浅蓝灰白色
GUI_TITLE_COLOR = "#1a3a5c"  # 深蓝色
```


这就是把颜色值存起来方便复用。就像你定义了一套"品牌色"，到处用这个变量而不是写 `#A8B4D4` 十几次。

### 4.3 数据类型

Python里有几种常见的数据类型：

**字符串（str）**：用引号包起来的文本

```python
"Hello"
'World'
"""多行文本"""
```


**数字（int/float）**：

```python
18        # 整数（int）
3.14      # 小数（float）
```


**布尔值（bool）**：

```python
True      # 真
False     # 假
```


在 `config.py` 里你能看到这些类型的实际使用：


```python
"TEST_MODE": False          # 布尔值
"TIME_OFFSET": -0.2          # 数字（小数）
"SEARCH_KEYWORDS": "Kraken"  # 字符串
```


### 4.4 字符串操作

**字符串拼接**：用 `+` 把字符串连起来


```python
name = "咩咩"
print("Hello " + name)  # 输出: Hello 咩咩
```


**f-string（格式化字符串）**：在字符串里直接插入变量


```python
name = "咩咩"
target_time = "2026-05-15 00:00:00"
print(f"目标时间: {target_time}, 抢购者: {name}")
# 输出: 目标时间: 2026-05-15 00:00:00, 抢购者: 咩咩
```


f-string是Python 3.6+的特性，写起来比 `+` 拼接清晰很多。

在 `config.py` 的 `notify` 函数里就有这样的用法：


```python
# 来自 RSI_MIEKCIK_BUY/modules/config.py 约第120行
# 简化版示例
f'显示通知 "{title}" 内容 "{msg}"'
```


### 4.5 运算符

Python的数学运算符和数学一样：`+ - * /`，还有两个特别的：


```python
//   # 整除（取整数部分）
%    # 取余（除法剩下的）
**   # 幂（几次方）
```


看 `gui_progress.py` 里的倒计时计算：


```python
# 来自 RSI_MIEKCIK_BUY/modules/gui_progress.py 约第270行
hours = int(diff // 3600)           # 多少小时
mins = int((diff % 3600) // 60)     # 多少分钟
secs = int(diff % 60)              # 多少秒
```


逻辑运算符：`and or not`


```python
if is_warbond and is_limited:
    # 两个条件都满足才执行
    pass

if not is_out_of_stock:
    # 不缺货才执行
    pass
```


### 4.6 PM桥梁

> 你设计了一个筛选功能：只显示"有库存"且"是Warbond"的商品。
> 
> 开发说可以用一句话表示：`is_in_stock and is_warbond`
> 
> 你：哦，这不就是"且"的关系吗？我懂了！

这就是产品经理理解代码的思路——不需要会写，但要知道代码在表达什么逻辑。

### 4.7 动手试一下

1. 在VS Code里新建一个文件 `test_variables.py`
2. 写以下代码：


```python
# 测试变量和类型
name = "我的RSI脚本"
target_date = "2026-05-15"
target_hour = 0
target_minute = 0
test_mode = False

# f-string格式化
print(f"抢购日期: {target_date}")
print(f"抢购时间: {target_hour}:{target_minute:02d}")  # :02d表示至少2位，不够补0
print(f"测试模式: {'开启' if test_mode else '关闭'}")

# 简单计算
seconds_until = 60 * 30  # 30分钟有多少秒
print(f"距离抢购还有 {seconds_until} 秒")
```


3. 运行看看结果

---


> 📖 **继续学习Python的容器类型：列表和字典，以及条件判断和循环。**

## 第5章 基础语法（下）

### 5.1 场景引入

你在 `config.py` 里看到这样的代码：


```python
# 来自 RSI_MIEKCIK_BUY/modules/config.py 约第35行
CFG = {
    "TARGET_TIME": "2026-05-15 00:00:00",
    "TIME_OFFSET": -0.2,
    # ...
}
```


然后又在 `browser.py` 里看到：


```python
# 来自 RSI_MIEKCIK_BUY/modules/browser.py 约第90行
px = proxy()
if px:
    log.info(f"📡 代理: {px}")
```


这里 `CFG` 外面包着大括号 `{}`，这就是**字典**（Dictionary）。让我们来认识几种重要的"容器"类型。

### 5.2 列表（List）

**列表** 是什么？就像一个购物清单，按顺序排列的东西。


```python
# 定义列表
steps = ["登录", "预热", "加购", "付款"]
numbers = [1, 2, 3, 4, 5]

# 访问元素（下标从0开始！）
print(steps[0])   # 输出: 登录
print(steps[-1])   # 输出: 付款（倒数第一个）

# 添加元素
steps.append("完成")

# 遍历
for step in steps:
    print(f"步骤: {step}")
```


### 5.3 字典（Dictionary）

**字典** 是什么？就像真实世界的字典——用"键"（key）查"值"（value）。


```python
# 定义字典（用大括号）
product = {
    "name": "Kraken",
    "price": 675.00,
    "in_stock": True,
    "is_warbond": False
}

# 访问值
print(product["name"])      # 输出: Kraken
print(product.get("price")) # 输出: 675.0

# 修改值
product["price"] = 700.00

# 添加新键值对
product["quantity"] = 1
```


> 💡 **生活比喻**：想象一个通讯录。字典的key是姓名，value是电话号码。你说"帮我在通讯录里查一下张三"，这就是`contacts["张三"]`的意思。

在 `config.py` 里，`CFG` 就是一个大字典，存着所有配置：


```python
CFG = {
    "TARGET_TIME": "2026-05-15 00:00:00",  # 目标时间
    "SEARCH_KEYWORDS": "Kraken",          # 搜索关键词
    "TEST_MODE": False,                    # 测试模式
    # ... 更多配置
}
```


### 5.4 条件判断

**if/else** 语句让程序做选择：


```python
is_logged_in = True

if is_logged_in:
    print("已登录，跳到商品页")
else:
    print("未登录，先去登录")
```


**elif** 处理多种情况：


```python
stock_count = 5

if stock_count == 0:
    print("缺货")
elif stock_count < 10:
    print("库存紧张")
elif stock_count < 100:
    print("有货")
else:
    print("货源充足")
```


**多条件**：


```python
is_warbond = False
is_out_of_stock = False

# 且关系：两个都要满足
if not is_warbond and not is_out_of_stock:
    print("这是普通版且有货，可以抢！")
```


在 `api_client.py` 里就有类似的判断逻辑。这里需要特别说明：实际代码中，这段逻辑是用JavaScript写在 `page.evaluate()` 里的，而不是纯Python。Playwright通过 `page.evaluate()` 在浏览器环境中执行JavaScript代码。


```python
# 来自 RSI_MIEKCIK_BUY/modules/api_client.py 约第900行
# ⚠️ 注意：这是嵌入在Python字符串里的 JavaScript 代码！
result = self.page.evaluate("""
    () => {
        // 省略部分代码...
        
        // 判断条件：无Warbond标签 且 可加购
        // ⚠️ 以下是 JavaScript 代码，不是 Python！
        if (!hasWarbond && !isOutOfStock) {
            targetCard = card;
            targetPrice = price;
        }
        
        return { /* 返回结果 */ };
    }
""")
```


这就是Python调用JavaScript的典型方式——把JS代码作为字符串传给 `page.evaluate()`，Playwright会在浏览器环境中执行它。

### 5.5 循环

**for循环** 遍历列表或字典：


```python
# 遍历列表
products = ["Kraken", "Apollo", "Cutlass"]
for product in products:
    print(product)

# 遍历字典
info = {"name": "Kraken", "price": 675}
for key in info:
    print(f"{key}: {info[key]}")
```


**while循环** 满足条件就一直执行：


```python
# 来自 main.py 约第480行（简化示例）
# 实际代码中会使用 server_offset 来校准服务器时间
server_offset = 0  # 时间偏移量，用于校准本地与服务器的时间差
while time.time() - server_offset < target:
    # 检查是否点击了取消
    if gui and gui.is_cancel_clicked():
        print("用户取消")
        break
```


> 💡 说明：实际项目里 `server_offset` 会通过自动校准或手动设置来获取，这里是简化后的示例。

### 5.6 json文件结构

项目里的 `sku_bookmarks.json` 就是典型的JSON格式：


```json
# 来自 RSI_MIEKCIK_BUY/sku_bookmarks.example.json
{
  "version": "1.0",
  "updated_at": "2024-05-02T12:00:00",
  "skus": [
    {
      "sku_id": "SHIP-EXAMPLE-001",
      "name": "示例飞船 - RSI Apollo",
      "price": 175.00,
      "in_stock": true,
      "is_warbond": false
    },
    {
      "sku_id": "SHIP-EXAMPLE-002", 
      "name": "示例飞船 - Drake Cutlass Black",
      "price": 110.00,
      "in_stock": true,
      "is_warbond": false
    }
  ]
}
```


JSON 就是用文本表示复杂数据结构的标准格式。Python里可以用 `json` 模块读取和写入。

> 💡 **PM桥梁**：json文件就像你的"配置数据库"，存在磁盘上，下次程序启动还能读出来。这比你每次都手输配置强多了。

### 5.7 动手试一下

1. 创建一个 `my_config.json` 文件：


```json
{
  "username": "star_citizen_fan",
  "owned_ships": ["Aurora MR", "Mustang Alpha"],
  "wishlist": {
    "primary": "Kraken",
    "backup": "Carrack"
  }
}
```


2. 创建一个读取它的Python脚本：


```python
import json

with open("my_config.json", "r", encoding="utf-8") as f:
    config = json.load(f)

print(f"用户名: {config['username']}")
print(f"已有飞船: {config['owned_ships']}")
print(f"心愿单首选: {config['wishlist']['primary']}")
```


3. 运行看看能不能读出来

---


> 📖 **理解函数和模块的概念，学会把代码组织成可复用的单元。**

## 第6章 函数与模块

### 6.1 场景引入

你在 `config.py` 里看到这样的代码：


```python
# 来自 RSI_MIEKCIK_BUY/modules/config.py 约第55行
def get_target():
    """获取目标时间戳（UTC）"""
    from datetime import timedelta
    from calendar import timegm
    tz_offset = timedelta(hours=8)  # Asia/Shanghai = UTC+8
    local_dt = datetime.strptime(CFG["TARGET_TIME"], "%Y-%m-%d %H:%M:%S")
    utc_dt = local_dt - tz_offset
    return timegm(utc_dt.timetuple())
```


`def` 是什么？为什么有个 `"""..."""`？这个函数在干什么？

### 6.2 函数是什么

**函数**（Function）就是一段可以反复使用的代码。你定义一次，可以调用多次。

生活里的例子：你跟助理说"帮我泡杯咖啡"，助理知道泡咖啡的步骤。下次你说"再来一杯"，助理执行同样的步骤。

编程里的例子：


```python
# 定义函数
def say_hello():
    print("Hello!")

# 调用函数
say_hello()  # 输出: Hello!
say_hello()  # 输出: Hello!
```


### 6.3 函数的参数和返回值

函数可以接受**参数**（输入），也可以**返回**结果（输出）：


```python
# 定义带参数的函数
def greet(name):
    return f"Hello, {name}!"

# 调用并获取返回值
message = greet("咩咩")
print(message)  # 输出: Hello, 咩咩!
```


**参数**：函数需要什么才能工作
**返回值**：函数执行完后给回什么

看 `config.py` 里的例子：


```python
# 来自 RSI_MIEKCIK_BUY/modules/config.py 约第55行
def get_target():
    """获取目标时间戳（UTC）"""
    # ... 实现代码 ...
    return timegm(utc_dt.timetuple())  # 返回时间戳
```


调用方只需要 `target = get_target()`，不用管里面怎么算的。

### 6.4 import 和模块

**模块**（Module）就是一个 `.py` 文件。把代码分到不同文件里，好管理。

**import** 就是"把别的文件拿过来用"：


```python
# 从 modules 文件夹里导入 config
from modules import config

# 现在可以用 config.XXX 访问 config.py 里的内容
target = config.get_target()
```


在 `main.py` 里：


```python
# 来自 RSI_MIEKCIK_BUY/modules/main.py 约第20行
from . import config
from .browser import create_browser, login, run_api_mode, run_page_mode
from .gui_progress import RSIGUI
from .gui_config import _show_clear_cart_dialog
from .api_client import RSIClient
```


`from . import config` 的 `.` 表示"从当前包导入"。

你也可以直接 `import config`，然后用 `config.CFG`、`config.get_target()` 这样调用。

### 6.5 包的目录结构


```bash
modules/
    __init__.py      # 包的入口，声明"这个文件夹是个包"
    config.py        # 配置模块
    main.py          # 主逻辑模块
    browser.py       # 浏览器模块
    api_client.py    # API模块
    gui_config.py    # 配置界面模块
    gui_progress.py  # 进度界面模块
```


`__init__.py` 可以是空文件，也可以写一些初始化代码。它的存在告诉Python："这不只是一个文件夹，这是一个可以import的包"。

### 6.6 PM桥梁

> 你跟开发说："我需要加一个功能：用户可以设置提醒，提前5分钟通知他。"
> 
> 开发说："行，我写个 `send_reminder()` 函数，到时候调用一下就行。"
> 
> 你理解：他要把"发通知"这个动作打包成一个工具，哪里需要哪里调。

这就是产品经理理解函数的方式：把一件事打包，用的时候调用。

### 6.7 动手试一下

1. 在 `modules/` 目录下新建一个文件 `my_utils.py`
2. 写一个简单的函数：


```python
def format_price(price):
    """格式化价格显示"""
    return f"${price:.2f}"

def is_expensive(price, threshold=100):
    """判断是否超过阈值，默认100美元"""
    return price > threshold
```


3. 在 `main.py` 的某个位置尝试导入并使用：


```python
# 注意：在 modules/ 外部使用时用绝对导入
from modules.my_utils import format_price, is_expensive

# 测试
price = 675.0
print(format_price(price))           # $675.00
print(is_expensive(price))            # True
print(is_expensive(price, 1000))      # False
```


> ⚠️ 在练习文件中使用相对导入（如 `from .my_utils`）会报错。建议使用绝对导入 `from modules.my_utils`，更简单可靠。

（注意：这是练习代码，实际项目不要乱改）

---


> 📖 **掌握编码规范和常见错误的排查方法，让代码更健壮。**

## 第7章 编码规范+避坑

### 7.1 场景引入

你的代码跑着跑着，报错了：


```python
SyntaxError: invalid character '💰' (U+1F4B0)
```


或者：


```python
UnicodeDecodeError: 'gbk' codec can't decode byte 0xa1 in position 0
```


这是遇到了**编码问题**。Python 3默认UTF-8，但Windows中文系统有时候会用GBK。还有一种常见问题是**缩进错误**——Tab和空格混用，Python直接报错。

### 7.2 中文编码问题

中文Windows的默认编码是GBK，但Python文件是UTF-8。所以当你写中文字符串时，要告诉Python"这个文件用UTF-8"：


```python
# -*- coding: utf-8 -*-
# 这行写在文件第一行（或第二行如果第一行是shebang）

print("你好，世界")
```


几乎所有的Python文件开头都有这行：


```python
# 来自 RSI_MIEKCIK_BUY/modules/config.py 第1行
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
```


### 7.3 缩进坑

Python用**缩进**（Indent）来表示代码层级，不像其他语言用 `{}`。

**正确**：

```python
if is_logged_in:
    print("欢迎")
    if is_vip:
        print("VIP专属折扣")
```


**错误**：

```python
if is_logged_in:
print("欢迎")  # 缩进不一致，报错！
```


**最坑的坑**：Tab和空格混用。看起来一样，但Python能分辨。

VS Code右下角有个显示"Spaces: 4"或"Tab Size: 4"的地方，点击它可以"Convert Indentation to Spaces"——强烈建议统一用空格，4个空格 = 1个Tab。

### 7.4 常见报错类型

| 报错类型 | 含义 | 例子 |
|---------|------|------|
| SyntaxError | 语法错误，代码写错了 | 括号没配对、缩进错误 |
| NameError | 用了没定义的变量 | `print(x)` 但 x 没赋值 |
| TypeError | 类型不匹配 | `"1" + 1` 字符串不能直接加数字 |
| AttributeError | 对象没有这个属性 | `str` 没有 `append` 方法 |
| FileNotFoundError | 文件不存在 | `open("xxx.txt")` 但文件不在 |
| KeyError | 字典没有这个键 | `cfg["NOT_EXIST"]` |

### 7.5 怎么读报错信息

看这个报错：


```python
KeyError: 'TARGET_TIME'

During handling of the above exception, another exception occurred:

File "modules/config.py", line 55, in get_target
    datetime.strptime(CFG["TARGET_TIME"], "%Y-%m-%d %H:%M:%S")
```


从上往下读：
1. 先看第一行 `KeyError: 'TARGET_TIME'` —— 出什么错了（字典找不到这个键）
2. 然后看最后一行 `File "modules/config.py", line 55` —— 哪行代码出问题了
3. 往上追，找到原因：CFG里没有TARGET_TIME？

> 💡 **PM桥梁**：读报错就像看用户反馈。"App闪退了"是第一层报错，"点击购买按钮后闪退"是具体场景，"复现步骤：1.打开App 2.点购买"是完整的上下文。开发需要的是第三种。

### 7.6 项目中的真实报错案例

在 `browser.py` 的 `cookies_save` 函数里：


```python
# 来自 RSI_MIEKCIK_BUY/modules/browser.py 约第35行
def cookies_save(ctx):
    """保存cookies"""
    with open(CFG["COOKIE_FILE"], 'w') as f:
        json.dump(ctx.cookies(), f)
    log.info("✅ Cookies已保存")
```


这里假设 `CFG["COOKIE_FILE"]` 目录存在。如果目录不存在，`open()` 会报 `FileNotFoundError`。

解决方案通常是先创建目录：


```python
import os
os.makedirs(os.path.dirname(file_path), exist_ok=True)
```


### 7.7 命名规范

Python有个约定俗成的命名规范，叫PEP 8：

- 变量/函数：`snake_case`（小写+下划线）
  - ✅ `target_time`, `is_logged_in`
  - ❌ `targetTime`, `isLoggedIn`

- 类名：`PascalCase`（首字母大写）
  - ✅ `RSIClient`, `RSIGUI`
  - ❌ `rsiClient`, `rsi_client`

- 常量：全大写
  - ✅ `CFG`, `GRAPHQL_URL`
  - ❌ `cfg`, `graphqlUrl`

- 私有变量（不想让别人用）：前导下划线
  - `_internal_value`

### 7.8 注释规范


```python
# 单行注释：解释"为什么这么做"

def get_target():
    """
    多行注释（docstring）：
    获取目标时间戳（UTC）
    
    Returns:
        int: Unix时间戳
    """
```


好的注释解释**为什么**，不解释**是什么**（代码本身已经说清楚了）。


```python
# ❌ 差注释
# 遍历列表
for item in items:
    print(item)

# ✅ 好注释
# RSI API要求按创建时间排序，最新的排在前面
for item in items:
    print(item)
```


### 7.9 动手试一下

1. 故意制造一个错误：


```python
name = "咩咩"
print(nmae)  # 故意拼错变量名
```


运行看看报什么错？

2. 修复它：


```python
name = "咩咩"
print(name)  # 正确的变量名
```


3. 尝试读取一个不存在的文件：


```python
with open("not_exist.txt", "r") as f:
    content = f.read()
```


这个报错是什么类型？

---

# 第三部分 · 逐模块深入项目


> 📖 **从需求到代码，看看产品需求是怎么变成程序的。**

## 第8章 需求到代码的翻译

### 8.1 场景引入

你作为PM，设计了一个RSI抢购工具的产品流程：


```python
用户设置目标时间 → 到点自动刷新 → 找到商品 → 加入购物车 → 付款 → 显示结果
```


现在你想知道：这些"需求"是怎么变成代码的？

### 8.2 调用链：从main.py开始

`main.py` 是项目的核心协调器。打开它，你会看到 `run()` 函数：


```python
# 来自 RSI_MIEKCIK_BUY/modules/main.py 约第55行
def run():
    """主入口，改成while循环支持多次执行"""
    
    while True:
        # 显示配置窗口
        if not _show_config_dialog():
            return  # 用户取消
        
        # 关闭可能存在的旧浏览器
        try:
            subprocess.run(['taskkill', '/F', '/IM', 'msedge.exe'], 
                           capture_output=True, timeout=5)
            time.sleep(2.5)
        except:
            pass
        
        log.info("=" * 50)
        log.info("🐑 咩咩蹄到好船来 V1.0.0")
        log.info(f"⏰ 目标: {CFG['TARGET_TIME']}")
        log.info(f"🎮 模式: {'页面加购+API付款' if CFG['MODE'] == 'api' else '页面模式'}")
        log.info("=" * 50)
        
        # ... 后面是启动浏览器、执行抢购流程
```


这就是程序的"主循环"（Main Loop）：一直运行，直到用户取消或出错。

### 8.3 状态机思维

抢购流程可以理解为**状态机**（State Machine）——程序在不同状态之间跳转。

看 `gui_progress.py` 里定义的步骤（V3.0.4版本，实际共7步）：


```python
# 来自 RSI_MIEKCIK_BUY/modules/gui_progress.py 约第58行
self.steps = [
    ("calibrate", "时间校准..."),
    ("wait", "等待抢购时间..."),
    ("login", "登录验证..."),
    ("warmup", "预热加载..."),
    ("cart", "加入购物车..."),
    ("checkout", "极速结账..."),
    ("done", "完成！"),
]
```


这就像你海淘包裹的物流状态：
- 已发货 → 运输中 → 清关中 → 派送中 → 已签收

程序也是：
- 等待中 → 登录成功 → 预热成功 → 加购成功 → 付款成功

### 8.4 配置到启动

**配置阶段**（gui_config.py）：


```python
# 来自 RSI_MIEKCIK_BUY/modules/gui_config.py 约第340行
# 用户点击"开始抢购"后
CFG["TARGET_TIME"] = target_datetime
CFG["SEARCH_KEYWORDS"] = keywords_url
CFG["EXCLUDE_KEYWORDS"] = exclude_keywords
CFG["TIME_OFFSET"] = offset
```


用户填的表单数据，写入 `CFG` 字典保存。

**执行阶段**（main.py）：


```python
# 来自 RSI_MIEKCIK_BUY/modules/main.py 约第100行
# 获取服务器时间校准
time_offset = CFG.get("TIME_OFFSET", 0)
target = config.get_target() + time_offset
now_ts = time.time()
log.info(f"⏰ 目标时间: {CFG['TARGET_TIME']}, 偏移: {time_offset:.3f}s")
```


`main.py` 读取 `CFG` 里的配置，按配置执行。

### 8.5 PM桥梁

> 状态机是产品经理最应该理解的技术概念之一。
> 
> 想想任何用户流程：
> - 电商：浏览 → 加购 → 下单 → 付款 → 发货 → 签收
> - 注册：填写信息 → 验证码 → 激活 → 完成
> - 退款：申请 → 审核 → 退款中 → 退款成功
> 
> 每个状态之间有明确的转换条件。程序就是用代码"翻译"这个状态机。

### 8.6 动手试一下

看 `main.py` 里的抢购主流程（约第100-160行），尝试理解每一步：

1. 找到 `target = config.get_target() + time_offset` 这一行
2. 在 `config.py` 里找到 `get_target()` 函数，理解它怎么计算目标时间戳
3. 思考：为什么用时间戳而不是直接比较时间字符串？

---


> 📖 **学习文件的读取和写入，以及JSON数据的处理。**

## 第9章 文件操作与JSON

### 9.1 场景引入

用户配置了抢购参数，下次打开程序想保留上次设置。但程序关闭后内存里的数据就丢了——怎么办？

答案是：把数据**存到文件里**。下次启动时**再读出来**。

### 9.2 读取文件

**基础读取**：


```python
with open("example.txt", "r", encoding="utf-8") as f:
    content = f.read()
```


- `"r"` 表示读模式（read）
- `with ... as f:` 是**上下文管理器**，自动处理关闭文件，不用担心忘记 `f.close()`
- `encoding="utf-8"` 确保中文正常读写

**逐行读取**：


```python
with open("example.txt", "r", encoding="utf-8") as f:
    for line in f:
        print(line.strip())  # strip()去掉行尾换行符
```


### 9.3 写入文件


```python
with open("output.txt", "w", encoding="utf-8") as f:
    f.write("Hello World\n")
    f.write("第二行\n")
```


- `"w"` 表示写模式（write）。**注意：会覆盖原文件！**
- 如果想追加，用 `"a"` 模式（append）

### 9.4 读写JSON

JSON是存储结构化数据的标准格式。Python内置了 `json` 模块。

**读取JSON**：


```python
import json

with open("sku_bookmarks.json", "r", encoding="utf-8") as f:
    data = json.load(f)  # 读取并解析JSON

# 现在data是一个字典
print(data["version"])  # "1.0"
print(data["skus"])      # 列表
```


**写入JSON**：


```python
import json

data = {
    "version": "1.0",
    "updated_at": "2024-05-02T12:00:00",
    "skus": [
        {"sku_id": "001", "name": "Apollo"},
        {"sku_id": "002", "name": "Cutlass"}
    ]
}

with open("output.json", "w", encoding="utf-8") as f:
    json.dump(data, f, ensure_ascii=False, indent=2)
    # ensure_ascii=False: 不把中文转成\uXXXX
    # indent=2: 格式化输出，便于阅读
```


### 9.5 项目中的配置文件

在 `gui_config.py` 里保存配置：


```python
# 来自 RSI_MIEKCIK_BUY/modules/gui_config.py 约第40行
def _save_config(data):
    """保存配置"""
    config_file = "./scautobuy/rsi_config.json"
    try:
        with open(config_file, 'w', encoding='utf-8') as f:
            json.dump(data, f, ensure_ascii=False, indent=2)
    except:
        pass
```


在 `gui_config.py` 里加载配置：


```python
# 来自 RSI_MIEKCIK_BUY/modules/gui_config.py 约第29行
def _load_saved_config():
    """加载保存的配置"""
    config_file = "./scautobuy/rsi_config.json"
    if os.path.exists(config_file):
        try:
            with open(config_file, 'r', encoding='utf-8') as f:
                return json.load(f)
        except:
            pass
    return None
```


注意 `os.path.exists()` 检查文件是否存在，避免读取不存在的文件报错。

### 9.6 PM桥梁

> 你设计了一个"记住用户偏好"的功能。用户改了主题颜色、下次打开还是这个颜色。
>
> 实现方式：用户改设置 → 存到文件（JSON/数据库） → 下次启动时读取
>
> 这就是"持久化"（Persistence）——把内存里的数据变成磁盘上的文件。

### 9.7 动手试一下

1. 创建一个配置文件 `my_settings.json`：


```json
{
  "theme": "dark",
  "language": "zh-CN",
  "auto_buy_enabled": true
}
```


2. 写一个读取配置的Python脚本：


```python
import json
import os

def load_settings():
    filename = "my_settings.json"
    if not os.path.exists(filename):
        print("配置文件不存在，使用默认设置")
        return {"theme": "light", "language": "en"}
    
    with open(filename, "r", encoding="utf-8") as f:
        return json.load(f)

def save_settings(settings):
    with open("my_settings.json", "w", encoding="utf-8") as f:
        json.dump(settings, f, ensure_ascii=False, indent=2)

# 测试
s = load_settings()
print(f"当前设置: {s}")

# 修改并保存
s["theme"] = "light"
save_settings(s)
print("已保存修改")
```


---


> 📖 **掌握异常处理和日志记录，让程序出问题也能从容应对。**

## 第10章 异常处理与日志

### 10.1 场景引入

你的程序跑着跑着，突然挂了。用户打电话来："程序崩了，什么都没了！"

你去看日志，发现最后一行是：


```python
File "modules/api_client.py", line 200, in get_cart_total
    total = float(total_raw.get('amount', 0))
AttributeError: 'NoneType' object has no attribute 'get'
```


`total_raw` 是 `None`（空），但代码假设它是字典。这是网络请求失败的常见情况——RSI服务器没返回数据。

怎么处理？用**异常处理**。

### 10.2 try/except


```python
try:
    # 尝试执行这段代码
    result = risky_operation()
    print(result)
except ExceptionType:
    # 如果出错了，执行这段
    print("出错了！")
```


`ExceptionType` 可以是具体的错误类型，也可以用通用的 `Exception`。


```python
try:
    result = data["price"]  # 如果data没有price键
except KeyError:
    print("数据格式不对，缺少price字段")
```


### 10.3 实际案例

看 `api_client.py` 的 `get_cart_total` 函数：


```python
# 来自 RSI_MIEKCIK_BUY/modules/api_client.py 约第210行
# 方法2: API
result = self.gql("MiniCartWidgetInitializationQuery")
cart = result.get('data', {}).get('store', {}).get('cart', {})
total_raw = cart.get('total', 0)
if isinstance(total_raw, dict):
    total = float(total_raw.get('amount', 0))
elif isinstance(total_raw, (int, float)):
    total = float(total_raw)
elif isinstance(total_raw, str):
    total = float(re.sub(r'[^\d.]', '', total_raw) or '0')
else:
    total = 0.0
```


这里用了很多 `get(key, default)` 来安全地获取字典值。即使某一级没有这个键，也不会报错，而是返回默认值。

### 10.4 try/except/finally


```python
try:
    file = open("data.json")
    data = json.load(file)
except FileNotFoundError:
    data = {}  # 文件不存在，用空数据
except json.JSONDecodeError:
    data = {}  # JSON格式错误，用空数据
finally:
    if 'file' in locals():  # 如果file变量存在
        file.close()  # 确保文件关闭
```


`finally` 里的代码**无论是否出错都会执行**，常用于清理工作。

Python更推荐用 `with` 语句自动处理：


```python
try:
    with open("data.json") as f:
        data = json.load(f)
except:
    data = {}
```


用 `with` 就不需要手动 `close()` 了。

### 10.5 日志（Logging）

**打印调试**：


```python
print("用户登录成功")
print(f"用户ID: {user_id}")
```


**问题**：发布后这些打印信息就没了，而且会干扰正常输出。

**日志**：


```python
import logging

log = logging.getLogger(__name__)
log.info("用户登录成功")
log.warning(f"API响应超时，重试第{i}次")
log.error(f"无法连接服务器: {e}")
```


在 `config.py` 里设置了日志：


```python
# 来自 RSI_MIEKCIK_BUY/modules/config.py 约第115行
os.makedirs(os.path.dirname(CFG["LOG_FILE"]), exist_ok=True)
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s.%(msecs)03d %(message)s',
    datefmt='%H:%M:%S',
    handlers=[
        logging.FileHandler(CFG["LOG_FILE"], encoding='utf-8'),
        logging.StreamHandler(sys.stdout)
    ]
)
log = logging.getLogger()
```


日志同时输出到：
1. 文件（`./scautobuy/rsi_buy.log`）
2. 屏幕（命令行）

### 10.6 日志级别

| 级别 | 含义 | 使用场景 |
|------|------|----------|
| DEBUG | 调试信息 | 开发时看细节 |
| INFO | 一般信息 | 正常运行的状态 |
| WARNING | 警告 | 可能有问题但能继续 |
| ERROR | 错误 | 出问题了 |
| CRITICAL | 严重错误 | 可能要崩溃了 |

### 10.7 PM桥梁

> **异常处理** = 你产品里的兜底方案
> 
> 你设计支付流程："如果微信支付失败，提示用户重试；如果支付宝失败，也提示重试；如果都失败，显示客服联系方式。"
> 
> 程序里的异常处理就是这些"如果...失败"的代码版本。
>
> **日志** = 你产品里的埋点
>
> 你想知道用户在哪一步放弃购买，加埋点。程序出问题了，看日志。两者本质一样：记录关键事件，方便排查问题。

### 10.8 动手试一下

1. 创建一个会抛出异常的函数：


```python
def divide(a, b):
    try:
        return a / b
    except ZeroDivisionError:
        print("错误：除数不能为零！")
        return None
    except TypeError:
        print("错误：需要数字类型！")
        return None

# 测试
print(divide(10, 2))   # 5.0
print(divide(10, 0))   # 错误信息
print(divide("a", 1))  # 错误信息
```


2. 给上面的函数加上日志：


```python
import logging

logging.basicConfig(level=logging.INFO, format='%(message)s')
log = logging.getLogger()

def divide(a, b):
    try:
        result = a / b
        log.info(f"{a} / {b} = {result}")
        return result
    except ZeroDivisionError:
        log.warning(f"除零错误：{a} / {b}")
        return None
```


---


> 📖 **用Tkinter创建图形界面，实现用户交互窗口。**

## 第11章 GUI · Tkinter

### 11.1 场景引入

你打开RSI项目，看到了两个窗口：
- 配置窗口：设置目标时间、关键词
- 进度窗口：实时显示抢购状态

这两个窗口是怎么做出来的？用的是Python内置的GUI库——**Tkinter**。

### 11.2 Tkinter是什么

**Tkinter** 是Python标准库里的GUI工具包（TK Interface）。"标准库"意味着自带的，不用额外安装。

你可以把它理解为：
- 积木套装：有现成的零件（按钮、输入框、标签）
- 你负责组装：把零件拼成窗口

### 11.3 窗口和控件

**创建窗口**：


```python
import tkinter as tk

root = tk.Tk()
root.title("我的第一个窗口")
root.geometry("400x300")  # 宽x高

root.mainloop()  # 启动窗口，进入事件循环
```


`mainloop()` 是个无限循环，等待用户操作（点按钮、输入文字），直到窗口关闭。

### 11.4 常用控件

| 控件 | 作用 | 代码示例 |
|------|------|----------|
| Label | 显示文字 | `tk.Label(root, text="Hello").pack()` |
| Button | 按钮 | `tk.Button(root, text="点我", command=func).pack()` |
| Entry | 单行输入框 | `tk.Entry(root).pack()` |
| Text | 多行文本框 | `tk.Text(root).pack()` |
| Checkbutton | 复选框 | `tk.Checkbutton(root, text="测试模式").pack()` |
| Combobox | 下拉选择 | `ttk.Combobox(root, values=[...]).pack()` |

### 11.5 布局管理

Tkinter有三种布局方式：
- `pack()`：自动pack，适合简单布局
- `place()`：精确坐标放置
- `grid()`：网格布局

看 `gui_progress.py` 的例子：


```python
# 来自 RSI_MIEKCIK_BUY/modules/gui_progress.py 约第115行
# 标题居中
title_label = tk.Label(
    self.root, text="🐑 咩咩蹄到好船来 V1.0.0", 
    font=("Microsoft YaHei UI", 14, "bold"),
    fg="#1a3a5c", bg=PROGRESS_BG_COLOR, padx=6, pady=2
)
title_label.place(relx=0.5, y=15, anchor='n')
```


`place(relx=0.5, y=15, anchor='n')` 意思是：相对于父容器，横向居中（0.5=50%），纵向离顶部15像素，锚点在北（n=center-north）。

### 11.6 事件驱动

GUI是**事件驱动**的——程序不主动做什么，等用户操作才反应。

常见事件：
- 点击按钮 → 触发 `command` 回调
- 输入文字 → 文本变化
- 关闭窗口 → 触发关闭事件


```python
def on_click():
    print("按钮被点了！")

button = tk.Button(root, text="点我", command=on_click)
```


按钮被点，`on_click()` 就会被调用。这就是**回调函数**（Callback Function）——把一个函数"回调"给另一个组件。

### 11.7 回调与闭包

看 `gui_config.py` 的例子：


```python
# 来自 RSI_MIEKCIK_BUY/modules/gui_config.py 约第306行
def on_submit():
    # 验证搜索关键词
    keywords = url_entry.get().strip()
    if not keywords:
        url_entry.focus_set()
        return
    
    # ... 写入CFG ...
    
    result["continue"] = True
    root.destroy()

start_btn = tk.Button(root, text="开始抢购", command=on_submit)
```


当用户点"开始抢购"，`on_submit()` 执行：
1. 读取输入框内容
2. 验证非空
3. 写入配置
4. 关闭窗口

### 11.8 PM桥梁

> **事件驱动** = 你产品的交互逻辑
>
> 想象一个线下门店：
> - 店员站在门口等（mainloop）
> - 顾客推门进来 → 店员打招呼（触发事件）
> - 顾客问有没有某款商品 → 店员去仓库查
> - 顾客买单 → 收银处理
>
> GUI程序就是这样：等用户操作（事件），然后响应（回调）。

### 11.9 动手试一下

1. 创建一个简单的计算器窗口：


```python
import tkinter as tk

def calculate():
    try:
        a = float(entry_a.get())
        b = float(entry_b.get())
        result = a + b
        label_result.config(text=f"结果: {result}")
    except ValueError:
        label_result.config(text="请输入数字！")

root = tk.Tk()
root.title("简单加法计算器")
root.geometry("300x150")

# 标签
tk.Label(root, text="第一个数字:").place(x=20, y=20)
tk.Label(root, text="第二个数字:").place(x=20, y=50)
tk.Label(root, text="运算结果:").place(x=20, y=80)

# 输入框
entry_a = tk.Entry(root, width=15)
entry_a.place(x=120, y=20)
entry_b = tk.Entry(root, width=15)
entry_b.place(x=120, y=50)

# 结果标签
label_result = tk.Label(root, text="结果: -")
label_result.place(x=120, y=80)

# 按钮
tk.Button(root, text="相加", command=calculate).place(x=120, y=110)

root.mainloop()
```


2. 运行看看效果

---


> 📖 **学习浏览器自动化，让程序帮你操作网页。**

## 第12章 浏览器自动化

### 12.1 场景引入

抢购脚本的核心功能是什么？**自动操作浏览器**。

- 打开网页
- 登录账号
- 刷新页面
- 点击按钮
- 填写表单
- 提取网页数据

这一切是怎么做到的？用的是 **Playwright**（或 Selenium）框架。

### 12.2 Playwright是什么

**Playwright** 是微软出的浏览器自动化框架。你可以理解为：
- 浏览器 = 真人用户
- Playwright = 给你一个遥控器，让Python控制浏览器


```python
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    browser = p.chromium.launch()  # 启动Chrome
    page = browser.new_page()      # 打开新标签页
    page.goto("https://example.com")  # 访问网页
    browser.close()
```


### 12.3 等待网页加载

网页加载需要时间，代码执行太快会"抢跑"。Playwright提供了几种等待策略：


```python
# 等待DOM加载完成（不等待图片/JS）
page.goto(url, wait_until="domcontentloaded")

# 等待全部资源加载完成
page.goto(url, wait_until="load")

# 等待网络空闲
page.goto(url, wait_until="networkidle")

# 显式等待某个元素出现
page.wait_for_selector('.add-to-cart', timeout=10000)
```


### 12.4 定位元素

要想点击按钮，得先"找到"它。Playwright支持多种定位方式：

| 定位方式 | 示例 | 说明 |
|----------|------|------|
| CSS选择器 | `page.locator("button.primary")` | 按CSS选择器 |
| 文本 | `page.locator("text=登录")` | 按显示文字 |
| XPath | `page.locator("//button[@id='btn']")` | 按XML路径 |
| 组合 | `page.locator("button:has-text('Add to Cart')")` | 查找包含文字的按钮 |

看 `browser.py` 的例子：


```python
# 来自 RSI_MIEKCIK_BUY/modules/browser.py 约第200行
# 找到"Sign In"按钮并点击
btn = page.query_selector('button:has-text("Sign In")')
if btn and btn.is_visible():
    btn.click()
```


### 12.5 执行JavaScript

有时候直接用JS更方便：


```python
# 来自 api_client.py 约第295行
result = self.page.evaluate("""
    () => {
        const cards = document.querySelectorAll('.c-skuCard');
        return { cardCount: cards.length };
    }
""")
```


`page.evaluate()` 在浏览器里执行JavaScript代码，可以访问和操作网页DOM。

> 💡 **语言协作说明**：Python可以通过Playwright在浏览器里执行JS代码，就像你用中文写需求但开发用英文写代码一样。Python负责整体流程控制，JS负责浏览器内的具体操作——不同语言负责不同的事，各司其职。

### 12.6 处理iframe和弹窗

网页里可能有嵌套的iframe（内嵌网页），或者弹出框：


```python
# 切换到iframe
page.frame_locator("iframe[name='payment']").locator("button").click()

# 处理alert弹窗
page.on("dialog", lambda dialog: dialog.accept())
page.click("button")  # 触发弹窗
```


### 12.7 截图功能


```python
# 来自 config.py 约第140行
def ss(page, name):
    """截图函数"""
    if page:
        os.makedirs(CFG["SCREENSHOT_DIR"], exist_ok=True)
        path = f"{CFG['SCREENSHOT_DIR']}/{name}_{datetime.now().strftime('%H%M%S_%f')}.png"
        try:
            page.screenshot(path=path)
            log.info(f"📸 {path}")
        except: pass
        return path
    return ""
```


### 12.8 PM桥梁

> **浏览器自动化** = 把你手动操作网页的动作"录制"下来，然后让代码重复播放。
>
> 你手动抢购流程：
> 1. 打开RSI商店 → `page.goto()`
> 2. 搜索Kraken → `page.fill()` 填搜索框 + `page.click()` 点搜索
> 3. 找到商品点加购 → `page.click('.add-to-cart')`
> 4. 去购物车 → `page.goto(CART_URL)`
>
> 自动化脚本就是把你这些动作翻译成代码。

### 12.9 动手试一下

1. 先确保安装了playwright和chromium：

```bash
pip install playwright
python -m playwright install chromium
```


2. 创建一个测试脚本：


```python
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    browser = p.chromium.launch(headless=False)  # headless=False能看到浏览器
    page = browser.new_page()
    
    # 打开百度
    page.goto("https://www.baidu.com")
    
    # 在搜索框输入
    page.fill("#kw", "Python教程")
    
    # 点搜索按钮
    page.click("#su")
    
    # 等待搜索结果加载
    page.wait_for_timeout(2000)  # 等待2秒
    
    # 截图保存
    page.screenshot(path="baidu_result.png")
    
    print("完成！截图已保存")
    
    browser.close()
```


---


> 📖 **理解面向对象编程，用类来组织更复杂的代码。**

## 第13章 面向对象入门

### 13.1 场景引入

你看到 `api_client.py` 里有这样的代码：


```python
# 来自 RSI_MIEKCIK_BUY/modules/api_client.py 约第21行
class RSIClient:
    """RSI GraphQL API 客户端"""
    
    def __init__(self, page):
        self.page = page
        self.url = CFG["GRAPHQL_URL"]
        self.billing_address_id = None
        self.cart_total = 0.0
    
    def get_cart_total(self):
        """获取购物车总额"""
        # ...
```


`class` 是什么？和前面讲的函数有什么区别？

### 13.2 类和对象

**类**（Class）是什么？可以理解为"设计图"或"模板"。

**对象**（Object）是什么？根据设计图造出来的"具体实例"。

现实世界的例子：
- **类** = 汽车的图纸
- **对象** = 根据图纸造出来的具体汽车（京A12345、北京现代）

代码世界的例子：


```python
# 定义类（汽车图纸）
class Car:
    def __init__(self, brand, color):
        self.brand = brand      # 属性
        self.color = color      # 属性
        self.speed = 0          # 初始速度
    
    def drive(self):
        print(f"{self.brand} 正在行驶，速度 {self.speed}")
    
    def accelerate(self, increment):
        self.speed += increment

# 创建对象（根据图纸造车）
my_car = Car("特斯拉", "黑色")
your_car = Car("比亚迪", "白色")

# 调用方法
my_car.drive()              # 特斯拉 正在行驶，速度 0
my_car.accelerate(50)
my_car.drive()              # 特斯拉 正在行驶，速度 50
```


### 13.3 self是什么

`self` 是"**自己**"的意思。类里的方法，第一个参数永远是 `self`，表示"这个对象本身"。


```python
class Car:
    def drive(self):
        # self 就是调用这个方法的具体对象
        print(f"{self.brand} 在开")
```


当你写 `my_car.drive()` 时：
- `my_car` 就是 `self`
- `self.brand` 就是 `my_car.brand`

### 13.4 __init__ 构造函数

`__init__` 是"初始化"方法，创建对象时自动调用：


```python
def __init__(self, page):
    self.page = page          # 把传入的page存为属性
    self.url = CFG["GRAPHQL_URL"]  # 其他初始化
```


相当于类的"构造函数"，对象被创建时会先执行这里。

### 13.5 属性和方法

**属性** = 对象的特征（变量）
**方法** = 对象的行为（函数）


```python
class RSIClient:
    # 属性
    def __init__(self, page):
        self.page = page          # 存储页面对象
        self.billing_address_id = None  # 账单地址
        self.cart_total = 0.0     # 购物车总额
    
    # 方法
    def get_cart_total(self):
        """获取购物车总额"""
        # 方法里有self，可以访问属性
        print(f"当前总额: {self.cart_total}")
```


### 13.6 项目中的类

**RSIClient**（api_client.py）：

```python
class RSIClient:
    """RSI GraphQL API 客户端"""
    
    def __init__(self, page):
        # 初始化
        self.page = page
        self.url = CFG["GRAPHQL_URL"]
    
    def gql(self, operation_name, variables=None):
        # 执行GraphQL请求
        pass
    
    def add_to_cart_via_page(self):
        # 列表页加购
        pass
    
    def apply_credits(self, amount=None):
        # 应用信用点
        pass
```


**RSIGUI**（gui_progress.py）：

```python
class RSIGUI:
    """进度GUI"""
    
    def __init__(self, target_time):
        self.target_time = target_time
        self.steps = [...]
    
    def update_status(self, text, step_key):
        # 更新状态
        pass
    
    def update_step(self, key, success):
        # 更新步骤状态
        pass
```


### 13.7 PM桥梁

> **类** = 你设计的产品功能模块
>
> 想象你设计了一个"购物车"功能：
> - 它有属性：商品列表、总价、优惠码
> - 它有方法：添加商品、删除商品、应用优惠、计算总价
>
> 类就是把相关的"数据"和"操作"打包在一起。
>
> **面向对象编程**（OOP）就是"用类和对象来组织代码"的设计思路。

### 13.7.1 self.items 详解

在上面的购物车示例中：
- `self` 就是"这个购物车对象自己"
- `self.items` 就是"这个对象自己的 items 属性"（一个列表）
- `self.total` 就是"这个对象自己的 total 属性"（一个数字）

当你写 `cart.add_item("Kraken", 675.00)` 时：
- `cart` 就是 `self`
- `self.items.append(...)` 就是 `cart.items.append(...)`

> 💡 **生活比喻**：就像"我自己的钱包"、"我自己背包里的东西"。"我"就是 self，"我背包里的东西"就是 self.items。

### 13.8 动手试一下

1. 创建一个简单的"购物车"类：


```python
class ShoppingCart:
    def __init__(self):
        self.items = []  # 购物车里的商品
        self.total = 0.0
    
    def add_item(self, name, price, quantity=1):
        """添加商品"""
        self.items.append({
            "name": name,
            "price": price,
            "quantity": quantity
        })
        self.total += price * quantity
        print(f"添加了 {name}，单价 {price}")
    
    def remove_item(self, name):
        """移除商品"""
        for item in self.items:
            if item["name"] == name:
                self.total -= item["price"] * item["quantity"]
                self.items.remove(item)
                print(f"移除了 {name}")
                return
        print(f"{name} 不在购物车里")
    
    def checkout(self):
        """结算"""
        print(f"\n{'='*30}")
        print(f"购物车结算")
        print(f"{'='*30}")
        for item in self.items:
            print(f"{item['name']} x {item['quantity']}: ${item['price'] * item['quantity']:.2f}")
        print(f"{'='*30}")
        print(f"总计: ${self.total:.2f}")

# 测试
cart = ShoppingCart()
cart.add_item("RSI Kraken", 675.00)
cart.add_item("RSI Apollo", 175.00, 2)
cart.checkout()
```


---


> 📖 **学会使用Git管理代码版本，再也不怕改坏了。**

## 第14章 Git与版本管理

### 14.1 场景引入

你改了一个Python文件，改着改着改坏了，但你想不起来原来怎么写的了。

或者：你想试试新功能，但不敢直接改，怕改坏了回不去。

这都是**版本控制**要解决的问题。

### 14.2 Git是什么

**Git** 是一个版本控制系统，可以理解为"给代码装上了时光机"：
- 随时可以回到任何一个历史版本
- 记录每次改了什么、谁改的
- 可以开分支，同时做多件事

### 14.3 基本概念

| 概念 | 解释 |
|------|------|
| 仓库（Repository） | 存放代码的地方 |
| 提交（Commit） | 给当前状态拍一张快照 |
| 分支（Branch） | 从主线分出来做独立开发 |
| 合并（Merge） | 把分支的改动合回主线 |

### 14.4 基本命令

**初始化仓库**：

```bash
git init
```


**查看状态**：

```bash
git status
```


**添加文件到暂存区**：

```bash
git add filename.py     # 添加单个文件
git add .                # 添加所有改动
```


**提交**：

```bash
git commit -m "添加了XXX功能"
```


**查看历史**：

```bash
git log
```


**回退版本**：

```bash
git checkout xxx    # 回到某个提交
git reset --hard xxx  # 强制回退（小心！）
```


### 14.5 分支操作

**创建分支**：

```bash
git branch feature-xyz    # 创建分支
git checkout feature-xyz   # 切换到分支
# 或者一步到位：
git checkout -b feature-xyz
```


**合并分支**：

```bash
git checkout main         # 先切回主线
git merge feature-xyz     # 把feature-xyz合并过来
```


### 14.6 远程仓库

GitHub、GitLab、Gitee都是"云端仓库"：


```bash
# 克隆远程仓库到本地
git clone https://github.com/user/repo.git

# 推送本地提交到远程
git push origin main

# 拉取远程更新
git pull origin main
```


### 14.7 项目中的.gitignore

项目里有 `.gitignore` 文件，告诉Git"哪些文件不用管"：


```bash
# 来自 RSI_MIEKCIK_BUY/.gitignore（类似内容）
__pycache__/       # Python编译缓存
*.pyc              # 编译产物
scautobuy/         # 运行产生的临时文件
*.log              # 日志文件
```


这些文件每次运行都会变，不需要纳入版本控制。

### 14.8 PM桥梁

> **Git分支** = 你产品的功能迭代
>
> - `main` 或 `master` = 正式环境，稳定版本
> - `dev` 或 `develop` = 测试环境，开发中的版本
> - `feature-xxx` = 某个新功能的开发分支
>
> 开发流程：
> 1. 从 `main` 新建 `feature-kraken-ui`
> 2. 在新分支开发UI改进
> 3. 开发完成，合并回 `main`
> 4. `main` 测试通过，发布新版本
>
> **Commit信息** = 你写的代码变更说明
>
> 好习惯：每次commit都写清楚改了什么
> - ✅ "修复了时间格式化的时区问题"
> - ❌ "更新"

### 14.9 动手试一下

1. 在命令行进入RSI项目目录
2. 运行 `git status`，看看有哪些改动
3. 运行 `git log --oneline`，看看提交历史
4. （如果有GitHub）尝试fork这个仓库

---

# 第四部分 · 独立作战


> 📖 **学习架构设计思维，从全局角度思考代码组织。**

## 第15章 架构设计思维

### 15.1 场景引入

你看了RSI项目的架构，发现它是V1→V2→V3这样一步步演进的。最开始可能只是一个简单的脚本，后来才加上了GUI、API模式等功能。

现在你想知道：**怎么从零设计一个好的项目架构？**

### 15.2 高内聚低耦合

这是软件架构的核心原则：

- **高内聚**（High Cohesion）：一个模块只做一件事，把相关的东西放在一起
- **低耦合**（Low Coupling）：模块之间依赖要少，改一个模块不影响其他

看RSI项目的模块划分：
- `config.py`：只管配置
- `browser.py`：只管浏览器操作
- `api_client.py`：只管API调用
- `gui_*.py`：只管界面

每个模块职责单一，模块之间通过 `CFG` 字典和函数调用传递数据，改了GUI不影响浏览器逻辑。

### 15.3 依赖关系图


```python
main.py（协调者）
├── gui_config.py（用户输入）
├── gui_progress.py（界面展示）
├── browser.py（浏览器操作）
│   └── api_client.py（API调用）
└── config.py（配置共享）
```


`main.py` 依赖其他所有模块，但其他模块之间尽量不相互依赖（或者依赖关系很清晰）。

### 15.4 配置开关设计

看 `config.py` 里的开关设计：


```python
CFG = {
    # 执行模式
    "MODE": "api",  # "api" = 列表页加购+API付款, "page" = 纯页面模式
    "TEST_MODE": False,  # 测试模式
}
```


这是一种**策略模式**（Strategy Pattern）：同一个功能，有多种实现方式，通过配置切换。

### 15.5 TURBO_MODE

> ⚠️ 以下为假设施教示例，用于讲解配置开关设计思路，并非项目中的实际代码

假设你要加一个"TURBO模式"，比正常模式更快（代价是更容易出错）。

设计思路：
1. 在 `config.py` 加开关：

```python
"TURBO_MODE": False,
"TURBO_DELAY": 0.001,  # 普通模式1ms延迟
"NORMAL_DELAY": 0.01,  # 普通模式10ms延迟
```


2. 在调用的地方：

```python
delay = CFG["TURBO_DELAY"] if CFG.get("TURBO_MODE") else CFG["NORMAL_DELAY"]
time.sleep(delay)
```


这样只需要改配置，不用改核心逻辑。

### 15.6 PM桥梁

> **架构设计** = 你产品PRD里的"技术方案"章节
>
> 产品经理需要思考：
> - 这个功能涉及哪些模块？
> - 模块之间怎么配合？
> - 哪些是核心流程，哪些是边缘流程？
> - 怎么做能让开发效率最高、改动影响最小？
>
> **高内聚低耦合** = 你拆分需求时的"一个功能一个需求"
>
> 别说"登录注册流程优化"，拆成：
> - 登录功能
> - 注册功能
> - 找回密码
> - SSO单点登录
>
> 每个功能独立设计、独立开发、独立测试。

### 15.7 动手试一下

分析RSI项目架构：

1. 画出模块依赖图（用文字描述或手绘）
2. 找出哪些地方有"配置开关"的设计
3. 思考：如果你要加一个"邮件通知"功能，应该怎么设计？
   - 新增 `notification.py` 模块？
   - 还是直接写在 `config.py` 里？
   - 邮件发送用哪个库？

---


> 📖 **动手改造项目，体验实际改代码的感觉。**

## 第16章 实战改造

### 16.1 场景引入

你看了代码，想动手改一改试试。但又怕改坏了。

这一章教你几个**安全改造**的小练习，改错了也不怕，大不了从头再来。

### 16.2 改延迟参数

**目标**：把API模式的延迟从0.01秒改成0.005秒（更快）

**步骤**：
1. 找到 `config.py`
2. 找到 `"DELAY": 0.01` 这一行
3. 改成 `0.005`
4. 运行测试看看效果

> ⚠️ 改太快可能导致：
> - 服务器认为是机器人攻击，IP被封
> - 来不及等页面加载完就点下一步，导致操作失败
> - 建议保留测试模式 `TEST_MODE: True`，先测再真抢

### 16.3 改界面文案

**目标**：把"咩咩蹄到好船来"改成"星际战舰抢购助手"

**涉及文件**：
- `gui_config.py`：配置窗口标题
- `gui_progress.py`：进度窗口标题

**步骤**：
1. 搜索"咩咩蹄到好船来"
2. 替换成"星际战舰抢购助手"
3. 运行看效果

代码里这样写（`gui_progress.py`）：

```python
# 来自 RSI_MIEKCIK_BUY/modules/gui_progress.py 约第91行
self.root.title("🐑 咩咩蹄到好船来 V1.0.0")
```


改成：

```python
self.root.title("🚀 星际战舰抢购助手 V1.0.0")
```


### 16.4 改默认搜索词

**目标**：把默认搜索词从"Kraken"改成你想抢的飞船

**文件**：`config.py`

```python
"SEARCH_KEYWORDS": "Kraken",  # 改成你想抢的
```


或者在 `gui_config.py` 里改默认显示：

```python
# 来自 RSI_MIEKCIK_BUY/modules/gui_config.py 约第490行
url_entry.insert(0, saved_config.get("search_keywords", CFG["SEARCH_KEYWORDS"]))
```


### 16.5 给书签加排序

**目标**：让 `sku_bookmarks.json` 的内容按价格排序

看 `modules/sku_bookmarks.py` 实际的函数（该文件只有53行，只有4个函数）：


```python
# 来自 RSI_MIEKCIK_BUY/modules/sku_bookmarks.py
import json
import os

BOOKMARKS_FILE = os.path.join(_cfg.BASE_PATH, 'sku_bookmarks.json')

def load_bookmarks():
    """加载收藏的skuId列表"""
    # ... 实现代码

def save_bookmarks(bookmarks):
    """保存收藏的skuId列表"""
    # ... 实现代码

def add_bookmark(name: str, sku_id: str, price: float = 0):
    """添加一个skuId到收藏夹"""
    # ... 实现代码

def remove_bookmark(sku_id: str):
    """删除一个收藏"""
    # ... 实现代码
```


如果要添加排序功能，可以这样扩展 `add_bookmark` 后的处理逻辑：


```python
def add_bookmark(name: str, sku_id: str, price: float = 0):
    """添加一个skuId到收藏夹"""
    bookmarks = load_bookmarks()
    # 检查是否已存在（按skuId去重）
    for b in bookmarks:
        if b.get('sku_id') == sku_id:
            b['name'] = name  # 更新名称
            b['price'] = price
            save_bookmarks(bookmarks)
            return
    bookmarks.append({'name': name, 'sku_id': sku_id, 'price': price})
    save_bookmarks(bookmarks)

def get_bookmarks_sorted_by_price(reverse=False):
    """按价格排序获取所有收藏（注意：这个函数需要自己添加）"""
    bookmarks = load_bookmarks()
    return sorted(bookmarks, key=lambda x: x.get("price", 0), reverse=reverse)
```


> ⚠️ 注意：教程中的代码仅供参考，实际项目请根据源码实际情况修改。

### 16.6 使用Git保存改造

改之前先commit一次，改坏了可以回退：


```bash
git add .
git commit -m "改造前的版本"
# 做修改...
# 如果改坏了：
git reset --hard HEAD
# 就回到改造前的状态了
```


### 16.7 PM桥梁

> **改代码** = 你产品的小版本迭代
>
> 产品经理经常提"微调"需求：
> - 文案改一下
> - 默认值调一下
> - 阈值改一改
>
> 这些在代码里就是"配置改一改"或"某行代码改一改"。
>
> 学会改代码，你就能自己验证"这个文案用户看了会不会有歧义"这样的问题。

### 16.8 动手试一下

选择一个改造任务：

1. **简单**：改默认测试时间（从"明天0点"改成"今天某个时间"）
2. **中等**：加一个自定义日志级别的功能
3. **挑战**：给gui_progress加一个"显示调试信息"的可选开关

记得改之前 `git commit` 一次！

---


> 📖 **从改代码到写代码，完整实现一个新功能。**

## 第17章 从改到创

### 17.1 场景引入

改代码只是第一步。你现在想**自己加一个新功能**。

比如："我想让脚本在抢购成功后自动给我发邮件通知。"

这一章教你：**从需求拆解到代码落地的完整思路**。

### 17.2 需求拆解

**需求**：抢购成功后自动发邮件

**拆解**：
1. 什么时候发？→ 抢购成功时（检测到URL包含"success"或"confirm"）
2. 发给谁？→ 用户配置里指定邮箱
3. 发什么内容？→ "恭喜！抢到了XXX，耗时X秒"
4. 怎么发？→ 用SMTP协议发送邮件

### 17.3 技术方案

**方案A：Python内置smtplib**
- 优点：不需要第三方库
- 缺点：配置稍复杂，要填邮箱服务器地址

**方案B：第三方库yagmail**
- 优点：超级简单
- 缺点：多装一个库

### 17.4 伪代码设计


```python
def send_success_email(item_name, duration):
    """
    发送抢购成功邮件
    
    参数：
        item_name: 抢到的商品名称
        duration: 耗时（秒）
    """
    
    # 1. 读取用户配置的邮箱
    user_email = CFG.get("USER_EMAIL")
    if not user_email:
        return  # 没配置，不发
    
    # 2. 构建邮件内容
    subject = "🎉 抢购成功通知"
    body = f"""
    恭喜！
    
    您成功抢购了 {item_name}
    抢购耗时: {duration:.2f} 秒
    
    祝您游戏愉快！
    """
    
    # 3. 调用发送函数
    try:
        smtp_send(user_email, subject, body)
        log.info(f"📧 邮件已发送到 {user_email}")
    except Exception as e:
        log.warning(f"📧 邮件发送失败: {e}")
```


### 17.5 模块设计

新增一个 `notification.py` 模块：


```python
# notification.py
import smtplib
from email.mime.text import MIMEText
from email.header import Header

def send_email(to_addr, subject, body, smtp_server="smtp.gmail.com", port=587):
    """发送邮件"""
    
    # 发件人配置（硬编码或从环境变量读取）
    from_addr = "your_email@gmail.com"
    password = "your_app_password"  # Gmail需要应用专用密码
    
    # 构建邮件
    msg = MIMEText(body, 'plain', 'utf-8')
    msg['Subject'] = Header(subject, 'utf-8')
    msg['From'] = from_addr
    msg['To'] = to_addr
    
    # 发送
    with smtplib.SMTP(smtp_server, port) as server:
        server.starttls()
        server.login(from_addr, password)
        server.send_message(msg)

def notify_success(item_name, duration, email):
    """抢购成功通知"""
    subject = "🎉 RSI抢购成功！"
    body = f"成功抢购 {item_name}，耗时 {duration:.2f}秒"
    send_email(email, subject, body)
```


### 17.6 集成到主流程

在 `main.py` 的成功分支调用：


```python
# 原来的代码...
if success:
    # 显示结果
    if gui: gui.show_result(True, f"总耗时 {total:.2f}秒")
    
    # 新增：发送邮件通知
    try:
        from .notification import notify_success
        notify_success(
            CFG["SEARCH_KEYWORDS"].replace("+", " "),
            total,
            CFG.get("USER_EMAIL")
        )
    except ImportError:
        pass  # 模块不存在，忽略
```


### 17.7 配置项

在 `config.py` 加：


```python
"USER_EMAIL": None,  # 通知邮箱，留空不发邮件
```


在 `gui_config.py` 的配置界面加邮箱输入框。

### 17.8 PM桥梁

> **从改到创** = 你自己当了一次"技术产品经理"
>
> 你做了一件完整的事：
> 1. 发现了需求（想要邮件通知）
> 2. 拆解了技术点（什么时候发、发什么、怎么发）
> 3. 设计了方案（选哪个库、怎么组织代码）
> 4. 落地了代码（写模块、集成）
>
> 这就是一个完整的产品开发闭环。

### 17.9 动手试一下

选择一个新功能实现：

**选项A：微信/钉钉通知**
- 用企业微信机器人的Webhook
- 发送POST请求到Webhook地址即可

**选项B：加一个"历史记录"功能**
- 把每次抢购记录存到JSON文件
- 下次打开能看到历史

**选项C：加一个"定时提醒"**
- 开抢前5分钟弹系统通知
- 用 `win10toast` 或系统自带通知

选择一个，画好流程图，写出伪代码，然后尝试实现！

---

# 附录

## A. 常用命令速查


```bash
# Python运行
python script.py

# 安装依赖
pip install xxx
pip install -r requirements.txt

# Git操作
git status
git add .
git commit -m "说明"
git push
git pull

# 格式化代码
python -m black script.py
```


## B. 常见错误排查

| 错误 | 原因 | 解决 |
|------|------|------|
| `ModuleNotFoundError` | 没装这个库 | `pip install xxx` |
| `IndentationError` | 缩进不一致 | 用4个空格替换Tab |
| `FileNotFoundError` | 文件不存在 | 检查路径 |
| `TimeoutError` | 网络超时 | 检查网络、重试 |
| `PermissionError` | 权限不足 | 用管理员运行/改文件权限 |

## C. 推荐学习资源

- 官方文档：https://docs.python.org/zh-cn/3/
- 菜鸟教程Python：https://www.runoob.com/python3/python3-tutorial.html
- VS Code Python教程：内置的Python extension有教程

---

## D. 术语表

| 术语 | 解释 |
|------|------|
| **时间戳（Unix timestamp）** | 计算机记录时间的方式，从1970年1月1日到现在的秒数。例如：1715760000 表示"2024年5月15日" |
| **docstring** | 用三引号 `"""..."""` 包裹的函数说明文档，用于解释函数的功能、参数和返回值 |
| **ensure_ascii** | `json.dump()` 的参数。`ensure_ascii=False` 允许中文原文存储；`ensure_ascii=True` 会把中文转成 `\uXXXX` 的Unicode格式 |
| **CSS选择器** | 网页元素的"地址"，用于精确定位页面元素。例如 `#kw` 表示 id 为 kw 的元素，`.btn` 表示 class 包含 btn 的元素 |
| **while True** | 无限循环，程序会一直执行循环体内部代码，直到遇到 `break` 或 `return` 才退出。需要谨慎使用 |
| **死循环** | 不会自动结束的循环。如果循环条件永远为真且没有 `break`，程序会卡在循环里无法退出 |
| **相对导入** | 在包内部使用的导入方式，如 `from . import config`，`.` 表示当前包目录 |
| **绝对导入** | 完整的导入路径，如 `from modules.config import CFG`，从项目根目录开始的完整路径 |
| **HTTP** | 超文本传输协议，定义客户端（如浏览器）和服务器之间请求-响应格式的规则 |
| **API（接口）** | Application Programming Interface，应用程序编程接口。可以理解为"服务方提供的功能说明文档"，开发按照文档调用相应功能 |
| **URL** | Uniform Resource Locator，网上的资源地址，如 `https://example.com/page` |
| **DOM** | Document Object Model，浏览器把HTML文档解析成树形结构，方便JS操作页面元素 |
| **回调函数（Callback）** | 把一个函数作为参数传给另一个函数，等特定事件发生时自动调用。GUI编程中最常见 |
| **参数（Argument）** | 函数调用时传入的具体值，如 `func(18)` 中的 `18` |
| **形参（Parameter）** | 函数定义时声明的变量名，如 `def func(x):` 中的 `x` |

---

祝你在Python的世界里玩得开心！如果遇到问题，记得：
1. 看报错信息
2. 搜索引擎搜错误
3. 看官方文档
4. 问AI助手
