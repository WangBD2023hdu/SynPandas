# 快速测试指南

## 测试跨页表格多页PDF功能

### 步骤1：准备测试数据

确保以下数据文件存在：
- `examples/data/text.json`
- `examples/data/table.json`

### 步骤2：修改配置文件

使用 `examples/multipage_table.yaml` 配置文件，确保：
- `layout_config.element.table: 1` （包含表格）
- `multipage.enabled: true` （启用多页支持）

### 步骤3：运行测试

```bash
cd /Users/bdw/Downloads/INF-MLLM/Infinity-Parser/Infinity-Synth
python main.py --config=examples/multipage_table.yaml
```

### 步骤4：验证结果

1. 检查生成的PDF文件：
   ```bash
   ls -lh working/image/*.pdf
   ```

2. 打开PDF文件，验证：
   - ✅ PDF包含多页
   - ✅ 表格跨页显示
   - ✅ 每页都显示表头
   - ✅ 表格内容完整

### 步骤5：查看日志

运行时会输出：
- 跨页表格检测信息
- PDF生成进度
- 页面元素提取信息

### 常见问题

**Q: PDF只有一页？**
A: 检查表格是否真的超出页面范围，可以增加表格行数或减少页面高度。

**Q: 表头没有重复？**
A: 确保表格HTML包含 `<thead>` 标签，检查CSS是否正确加载。

**Q: 生成失败？**
A: 检查Chrome和ChromeDriver是否正确安装，查看错误日志。

### 调试技巧

1. 使用 `--check` 参数查看标注框：
   ```bash
   python main.py --config=examples/multipage_table.yaml --check
   ```

2. 检查HTML输出：
   ```bash
   cat working/html/output_0.html | grep -A 10 "table"
   ```

3. 查看生成的JSON数据：
   ```bash
   cat working/ground_truth/result_of_id0.json | head -50
   ```
