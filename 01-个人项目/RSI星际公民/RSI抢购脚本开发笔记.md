# RSI抢购脚本开发笔记

## V3.0.0 咩咩KICK！（当前版本）
- 仓库: `git@github.com:cyaegha0425/RSI_MIEKCIK_BUY.git`
- 架构: 全API抢购，Playwright CDP + GraphQL直连
- 两种输入模式:
  1. **刷新拦截模式** — 搜索关键词，T-0刷新，GraphQL拦截提取skuId
  2. **SKU ID直购模式** — 直接输入skuId，T-10开始API加购轮询
- 核心流程: 登录→预热→校准→等待→拦截/加购→极速结账
- 伏击模式: 物品提前在购物车，预装填后T-0只发validate
- SKU收藏夹: 拦截成功自动保存所有商品（名称+skuId，价格可留空）

### V3关键踩坑
1. **RSIClient.__init__死代码**: 实例变量写在is_page_alive()的return False之后是死代码
2. **Python 3.14 tkinter线程安全**: after_idle也不允许从子线程调用，必须用queue+after()轮询
3. **CDP ctx.pages[0]是Edge预存tab**: Playwright事件hook不上去，必须ctx.new_page()
4. **RSI浏览页SSR vs SPA**: 不带keywords=走SSR无GraphQL，带keywords=走SPA+GraphQL
5. **全量SKU扫描不可行**: 浏览页SSR无GraphQL，7次尝试均失败，已删除此功能
6. **信用点金额必须精确匹配**: AddCreditMutation的amount与商品价格不一致会导致结账失败
7. **page.evaluate()只接受1个额外参数**: 多参数用dict传递
8. **JS正则\$和\d在Python字符串里是无效转义**: 必须写成\\$和\\d

### V3价格获取优先级
1. 手动填写ITEM_PRICE → 最稳
2. 拦截器GraphQL响应price字段 → 刷新拦截模式有效
3. 页面DOM读取 → 拦截模式fallback
4. 报错终止
- SKU模式没有手填价格直接报错，不走DOM/探价

## V2 列表页加购模式（已归档）
- 文件: `./星际公民/rsi_auto_buy_gui_v4.py`
- 流程: 筛选URL→找卡片→点加购→"successfully added"→跳购物车→MAX→继续→地址→agree→I AGREE

## V1 商品详情页模式（已归档）
- 文件: `./星际公民/rsi_auto_buy.py`
- 流程: 商品页→VIEW OFFERS→选SKU→swiper导航→ADD TO CART→购物车→付款

## 通用踩坑记录

### Playwright相关
1. **timeout单位是毫秒**，不能写秒的数值
2. **JS click不触发React事件**，用Playwright原生click
3. **CDP连接用127.0.0.1**，不用localhost
4. **tkinter和Playwright sync_api事件循环冲突**，Playwright必须在独立线程
5. **Edge CDP模式**解决RSI反自动化检测
6. **page.evaluate()只接受1个额外参数**，多参数用dict传递

### RSI网站相关
1. SKU数据在graphql响应中，页面HTML不包含
2. Warbond版和信用点版是不同SKU，通过标签区分
3. CART_URL有/store/路径
4. 不带keywords=的浏览页是SSR，不发GraphQL
5. 带keywords=的页面走SPA，JS主动发GraphQL

### GUI相关
1. os._exit(0)强制退出进程，避免残留
2. Playwright线程不直接destroy GUI（避免Tcl_AsyncDelete）
3. Python 3.14连after_idle都不允许从子线程调用
4. 确认弹窗不自动关闭，等用户点确定

### 付款相关
1. 信用点版=无Warbond/无-disclaimer的Standalone Ship
2. AddCreditMutation金额必须与商品价格精确匹配
3. CartValidateCartMutation信用点付款token=""即可
4. NextStepMutation×2自动跳过地址绑定（RSI用默认地址）
5. 幽灵订单问题：API返回成功但实际未扣款/未到账
3. 加购后不会自动跳购物车，需手动goto CART_URL

## 测试商品
- PTV（高尔夫球车）skuId=10898，常用于测试
- 100i $50，Aurora Mk II $45等低价商品适合反复测试

---

## 百科踩坑详解完整记录（来源：百科第八章）

> 项目专属踩坑请查阅本文件，通用踩坑速查见方法论第八章。

### 踩坑1：编译缓存导致改源码不生效

**问题表现**：改了.py文件但程序跑的还是旧逻辑。

**排查过程**：

1. 用户反馈：改了config.py里的超时时间，但程序还是用的旧的
2. 检查：确认文件确实改了，git log显示最新提交
3. 进一步检查：发现目录下有.pyd文件（编译后的Cython模块）
4. 根因定位：Python优先级.pyd > .py，改了源码但没删编译缓存=没改
5. 解决方案：开发阶段删掉所有.pyd和__pycache__，只在发布时编译

**教训**：开发环境避免预编译，改源码才能生效。

### 踩坑2：VPN导致程序崩溃

**问题表现**：开了VPN就崩溃，关了VPN正常运行。

**排查过程**：

1. 第一次尝试：换VPN软件 → 失败
2. 第二次尝试：换VPN节点 → 失败
3. 第三次尝试：调整VPN配置 → 失败
4. 第四次尝试：禁用部分VPN功能 → 失败
5. 止损判断：4次不同方案均失败，识别为系统级网络劫持
6. 最终结论：标注环境限制，非代码问题，暂不解决

**教训**：应用层无法解决系统级问题，识别无解后止损。

### 踩坑3：打包后缺模块

**问题表现**：本地运行正常，打包后报No module named logging.handlers。

**排查过程**：

1. 用户反馈：build完启动报错
2. 根因定位：PyInstaller静态扫描遗漏了懒加载模块
3. 解决方案：修改build脚本，显式声明隐藏模块

**教训**：打包工具扫描能力有限，特殊模块需要显式声明。

### 踩坑4：GUI框架行为异常

**问题表现**：按直觉配置参数但行为不对。

**排查过程**：

1. 问题：框架API与直觉不符
2. 逐项验证：每个参数单独测试，确认实际行为
3. 结论：老旧框架文档过时，实际行为与文档不符

**教训**：使用老旧框架时不要假设一致，逐项验证。

### 踩坑5：多重配置叠加

**问题表现**：设了时间偏移-0.1s但实际跑-0.3s。

**排查过程**：

1. 检查config.py的偏移设置 → -0.1s
2. 检查源码中的偏移设置 → -0.2s
3. 根因定位：config和源码各有一个偏移量，叠加生效
4. 解决方案：统一为单一入口，所有偏移在config.py设置

**教训**：核心参数单一入口，避免多处定义。

### 踩坑6：Force Push后本地不同步

**问题表现**：Agent force push后，用户本地git pull报错。

**排查过程**：

1. 用户反馈：pull报错，历史不一致
2. 根因：force push改变了远程历史，本地历史与远程冲突
3. 解决方案：git fetch origin + git reset --hard origin/分支名

**教训**：force push后必须同步本地，保持与远程一致。
