# RSI星际公民限量船抢购脚本 — 扣子编程开发Prompt

> **版本**: V3.0.4 咩咩KICK!
> **项目目标**: 在扣子编程(Coze AI Programming)中重新开发RSI限量船抢购脚本
> **项目仓库**: `git@github.com:cyaegha0425/RSI_MIEKCIK_BUY.git`

---

## 1. 项目概述

### 1.1 这是什么项目

**咩咩Kick! V3.0.4** 是一个自动化抢购RSI（Robert Space Industries）星际公民商店限量Pledge商品的Python脚本。

### 1.2 为什么做这个项目

RSI每逢重大节日会限时+限量发售特定船的Pledge：
- **北京时间0点起每4小时整点放量**，数量极少
- 真人和脚本群同时抢，**秒没**
- 目标：用已有的Credit（信用点），在整点放货瞬间自动完成购买

### 1.3 核心价值

- **通用限量船购买脚本**：不局限于某一条船，可配置目标船，适用于任何限时+限量的Standalone版本抢购
- **极致速度**：全程1-2秒
- **两种模式**：正面硬刚（实时监控+API加购） vs 伏击模式（物品预置购物车）

---

## 2. 业务背景

### 2.1 RSI信用点/Pledge体系

| 概念 | 说明 |
|------|------|
| **Credit（信用点）** | 充值真金白银获得，是用户的钱 |
| **Pledge（质押）** | 用信用点换来的船，钱以另一种形态存在 |
| **Melt（融船）** | Pledge退回为信用点，是资金形态转换 |
| **24小时CD** | 新Pledge有24小时冷却期，过CD后才能melt |
| **Buy Back Pack** | melt后的Pledge进入此处，之后可用Credit或现金买回 |

### 2.2 限量船的两种版本

| 版本 | 购买方式 | 折扣 | 风险 |
|------|----------|------|------|
| **Warbond版** | 限现金购买 | 带折扣 | 不能用信用点，无法转卖 |
| **Standalone版** | 可用Credit购买 | 无折扣 | 可用信用点购买 |

### 2.3 抢购业务逻辑

```
1. 用户必须有足够Credit余额（余额不够=抢到了也付不了款）
2. 整点放货瞬间，目标商品状态从"OUT OF STOCK"变为可购买
3. 脚本需要在毫秒级时间内完成：加购→应用信用点→结账
4. 限量船秒没，速度就是一切
```

### 2.4 24小时CD与Buy Back规则

- **常驻商店Pledge**：melt后随时可重新购买，不受季度2次限制
- **限时出售Pledge**：melt后只能从Buy Back Pack买回，消耗季度2次Credit额度
- **Buy Back限制**：用Credit从Buy Back Pack买回，每季度只有2次机会

---

## 3. 技术架构

### 3.1 V3.0架构：全API抢购

```
┌─────────────────────────────────────────────────────────────┐
│                        V3.0 全API架构                        │
├─────────────────────────────────────────────────────────────┤
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────────┐ │
│  │   Playwright │    │  SKU拦截器   │    │   RSIClient     │ │
│  │  (CDP模式)   │───▶│ (GraphQL)   │───▶│ (urllib API)    │ │
│  └─────────────┘    └─────────────┘    └─────────────────┘ │
│         │                                        │          │
│         ▼                                        ▼          │
│  ┌─────────────┐                        ┌─────────────────┐ │
│  │  Edge浏览器  │                        │  极速结账流程    │ │
│  │ (保持登录态) │                        │  AddCredit      │ │
│  └─────────────┘                        │  NextStep×2     │ │
│                                        │  Validate       │ │
│                                        └─────────────────┘ │
└─────────────────────────────────────────────────────────────┘
```

### 3.2 两种输入模式

#### 模式1：刷新拦截模式（intercept）
- 输入搜索关键词
- T-0时刷新页面，拦截器从GraphQL响应自动提取skuId
- 匹配成功后API加购→极速结账
- **优势**：不需要提前知道skuId

#### 模式2：SKU ID直购模式（sku）
- 直接输入商品SKU ID
- T-10开始API加购轮询（0.5秒间隔）
- **必须手动填写价格**（信用点金额需精确匹配）
- 适合已知skuId的场景

### 3.3 伏击模式

前提：物品已在购物车

```
伏击模式流程：
1. 预装填：T-30s执行 AddCreditMutation → NextStepMutation × 2
2. T-0：只发 CartValidateCartMutation
3. validate失败检查流程状态，流程丢失则重新装填（最多2次）
4. validate重试4次
```

### 3.4 项目结构

