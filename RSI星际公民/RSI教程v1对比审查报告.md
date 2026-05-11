# 教程 vs 项目代码对比报告

> 审查时间：2024年
> 教程：RSI项目Python自学教程.md（3207行，18章）
> 项目代码：/root/Projects/RSI_MIEKCIK_BUY/

---

## 🔴 严重偏差（与实际项目不一致，会误导读者）

### 1. 【最严重】第12章 - 浏览器自动化方式完全不同

**偏差描述**：教程教的是标准 Playwright+Chromium，实际项目用的是 CDP 连接本地 Edge

**教程说的（第12章）**：
```python
# 教程示例 - 标准Playwright启动Chromium
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    browser = p.chromium.launch()  # 直接启动浏览器
    page = browser.new_page()
    page.goto("https://example.com")
```

**实际代码（browser.py 第61-137行）**：
```python
# 实际项目 - 优先通过CDP连接本地Edge（无自动化标记，RSI不拦截）
def create_browser(p):
    edge_path = find_edge()
    edge_profile = find_edge_profile()
    
    if edge_path and edge_profile:
        log.info("🌐 启动本地Edge (CDP模式)")
        # 1. 强制杀残留Edge进程
        # 2. 启动Edge并开启远程调试端口 9222
        # 3. 通过CDP连接（最多5次尝试，每次失败后重启Edge）
        browser = p.chromium.connect_over_cdp(f"http://127.0.0.1:{debug_port}")
        # 只有CDP失败才回退Chromium
    else:
        log.info("🌐 使用Chromium浏览器")
        browser = p.chromium.launch(...)
```

**影响**：这是**架构级差异**。教程完全没提 CDP 概念，读者学完去改项目会一头雾水。项目为了绕过 RSI 反自动化检测，专门设计了 CDP+Edge 方案，教程完全没有覆盖。

---

### 2. config.py 配置项大量遗漏/错误

**教程提到的配置（第4章）**：
```python
CFG = {
    "TARGET_TIME": "2026-05-15 00:00:00",
    "TIMEZONE": "Asia/Shanghai",
    "TIME_OFFSET": -0.2,           # ← 教程有，实际已删除
    "BROWSE_URL_PREFIX": "...",
    "SEARCH_KEYWORDS": "Kraken",
    "TEST_MODE": False,            # ← 实际项目用的是 DRY_RUN
    "UA": "Mozilla/5.0...",
}
```

**实际代码（config.py 第51-87行）**：
```python
CFG = {
    "TARGET_TIME": "2026-05-15 00:00:00",
    "TIMEZONE": "Asia/Shanghai",
    # 删除了 TIME_OFFSET，新增以下配置：
    "ITEM_PRICE": 0,
    "EXCLUDE_KEYWORDS": "",
    "CART_URL": "https://...",
    "GRAPHQL_URL": "https://robertsspaceindustries.com/graphql",
    "PROXY": None,
    "MODE": "api",  # api 或 page
    "PRE_WARM": 60,
    "PRE_REFRESH": 0.05,
    "DELAY": 0.01,
    "TIMEOUT": 3,
    "INPUT_MODE": "intercept",  # ← 教程完全没提
    "SKU_ID": "",              # ← 教程完全没提
    "MANUAL_TIME_OFFSET": "-0.1",  # ← 替代了TIME_OFFSET
    "AUTO_CALIBRATE": False,      # ← 教程完全没提
    "AMBUSH_MODE": False,         # ← 教程完全没提（伏击模式）
    ...
}
```

**影响**：教程的配置章节与实际代码严重脱节，读者照着改配置会找不到对应字段。

---

### 3. 完全遗漏伏击模式（AMBUSH_MODE）

**偏差描述**：教程完全没有提及实际项目中非常重要的"伏击模式"

**实际代码（main.py）**：
```python
# 伏击模式：提前将物品加入购物车，T-0时极速结账
if CFG.get("AMBUSH_MODE", False):
    log.info("📍 [伏击模式] 开始执行...")
    # 1. 从购物车页面DOM读取商品总价
    # 2. T-30s执行预装填：AddCreditMutation → NextStepMutation × 2
    # 3. T-0s执行：CartValidateCartMutation
    client.superfast_ambush()  # 预装填
    # 等待精确时间...
    client.ambush_validate()    # T-0验证
```

**影响**：伏击模式是项目的核心功能之一（能在 1-2 秒内完成付款），教程完全没提。

---

### 4. gui_progress.py 步骤定义不一致

