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

> 💡 **提前说一句**：我们的项目实际用的是 **CDP连接本地Edge浏览器**（因为RSI网站会检测并屏蔽自动化浏览器），Chromium只是备用方案。但学习阶段先用Chromium练手，第12章会详细讲两种方式的区别和切换。先装上不亏。

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

> 💡 **本章重点**：CDP连接Edge（反检测核心）、回退Chromium、反自动化检测原理

### 12.1 场景引入

上一章我们学会了用Tkinter做GUI界面，现在要解决一个核心问题：**程序怎么操作网页？**

想象你要抢一个限量商品：
1. 打开浏览器
2. 登录账号
3. 找到商品页面
4. 点击"加入购物车"
5. 填写信息并付款

这些操作，如果用纯API来做，需要研究网站的每个接口。但如果用浏览器自动化，程序可以**像真人一样操作网页**——打开浏览器，点击按钮，填写表单。

**关键区别**：
- API模式：直接发送HTTP请求，快但需要研究接口
- 页面模式：让程序控制真实浏览器操作网页，慢但通用

本项目的V3.0设计是**混合模式**：先用页面加购（绕过SKU提取），再用API付款（追求速度）。

### 12.2 Playwright基础

Playwright是微软开发的浏览器自动化库，可以控制Chrome、Edge、Firefox等浏览器。

**两种启动方式**：

```python
# 方式1：直接启动（自己创建浏览器进程）
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    browser = p.chromium.launch()  # 启动Chromium
    page = browser.new_page()
    page.goto("https://example.com")
```

```python
# 方式2：通过CDP连接（复用已打开的浏览器）
with sync_playwright() as p:
    browser = p.chromium.connect_over_cdp("http://127.0.0.1:9222")
    page = browser.new_page()
```

**本项目的选择**：优先用方式2连接本地Edge，只有失败时才回退方式1启动Chromium。

> ⚠️ **简化说明**：实际代码中 `create_browser()` 函数做了大量准备工作（查找Edge路径、清理残留进程等），完整流程约80行。

### 12.3 反自动化检测

#### 12.3.1 网站怎么检测机器人？

当你用自动化工具控制浏览器时，网站可以通过多种方式发现你不是"真正的人"：

| 检测点 | 说明 | 绕过方法 |
|--------|------|----------|
| `navigator.webdriver` | 自动化浏览器会暴露这个属性 | 修改为undefined |
| headless模式 | 无头浏览器没有地址栏等 | 用headless=False |
| 自动化特征 | window.navigator.plugins等 | 修改浏览器属性 |
| 鼠标轨迹 | 机器人移动很规律 | 模拟随机延迟 |

#### 12.3.2 项目的反检测策略

**策略1：使用真实浏览器Edge**

项目优先使用用户本地的Edge浏览器，而不是自动化的Chromium：

```python
# 来自 browser.py - 查找Edge安装路径
def find_edge():
    """查找Edge安装路径"""
    paths = [
        r"C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe",
        r"C:\Program Files\Microsoft\Edge\Application\msedge.exe",
        os.path.expanduser(r"~\AppData\Local\Microsoft\Edge\Application\msedge.exe"),
    ]
    for p in paths:
        if os.path.exists(p):
            return p
    return None
```

**策略2：通过CDP连接而不是启动**

```python
# 通过CDP连接（最多5次尝试，每次失败后重启Edge）
browser = None
for cdp_attempt in range(5):
    try:
        browser = p.chromium.connect_over_cdp(f"http://127.0.0.1:{debug_port}", timeout=8000)
        break
    except Exception as cdp_err:
        log.warning(f"CDP连接失败(尝试{cdp_attempt+1}/5): {cdp_err}")
        # 重启Edge再试...
```

CDP（Chrome DevTools Protocol）连接是让程序"遥控"已经打开的浏览器，这个浏览器和用户手动打开的没有区别。

**策略3：回退时修改webdriver标志**

```python
# 回退Chromium时添加反检测脚本
ctx.add_init_script("Object.defineProperty(navigator,'webdriver',{get:()=>undefined})")
```

> ⚠️ **简化说明**：实际代码还有更多反检测措施，如 `--disable-blink-features=AutomationControlled` 启动参数等。

### 12.4 CDP连接Edge（核心）

CDP连接是本项目的核心，它让程序使用"真正的用户浏览器"，从而绕过网站的反自动化检测。

#### 12.4.1 为什么不用标准Playwright启动？

标准Playwright启动的浏览器带有自动化标记：

```python
# ❌ 不推荐 - 这种启动方式会被检测
browser = p.chromium.launch()  # Playwright自己启动的Chromium
page = browser.new_page()
```

因为Playwright启动的浏览器会：
1. 设置 `navigator.webdriver = true`
2. 缺少真实的浏览器插件信息
3. 没有用户历史记录和cookies

#### 12.4.2 CDP连接流程

**步骤1：启动Edge并开启远程调试端口**

```python
# 启动Edge并开启远程调试
cmd = [
    edge_path,
    f"--remote-debugging-port={debug_port}",  # 开启9222端口
    f"--user-data-dir={edge_profile}",         # 使用用户数据目录
    "--no-first-run",
    "--disable-extensions",
    "https://www.bing.com",  # 初始页面避免白屏
]
_edge_proc = subprocess.Popen(cmd, ...)
```

**步骤2：等待CDP端口就绪**

```python
# 等待Edge启动就绪
for i in range(20):
    time.sleep(1)
    try:
        resp = urllib.request.urlopen(f"http://127.0.0.1:{debug_port}/json/version")
        log.info(f"CDP端口就绪 (第{i+1}秒)")
        break
    except:
        pass
else:
    raise Exception("Edge启动超时(20秒)")
```

**步骤3：通过CDP连接**

```python
# 通过CDP连接
browser = p.chromium.connect_over_cdp(f"http://127.0.0.1:{debug_port}", timeout=8000)
```

> ⚠️ **简化说明**：实际代码中CDP连接失败会重启Edge重试，最多重试5次。完整代码约130行。

#### 12.4.3 回退机制

如果CDP连接5次都失败，就回退使用Chromium：

```python
# 回退：使用Chromium
browser = p.chromium.launch(
    headless=False,
    args=['--disable-blink-features=AutomationControlled', '--no-sandbox', '--disable-dev-shm-usage'],
    proxy={"server": px} if px else None
)
```

> 💡 **PM桥梁**：这就像汽车的"备用轮胎"。平时用Edge（性能好、不被检测），万一CDP连不上Edge，再用Chromium兜底。

### 12.5 等待策略

自动化操作网页时，"等"是一个技术活：

