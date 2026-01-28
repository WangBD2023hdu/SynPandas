# 跨页表格多页PDF合成功能 - 修改总结

## 概述

本次更新为项目添加了跨页表格的多页PDF合成功能，使得当表格内容超出单页时，系统能够自动将表格分割到多个页面，并在每页重复显示表头。

## 修改的文件

### 1. `core/Render.py`
**主要修改：**
- 添加了 `PyPDF2` 导入（可选，用于PDF合并）
- 新增 `detect_cross_page_table()` 方法：检测页面中是否有跨页表格
- 新增 `generate_multipage_pdf()` 方法：生成支持跨页表格的多页PDF
- 保留了原有的 `get_location()` 方法作为单页PDF的fallback

**关键功能：**
- 自动检测表格是否超出页面范围
- 使用Chrome的CDP（Chrome DevTools Protocol）生成多页PDF
- 支持通过CSS控制表格跨页行为

### 2. `pipeline.py`
**主要修改：**
- 将 `render.get_location()` 替换为 `render.generate_multipage_pdf()`
- 启用多页PDF支持（`enable_multipage=True`）

### 3. `utils/utils.py`
**主要修改：**
- 添加了 `BeautifulSoup` 导入
- 新增 `split_table_for_multipage()` 函数：将HTML表格分割成多个部分（备用功能）

### 4. `templates/three_columns/document.css.jinja`
**主要修改：**
- 添加了表格跨页相关的CSS属性：
  - `page-break-inside: auto` - 允许表格跨页
  - `display: table-header-group` - 确保表头在每页重复
  - `page-break-inside: avoid` - 避免行在页面中间被截断
- 添加了 `@media print` 媒体查询，优化打印时的表格显示

### 5. 新增文件

#### `examples/multipage_table.yaml`
示例配置文件，展示如何启用跨页表格功能：
```yaml
multipage:
  enabled: true
  table:
    max_rows_per_page: 15
```

#### `MULTIPAGE_TABLE_GUIDE.md`
使用指南文档，包含：
- 功能说明
- 使用方法
- 技术实现细节
- 故障排除指南

## 技术实现原理

### 1. 跨页检测
通过JavaScript检测表格的 `getBoundingClientRect()` 位置信息，判断表格是否超出页面底部。

### 2. 多页PDF生成
使用Chrome的 `Page.printToPDF` CDP命令生成PDF。Chrome会自动处理内容分页，配合CSS的 `page-break-*` 属性控制分页行为。

### 3. 表头重复
通过CSS的 `display: table-header-group` 确保 `<thead>` 元素在每页重复显示。

## 使用方法

1. **配置启用**：在配置文件中添加 `multipage.enabled: true`
2. **运行生成**：`python main.py --config=examples/multipage_table.yaml`
3. **查看结果**：生成的PDF文件保存在 `save_image_dir` 目录

## 兼容性

- ✅ 向后兼容：原有的单页PDF生成功能仍然可用
- ✅ 可选功能：通过配置控制是否启用多页支持
- ✅ CSS兼容：使用标准的CSS打印属性，兼容现代浏览器

## 注意事项

1. 确保表格使用 `<thead>` 和 `<tbody>` 结构
2. 大型表格可能需要更多生成时间
3. 需要Chrome浏览器支持CDP功能

## 未来改进方向

1. 支持自定义每页行数
2. 支持表格跨页时的自定义样式
3. 优化大型表格的性能
4. 添加表格跨页的预览功能
