---
name: missed-defects-analysis 
description: 分析历史流出问题并上传记忆 
modeSlugs:  
 - requirements-analysis  
 - test-case  
 - test-code  
---

# Missed Defects Analysis  

## Instructions  

分析流出问题，总结经验并上传记忆，以提高未来测试的覆盖度。  

1. 对需求进行上下文分析  
2. 编写一条可以覆盖该流出问题的测试用例  
3. 确认该测试用例所属的测试点（对应需求结构化分解/测试点维度分析中的某个测试点）  
4. 总结经验（思考如何针对性地测试才能覆盖此类流出问题）  
5. 使用memory-mcp上传记忆（对以上内容进行精简并且上传）  
6. **流出问题记忆上传格式要求（强制执行）**：  
    * **session_id**: user_songziyue_YYYY.MM.DD（用户名+当前时间）  
    * **content**: （用于语义检索）- 使用【】标记： 【流出问题记忆】<对流出问题的简短描述> 【需求】<需求上下文分析> 【测试用例】<可以覆盖该流出问题的测试用例> 【测试点】<该测试用例所属的测试点> 【经验】<总结经验>  
    * **metadata字段**（用于精确过滤）- ⚠️ 只能填写：
        ```
        {  
        "module": "模块名",  
        "project": "项目名",  
        "flow_out_time": "YYYY.MM",  
        "attribute": "市场问题/云端问题/EE问题/性能问题/Pack下线问题/充电适应性问题/冬夏标问题"  
        }
        ```