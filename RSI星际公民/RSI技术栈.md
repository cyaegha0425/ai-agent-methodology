# RSI抢购脚本 — RSI技术栈

> 触发：需要理解RSI网站技术架构时

## 核心架构
- 前端Apollo Client + GraphQL，端点 `/graphql`
- 批量请求格式（JSON数组），CSRF Token每次变化
- Warbond和信用点是不同SKU，通过标题区分
- 价格单位美分（$825.00 = 82500 cents）

## 关键URL
- 机库页面：`https://robertsspaceindustries.com/en/account/pledges`
- 商店浏览：`https://robertsspaceindustries.com/en/store/pledge/browse/extras/standalone-ships`
- 购物车：`https://robertsspaceindustries.com/en/store/pledge/cart`