```python
# 等待页面加载完成
page.goto("https://example.com", wait_until="domcontentloaded")

# 等待某个元素出现
page.wait_for_selector('.c-summary', timeout=10000)

# 等待固定时间（不推荐，但有时必要）
time.sleep(0.5)
```

**本项目的等待策略**：

| 场景 | 等待方式 | 原因 |
|------|----------|------|
| 页面导航 | `wait_until="domcontentloaded"` | 足够快 |
| 元素加载 | `wait_for_selector()` + 超时 | 防止无限等 |
| API请求 | 直接发请求 | 不依赖DOM |

### 12.6 定位元素

Playwright提供多种元素定位方式：

```python
# 按文本内容定位
page.locator('button:has-text("Add to Cart")')

# 按CSS选择器
page.locator('.c-summary')

# 按ID
page.locator('#submit-button')

# 取第一个匹配
page.locator('button').first

# 强制点击（跳过不可见检查）
btn.click(force=True)
```

**本项目常用的选择器**：

```python
# 来自 browser.py 的实际代码
page.locator('button:has-text("MAX")').first        # MAX按钮
page.locator('button:has-text("I AGREE")').first     # 同意条款
page.locator('.m-cartActionBar__button').first       # 购物车操作栏
page.locator('input[type="checkbox"]').first          # 复选框
```

### 12.7 页面交互

```python
# 点击按钮
btn.click()

# 输入文本
input_box.fill("hello")

# 滚动页面
page.evaluate("window.scrollTo(0, document.body.scrollHeight)")

# 执行JavaScript
result = page.evaluate("""
    () => {
        const btn = document.querySelector('button');
        return btn ? btn.innerText : null;
    }
""")
```

**备用JS点击**：

当Playwright的点击失败时，项目会使用JavaScript作为备用：

```python
# 来自 browser.py - 点MAX的备用方案
try:
    page.wait_for_selector('button:has-text("MAX")', timeout=5000)
    max_btn.click(force=True, timeout=3000)
except:
    # 备用JS点击
    page.evaluate("""() => {
        const btns = [...document.querySelectorAll('button')];
        const m = btns.find(b => (b.innerText||'').trim().toUpperCase() === 'MAX');
        if (m) m.click();
    }""")
```

> 💡 **PM桥梁**：这就像"主方案失败，用备用方案"。用户点按钮没反应，客服帮你手动操作。

### 12.8 截图与调试

```python
# 截图函数（来自 config.py）
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

**自动截图场景**：
- 成功：`SUCCESS.png`
- 失败：`CHECK.png`
- 错误：`error.png`

### 12.9 动手练习

创建一个脚本，用Playwright打开百度并搜索：

```python
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    browser = p.chromium.launch(headless=False)
    page = browser.new_page()
    
    # 打开百度
    page.goto("https://www.baidu.com")
    
    # 截图
    page.screenshot(path="baidu.png")
    
    # 在搜索框输入
    page.fill("#kw", "Playwright教程")
    
    # 点击搜索按钮
    page.click("#su")
    
    # 等待搜索结果
    page.wait_for_selector("#content_left")
    
    # 再截一张
    page.screenshot(path="baidu_result.png")
    
    browser.close()
```


## 第13章 面向对象入门

> 💡 **本章重点**：class、self、继承；RSIClient类实际方法

### 13.1 场景引入

想象你开了一家奶茶店：
- 每个员工都是**一个对象**
- 员工有**属性**（姓名、工号、岗位）
- 员工有**方法**（点单、制作奶茶、收银）

面向对象编程（OOP）就是用这种思路来组织代码：**把数据和操作数据的方法打包在一起**。

### 13.2 类和对象

**类（Class）**：模板/蓝图
**对象（Object）**：根据模板创建的实例

```python
# 定义一个奶茶店员类
class MilkTeaClerk:
    # __init__ 是构造函数，创建对象时自动调用
    def __init__(self, name, counter):
        self.name = name           # 属性：名字
        self.counter = counter     # 属性：工位
    
    def take_order(self, order):
        """方法：点单"""
        print(f"{self.name} 接单: {order}")
        return f"{order} 正在制作..."
    
    def serve(self, drink):
        """方法：出餐"""
        print(f"{self.name} 出餐: {drink}")

# 创建对象
clerk = MilkTeaClerk("小王", 1)
clerk.take_order("珍珠奶茶")
```

### 13.3 self是什么？

`self` 是**指向对象本身**的引用：

```python
class Counter:
    def __init__(self):
        self.count = 0  # self.count是这个对象的count属性
    
    def add(self):
        self.count += 1  # 给这个对象的count加1

c1 = Counter()
c2 = Counter()
c1.add()
c1.add()
c2.add()
print(c1.count)  # 2
print(c2.count)  # 1
```

### 13.4 RSIClient 类

这是本项目的API客户端类，封装了所有GraphQL操作：

```python
# 来自 api_client.py
class RSIClient:
    """RSI GraphQL API 客户端"""
    
    def __init__(self, page):
        self.page = page                      # 浏览器页面对象
        self.url = CFG["GRAPHQL_URL"]         # GraphQL接口地址
        self.billing_address_id = None        # 收货地址ID
        self.cart_data = {}                    # 购物车数据
        self.cart_total = 0.0                  # 购物车总价
    
    def gql(self, operation_name, variables=None, return_headers=False):
        """执行GraphQL请求"""
        # 详细实现见第15章
        pass
    
    def calibrate_time(self, samples=10, interval=0.5, deadline=0):
        """校准本地与服务器时间"""
        # 详细实现见第16章
        pass
    
    def add_to_cart_via_page(self):
        """页面加购（提取SKU然后调用GraphQL）"""
        pass
    
    def superfast_checkout(self):
        """极速API结账（伏击模式外的普通模式）"""
        # 1. 应用信用点
        # 2. NextStep × 2
        # 3. 验证购物车
        pass
    
    def superfast_ambush(self):
        """伏击模式预装填"""
        # 1. 读取购物车总价
        # 2. AddCreditMutation
        # 3. NextStepMutation × 2
        pass
    
    def ambush_validate(self):
        """伏击模式T-0验证"""
        # 带装填保护的validate
        pass
```

> ⚠️ **简化说明**：实际RSIClient还有 `apply_credits()`、`assign_address()`、`validate_cart()` 等方法，总计约500行代码。

### 13.5 方法调用示例

```python
# 创建客户端
client = RSIClient(page)

# 调用校准方法
offset = client.calibrate_time(samples=10, interval=0.5)
print(f"时间偏移: {offset:.3f}秒")

# 调用加购
success = client.add_to_cart_via_page()
if success:
    print("加购成功！")

