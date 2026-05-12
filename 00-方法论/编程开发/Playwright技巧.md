# Playwright技巧

## 常见问题

### timeout单位是毫秒
Playwright所有timeout参数单位是毫秒，不能写秒的数值。
- 错误: `timeout=20`（实际0.02秒）
- 正确: `timeout=20000`（20秒）

### JS click不触发React事件
- React监听的是合成事件，`element.click()` 不触发
- 必须用Playwright原生 `locator.click()`
- 如果Playwright click也失败，尝试 `click(force=True)`

### CDP模式连接
- Edge CDP模式: 启动时加 `--remote-debugging-port=9222`
- 连接地址用 `127.0.0.1:9222`，不用 `localhost`

### tkinter和Playwright事件循环冲突
- `sync_playwright` 和 tkinter 不能在同一线程
- Playwright必须在独立线程运行
- Playwright线程不能直接destroy GUI（避免Tcl_AsyncDelete错误）

### wait_for_selector vs sleep
- `wait_for_selector` 等DOM元素出现，更可靠
- 但在极速模式下sleep可能更快，因为wait_for有轮询开销
- 抢购场景建议：关键步骤用wait，非关键步骤用短sleep

### force=True
- 跳过可操作性检查，直接点击
- 适用于元素被遮挡、不在视口内等情况
- 抢购场景推荐使用，减少等待