**教程说的（第8章）**：
```python
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

教程说"实际共7步"，这个是对的，但...

**实际代码中步骤有变化**：
- 伏击模式下 `("wait", ...)` 变成 `("wait", "伏击等待...")`
- `("checkout", "极速结账...")` 在伏击模式下显示 `("checkout", "伏击验证...")`
- 成功后会添加 `("done", "完成！")`

**影响**：虽然步骤数量一致，但教程没有说明不同模式下的步骤差异。

---

### 5. sku_bookmarks.py 数据结构不同

**教程说的（第16章）**：
```python
# 教程示例 - 字典格式
data = {
    "version": "1.0",
    "skus": [...]
}
```

**实际代码（sku_bookmarks.py）**：
```python
# 实际 - 直接返回列表，不是字典
def load_bookmarks():
    # 直接返回列表，不包装在字典里
    return json.load(f)  # 返回 []

def save_bookmarks(bookmarks):
    # bookmarks就是列表，直接dump
    json.dump(bookmarks, f, ensure_ascii=False, indent=2)
```

**影响**：教程示例的数据结构与实际不符，读者照着写会出错。

---

## 🟡 中等偏差（简化或遗漏，但不至于误导）

### 6. 第8章 - 流程步骤描述过于简化

**教程描述**：
```
1. 页面加购
2. 等购物车加载，点MAX
3. 点继续
4. 地址步骤
4.5. 勾选agree复选框
5. 确认付款
```

**实际代码（run_api_mode）**：
```python
def run_api_mode(page, dry_run=None, target_time=0):
    # 步骤1: 页面加购
    cart_success = client.add_to_cart_via_page()
    
    # 步骤2: 点MAX - 有JS备用方案
    page.wait_for_selector('.c-summary', timeout=10000)
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
    
    # 步骤3-4: 点继续 × 2
    # 步骤4.5: 勾选checkbox（有JS备用）
    # 步骤5: 确认付款（尝试多个选择器，有重试逻辑）
    for agree_attempt in range(1, 3):
        for sel in ['button:has-text("I AGREE")', 'button:has-text("Confirm")', ...]:
            try:
                btn.click(force=True, timeout=3000)
                clicked = True
                break
            except: continue
```

**影响**：教程描述的是理想流程，实际代码有大量的异常处理、重试逻辑、备用JS方案。

---

### 7. 第8章 - 时间校准逻辑完全没讲

**偏差描述**：教程只提到 `get_target()` 函数，完全没讲实际项目复杂的时间校准机制

**实际代码（main.py）**：
```python
# 实际项目有多套校准策略
manual_offset = float(CFG.get("MANUAL_TIME_OFFSET", ""))
auto_calibrate = bool(CFG.get("AUTO_CALIBRATE", False))

# 距T-0不足15秒：快速校准（3次采样）
if time_to_target < 15:
    server_offset = client.calibrate_time(samples=3, interval=0.3)
# 距T-0不足8秒：跳过校准
elif time_to_target < 8:
    server_offset = manual_offset
# 正常情况：完整校准
else:
    server_offset = client.calibrate_time()  # 默认10次采样，0.5秒间隔

# 叠加手动偏移
if manual_offset != 0.0:
    server_offset += manual_offset
```

**api_client.py 中的 calibrate_time 方法**（约200行！）：
- 1秒密度窗口算法
- RTT异常过滤
- 顺序统计量偏置修正
- 支持 deadline 参数

**影响**：时间校准是项目的核心技术点，教程完全没有覆盖。

---

### 8. API客户端使用 urllib 而非 requests

**教程第1章建议安装**：
```bash
pip install requests
```

**实际项目（api_client.py）**：
```python
# 实际用的是 urllib（Python内置），没有用 requests
import urllib.request

req = urllib.request.Request(
    self.url,
    data=json.dumps(payload).encode('utf-8'),
    headers={...}
)
with urllib.request.urlopen(req, timeout=10) as resp:
    result = json.loads(resp.read().decode('utf-8'))
```

**影响**：教程误导读者去安装不必要的 requests 库。

---

### 9. run_api_mode vs superfast_checkout

**教程没讲清楚**：
教程提到 `run_api_mode()` 函数，但实际项目主流程用的是 `client.superfast_checkout()`

**实际代码（main.py）**：
```python
if CFG["MODE"] == "api":
    if cart_success:
        success = client.superfast_checkout()  # ← 实际调用的是这个
    else:
        success = run_page_mode(page)
else:
    success = run_page_mode(page)