```
RSI_MIEKCIK_BUY/
├── RSI_MIEKCIK_BUY.py          # 主入口（含免责声明弹窗）
├── modules/
│   ├── main.py                 # 主流程：登录→预热→校准→拦截→加购→结账
│   ├── api_client.py           # RSIClient类，GraphQL API封装
│   ├── browser.py              # 浏览器启动/CDP连接
│   ├── config.py               # 常量、配置、GraphQL Queries
│   ├── gui_config.py           # 配置窗口（两种模式+收藏夹+高级设置）
│   ├── gui_progress.py         # 进度窗口+弹窗
│   ├── sku_interceptor.py      # SKU拦截器（Playwright响应拦截）
│   ├── sku_bookmarks.py        # SKU收藏夹（JSON存储）
│   ├── calibration.py          # 时间校准（NTP采样+密度窗口）
│   └── latency_test.py         # 延迟测试
├── images/                     # 背景图（exe同目录外部读取）
│   ├── gui_bg.png              # 配置+进度窗口背景 (600x560)
│   ├── gui_bg_success.png      # 成功弹窗背景 (520x325)
│   └── gui_bg_fail.png         # 失败弹窗背景 (520x325)
├── sku_bookmarks.json          # 本地收藏夹（gitignore）
└── sku_bookmarks.example.json  # 收藏夹模板（跟仓库）
```

---

## 4. 核心流程

### 4.1 完整流程图

```
┌────────────────────────────────────────────────────────────────────┐
│                           完整抢购流程                              │
└────────────────────────────────────────────────────────────────────┘

[启动] → [免责声明弹窗] → [配置窗口]
                              │
              ┌───────────────┼───────────────┐
              ▼               ▼               ▼
         [正面硬刚]      [伏击模式]     [测试模式]
              │               │               │
              ▼               ▼               ▼
        [Edge CDP连接]  [预置购物车]   [完整购买测试]
              │               │               │
              ▼               ▼               ▼
        [登录验证] ←───────[登录验证]───────→[登录验证]
              │               │               │
              ▼               ▼               ▼
        [预热页面]      [伏击预装填]     [打开页面]
              │               │               │
              ▼               ▼               ▼
        [时间校准]      [T-0等待]         [页面加购]
              │               │               │
              ▼               ▼               ▼
   ┌─────────┴─────────┐     │         [购物车+MAX]
   ▼                   ▼     ▼               │
[拦截模式]        [SKU模式]  │               ▼
   │                   │     │          [Continue×2]
   ▼                   ▼     ▼               │
[T-0刷新页面]    [T-10轮询]  │          [勾选agree]
   │                   │     │               │
   ▼                   ▼     ▼               ▼
[API加购]      [API加购+结账] [T-0 Validate]
   │                   │     │
   ▼                   ▼     ▼
[极速结账] ←────────────[极速结账]
   │
   ▼
[结果弹窗]
```

### 4.2 正面硬刚流程（拦截模式）

```python
# 伪代码流程
1. 登录 → 确保RSI账号已登录
2. 预热 → 打开商品列表页 (带keywords参数的SPA页面)
3. 时间校准 → 测量本地与RSI服务器时间差
4. 等待T-0
5. 刷新页面 → 触发GraphQL请求获取商品数据
6. 拦截器提取skuId → 从GraphQL响应解析store.listing.resources
7. 匹配关键词 → 过滤Warbond版商品
8. API加购 → AddCartMultiItemMutation
9. 极速结账 → AddCreditMutation → NextStepMutation×2 → CartValidateCartMutation
10. 结果弹窗 → 显示成功/失败
```

### 4.3 极速结账API链路

```python
# 完整GraphQL API链路（无需DOM操作）
1. AddCartMultiItemMutation(skuId, storeFront="pledge")
   └─ 加购商品到购物车

2. AddCreditMutation(amount=商品价格, storeFront="pledge")
   └─ 应用信用点（金额必须与商品价格精确匹配）

3. NextStepMutation(storeFront="pledge")
   └─ 推进结账流程到地址步骤

4. NextStepMutation(storeFront="pledge")
   └─ 跳过地址绑定（RSI使用默认地址）

5. CartValidateCartMutation(token="", mark="", storeFront="pledge")
   └─ 确认付款（信用点付款token=""即可）
```

---

## 5. API详情

### 5.1 GraphQL端点

```python
GRAPHQL_URL = "https://robertsspaceindustries.com/graphql"
```

### 5.2 请求格式

```python
# 请求头
headers = {
    'Content-Type': 'application/json',
    'Accept': '*/*',
    'accept-language': 'en',
    'Cookie': cookie_str,  # 从浏览器上下文获取
    'Referer': 'https://robertsspaceindustries.com/en/store/pledge/cart',
}

# x-csrf-token从页面meta标签获取
csrf_token = page.evaluate("""
    () => {
        const meta = document.querySelector('meta[name="csrf-token"]');
        return meta ? meta.getAttribute('content') : '';
    }
""")

# 请求体（批量数组格式）
payload = [{
    "operationName": "OperationName",
    "variables": {"key": "value"},
    "query": QUERY_STRING.strip()
}]
```

### 5.3 关键Queries定义（config.py）

