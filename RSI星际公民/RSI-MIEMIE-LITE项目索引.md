# RSI MIEMIE LITE V1.0.0 项目索引

## 基本信息
- **路径**：`/root/Projects/RSI_MIEMIE_LITE/`
- **GitHub**：`cyaegha0425/RSI_MIEMIE_LITE`（私有）
- **性质**：V3.0.4的DOM点击精简版，删除API/SKU模式，用于小范围发布

## 已完成工作
### 1. 前期改造
- 删除：`api_client.py`、`sku_interceptor.py`、`setup_cython.py`
- 修改DOM-only：`browser.py`、`main.py`、`config.py`、`gui_config.py`、`gui_progress.py`、`calibration.py`、`__init__.py`
- 重命名：入口文件改为`RSI_MIEMIE_LITE.py`
- 更新：`README.md`

### 2. BUG修复
- 对齐V1 `add_to_cart_via_page`逻辑，更新`find_card`/`click_card_btn`函数选择器为`.c-skuCard`/`.a-skuButton`
- 添加5次重试逻辑
- 新增调试日志

### 3. Git操作
- 已commit并push到`origin/main`
- Commit信息：`fix: find_card/click_card_btn对齐V1 add_to_cart_via_page选择器(.c-skuCard/.a-skuButton)`

## 注意事项
- `sku_bookmarks.py`仍保留（功能独立，非API/SKU模式依赖）