# RSI GraphQL API参考

## 端点
- `https://robertsspaceindustries.com/graphql`
- 所有操作走同一端点，通过operationName区分
- 响应为批量数组格式（一个请求多个operation响应）

## 请求格式
- Content-Type: application/json
- Cookie: 必须携带完整RSI登录cookies（包括Rsi-Token）
- x-rsi-token: CSRF token，从cookies中提取
- body: {"operationName":"xxx","variables":{...},"query":"..."}

## 关键Mutations

### 1. AddCartMultiItemMutation — 加购
变量: {"skuId": "12345", "storeFront": "pledge"}
- 注意：CartAddInput只有skuId字段，没有quantity
- 成功返回: store.cart.mutations.addMany → count, type
- 失败返回: errors数组，常见 "Internal server error"（商品未上架/不可售）

### 2. AddCreditMutation — 应用信用点
变量: {"amount": 15.0, "storeFront": "pledge"}
- amount必须与商品实际价格精确匹配，否则结账失败
- 返回: store.cart.totals.credits → amount, maxApplicable
- 信用点应用后cart页面的金额应变为$0.00

### 3. NextStepMutation — 推进结账流程
变量: {"storeFront": "pledge"}
- 第一次: 从CartLines推进到CreditCoupon→Addresses
- 第二次: 跳过地址绑定，推进到Payment，返回orderSlug
- RSI使用默认地址，无需CartAddressAssignMutation
- 返回: store.cart.mutations.flow.moveNext, store.order.slug

### 4. CartValidateCartMutation — 确认付款
变量: {"token": "", "mark": "", "storeFront": "pledge"}
- 信用点付款token和mark传空字符串即可，不需要recaptcha
- 成功返回: store.cart.mutations.validate → 订单号
- 或: store.order.slug → 订单号

### 5. CartAddressAssignMutation — 绑定地址（V3已不使用）
变量: billing地址ID
- RSI自动使用默认地址，V3中NextStep自动跳过

## 关键Queries

### MiniCartWidgetInitializationQuery — 获取购物车总额
返回cart.total.amount

### GetShip — 获取商品SKU信息

## 关键发现

### RSI商店页面架构
- **不带keywords参数**: SSR渲染，HTML直出，不发GraphQL请求
- **带keywords参数**: SPA前端，JS主动发GraphQL请求获取商品数据
- reload不带keywords也不会触发GraphQL
- 抢船拦截模式能拿到GraphQL是因为URL带keywords参数

### CDP连接注意事项
- ctx.pages[0]是Edge预存tab，Playwright事件hook不上去
- 必须用ctx.new_page()创建新页面

### 信用点付款流程
1. AddCartMultiItemMutation（加购）
2. AddCreditMutation（应用信用点，amount=商品价格）
3. NextStepMutation × 2（推进结账+跳过地址）
4. CartValidateCartMutation（确认付款，token=""）

### 价格匹配问题
- AddCreditMutation的amount必须与商品价格精确匹配
- 金额不匹配会导致结账失败（信用点不够或多了都不行）
- 价格来源优先级：手动填写 > 拦截器价格 > DOM读取 > 报错停止
- URL路径slug ≠ GraphQL查询slug
- 如"ptv" ≠ "1dd8mtr1l691y"
- 响应是批量数组格式，需遍历查找

## 注意
- Warbond版和信用点版是不同SKU
- SKU在graphql响应中不在HTML里
- 需要登录态cookies
