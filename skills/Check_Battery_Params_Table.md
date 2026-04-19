name: check-cell-params-table description: 查电芯参数表 modeSlugs:  
* requirements-analysis  
* test-case  
* test-code  
  
# Check Cell Params Table  
## Instructions  
使用doc-snapshotMCP查“电芯参数表”中的参数。  
1. 使用get_excel_sheet_names功能确定sheet。  
2. 使用recommend_cell_range功能自动推荐表格区域。  
3. 若表格较为复杂，使用get_excel_sheet_snapshot功能获取表格结构快照，以确认需要查询的表格区域。  
4. 使用get_excel_cell_data功能获取表格区域的数据。  
5. 查询电芯参数表后，请将表格区域的内容打印出来，并请求用户确认。  