```python
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
    ...EntityAfterUpdateFragment
    __typename
  }
}

fragment EntityAfterUpdateFragment on TyStore {
  cart {
    lineItemsQties
    billingRequired
    totals {
      total
      credits {
        amount
        __typename
      }
      __typename
    }
    __typename
  }
  __typename
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
      totals {
        total
        __typename
      }
      __typename
    }
    ...CartFlowFragment
    ...OrderSlugFragment
    __typename
  }
}

fragment CartFlowFragment on TyStore {
  cart {
    flow {
      steps {
        step
        action
        finalStep
        active
        __typename
      }
      current {
        orderCreated
        __typename
      }
      __typename
    }
    __typename
  }
  __typename
}

fragment OrderSlugFragment on TyStore {
  order {
    slug
    __typename
  }
  __typename
}
''',

    # 4. 确认付款
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
    ...CartFlowFragment2
    ...OrderSlugFragment2
    __typename
  }
}

fragment CartFlowFragment2 on TyStore {
  cart {
    flow {
      steps {
        step
        action
        finalStep
        active
        __typename
      }
      current {
        orderCreated
        __typename
      }
      __typename
    }
    __typename
  }
  __typename
}

fragment OrderSlugFragment2 on TyStore {
  order {
    slug
    __typename
  }
  __typename
}
''',

    # 5. 购物车信息查询
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
}
```

### 5.4 API客户端实现（RSIClient类）

```python
class RSIClient:
    def __init__(self, page):
        self.page = page
        self.url = "https://robertsspaceindustries.com/graphql"
        self.cart_data = {}
    
    def gql(self, operation_name: str, variables: Dict = None) -> Dict:
        """执行GraphQL请求（使用urllib，从浏览器取cookies）"""
        import urllib.request
        import json
        
        query = QUERIES.get(operation_name, "")
        payload = [{
            "operationName": operation_name,
            "variables": variables or {},
            "query": query.strip()
        }]
        
        # 从浏览器上下文获取cookies
        cookie_str = ""
        try:
            browser_cookies = self.page.context.cookies()
            cookie_parts = []
            for c in browser_cookies:
                if c.get('domain', '').endswith('robertsspaceindustries.com'):
                    cookie_parts.append(f"{c['name']}={c['value']}")
            cookie_str = "; ".join(cookie_parts)
        except Exception as e:
            log.warning(f"获取cookies异常: {e}")
        
        # 获取CSRF token
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
        
        with urllib.request.urlopen(req, timeout=10) as resp:
            result = json.loads(resp.read().decode('utf-8'))
        
        # RSI GraphQL响应可能是批量数组格式
        if isinstance(result, list):
            for item in result:
                if isinstance(item, dict) and item.get('data'):
                    return item
            return result[0] if result else {}
        return result or {}
    
    def calibrate_time(self, samples: int = 10, interval: float = 0.5) -> float:
        """校准本地与RSI服务器的时间偏移"""
        # 使用1秒密度窗口算法
        # 返回：时间偏移（秒），正值=本地比服务器慢
        pass
    
    def add_to_cart_via_page(self) -> bool:
        """页面点击加购（通过Playwright DOM操作）"""
        # 使用选择器 .c-skuCard 找卡片，.a-skuButton 点按钮
        pass
    
    def superfast_checkout(self) -> bool:
        """极速结账：AddCredit → NextStep×2 → Validate"""
        pass
```

### 5.5 时间校准算法

```python
def calibrate_time(self, samples: int = 10, interval: float = 0.5) -> float:
    """1秒密度窗口算法
    
    算法：
    1. 多次采样，记录 (偏移值, RTT)
    2. 过滤RTT异常的样本（>中位数2倍）
    3. 1秒滑动窗口找包含最多点的区间（真实偏移必在此区间内）
    4. 取窗口内最低25%样本，减去Uniform顺序统计量偏置
    """
    raw_samples = []  # (offset, rtt)
    
    for i in range(samples):
        # 发送GraphQL请求，从响应头获取RSI服务器时间
        req = urllib.request.Request(self.url, ...)
        t1 = time.time()
        with urllib.request.urlopen(req, timeout=10) as resp:
            t2 = time.time()
            server_dt = parsedate_to_datetime(resp.headers.get('Date'))
            server_ts = server_dt.timestamp()
        
        mid = (t1 + t2) / 2.0
        rtt = t2 - t1
        d = mid - server_ts
        raw_samples.append((d, rtt))
    
    # RTT过滤
    rtts = [s[1] for s in raw_samples]
    median_rtt = sorted(rtts)[len(rtts)//2]
    filtered = [(d, r) for d, r in raw_samples if r <= median_rtt * 2]
    
    # 密度窗口找最大连续区间
    # ...
    
    return offset
```

### 5.6 SKU拦截器实现

