## 测试点总结(Test point summarize)  
1. 根据需求结构化分解和测试点维度分析结果总结测试点。确认各模式、条件、时间特性、执行路径、组合、状态跳转、时序组合等测试场景均能被覆盖。  
2. 基于历史流出问题记忆，分析高频流出场景和流出模式，制定针对性的测试策略，补充必要测试点以提升缺陷拦截率。  
3. 每一条测试点请说明：测试步骤、期望结果。  
4. 合并测试目的完全相同的测试点，模式或条件设置不相同不合并。  
5. 测试点文档编写和归档要求：  
    * 目录结构  
        * requirements-analysis模式已创建测试任务路径，期待当前目录结构如下: 
            ```
            TestCase/  
            └── {测试阶段}_{日期}/{待测功能}/  
                ├── [已完成]Mermaid.md # 思维导图文档  
                ├── [进行中]TestPoint.md # 测试点文档  
                ├── [未完成]TestCase.md # 测试用例文档  
                ├── [未完成]TestCase01.robot # 测试套件(可多个) 
                └── [未完成]Resource.robot # 资源文件  
            ```
        * 当前任务需要创建一个markdown文件，命名为"TestPoint.md"。  
    * 测试点编写规范  
        * 要求每行一个测试点，测试点ID从TP-001开始递增编号，请自行分段以方便阅读。  
        * 文件开头应当定义style，format:<style> th { background-color: #FFE0B2; color: black; padding: 10px; border: 1px solid black; line-height: 1; } td { padding: 8px; color: black; border: 1px solid black; line-height: 1; } table { border-collapse: collapse; } </style>  

            |测试点ID|测试点|测试步骤|期望结果|
            | --- | --- | --- | --- |
            | `TP-<index>` | `<描述测试目的>` | `<描述测试步骤：进入xx模式，设置xx条件等>` | `<描述期望结果>` |

<br>  

## 测试用例编写(Test case writing)  
1. 针对所有测试点，结合变量和关键字编写HIL台架可执行的测试用例。  
2. 若测试步骤可以合并，可以通过一条测试用例覆盖多个测试点，减少用例数量。  
3. 当TX/RX变量不明确的时候你可以随时参考历史脚本，利用工具查询或咨询用户。  
4. 测试用例中应当观测必要的中间变量。  
5. 测试用例的文档编写和归档要求：  
    * 目录结构  
        * 期待当前目录结构如下: TestCase/  
            ```
            TestCase/  
            └── {测试阶段}_{日期}/{待测功能}/  
                ├── [已完成]Mermaid.md # 思维导图文档  
                ├── [已完成]TestPoint.md # 测试点文档  
                ├── [进行中]TestCase.md # 测试用例文档  
                ├── [未完成]TestCase01.robot # 测试套件(可多个) 
                └── [未完成]Resource.robot # 资源文件  
            ```  
        * 当前任务需要创建一个markdown文件，命名为"TestCase.md"。  
    * 测试用例编写规范  
        * 测试用例应当trace到全部的测试点，测试用例ID从TC-001开始递增编号,请自行分段以方便阅读。  
        * 文件开头定义style，format:<style> th { background-color: #BBDEFB; color: black; padding: 10px; border: 1px solid black; line-height: 1; } td { padding: 8px; color: black; border: 1px solid black; line-height: 1; } table { border-collapse: collapse; } </style>  

            | 测试用例ID | 测试点Trace | 测试目的 | 测试步骤 | 步骤描述 | 期望结果 | 测试结果 |
            | --- | --- | --- | --- | --- | --- | --- |
            | `TC-<index>` | `对应的测试点` | `<描述测试目的>` | `Step_<index>` | `<描述测试步骤：进入xx模式，设置xx条件等>` | `<描述期望结果>` | `Fail or Pass` |
<br>  

## 覆盖度分析(Coverage analysis)  

1. 统计测试用例是否完全覆盖了测试点，计算覆盖度百分比。  
2. 完成测试点维度分析后询问用户是否切换模式以完成：  
    * 测试点总结、测试用例编写(Test Case Mode)  
    * 测试脚本编写和执行(Test Code Mode)  
