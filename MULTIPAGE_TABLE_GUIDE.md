# 跨页表格多页PDF合成指南

## 功能说明

本项目现在支持包含跨页表格的多页PDF合成功能。当表格内容超出单页时，系统会自动将表格分割到多个页面，并在每页重复显示表头。

## 主要特性

1. **自动检测跨页表格**：系统会自动检测表格是否超出页面范围
2. **多页PDF生成**：支持生成包含多页内容的PDF文档
3. **表头重复**：跨页时自动在每页重复显示表头
4. **CSS优化**：通过CSS确保表格在跨页时正确显示

## 使用方法

### 1. 配置文件设置

在配置文件中添加 `multipage` 配置项：

```yaml
multipage:
  enabled: true  # 启用多页PDF支持
  table:
    max_rows_per_page: 15  # 每页最大行数（不包括表头）
```

### 2. 运行生成

使用支持跨页表格的配置文件运行：

```bash
python main.py --config=examples/multipage_table.yaml
```

### 3. 查看结果

生成的PDF文件会保存在配置文件中指定的 `save_image_dir` 目录下，文件名格式为 `{unique_id}.pdf`。

## 技术实现

### CSS支持

表格跨页通过以下CSS属性实现：

- `page-break-inside: auto` - 允许表格跨页
- `display: table-header-group` - 确保表头在每页重复
- `page-break-inside: avoid` - 避免行在页面中间被截断

### 代码修改

1. **Render.py**：
   - 添加了 `detect_cross_page_table()` 方法检测跨页表格
   - 添加了 `generate_multipage_pdf()` 方法生成多页PDF
   - 修改了PDF生成逻辑以支持多页

2. **pipeline.py**：
   - 更新为使用 `generate_multipage_pdf()` 方法

3. **document.css.jinja**：
   - 添加了跨页表格的CSS支持
   - 添加了打印媒体查询优化

## 注意事项

1. **表格结构**：确保表格使用 `<thead>` 和 `<tbody>` 结构，这样表头才能正确重复
2. **页面高度**：默认A4页面高度为297mm，系统会自动处理内容超出
3. **性能**：多页PDF生成可能需要更多时间，特别是对于大型表格

## 示例配置

参考 `examples/multipage_table.yaml` 查看完整的配置示例。

## 故障排除

如果遇到表格跨页显示不正确的问题：

1. 检查表格HTML结构是否包含 `<thead>` 和 `<tbody>`
2. 检查CSS是否正确加载
3. 查看浏览器控制台是否有JavaScript错误
4. 确认Chrome版本是否支持所需的打印功能