```python
class SKUInterceptor:
    """从GraphQL响应中提取skuId"""
    
    def __init__(self, page, keywords: str = "", exclude_keywords: str = ""):
        self.page = page
        self.keywords = [k.strip().lower() for k in keywords.split() if k.strip()]
        self.exclude_keywords = [k.strip().lower() for k in exclude_keywords.split(',') if k.strip()]
        self._products = []
        self._sku_id = None
        self._registered = False
    
    def register(self):
        """注册page.on('response')拦截器"""
        if not self._registered:
            self.page.on('response', self._handle_response)
            self._registered = True
    
    def _handle_response(self, response):
        """拦截GraphQL响应"""
        if '/graphql' not in response.url:
            return
        if response.status != 200:
            return
        
        try:
            body = response.text()
            data = json.loads(body)
            
            # 递归提取 listing.resources
            self._extract_listings(data)
        except:
            pass
    
    def _extract_listings(self, obj, depth=0):
        """递归提取listing数据"""
        if depth > 10:
            return
        
        if isinstance(obj, dict):
            # 检查是否包含 listing.resources
            if 'listing' in obj and 'resources' in obj.get('listing', {}):
                resources = obj['listing']['resources']
                if isinstance(resources, list):
                    for resource in resources:
                        self._process_product(resource)
            
            # 递归处理子节点
            for key, value in obj.items():
                if isinstance(value, (dict, list)):
                    self._extract_listings(value, depth + 1)
        
        elif isinstance(obj, list):
            for item in obj:
                if isinstance(item, (dict, list)):
                    self._extract_listings(item, depth + 1)
    
    def _process_product(self, resource: dict):
        """处理单个商品资源"""
        name = resource.get('name', '')
        sku_id = resource.get('skuId', '') or resource.get('id', '')
        
        # 提取价格
        price_info = resource.get('price', {})
        if isinstance(price_info, dict):
            amount = price_info.get('amount', 0)
            price = amount / 100.0 if amount > 100 else float(amount)
        
        if not sku_id:
            return
        
        self._products.append({
            'name': name,
            'skuId': sku_id,
            'price': price
        })
        
        # 检查关键词匹配
        if self._is_match(name):
            self._sku_id = sku_id
            log.info(f"🎯 匹配成功! skuId={sku_id}")
    
    def _is_match(self, name: str) -> bool:
        """检查商品名称是否匹配"""
        name_lower = name.lower()
        
        # 必须包含所有关键词
        if self._keywords_pattern:
            if not self._keywords_pattern.search(name_lower):
                return False
        
        # 不能包含排除关键词
        if self._exclude_pattern:
            if self._exclude_pattern.search(name_lower):
                return False
        
        return True
    
    def get_sku_id(self) -> Optional[str]:
        return self._sku_id
```

---

## 6. GUI规范

### 6.1 颜色方案

```python
# GUI颜色常量（config.py）
GUI_BG_COLOR = "#A8B4D4"      # 背景色（浅蓝灰白）
GUI_TITLE_COLOR = "#1a3a5c"   # 标题文字（深蓝）
GUI_TEXT_COLOR = "#2d2d2d"    # 正文文字（深灰）
GUI_BG_DARK = "#1e1e2e"       # 备用深色背景

# 按钮颜色
BUTTON_START = "#6A8CBA"      # 开始按钮（暗蓝）
BUTTON_CANCEL = "#9E6B7A"     # 取消按钮（暗玫红）
BUTTON_CONFIRM = "#7B8FB7"    # 确认按钮（中暗蓝）
```

| 用途 | 颜色值 | 说明 |
|------|--------|------|
| 背景色 | `#A8B4D4` | 浅蓝灰白，融入动漫风格背景图 |
| 标题文字 | `#1a3a5c` | 深蓝色，用于标题和重点 |
| 正文文字 | `#2d2d2d` | 深灰色，用于普通文字 |
| 开始按钮 | `#6A8CBA` | 暗蓝色，fg=white |
| 取消按钮 | `#9E6B7A` | 暗玫红色，fg=white |
| 确认按钮 | `#7B8FB7` | 中暗蓝色，fg=white |

### 6.2 配置窗口布局（630×720）

```
┌──────────────────────────────────────────────────────┐
│  🐑 咩咩蹄到好船来 V3.0.4                              │  ← 标题
│  抢光CIG的机库                                        │  ← 副标题
├──────────────────────────────────────────────────────┤
│                                                       │
│  ┌─ 伏击模式开关（滑动开关）─┐                        │
│  │ 左=正面硬刚(蓝色) 右=伏击(红橙色) │                │
│  └─────────────────────────┘                         │
│                                                       │
│  时间: [2026]年[05]月[15]日 [00]时[00]分[00]秒      │
│                                                       │
│  ┌─ 输入方式 ──────────────────────────────────────┐ │
│  │ ○ SKU ID直购   ○ 刷新拦截                        │ │
│  └─────────────────────────────────────────────────┘ │
│                                                       │
│  📦 SKU ID: [____________] 📦收藏夹                  │
│  🔍 搜索关键词: [____________]                        │
│  💰 价格(USD): [____________]                        │
│  🚫 排除关键词: [____________]                        │
│                                                       │
│  [高级设置]    [开始抢购]    [取消]                  │
│                                                       │
│  ⚠️ 请提前清空购物车，登录好账号                      │
│                                                       │
│                                        by 咩咩莉娅 V3.0.4 │
└──────────────────────────────────────────────────────┘
```

### 6.3 进度窗口布局（546×683）

