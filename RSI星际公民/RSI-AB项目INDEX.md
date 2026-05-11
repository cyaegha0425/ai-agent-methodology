# RSI-AB 项目归档索引

> **咩咩蹄到好船来 V1.0** — RSI星际公民自动抢购工具
> 项目完成时间：2026-05-01
> 项目目录：`./Project/RSI-AB/`

---

## 项目概述

自动抢购RSI（Robert Space Industries）星际公民商店的限时商品。目标：2026年5月15日0点抢购Kraken（黑海妖），信用点付款，追求极致速度。

- 完成V1商品详情页模式 + V2列表页加购模式
- 16次真实购买测试全部成功
- tkinter GUI + 模块化架构 + PyInstaller打包
- V1.0新增：排除关键词、测试模式、高级设置弹窗

---

## 项目结构

```
RSI-AB/
├── RSI_MIEBUY_V1.py        # 主入口（延迟get_args()）
├── icon.png / icon.ico      # 图标
├── build.bat                # PyInstaller打包脚本
├── README.md                # 项目说明
├── GUIDE.md / GUIDE.txt     # 用户操作指南
├── images/                  # 背景图（exe同目录外部读取）
│   ├── gui_bg.png           # 配置+进度窗口背景 (600x560)
│   ├── gui_bg_success.png   # 成功弹窗背景 (520x325)
│   └── gui_bg_fail.png      # 失败弹窗背景 (520x325)
└── modules/
    ├── config.py (352行)    # 常量、配置、resource_path、get_args
    ├── gui_config.py (482行) # 配置窗口
    ├── gui_progress.py (466行) # 进度窗口+弹窗
    ├── api_client.py (524行) # RSIClient类，API交互
    ├── browser.py (528行)   # 浏览器自动化（run_api_mode主流程）
    └── main.py (344行)      # 主入口模块，Playwright线程
```

---

## 关键架构决策

1. **模块拆分**：800行阈值，每加功能先评估是否需新模块
2. **Playwright在独立线程**：tkinter主循环和Playwright sync_api冲突，必须线程分离
3. **CDP模式连接Edge**：用127.0.0.1:9222，不用localhost
4. **列表页加购模式**：V2核心，直接在浏览页点击加购，绕过SKU提取
5. **信用点付款**：购物车页面点MAX按钮应用信用点
6. **延迟解析args**：get_args()避免PyInstaller导入时触发parse_args()
7. **排除关键词**：CFG["EXCLUDE_KEYWORDS"]，split(",")过滤空strip，卡片标题不含排除词才加购
8. **测试模式**：CFG["TEST_MODE"]，勾选后不点I Agree，不真正扣款
9. **高级设置弹窗**：时间偏移+代理地址收到弹窗，主窗口更简洁
7. **外部图片**：背景图放exe同目录images/，不用--add-data打包进exe
8. **PhotoImage绑定master**：创建时指定master=root/dialog，防止GC和跨窗口引用失效

---

## Harness（必须保留的修复点）