# 调用结账
result = client.superfast_checkout()
```

### 13.6 动手练习

创建一个"购物车"类：

```python
class ShoppingCart:
    def __init__(self):
        self.items = []
        self.total = 0.0
    
    def add_item(self, name, price):
        self.items.append({"name": name, "price": price})
        self.total += price
    
    def remove_item(self, name):
        for i, item in enumerate(self.items):
            if item["name"] == name:
                self.total -= item["price"]
                self.items.pop(i)
                break
    
    def checkout(self):
        print(f"结账商品: {self.items}")
        print(f"总计: ${self.total:.2f}")

# 测试
cart = ShoppingCart()
cart.add_item("星之卡比", 59.99)
cart.add_item("塞尔达传说", 69.99)
cart.checkout()
```


## 第14章 项目架构解析

> 💡 **本章重点**：模块分工、配置详解、主入口流程

### 14.1 整体架构

```
RSI_MIEKCIK_BUY/
├── RSI_MIEKCIK_BUY.py    # 主入口（显示免责、解析参数、启动主循环）
└── modules/
    ├── __init__.py        # 包初始化
    ├── config.py          # 配置常量、GraphQL查询、日志工具
    ├── main.py            # 主逻辑（Playwright线程、主循环）
    ├── browser.py         # 浏览器创建、登录、页面/API模式
    ├── api_client.py      # GraphQL客户端（RSIClient类）
    ├── calibration.py     # 多级时间校准调度器
    ├── gui_config.py      # 配置界面（设置目标时间、关键词等）
    ├── gui_progress.py    # 进度界面（显示步骤、倒计时）
    ├── sku_bookmarks.py   # SKU收藏夹（列表结构）
    ├── sku_interceptor.py # SKU拦截器
    └── latency_test.py   # 延迟测试
```

### 14.2 配置详解（config.py）

**完整的配置项**：

```python
# 来自 config.py 第51-87行
CFG = {
    # ===== 目标设置 =====
    "TARGET_TIME": "2026-05-15 00:00:00",  # 抢购时间
    "TIMEZONE": "Asia/Shanghai",           # 时区（实际计算用UTC+8）
    
    # ===== 商品设置 =====
    "BROWSE_URL_PREFIX": "https://robertsspaceindustries.com/en/store/pledge/browse/extras/standalone-ships?page=1&sortField=price&sortDir=desc&keywords=",
    "SEARCH_KEYWORDS": "Kraken",           # 搜索关键词
    "ITEM_PRICE": 0,                        # 商品价格（0表示不限）
    "EXCLUDE_KEYWORDS": "",                 # 排除关键词（英文逗号分隔）
    "CART_URL": "https://robertsspaceindustries.com/en/store/pledge/cart",
    
    # ===== API设置 =====
    "GRAPHQL_URL": "https://robertsspaceindustries.com/graphql",
    "PROXY": None,                          # 代理（None表示直连）
    
    # ===== 执行模式 =====
    "MODE": "api",  # "api" = 页面加购+API付款, "page" = 纯页面模式
    
    # ===== 时间设置 =====
    "PRE_WARM": 60,           # 提前多少秒开始预热
    "PRE_REFRESH": 0.05,     # 提前多少秒刷新页面（50ms）
    "DELAY": 0.01,            # API模式极小延迟
    "TIMEOUT": 3,            # 浏览器操作超时（秒）
    
    # ===== 浏览器设置 =====
    "UA": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 ...",
    
    # ===== 高级设置 =====
    "INPUT_MODE": "intercept",  # "intercept"或"sku"
    "SKU_ID": "",               # 直接指定SKU ID
    "MANUAL_TIME_OFFSET": "-0.1",  # 手动时间偏移（秒）
    "AUTO_CALIBRATE": False,    # 是否自动校准时间
    
    # ===== 文件路径 =====
    "COOKIE_FILE": os.path.join(BASE_PATH, "scautobuy", "rsi_cookies.json"),
    "LOG_FILE": os.path.join(BASE_PATH, "scautobuy", "rsi_buy.log"),
    "SCREENSHOT_DIR": os.path.join(BASE_PATH, "scautobuy", "screenshots"),
}
```

> ⚠️ **重要变化**：教程旧版提到的 `TIME_OFFSET` 已被删除，新版本使用 `MANUAL_TIME_OFFSET` 和 `AUTO_CALIBRATE`。

> ⚠️ **注意**：`AMBUSH_MODE` 不在上面的 CFG 默认字典中！它是通过 GUI 配置窗口动态设置到 CFG 中的——用户在 GUI 中勾选"伏击模式"后，`gui_config.py` 才会把 `CFG["AMBUSH_MODE"]` 设为 True。所以直接改 config.py 是加不进伏击模式开关的。

### 14.3 启动流程

**入口**：`RSI_MIEKCIK_BUY.py`

```python
# 来自 RSI_MIEKCIK_BUY.py
if __name__ == "__main__":
    # 显示免责声明
    if not _show_disclaimer():
        sys.exit(0)
    
    args = get_args()
    if args.test:
        test()           # 测试模式
    else:
        run()            # 正常运行
```

**主流程**（`modules/main.py` 的 `run()` 函数）：

```
┌─────────────────────────────────────────────────────────┐
│ 1. 显示配置窗口（gui_config.py）                         │
│    - 用户设置目标时间、关键词、模式等                     │
│    - 点"开始抢购"后继续                                  │
└────────────────────────┬────────────────────────────────┘
                         ↓
┌─────────────────────────────────────────────────────────┐
│ 2. 创建GUI进度窗口（gui_progress.py）                    │
│    - 显示倒计时、步骤状态                                │
└────────────────────────┬────────────────────────────────┘
                         ↓
┌─────────────────────────────────────────────────────────┐
│ 3. Playwright线程（独立线程运行browser.py）              │
│    ┌─────────────────────────────────────────────────┐   │
│    │ 3.1 创建浏览器（优先CDP+Edge，回退Chromium）    │   │
│    │ 3.2 登录验证（加载cookies或手动登录）           │   │
│    │ 3.3 预热页面（伏击模式预热购物车）               │   │
│    │ 3.4 时间校准（根据剩余时间自适应策略）           │   │
│    │ 3.5 执行抢购（根据MODE分支）                   │   │
│    └─────────────────────────────────────────────────┘   │
└────────────────────────┬────────────────────────────────┘
                         ↓
┌─────────────────────────────────────────────────────────┐
│ 4. 主线程运行GUI mainloop（tkinter要求）                 │
│    - 更新倒计时显示                                      │
│    - 处理用户取消操作                                    │
└─────────────────────────────────────────────────────────┘
```

### 14.4 模式分支

```python
# 来自 main.py - 根据MODE分支
if CFG["MODE"] == "api":
    if cart_success:
        success = client.superfast_checkout()    # API极速付款
    else:
        success = run_page_mode(page)             # 降级页面模式