```
┌──────────────────────────────────────────────────────┐
│  咩咩蹄到好船来 V3.0.4 咩咩KICK！  [正面硬刚]         │  ← 标题+模式
│  抢光CIG的机库                                        │
├──────────────────────────────────────────────────────┤
│                                                       │
│  ⏰ 距抢购: 2小时 30分 45秒                           │  ← 倒计时（置顶）
│                                                       │
│  📋 日志                                               │  ← 日志按钮（左下）
│                                                       │
│  ┌────────────────────────────────────────────────┐ │
│  │  ✅ 时间校准                                    │ │
│  │  ⏳ 等待抢购时间...                             │ │  ← 步骤状态
│  │  ○ 登录验证                                    │ │
│  │  ○ 预热加载                                    │ │
│  │  ○ 加入购物车                                  │ │
│  │  ○ 极速结账                                    │ │
│  └────────────────────────────────────────────────┘ │
│                                                       │
│  🕐 手动偏移: -0.100秒                                │  ← 校准结果
│                                                       │
│                     [取消抢购]                        │  ← 底部（rely=0.92）
│                                                       │
└──────────────────────────────────────────────────────┘
```

### 6.4 结果弹窗规范

```python
# 成功弹窗
┌─────────────────────────────────────┐
│                                     │
│                              🎉 好船来啦！Nice Boat! │
│                                     │  ← relx=0.83, rely=0.28
│                              提前 1.23秒完成     │  ← relx=0.85, rely=0.52
│                                     │
│                             [确定]  │  ← relx=0.85, rely=0.72
│                                     │
└─────────────────────────────────────┘

# 失败弹窗
┌─────────────────────────────────────┐
│                                     │
│                           ❌ 咩咩Kick！失败 │
│                                     │  ← relx=0.83, rely=0.28
│                           下次一定！     │
│                                     │  ← relx=0.85, rely=0.52
│                             [确定]  │
│                                     │  ← relx=0.85, rely=0.72
└─────────────────────────────────────┘

# 清空购物车弹窗（居中）
┌─────────────────────────────────────┐
│                                     │
│              ⚠️ 请清空购物车        │  ← 居中
│                                     │
│  请在浏览器中清空购物车后           │
│  点击下方确认按钮继续               │
│                                     │
│        [我已清空，确认继续]         │  ← 居中
│                                     │
└─────────────────────────────────────┘
```

### 6.5 tkinter关键约束

```python
# ========== tkinter GUI harness（必须遵守！）==========

# 1. 输入框样式
entry = tk.Entry(..., bg="white", fg="black", ...)
# 禁止：bg="" 空字符串会报错 TclError

# 2. 背景色
root.configure(bg=GUI_BG_COLOR)  # 使用实色，不能为空

# 3. PhotoImage引用保持
self._bg_image = ImageTk.PhotoImage(bg_img, master=self.root)
# 必须保持引用，否则被GC回收

# 4. 按钮直接布局到root
start_btn = tk.Button(root, text="开始", command=..., bg="#6A8CBA", fg="white")
start_btn.place(relx=0.5, y=600, anchor='n')
# 禁止：用Frame包裹按钮行

# 5. 弹窗对齐规则
# 结果弹窗：靠右对齐 relx=0.85
# 清空购物车弹窗：居中

# 6. 取消按钮位置
cancel_btn.place(relx=0.5, rely=0.92, anchor='s', y=-15)

# 7. 署名Label样式
author_label = tk.Label(root, text="by 咩咩莉娅",
                        font=("Microsoft YaHei UI", 8),
                        fg=GUI_TEXT_COLOR, bg=GUI_BG_COLOR)
# 禁止：bg=root.cget('bg')

# 8. 线程安全
# tkinter不允许从子线程调用after/update
# 方案：queue + root.after(50, _poll_queue) 轮询
```

### 6.6 GUI进度类实现框架

```python
class RSIGUI:
    def __init__(self, target_time: str, mode_label: str = "[正面硬刚]"):
        self.enabled = True
        self.target_time = target_time
        self._running = True
        self._cancel_clicked = False
        self._result_confirmed = False
        self._gui_queue = queue.Queue()
        self._after_ids = []
        
        # 步骤定义
        self.steps = [
            ("calibrate", "时间校准..."),
            ("wait", "等待抢购时间..."),
            ("login", "登录验证..."),
            ("warmup", "预热加载..."),
            ("cart", "加入购物车..."),
            ("checkout", "极速结账..."),
            ("done", "完成！"),
        ]
    
    def run_mainloop(self):
        """在主线程运行GUI mainloop"""
        # 创建root窗口
        self.root = tk.Tk()
        self.root.title("咩咩蹄到好船来 V3.0.4")
        self.root.geometry("546x683")
        self.root.attributes('-topmost', True)
        
        # 背景图
        self._bg_image = ImageTk.PhotoImage(..., master=self.root)
        bg_label = tk.Label(self.root, image=self._bg_image)
        bg_label.place(x=0, y=0, relwidth=1, relheight=1)
        
        # 标题
        tk.Label(self.root, text="咩咩蹄到好船来 V3.0.4",
                 font=("Microsoft YaHei UI", 14, "bold"),
                 fg="#1a3a5c", bg=GUI_BG_COLOR).place(...)
        
        # 倒计时
        self._update_countdown()
        
        # 消息轮询
        self._poll_queue()
        
        self.root.mainloop()
    
    def _poll_queue(self):
        """主线程轮询GUI消息队列"""
        try:
            while True:
                msg = self._gui_queue.get_nowait()
                self._handle_gui_message(msg[0], msg[1])
        except queue.Empty:
            pass
        
        if self._running:
            self._after_ids.append(self.root.after(50, self._poll_queue))
    
    def _handle_gui_message(self, msg_type, msg_data):
        """处理GUI消息"""
        if msg_type == "update_status":
            status, key = msg_data
            self.status_label.config(text=f"⏳ {status}")
        elif msg_type == "update_step":
            key, success = msg_data
            icon = "✅" if success else "❌"
            self.step_labels[key].config(text=icon)
        elif msg_type == "show_result":
            success, msg = msg_data
            self._show_result_dialog(success, msg)
    
    def _on_cancel_clicked(self):
        """取消按钮点击"""
        self._cancel_clicked = True
        self._running = False
        self._cancel_all_after()
        self.root.quit()
    
    # ========== 线程安全API（供子线程调用）==========
    def update_status(self, status: str, key: str = None):
        if not self.enabled:
            return
        self._gui_queue.put(("update_status", (status, key)))
    
    def update_step(self, key: str, success: bool):
        if not self.enabled:
            return
        self._gui_queue.put(("update_step", (key, success)))
    
    def show_result(self, success: bool, msg: str = None):
        if not self.enabled:
            return
        self._gui_queue.put(("show_result", (success, msg)))
```

