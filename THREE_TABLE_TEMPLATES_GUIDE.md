# 三种跨页表格模板使用指南

本项目提供了三种不同的跨页表格模板，适用于不同的使用场景。

## 模板概览

### 1. 表头重复模板 (`document_repeat_header`)
**特点**：每页都显示表头
- ✅ 表头在每页重复显示
- ✅ 适合需要频繁查看表头的场景
- ✅ 最常用的跨页表格模式

**适用场景**：
- 数据表格，需要每页都能看到列名
- 财务报表
- 统计表格

### 2. 表头不重复模板 (`document_no_repeat_header`)
**特点**：只在第一页显示表头，后续页面只显示表格主体
- ✅ 节省页面空间
- ✅ 表头只在第一页显示
- ⚠️ 注意：纯CSS实现有限制，可能需要JavaScript辅助

**适用场景**：
- 长表格，表头信息简单
- 需要最大化内容显示空间
- 表格列数较少，容易记忆

### 3. 单元格跨页模板 (`document_cell_break`)
**特点**：允许单元格内容跨页显示
- ✅ 单元格内容可以跨页分割
- ✅ 适合单元格内容较长的表格
- ✅ 自动处理长文本换行

**适用场景**：
- 单元格包含长文本
- 描述性表格
- 需要完整显示单元格内容的场景

## 使用方法

### 步骤1：选择模板

在配置文件中指定要使用的模板：

```yaml
work_path:
  template_file: "three_columns/document_repeat_header.html.jinja"  # 表头重复
  # template_file: "three_columns/document_no_repeat_header.html.jinja"  # 表头不重复
  # template_file: "three_columns/document_cell_break.html.jinja"  # 单元格跨页
```

### 步骤2：运行生成

使用对应的配置文件：

```bash
# 表头重复
python main.py --config=examples/repeat_header.yaml

# 表头不重复
python main.py --config=examples/no_repeat_header.yaml

# 单元格跨页
python main.py --config=examples/cell_break.yaml
```

## 技术实现细节

### 表头重复模板

**CSS关键属性**：
```css
.table-repeat-header .table-block table thead {
  display: table-header-group; /* 表头在每页重复 */
}

.table-repeat-header .table-block table {
  page-break-inside: auto; /* 允许表格跨页 */
}

.table-repeat-header .table-block table tr {
  page-break-inside: avoid; /* 行不被截断 */
}
```

### 表头不重复模板

**CSS关键属性**：
```css
.table-no-repeat-header .table-block table thead {
  display: table-header-group;
  page-break-after: always; /* 表头后强制分页 */
}

.table-no-repeat-header .table-block table {
  page-break-inside: auto;
}
```

**注意**：纯CSS无法完全实现表头不重复，Chrome的打印引擎会自动重复表头。如果需要完全控制，可能需要：
1. 使用JavaScript动态处理
2. 服务端渲染时分割表格
3. 使用PDF处理库后处理

### 单元格跨页模板

**CSS关键属性**：
```css
.table-cell-break .table-block th,
.table-cell-break .table-block td {
  page-break-inside: auto; /* 允许单元格跨页 */
  word-wrap: break-word;
  overflow-wrap: break-word;
  hyphens: auto;
}

.table-cell-break .table-block table tr {
  page-break-inside: auto; /* 允许行跨页 */
}
```

## 对比表

| 特性 | 表头重复 | 表头不重复 | 单元格跨页 |
|------|---------|-----------|-----------|
| 表头显示 | 每页重复 | 仅第一页 | 每页重复 |
| 行跨页 | ✅ | ✅ | ✅ |
| 单元格跨页 | ❌ | ❌ | ✅ |
| 适用场景 | 数据表格 | 长表格 | 长文本单元格 |
| CSS实现 | ✅ 完全支持 | ⚠️ 部分支持 | ✅ 完全支持 |

## 最佳实践

1. **选择模板**：
   - 大多数情况使用"表头重复"模板
   - 需要节省空间时使用"表头不重复"模板
   - 单元格有长文本时使用"单元格跨页"模板

2. **表格结构**：
   - 确保表格使用 `<thead>` 和 `<tbody>` 结构
   - 表头放在 `<thead>` 中
   - 数据放在 `<tbody>` 中

3. **测试**：
   - 生成PDF后检查跨页效果
   - 验证表头是否正确显示
   - 检查单元格内容是否完整

## 故障排除

### 问题1：表头没有重复
**解决方案**：
- 检查表格是否使用 `<thead>` 标签
- 确认CSS类名正确（`table-repeat-header`）
- 检查浏览器打印设置

### 问题2：单元格内容被截断
**解决方案**：
- 使用"单元格跨页"模板
- 检查CSS的 `word-wrap` 和 `overflow-wrap` 属性
- 增加单元格的 `padding`

### 问题3：表头不重复模板仍然重复表头
**解决方案**：
- 这是Chrome打印引擎的默认行为
- 考虑使用JavaScript动态处理
- 或使用PDF后处理工具

## 示例文件

- `examples/repeat_header.yaml` - 表头重复配置
- `examples/no_repeat_header.yaml` - 表头不重复配置
- `examples/cell_break.yaml` - 单元格跨页配置

## 相关文件

- `templates/three_columns/document_repeat_header.html.jinja` - 表头重复HTML模板
- `templates/three_columns/document_repeat_header.css.jinja` - 表头重复CSS样式
- `templates/three_columns/document_no_repeat_header.html.jinja` - 表头不重复HTML模板
- `templates/three_columns/document_no_repeat_header.css.jinja` - 表头不重复CSS样式
- `templates/three_columns/document_cell_break.html.jinja` - 单元格跨页HTML模板
- `templates/three_columns/document_cell_break.css.jinja` - 单元格跨页CSS样式
