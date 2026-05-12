# RSI抢购脚本 — Harness与规则

> 触发：涉及RSI项目任何代码修改

## 代码修改原则
- **局部修改**：只改需要改的部分，严禁全量重写整个文件
- **⚠️ 严禁自我创造**：有参考代码（V1/V3）时，必须对照参考代码实现，不要凭想象编造。用户原话："为什么要胡整，为什么自我创造，为什么不按我的需求走"
- **⚠️ 已有能跑的代码直接搬，不要"理解后重写"**——重写=引入新bug。看懂了逻辑再用自己的方式实现，看似省事实则引入偏差
- **⚠️ 先确认代码路径再搬**：V1有多个模式时，必须搞清实际走的是哪个函数，从那个函数搬。V1能跑 ≠ 你理解的V1能跑
- **⚠️ 选择器/技术事实必须可验证**：修改CSS选择器、API接口等技术细节前，必须用现有工作代码或实际页面验证，不能凭子agent一句话就改
- **⚠️ 选择器必须有出处**：任何DOM选择器，必须能在参考代码的对应函数中找到原文。找不到=不能写

## RSI项目HARNESS规则
1. 严禁全量重写文件，必须使用edit_file的replace_one/replace_all/append/append_newline模式
2. 每次修改前必须用read_file读取目标文件相关部分，精确定位修改位置
3. old_string必须精确匹配原文（包括缩进、空格、换行），确保只修改目标内容
4. 改动多时分多次replace_one操作，每次只修改一小段内容
5. 绝对禁止使用write_file重写整个文件

## sub-agent harness必须完整
任务描述必须包含所有已知修复和"为什么这样写"的上下文

## 模块化开发
- 800行以上必须拆模块，1200行还在单文件就是事故
- 主agent先定位行号范围，只把那部分发给sub-agent改，改完用edit_file精确替换

## .pyd缓存坑
Cython编译后.pyd优先级高于.py，改源码不删.pyd = 没改。开发阶段删掉所有.pyd，只发布时编译

## tkinter GUI harness
### 基础规则
- 输入框 bg="white" fg="black"，bg=""空字符串报TclError
- GUI背景色统一 #A8B4D4
- ImageTk.PhotoImage 必须保持引用否则被GC回收
- ttk.Combobox 不支持bg参数
- padx 不支持元组格式(2,8)

### 窗口规范
- 配置窗口630×720，清空购物车弹窗630×480，高级设置弹窗570×480（pack布局）
- 横排布局：标签和输入框同一行
- 滑动开关：左=正面硬刚（蓝色调），右=伏击模式（红橙色调）
- 背景图resize到630×720
- 三种输入模式：SKU ID直购、关键词搜索、刷新拦截

### 弹窗/按钮规则
- 失败弹窗点确认后返回配置窗口
- 取消按钮在进度窗口底部（rely=0.92, anchor='s', y=-15）
- 结果弹窗靠右对齐（relx=0.85, anchor='e'），清空购物车弹窗居中
- 署名Label用 bg=GUI_BG_COLOR 替代 root.cget('bg')

### 线程安全
- Python 3.14 tkinter不允许从非主线程调after/after_idle
- 方案：queue + after() 50ms轮询
- root.update()会导致重入，应去掉
- 先 _gui.root.destroy() 再 _gui_thread.join(timeout=3)

## 打包与部署
### Cython保护（V3.0.3+）
- 核心6模块编译为.pyd（api_client/main/sku_interceptor/calibration/browser/config）
- GUI模块保留.py
- setup_cython.py: annotation_typing=False

### 打包脚本
- `build_protected.bat`：Cython编译→源码.bak暂存→PyInstaller打包→源码恢复→dist/

### 白屏问题
- about:blank页面CDP连着但没真正加载，手动导航触发有效页面加载

## 校准与时间
- AUTO_CALIBRATE 默认 False，TIME_OFFSET=0，MANUAL_TIME_OFFSET=""
- GUI手动偏移默认 "-0.1"
- 多次时间校准：≥60s → 三次；30~60s → 两次；5~30s → 一次；<5s → 立刻

## API与结账
### 结账流程（V1 run_api_mode，LITE参考源）
购物车→Checkout→信用点+MAX→Continue→地址Continue→勾选agree→I AGREE

### 关键注意
- V1有两个结账函数：run_api_mode（完整分步，主用）和run_page_mode（简化备用，仅fallback）
- 读取V1代码时必须看全两个函数，不能只看一个就下结论
- agree勾选：不检查checkbox状态，直接点击
- MAX按钮重试最多3次
- 信用点amount必须与商品价格精确匹配

### API加购
- `api_add_to_cart(sku_id) -> (success, error_code)`
- 轮询T-5s~T+3s，0.5秒间隔
- OutOfStock继续，HTTP429 sleep 3s

### SKU拦截器
- 必须在page.goto()之前注册page.on('response')
- 过滤/graphql，提取store.listing.resources

### 逆向API原则
- 必须先抓真实请求，严禁猜测
- 正确流程：调试版挂网络拦截器→走一遍完整流程→拿到真实日志→照抄实现

## V1实际代码路径（已验证）

V1默认 `MODE=api`，走 `run_api_mode`：

```
main.py → MODE=="api" → run_api_mode(page)
  → 步骤1: client.add_to_cart_via_page()  # 用.c-skuCard找卡片，用.a-skuButton点按钮
  → 步骤2: 购物车 → 点MAX
  → 步骤3: 点Continue → 地址 → 勾选agree → I AGREE
  → 失败fallback: run_page_mode(page)  # 用.offer-card（但几乎不会走到这）
```

### 关键选择器（从V1 add_to_cart_via_page直接搬）

| 用途 | 选择器 | 来源 |
|------|--------|------|
| 卡片 | `.c-skuCard` | V1 api_client.py add_to_cart_via_page |
| 标签 | `.c-skuCard__tag` | 同上 |
| 标题 | `.c-skuCard__title, .c-skuCard__name, [class*="title"]` | 同上 |
| 加购按钮 | `.a-skuButton` | 同上 |
| 购物车Continue | `.m-cartActionBar__button` | V1 browser.py run_api_mode |
| 信用点 | `input[value='credits']` | 同上 |
| MAX按钮 | `button:has-text("MAX")` | 同上 |