---

## 7. 关键约束

### 7.1 时间精度要求

```python
# 目标：T-0时刷新页面
TARGET = "2026-05-15 00:00:00"  # 北京时间

# 时间偏移策略
TIME_OFFSET = -0.2  # 秒，负数=提前发请求
# 原因：网络延迟+服务器处理时间，本地时间比服务器快需提前发

# 校准时机
# - ≥60s：启动时10次采样 + T-10时10次采样
# - 30~60s：启动时10次采样 + T-10时5次采样
# - <30s：跳过校准，直接用手动偏移
# - <5s：立刻执行，用手动偏移
```

### 7.2 Cookies管理

```python
# 保存cookies
def cookies_save(ctx):
    with open(CFG["COOKIE_FILE"], 'w') as f:
        json.dump(ctx.cookies(), f)

# 加载cookies
def cookies_load():
    if os.path.exists(CFG["COOKIE_FILE"]):
        with open(CFG["COOKIE_FILE"]) as f:
            return json.load(f)
    return None

# Cookie文件路径
COOKIE_FILE = os.path.join(BASE_PATH, "scautobuy", "rsi_cookies.json")
```

### 7.3 错误处理

```python
# 1. 网络请求异常
try:
    with urllib.request.urlopen(req, timeout=10) as resp:
        result = json.loads(resp.read().decode('utf-8'))
except urllib.error.HTTPError as e:
    log.error(f"GraphQL请求HTTP错误: {e.code} {e.reason}")
    return {"__http_status__": e.code}
except Exception as e:
    log.error(f"GraphQL请求失败: {e}")
    return {}

# 2. Playwright超时
page.set_default_timeout(CFG["TIMEOUT"] * 1000)  # 毫秒！

# 3. 用户取消
if gui and gui.is_cancel_clicked():
    raise Exception("用户取消抢购")
```

### 7.4 取消抢购逻辑

```python
# 主线程检测到取消
if gui and gui.is_cancel_clicked():
    log.info("⚠️ 用户取消抢购")
    result_queue.put(("cancel", None))
    break

# 取消后返回配置窗口继续
try:
    result = result_queue.get_nowait()
    if result[0] in ("cancel", "success", "error", "interrupt"):
        continue  # 重新显示配置界面
except queue.Empty:
    break
```

### 7.5 信用点金额匹配

```python
# AddCreditMutation的amount必须与商品价格精确匹配
# 否则结账失败

# 价格获取优先级
PRIORITY:
1. 手动填写 ITEM_PRICE → 最稳
2. 拦截器GraphQL响应price字段 → 刷新拦截模式有效
3. 页面DOM读取 → 拦截模式fallback
4. 报错终止

# SKU模式没有手填价格直接报错，不走DOM/探价
```

---

## 8. 已知踩坑

### 8.1 Playwright相关

| 坑 | 原因 | 解决方案 |
|----|------|----------|
| timeout单位是毫秒 | Playwright API设计 | 写3000而不是3 |
| JS click不触发React事件 | React用自定义事件 | 用Playwright原生click |
| CDP连接用127.0.0.1 | localhost可能解析问题 | 用127.0.0.1 |
| ctx.pages[0]不可用 | Edge预存tab没有Playwright事件 | 必须ctx.new_page() |
| page.evaluate()只接受1个额外参数 | Playwright限制 | 多参数用dict传递 |
| JS正则\$和\d在Python字符串里无效 | Python字符串转义 | 写成\\$和\\d |

### 8.2 RSI网站相关

| 坑 | 原因 | 解决方案 |
|----|------|----------|
| SKU在GraphQL响应中 | RSI前端架构 | 用拦截器提取 |
| 不带keywords=走SSR | 不发GraphQL | 必须带keywords参数 |
| Warbond版不能用信用点 | RSI销售策略 | 通过标签过滤 |
| CART_URL有/store/路径 | RSI URL设计 | 完整路径 |

