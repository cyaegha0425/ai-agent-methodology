# Playwright — RSI网站特殊处理

> RSI项目专属的Playwright处理技巧

## 超时设置
- 使用较长超时（如60000ms）
- 优先 `wait_until="domcontentloaded"` 而非 `"networkidle"`

## 滚动加载
- 多次滚动触发懒加载
- 每次滚动后等0.5-1秒

## 响应拦截
- 递归查找包含id和name的对象
- 处理GraphQL响应考虑数组格式

## URL重定向
- RSI部分分类页面会自动重定向，需处理

## CDP连接
- `ctx.pages[0]` 是预存tab
- 必须用 `ctx.new_page()` 创建新页面
- CDP模式可以绕过RSI的反自动化检测
