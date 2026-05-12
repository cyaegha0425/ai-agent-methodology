# RSI商店DOM结构参考

## 列表页（V2使用）
- **URL前缀**: `https://robertsspaceindustries.com/en/store/pledge/browse/extras/standalone-ships?page=1&sortField=price&sortDir=desc&keywords=`
- **卡片容器**: `.browsePage-cardStack__grid`
- **商品卡片**: `.c-skuCard`
- **商品标签**: `.c-skuCard__tag`（文字如"Warbond"、"Out of Stock"、"Concept"等）
- **加购按钮**: `.a-skuButton`（可用时"Add to Cart"，不可用"OUT OF STOCK"）
- **无结果提示**: "NO STANDALONE SHIPS WERE FOUND"
- **加购成功提示**: "Item successfully added to your cart!"

### 判断逻辑
- 信用点版 = 没有Warbond标签的卡片
- 可加购 = 按钮文字不是"OUT OF STOCK"
- 无结果 = 页面有"WERE FOUND"文字且卡片数为0

## 商品详情页（V1使用，已归档）
- **VIEW OFFERS按钮**: `button/a/[role="button"]` 文字="VIEW OFFERS"
- **SKU卡片**: `div.c-optionsItemShip`
- **SKU标题**: `.c-optionsItemShip__title`（有data-original-value属性存英文原名）
- **Disclaimer标识**: `.c-optionItemDisclaimer` 或 class含`-disclaimer`
- **Swiper组件**: `.swiper`，导航用`swiper.swiper.slideTo(index)`
- **ADD TO CART**: `.m-skuValue button`

## 购物车页面
- **URL**: `https://robertsspaceindustries.com/en/store/pledge/cart`（注意有/store/）
- **MAX按钮**: `button:has-text("MAX")`
- **继续按钮**: `.m-cartActionBar__button`
- **Agree复选框**: `input[type="checkbox"]`
- **I AGREE按钮**: `button:has-text("I AGREE")`

## 通用
- RSI前端使用React + Apollo Client + GraphQL
- 所有操作走 `/graphql` 端点，通过operationName区分
- 响应为批量数组格式
- URL路径slug ≠ GraphQL查询slug
- Playwright click比JS click可靠（JS click不触发React事件）