### 8.3 GUI/tkinter相关

| 坑 | 原因 | 解决方案 |
|----|------|----------|
| bg=""空字符串报错 | Tcl解析 | 必须用实色 |
| PhotoImage被GC回收 | 引用丢失 | 保持self引用 |
| 子线程不能调after | tkinter线程安全 | 用queue+轮询 |
| padx不支持元组格式 | tkinter限制 | 分开写padx=2, padx=8 |

### 8.4 编译缓存坑

```python
# 问题：改了.py文件但程序跑的还是旧逻辑
# 原因：.pyd优先级高于.py，改源码不删.pyd = 没改

# 解决：开发阶段删掉所有.pyd和__pycache__
# 只在发布时编译
import shutil
shutil.rmtree("modules/__pycache__", ignore_errors)
for f in glob.glob("modules/*.pyd"):
    os.remove(f)
```

### 8.5 VPN坑

```python
# 问题：开了VPN就崩溃
# 原因：系统级网络劫持
# 解决：标注环境限制，建议关闭VPN运行
```

---

## 9. 开发规范

### 9.1 代码修改原则

```python
# 1. 局部修改原则
# 只改需要改的部分，严禁全量重写整个文件

# 2. 精确匹配原则
# old_string必须精确匹配原文（包括缩进、空格、换行）

# 3. 分次替换原则
# 改动多时分多次replace_one操作，每次只修改一小段

# 4. edit_file工具模式
edit_file(
    file_path="modules/config.py",
    mode="replace_one",
    old_string="TIMEOUT = 3",
    new_string="TIMEOUT = 5"
)

# 5. 禁止使用write_file重写整个文件
```

### 9.2 模块化要求

```python
# 800行阈值：每加功能先评估是否需新模块
# 主文件超过800行必须拆分

# 模块划分
modules/
├── main.py           # 主流程（<500行）
├── api_client.py     # GraphQL客户端
├── browser.py        # 浏览器操作
├── config.py         # 配置和常量
├── gui_config.py     # 配置窗口
├── gui_progress.py   # 进度窗口
├── sku_interceptor.py # SKU拦截器
├── sku_bookmarks.py  # SKU收藏夹
├── calibration.py    # 时间校准
└── latency_test.py   # 延迟测试
```

### 9.3 配置管理

```python
# 单一配置入口（config.py）
CFG = {
    "TARGET_TIME": "2026-05-15 00:00:00",
    "TIMEZONE": "Asia/Shanghai",
    "GRAPHQL_URL": "https://robertsspaceindustries.com/graphql",
    "BROWSE_URL_PREFIX": "https://robertsspaceindustries.com/en/store/pledge/browse/extras/standalone-ships?page=1&sortField=price&sortDir=desc&keywords=",
    "CART_URL": "https://robertsspaceindustries.com/en/store/pledge/cart",
    "SEARCH_KEYWORDS": "Kraken",
    "ITEM_PRICE": 0,
    "EXCLUDE_KEYWORDS": "",
    "INPUT_MODE": "intercept",  # "intercept" 或 "sku"
    "SKU_ID": "",
    "MANUAL_TIME_OFFSET": "-0.1",
    "AUTO_CALIBRATE": False,
    "AMBUSH_MODE": False,
    "MODE": "api",  # "api" 或 "page"
    "TIMEOUT": 3,  # 秒
    "UA": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36...",
}

# 禁止多处定义偏移量
# 统一从config.py读取
```

### 9.4 打包配置

```python
# PyInstaller打包命令
pyinstaller --onefile --windowed --icon=icon.ico --name "RSI_MIEKCIK_BUY" RSI_MIEKCIK_BUY.py

# 图片不放exe内，放同目录images/
# resource_path()用sys.frozen判断开发/打包环境
def resource_path(relative_path):
    if getattr(sys, 'frozen', False):
        base_path = os.path.dirname(sys.executable)
    else:
        base_path = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))
    return os.path.join(base_path, 'images', relative_path)

# 打包目录不能有中文路径
```

---

## 10. 关键URL和选择器

### 10.1 关键URL

```python
# 商品列表页（带keywords走SPA）
BROWSE_URL_PREFIX = "https://robertsspaceindustries.com/en/store/pledge/browse/extras/standalone-ships?page=1&sortField=price&sortDir=desc&keywords="

# 购物车页面
CART_URL = "https://robertsspaceindustries.com/en/store/pledge/cart"

# 机库页面（购买成功后跳转）
HANGAR_URL = "https://robertsspaceindustries.com/en/account/pledges"
```

### 10.2 DOM选择器（从V1/V3验证）