else:
    success = run_page_mode(page)                 # 纯页面模式

# 伏击模式（AMBUSH_MODE）走独立分支
if CFG.get("AMBUSH_MODE", False):
    client.superfast_ambush()    # T-30s预装填
    # 等待T-0...
    client.ambush_validate()     # T-0验证
```

### 14.5 模块职责表

| 模块 | 职责 | 关键类/函数 |
|------|------|------------|
| config.py | 配置常量、GraphQL查询、日志 | `CFG`, `QUERIES`, `log`, `get_target()` |
| main.py | 主循环、线程管理 | `run()`, `_run_playwright_thread()` |
| browser.py | 浏览器操作 | `create_browser()`, `login()`, `run_api_mode()`, `run_page_mode()` |
| api_client.py | GraphQL请求封装 | `RSIClient` 类 |
| calibration.py | 时间校准调度 | `CalibrationScheduler` 类 |
| gui_config.py | 配置输入界面 | `_show_config_dialog()` |
| gui_progress.py | 进度显示界面 | `RSIGUI` 类 |
| sku_bookmarks.py | SKU收藏管理 | `load_bookmarks()`, `add_bookmark()` |


## 第15章 网络请求与API

> 💡 **本章重点**：urllib发送GraphQL请求、RSIClient.gql()核心流程

### 15.1 urllib基础

**项目用urllib而不是requests**。urllib是Python内置库，不需要额外安装。

#### 15.1.1 urllib vs requests

| | urllib | requests |
|---|--------|----------|
| 安装 | 内置，无需安装 | 需要 `pip install requests` |
| 语法 | 稍复杂 | 更简洁 |
| 功能 | 基础够用 | 功能更丰富 |

```python
# urllib方式（项目实际用）
import urllib.request
import json

req = urllib.request.Request(
    "https://api.example.com",
    data=json.dumps({"key": "value"}).encode('utf-8'),  # POST数据
    headers={'Content-Type': 'application/json'}         # 请求头
)

with urllib.request.urlopen(req, timeout=10) as resp:
    result = json.loads(resp.read().decode('utf-8'))
```

```python
# requests方式（教程旧版误导）
import requests

resp = requests.post(
    "https://api.example.com",
    json={"key": "value"}  # 更简洁的写法
)
result = resp.json()
```

> ⚠️ **重要**：不要被教程旧版误导去安装requests。本项目完全使用urllib。

#### 15.1.2 urllib请求体结构

```python
# urllib发送JSON数据
import json
import urllib.request

payload = {
    "operationName": "AddCartMultiItemMutation",
    "variables": {"query": [...]},
    "query": "mutation AddCartMultiItemMutation(...) {...}"
}

req = urllib.request.Request(
    url,
    data=json.dumps([payload]).encode('utf-8'),  # 注意是数组格式
    headers={'Content-Type': 'application/json'}
)
```

### 15.2 GraphQL请求原理

GraphQL是一种API查询语言，和传统REST的区别：

| | REST | GraphQL |
|---|------|---------|
| 请求方式 | GET/POST多个端点 | POST一个端点 |
| 返回数据 | 固定格式 | 你要什么返回什么 |
| 一次获取 | 多个资源需要多次请求 | 可以一次请求多个 |

GraphQL请求格式：

```json
// 请求
{
  "operationName": "MiniCartWidgetInitializationQuery",
  "variables": {"storeFront": "pledge"},
  "query": "query MiniCartWidgetInitializationQuery($storeFront: String!) {...}"
}

// 响应
{
  "data": {
    "store": {
      "cart": {
        "lineItemsQties": 2
      }
    }
  }
}
```

### 15.3 RSIClient.gql() 核心流程

这是API客户端的核心方法，封装了所有GraphQL操作：

```python
# 来自 api_client.py 第35-100行
def gql(self, operation_name: str, variables: Dict = None, return_headers: bool = False) -> Dict:
    """执行GraphQL请求（使用urllib，从浏览器取cookies）
    
    流程：
    1. 检查是否取消
    2. 构造请求payload
    3. 从浏览器获取cookies
    4. 从页面获取CSRF token
    5. 发送请求
    6. 解析响应
    """
    import urllib.request
    
    # 检查用户是否取消
    gui = config.get_gui()
    if gui and gui.is_cancel_clicked():
        raise Exception("用户取消抢购")
    
    # 构造payload
    query = QUERIES.get(operation_name, "")  # 从config获取query
    payload = [{
        "operationName": operation_name,
        "variables": variables or {},
        "query": query.strip()
    }]
    
    # 从浏览器获取cookies
    cookie_str = ""
    try:
        browser_cookies = self.page.context.cookies()
        cookie_parts = []
        for c in browser_cookies:
            if c.get('domain', '').endswith('robertsspaceindustries.com'):
                cookie_parts.append(f"{c['name']}={c['value']}")
        cookie_str = "; ".join(cookie_parts)
    except Exception as e:
        log.warning(f"获取浏览器cookies异常: {e}")
    
    # 从页面获取CSRF token
    csrf_token = ""
    try:
        csrf_token = self.page.evaluate("""
            () => {
                const meta = document.querySelector('meta[name="csrf-token"]');
                return meta ? meta.getAttribute('content') : '';
            }
        """)
    except:
        pass
    
    # 构造请求
    req = urllib.request.Request(
        self.url,
        data=json.dumps(payload).encode('utf-8'),
        headers={
            'Content-Type': 'application/json',
            'Accept': '*/*',
            'accept-language': 'en',
            'Cookie': cookie_str,
            'Referer': self.page.url,
        }
    )
    if csrf_token:
        req.add_header('x-csrf-token', csrf_token)
    
    # 发送请求
    log.info(f"📤 发送: op={operation_name}, cookies={len(cookie_str.split('; ')) if cookie_str else 0}个")
    
    with urllib.request.urlopen(req, timeout=10) as resp:
        result = json.loads(resp.read().decode('utf-8'))
    
    # 解析响应（可能是数组格式）
    if isinstance(result, list):
        for item in result:
            if isinstance(item, dict) and item.get('data'):
                return item
        return result[0] if result else {}
    return result or {}
