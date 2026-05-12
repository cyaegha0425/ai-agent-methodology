# tkinter笔记

## 线程安全
- tkinter主线程跑GUI，Playwright在子线程
- 子线程不能直接操作GUI控件（Tcl_AsyncDelete错误）
- 用队列或标志变量通信

## 事件循环
- `root.mainloop()` 是阻塞的
- 子线程通过修改共享变量通知主线程

## 样式
- Catppuccin Mocha配色方案
- 背景: #1e1e2e, 前景: #cdd6f4, 输入框: #313244
- 按钮成功: #a6e3a1, 失败: #f38ba8

## 窗口
- `attributes('-topmost', True)` 置顶
- `resizable(False, False)` 禁止缩放
- geometry居中: `+{(screen_w-win_w)//2}+{(screen_h-win_h)//2}`

## 注意
- GUI线程设为非daemon，不被强制杀
- 主线程等GUI关闭后再退出
- os._exit(0)强制退出时不走析构函数