```python
# 列表页卡片
CARDS = ".c-skuCard"              # 商品卡片容器
CARD_TAG = ".c-skuCard__tag"      # 卡片标签（Warbond/Out of Stock等）
CARD_TITLE = ".c-skuCard__title, .c-skuCard__name, [class*=\"title\"]"  # 卡片标题
ADD_BTN = ".a-skuButton"           # 加购按钮

# 购物车页面
MAX_BTN = "button:has-text(\"MAX\")"     # MAX按钮
CONTINUE_BTN = ".m-cartActionBar__button" # 继续按钮
CHECKBOX = "input[type=\"checkbox\"]"      # 同意复选框
I_AGREE_BTN = "button:has-text(\"I AGREE\")"  # I AGREE按钮
CREDIT_INPUT = "input[value='credits']"   # 信用点单选框

# 页面模式备用选择器
OFFER_CARD = ".offer-card, .pledge-card, .offer-item"  # 老版商品卡片
VIEW_OFFERS = "button:has-text(\"VIEW OFFERS\")"         # 查看选项按钮
```

### 10.3 测试商品

```python
# 低价测试商品（反复购买/退款不心疼）
TEST_ITEMS = [
    {"name": "PTV", "skuId": "10898", "price": 15.0},
    {"name": "100i", "skuId": "10673", "price": 50.0},
    {"name": "Aurora Mk II", "skuId": "10173", "price": 45.0},
]
```

---

## 11. 主入口流程代码框架

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
"""咩咩Kick! V3.0.4 - 入口"""

import os
import queue
import threading
import time
from datetime import datetime

from playwright.sync_api import sync_playwright

from . import config
from .browser import create_browser, login, run_api_mode, run_page_mode
from .gui_progress import RSIGUI
from .api_client import RSIClient
from .calibration import CalibrationScheduler
from .sku_interceptor import SKUInterceptor

def _run_playwright_thread(result_queue):
    """Playwright操作在独立线程中执行"""
    with sync_playwright() as p:
        # 1. 创建浏览器（CDP模式）
        ctx = create_browser(p)
        page = ctx.new_page()
        page.set_default_timeout(CFG["TIMEOUT"] * 1000)
        
        # 2. 登录
        login(ctx)
        
        # 3. 预热页面
        warmup_url = CFG["CART_URL"] if CFG.get("AMBUSH_MODE") else \
                     (CFG["BROWSE_URL_PREFIX"] + CFG["SEARCH_KEYWORDS"])
        page.goto(warmup_url, wait_until="domcontentloaded")
        
        # 4. 创建API客户端
        client = RSIClient(page)
        
        # 5. 时间校准
        server_offset = client.calibrate_time()
        CFG["SERVER_TIME_OFFSET"] = server_offset
        
        # 6. 伏击模式 vs 正面硬刚
        if CFG.get("AMBUSH_MODE"):
            # 伏击模式...
            pass
        else:
            # 正面硬刚模式
            if CFG["INPUT_MODE"] == "intercept":
                # 拦截模式
                interceptor = SKUInterceptor(page, CFG["SEARCH_KEYWORDS"], CFG["EXCLUDE_KEYWORDS"])
                interceptor.register()
                
                # 等待T-0
                while time.time() < target_ts - CFG["SERVER_TIME_OFFSET"]:
                    if gui and gui.is_cancel_clicked():
                        raise Exception("用户取消抢购")
                    time.sleep(0.05)
                
                # T-0刷新
                page.goto(CFG["BROWSE_URL_PREFIX"] + CFG["SEARCH_KEYWORDS"], ...)
                
                # 等待拦截器提取skuId
                for _ in range(60):
                    sku_id = interceptor.get_sku_id()
                    if sku_id:
                        break
                    time.sleep(0.1)
                
                # API加购+结账
                client.add_to_cart(sku_id)
                client.superfast_checkout()
            
            else:
                # SKU模式
                pass
        
        # 7. 结果处理
        result_queue.put(("success", success))

def run():
    """主入口"""
    while True:
        # 显示配置窗口
        if not _show_config_dialog():
            return
        
        # 创建GUI
        gui = RSIGUI(CFG["TARGET_TIME"])
        config.set_gui(gui)
        
        # Playwright线程
        result_queue = queue.Queue()
        pw_thread = threading.Thread(target=_run_playwright_thread, args=(result_queue,))
        pw_thread.start()
        
        # GUI主循环
        gui.run_mainloop()
        
        # 等待线程结束
        pw_thread.join(timeout=10)
        
        # 检查结果
        try:
            result = result_queue.get_nowait()
            if result[0] in ("cancel", "success", "error"):
                continue
        except queue.Empty:
            break
    
    os._exit(0)

if __name__ == "__main__":
    run()
```

---

## 12. 总结

本prompt覆盖了RSI星际公民限量船抢购脚本的完整开发规范：

1. **业务背景**：RSI信用点/Pledge体系、限量船抢购机制
2. **技术架构**：V3全API架构、两种输入模式、伏击模式
3. **核心流程**：登录→预热→校准→监控→抢购→结账
4. **API详情**：GraphQL端点、请求格式、关键Mutations
5. **GUI规范**：tkinter布局、颜色方案、弹窗规则
6. **关键约束**：时间精度、cookies管理、错误处理
7. **已知踩坑**：Playwright、RSI网站、tkinter、编译缓存
8. **开发规范**：局部修改、模块化、配置管理

**核心原则**：局部修改、精确匹配、不自我创造、参考现有代码实现。

---

*by 咩咩莉娅 V3.0.4*