```

> ⚠️ **简化说明**：实际代码还有HTTP错误处理、响应头时间提取等功能。

### 15.4 项目的GraphQL查询

config.py中定义了所有GraphQL查询：

```python
# 来自 config.py 第89-200行
QUERIES = {
    # 1. 加购
    "AddCartMultiItemMutation": '''
mutation AddCartMultiItemMutation($query: [CartAddInput!], $storeFront: String = "pledge") {
  store(name: $storeFront) {
    cart {
      mutations {
        addMany(query: $query) {
          count
          __typename
        }
        __typename
      }
      __typename
    }
    __typename
  }
}
''',
    
    # 2. 应用信用点
    "AddCreditMutation": '''
mutation AddCreditMutation($amount: Float!, $storeFront: String) {
  store(name: $storeFront) {
    cart {
      mutations {
        credit_update(amount: $amount)
        __typename
      }
      __typename
    }
    ...
}
''',
    
    # 3. 下一步
    "NextStepMutation": '''
mutation NextStepMutation($storeFront: String) {
  store(name: $storeFront) {
    cart {
      hasDigital
      mutations {
        flow {
          moveNext
          __typename
        }
        __typename
      }
      ...
    }
    ...
}
''',
    
    # 4. 绑定地址
    "CartAddressAssignMutation": '''
mutation CartAddressAssignMutation($billing: ID, $shipping: ID, $storeFront: String) {
  store(name: $storeFront) {
    cart {
      mutations {
        assignAddresses(assign: {billing: $billing, shipping: $shipping})
        __typename
      }
      __typename
    }
    __typename
  }
}
''',
    
    # 5. 确认付款
    "CartValidateCartMutation": '''
mutation CartValidateCartMutation($storeFront: String, $token: String, $mark: String) {
  store(name: $storeFront) {
    cart {
      mutations {
        validate(mark: $mark, token: $token)
        __typename
      }
      __typename
    }
    ...
  }
}
''',
    
    # 6. 获取购物车信息（轻量查询，用于时间校准）
    "MiniCartWidgetInitializationQuery": '''
query MiniCartWidgetInitializationQuery($storeFront: String!) {
  store(name: $storeFront) {
    cart {
      id
      lineItemsQties
      __typename
    }
    __typename
  }
}
''',
    
    # 7. 获取地址簿
    "AddressBookQuery": '''
query AddressBookQuery {
  store {
    addressBook {
      id
      defaultBilling
      company
      firstname
      lastname
      ...
    }
  }
}
''',
}
```

### 15.5 superfast_checkout() 流程

普通模式的API结账流程：

```python
# 来自 api_client.py 的 superfast_checkout() 方法
def superfast_checkout(self) -> Dict:
    """极速API结账"""
    # 步骤1: 应用信用点（用购物车总价）
    log.info("📍 [应用信用点]")
    total = self.cart_total
    if total <= 0:
        log.warning("⚠️ 购物车总价为0，跳过信用点")
    else:
        result = self.gql("AddCreditMutation", {"amount": total, "storeFront": "pledge"})
    
    # 步骤2: NextStep × 2（跳过地址绑定，使用默认地址）
    log.info("📍 [NextStep 1]")
    result = self.gql("NextStepMutation", {"storeFront": "pledge"})
    
    log.info("📍 [NextStep 2]")
    result = self.gql("NextStepMutation", {"storeFront": "pledge"})
    order_slug = result.get('data', {}).get('store', {}).get('order', {}).get('slug', '')
    
    # 步骤3: 验证购物车（带token）
    log.info("📍 [验证购物车]")
    # 获取token逻辑（略）
    result = self.gql("CartValidateCartMutation", {...})
    
    return {"success": True}
```

> ⚠️ **简化说明**：实际代码还有token提取、地址绑定、异常重试等逻辑。

### 15.6 动手：用urllib发请求

写一个简单的测试脚本：

```python
import urllib.request
import json

url = "https://api.github.com/graphql"

headers = {
    'Authorization': 'token YOUR_TOKEN_HERE',
    'Content-Type': 'application/json',
}

payload = {
    "query": """
    {
      viewer {
        login
        name
      }
    }
    """
}

req = urllib.request.Request(
    url,
    data=json.dumps(payload).encode('utf-8'),
    headers=headers
)

try:
    with urllib.request.urlopen(req, timeout=10) as resp:
        result = json.loads(resp.read().decode('utf-8'))
        print(json.dumps(result, indent=2, ensure_ascii=False))
except Exception as e:
    print(f"请求失败: {e}")
```


## 第16章 时间校准

> 💡 **本章重点**：calibrate_time()算法原理、不同时间窗口的校准策略

### 16.1 为什么需要校准？

**问题**：本地时钟 ≠ 服务器时钟

```
本地时间: 2024-05-15 00:00:00.500  (快了0.5秒)
服务器时间: 2024-05-15 00:00:00.000

如果目标是T=0，本地以为已经过了0.5秒，实际还没到！
```

**影响**：
- 提前开枪 → 超时/失败
- 延后开枪 → 商品已被抢光

### 16.2 校准算法原理

#### 16.2.1 基础原理

从HTTP响应头获取服务器时间：

```
t1 (发送请求) ──────→ 服务器收到 ──────→ t2 (收到响应)
                      ↓
                   Date头
                      ↓
                   服务器时间: T_server
                   
本地估计的中间时间: T_mid = (t1 + t2) / 2
时间偏移 = T_mid - T_server
```

#### 16.2.2 算法的三个步骤

**步骤1：多次采样**

```python
# 发送10次请求，记录每次的(偏移值, RTT)
raw_samples = []  # [(offset, rtt), ...]
for i in range(10):
    t1 = time.time()
    resp = urllib.request.urlopen(req, timeout=10)
    t2 = time.time()
    server_time = parse_http_date(resp.headers['Date'])
    offset = (t1 + t2) / 2 - server_time
    raw_samples.append((offset, t2 - t1))
    time.sleep(0.5)  # 间隔0.5秒
```

**步骤2：RTT异常过滤**

网络波动可能导致某次请求特别慢，过滤掉异常值：

```python
# 剔除RTT > 中位数2倍的样本
rtts = [s[1] for s in raw_samples]
median_rtt = statistics.median(rtts)
filtered_samples = [s for s in raw_samples if s[1] <= median_rtt * 2]
```

**步骤3：1秒密度窗口**

```
时间轴上的采样点分布：

  偏移值
     │
  0.3├                    ●
     │           ●              ●
  0.2├    ●         ●       ●
     │
  0.1├                            ●
     │────────────────────────────────────────→ 时间
    
    0.5s      1.0s      1.5s      2.0s
    
真实偏移一定在"密度最高的1秒窗口"内
```

取窗口内最低25%样本，减去Uniform顺序统计量偏置：

```python
# 找1秒滑动窗口内点数最多的区间
best_window = find_densest_window(filtered_samples, window_size=1.0)