```

`run_api_mode()` 在 `browser.py` 中定义，是备用/测试模式用的；生产环境用 `RSIClient.superfast_checkout()`。

---

### 10. GraphQL 相关的代码复杂度

**教程第12章**：
```python
# 教程只简单提了一下 evaluate 执行 JS
result = self.page.evaluate("""() => {...}""")
```

**实际代码（api_client.py）**：
- `RSIClient` 类有大量 GraphQL 相关方法
- `gql()` 方法处理 cookies 获取、CSRF token、请求重试
- `add_to_cart_via_page()` 有完整的 SKU 提取逻辑
- `superfast_checkout()` 有完整的 API 付款流程
- 还有伏击模式的 `superfast_ambush()` 和 `ambush_validate()`

**影响**：教程对 GraphQL 客户端的描述过于简化，读者无法理解实际项目的工作原理。

---

## 🟢 一致（教程与实际代码匹配）

### 11. 模块结构和命名
```python
# 教程描述的结构
RSI_MIEKCIK_BUY/
├── RSI_MIEKCIK_BUY.py  # ✓ 主入口
└── modules/
    ├── __init__.py      # ✓
    ├── config.py        # ✓ 配置
    ├── main.py          # ✓ 主逻辑
    ├── gui_config.py    # ✓ 配置界面
    ├── gui_progress.py  # ✓ 进度界面
    ├── browser.py       # ✓ 浏览器操作
    ├── api_client.py    # ✓ API调用
    ├── sku_interceptor.py  # ✓ (教程没讲但存在)
    ├── calibration.py   # ✓ (教程没讲但存在)
    ├── sku_bookmarks.py # ✓ (存在但教程描述有误)
    └── latency_test.py  # ✓ (教程没讲但存在)
```

### 12. RSIClient 类存在
```python
# 教程描述的
class RSIClient:
    def __init__(self, page): ...
    def get_cart_total(self): ...
```

**实际代码基本一致**，但方法更多：
```python
class RSIClient:
    def __init__(self, page)
    def gql(self, operation_name, variables=None, return_headers=False)
    def calibrate_time(self, samples=10, interval=0.5, deadline=0)
    def add_to_cart_via_page(self)
    def apply_credits(self, amount=None)
    def superfast_checkout(self)
    def superfast_ambush(self)
    def ambush_validate(self)
    def get_cart_items_from_page(self)
    # ... 更多方法
```

### 13. RSIGUI 类存在
```python
# 教程描述的
class RSIGUI:
    def __init__(self, target_time)
    def update_status(self, text, step_key)
    def update_step(self, key, success)
```

**实际代码一致**

### 14. 基础语法讲解
- 变量、数据类型、字符串操作 ✓
- 列表、字典、条件判断、循环 ✓
- 函数定义和调用 ✓
- import 和模块 ✓
- 异常处理（try/except） ✓
- JSON 文件读写 ✓
- Tkinter GUI 基础 ✓
- 面向对象（class、self、__init__） ✓

---

## 总结

### 偏差严重程度评估

| 类别 | 数量 | 严重程度 |
|------|------|----------|
| 🔴 严重偏差 | 5项 | 高 - 会误导读者 |
| 🟡 中等偏差 | 5项 | 中 - 知识盲区 |
| 🟢 一致 | 3项 | - |

### 关键发现

1. **最严重问题**：第12章浏览器自动化方式完全错误，实际项目用的是 CDP+Edge 而不是标准 Playwright+Chromium。这是一个架构级差异，不是简单笔误。

2. **遗漏核心功能**：伏击模式（AMBUSH_MODE）是项目的核心技术亮点，教程完全没有提及。

3. **配置项过时**：教程的配置示例与实际代码有较大出入，TIME_OFFSET 已删除，新增了多个配置项。

4. **GraphQL 复杂度被低估**：实际项目的 API 客户端非常复杂，有大量校准、异常处理、重试逻辑，教程完全没有覆盖。

### 修复建议

1. **第12章必须重写**：详细讲解 CDP 原理、Edge 连接过程、回退机制

2. **增加伏击模式章节**：这是项目 V3.0 的核心创新，需要专门讲解

3. **更新配置章节**：对照实际 config.py 逐项说明

4. **增加时间校准章节**：calibrate_time() 的算法虽然复杂，但原理需要讲解

5. **修正 sku_bookmarks.py 示例**：数据结构应该是列表不是字典

6. **区分 run_api_mode 和 superfast_checkout**：说明两者的使用场景

---

**审查结论**：教程与实际项目存在多处严重偏差，特别是浏览器自动化和伏击模式两个核心技术点。**不适合作为真正的"基于实际项目"的教程使用。**
