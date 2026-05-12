# RSI GraphQL API 注意事项
- **端点**：`https://robertsspaceindustries.com/graphql`
- **需要cookies + csrf token**，从浏览器CDP连接获取，CSRF Token每次请求都会变化，不能缓存，需实时获取
- **分页方式**：`after` cursor + `pageInfo.hasNextPage` / `pageInfo.endCursor`
- **响应结构**（推断，未完全验证）：`data.catalog.store.browse.edges[].node` → 含 id/name/title/price.amount/inventory.inStock
- ⚠️ query格式是按现有scrape脚本推断的，尚未用实际API响应验证。如果请求失败，需要：
  1. 用浏览器打开RSI商店页，拦截实际GraphQL请求获取真实query
  2. 或读取 `modules/sku_interceptor.py`（如果存在）了解拦截到的响应结构
- **价格单位**：RSI API返回的price.amount可能是分，>100时需除以100