1. Playwright timeout单位是**毫秒**
2. CDP连接用**127.0.0.1**而非localhost
3. tkinter和Playwright sync_api事件循环冲突：Playwright在独立线程
4. CART_URL有**/store/**路径
5. 付款结果根据**URL判断**return True/False，不能无脑return True
6. **os._exit(0) + taskkill杀自己PID** 强制退出
7. subprocess.run taskkill自动杀Edge
8. JS evaluate里**不能使用Python的log变量**
9. 加购后**不会自动跳购物车**，需手动goto CART_URL
10. 列表页加购看**"successfully added"**文字判断成功
11. agree勾选：**不检查checkbox是否已勾选，直接点击**；I AGREE按钮点击失败时等待1秒重新勾选checkbox再尝试
12. get_gui/set_gui/_gui在**config.py**中
13. run()用**while循环不递归**
14. 输入框bg="white" fg="black"
15. bg=""空字符串会报错TclError，必须用实色
16. ttk.Combobox不支持bg参数
17. padx不支持元组格式(2,8)
18. ImageTk.PhotoImage必须保持引用否则被GC回收
19. 🚀已全部替换为🐑
20. **局部修改原则**：只改需要改的部分，严禁全量重写整个文件
21. **sub-agent harness必须完整**：包括"为什么这样写"的上下文

---

## GUI统一审美规范

| 用途 | 颜色 | 说明 |
|------|------|------|
| 底色条 | #E8EDF2 | 浅蓝灰白，融入背景 |
| 标题文字 | #1a3a5c | 深蓝 |
| 正文文字 | #2d2d2d | 深灰 |
| 开始按钮 | #6A8CBA | 暗蓝，fg=white |
| 取消按钮 | #9E6B7A | 暗玫红，fg=white |
| 确认按钮 | #7B8FB7 | 中暗蓝，fg=white |
| 署名 | #2d2d2d | 深灰，无底色 |

- 按钮本身就是色块，不需要额外底色Frame包裹
- 署名"by 咩咩莉娅"不加底色
- 清空购物车弹窗居中，成功/失败弹窗靠右(relx=0.85)
- 展示颜色方案时必须生成色卡图

---

## V1.0 标题/文案

- 标题：🐑 咩咩蹄到好船来 V1.0
- 副标题：抢光CIG的机库
- 成功弹窗：🎉 好船来啦！/ 不愧是我！
- 失败弹窗：❌ 咩失前蹄啦！/ 下次一定！
- 制作者：咩咩莉娅

---

## tkinter关键踩坑

1. bg=""空字符串报错TclError，必须用实色
2. ttk.Combobox不支持bg参数
3. padx不支持元组格式
4. tkinter不支持真透明背景
5. PhotoImage必须保持引用否则被GC回收
6. **PhotoImage必须指定master=root**，否则跨窗口引用失效
7. root.destroy()必须从GUI线程调用（用root.after()调度）
8. 跨线程destroy不可靠，用_gui_thread.join()等待自然结束

---

## 打包相关

- 命令：`pyinstaller --onefile --windowed --icon=icon.ico --name "RSI_MIEBUY_V1" RSI_MIEBUY_V1.py`
- 图片不放exe内，放同目录images/
- resource_path()用sys.frozen判断开发/打包环境
- get_args()延迟解析避免PyInstaller分析导入时报错
- bat文件用英文+chcp 65001
- 打包目录不能有中文路径

---

## 抢购配置

- 目标商品：Kraken（白海妖），信用点版本
- 抢购时间：2026-05-15 00:00:00
- TIME_OFFSET：-0.2秒
- V2 URL前缀：`https://robertsspaceindustries.com/en/store/pledge/browse/extras/standalone-ships?page=1&sortField=price&sortDir=desc&keywords=`
- CART_URL：`https://robertsspaceindustries.com/en/store/pledge/cart`
- CDP调试端口：9222
- 用户环境：Python 3.14 + Windows + Edge

---

## 后续待做（项目已归档，需时再启）

- [ ] 5月15日正式抢购：本地build EXE + time.is校准TIME_OFFSET
- [ ] 测试买的船24小时后融回信用点（当前$795消费）
- [ ] GitHub/Gitee仓库搭建（云电脑已有环境）
- [ ] 排版需求（等脚本改完后再新开session聊）

---

## 相关文件索引

- `harness-RSI.md` — RSI项目代码修改原则和HARNESS规则
- `Python打包-RSI项目.md` — RSI项目PyInstaller打包详细笔记
- `Playwright-RSI特殊处理.md` — RSI网站专属Playwright技巧

---

## 相关参考文件

| 文件 | 内容摘要 |
|------|----------|
| `RSI-GQL-API.md` | RSI GraphQL API端点、请求格式、关键Mutations（AddCartMultiItemMutation、AddCreditMutation、NextStepMutation、CartValidateCartMutation） |
| `RSI历史归档.md` | V1.x~V3.0.3详细修复记录，包括白屏bug修复、GUI优化、价格验证等关键迭代 |
| `RSI商店DOM结构.md` | RSI商店页面DOM选择器：列表页商品卡片、加购按钮、购物车页面元素定位 |
| `RSI技术栈.md` | RSI网站技术架构：Apollo Client + GraphQL、Warbond/信用点SKU区分、价格单位（美分） |
| `RSI抢购脚本开发笔记.md` | V1/V2/V3开发过程踩坑：CDP连接、RSI SPA/SSR判断、信用点金额精确匹配等 |
| `事故复盘-2026-05-04.md` | 幻觉事故复盘：.offer-card误判废弃、V1结账流程误读、源码分析≠动态DOM验证 |

---

## 百科4.3详解补充内容（来源：百科）

### 百科4.3中的INDEX模板结构

百科中收录的INDEX模板结构示例：

```markdown
# 项目A INDEX（脱敏示例）

## 核心文件
- 项目A/harness.md — 开发流程与避坑经验
- 项目A/api.md — 接口文档
- 项目A/配置说明.md — 配置文件详解

## 参考文件
- 项目A/旧版本记录.md — 历史版本说明
- 项目A/踩坑记录.md — 已知问题与解决方案

# 项目B INDEX（脱敏示例）

## 核心文件
- 项目B/开发笔记.md — 开发过程记录
- 项目B/技术方案.md — 技术选型与实现
- 项目B/测试报告.md — 测试结果与问题

## 参考文件
- 项目B/竞品分析.md — 竞品对比分析
```

### RSI项目实际文件清单（来自百科4.3知识库目录结构示例）

百科中收录的RSI项目知识库目录结构：

```
知识库/RSI星际公民/
├── INDEX.md                    # RSI项目唯一导航入口
├── harness-RSI.md             # RSI开发流程与避坑
├── RSI-AB项目INDEX.md          # AB抢购项目完整索引
├── RSI-V3项目索引.md           # V3版本完整索引
├── RSI-GQL-API.md              # RSI GraphQL接口文档
├── RSI历史归档.md              # V1~V3详细迭代记录
├── RSI商店DOM结构.md           # RSI网站DOM选择器
├── RSI技术栈.md                # RSI网站技术架构
├── RSI抢购脚本开发笔记.md      # 开发踩坑记录
├── Python打包-RSI项目.md       # PyInstaller打包笔记
├── Playwright-RSI特殊处理.md   # Playwright技巧
└── 事故复盘-2026-05-04.md      # 事故复盘记录
```


## 需求来源：抢购的业务场景

RSI在重大节日限时+限量发售特定船的Pledge：
- **Warbond版**：限现金购买，带折扣
- **Standalone版**：可用Credit购买，无折扣
- 北京时间0点起每4小时整点放量，数量极少，真人和脚本群同时抢，秒没

**项目定位**：通用限量船购买脚本，不局限于某一条船。可配置目标船，适用于任何限时+限量的Standalone版本抢购。

抢购脚本的核心价值：用已有的Credit，在整点放货瞬间自动完成购买。**Credit余额不够=抢到了也付不了款**，所以信用点追踪是前置保障。

**信用点追踪规则**：详见 `./知识库/Agent运维/信用点追踪规则.md`（中间值追踪机制、Credit/Pledge互转规则、Buy Back限制、核心理解）