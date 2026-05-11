# RSI_MIEKCIK_BUY V3.0.0 项目索引

> **咩咩蹄到好船来 V3.0.0 咩咩KICK！** — RSI星际公民全API自动抢购工具
> 仓库: `git@github.com:cyaegha0425/RSI_MIEKCIK_BUY.git`

---

## 项目概述

V3.0.0 全新架构，从V2的DOM点击模式升级为全API抢购架构。核心变化：
- 不再依赖页面DOM点击加购，直接调用GraphQL API
- 新增SKU拦截器，T-0刷新自动从GraphQL响应提取skuId
- 极速结账：信用点→NextStep×2→Validate，全API链路
- 伏击模式：物品预置购物车，预装填后T-0只发validate

---

## 项目结构

```
RSI_MIEKCIK_BUY/
├── RSI_MIEKCIK_BUY.py          # 主入口
├── README.md / GUIDE.txt        # 文档
├── sku_bookmarks.example.json   # 收藏夹模板（跟仓库）
├── sku_bookmarks.json           # 本地收藏夹（gitignore）
└── modules/
    ├── main.py                  # 主流程：登录→预热→校准→拦截→加购→结账
    ├── api_client.py            # RSIClient类，GraphQL API封装
    ├── gui_config.py            # 配置窗口（两种模式+收藏夹+高级设置）
    ├── gui_progress.py          # 进度窗口
    ├── sku_interceptor.py       # SKU拦截器（Playwright响应拦截）
    ├── sku_bookmarks.py         # SKU收藏夹（JSON存储）
    ├── calibration.py           # 时间校准（NTP采样+密度窗口）
    ├── browser.py               # 浏览器启动/CDP连接
    ├── config.py                # 常量、配置、GUI颜色
    └── latency_test.py          # 延迟测试
```

---

## 两种输入模式

### 1. 刷新拦截模式（intercept）
- 输入搜索关键词
- T-0时刷新页面，拦截器从GraphQL响应自动提取skuId
- 匹配成功后API加购→极速结账
- 优势：不需要提前知道skuId
- 拦截器自动保存所有商品到收藏夹

### 2. SKU ID直购模式（sku）
- 直接输入商品SKU ID
- T-10开始API加购轮询（0.5秒间隔）
- 必须手动填写价格（信用点金额需精确匹配）
- 适合已知skuId的场景

---

## 极速结账流程

```
AddCartMultiItemMutation → AddCreditMutation → NextStepMutation → NextStepMutation → CartValidateCartMutation
       加购                  应用信用点          推进到地址          推进到付款           确认付款
```

全链路API，无需DOM操作，信用点付款token=""即可。

---

## 伏击模式

前提：物品已在购物车
1. 预装填：AddCreditMutation → NextStep×2
2. T-0：只发CartValidateCartMutation
3. validate失败检查流程状态，流程丢失则重新装填（最多2次）
4. validate重试4次

---

## 时间校准

- NTP采样10次，间隔0.5秒
- 密度窗口过滤：找最大的连续窗口
- 自动偏移计算
- 校准策略：T-60→T-40→T-20，距T-0<20秒跳过校准偏移=0

---

## SKU收藏夹

- 文件：sku_bookmarks.json（gitignore），sku_bookmarks.example.json（跟仓库）
- 拦截成功自动保存所有商品（名称+skuId+价格，Unknown过滤）
- 价格可留空，手动添加也支持
- 弹窗支持搜索、选择、删除、手动添加
- 选择后自动回填skuId+价格+切换到sku模式

---

## 关键架构决策

1. **全API架构**：不依赖DOM点击，直接GraphQL API加购+结账
2. **Playwright CDP模式**：连接本地Edge，绕过反自动化检测
3. **拦截器模式**：Playwright page.on("response") 拦截GraphQL响应
4. **ctx.new_page()**：CDP连接后必须new_page，ctx.pages[0]是预存tab
5. **SKU模式必须填价格**：信用点金额必须精确匹配，否则结账失败
6. **价格栏默认清空**：不回填上次价格，只有收藏夹选择才回填
7. **全量SKU扫描已删除**：浏览页SSR无GraphQL，不可行
8. **探价模式已删除**：AddCreditMutation探价不可靠，直接报错更安全

---

## Harness（必须保留的修复点）

1. Playwright timeout单位是**毫秒**
2. CDP连接用**127.0.0.1**，ctx.pages[0]不可用，必须**new_page()**
3. tkinter和Playwright事件循环冲突：Playwright在独立线程
4. **Python 3.14 tkinter**：after_idle也不允许子线程调用
5. **page.evaluate()只接受1个额外参数**，多参数用dict传递
6. **JS正则\$和\d**在Python字符串里是无效转义，必须写成\\$和\\d
7. **AddCreditMutation金额必须精确匹配**商品价格
8. **CartValidateCartMutation**信用点付款token=""即可
9. **NextStepMutation×2**自动跳过地址绑定
10. **幽灵订单**：API返回成功但实际未扣款/未到账
11. **RSI浏览页SSR**：不带keywords=不发GraphQL
12. **局部修改原则**：只改需要改的部分，严禁全量重写
13. **try:except:pass会吞异常**：收藏夹保存异常改为log.warning

---

## GUI颜色方案

| 用途 | 颜色 |
|------|------|
| 背景色 | #A8B4D4 |
| 标题文字 | #1a3a5c |
| 正文文字 | #2d2d2d |
| 开始按钮 | #6A8CBA |
| 取消按钮 | #9E6B7A |
| 确认按钮 | #7B8FB7 |

---

## 已知问题

1. **Fiber在Windows Edge上不生效**：Linux Chrome能找到item.id，Edge递归6层搜不到。拦截器已兜住
2. **Tcl_AsyncDelete线程安全崩溃**：偶发，扫描功能删了后触发概率更低
3. **Edge运行时崩溃恢复**：CDP重试已优化，但运行中崩溃自动重连未实现

---

## 相关文件索引

- `harness-RSI.md` — RSI项目代码修改原则和HARNESS规则
- `Python打包-RSI项目.md` — RSI项目PyInstaller打包详细笔记
- `Playwright-RSI特殊处理.md` — RSI网站专属Playwright技巧

---

## 相关参考文件

| 文件 | 内容摘要 |
|------|----------|
| `RSI-GQL-API.md` | RSI GraphQL API端点、请求格式、关键Mutations（AddCartMultiItemMutation、AddCreditMutation、NextStepMutation、CartValidateCartMutation） |
| `RSI历史归档.md` | V1.x~V3.0.3详细修复记录，包括白屏bug修复、GUI优化、价格验证等关键迭代 |
| `RSI商店DOM结构.md` | RSI商店页面DOM选择器：列表页商品卡片、加购按钮、购物车页面元素定位 |
| `RSI技术栈.md` | RSI网站技术架构：Apollo Client + GraphQL、Warbond/信用点SKU区分、价格单位（美分） |
| `RSI抢购脚本开发笔记.md` | V1/V2/V3开发过程踩坑：CDP连接、RSI SPA/SSR判断、信用点金额精确匹配等 |
| `事故复盘-2026-05-04.md` | 幻觉事故复盘：.offer-card误判废弃、V1结账流程误读、源码分析≠动态DOM验证 |
