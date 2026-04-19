---
name: check-cell-params-table
description: 查电芯参数表
modeSlugs:
  - requirements-analysis
  - test-case
  - test-code
---

# Check Cell Params Table  

## Instructions  

使用 doc-snapshotMCP 查“电芯参数表”中的参数。

1. 使用 `get_excel_sheet_names` 功能确定 sheet  
2. 使用 `recommend_cell_range` 功能自动推荐表格区域  
3. 若表格较为复杂，使用 `get_excel_sheet_snapshot` 获取表格结构快照，以确认需要查询的表格区域  
4. 使用 `get_excel_cell_data` 获取表格区域的数据  
5. 查询完成后，将表格区域内容打印出来，并请求用户确认  