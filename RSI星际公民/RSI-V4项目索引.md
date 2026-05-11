# RSI_MIEKCIK_BUY V4.0.0 项目索引

> **咩咩Kick! V4.0.0 ⚡TURBO闪电狂暴版** — RSI星际公民全API自动抢购工具
> 仓库: `git@github.com:cyaegha0425/RSI_MIEKCIK_BUY.git` (V4分支)

---

## 项目概述

V4.0.0 在V3.0.4基础上新增**TURBO闪电狂暴模式**：

### 核心改动：激进结账
- NextStep1发出后，**不等HTTP完整响应**，固定短延迟（可配置）后直接发NextStep2
- NextStep2发出后，同样短延迟后直接发Validate
- 记录每一步的实际响应状态和耗时
- 失败**自动回退**：激进模式失败 → 恢复正常等待模式重试

### V3其他功能保持不变
- 拦截模式 / SKU模式 / 伏击模式
- 时间校准 / GUI配置 / SKU收藏夹

---

## 项目结构

```
RSI_MIEKCIK_BUY_V4/
├── RSI_MIEKCIK_BUY.py          # 主入口 (V4.0.0 ⚡TURBO)
├── README.md / GUIDE.txt        # 文档
├── sku_bookmarks.example.json   # 收藏夹模板
├── sku_bookmarks.json           # 本地收藏夹
└── modules/
    ├── main.py                  # 主流程（V3逻辑不变）
    ├── api_client.py            # RSIClient + TURBO结账
    ├── gui_config.py            # 配置窗口 + TURBO开关
    ├── gui_progress.py          # 进度窗口
    ├── sku_interceptor.py       # SKU拦截器
    ├── sku_bookmarks.py         # SKU收藏夹
    ├── calibration.py           # 时间校准
    ├── browser.py               # 浏览器启动
    ├── config.py                # 常量 + TURBO配置
    └── latency_test.py          # 延迟测试
```

---

## V4.0 TURBO模式配置

### config.py 新增配置项

```python
CFG = {
    # ... V3原有配置 ...
    
    # ===== V4.0 TURBO模式 =====
    "TURBO_MODE": False,           # 激进模式开关
    "TURBO_NEXT_DELAY": 0.2,       # NextStep间隔延迟（秒）
    "TURBO_VALIDATE_DELAY": 0.15,  # Validate间隔延迟（秒）
}
```

### GUI高级设置新增

- ⚡ **TURBO 闪电狂暴模式** 复选框
- **NextStep间隔** 输入框（默认0.2秒）
- **Validate间隔** 输入框（默认0.15秒）

---

## V4.0 激进结账流程

### 正常模式（V3行为）
```
AddCredit → NextStep1(等响应) → NextStep2(等响应) → Validate(等响应)
```

### TURBO模式（V4行为）
```
AddCredit → ⚡NextStep1(发出) → ⏱0.2s → ⚡NextStep2(发出) → ⏱0.15s → ⚡Validate(发出)
           ↓                    ↓                    ↓
         异步记录响应          异步记录响应          异步记录响应
```

### 回退机制
```
TURBO Validate失败 → 记录 → 回退到正常模式重试 → 若再失败则失败
```

---

## 激进模式适用场景

### ✅ 建议开启TURBO模式
- 网络延迟 < 100ms
- 服务器响应稳定
- 需要极致速度

### ❌ 建议关闭TURBO模式
- 网络延迟 > 200ms
- 首次使用或测试
- 不确定网络状况

### 建议延迟参数
| 网络延迟 | TURBO_NEXT_DELAY | TURBO_VALIDATE_DELAY |
|---------|-----------------|---------------------|
| < 50ms  | 0.15s           | 0.10s               |
| 50-100ms | 0.20s          | 0.15s               |
| 100-200ms | 0.30s         | 0.20s               |
| > 200ms | 建议关闭TURBO    | 建议关闭TURBO        |

---

## 日志示例

### TURBO模式成功
```
⚡⚡⚡ [TURBO狂暴模式] 激进结账！延迟=0.2s ⚡⚡⚡
📍 [极速结账] ⚡TURBO NextStep1...
   ⏱ NextStep1响应耗时: 45ms
   moveNext=True, steps=['billing', 'payment']
📍 [极速结账] ⚡TURBO NextStep2 (延迟0.2s)...
   ⏱ NextStep2响应耗时: 38ms
   moveNext=True, orderSlug=
📍 [极速结账] ⚡TURBO Validate (延迟0.15s)...
   ⏱ Validate响应耗时: 52ms
   🎉 付款成功! 订单号: RSI-XXXXXX
```

### TURBO回退成功
```
⚡⚡⚡ [TURBO狂暴模式] 激进结账！延迟=0.2s ⚡⚡⚡
📍 [极速结账] ⚡TURBO NextStep1...
   ⏱ NextStep1响应耗时: 250ms
⚠️ TURBO NextStep2失败，尝试正常模式补救...
📍 [回退模式] 正常等待重试结账...
   正常NextStep1: moveNext=True
   正常NextStep2: moveNext=True, orderSlug=
   🎉 回退模式付款成功! 订单号: RSI-XXXXXX
```

---

## API变更详情

### api_client.py 修改的函数

| 函数 | V3行为 | V4 TURBO行为 |
|------|--------|--------------|
| `superfast_checkout()` | 等待NextStep响应后再发下一个 | 短延迟直接发下一个 |
| `superfast_ambush()` | 等待NextStep响应后再发下一个 | 短延迟直接发下一个 |
| `_ambush_refill()` | 等待NextStep响应后再发下一个 | 短延迟直接发下一个 |
| `ambush_validate()` | 等待validate响应 | 短延迟后发validate，失败回退正常模式 |
| `_normal_checkout_retry()` | 无（新增） | TURBO失败后的回退函数 |

### 新增函数

```python
def _normal_checkout_retry(self, total: float) -> bool:
    """TURBO模式失败后，回退到正常模式重试结账"""
    # 重新执行完整的正常结账流程
    # NextStep1 → NextStep2 → Validate
```

---

## V3 → V4 迁移要点

1. **配置文件兼容**：V4默认关闭TURBO_MODE，V3用户升级后行为不变
2. **GUI新增选项**：高级设置中多了TURBO开关和延迟配置
3. **日志新增标识**：TURBO模式日志带有 ⚡ 图标和"TURBO"字样
4. **向后兼容**：V4完全兼容V3的所有功能和配置

---

## 命名由来

参考主人风格：
- V1: 咩咩蹄到好船来
- V3: 咩咩KICK
- V4: **咩咩⚡TURBO闪电狂暴版**

寓意：开启TURBO模式后，抢购速度如闪电，势不可挡！

---

## 使用建议

1. **首次使用先测试**：先用测试模式验证功能正常
2. **根据网络调整参数**：延迟参数不是越小越好，要匹配网络延迟
3. **保留回退机制**：TURBO失败会自动回退，不用担心完全失败
4. **关注日志**：失败时查看日志确认是TURBO还是正常模式失败

