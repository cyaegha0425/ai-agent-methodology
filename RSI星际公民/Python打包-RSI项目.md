# Python打包EXE笔记

## PyInstaller
- 命令: `pyinstaller --onefile --icon=rsi_icon.ico rsi_auto_buy_gui_v4.py`
- build.bat放在release目录下执行
- 打包目录不能有中文路径（用scautobuy）

## 图标
- 需要ico格式，--icon参数指定
- Windows图标缓存问题：属性里图标正确但资源管理器不显示
- 解决：清理图标缓存或等系统刷新

## 退出
- 用 `os._exit(0)` 强制退出进程
- 不用 `sys.exit()`，可能有线程残留
- 退出前先 `taskkill /F /IM msedge.exe` 杀Edge
- 退出前 `taskkill /F /PID {自己的PID}` 杀自己

## build文件夹
- PyInstaller生成的中间文件，可以删除
- 只需要最终的dist目录下的exe

## bat文件
- 内容用英文避免乱码
- `@echo off` 开头
- `pause` 结尾（防止窗口闪退）

## argparse + PyInstaller
- argparse.parse_args()在模块级别执行会导致PyInstaller打包失败
- PyInstaller分析导入时触发parse_args()，但子进程传了额外参数导致报错
- 解决：延迟解析，用get_args()函数包裹，只在运行时调用
- 模块级别也不要调用get_args()，PyInstaller导入时一样会触发

## 资源文件处理
- 背景图等大文件不建议--add-data打包进exe，会导致路径适配复杂
- 更好的方案：图片放exe同目录的images/下，代码里用相对路径读取
- resource_path()用sys.frozen判断开发/打包环境
- 打包后目录：exe + images/ + icon.ico

## tkinter + PyInstaller
- ImageTk.PhotoImage必须指定master=root，否则跨窗口引用失效
- root.destroy()必须从GUI线程调用，用root.after()调度
- 跨线程destroy不可靠，用_gui_thread.join()等待自然结束

## bat文件补充
- 加`chcp 65001 >nul`解决UTF-8编码问题
- 用`python -m PyInstaller`比直接`pyinstaller`更可靠
- 加pyinstaller检查和自动安装逻辑
