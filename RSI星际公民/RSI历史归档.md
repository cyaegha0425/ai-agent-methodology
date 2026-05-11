# RSI抢购脚本历史归档

## V1.x 详细修复记录

### V1.1
- 排除关键词+测试模式+高级设置弹窗+5次重试+保留浏览器
- 测试模式：GUI勾选后生效，勾选agree后停止不真正扣款

### V1.2 白屏bug修复
- 根因：_gui_thread未保存引用→join()跳过→旧Tk窗口未销毁→新Tk实例白屏
- gui_progress.py: __init__添加self._gui_thread=None，_start_gui_thread保存线程引用
- on_confirm去掉after(200, destroy)，改为直接try-except destroy
- main.py：先_gui.root.destroy()再_gui_thread.join(timeout=3)

### V1.3 GUI优化+白屏防御+mainloop架构修复
- mainloop改主线程运行（修复RuntimeError: main thread is not in main loop）
- 浏览器白屏检测+自动重试

### V1.4 GUI颜色精修+日志区域移除
- 所有背景色统一#A8B4D4
- 结果弹窗靠右对齐，清空购物车居中
- 删除日志区域（log_frame/log_text/log_title），移除scrolledtext
- _result_confirmed状态标志

### V1.5 11个Bug修复+GUI视觉优化
- _result_confirmed未初始化AttributeError修复
- 取消抢购后窗口消失→0.3秒延迟
- MAX按钮3次重试+信用点验证
- 按钮去底色Frame、署名去底色、取消按钮位置调整等

## V2.0 详细记录
- GUI重做630×720横排布局+滑动开关
- 多次动态时间校准（1-3次根据剩余时间）
- 伏击模式Validate重试（0.3秒3次）
- commits: cf866d6(重做GUI), bdbd25a(多次校准+重试)

## V3.0.0 完整改动清单
- 三种输入模式：SKU ID直购、关键词搜索、刷新拦截
- 全API抢购架构：API加购轮询→API付款，T+3s回退DOM
- SKU拦截器模块sku_interceptor.py
- API加购方法api_add_to_cart返回(success, error_code)
- GUI颜色#A8B4D4，窗口630×720
- 完成确认订单：C2 Hercules $400, PTV Buggy $15

## V3.0.1 线程安全修复
- Python 3.14 tkinter不允许从非主线程调after/after_idle
- 方案：queue.Queue + root.after(50, _poll_queue)轮询
- 6个坑：50ms延迟可接受、queue必须一次清空、root.update()重入、退出时序、show_result弹窗、_check_quit合并

## V3.0.3 Cython保护+免责声明
- 核心6模块编译为.pyd
- setup_cython.py: annotation_typing=False
- 返回类型修复：-> list改为-> dict（api_client.py:1284）
- 免责声明弹窗460×500，每次启动必须同意
- build_protected.bat / build_protected_debug.bat

## 旧版单文件脚本V1.0/V3.1（已归档）
- V1.0: ./星际公民/RSI_MIEBUY_V1.py，列表页直接加购+API付款
- V3.1: ./星际公民/rsi_auto_buy.py，页面加购+API付款混合模式
- 已脱敏打包：./星际公民/rsi-auto-buy-v3.1.zip
- 旧版GUI：根窗口不设实色bg，背景图place全窗口，控件单独小底色#2a2a3e

## RSI GraphQL捕获结果（2026-05-02）
- 输出：debug_gql_capture.json, GraphQL_格式分析报告.md, capture_gql.py
- 批量GraphQL，JSON数组格式，CSRF Token每次变化
- 捕获操作：GetAvailableCurrencies, FeatureToggle, MiniCartWidgetInitializationQuery, PlatformQuery, StepperQuery, account

## SKU抓取结果（2026-05-02）
- 90个商品SKU（84个Standalone，6个Warbond）
- 输出：sku_mapping.md, sku_data.json, sku_raw.json
- 方法：Chrome CDP + Playwright拦截GraphQL，遍历14页

## API频率限制测试（待执行）
- 脚本：test_rate_limit.py / test_rate_limit_interactive.py
- 目标：0.5秒间隔10秒，skuId=18039 Pioneer
- 阻塞：需用户登录RSI账号

## 超级咩Impact！已完成待办
- 校准采样5→10次
- CDP连接超时重试3次
- 删除地址绑定步骤
- 正面硬刚刷新轮询策略
- API高并发补偿
- tkinter after→after_idle
- 清理调试日志
- API重试空响应bug修复