# 取最低25%样本
bottom_quartile = sorted(best_window)[:len(best_window)//4]

# 减去顺序统计量偏置（约n/(2N)）
bias = len(bottom_quartile) / (2 * len(best_window))
final_offset = median(bottom_quartile) - bias
```

> ⚠️ **简化说明**：实际代码使用更复杂的统计算法，约200行。关键是理解原理，不必掌握每行代码。

### 16.3 AUTO_CALIBRATE vs MANUAL_TIME_OFFSET

```python
# 来自 config.py
"AUTO_CALIBRATE": False,      # 是否启用自动校准
"MANUAL_TIME_OFFSET": "-0.1", # 手动微调（秒）
```

**两种模式**：

| 模式 | 说明 | 适用场景 |
|------|------|----------|
| AUTO_CALIBRATE=True | 程序自动计算偏移 | 网络稳定、多次抢购 |
| AUTO_CALIBRATE=False | 用MANUAL_TIME_OFFSET固定值 | 网络不稳定、快速测试 |

### 16.4 不同时间窗口的校准策略

```python
# 来自 main.py 的校准策略
_target_raw = config.get_target()
time_to_target = _target_raw - time.time()

if time_to_target < 0:
    # T-0已过，跳过校准直接抢
    server_offset = manual_offset if manual_offset != 0.0 else 0.0
    
elif not auto_calibrate:
    # 自动校准未启用，跳过校准
    server_offset = manual_offset
    
elif time_to_target < 15:
    # 距T-0不足15秒：快速校准3次
    if time_to_target < 8:
        # 不足8秒：直接用手动偏移
        server_offset = manual_offset if manual_offset != 0.0 else 0.0
    else:
        server_offset = client.calibrate_time(samples=3, interval=0.3)
    
else:
    # 正常情况：完整校准10次
    server_offset = client.calibrate_time()

# 叠加手动微调
if manual_offset != 0.0:
    server_offset += manual_offset
```

**校准策略表**：

| 距T-0剩余 | 校准策略 | 采样次数 | 间隔 |
|-----------|----------|----------|------|
| ≥15秒 | 完整校准 | 10次 | 0.5秒 |
| 8~14秒 | 快速校准 | 3次 | 0.3秒 |
| <8秒 | 跳过校准 | - | - |
| <0秒 | T-0已过 | - | - |

### 16.5 多级校准调度器

calibration.py中的 `CalibrationScheduler` 类提供了更精细的调度：

```python
# 来自 calibration.py
class CalibrationScheduler:
    """多级时间校准调度器
    
    校准方案：
    - 启动时：10次采样（粗校准）
    - 距T-0>60s：每30秒校准一次（修正漂移）
    - T-60s：10次采样（中校准）
    - T-30s：10次采样（精校准）
    - T-10s：10次采样0.3s间隔（最终精校准）
    """
    
    def check_and_calibrate(self) -> float:
        """检查是否到达校准时间点，执行校准"""
        for calib_target, calib_id in self.calib_points:
            if calib_id not in self.calib_done and time.time() >= calib_target:
                self.calib_done.add(calib_id)
                
                # T-20s及以内用0.3s间隔，其他0.5s间隔
                dist = self.target - calib_target
                samples = 10
                interval = 0.3 if dist <= 25 else 0.5
                
                # T-5s加deadline保护
                deadline = self.target - 2 if dist <= 10 else 0
                
                new_offset = self.client.calibrate_time(
                    samples=samples, 
                    interval=interval, 
                    deadline=deadline
                )
```

### 16.6 动手理解校准

用Python验证校准原理：

```python
import time
from datetime import datetime
from email.utils import parsedate_to_datetime
import urllib.request

def simple_calibrate():
    """简化版校准：只校准1次"""
    url = "https://robertsspaceindustries.com/graphql"
    
    # 发送请求
    req = urllib.request.Request(
        url,
        data=b'{"operationName":"MiniCartWidgetInitializationQuery","variables":{"storeFront":"pledge"},"query":"query MiniCartWidgetInitializationQuery($storeFront: String!) { store(name: $storeFront) { cart { id lineItemsQties __typename } __typename } }"}',
        headers={'Content-Type': 'application/json'}
    )
    
    t1 = time.time()
    with urllib.request.urlopen(req, timeout=10) as resp:
        t2 = time.time()
        date_str = resp.headers.get('Date')
    
    # 解析服务器时间
    server_dt = parsedate_to_datetime(date_str)
    server_ts = server_dt.timestamp()
    
    # 计算偏移
    mid = (t1 + t2) / 2
    offset = mid - server_ts
    
    print(f"发送时间: {t1:.3f}")
    print(f"收到时间: {t2:.3f}")
    print(f"服务器时间: {server_ts:.3f}")
    print(f"中间时间: {mid:.3f}")
    print(f"时间偏移: {offset:+.3f}秒")
    
    return offset

# 测试
offset = simple_calibrate()
print(f"\n本地{'快' if offset > 0 else '慢'}服务器 {abs(offset):.3f}秒")
```


## 第17章 伏击模式

> 💡 **本章重点**：superfast_ambush()、ambush_validate()、伏击vs普通模式差异

### 17.1 什么是伏击模式？

**普通模式**：T-0时开始操作
- 打开页面
- 点加购
- API结账
- **耗时：2-5秒**

**伏击模式**：提前埋伏，T-0时极速验证
- T-30s：提前加购、进入结算流程
- T-0：直接发送验证请求
- **耗时：0.1-0.5秒**

```
普通模式时间线：
        T-0              T-2              T-5
        ↓                ↓                ↓
   ─────●────────────────●────────────────●─────
        开始操作      信用点+NextStep    验证完成

伏击模式时间线：
        T-30             T-0
        ↓                ↓
   ─────●────────────────●─────
        预装填完成    0.1秒验证
```

### 17.2 伏击模式核心函数

#### 17.2.1 superfast_ambush() 预装填

```python
# 来自 api_client.py
def superfast_ambush(self) -> bool:
    """伏击模式极速结账
    
    前提：物品已在购物车
    流程：
    1. 从购物车页面DOM读取商品总价
    2. T-30s执行预装填：AddCreditMutation → NextStepMutation × 2
    3. T-0s执行：CartValidateCartMutation
    """
    log.info("📍 [伏击模式] 开始执行...")
    
    # 步骤1: 读取购物车总价
    cart_info = self.get_cart_items_from_page()
    items = cart_info.get('items', [])
    total = cart_info.get('total', 0)
    
    if not items:
        log.error("❌ 购物车为空，无法执行伏击模式")
        return False
    
    # 如果total为0，累加所有商品价格
    if total <= 0:
        total = sum(item['price'] for item in items)
    
    log.info(f"   商品总价: ${total}")
    
    # 步骤2: 应用信用点
    log.info("📍 [伏击模式] 应用信用点...")
    result = self.gql("AddCreditMutation", {"amount": total, "storeFront": "pledge"})
    time.sleep(0.1)
    
    # 步骤3: NextStep
    log.info("📍 [伏击模式] NextStep...")
    result = self.gql("NextStepMutation", {"storeFront": "pledge"})
    time.sleep(0.1)
    
    # 步骤4: 再次NextStep（跳过地址绑定）
    log.info("📍 [伏击模式] NextStep (确认)...")
    result = self.gql("NextStepMutation", {"storeFront": "pledge"})
    
    log.info("📍 [伏击模式] 预装填完成，等待T-0...")
    self.cart_data['ambush_ready'] = True
    return True
```

#### 17.2.2 ambush_validate() T-0验证

```python
# 来自 api_client.py
def ambush_validate(self) -> bool:
    """伏击模式 - T-0时刻执行最终验证（带装填保护）
    
    validate失败后检查流程状态，如流程丢失则重新装填再重试
    """
    refill_count = 0
    
    for attempt in range(1, 5):
        log.info(f"📍 [伏击模式] T-0 执行验证... (尝试 {attempt}/4)")
        
        # 发送验证请求
        result = self.gql("CartValidateCartMutation", {
            "token": "",
            "mark": "",
            "storeFront": "pledge"
        })
        
        # 检查结果
        validate_result = result.get('data', {}).get('store', {}).get('cart', {}).get('mutations', {}).get('validate', '')
        order_slug = result.get('data', {}).get('store', {}).get('order', {}).get('slug', '')
        
        if validate_result or order_slug:
            order_num = order_slug or validate_result
            log.info(f"   🎉 付款成功! 订单号: {order_num}")
            # 跳转到机库
            try:
                self.page.evaluate("window.setTimeout(() => window.location.href = 'https://robertsspaceindustries.com/en/account/pledges', 100)")
            except:
                pass
            return True
        
        # validate失败，检查流程是否还在
        if attempt < 4:
            check = self.gql("NextStepMutation", {"storeFront": "pledge"})
            check_move = check.get('data', {}).get('store', {}).get('cart', {}).get('mutations', {}).get('flow', {}).get('moveNext', False)
            
            if check_move:
                # 流程还在，0.2秒后重试
                log.warning("⚠️ 验证失败，流程仍在，0.2秒后重试validate...")
                time.sleep(0.2)
            else:
                # 流程丢失，重新装填
                if refill_count < 2:
                    refill_count += 1
                    log.warning(f"⚠️ 流程丢失，重新装填 ({refill_count}/2)...")
                    if self._ambush_refill():
                        time.sleep(0.2)
                    else:
                        log.error("❌ 重新装填失败")
                        time.sleep(0.3)
                else:
                    log.error("❌ 超过最大重装次数，放弃")
                    break
    
    return False
```

> ⚠️ **简化说明**：上面的代码调用了 `self._ambush_refill()`，这是一个内部方法，作用是"重新装填"——重新应用信用点（`AddCreditMutation`）+ 推进两步（`NextStepMutation` ×2）。当 validate 失败且流程丢失时，用它恢复装填状态再重试验证。最多允许重装2次。

### 17.3 伏击 vs 普通模式对比

| 特性 | 普通模式 | 伏击模式 |
|------|----------|----------|
| 开启条件 | `AMBUSH_MODE=False` | `AMBUSH_MODE=True` |
| 预热页面 | 搜索列表页 | 购物车页面 |
| 加购时机 | T-0刷新后 | 提前加购 |
| 结账流程 | 完整流程 | 预装填+快速验证 |
| 理论耗时 | 2-5秒 | 0.1-0.5秒 |
| 成功率 | 较高 | 最高 |
| 风险 | 可能超时 | 需提前确保购物车正确 |

### 17.4 步骤差异

gui_progress.py的 `RSIGUI.__init__()` 中定义了两种模式的步骤数组：

```python
# 来自 gui_progress.py
# 普通模式步骤
self.steps = [
    ("calibrate", "时间校准..."),
    ("wait", "等待抢购时间..."),
    ("login", "登录验证..."),
    ("warmup", "预热加载..."),
    ("cart", "加入购物车..."),
    ("checkout", "极速结账..."),
    ("done", "完成！"),
]

# 伏击模式步骤
self.ambush_steps = [
    ("ambush", "伏击预装填..."),
    ("wait", "等待T-0卡点..."),
    ("checkout", "T-0验证..."),
    ("done", "完成！"),
]
```

> ⚠️ **简化说明**：两套步骤都在 `__init__` 中一次性定义好，运行时根据 `AMBUSH_MODE` 选择用哪套。"done"步骤不是成功后动态添加的，而是初始化时就有的，只有执行到这一步才会被标记为完成。

### 17.5 为什么能做到1-2秒结账？

1. **提前加购**：不用等T-0再点加购
2. **预装填**：T-30s已经把信用点、地址等步骤跑完
3. **直接验证**：T-0只发一个GraphQL请求验证
4. **装填保护**：万一验证失败，0.2秒内重试或重新装填

```
伏击模式时间分解：
T=0.0s    发送 CartValidateCartMutation
T=0.1s    收到响应，付款成功
T=0.2s    跳转机库

总耗时：约0.2秒
```

### 17.6 使用伏击模式的注意事项

1. **提前确保商品正确**：伏击模式不会重新确认商品
2. **购物车清空**：确保购物车只有要抢的商品
3. **时间校准**：伏击模式对时间精度要求更高
4. **网络稳定**：需要稳定的网络连接


## 第18章 项目实战总结

> 💡 **本章重点**：完整流程串联、关键设计决策回顾、常见问题调试

### 18.1 完整执行流程串联

```
┌─────────────────────────────────────────────────────────────────────┐
│                         启动阶段                                     │
├─────────────────────────────────────────────────────────────────────┤
│ 1. RSI_MIEKCIK_BUY.py                                               │
│    - 显示免责声明                                                    │
│    - 解析命令行参数                                                  │
│    - 调用 main.run()                                                │
├─────────────────────────────────────────────────────────────────────┤
│ 2. gui_config.py - 配置窗口                                         │
│    - 用户输入：目标时间、关键词、模式（普通/伏击）                      │
│    - 点击"开始抢购"保存配置                                          │
├─────────────────────────────────────────────────────────────────────┤
│ 3. main.py - 初始化                                                  │
│    - 创建 RSIGUI 进度窗口                                            │
│    - 启动 Playwright 线程                                            │
│    - 主线程运行 GUI mainloop                                         │
└─────────────────────────────────────────────────────────────────────┘
                                    ↓
┌─────────────────────────────────────────────────────────────────────┐
│                         Playwright线程                              │
├─────────────────────────────────────────────────────────────────────┤
│ 4. browser.py - create_browser()                                   │
│    - 查找Edge安装路径                                                │
│    - 优先CDP连接Edge（无自动化标记）                                  │
│    - 回退启动Chromium（带反检测脚本）                                 │
├─────────────────────────────────────────────────────────────────────┤
│ 5. browser.py - login()                                             │
│    - 加载本地cookies                                                 │
│    - 无cookies则提示用户登录                                          │
│    - 登录成功后保存cookies                                           │
├─────────────────────────────────────────────────────────────────────┤
│ 6. main.py - 预热阶段                                               │
│    - 普通模式：打开搜索列表页                                         │
│    - 伏击模式：打开购物车页面                                         │
│    - 提示用户清空购物车（伏击模式跳过）                               │
├─────────────────────────────────────────────────────────────────────┤
│ 7. main.py - 时间校准                                               │
│    - 计算距T-0剩余时间                                               │
│    - 根据剩余时间选择校准策略                                         │
│    - 叠加手动时间偏移                                                │
└─────────────────────────────────────────────────────────────────────┘
                                    ↓
                    ┌─────────────────┴─────────────────┐
                    ↓                                   ↓
    ┌───────────────────────────┐       ┌───────────────────────────┐
    │     普通模式分支            │       │     伏击模式分支          │
    ├───────────────────────────┤       ├───────────────────────────┤
    │ T-10s 刷新页面             │       │ T-30s superfast_ambush() │
    │ T-0   add_to_cart_via_page│       │   - 读取购物车总价        │
    │ T+X   superfast_checkout()│       │   - AddCreditMutation    │
    │       - AddCreditMutation │       │   - NextStepMutation × 2  │
    │       - NextStepMutation×2│       │                           │
    │       - validate_cart     │       │ 等待T-0...                │
    │                           │       │                           │
    │       耗时：2-5秒          │       │ T-0 ambush_validate()    │
    └───────────────────────────┘       │   - CartValidateCart      │
                                        │   - 装填保护重试           │
                                        │                           │
                                        │   耗时：0.1-0.5秒          │
                                        └───────────────────────────┘
                                    ↓
┌─────────────────────────────────────────────────────────────────────┐
│                         结果处理                                     │
├─────────────────────────────────────────────────────────────────────┤
│ - 成功：显示成功弹窗、跳转机库、发送系统通知                          │
│ - 失败：显示失败弹窗、截图保存                                       │
│ - 用户确认后返回配置界面                                             │
└─────────────────────────────────────────────────────────────────────┘
```

### 18.2 关键设计决策回顾

#### 18.2.1 为什么用CDP连接Edge？

| 方案 | 优点 | 缺点 |
|------|------|------|
| Playwright启动Chromium | 简单 | 有自动化标记，易被检测 |
| **CDP连接Edge** | 无自动化标记，用户数据共享 | 需要Edge环境 |
| Selenium | 通用性好 | 更慢，标记更多 |

**决策**：优先用CDP连接Edge，模拟真实用户操作。

#### 18.2.2 为什么用urllib而不是requests？

- urllib是Python内置库，**无需pip install**
- 项目目标是**开箱即用**
- urllib性能足够满足需求

#### 18.2.3 为什么混合API+页面模式？

- **页面加购**：绕过SKU提取难题，兼容性强
- **API结账**：速度快，1-2秒完成

**决策**：取长补短，速度与兼容性兼顾。

#### 18.2.4 为什么需要伏击模式？

普通模式在T-0时需要完成：
1. 页面刷新
2. 点击加购
3. API结账

即使每步0.5秒，也要1.5秒起步。伏击模式把第2、3步提前到T-30s，T-0只做验证。

### 18.3 常见问题与调试

#### 18.3.1 CDP连接失败

```
错误：CDP连接5次均失败
原因：Edge未正常关闭、端口占用、防病毒软件阻止
解决：
1. 手动关闭所有Edge窗口
2. 重启电脑
3. 检查防火墙设置
```

#### 18.3.2 登录失败

```
错误：Cookies加载失败，重新登录
原因：cookies文件过期、浏览器版本更新
解决：
1. 删除 scautobuy/rsi_cookies.json
2. 重新运行，手动登录
```

#### 18.3.3 时间校准偏差大

```
现象：校准偏移 > 0.5秒
原因：网络不稳定、NTP同步问题
解决：
1. 启用 MANUAL_TIME_OFFSET 微调
2. 多次校准取平均值
3. 换用有线网络
```

#### 18.3.4 伏击模式失败

```
原因1：购物车不是预期商品
解决：确保伏击前购物车只有目标商品

原因2：T-0前购物车超时失效
解决：缩短预装填到T-0的间隔

原因3：流程丢失
解决：ambush_validate()有自动重装填机制
```

### 18.4 截图与日志

**截图保存位置**：

```python
SCREENSHOT_DIR = os.path.join(BASE_PATH, "scautobuy", "screenshots")
```

| 文件名 | 含义 |
|--------|------|
| `SUCCESS_*.png` | 抢购成功 |
| `CHECK_*.png` | 需要人工确认 |
| `DRY_RUN_*.png` | 测试模式完成 |
| `error_*.png` | 发生错误 |

**日志文件**：

```python
LOG_FILE = os.path.join(BASE_PATH, "scautobuy", "rsi_buy.log")
```

查看日志：

```bash
# Windows
type scautobuy\rsi_buy.log

# Linux/Mac
cat scautobuy/rsi_buy.log
```

### 18.5 下一步：你可以怎么改这个项目

#### 18.5.1 功能扩展

1. **多商品抢购**：同时监控多个商品
2. **价格提醒**：商品上架前发送通知
3. **自动重试**：结账失败后自动重试
4. **Webhook通知**：成功/失败后发送HTTP回调

#### 18.5.2 性能优化

1. **更精准的校准**：使用NTP服务器校准
2. **并行请求**：同时发送多个GraphQL请求
3. **预连接**：提前建立TCP连接

#### 18.5.3 代码改进

1. **类型注解**：添加完整的类型提示
2. **单元测试**：用pytest写测试用例
3. **配置外置**：把敏感配置放.env文件
4. **日志分级**：INFO/DEBUG/WARNING分级输出

### 18.6 恭喜完成实战部分！

你现在应该理解：

- ✅ Playwright浏览器自动化（CDP+Edge优先）
- ✅ urllib发送GraphQL请求
- ✅ 时间校准算法原理
- ✅ 伏击模式极速结账
- ✅ 项目整体架构

**下一步建议**：
1. 阅读完整源码（`/root/Projects/RSI_MIEKCIK_BUY/modules/`）
2. 尝试修改配置参数
3. 在测试模式下运行程序
4. 动手改进某个模块


> 📚 **继续学习**：
> - 《Python官方文档》https://docs.python.org/zh-cn/3/
> - 《Playwright文档》https://playwright.dev/python/
> - 《GraphQL中文网》https://graphql.cn/
